---
title: BlazorGestione dello stato ASP.NET Core
author: guardrex
description: Informazioni su come salvare in modo permanente lo stato nelle Blazor app Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: 59adcce972b503a6aa6e596bc9bff63225961f84
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243200"
---
# <a name="aspnet-core-blazor-state-management"></a>BlazorGestione dello stato ASP.NET Core

Di [Steve Sanderson](https://github.com/SteveSandersonMS)

BlazorIl server è un Framework di app con stato. Nella maggior parte dei casi, l'app mantiene una connessione continuativa al server. Lo stato dell'utente viene mantenuto nella memoria del server in un *circuito*. 

Di seguito sono riportati alcuni esempi di stato utilizzati per il circuito di un utente:

* Interfaccia utente sottoposta a rendering: la gerarchia delle istanze dei componenti e l'output di rendering più recente.
* Valori dei campi e delle proprietà nelle istanze del componente.
* Dati contenuti nelle istanze del servizio [di inserimento delle dipendenze](xref:fundamentals/dependency-injection) che hanno come ambito il circuito.

> [!NOTE]
> Questo articolo risolve la persistenza dello stato nelle Blazor app Server. BlazorLe app webassembly possono sfruttare [la persistenza dello stato sul lato client nel browser](#client-side-in-the-browser) , ma richiedono soluzioni personalizzate o pacchetti di terze parti oltre l'ambito di questo articolo.

## <a name="blazor-circuits"></a>Blazorcircuiti

Se un utente riscontra una perdita di connessione di rete temporanea, Blazor tenta di riconnettere l'utente al circuito originale per poter continuare a usare l'app. Tuttavia, la riconnessione di un utente al circuito originale nella memoria del server non è sempre possibile:

* Il server non è in grado di mantenere sempre un circuito disconnesso. Il server deve rilasciare un circuito disconnesso dopo un timeout o quando il server è sottoposto a un numero eccessivo di richieste di memoria.
* Negli ambienti di distribuzione con bilanciamento del carico multiserver tutte le richieste di elaborazione del server potrebbero non essere più disponibili in un determinato momento. I singoli server possono avere esito negativo o essere rimossi automaticamente quando non sono più necessari per gestire il volume complessivo delle richieste. Il server originale potrebbe non essere disponibile quando l'utente tenta di riconnettersi.
* L'utente potrebbe chiudere e riaprire il browser o ricaricare la pagina, che rimuove qualsiasi stato contenuto nella memoria del browser. Ad esempio, i valori impostati tramite le chiamate di interoperabilità JavaScript andranno perduti.

Quando un utente non può essere riconnesso al circuito originale, l'utente riceve un nuovo circuito con uno stato vuoto. Questa operazione equivale a chiudere e riaprire un'app desktop.

## <a name="preserve-state-across-circuits"></a>Mantieni stato tra circuiti

In alcuni scenari è consigliabile mantenere lo stato tra i circuiti. Un'app può conservare dati importanti per un utente se:

* Il server Web non è più disponibile.
* Il browser dell'utente è forzato ad avviare un nuovo circuito con un nuovo server Web.

In generale, la gestione dello stato tra circuiti si applica agli scenari in cui gli utenti stanno creando attivamente dati, non semplicemente leggendo i dati già esistenti.

Per mantenere lo stato oltre un singolo circuito, *non archiviare semplicemente i dati nella memoria del server*. L'app deve salvare i dati in modo permanente in un altro percorso di archiviazione. La persistenza dello stato non è automatica. È necessario eseguire i passaggi quando si sviluppa l'app per implementare la persistenza dei dati con stato.

La persistenza dei dati è in genere necessaria solo per uno stato di valore elevato che gli utenti hanno impiegato per creare. Negli esempi seguenti lo stato di salvataggio permanente Risparmia tempo o facilita le attività commerciali:

* WebForm in più passaggi: è necessario che un utente immetta nuovamente i dati per diversi passaggi completati di un processo a più passaggi se il relativo stato viene perso. Un utente perde lo stato in questo scenario se si allontana dal modulo a più passaggi e torna al modulo in un secondo momento.
* Carrello acquisti: è possibile mantenere qualsiasi componente commerciale importante di un'app che rappresenti potenziali ricavi. Un utente che perde il proprio stato e quindi il carrello della spesa può acquistare un minor numero di prodotti o servizi quando ritornano al sito in un secondo momento.

In genere non è necessario mantenere lo stato facilmente ricreato, ad esempio il nome utente immesso in una finestra di dialogo di accesso che non è stata inviata.

> [!IMPORTANT]
> Un'app può solo salvare *lo stato dell'app*. Non è possibile rendere permanente le interfacce utente, ad esempio le istanze dei componenti e le relative strutture di rendering. I componenti e gli alberi di rendering non sono in genere serializzabili. Per salvare in modo permanente un elemento simile allo stato dell'interfaccia utente, ad esempio i nodi espansi di un TreeView, l'app deve avere codice personalizzato per modellare il comportamento come stato dell'app serializzabile.

## <a name="where-to-persist-state"></a>Posizione in cui salvare lo stato

Esistono tre percorsi comuni per lo stato permanente in un' Blazor app Server. Ogni approccio è più adatto a scenari diversi e presenta diverse avvertenze:

* [Lato server in un database](#server-side-in-a-database)
* [URL](#url)
* [Lato client nel browser](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Lato server in un database

Per la persistenza dei dati permanente o per tutti i dati che devono estendersi a più utenti o dispositivi, un database lato server indipendente è quasi certamente la scelta migliore. Le opzioni includono:

* Database SQL relazionale
* Archivio chiave-valore
* Archivio BLOB
* Archivio tabelle

Dopo che i dati sono stati salvati nel database, un nuovo circuito può essere avviato da un utente in qualsiasi momento. I dati dell'utente vengono conservati e resi disponibili in qualsiasi nuovo circuito.

Per altre informazioni sulle opzioni di archiviazione dei dati di Azure, vedere la [documentazione di archiviazione](/azure/storage/) di Azure e i [database di Azure](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>URL

Per i dati temporanei che rappresentano lo stato di navigazione, modellare i dati come parte dell'URL. Esempi di stato modellati nell'URL includono:

* ID di un'entità visualizzata.
* Numero di pagina corrente in una griglia di paging.

Il contenuto della barra degli indirizzi del browser viene mantenuto:

* Se l'utente ricarica manualmente la pagina.
* Se il server Web non è più disponibile e l'utente è costretto a ricaricare la pagina per connettersi a un server diverso.

Per informazioni sulla definizione di modelli URL con la `@page` direttiva, vedere <xref:blazor/fundamentals/routing> .

### <a name="client-side-in-the-browser"></a>Lato client nel browser

Per i dati temporanei che l'utente sta creando attivamente, un archivio di backup comune è costituito dalle `localStorage` raccolte e del browser `sessionStorage` . L'app non è necessaria per gestire o cancellare lo stato archiviato se il circuito viene abbandonato, il che rappresenta un vantaggio rispetto all'archiviazione sul lato server.

> [!NOTE]
> "Lato client" in questa sezione si riferisce agli scenari lato client nel browser, non al [ Blazor modello di hosting webassembly](xref:blazor/hosting-models#blazor-webassembly). `localStorage`e `sessionStorage` possono essere usati nelle Blazor app webassembly, ma solo scrivendo codice personalizzato o usando un pacchetto di terze parti.

`localStorage`e si `sessionStorage` differenziano nel modo seguente:

* `localStorage`ha come ambito il browser dell'utente. Se l'utente ricarica la pagina o chiude e riapre il browser, lo stato è permanente. Se l'utente apre più schede del browser, lo stato viene condiviso tra le schede. I dati rimangono in `localStorage` fino a quando non vengono cancellati in modo esplicito.
* `sessionStorage`ha come ambito la scheda del browser dell'utente. Se l'utente ricarica la scheda, lo stato è permanente. Se l'utente chiude la scheda o il browser, lo stato viene perso. Se l'utente apre più schede del browser, ogni scheda ha una propria versione indipendente dei dati.

In genere, `sessionStorage` è più sicuro da usare. `sessionStorage`evita il rischio che un utente apra più schede e riscontri quanto segue:

* Bug nell'archiviazione dello stato tra le schede.
* Comportamento confuso quando una scheda sovrascrive lo stato di altre schede.

`localStorage`è la scelta migliore se l'app deve conservare lo stato tra la chiusura e la riapertura del browser.

Avvertenze per l'uso dell'archiviazione del browser:

* Analogamente all'utilizzo di un database lato server, il caricamento e il salvataggio dei dati sono asincroni.
* A differenza di un database lato server, l'archiviazione non è disponibile durante il prerendering perché la pagina richiesta non esiste nel browser durante la fase di pre-rendering.
* L'archiviazione di alcuni kilobyte di dati è ragionevole per la permanenza per le Blazor app Server. Oltre alcuni kilobyte, è necessario considerare le implicazioni relative alle prestazioni perché i dati vengono caricati e salvati in rete.
* Gli utenti possono visualizzare o manomettere i dati. ASP.NET Core [protezione dei dati](xref:security/data-protection/introduction) può ridurre il rischio.

## <a name="third-party-browser-storage-solutions"></a>Soluzioni di archiviazione del browser di terze parti

I pacchetti NuGet di terze parti forniscono le API per l'uso di `localStorage` e `sessionStorage` .

Vale la pena considerare la scelta di un pacchetto che usa in modo trasparente la [protezione dei dati](xref:security/data-protection/introduction)ASP.NET Core. ASP.NET Core protezione dei dati consente di crittografare i dati archiviati e di ridurre il rischio potenziale di manomissioni dei dati archiviati. Se i dati serializzati in JSON vengono archiviati in testo non crittografato, gli utenti possono visualizzare i dati usando gli strumenti di sviluppo del browser e modificare anche i dati archiviati. La protezione dei dati non è sempre un problema perché i dati potrebbero essere di natura banale. Ad esempio, la lettura o la modifica del colore archiviato di un elemento dell'interfaccia utente non costituisce un rischio di sicurezza significativo per l'utente o l'organizzazione. Evitare di consentire agli utenti di ispezionare o manomettere *i dati sensibili*.

## <a name="protected-browser-storage-experimental-package"></a>Pacchetto sperimentale di archiviazione del browser protetto

Un esempio di pacchetto NuGet che fornisce la [protezione dei dati](xref:security/data-protection/introduction) per `localStorage` e `sessionStorage` è [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`è un pacchetto sperimentale non supportato non adatto per l'uso in fase di produzione.

### <a name="installation"></a>Installazione

Per installare il `Microsoft.AspNetCore.ProtectedBrowserStorage` pacchetto:

1. Nel Blazor progetto di applicazione server aggiungere un riferimento al pacchetto a [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .
1. Nel codice HTML di primo livello (ad esempio, nel `Pages/_Host.cshtml` file nel modello di progetto predefinito) aggiungere il `<script>` tag seguente:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. Nel `Startup.ConfigureServices` metodo chiamare `AddProtectedBrowserStorage` per aggiungere `localStorage` e `sessionStorage` servizi alla raccolta di servizi:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Salvare e caricare i dati all'interno di un componente

In tutti i componenti che richiedono il caricamento o il salvataggio dei dati nell'archiviazione del browser, usare [`@inject`](xref:mvc/views/razor#inject) per inserire un'istanza di uno dei seguenti elementi:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

La scelta dipende dall'archivio di backup che si desidera utilizzare. Nell'esempio seguente `sessionStorage` viene usato:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

L' `@using` istruzione può essere inserita in un `_Imports.razor` file anziché nel componente. L'uso del `_Imports.razor` file rende lo spazio dei nomi disponibile a segmenti più grandi dell'app o dell'intera app.

Per salvare in modo permanente il `currentCount` valore nel `Counter` componente del modello di progetto, modificare il `IncrementCount` metodo in modo da usare `ProtectedSessionStore.SetAsync` :

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Nelle app più grandi e più realistiche, l'archiviazione dei singoli campi è uno scenario improbabile. È più probabile che le app memorizzino interi oggetti modello che includono lo stato complesso. `ProtectedSessionStore`serializza e deserializza automaticamente i dati JSON.

Nell'esempio di codice precedente i `currentCount` dati vengono archiviati come `sessionStorage['count']` nel browser dell'utente. I dati non vengono archiviati in testo non crittografato, ma vengono protetti usando la [protezione dei dati](xref:security/data-protection/introduction)di ASP.NET Core. I dati crittografati possono essere visualizzati se `sessionStorage['count']` viene valutato nella console per sviluppatori del browser.

Per ripristinare i `currentCount` dati se l'utente torna al `Counter` componente in un secondo momento (anche se si trovano in un circuito completamente nuovo), usare `ProtectedSessionStore.GetAsync` :

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Se i parametri del componente includono lo stato di navigazione, chiamare `ProtectedSessionStore.GetAsync` e assegnare il risultato in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , non <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> . <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>viene chiamato una sola volta quando viene creata la prima istanza del componente. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>non viene chiamato di nuovo in un secondo momento se l'utente passa a un URL diverso rimanendo nella stessa pagina. Per altre informazioni, vedere <xref:blazor/components/lifecycle>.

> [!WARNING]
> Gli esempi in questa sezione funzionano solo se per il server non è abilitato il prerendering. Con il prerendering abilitato, viene generato un errore simile al seguente:
>
> > Non è possibile eseguire le chiamate di interoperabilità JavaScript in questo momento. Il motivo è che il componente viene preeseguito.
>
> Disabilitare il prerendering o aggiungere altro codice per lavorare con il prerendering. Per ulteriori informazioni sulla scrittura di codice che funziona con il prerendering, vedere la sezione [handle prerendering](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Gestire lo stato di caricamento

Poiché l'archiviazione del browser è asincrona (a cui si accede tramite una connessione di rete), si verifica sempre un periodo di tempo prima che i dati vengano caricati e disponibili per l'uso da parte di un componente. Per ottenere risultati ottimali, è possibile eseguire il rendering di un messaggio di stato di caricamento mentre è in corso il caricamento anziché visualizzare dati vuoti o predefiniti.

Un approccio consiste nel rilevare se i dati sono ancora in corso di `null` caricamento o meno. Nel componente predefinito `Counter` , il conteggio viene mantenuto in un oggetto `int` . Rendere `currentCount` nullable aggiungendo un punto interrogativo ( `?` ) al tipo ( `int` ):

```csharp
private int? currentCount;
```

Anziché visualizzare in modo non condizionale il numero e il **`Increment`** pulsante, scegliere di visualizzare questi elementi solo se i dati vengono caricati:

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Gestione preliminare

Durante il prerendering:

* Una connessione interattiva al browser dell'utente non esiste.
* Il browser non dispone ancora di una pagina in cui è possibile eseguire il codice JavaScript.

`localStorage`o `sessionStorage` non sono disponibili durante il prerendering. Se il componente tenta di interagire con l'archiviazione, viene generato un errore simile al seguente:

> Non è possibile eseguire le chiamate di interoperabilità JavaScript in questo momento. Il motivo è che il componente viene preeseguito.

Un modo per risolvere l'errore consiste nel disabilitare il prerendering. Questa è in genere la scelta migliore se l'app usa in modo intensivo l'archiviazione basata su browser. Il prerendering aggiunge complessità e non è vantaggioso per l'app perché l'app non può prerenderizzare contenuti utili fino a quando non `localStorage` `sessionStorage` sono disponibili o.

Per disabilitare il prerendering, aprire il `Pages/_Host.cshtml` file e impostare l' `render-mode` [Helper tag del componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) su <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> .

Il prerendering può essere utile per altre pagine che non utilizzano `localStorage` o `sessionStorage` . Per rendere abilitato il prerendering, rinviare l'operazione di caricamento finché il browser non è connesso al circuito. Di seguito è riportato un esempio per l'archiviazione di un valore del contatore:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Fattorizzare la conservazione dello stato in una posizione comune

Se molti componenti si basano sull'archiviazione basata su browser, la reimplementazione del codice del provider di stato più volte crea una duplicazione del codice. Un'opzione per evitare la duplicazione del codice consiste nel creare un *componente padre del provider di stato* che incapsula la logica del provider di stato. I componenti figlio possono funzionare con i dati persistenti senza considerare il meccanismo di persistenza dello stato.

Nell'esempio seguente di un `CounterStateProvider` componente, i dati del contatore vengono mantenuti:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
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
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

Il `CounterStateProvider` componente gestisce la fase di caricamento non eseguendo il rendering del relativo contenuto figlio fino al completamento del caricamento.

Per utilizzare il `CounterStateProvider` componente, eseguire il wrapping di un'istanza del componente intorno a qualsiasi altro componente che richiede l'accesso allo stato del contatore. Per rendere lo stato accessibile a tutti i componenti di un'app, eseguire il wrapping del `CounterStateProvider` componente intorno all'oggetto <xref:Microsoft.AspNetCore.Components.Routing.Router> nel `App` componente ( `App.razor` ):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

I componenti di cui è stato eseguito il wrapper ricevono e possono modificare lo stato del contatore permanente. Il `Counter` componente seguente implementa il modello:

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

Il componente precedente non è necessario per interagire con `ProtectedBrowserStorage` , né gestire una fase di "caricamento".

Per gestire il prerendering come descritto in precedenza, `CounterStateProvider` può essere modificato in modo che tutti i componenti che utilizzano i dati del contatore funzionino automaticamente con il prerendering. Per informazioni dettagliate, vedere la sezione [handle prerendering](#handle-prerendering) .

In generale, è consigliabile usare il modello di *componente padre del provider di stato* :

* Per utilizzare lo stato in molti altri componenti.
* Se è presente un solo oggetto di stato di primo livello da salvare in modo permanente.

Per salvare in modo permanente molti oggetti di stato diversi e utilizzare subset diversi di oggetti in posizioni diverse, è preferibile evitare di gestire il caricamento e il salvataggio dello stato a livello globale.
