---
title: modelli Blazor di hosting ASP.NET Core
author: guardrex
description: Comprendere Blazor i Blazor modelli di hosting WebAssembly e Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 0dfc991f76acb227ce9ea27a07fbae50571f0117
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80471823"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>modelli Blazor di hosting ASP.NET Core

Di [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazorè un framework Web progettato per eseguire sul lato client nel browser in un runtime .NET* Blazor *basato su [WebAssembly](https://webassembly.org/)(* Blazor WebAssembly*) o sul lato server in ASP.NET Core ( Server ). Indipendentemente dal modello di hosting, i modelli di app e componenti *sono gli stessi.*

Per creare un progetto per i modelli <xref:blazor/get-started>di hosting descritti in questo articolo, vedere .

Per una configurazione <xref:blazor/hosting-model-configuration>avanzata, vedere .

## <a name="opno-locblazor-webassembly"></a>BlazorAssembly Web

Il modello di Blazor hosting principale per è in esecuzione lato client nel browser su WebAssembly. L'app, Blazor le relative dipendenze e il runtime .NET vengono scaricati nel browser. L'app viene eseguita direttamente nel thread dell'interfaccia utente del browser. Gli aggiornamenti dell'interfaccia utente e la gestione degli eventi si verificano all'interno dello stesso processo. Le risorse dell'app vengono distribuite come file statici in un server Web o in un servizio in grado di fornire contenuto statico ai client.

![BlazorWebAssembly: Blazor l'app viene eseguita in un thread dell'interfaccia utente all'interno del browser.](hosting-models/_static/blazor-webassembly.png)

Per creare Blazor un'app utilizzando il modello di hosting lato client, utilizzare il ** Blazor ** modello WebAssembly App ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).

Dopo aver ** Blazor ** selezionato il modello WebAssembly App, è possibile configurare l'app per l'utilizzo di un back-end ASP.NET Core selezionando la casella di controllo **ospitata ASP.NET Core** [(dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)). L'app ASP.NET Blazor Core fornisce l'app ai client. L'app Blazor WebAssembly può interagire con il server [SignalR](xref:signalr/introduction) in<xref:tutorials/signalr-blazor-webassembly>rete utilizzando chiamate API Web o ( ).

I modelli `blazor.webassembly.js` includono lo script che gestisce:

* Download del runtime di .NET, dell'app e delle dipendenze dell'app.
* Inizializzazione del runtime per eseguire l'app.

Il Blazor modello di hosting WebAssembly offre diversi vantaggi:

* Non esiste alcuna dipendenza sul lato server .NET. L'app è completamente funzionante dopo essere stata scaricata nel client.
* Le risorse e le funzionalità dei client sono sfruttate appieno.
* Il lavoro viene scaricato dal server al client.
* Non è necessario un server Web ASP.NET Core per ospitare l'app. Scenari di distribuzione senza server sono possibili (ad esempio, la gestione dell'app da una rete CDN).

L'hosting di Blazor WebAssembly è dovuto all'hosting:

* L'app è limitata alle funzionalità del browser.
* È necessario utilizzare hardware e software client in grado (ad esempio, il supporto WebAssembly).
* Le dimensioni del download sono maggiori e il caricamento delle app richiede più tempo.
* Il supporto di runtime e strumenti .NET è meno maturo. Ad esempio, esistono limitazioni nel supporto e nel debug di [.NET Standard.For](/dotnet/standard/net-standard) example, limitations exist in .NET Standard support and debugging.

Il Blazor modello di app ospitata supporta i [contenitori Docker.](/dotnet/standard/microservices-architecture/container-docker-introduction/index) Fare clic con il pulsante destro del mouse sul progetto Server in Visual Studio e **scegliere Aggiungi** > **supporto Docker**.

## <a name="opno-locblazor-server"></a>BlazorServer

Con Blazor il modello di hosting Server, l'app viene eseguita sul server dall'interno di un'app ASP.NET Core. Gli aggiornamenti dell'interfaccia utente, la gestione [SignalR](xref:signalr/introduction) degli eventi e le chiamate JavaScript vengono gestiti tramite una connessione.

![Il browser interagisce con l'app (ospitata all'interno di SignalR un'app ASP.NET Core) sul server tramite una connessione.](hosting-models/_static/blazor-server.png)

Per creare Blazor un'app usando il Blazor modello di hosting Server, usare il modello ASP.NET Core ** Blazor Server App** ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)). L'app ASP.NET Blazor Core ospita l'app Server e crea l'endpoint SignalR a cui si connettono i client.

L'app ASP.NET Core fa `Startup` riferimento alla classe dell'app da aggiungere:The ASP.NET Core app references the app's class to add:

* Servizi sul lato server.
* App alla pipeline di gestione delle richieste.

Lo `blazor.server.js` script stabilisce la connessione client. È responsabilità dell'app mantenere e ripristinare lo stato dell'app in base alle esigenze (ad esempio, in caso di connessione di rete persa). Lo `blazor.server.js` script viene servito da una risorsa incorporata nel framework condiviso ASP.NET Core.The script is served from an embedded resource in the ASP.NET Core shared framework.

Il Blazor modello di hosting Server offre diversi vantaggi:

* Le dimensioni del download Blazor sono significativamente inferiori rispetto a un'app WebAssembly e l'app viene caricata molto più velocemente.
* L'app sfrutta appieno le funzionalità del server, incluso l'uso di qualsiasi API compatibile con .NET Core.
* .NET Core nel server viene usato per eseguire l'app, pertanto gli strumenti .NET esistenti, ad esempio il debug, funzionano come previsto.
* Sono supportati i thin client. Ad esempio, Blazor le app server funzionano con browser che non supportano WebAssembly e su dispositivi con risorse limitate.
* La base di codice .NET/Cè dell'app, incluso il codice del componente dell'app, non viene servita ai client.

Ci sono aspetti Blazor negativi per l'hosting del server:

* Una latenza più elevata di solito esiste. Ogni interazione dell'utente comporta un hop di rete.
* Non è disponibile alcun supporto offline. Se la connessione client non riesce, l'app smette di funzionare.
* La scalabilità è complessa per le app con molti utenti. Il server deve gestire più connessioni client e gestire lo stato client.
* Per gestire l'app è necessario un server ASP.NET Core. Scenari di distribuzione senza server non sono possibili (ad esempio, la gestione dell'app da una rete CDN).

Il Blazor modello di app Server supporta i [contenitori Docker.](/dotnet/standard/microservices-architecture/container-docker-introduction/index) Fare clic con il pulsante destro del mouse sul progetto in Visual Studio e selezionare **Aggiungi** > **supporto Docker**.

### <a name="comparison-to-server-rendered-ui"></a>Confronto con l'interfaccia utente sottoposta a rendering del serverComparison to server-rendered UI

Un modo Blazor per comprendere le app server consiste nel comprendere le differenze rispetto ai modelli tradizionali per il rendering dell'interfaccia utente in ASP.NET app di base usando visualizzazioni Razor o pagine Razor.One way to understand Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages. Entrambi i modelli usano il linguaggio Razor per descrivere il contenuto HTML, ma differiscono in modo significativo nel modo in cui viene eseguito il rendering del markup.

Quando viene eseguito il rendering di una pagina o di una visualizzazione Razor, ogni riga di codice Razor genera codice HTML in formato testo. Dopo il rendering, il server elimina la pagina o l'istanza di visualizzazione, incluso lo stato prodotto. Quando si verifica un'altra richiesta per la pagina, ad esempio quando la convalida del server non riesce e viene visualizzato il riepilogo di convalida:

* L'intera pagina viene nuovamente sottoposta a rendering in testo HTML.
* La pagina viene inviata al client.

Un'app Blazor è composta da elementi riutilizzabili dell'interfaccia utente denominati *componenti*. Un componente contiene il codice, il markup e altri componenti di C. Quando viene eseguito Blazor il rendering di un componente, produce un grafico dei componenti inclusi simile a un MODELLO DOM (Document Object Model) HTML o XML. Questo grafico include lo stato del componente mantenuto nelle proprietà e nei campi. Blazorvaluta il grafico del componente per produrre una rappresentazione binaria del markup. Il formato binario può essere:

* Trasformato in testo HTML&dagger;(durante il prerendering ).
* Utilizzato per aggiornare in modo efficiente il markup durante il rendering regolare.

&dagger;*Prerendering* &ndash; Il componente Razor richiesto viene compilato sul server in HTML statico e inviato al client, dove viene eseguito il rendering all'utente. Dopo aver eseguito la connessione tra il client e il server, gli elementi di prerendering statici del componente vengono sostituiti con elementi interattivi. Il prerendering rende l'app più reattiva all'utente.

Un aggiornamento Blazor dell'interfaccia utente in viene attivato da:A UI update in is triggered by:

* Interazione dell'utente, ad esempio la selezione di un pulsante.
* Trigger dell'app, ad esempio un timer.

Viene eseguito nuovamente il rendering del grafico e viene calcolato un *diff* dell'interfaccia utente (differenza). Questo diff è il più piccolo set di modifiche DOM necessarie per aggiornare l'interfaccia utente sul client. Il diff viene inviato al client in formato binario e applicato dal browser.

Un componente viene eliminato dopo che l'utente si allontana da esso sul client. Mentre un utente interagisce con un componente, lo stato del componente (servizi, risorse) deve essere mantenuto nella memoria del server. Poiché lo stato di molti componenti potrebbe essere gestito contemporaneamente dal server, l'esaurimento della memoria è un problema che deve essere affrontato. Per istruzioni su come Blazor creare un'app Server per garantire <xref:security/blazor/server>il miglior utilizzo della memoria del server, vedere .

### <a name="circuits"></a>Circuiti

Un'app Blazor Server si basa su [ASP.NET Core SignalR ](xref:signalr/introduction). Ogni client comunica con il server SignalR su una o più connessioni denominate *circuito*. Un circuito Blazorè l'astrazione sulle SignalR connessioni che può tollerare interruzioni temporanee della rete. Quando Blazor un client verifica SignalR che la connessione è disconnessa, tenta SignalR di riconnettersi al server utilizzando una nuova connessione.

Ogni schermata del browser (scheda del browser Blazor o iframe) connessa a un'app Server utilizza una SignalR connessione. Questa è un'altra importante distinzione rispetto alle tipiche app sottoposte a rendering del server. In un'app sottoposta a rendering del server, l'apertura della stessa app in più schermate del browser in genere non si traduce in richieste di risorse aggiuntive sul server. In Blazor un'app Server, ogni schermata del browser richiede un circuito separato e istanze separate dello stato del componente per essere gestite dal server.

Blazorconsidera la chiusura di una scheda del browser o la navigazione verso un URL esterno come una terminazione *normale.* In caso di terminazione normale, il circuito e le risorse associate vengono immediatamente rilasciati. Un client può anche disconnettersi in modo non normale, ad esempio a causa di un'interruzione della rete. BlazorIl server archivia i circuiti disconnessi per un intervallo configurabile per consentire al client di riconnettersi.

BlazorServer consente al codice di definire un *gestore di circuiti*, che consente l'esecuzione di codice sulle modifiche apportate allo stato del circuito di un utente. Per altre informazioni, vedere <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Latenza interfaccia utenteUI Latency

La latenza dell'interfaccia utente è il tempo necessario da un'azione avviata al momento in cui l'interfaccia utente viene aggiornata. I valori più piccoli per la latenza dell'interfaccia utente sono indispensabili per un'app che si senta reattiva a un utente. In Blazor un'app Server, ogni azione viene inviata al server, elaborata e viene inviata una differenza dell'interfaccia utente. Di conseguenza, la latenza dell'interfaccia utente è la somma della latenza di rete e della latenza del server nell'elaborazione dell'azione.

Per un'app line-of-business limitata a una rete aziendale privata, l'effetto sulla percezione della latenza da parte degli utenti dovuta alla latenza di rete è in genere impercettibile. Per un'app distribuita su Internet, la latenza può diventare evidente per gli utenti, in particolare se gli utenti sono ampiamente distribuiti geograficamente.

L'utilizzo della memoria può anche contribuire alla latenza dell'app. L'aumento dell'utilizzo della memoria comporta frequenti operazioni di Garbage Collection o paging della memoria su disco, che riducono le prestazioni dell'app e di conseguenza aumentano la latenza dell'interfaccia utente. Per altre informazioni, vedere <xref:security/blazor/server>.

BlazorLe app server devono essere ottimizzate per ridurre al minimo la latenza dell'interfaccia utente riducendo la latenza di rete e l'utilizzo della memoria. Per un approccio alla misurazione <xref:host-and-deploy/blazor/server#measure-network-latency>della latenza di rete, vedere . Per ulteriori SignalR informazioni Blazorsu e , vedere:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>Connessione al server

BlazorLe app server SignalR richiedono una connessione attiva al server. Se la connessione viene persa, l'app tenta di riconnettersi al server. Finché lo stato del client è ancora in memoria, la sessione client riprende senza perdere lo stato.

Un'app Blazor Server esegue il prerendering in risposta alla prima richiesta del client, che configura lo stato dell'interfaccia utente nel server. Quando il client tenta SignalR di creare una connessione, il client deve riconnettersi allo stesso server. BlazorLe app server che usano più di un SignalR server back-end devono implementare *sessioni permanenti* per le connessioni.

È consigliabile usare Blazor il servizio di [Azure SignalR ](/azure/azure-signalr) per le app del server. Il servizio consente di Blazor aumentare la scalabilità SignalR di un'app Server a un numero elevato di connessioni simultanee. Le sessioni permanenti vengono SignalR abilitate per il `ServerStickyMode` servizio di `Required`Azure impostando l'opzione o il valore di configurazione del servizio su . Per altre informazioni, vedere <xref:host-and-deploy/blazor/server#signalr-configuration>.

Quando si utilizza IIS, le sessioni permanenti vengono abilitate con Application Request Routing. Per ulteriori informazioni, vedere [Bilanciamento del carico HTTP mediante il routing delle richieste di applicazione](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>
