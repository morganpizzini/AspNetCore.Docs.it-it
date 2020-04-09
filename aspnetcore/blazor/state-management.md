---
title: Gestione Blazor dello stato di ASP.NET Core
author: guardrex
description: Informazioni su come Blazor mantenere lo stato nelle app server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218869"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a>Gestione Blazor dello stato di ASP.NET Core

Di [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorIl server è un framework dell'app con stato. Nella maggior parte dei criteri, l'app mantiene una connessione continua al server. Lo stato dell'utente viene mantenuto nella memoria del server in un *circuito*. 

Esempi di stato tenuto per il circuito di un utente includono:

* Interfaccia utente&mdash;di cui è stato eseguito il rendering la gerarchia delle istanze del componente e l'output di rendering più recente.
* I valori di tutti i campi e le proprietà nelle istanze del componente.
* I dati contenuti nelle istanze del servizio [di inserimento delle dipendenze (DI)](xref:fundamentals/dependency-injection) con ambito al circuito.

> [!NOTE]
> Questo articolo riguarda Blazor la persistenza dello stato nelle app del server. BlazorLe app WebAssembly possono sfruttare la [persistenza dello stato sul lato client nel browser,](#client-side-in-the-browser) ma richiedono soluzioni personalizzate o pacchetti di terze parti oltre l'ambito di questo articolo.

## <a name="opno-locblazor-circuits"></a>BlazorCircuiti

Se un utente si verifica Blazor una perdita di connessione di rete temporanea, tenta di riconnettere l'utente al circuito originale in modo che possa continuare a usare l'app. Tuttavia, la riconnessione di un utente al circuito originale nella memoria del server non è sempre possibile:

* Il server non può mantenere un circuito disconnesso per sempre. Il server deve rilasciare un circuito disconnesso dopo un timeout o quando il server è sotto pressione della memoria.
* Negli ambienti di distribuzione multiserver con carico bilanciato, tutte le richieste di elaborazione del server potrebbero non essere disponibili in un determinato momento. I singoli server potrebbero non riuscire o essere rimossi automaticamente quando non sono più necessari per gestire il volume complessivo delle richieste. Il server originale potrebbe non essere disponibile quando l'utente tenta di riconnettersi.
* L'utente potrebbe chiudere e riaprire il browser o ricaricare la pagina, rimuovendo qualsiasi stato contenuto nella memoria del browser. Ad esempio, i valori impostati tramite chiamate di interoperabilità JavaScript vengono persi.

Quando un utente non può essere riconnesso al circuito originale, riceve un nuovo circuito con uno stato vuoto. Ciò equivale alla chiusura e alla riapertura di un'app desktop.

## <a name="preserve-state-across-circuits"></a>Conservare lo stato tra i circuiti

In alcuni scenari, è auspicabile mantenere lo stato tra i circuiti. Un'app può conservare dati importanti per un utente se:An app can retain important data for a user if:

* Il server Web non è più disponibile.
* Il browser dell'utente è costretto ad avviare un nuovo circuito con un nuovo server web.

In generale, la gestione dello stato tra i circuiti si applica agli scenari in cui gli utenti creano attivamente dati, non alla semplice lettura dei dati già esistenti.

Per mantenere lo stato oltre un singolo circuito, *non archiviare semplicemente i dati nella memoria del server.* L'app deve rendere persistenti i dati in un'altra posizione di archiviazione. La persistenza dello&mdash;stato non è automatica, è necessario eseguire le operazioni quando si sviluppa l'app per implementare la persistenza dei dati con stato.

La persistenza dei dati è in genere necessaria solo per lo stato di valore elevato che gli utenti hanno speso per creare. Negli esempi seguenti, lo stato persistente consente di risparmiare tempo o aiuti nelle attività commerciali:

* Modulo Web &ndash; multifase È dispendioso in termini di tempo per un utente reinserire i dati per diversi passaggi completati di un processo a più passaggi se il loro stato viene perso. Un utente perde lo stato in questo scenario se si sposta dal form a più passaggi e tornare al modulo in un secondo momento.
* Carrello &ndash; della spesa È possibile mantenere qualsiasi componente commercialmente importante di un'app che rappresenta potenziali entrate. Un utente che perde il proprio stato, e quindi il carrello, può acquistare meno prodotti o servizi quando torna al sito in un secondo momento.

In genere non è necessario mantenere lo stato ricreato facilmente, ad esempio il nome utente immesso in una finestra di dialogo di accesso che non è stata inviata.

> [!IMPORTANT]
> Un'app può solo rendere persistente *lo stato dell'app.* Le api non possono essere rese persistenti, ad esempio le istanze dei componenti e i relativi alberi di rendering. I componenti e gli alberi di rendering non sono in genere serializzabili. Per rendere persistente un elemento simile allo stato dell'interfaccia utente, ad esempio i nodi espansi di un controllo TreeView, l'app deve disporre di codice personalizzato per modellare il comportamento come stato serializzabile dell'app.

## <a name="where-to-persist-state"></a>Dove mantenere lo stato

Esistono tre posizioni comuni per Blazor la persistenza dello stato in un'app Server.Three common locations exist for persisting state in a Server app. Ogni approccio è più adatto a scenari diversi e ha diversi avvertimenti:

* [Lato server in un database](#server-side-in-a-database)
* [URL](#url)
* [Lato client nel browser](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Lato server in un database

Per la persistenza permanente dei dati o per tutti i dati che devono estendersi su più utenti o dispositivi, un database lato server indipendente è quasi certamente la scelta migliore. Le opzioni includono:

* Database SQL relazionale
* Archivio chiave-valore
* Archivio BLOB
* Archivio tabelle

Dopo aver salvato i dati nel database, un nuovo circuito può essere avviato da un utente in qualsiasi momento. I dati dell'utente vengono conservati e disponibili in qualsiasi nuovo circuito.

Per altre informazioni sulle opzioni di archiviazione dei dati di Azure, vedere la [documentazione di Archiviazione di Azure](/azure/storage/) e database di Azure.For more information on Azure data storage options, see the Azure Storage Documentation and [Azure Databases.](https://azure.microsoft.com/product-categories/databases/)

### <a name="url"></a>URL

Per i dati temporanei che rappresentano lo stato di navigazione, modellare i dati come parte dell'URL. Di seguito sono riportati alcuni esempi di stato modellato nell'URL:

* ID di un'entità visualizzata.
* Numero di pagina corrente in una griglia di paging.

Il contenuto della barra degli indirizzi del browser viene mantenuto:

* Se l'utente ricarica manualmente la pagina.
* Se il server Web&mdash;non è più disponibile, l'utente è costretto a ricaricare la pagina per connettersi a un altro server.

Per informazioni sulla definizione `@page` di <xref:blazor/routing>modelli di URL con la direttiva , vedere .

### <a name="client-side-in-the-browser"></a>Lato client nel browser

Per i dati temporanei che l'utente sta creando attivamente, un `localStorage` `sessionStorage` archivio di backup comune è il browser e le raccolte. L'app non è necessaria per gestire o cancellare lo stato archiviato se il circuito viene abbandonato, il che è un vantaggio rispetto all'archiviazione lato server.

> [!NOTE]
> "Lato client" in questa sezione si riferisce agli [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)scenari lato client nel browser, non al modello di hosting WebAssembly . `localStorage`e `sessionStorage` può essere Blazor utilizzato nelle applicazioni WebAssembly, ma solo scrivendo codice personalizzato o utilizzando un pacchetto di terze parti.

`localStorage`e `sessionStorage` differiscono come segue:

* `localStorage`ha come ambito il browser dell'utente. Se l'utente ricarica la pagina o chiude e riapre il browser, lo stato viene mantenuto. Se l'utente apre più schede del browser, lo stato viene condiviso tra le schede. I dati `localStorage` vengono mantenuti fino a quando non vengono cancellati in modo esplicito.
* `sessionStorage`ha come ambito la scheda del browser dell'utente. Se l'utente ricarica la scheda, lo stato persiste. Se l'utente chiude la scheda o il browser, lo stato viene perso. Se l'utente apre più schede del browser, ogni scheda ha la propria versione indipendente dei dati.

Generalmente, `sessionStorage` è più sicuro da usare. `sessionStorage`evita il rischio che un utente apra più schede e incontri quanto segue:

* Bug nell'archiviazione dello stato tra le schede.
* Comportamento di confusione quando una scheda sovrascrive lo stato di altre schede.

`localStorage`è la scelta migliore se l'app deve mantenere lo stato durante la chiusura e la riapertura del browser.

Avvertenze per l'utilizzo dell'archiviazione del browser:

* Analogamente all'uso di un database sul lato server, il caricamento e il salvataggio dei dati sono asincroni.
* A differenza di un database sul lato server, l'archiviazione non è disponibile durante il prerendering perché la pagina richiesta non esiste nel browser durante la fase di prerendering.
* L'archiviazione di alcuni kilobyte di Blazor dati è ragionevole per mantenere per le applicazioni Server. Oltre a pochi kilobyte, è necessario considerare le implicazioni in termini di prestazioni perché i dati vengono caricati e salvati in rete.
* Gli utenti possono visualizzare o manomettere i dati. ASP.NET core [Data Protection](xref:security/data-protection/introduction) può ridurre il rischio.

## <a name="third-party-browser-storage-solutions"></a>Soluzioni di archiviazione di browser di terze parti

I pacchetti NuGet di terze parti `localStorage` forniscono API per l'utilizzo con e `sessionStorage`.

Vale la pena scegliere un pacchetto che utilizza in modo trasparente ASP.NET [protezione dei dati](xref:security/data-protection/introduction)di Core . ASP.NET Core Data Protection crittografa i dati memorizzati e riduce il rischio potenziale di manomissione dei dati archiviati. Se i dati serializzati IN JSON vengono archiviati in testo non crittografato, gli utenti possono visualizzare i dati utilizzando gli strumenti di sviluppo del browser e anche modificare i dati archiviati. La protezione dei dati non è sempre un problema perché i dati potrebbero essere di natura semplice. Ad esempio, la lettura o la modifica del colore archiviato di un elemento dell'interfaccia utente non è un rischio significativo per la sicurezza dell'utente o dell'organizzazione. Evitare di consentire agli utenti di ispezionare o manomettere *i dati sensibili.*

## <a name="protected-browser-storage-experimental-package"></a>Pacchetto sperimentale Protected Browser Storage

Un esempio di pacchetto NuGet che `localStorage` `sessionStorage` fornisce la protezione [dei dati](xref:security/data-protection/introduction) per ed è [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`è un pacchetto sperimentale non supportato non adatto all'uso in produzione in questo momento.

### <a name="installation"></a>Installazione

Per installare `Microsoft.AspNetCore.ProtectedBrowserStorage` il pacchetto:

1. Nel Blazor progetto di app Server aggiungere un riferimento al pacchetto [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).
1. Nel codice HTML di primo livello, ad esempio nel file *Pages/_Host.cshtml* `<script>` nel modello di progetto predefinito, aggiungere il tag seguente:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. Nel `Startup.ConfigureServices` metodo, `AddProtectedBrowserStorage` chiamare `localStorage` per `sessionStorage` aggiungere e servizi alla raccolta di servizi:In the method, call to add and services to the service collection:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Salvare e caricare i dati all'interno di un componente

In qualsiasi componente che richiede il caricamento [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) o il salvataggio dei dati nell'archivio del browser, utilizzare per inserire un'istanza di uno degli elementi seguenti:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

La scelta dipende da quale negozio di backup si desidera utilizzare. Nell'esempio seguente `sessionStorage` viene utilizzato:In the following example, is used:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

L'istruzione `@using` può essere inserita in un file *_Imports.razor* anziché nel componente. L'uso del file *_Imports.razor* rende lo spazio dei nomi disponibile per segmenti più grandi dell'app o dell'intera app.

Per rendere `_currentCount` persistente `Counter` il valore nel componente `IncrementCount` del `ProtectedSessionStore.SetAsync`modello di progetto, modificare il metodo in modo che utilizzi:

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

Nelle app più grandi e realistiche, l'archiviazione di singoli campi è uno scenario improbabile. È più probabile che le app archivino interi oggetti modello che includono uno stato complesso. `ProtectedSessionStore`serializza e deserializza automaticamente i dati JSON.

Nell'esempio di codice `_currentCount` precedente, i `sessionStorage['count']` dati vengono archiviati come nel browser dell'utente. I dati non vengono archiviati in testo non crittografato, ma sono protetti utilizzando ASP.NET [Core's Data Protection](xref:security/data-protection/introduction). I dati crittografati possono `sessionStorage['count']` essere visualizzati se vengono valutati nella console per sviluppatori del browser.

Per recuperare `_currentCount` i dati se `Counter` l'utente ritorna al componente in un `ProtectedSessionStore.GetAsync`secondo momento (incluso se si trovano su un circuito completamente nuovo), utilizzare :

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Se i parametri del componente includono `ProtectedSessionStore.GetAsync` lo stato `OnParametersSetAsync`di `OnInitializedAsync`navigazione, chiamare e assegnare il risultato in , non . `OnInitializedAsync`viene chiamato solo una volta quando viene creata un'istanza del componente. `OnInitializedAsync`non viene chiamato di nuovo in un secondo momento se l'utente passa a un URL diverso pur rimanendo nella stessa pagina. Per altre informazioni, vedere <xref:blazor/lifecycle>.

> [!WARNING]
> Gli esempi in questa sezione funzionano solo se nel server non è abilitato il prerendering. Con il prerendering abilitato, viene generato un errore simile al:
>
> > Le chiamate di interoperabilità JavaScript non possono essere emesse in questo momento. Ciò è dovuto al fatto che è in corso il prerendering del componente.
>
> Disabilitare il prerendering o aggiungere codice aggiuntivo per lavorare con il prerendering. Per altre informazioni sulla scrittura di codice che funziona con il prerendering, vedere la sezione Gestire il [prerendering.](#handle-prerendering)

### <a name="handle-the-loading-state"></a>Gestire lo stato di caricamento

Poiché l'archiviazione del browser è asincrona (accessibile tramite una connessione di rete), c'è sempre un periodo di tempo prima che i dati vengano caricati e disponibili per l'utilizzo da parte di un componente. Per ottenere risultati ottimali, è in corso il rendering di un messaggio sullo stato del caricamento durante il caricamento anziché visualizzare dati vuoti o predefiniti.

Un approccio consiste nel `null` tenere traccia se i dati sono (ancora in fase di caricamento) o meno. Nel componente `Counter` di default, il `int`conteggio viene mantenuto in un file . Rendere `_currentCount` nullable aggiungendo un`?`punto interrogativo`int`( ) al tipo ( ):

```csharp
private int? _currentCount;
```

Anziché visualizzare incondizionatamente il conteggio e il pulsante **Incremento,** scegliere di visualizzare questi elementi solo se i dati vengono caricati:

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Gestire il prerendering

Durante il prerendering:

* Non esiste una connessione interattiva al browser dell'utente.
* Il browser non dispone ancora di una pagina in cui è possibile eseguire codice JavaScript.

`localStorage`o `sessionStorage` non sono disponibili durante il prerendering. Se il componente tenta di interagire con l'archiviazione, viene generato un errore simile al:

> Le chiamate di interoperabilità JavaScript non possono essere emesse in questo momento. Ciò è dovuto al fatto che è in corso il prerendering del componente.

Un modo per risolvere l'errore consiste nel disabilitare il prerendering. Questa è di solito la scelta migliore se l'applicazione fa un uso pesante di archiviazione basata su browser. Il prerendering aggiunge complessità e non ne trae vantaggio perché l'app non può eseguire il prerendering di qualsiasi contenuto utile fino a quando `localStorage` o `sessionStorage` non sono disponibili.

Per disabilitare il prerendering, aprire il file `render-mode` *Pages/_Host.cshtml* e impostare Component Tag [Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) su <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.

Il prerendering può essere utile per `localStorage` altre `sessionStorage`pagine che non utilizzano o . Per mantenere abilitato il prerendering, rinviare l'operazione di caricamento fino a quando il browser non è connesso al circuito. Di seguito è riportato un esempio per l'archiviazione di un valore di contatore:The following is an example for storing a counter value:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Eseguire il factoring della conservazione dello stato in un percorso comune

Se molti componenti si basano sull'archiviazione basata su browser, la riimplementazione del codice del provider di stato più volte crea la duplicazione del codice. Un'opzione per evitare la duplicazione del codice consiste nel creare un *componente padre* del provider di stato che incapsula la logica del provider di stato. I componenti figlio possono utilizzare dati persistenti indipendentemente dal meccanismo di persistenza dello stato.

Nell'esempio seguente `CounterStateProvider` di un componente, i dati del contatore vengono mantenuti:In the following example of a component, counter data is persisted:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

Il `CounterStateProvider` componente gestisce la fase di caricamento non eseguendo il rendering del contenuto figlio fino al completamento del caricamento.

Per utilizzare `CounterStateProvider` il componente, eseguire il wrapping di un'istanza del componente intorno a qualsiasi altro componente che richiede l'accesso allo stato del contatore. Per rendere lo stato accessibile a tutti `CounterStateProvider` i `Router` componenti `App` in un'app, eseguire il wrapping del componente intorno al componente (*App.razor*):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

I componenti di cui è stato eseguito il wrapping ricevono e possono modificare lo stato del contatore persistente. Il `Counter` componente seguente implementa il modello:The following component implements the pattern:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Il componente precedente non è necessario `ProtectedBrowserStorage`per interagire con , né si occupa di una fase di "caricamento".

Per gestire il prerendering `CounterStateProvider` come descritto in precedenza, può essere modificato in modo che tutti i componenti che utilizzano i dati del contatore funzionino automaticamente con il prerendering. Vedere la sezione Gestire il [prerendering](#handle-prerendering) per i dettagli.

In generale, è consigliabile il modello di componente padre del provider di stato:In general, *state provider parent component* pattern is recommended:

* Per consumare lo stato in molti altri componenti.
* Se è presente un solo oggetto di stato di primo livello da mantenere.

Per rendere persistenti molti oggetti di stato diversi e utilizzare sottoinsiemi diversi di oggetti in posizioni diverse, è preferibile evitare di gestire il caricamento e il salvataggio dello stato a livello globale.
