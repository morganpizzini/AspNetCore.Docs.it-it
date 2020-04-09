---
title: Creazione di applicazioni Web Blazor progressive con ASP.NET WebAssembly di base
author: guardrex
description: Informazioni su come Blazorcreare un'applicazione Web progressiva basata su PWA che utilizza le funzionalità del browser moderno per comportarsi come un'app desktop.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: fe69e51aefae9c80e5bb4b78151d384ce25d41a7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218947"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a>Creazione di applicazioni Web progressive con ASP.NETCore Blazor WebAssembly

Di [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Un'applicazione Web progressiva (PWA) è un'applicazione a pagina singola (SPA) che utilizza le moderne API del browser e le funzionalità per comportarsi come un'applicazione desktop. Blazor WebAssembly è una piattaforma di app Web lato client basata su standard, pertanto può usare qualsiasi API del browser, incluse le API di PWA necessarie per le funzionalità seguenti:

* Lavorare offline e caricare istantaneamente, indipendentemente dalla velocità della rete.
* In esecuzione nella propria finestra dell'app, non solo in una finestra del browser.
* Avvio dal menu di avvio del sistema operativo, dal dock o dalla schermata iniziale dell'host.
* Ricezione di notifiche push da un server back-end, anche se l'utente non usa l'app.
* Aggiornamento automatico in background.

La parola *progressivo* viene utilizzata per descrivere tali app perché:

* Un utente potrebbe prima scoprire e utilizzare l'applicazione all'interno del proprio browser web come qualsiasi altra SPA.
* Successivamente, l'utente progredisce a installarlo nel proprio sistema operativo e l'abilitazione delle notifiche push.

## <a name="create-a-project-from-the-pwa-template"></a>Creare un progetto dal modello di Project Web Access

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Quando si crea una nuova **applicazione Blazor WebAssembly** nella finestra di dialogo **Crea un nuovo progetto,** selezionare la casella di controllo **Stato di avanzamento applicazione Web:**

![La casella di controllo 'Applicazione Web progressiva' è selezionata nella finestra di dialogo Nuovo progetto di Visual Studio.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/Interfaccia della riga di comando di .NET Core](#tab/visual-studio-code+netcore-cli)

Creare un progetto di Project Web `--pwa` Access in una shell dei comandi con l'opzione:

```dotnetcli
dotnet new blazorwasm -o MyNewProject --pwa
```

---

Facoltativamente, PWA può essere configurato per un'app creata dal modello Hosted di ASP.NET core. Lo scenario di Project Web Access è indipendente dal modello di hosting.

## <a name="installation-and-app-manifest"></a>Manifesto dell'installazione e dell'app

Quando si visita un'app creata utilizzando il modello di Project Web Access, gli utenti hanno la possibilità di installare l'app nel menu di avvio del sistema operativo, nel dock o nella schermata iniziale. Il modo in cui questa opzione viene presentata dipende dal browser dell'utente. Quando si utilizzano browser desktop basati su Chromium, ad esempio Edge o Chrome, viene visualizzato un pulsante **Aggiungi** all'interno della barra degli URL. Dopo che l'utente seleziona il pulsante **Aggiungi,** riceve una finestra di dialogo di conferma:After the user selects the Add button, they receive a confirmation dialog:

![Il diaglog di conferma in Google Chrome presenta un pulsante Installa per l'utente per l'app 'MyBlazorPwa'.](progressive-web-app/_static/image2.png)

Su iOS, i visitatori possono installare PWA utilizzando il pulsante **Condividi** di Safari e la relativa opzione **Aggiungi alla schermata Home.** Su Chrome per Android, gli utenti devono selezionare il pulsante **Menu** nell'angolo in alto a destra, seguito da **Aggiungi alla schermata Home**.

Una volta installata, l'app viene visualizzata nella propria finestra senza una barra degli indirizzi:

![L'app "MyBlazorPwa" viene eseguita in Google Chrome senza una barra degli indirizzi.](progressive-web-app/_static/image3.png)

Per personalizzare il titolo della finestra, la combinazione di colori, l'icona o altri dettagli, vedere il file *manifest.json* nella directory *wwwroot* del progetto. Lo schema di questo file è definito dagli standard Web. Per ulteriori informazioni, vedere [Documenti Web MDN: Manifesto dell'app Web](https://developer.mozilla.org/docs/Web/Manifest).

## <a name="offline-support"></a>Supporto offline

Per impostazione predefinita, le app create utilizzando l'opzione del modello di Project Web Access dispongono del supporto per l'esecuzione offline. Un utente deve prima visitare l'app mentre è online. Il browser scarica e memorizza automaticamente nella cache tutte le risorse necessarie per operare offline.

> [!IMPORTANT]
> Il supporto allo sviluppo interferirebbe con il consueto ciclo di sviluppo di apportare modifiche e testarle. Pertanto, il supporto offline è abilitato solo per le app *pubblicate.* 

> [!WARNING]
> Se si intende distribuire un Project Web Access abilitato per la modalità offline, sono presenti [diversi avvisi e avvertenze importanti.](#caveats-for-offline-pwas) Questi scenari sono intrinseci ai PWA offline e non specifici di Blazor. Assicurati di leggere e comprendere queste avvertenze prima di formulare ipotesi sul funzionamento dell'app abilitata per la modalità offline.

Per vedere come funziona il supporto offline:

1. Pubblicare l'app. Per altre informazioni, vedere <xref:host-and-deploy/blazor/index#publish-the-app>.
1. Distribuire l'app in un server che supporta HTTPS e accedere all'app in un browser al relativo indirizzo HTTPS sicuro.
1. Aprire gli strumenti di sviluppo del browser e verificare che un *Service Worker* sia registrato per l'host nella scheda **Applicazione:**

   ![La scheda "Applicazione" degli strumenti di sviluppo di Google Chrome mostra un Service Worker attivato e in esecuzione.](progressive-web-app/_static/image4.png)

1. Ricaricare la pagina ed esaminare la scheda **Rete.** Service **Worker** o **cache di memoria** sono elencati come origini per tutte le risorse della pagina:

   ![Scheda "Rete" degli strumenti di sviluppo di Google Chrome che mostra le fonti per tutte le risorse della pagina.](progressive-web-app/_static/image5.png)

1. Per verificare che il browser non dipenda dall'accesso di rete per caricare l'app:

   * Arrestare il server Web e vedere come l'applicazione continua a funzionare normalmente, che include i ricaricamento della pagina. Allo stesso modo, l'applicazione continua a funzionare normalmente quando c'è una connessione di rete lenta.
   * Indicare al browser di simulare la modalità offline nella scheda **Rete:**

   ![Scheda "Rete" degli strumenti di sviluppo di Google Chrome con il menu a discesa della modalità browser modificata da "Online" a "Offline".](progressive-web-app/_static/image6.png)

Il supporto offline tramite un service worker Blazorè uno standard Web, non specifico di . Per altre informazioni sugli operatori del servizio, vedere [Documenti Web MDN: API Service Worker](https://developer.mozilla.org/docs/Web/API/Service_Worker_API). Per ulteriori informazioni sui modelli di utilizzo comuni per i service worker, consultate [Google Web: The Service Worker Lifecycle](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

BlazorIl modello pWA produce due file di lavoro del servizio:

* *wwwroot/service-worker.js*, utilizzato durante lo sviluppo.
* *wwwroot/service-worker.published.js*, utilizzato dopo la pubblicazione dell'app.

Per condividere la logica tra i due file di lavoro del servizio, considerare l'approccio seguente:To share logic between the two service worker files, consider the following approach:

* Aggiungere un terzo file JavaScript per contenere la logica comune.
* Usare [self.importScripts](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) per caricare la logica comune in entrambi i file di lavoro del servizio.

### <a name="cache-first-fetch-strategy"></a>Strategia di recupero con prima cache

Il *service-worker.published.js* incorporato risolve le richieste usando una strategia *cache-first.* Ciò significa che il service worker preferisce restituire il contenuto memorizzato nella cache, indipendentemente dal fatto che l'utente disponga dell'accesso alla rete o che il contenuto più recente sia disponibile nel server.

La strategia cache-first è utile perché:

* **Garantisce affidabilità.** &ndash;L'accesso alla rete non è uno stato booleano. Un utente non è semplicemente online o offline:

  * Il dispositivo dell'utente può presupporre che sia online, ma la rete potrebbe essere così lenta da essere poco pratica da attendere.
  * La rete potrebbe restituire risultati non validi per determinati URL, ad esempio quando è presente un portale WIFI captive che attualmente blocca o reindirizza determinate richieste.
  
  Questo è il motivo per cui l'API del `navigator.onLine` browser non è affidabile e non dovrebbe dipendere.

* **Garantisce la correttezza.** &ndash;Quando si crea una cache di risorse offline, il service worker usa l'hashing del contenuto per garantire che abbia recuperato uno snapshot completo e autocoerente delle risorse in un singolo istante nel tempo. Questa cache viene quindi utilizzata come unità atomica. Non ha senso chiedere alla rete risorse più recenti, poiché le uniche versioni richieste sono quelle già memorizzate nella cache. Qualsiasi altra cosa rischia l'incoerenza e l'incompatibilità (ad esempio, il tentativo di utilizzare versioni di assembly .NET che non sono stati compilati insieme).

### <a name="background-updates"></a>Aggiornamenti in background

Come modello mentale, è possibile pensare a un PWA offline-first come comportarsi come un'app per dispositivi mobili che può essere installata. L'app viene avviata immediatamente indipendentemente dalla connettività di rete, ma la logica dell'app installata proviene da uno snapshot temporizzato che potrebbe non essere la versione più recente.

Il Blazor modello di Project Web Access produce app che tentano automaticamente di aggiornarsi in background ogni volta che l'utente visita e dispone di una connessione di rete funzionante. Il modo in cui funziona è il seguente:

* Durante la compilazione, il progetto genera un *manifesto degli asset*del service worker . Per impostazione predefinita, questo è chiamato *service-worker-assets.js*. Il manifesto elenca tutte le risorse statiche necessarie per la funzione offline da parte dell'app, ad esempio assembly .NET, file JavaScript e CSS, inclusi gli iste del contenuto. L'elenco delle risorse viene caricato dal service worker in modo che sappia quali risorse memorizzare nella cache.
* Ogni volta che l'utente visita l'app, il browser richiede nuovamente *service-worker.js* e *service-worker-assets.js* in background. I file vengono confrontati byte per byte con il service worker installato esistente. Se il server restituisce il contenuto modificato per uno di questi file, il service worker tenta di installare una nuova versione di se stesso.
* Quando si installa una nuova versione di se stessa, il service worker crea una nuova cache separata per le risorse offline e inizia a popolare la cache con le risorse elencate in *service-worker-assets.js*. Questa logica viene `onInstall` implementata nella funzione all'interno di *service-worker.published.js*.
* Il processo viene completato correttamente quando tutte le risorse vengono caricate senza errori e tutti gli errori di contenuto corrispondono. In caso di esito positivo, il nuovo service worker immette uno stato *di attesa per l'attivazione.* Non appena l'utente chiude l'app (nessuna finestra o schede dell'app rimanente), il nuovo service worker diventa *attivo* e viene usato per le visite successive all'app. Il vecchio service worker e la relativa cache vengono eliminati.
* Se il processo non viene completato correttamente, la nuova istanza del lavoratore del servizio viene eliminata. Il processo di aggiornamento viene tentato nuovamente alla successiva visita dell'utente, quando si spera che il client disponga di una connessione di rete migliore in grado di completare le richieste.

Personalizzare questo processo modificando la logica di lavoro del servizio. Nessuno dei comportamenti precedenti è Blazor specifico, ma è semplicemente l'esperienza predefinita fornita dall'opzione del modello di Project Web Access. Per altre informazioni, vedere [Documenti Web MDN: API Service Worker](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).

### <a name="how-requests-are-resolved"></a>Modalità di risoluzione delle richieste

Come descritto nella sezione Strategia di [recupero di primo cache,](#cache-first-fetch-strategy) il service worker predefinito utilizza una strategia *cache-first,* vale a dire che tenta di gestire il contenuto memorizzato nella cache quando disponibile. Se non è presente alcun contenuto memorizzato nella cache per un determinato URL, ad esempio quando si richiedono dati da un'API back-end, il service worker esegue il fallback a una normale richiesta di rete. La richiesta di rete ha esito positivo se il server è raggiungibile. Questa logica viene `onFetch` implementata all'interno della funzione all'interno *di service-worker.published.js*.

Se i componenti Razor dell'app si basano sulla richiesta di dati dalle API back-end e vuoi fornire un'esperienza utente intuitiva per le richieste non riuscite a causa dell'indisponibilità della rete, implementa la logica all'interno dei componenti dell'app. Ad esempio, `try/catch` `HttpClient` utilizzare intorno alle richieste.

### <a name="support-server-rendered-pages"></a>Supporto delle pagine con rendering del server

Considera cosa accade quando l'utente passa `/counter` per la prima volta a un URL, ad esempio o qualsiasi altro collegamento diretto nell'app. In questi casi, non si desidera restituire `/counter`il contenuto memorizzato nella cache `/index.html` come , Blazor ma è necessario che il browser carichi il contenuto memorizzato nella cache per avviare l'app WebAssembly. Queste richieste iniziali sono note come richieste di *navigazione,* a differenza di:These initial requests are known as navigation requests, at pposed to:

* *richieste di risorse secondarie* per immagini, fogli di stile o altri file.
* *recuperare/XHR* richieste per i dati API.

Il service worker predefinito contiene la logica del caso speciale per le richieste di navigazione. Il service worker risolve le richieste restituendo il contenuto memorizzato nella cache per `/index.html`, indipendentemente dall'URL richiesto. Questa logica viene `onFetch` implementata nella funzione all'interno di *service-worker.published.js*.

Se l'app ha determinati URL che devono restituire codice `/index.html` HTML sottoposto a rendering del server e non essere serviti dalla cache, devi modificare la logica nel service worker. Se tutti gli `/Identity/` URL contenenti devono essere gestiti come normali richieste solo online al server, modificare la logica *service-worker.published.js.* `onFetch` Individuare il codice seguente:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Modificare il codice nel modo seguente:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Se non si fa questa operazione, indipendentemente dalla connettività di rete, il service worker `/index.html`intercetta le richieste per tali URL e le risolve utilizzando .

### <a name="control-asset-caching"></a>Controllare la memorizzazione nella cache degli assetControl asset caching

Se il progetto `ServiceWorkerAssetsManifest` definisce la Blazorproprietà MSBuild, gli strumenti di compilazione generano un manifesto degli asset del service worker con il nome specificato. Il modello di Project Web Access predefinito produce un file di progetto contenente la proprietà seguente:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

Il file viene inserito nella directory di output *wwwroot,* `/service-worker-assets.js`in modo che il browser possa recuperarlo richiedendo . Per visualizzare il contenuto di questo file, aprire */bin/Debug/'TARGET FRAMEWORK'/wwwroot/service-worker-assets.js* in un editor di testo. Tuttavia, non modificare il file, poiché viene rigenerato in ogni compilazione.

Per impostazione predefinita, questo manifesto elenca:By default, this manifest lists:

* Tutte Blazorle risorse gestite, ad esempio gli assembly .NET e i file di runtime di WebAssembly .NET necessari per funzionare offline.
* Tutte le risorse per la pubblicazione nella directory *wwwroot* dell'app, ad esempio immagini, fogli di stile e file JavaScript, incluse le risorse Web statiche fornite da progetti esterni e i pacchetti NuGet.

È possibile controllare quali di queste risorse vengono recuperate e `onInstall` memorizzate nella cache dal service worker modificando la logica in *service-worker.published.js*. Per impostazione predefinita, il service worker recupera e memorizza nella cache i file corrispondenti alle estensioni tipiche dei nomi di file Web, ad esempio *.html*, *.css*, *.js*e *.wasm*, oltre ai tipi di file specifici di Blazor WebAssembly (*.dll*, *.pdb*).

Per includere risorse aggiuntive che non sono presenti nella directory wwwroot `ItemGroup` dell'app, definire voci MSBuild aggiuntive, come illustrato nell'esempio seguente:To include additional resources that aren's present in the app's *wwwroot* directory, define extra MSBuild entries, as shown in the following example:

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

I `AssetUrl` metadati specificano l'URL relativo alla base che il browser deve utilizzare durante il recupero della risorsa da memorizzare nella cache. Questo può essere indipendente dal nome del file di origine originale su disco.

> [!IMPORTANT]
> L'aggiunta di a `ServiceWorkerAssetsManifestItem` non determina la pubblicazione del file nella directory *wwwroot* dell'app. L'output di pubblicazione deve essere controllato separatamente. L'unico `ServiceWorkerAssetsManifestItem` fa sì che una voce aggiuntiva venga visualizzata nel manifesto delle risorse del service worker.

## <a name="push-notifications"></a>Notifiche push

Come qualsiasi altro PWA, un Blazor WebAssembly PWA può ricevere notifiche push da un server back-end. Il server può inviare notifiche push in qualsiasi momento, anche quando l'utente non utilizza attivamente l'app. Ad esempio, le notifiche push possono essere inviate quando un altro utente esegue un'azione pertinente.

Il meccanismo per l'invio Blazor di una notifica push è completamente indipendente da WebAssembly, poiché viene implementato dal server back-end che può utilizzare qualsiasi tecnologia. Se si desidera inviare notifiche push da un server ASP.NET Core, prendere in considerazione [l'utilizzo di una tecnica simile all'approccio adottato nel workshop Blazing Pizza](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).

Il meccanismo per la ricezione e la visualizzazione Blazor di una notifica push sul client è indipendente anche da WebAssembly, poiché è implementato nel file JavaScript di service worker. Per un esempio, vedere [l'approccio utilizzato nel workshop Blazing Pizza](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Avvertenze per PWA offline

Non tutte le app devono tentare di supportare l'uso offline. Il supporto offline aggiunge una complessità significativa, pur non essendo sempre rilevante per i casi d'uso richiesti.

Il supporto offline è in genere rilevante solo:

* Se l'archivio dati primario è locale nel browser. Ad esempio, l'approccio è rilevante in un'app con un'interfaccia utente per un dispositivo [IoT](https://en.wikipedia.org/wiki/Internet_of_things) che archivia i dati in `localStorage` o [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).
* Se l'app esegue una quantità significativa di lavoro per recuperare e memorizzare nella cache i dati dell'API back-end rilevanti per ogni utente in modo che possa spostarsi tra i dati offline. Se l'app deve supportare la modifica, è necessario creare un sistema per tenere traccia delle modifiche e sincronizzare i dati con il back-end.
* Se l'obiettivo è garantire che l'app venga caricata immediatamente indipendentemente dalle condizioni di rete. Implementare un'esperienza utente adatta intorno alle richieste API back-end per mostrare lo stato di avanzamento delle richieste e comportarsi normalmente quando le richieste non riescono a causa di indisponibilità della rete.

Inoltre, i PWA offline devono affrontare una serie di complicazioni aggiuntive. Gli sviluppatori devono acquisire familiarità con le avvertenze riportate nelle sezioni seguenti.

### <a name="offline-support-only-when-published"></a>Supporto offline solo quando viene pubblicato

Durante lo sviluppo in genere si desidera vedere ogni modifica riflessa immediatamente nel browser senza passare attraverso un processo di aggiornamento in background. Pertanto, Blazoril modello di Project Web Access di 's abilita il supporto offline solo quando viene pubblicato.

Quando si crea un'app offline, non è sufficiente testare l'app nell'ambiente di sviluppo. È necessario testare l'app nello stato pubblicato per comprendere come risponde alle diverse condizioni di rete.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Aggiornamento del completamento dopo la navigazione dell'utente dall'app

Gli aggiornamenti non vengono completati fino a quando l'utente non si è allontanato dall'app in tutte le schede. Come spiegato nella sezione Aggiornamenti in [background,](#background-updates) dopo aver distribuito un aggiornamento all'app, il browser recupera i file di lavoro del servizio aggiornati per avviare il processo di aggiornamento.

Ciò che sorprende molti sviluppatori è che, anche quando questo aggiornamento viene completato, **non** ha effetto fino a quando l'utente non è navigato in tutte le schede. **Non** è sufficiente aggiornare la scheda che visualizza l'app, anche se è l'unica scheda che visualizza l'app. Finché l'app non viene chiusa completamente, il nuovo service worker rimane in *attesa di attivare* lo stato. **Questo non è Blazorspecifico di , ma piuttosto è un comportamento della piattaforma web standard.**

Ciò problematica in genere gli sviluppatori che stanno tentando di testare gli aggiornamenti al service worker o alle risorse memorizzate nella cache offline. Se si archiviano gli strumenti di sviluppo del browser, è possibile che venga visualizzato un elemento simile al seguente:

![La scheda "Applicazione" di Google Chrome mostra che l'assistente di servizio dell'app è "in attesa di attivazione".](progressive-web-app/_static/image7.png)

Finché l'elenco dei "client", che sono schede o finestre che visualizzano l'app, non è vuoto, il lavoratore continua ad attendere. Il motivo per cui i service worker fanno questo è per garantire la coerenza. Coerenza significa che tutte le risorse vengono recuperate dalla stessa cache atomica.

Durante il test delle modifiche, potrebbe essere utile fare clic sul collegamento "skipWaiting" come mostrato nella schermata precedente, quindi ricaricare la pagina. È possibile automatizzare questo per tutti gli utenti codificando il lavoratore del servizio per [saltare la fase di "attesa" e attivare immediatamente all'aggiornamento](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). Se si ignora la fase di attesa, si sta rinunciando alla garanzia che le risorse vengano sempre recuperate in modo coerente dalla stessa istanza della cache.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Gli utenti possono eseguire qualsiasi versione cronologica dell'app

Gli sviluppatori Web si aspettano abitualmente che gli utenti eseguano solo l'ultima versione distribuita dell'app Web, poiché è normale all'interno del modello di distribuzione Web tradizionale. Tuttavia, un PWA offline-first è più simile a un'app per dispositivi mobili nativa, in cui gli utenti non eseguono necessariamente la versione più recente.

Come spiegato nella sezione Aggiornamenti in [background,](#background-updates) dopo aver distribuito un aggiornamento all'app, **ogni utente esistente continua a usare una versione precedente per almeno un'ulteriore visita** perché l'aggiornamento si verifica in background e non viene attivato fino a quando l'utente non si sposta. Inoltre, la versione precedente in uso non è necessariamente quella distribuita. La versione precedente può essere *qualsiasi* versione cronologica, a seconda della data dell'ultimo completamento di un aggiornamento da parte dell'utente.

Questo può essere un problema se le parti front-end e back-end dell'app richiedono un accordo sullo schema per le richieste API. Non è necessario distribuire le modifiche dello schema dell'API non compatibili con le versioni precedenti fino a quando non si può essere certi che tutti gli utenti siano stati aggiornati. In alternativa, impedisci agli utenti di usare versioni precedenti incompatibili dell'app. Questo requisito di scenario è lo stesso delle app per dispositivi mobili native. Se si distribuisce una modifica sostanziale nelle API del server, l'app client viene interrotta per gli utenti che non sono ancora stati aggiornati.

Se possibile, non distribuire le modifiche di rilievo alle API back-end. In tal caso, è consigliabile usare [le API di Service Worker standard, ad esempio ServiceWorkerRegistration,](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) per determinare se l'app è aggiornata e, in caso contrario, per impedire l'utilizzo.

### <a name="interference-with-server-rendered-pages"></a>Interferenza con le pagine sottoposte a rendering del server

Come descritto nella sezione [Support server di cui è stato eseguito](#support-server-rendered-pages) il `/index.html` rendering, se si desidera ignorare il comportamento del service worker di restituzione del contenuto per tutte le richieste di navigazione, modificare la logica nel service worker.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Tutti i contenuti del manifesto dell'asset del service worker vengono memorizzati nella cache per impostazione predefinitaAll service worker asset manifest contents are cached by default

Come descritto nella sezione Controllare la [memorizzazione nella cache degli asset,](#control-asset-caching) il file *service-worker-assets.js* viene generato durante la compilazione ed elenca tutti gli asset che il service worker deve recuperare e memorizzare nella cache.

Poiché per impostazione predefinita questo elenco include tutto ciò che viene generato in *wwwroot*, inclusi i contenuti forniti da pacchetti e progetti esterni, è necessario fare attenzione a non inserire troppo contenuto. Se la directory *wwwroot* contiene milioni di immagini, il service worker tenta di recuperarle e memorizzarle nella cache tutte, consumando una larghezza di banda eccessiva e molto probabilmente non viene completata correttamente.

Implementare una logica arbitraria per controllare quale sottoinsieme del contenuto `onInstall` del manifesto deve essere recuperato e memorizzato nella cache modificando la funzione in *service-worker.published.js*.

### <a name="interaction-with-authentication"></a>Interazione con l'autenticazione

È possibile utilizzare l'opzione del modello di Project Web Access insieme alle opzioni di autenticazione. Un PWA offline può supportare anche l'autenticazione quando l'utente dispone di connettività di rete.

Quando un utente non dispone di connettività di rete, non può autenticare o ottenere token di accesso. Per impostazione predefinita, il tentativo di visitare la pagina di accesso senza accesso alla rete genera un messaggio di "errore di rete".

È necessario progettare un flusso dell'interfaccia utente che consente all'utente di eseguire operazioni utili in modalità offline senza tentare di autenticare o ottenere i token di accesso. In alternativa, è possibile progettare l'applicazione in modo che non riesca in modo aggraziato quando la rete non è disponibile. Se ciò non è possibile nell'app, è possibile che non si desideri abilitare il supporto offline.
