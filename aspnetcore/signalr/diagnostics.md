---
title: Registrazione e diagnostica in ASP.NET CoreSignalR
author: anurse
description: Informazioni su come raccogliere dati diagnostici dall' SignalR app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 06/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: 22e1d24bc9fed5fd8588c852e07f5ca935946596
ms.sourcegitcommit: 05490855e0c70565f0c4b509d392b0828bcfd141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507316"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>Registrazione e diagnostica in ASP.NET CoreSignalR

Di [Andrew Stanton-Nurse](https://twitter.com/anurse)

Questo articolo fornisce indicazioni per la raccolta di dati diagnostici dall' SignalR app ASP.NET Core per facilitare la risoluzione dei problemi.

## <a name="server-side-logging"></a>Registrazione lato server

> [!WARNING]
> I log lato server possono contenere informazioni riservate dall'app. **Non pubblicare mai log non** elaborati dalle app di produzione nei forum pubblici come github.

Poiché SignalR fa parte di ASP.NET Core, usa il sistema di registrazione ASP.NET Core. Nella configurazione predefinita, SignalR registra informazioni molto piccole, ma ciò può essere configurato. Per informazioni dettagliate sulla configurazione della registrazione ASP.NET Core, vedere la documentazione relativa alla [registrazione di ASP.NET Core](xref:fundamentals/logging/index#configuration) .

SignalRUSA due categorie di logger:

* `Microsoft.AspNetCore.SignalR`: Per i log relativi ai protocolli Hub, attivazione di hub, richiamo di metodi e altre attività correlate all'hub.
* `Microsoft.AspNetCore.Http.Connections`: Per i log relativi ai trasporti, ad esempio WebSocket, polling prolungato, eventi inviati dal server e infrastruttura di basso livello SignalR .

Per abilitare i log dettagliati da SignalR , configurare entrambi i prefissi precedenti al `Debug` livello del file *appSettings. JSON* aggiungendo gli elementi seguenti alla sottosezione `LogLevel` in `Logging` :

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

È anche possibile configurarlo nel codice nel `CreateWebHostBuilder` Metodo:

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

Se non si usa la configurazione basata su JSON, impostare i valori di configurazione seguenti nel sistema di configurazione:

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

Controllare la documentazione del sistema di configurazione per determinare come specificare i valori di configurazione annidati. Quando si usano le variabili di ambiente, ad esempio, `_` vengono usati due caratteri al posto di `:` (ad esempio, `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).

È consigliabile usare il `Debug` livello quando si raccolgono dati di diagnostica più dettagliati per l'app. Il `Trace` livello produce diagnostica di basso livello e raramente è necessario per diagnosticare i problemi nell'app.

## <a name="access-server-side-logs"></a>Accedere ai log lato server

La modalità di accesso ai log lato server dipende dall'ambiente in cui si esegue.

### <a name="as-a-console-app-outside-iis"></a>Come app console all'esterno di IIS

Se è in esecuzione un'app console, il [logger della console](xref:fundamentals/logging/index#console) deve essere abilitato per impostazione predefinita. SignalRi log verranno visualizzati nella console di.

### <a name="within-iis-express-from-visual-studio"></a>All'interno IIS Express da Visual Studio

Visual Studio Visualizza l'output del log nella finestra **output** . Selezionare l'opzione elenco a discesa **server Web ASP.NET Core** .

### <a name="azure-app-service"></a>Servizio app di Azure

Abilitare l'opzione **registrazione applicazioni (file System)** nella sezione **log di diagnostica** del portale del servizio app Azure e configurare il **livello** su `Verbose` . I log devono essere disponibili dal servizio di **streaming dei log** e nei log nel file System del servizio app. Per altre informazioni, vedere [streaming dei log di Azure](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Altri ambienti

Se l'app viene distribuita in un altro ambiente, ad esempio Docker, Kubernetes o il servizio Windows, vedere <xref:fundamentals/logging/index> per altre informazioni su come configurare i provider di registrazione appropriati per l'ambiente.

## <a name="javascript-client-logging"></a>Registrazione client JavaScript

> [!WARNING]
> I log lato client possono contenere informazioni riservate dall'app. **Non pubblicare mai log non** elaborati dalle app di produzione nei forum pubblici come github.

Quando si usa il client JavaScript, è possibile configurare le opzioni di registrazione usando il `configureLogging` metodo in `HubConnectionBuilder` :

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.

La tabella seguente illustra i livelli di log disponibili per il client JavaScript. Impostando il livello di registrazione su uno di questi valori, viene abilitata la registrazione a tale livello e a tutti i livelli superiori nella tabella.

| Level | Descrizione |
| ----- | ----------- |
| `None` | Nessun messaggio registrato. |
| `Critical` | Messaggi che indicano un errore nell'intera app. |
| `Error` | Messaggi che indicano un errore nell'operazione corrente. |
| `Warning` | Messaggi che indicano un problema non irreversibile. |
| `Information` | Messaggi informativi. |
| `Debug` | Messaggi di diagnostica utili per il debug. |
| `Trace` | Messaggi di diagnostica molto dettagliati progettati per la diagnosi di problemi specifici. |

Dopo aver configurato il livello di dettaglio, i log verranno scritti nella console del browser o nell'output standard in un'app NodeJS.

Se si desidera inviare i log a un sistema di registrazione personalizzato, è possibile fornire un oggetto JavaScript che implementa l' `ILogger` interfaccia. L'unico metodo che deve essere implementato è `log` , che accetta il livello dell'evento e il messaggio associato all'evento. Ad esempio:

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>Registrazione client .NET

> [!WARNING]
> I log lato client possono contenere informazioni riservate dall'app. **Non pubblicare mai log non** elaborati dalle app di produzione nei forum pubblici come github.

Per ottenere i log dal client .NET, è possibile usare il `ConfigureLogging` metodo su `HubConnectionBuilder` . Funziona allo stesso modo del `ConfigureLogging` metodo in `WebHostBuilder` e `HostBuilder` . È possibile configurare gli stessi provider di registrazione usati in ASP.NET Core. Tuttavia, è necessario installare e abilitare manualmente i pacchetti NuGet per i singoli provider di registrazione.

### <a name="console-logging"></a>Registrazione console

Per abilitare la registrazione della console, aggiungere il pacchetto [Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) . Usare quindi il `AddConsole` metodo per configurare il logger della console:

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>Debug della registrazione della finestra di output

È anche possibile configurare i log per accedere alla finestra di **output** in Visual Studio. Installare il pacchetto [Microsoft. Extensions. Logging. debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) e usare il `AddDebug` Metodo:

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Altri provider di registrazione

SignalRsupporta altri provider di registrazione, ad esempio Serilog, seq, NLog o qualsiasi altro sistema di registrazione che si integra con `Microsoft.Extensions.Logging` . Se il sistema di registrazione fornisce un `ILoggerProvider` , è possibile registrarlo con `AddProvider` :

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Controllo del livello di dettaglio

Se si esegue la registrazione da altre posizioni nell'app, è possibile che la modifica del livello predefinito a `Debug` sia troppo dettagliata. È possibile usare un filtro per configurare il livello di registrazione per i SignalR log. Questa operazione può essere eseguita nel codice, nello stesso modo in cui si trova nel server:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Tracce di rete

> [!WARNING]
> Una traccia di rete contiene il contenuto completo di tutti i messaggi inviati dall'app. **Non pubblicare mai** tracce di rete RAW da app di produzione a forum pubblici come github.

Se si verifica un problema, una traccia di rete può talvolta fornire numerose informazioni utili. Questa operazione è particolarmente utile se si sta per archiviare un problema nello strumento di rilevamento dei problemi.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Raccogliere una traccia di rete con Fiddler (opzione preferita)

Questo metodo funziona per tutte le app.

Fiddler è uno strumento molto potente per la raccolta di tracce HTTP. Installarlo da [Telerik.com/Fiddler](https://www.telerik.com/fiddler), avviarlo, quindi eseguire l'app e riprodurre il problema. Fiddler è disponibile per Windows e sono disponibili versioni beta per macOS e Linux.

Se si esegue la connessione tramite HTTPS, sono necessari alcuni passaggi aggiuntivi per assicurarsi che Fiddler possa decrittografare il traffico HTTPS. Per ulteriori informazioni, vedere la [documentazione di Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

Una volta raccolta la traccia, è possibile esportare la traccia scegliendo **file**  >  **Salva**  >  **tutte le sessioni** dalla barra dei menu.

![Esportazione di tutte le sessioni da Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Raccogliere una traccia di rete con tcpdump (solo macOS e Linux)

Questo metodo funziona per tutte le app.

È possibile raccogliere tracce TCP non elaborate tramite tcpdump eseguendo il comando seguente da una shell dei comandi. `root` `sudo` Se viene ricevuto un errore di autorizzazione, potrebbe essere necessario o precedere il comando:

```console
tcpdump -i [interface] -w trace.pcap
```

Sostituire `[interface]` con l'interfaccia di rete su cui si vuole eseguire l'acquisizione. In genere, questa operazione è simile a `/dev/eth0` (per l'interfaccia Ethernet standard) o `/dev/lo0` (per il traffico localhost). Per ulteriori informazioni, vedere la `tcpdump` pagina relativa all'utente nel sistema host.

## <a name="collect-a-network-trace-in-the-browser"></a>Raccogliere una traccia di rete nel browser

Questo metodo funziona solo per le app basate su browser.

La maggior parte del browser Strumenti di sviluppo dispone di una scheda di rete che consente di acquisire l'attività di rete tra il browser e il server. Tuttavia, queste tracce non includono messaggi di evento WebSocket e inviati dal server. Se si usano questi trasporti, un approccio migliore è quello di usare uno strumento come Fiddler o TcpDump (descritto di seguito).

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge e Internet Explorer

(Le istruzioni sono le stesse per Microsoft Edge e Internet Explorer)

1. Premere F12 per aprire gli strumenti di sviluppo
2. Fare clic sulla scheda rete
3. Aggiornare la pagina (se necessario) e riprodurre il problema
4. Fare clic sull'icona Salva sulla barra degli strumenti per esportare la traccia come file "HAR":

![Icona Salva nella scheda rete Microsoft Edge Dev Tools](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. Premere F12 per aprire gli strumenti di sviluppo
2. Fare clic sulla scheda rete
3. Aggiornare la pagina (se necessario) e riprodurre il problema
4. Fare clic con il pulsante destro del mouse in un punto qualsiasi dell'elenco di richieste e scegliere "Salva come HAR con contenuto":

![Opzione "Salva come HAR con contenuto" nella scheda rete di strumenti di sviluppo di Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Premere F12 per aprire gli strumenti di sviluppo
2. Fare clic sulla scheda rete
3. Aggiornare la pagina (se necessario) e riprodurre il problema
4. Fare clic con il pulsante destro del mouse in un punto qualsiasi dell'elenco di richieste e scegliere "Salva tutto come HAR"

![Opzione "Salva tutto come HAR" nella scheda rete di Mozilla Firefox Dev Tools](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>Alleghi i file di diagnostica ai problemi di GitHub

È possibile aggiungere i file di diagnostica ai problemi di GitHub rinominando i file in modo che abbiano un' `.txt` estensione e quindi trascinandoli e rilasciandoli al problema.

> [!NOTE]
> Non incollare il contenuto dei file di log o delle tracce di rete in un problema di GitHub. Questi log e tracce possono avere dimensioni molto elevate e GitHub li tronca in genere.

![Trascinamento dei file di log in un problema di GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a>Metriche

Metrica è una rappresentazione delle misure dei dati in intervalli di tempo. Ad esempio, richieste al secondo. I dati di metrica consentono di osservare lo stato di un'app a un livello elevato. Le metriche gRPC di .NET vengono emesse usando <xref:System.Diagnostics.Tracing.EventCounter> .

### <a name="signalr-server-metrics"></a>SignalRmetriche del server

SignalRle metriche del server sono segnalate nell' <xref:Microsoft.AspNetCore.Http.Connections> origine evento.

| Nome                    | Descrizione                 |
|-------------------------|-----------------------------|
| `connections-started`   | Totale connessioni avviate   |
| `connections-stopped`   | Totale connessioni interrotte   |
| `connections-timed-out` | Timeout connessioni totali |
| `current-connections`   | connessioni correnti         |
| `connections-duration`  | Durata media connessione |

### <a name="observe-metrics"></a>Osservare le metriche

[DotNet-Counters](/dotnet/core/diagnostics/dotnet-counters) è uno strumento di monitoraggio delle prestazioni per il monitoraggio dell'integrità ad hoc e l'analisi delle prestazioni di primo livello. Monitorare un'app .NET con `Microsoft.AspNetCore.Http.Connections` come nome del provider. Ad esempio:

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
