---
title: Registrazione in .NET Core e ASP.NET Core
author: rick-anderson
description: Informazioni su come usare il framework di registrazione fornito dal pacchetto NuGet Microsoft.Extensions.Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330749"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Registrazione in .NET Core e ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Di [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) e [Rick Anderson](https://twitter.com/RickAndMSFT)

.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti. Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.

La maggior parte degli esempi di codice illustrati in questo articolo proviene da app ASP.NET Core. Le parti specifiche per la registrazione di questi frammenti di codice si applicano a qualsiasi app .NET Core che usa l' [host generico](xref:fundamentals/host/generic-host). I modelli di app Web ASP.NET Core usano l'host generico.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([procedura per il download](xref:index#how-to-download-a-sample))

<a name="lp"></a>

## <a name="logging-providers"></a>Provider di registrazione

I provider di registrazione archiviano i log, ad eccezione del `Console` provider che Visualizza i log. Ad esempio, il provider applicazione Azure Insights archivia i log in applicazione Azure Insights. È possibile abilitare più provider.

I modelli di app Web ASP.NET Core predefiniti:

* Usare l' [host generico](xref:fundamentals/host/generic-host).
* Chiamare <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> , che aggiunge i provider di registrazione seguenti:
  * [Console](#console)
  * [Eseguire il debug](#debug)
  * [EventSource](#event-source)
  * [EventLog](#welog): solo Windows

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

Il codice precedente Mostra la `Program` classe creata con i modelli di app web ASP.NET Core. Le sezioni successive forniscono esempi basati sui modelli di app Web ASP.NET Core, che usano l'host generico. Le [app console non host](#nhca) sono descritte più avanti in questo documento.

Per eseguire l'override del set predefinito di provider di registrazione aggiunto da `Host.CreateDefaultBuilder` , chiamare `ClearProviders` e aggiungere i provider di registrazione richiesti. Ad esempio, il seguente codice:

* Chiama <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> per rimuovere tutte le <xref:Microsoft.Extensions.Logging.ILoggerProvider> istanze dal generatore.
* Aggiunge il provider di registrazione della [console](#console) .

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

Per ulteriori provider, vedere:

* [Provider di registrazione predefiniti](#bilp)
* [Provider di registrazione di terze parti](#third-party-logging-providers).

## <a name="create-logs"></a>Creare log

Per creare i log, usare un <xref:Microsoft.Extensions.Logging.ILogger%601> oggetto da [inserimento delle dipendenze](xref:fundamentals/dependency-injection) (di).

L'esempio seguente:

* Crea un logger, `ILogger<AboutModel>` , che usa una *categoria* di log del nome completo del tipo `AboutModel` . La categoria del log è una stringa associata a ogni log.
* Chiama <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> per registrare il `Information` livello. Il *livello* del log indica la gravità dell'evento registrato.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

I [livelli](#log-level) e le [categorie](#log-category) sono descritti in modo più dettagliato più avanti in questo documento.

Per informazioni su Blazor , vedere [creare log in Blazor e Blazor WebAssembly ](#clib) in questo documento.

[Creare log in Main e startup](#clms) Mostra come creare i log in `Main` e `Startup` .

## <a name="configure-logging"></a>Configurare la registrazione

La configurazione della registrazione viene comunemente fornita dalla `Logging` sezione di *appSettings*. `{Environment}` file con *estensione JSON* . I seguenti *appsettings.Development.jsnel* file vengono generati da ASP.NET Core modelli di app Web:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

Nel codice JSON precedente:

* `"Default"` `"Microsoft"` `"Microsoft.Hosting.Lifetime"` Vengono specificate le categorie, e.
* La `"Microsoft"` categoria si applica a tutte le categorie che iniziano con `"Microsoft"` . Questa impostazione, ad esempio, si applica alla `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` categoria.
* La `"Microsoft"` categoria registra a livello di log `Warning` e superiore.
* La `"Microsoft.Hosting.Lifetime"` categoria è più specifica della `"Microsoft"` categoria, quindi la `"Microsoft.Hosting.Lifetime"` categoria registra a livello di log "Information" e versioni successive.
* Un provider di log specifico non è specificato, pertanto `LogLevel` si applica a tutti i provider di registrazione abilitati, ad eccezione del [registro eventi di Windows](#welog).

La `Logging` proprietà può disporre <xref:Microsoft.Extensions.Logging.LogLevel> di proprietà del provider di log e. `LogLevel`Specifica il [livello](#log-level) minimo da registrare per le categorie selezionate. Nel codice JSON precedente `Information` e `Warning` vengono specificati i livelli di log. `LogLevel`indica la gravità del log e varia da 0 a 6:

`Trace`= 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 e `None` = 6.

Quando `LogLevel` si specifica un oggetto, la registrazione viene abilitata per i messaggi al livello specificato e superiori. Nel codice JSON precedente, la `Default` categoria viene registrata per `Information` e versioni successive. Ad esempio, `Information` `Warning` `Error` i messaggi,, e `Critical` vengono registrati. Se non `LogLevel` viene specificato alcun valore, per impostazione predefinita viene impostato il livello di registrazione `Information` . Per altre informazioni, vedere [livelli di log](#llvl).

Una proprietà del provider può specificare una `LogLevel` Proprietà. `LogLevel`in un provider specifica i livelli da registrare per quel provider e sostituisce le impostazioni del log non del provider. Si considerino i *appsettings.jsseguenti nel* file:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

Impostazioni in `Logging.{providername}.LogLevel` impostazioni di sostituzione in `Logging.LogLevel` . Nel codice JSON precedente, il `Debug` livello di log predefinito del provider è impostato su `Information` :

`Logging:Debug:LogLevel:Default:Information`

L'impostazione precedente specifica il `Information` livello di registrazione per ogni `Logging:Debug:` categoria eccetto `Microsoft.Hosting` . Quando viene elencata una categoria specifica, la categoria specifica sostituisce quella predefinita. Nel codice JSON precedente, le `Logging:Debug:LogLevel` categorie `"Microsoft.Hosting"` ed `"Default"` eseguire l'override delle impostazioni in`Logging:LogLevel`

È possibile specificare il livello di registrazione minimo per uno dei seguenti valori:

* Provider specifici: ad esempio,`Logging:EventSource:LogLevel:Default:Information`
* Categorie specifiche: ad esempio,`Logging:LogLevel:Microsoft:Warning`
* Tutti i provider e tutte le categorie:`Logging:LogLevel:Default:Warning`

I log al di sotto del livello minimo ***non***sono:

* Passato al provider.
* Registrato o visualizzato.

Per evitare l'eliminazione di tutti i log, specificare [LogLevel. None](xref:Microsoft.Extensions.Logging.LogLevel). `LogLevel.None`il valore di è 6, che è maggiore di `LogLevel.Critical` (5).

Se un provider supporta gli [ambiti del log](#logscopes), `IncludeScopes` indica se sono abilitati. Per altre informazioni, vedere [ambiti di log](#logscopes)

Il *appsettings.jsseguente nel* file contiene tutti i provider abilitati per impostazione predefinita:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

Nell'esempio precedente:

* Le categorie e i livelli non sono valori suggeriti. L'esempio viene fornito per mostrare tutti i provider predefiniti.
* Impostazioni in `Logging.{providername}.LogLevel` impostazioni di sostituzione in `Logging.LogLevel` . Ad esempio, il livello in `Debug.LogLevel.Default` sostituisce il livello in `LogLevel.Default` .
* Viene usato ogni *alias* del provider predefinito. Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo. Gli alias dei provider predefiniti sono:
  * Console
  * Debug
  * EventSource
  * EventLog
  * AzureAppServicesFile
  * AzureAppServicesBlob
  * ApplicationInsights

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a>Impostare il livello di registrazione per riga di comando, variabili di ambiente e altre configurazioni

Il livello di registrazione può essere impostato da uno qualsiasi dei [provider di configurazione](xref:fundamentals/configuration/index). 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

I comandi seguenti:

* Impostare la chiave ambiente `Logging:LogLevel:Microsoft` su un valore di `Information` in Windows.
* Testare le impostazioni quando si usa un'app creata con i modelli di applicazione Web ASP.NET Core. Il `dotnet run` comando deve essere eseguito nella directory del progetto dopo l'uso di `set` .

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

Impostazione dell'ambiente precedente:

* Viene impostato solo nei processi avviati dalla finestra di comando in cui sono stati impostati.
* Non viene letto dai browser avviati con Visual Studio.

Il comando [Setx](/windows-server/administration/windows-commands/setx) seguente imposta anche la chiave e il valore di ambiente in Windows. Diversamente da `set` , `setx` le impostazioni sono rese permanente. Il `/M` Commuter imposta la variabile nell'ambiente di sistema. Se `/M` non viene usato, viene impostata una variabile di ambiente utente.

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

In [app Azure servizio](https://azure.microsoft.com/services/app-service/)selezionare **impostazione nuova applicazione** nella pagina **Impostazioni > configurazione** . App Azure impostazioni dell'applicazione del servizio sono:

* Crittografati a riposo e trasmessi su un canale crittografato.
* Esposto come variabile di ambiente.

Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Per altre informazioni sull'impostazione di ASP.NET Core valori di configurazione usando le variabili di ambiente, vedere [variabili di ambiente](xref:fundamentals/configuration/index#environment-variables). Per informazioni sull'utilizzo di altre origini di configurazione, tra cui la riga di comando, Azure Key Vault, la configurazione app Azure, altri formati di file e altro ancora, vedere <xref:fundamentals/configuration/index> .

## <a name="how-filtering-rules-are-applied"></a>Applicazione delle regole di filtro

Quando viene creato un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>, l'oggetto <xref:Microsoft.Extensions.Logging.ILoggerFactory> seleziona una singola regola per ogni provider da applicare al logger. Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate. La regola più specifica per ogni coppia di provider e categoria viene selezionata dalle regole disponibili.

L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:

* Selezionare tutte le regole corrispondenti al provider o al relativo alias. Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.
* Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo. Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.
* Se sono selezionate più regole, scegliere l'**ultima**.
* Se non è selezionata alcuna regola, usare `MinimumLevel`.

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a>Registrazione dell'output da DotNet Run e Visual Studio

Vengono visualizzati i log creati con i [provider di registrazione predefiniti](#lp) :

* In Visual Studio
  * Nella finestra di output di debug durante il debug.
  * Nella finestra ASP.NET Core Server Web.
* Nella finestra della console quando l'app viene eseguita con `dotnet run` .

I log che iniziano con le categorie "Microsoft" appartengono a ASP.NET Core codice del Framework. ASP.NET Core e il codice dell'applicazione usano la stessa API e i provider di registrazione.

<a name="lcat"></a>

## <a name="log-category"></a>Categoria di log

Quando `ILogger` viene creato un oggetto, viene specificata una *categoria* . La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`. La stringa Category è arbitraria, ma la convenzione prevede l'uso del nome della classe. Ad esempio, in un controller il nome potrebbe essere `"TodoApi.Controllers.TodoController"` . Le app Web di ASP.NET Core usano `ILogger<T>` per ottenere automaticamente un' `ILogger` istanza che usa il nome di tipo completo di `T` come categoria:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.

<a name="llvl"></a>

## <a name="log-level"></a>Livello di registrazione

La tabella seguente elenca i <xref:Microsoft.Extensions.Logging.LogLevel> valori, il `Log{LogLevel}` metodo di estensione convenience e l'utilizzo suggerito:

| LogLevel | valore | Metodo | Descrizione |
| -------- | ----- | ------ | ----------- |
| [Trace](xref:Microsoft.Extensions.Logging.LogLevel) | 0 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | Contiene i messaggi più dettagliati. Questi messaggi possono contenere dati sensibili dell'app. Questi messaggi sono disabilitati per impostazione predefinita e ***non*** devono essere abilitati nell'ambiente di produzione. |
| [Eseguire il debug](xref:Microsoft.Extensions.Logging.LogLevel) | 1 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | Per il debug e lo sviluppo. Usare con cautela in produzione a causa del volume elevato. |
| [Informazioni](xref:Microsoft.Extensions.Logging.LogLevel) | 2 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | Tiene traccia del flusso generale dell'app. Potrebbe avere un valore a lungo termine. |
| [Warning](xref:Microsoft.Extensions.Logging.LogLevel) | 3 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | Per eventi anomali o imprevisti. Include in genere errori o condizioni che non provocano la mancata riuscita dell'app. |
| [Error (Errore) (Error (Errore)e)](xref:Microsoft.Extensions.Logging.LogLevel) | 4 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | Per errori ed eccezioni che non possono essere gestiti. Questi messaggi indicano un errore nell'operazione o nella richiesta corrente, non in un errore a livello di app. |
| [Critico](xref:Microsoft.Extensions.Logging.LogLevel) | 5 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | Per gli errori che richiedono attenzione immediata. Esempi: scenari di perdita di dati, spazio su disco insufficiente. |
| [Nessuno](xref:Microsoft.Extensions.Logging.LogLevel) | 6 | | Specifica che una categoria di registrazione non deve scrivere alcun messaggio. |

Nella tabella precedente, `LogLevel` è elencato dal livello di gravità più basso a quello più alto.

Il primo parametro del metodo [log](xref:Microsoft.Extensions.Logging.LoggerExtensions) , <xref:Microsoft.Extensions.Logging.LogLevel> , indica la gravità del log. Anziché chiamare `Log(LogLevel, ...)` , la maggior parte degli sviluppatori chiama i metodi di estensione [log {LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) . I `Log{LogLevel}` metodi di estensione [chiamano il metodo log e specificano LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs). Ad esempio, le due chiamate di registrazione seguenti sono equivalenti dal punto di vista funzionale e producono lo stesso log:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

`MyLogEvents.TestItem`ID dell'evento. `MyLogEvents`fa parte dell'app di esempio e viene visualizzato nella sezione [ID evento log](#leid) .

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

Il codice seguente crea i log `Information` e `Warning`:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Nel codice precedente, il primo `Log{LogLevel}` parametro, `MyLogEvents.GetItem` , è l' [ID evento del log](#leid). Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti. I parametri del metodo sono descritti nella sezione [modello di messaggio](#lmt) più avanti in questo documento.

Chiamare il `Log{LogLevel}` metodo appropriato per controllare la quantità di output di log scritta in un supporto di archiviazione specifico. Ad esempio:

* In produzione:
  * La registrazione a `Trace` `Information` livello di o genera un volume elevato di messaggi di log dettagliati. Per controllare i costi e non superare i limiti di archiviazione dei dati, `Trace` `Information` i messaggi di log e di livello in un archivio dati con volumi elevati e a basso costo. Prendere in considerazione la limitazione `Trace` e le `Information` categorie specifiche.
  * La registrazione a livello di file `Warning` `Critical` deve produrre pochi messaggi di log.
    * I costi e i limiti di archiviazione in genere non rappresentano un problema.
    * Pochi log consentono una maggiore flessibilità nelle scelte di archivio dati.
* In fase di sviluppo:
  * Impostare su `Warning`.
  * Aggiungere `Trace` o `Information` messaggi durante la risoluzione dei problemi. Per limitare l'output, impostare `Trace` o `Information` solo per le categorie sottoposte a ricerca.

ASP.NET Core scrive log per gli eventi del framework. Si consideri, ad esempio, l'output del log per:

* RazorApp di pagine creata con i modelli ASP.NET Core.
* Registrazione impostata su`Logging:Console:LogLevel:Microsoft:Information`
* Navigazione alla pagina privacy:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

I set JSON seguenti `Logging:Console:LogLevel:Microsoft:Information` :

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a>ID evento di registrazione

Ogni log può specificare un *ID evento*. L'app di esempio usa la `MyLogEvents` classe per definire gli ID evento:

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Un ID evento associa un set di eventi. Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.

Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo. Il provider Debug non visualizza gli ID evento. Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

Alcuni provider di registrazione archiviano l'ID evento in un campo, che consente di filtrare l'ID.

<a name="lmt"></a>

## <a name="log-message-template"></a>Modello di messaggio di registrazione
<!-- Review, Each log API uses a message template. -->
Ogni API di log usa un modello di messaggio. Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti. Usare nomi per i segnaposto, non numeri.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti. Nel codice seguente i nomi dei parametri sono fuori sequenza nel modello di messaggio:

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Il codice precedente crea un messaggio di log con i valori dei parametri in sequenza:

```text
Parameter values: param1, param2
```

Questo approccio consente ai provider di registrazione di implementare la [registrazione semantica o strutturata](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging). Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato. Questo consente ai provider di registrazione di archiviare i valori dei parametri come campi. Si consideri, ad esempio, il seguente metodo logger:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Ad esempio, durante la registrazione nell'archiviazione tabelle di Azure:

* Ogni entità tabella di Azure può `ID` avere `RequestTime` proprietà e.
* Le tabelle con proprietà semplificano le query sui dati registrati. Una query, ad esempio, può trovare tutti i log all'interno di un `RequestTime` intervallo specifico senza dover analizzare il timeout del messaggio di testo.

## <a name="log-exceptions"></a>Eccezioni di log

I metodi logger hanno overload che accettano un parametro Exception:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

La registrazione delle eccezioni è specifica del provider.

### <a name="default-log-level"></a>Livello di registrazione predefinito

Se il livello di registrazione predefinito non è impostato, il valore predefinito del livello di registrazione è `Information` .

Si consideri ad esempio la seguente app Web:

* Creato con i modelli di app Web ASP.NET.
* *appsettings.jsin* e *appsettings.Development.jsin* eliminato o rinominato.

Con la configurazione precedente, la navigazione alla privacy o home page produce molti `Trace` messaggi, `Debug` e `Information` con `Microsoft` nel nome della categoria.

Il codice seguente imposta il livello di registrazione predefinito quando il livello di registrazione predefinito non è impostato nella configurazione:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
In genere, i livelli di log devono essere specificati nella configurazione e non nel codice.

### <a name="filter-function"></a>Filter - funzione

Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

Il codice precedente Visualizza i log della console quando la categoria contiene `Controller` o `Microsoft` e il livello di registrazione è `Information` o superiore.

In genere, i livelli di log devono essere specificati nella configurazione e non nel codice.

## <a name="aspnet-core-and-ef-core-categories"></a>Categorie ASP.NET Core e EF Core

La tabella seguente contiene alcune categorie usate da ASP.NET Core e Entity Framework Core, con note sui log:

| Category                            | Note |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnostica generale di ASP.NET Core. |
| Microsoft.AspNetCore.DataProtection | Chiavi considerate, trovate e usate. |
| Microsoft.AspNetCore.HostFiltering  | Host consentiti. |
| Microsoft.AspNetCore.Hosting        | Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste. Assembly di avvio dell'hosting caricati. |
| Microsoft.AspNetCore.Mvc            | MVC e Razor diagnostica. Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni. |
| Microsoft.AspNetCore.Routing        | Informazioni sulla corrispondenza di route. |
| Microsoft.AspNetCore.Server         | Risposte di avvio, arresto e keep-alive per la connessione. Informazioni sui certificati HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | File forniti. |
| Microsoft.EntityFrameworkCore       | Diagnostica generale di Entity Framework Core. Attività e configurazione del database, rilevamento delle modifiche, migrazioni. |

Per visualizzare altre categorie nella finestra della console, impostare **appsettings.Development.jssu** come riportato di seguito:

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a>Ambiti dei log

 Un *ambito* può raggruppare un set di operazioni logiche. Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set. Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.

Un ambito:

* È un <xref:System.IDisposable> tipo restituito dal <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodo.
* Dura fino a quando non viene eliminato.

I provider seguenti supportano gli ambiti:

* `Console`
* [AzureAppServicesFile e AzureAppServicesBlob](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

Il codice JSON seguente abilita gli ambiti per il provider della console:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

Il codice seguente abilita gli ambiti per il provider Console:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

In genere, la registrazione deve essere specificata nella configurazione e non nel codice.

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a>Provider di registrazione predefiniti

ASP.NET Core include i provider di registrazione seguenti:

* [Console](#console)
* [Eseguire il debug](#debug)
* [EventSource](#event-source)
* [EventLog](#welog)
* [AzureAppServicesFile e AzureAppServicesBlob](#azure-app-service)
* [ApplicationInsights](#azure-application-insights)

Per informazioni `stdout` e per eseguire il debug della registrazione con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .

### <a name="console"></a>Console

Il `Console` provider registra l'output nella console. Per altre informazioni sulla visualizzazione dei `Console` log in fase di sviluppo, vedere [registrazione dell'output da DotNet Run e Visual Studio](#dnrvs).

### <a name="debug"></a>Debug

Il `Debug` provider scrive l'output del log usando la classe [System. Diagnostics. debug](/dotnet/api/system.diagnostics.debug) . Chiama per `System.Diagnostics.Debug.WriteLine` scrivere nel `Debug` provider.

In Linux il `Debug` percorso del log del provider è dipendente dalla distribuzione e può essere uno dei seguenti:

* */var/log/message*
* */var/log/syslog*

### <a name="event-source"></a>Origine evento

Il `EventSource` provider scrive in un'origine evento multipiattaforma con il nome `Microsoft-Extensions-Logging` . In Windows, il provider utilizza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

#### <a name="dotnet-trace-tooling"></a>strumenti di traccia DotNet

Lo strumento [DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) è uno strumento globale dell'interfaccia della riga di comando multipiattaforma che consente la raccolta di tracce .NET Core di un processo in esecuzione. Lo strumento raccoglie i <xref:Microsoft.Extensions.Logging.EventSource> dati del provider utilizzando un <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> .

Per istruzioni sull'installazione, vedere [DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) .

Usare gli strumenti di traccia DotNet per raccogliere una traccia da un'app:

1. Eseguire l'app con il `dotnet run` comando.
1. Determinare l'identificatore di processo (PID) dell'app .NET Core:
   * In Windows usare uno degli approcci seguenti:
     * Gestione attività (CTRL + ALT + CANC)
     * [comando tasklist](/windows-server/administration/windows-commands/tasklist)
     * [Comando PowerShell Get-Process](/powershell/module/microsoft.powershell.management/get-process)
   * In Linux usare il [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).

   Trovare il PID per il processo con lo stesso nome dell'assembly dell'app.

1. Eseguire il `dotnet trace` comando.

   Sintassi generale del comando:

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   Quando si usa una shell dei comandi di PowerShell, racchiudere il `--providers` valore tra virgolette singole ( `'` ):

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Nelle piattaforme non Windows aggiungere l' `-f speedscope` opzione per modificare il formato del file di traccia di output in `speedscope` .

   | Parola chiave | Descrizione |
   | :-----: | ----------- |
   | 1       | Registra i metadati relativi a `LoggingEventSource` . Non registra gli eventi da `ILogger` ). |
   | 2       | Attiva l' `Message` evento quando `ILogger.Log()` viene chiamato il metodo. Fornisce informazioni in un metodo programmatico (non formattato). |
   | 4       | Attiva l' `FormatMessage` evento quando `ILogger.Log()` viene chiamato il metodo. Fornisce la versione di stringa formattata delle informazioni. |
   | 8       | Attiva l' `MessageJson` evento quando `ILogger.Log()` viene chiamato il metodo. Fornisce una rappresentazione JSON degli argomenti. |

   | Livello dell'evento | Description     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs`le voci per `{Logger Category}` e `{Event Level}` rappresentano altre condizioni di filtro del log. Separare `FilterSpecs` le voci con un punto e virgola ( `;` ).

   Esempio di utilizzo di una shell dei comandi di Windows (**senza** virgolette singole intorno al `--providers` valore):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   Il comando precedente attiva:

   * Il logger dell'origine evento per produrre stringhe formattate ( `4` ) per gli errori ( `2` ).
   * `Microsoft.AspNetCore.Hosting`registrazione a `Informational` livello di registrazione ( `4` ).

1. Arrestare gli strumenti di traccia DotNet premendo il tasto invio o CTRL + C.

   La traccia viene salvata con il nome *Trace. NetTrace* nella cartella in cui `dotnet trace` viene eseguito il comando.

1. Aprire la traccia con [PerfView](#perfview). Aprire il file *Trace. NetTrace* ed esplorare gli eventi di traccia.

Se l'app non compila l'host con `CreateDefaultBuilder` , aggiungere il [provider di origine eventi](#event-source-provider) alla configurazione di registrazione dell'app.

Per altre informazioni, vedere:

* [Trace for Performance Analysis Utility (DotNet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (documentazione di .NET Core)
* [Trace for Performance Analysis Utility (DotNet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentazione del repository GitHub DotNet/Diagnostics)
* [Classe LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (browser API .NET)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [Origine riferimento LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): per ottenere l'origine riferimento per una versione diversa, modificare il ramo in `release/{Version}` , dove `{Version}` è la versione di ASP.NET Core desiderata.
* [PerfView](#perfview): utile per la visualizzazione delle tracce dell'origine evento.

#### <a name="perfview"></a>PerfView

Usare l' [utilità PerfView](https://github.com/Microsoft/perfview) per raccogliere e visualizzare i log. Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.

Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi** Non perdere all' `*` inizio della stringa.

<a name="welog"></a>

### <a name="windows-eventlog"></a>Registro eventi di Windows

Il `EventLog` provider invia l'output del log al registro eventi di Windows. Diversamente dagli altri provider, il `EventLog` provider ***non*** eredita le impostazioni predefinite non del provider. Se `EventLog` non si specificano le impostazioni del log, il [valore predefinito è LogLevel. Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).

Per registrare eventi inferiori a <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> , impostare in modo esplicito il livello di registrazione. Nell'esempio seguente viene impostato il livello di registrazione predefinito del registro eventi su <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

Gli [Overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) possono passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> . Se `null` o non è specificato, vengono usate le impostazioni predefinite seguenti:

* `LogName`: "Applicazione"
* `SourceName`: "Runtime .NET"
* `MachineName`: Viene usato il nome del computer locale.

Il codice seguente modifica il `SourceName` valore di dal valore predefinito di `".NET Runtime"` a `MyLogs` :

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a>Servizio app di Azure

Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.

Il pacchetto del provider non è incluso nel framework condiviso. Per usare il provider, aggiungere il relativo pacchetto al progetto.

Per configurare le impostazioni del provider, usare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, come illustrato nell'esempio seguente:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

Quando viene distribuito nel servizio app Azure, l'app usa le impostazioni nella sezione [log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) della pagina del **servizio app** del portale di Azure. Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.

* **Registrazione applicazioni (file system)**
* **Registrazione applicazione (BLOB)**

Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*. Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2. Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.

Questo provider viene registrato solo quando il progetto viene eseguito nell'ambiente Azure.

#### <a name="azure-log-streaming"></a>Flusso di registrazione di Azure

Azure log streaming supporta la visualizzazione delle attività di log in tempo reale da:

* Server applicazioni
* Server Web
* Traccia delle richieste non riuscite

Per configurare il flusso di registrazione di Azure:

* Passare alla pagina **log del servizio app** dalla pagina del portale dell'app.
* Impostare **Registrazione applicazioni (file system)** su **Attiva**.
* Scegliere il livello di registrazione in **Livello**. Questa impostazione è valida solo per lo streaming di log di Azure.

Passare alla pagina del **flusso di log** per visualizzare i log. I messaggi registrati vengono registrati con l' `ILogger` interfaccia.

### <a name="azure-application-insights"></a>Azure Application Insights

Il pacchetto del provider [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive i log per [applicazione Azure informazioni dettagliate](/azure/azure-monitor/app/cloudservices). Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi. Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.

Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core. Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.

Il pacchetto [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) è per ASP.NET 4. x, non ASP.NET Core.

Per altre informazioni, vedere le risorse seguenti:

* [Panoramica di Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.
* [ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.
* [Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.

## <a name="third-party-logging-providers"></a>Provider di registrazione di terze parti

Framework di registrazione di terze parti che usano ASP.NET Core:

* [elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4net](https://logging.apache.org/log4net/) ([repository GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([repository GitHub](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))
* [Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))

Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:

1. Aggiungere un pacchetto NuGet al progetto.
1. Chiamare un `ILoggerFactory` metodo di estensione fornito dal framework di registrazione.

Per altre informazioni, vedere la documentazione di ogni provider. I provider di registrazione di terze parti non sono supportati da Microsoft.

<a name="nhca"></a>

## <a name="non-host-console-app"></a>App console non host

Per un esempio di come usare l'host generico in un'app console non Web, vedere il file *Program.cs* dell' [app di esempio attività in background](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( <xref:fundamentals/host/hosted-services> ).

Il codice di registrazione per le app senza host generico differisce per il modo in cui vengono [aggiunti i provider](#add-providers) e [creati i logger](#create-logs). 

### <a name="logging-providers"></a>Provider di registrazione

In un'app console non host chiamare il metodo di estensione `Add{provider name}` del provider durante la creazione di un oggetto `LoggerFactory`:

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a>Creare log

Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>. Utilizzare `LoggerFactory` per creare un oggetto `ILogger` .

Nell'esempio seguente viene creato un logger con `LoggingConsoleApp.Program` come categoria.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

Negli esempi di ASP.NET CORE seguenti, il logger viene usato per creare log con `Information` come livello. Il *livello* del log indica la gravità dell'evento registrato.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

I [livelli](#log-level) e le [categorie](#log-category) sono descritti in modo più dettagliato in questo documento.

<a name="lhc"></a>

## <a name="log-during-host-construction"></a>Registra durante la costruzione dell'host

La registrazione durante la costruzione dell'host non è supportata direttamente. Tuttavia, è possibile usare un logger separato. Nell'esempio seguente viene usato un logger [Serilog](https://serilog.net/) per eseguire l'accesso `CreateHostBuilder` . `AddSerilog`Usa la configurazione statica specificata in `Log.Logger` :

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a>Configurare un servizio che dipende da ILogger

L'inserimento di un logger nel costruttore `Startup` è possibile nelle versioni precedenti di ASP.NET Core perché viene creato un contenitore di inserimento delle dipendenze separato per l'host Web. Per informazioni sul motivo per cui viene creato un solo contenitore per l'host generico, vedere l'[annuncio relativo alle modifiche di rilievo](https://github.com/aspnet/Announcements/issues/353).

Per configurare un servizio da cui dipende `ILogger<T>` , utilizzare l'inserimento del costruttore o fornire un metodo factory. L'approccio con il metodo factory è consigliato solo se non sono disponibili altre opzioni. Si consideri, ad esempio, un servizio che necessita `ILogger<T>` di un'istanza fornita da di:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

Il codice evidenziato precedente è una [funzione che viene](/dotnet/api/system.func-2) eseguita la prima volta che il contenitore di inserimento di dipendenze deve costruire un'istanza di `MyService` . È possibile accedere a uno dei servizi registrati in questo modo.

<a name="clms"></a>

## <a name="create-logs-in-main"></a>Creare log in Main

Il codice seguente esegue l'accesso `Main` ottenendo un' `ILogger` istanza di dopo la compilazione dell'host:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a>Creare log nella classe Startup

Il codice seguente scrive i log in `Startup.Configure` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

La scrittura di log prima del completamento della configurazione del contenitore di inserimento delle dipendenze nel metodo `Startup.ConfigureServices` non è supportata:

* L'inserimento del logger nel costruttore `Startup` non è supportato.
* L'inserimento del logger nella firma del metodo `Startup.ConfigureServices` non è supportato

Questa restrizione è dovuta al fatto che la registrazione dipende dall'inserimento delle dipendenze e dalla configurazione, che a sua volta dipende dall'inserimento delle dipendenze. Il contenitore di inserimento delle dipendenze non viene configurato finché il metodo `ConfigureServices` non è completato.

Per informazioni sulla configurazione di un servizio che dipende da `ILogger<T>` o perché un inserimento del costruttore di un logger in è stato eseguito `Startup` nelle versioni precedenti, vedere [configurare un servizio che dipende da ILogger](#csdi)

### <a name="no-asynchronous-logger-methods"></a>Evitare l'uso di metodi logger asincroni

La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono. Se un archivio dati di registrazione è lento, non scriverlo direttamente. Si consiglia di scrivere i messaggi di log in un archivio veloce inizialmente, quindi di spostarli nell'archivio lento in un secondo momento. Ad esempio, quando si esegue la registrazione in SQL Server, non farlo direttamente in un `Log` metodo, perché i `Log` metodi sono sincroni. Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server. Per altre informazioni, vedere [questo](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema di GitHub.

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a>Modificare i livelli di registrazione in un'app in esecuzione

L'API di registrazione non include uno scenario per modificare i livelli di registrazione mentre un'app è in esecuzione. Alcuni provider di configurazione, tuttavia, sono in grado di ricaricare la configurazione, operazione che ha effetto immediato sulla configurazione della registrazione. Per impostazione predefinita, ad esempio, il [provider di configurazione file](xref:fundamentals/configuration/index#file-configuration-provider)ricarica la configurazione di registrazione. Se la configurazione viene modificata nel codice mentre un'app è in esecuzione, l'app può chiamare [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) per aggiornare la configurazione di registrazione dell'app.

## <a name="ilogger-and-iloggerfactory"></a>ILogger e ILoggerFactory

Le <xref:Microsoft.Extensions.Logging.ILogger%601> <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfacce e e le implementazioni di sono incluse nel .NET Core SDK. Sono disponibili anche nei pacchetti NuGet seguenti:  

* Le interfacce si trovano in [Microsoft. Extensions. Logging. abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).
* Le implementazioni predefinite si trovano in [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a>Applicare le regole di filtro del log nel codice

L'approccio preferito per impostare le regole di filtro del log consiste nell'utilizzare la [configurazione](xref:fundamentals/configuration/index).

L'esempio seguente illustra come registrare le regole di filtro nel codice:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

`logging.AddFilter("System", LogLevel.Debug)`Specifica la `System` categoria e il livello di registrazione `Debug` . Il filtro viene applicato a tutti i provider perché non è stato configurato un provider specifico.

`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`Specifica

* `Debug`Provider di registrazione.
* Livello `Information` di registrazione e superiore.
* Tutte le categorie che iniziano con `"Microsoft"` .

## <a name="create-a-custom-logger"></a>Creare un logger personalizzato

Per aggiungere un logger personalizzato, aggiungere un oggetto <xref:Microsoft.Extensions.Logging.ILoggerProvider> con <xref:Microsoft.Extensions.Logging.ILoggerFactory> :

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

`ILoggerProvider`Crea una o più `ILogger` istanze di. Le `ILogger` istanze vengono utilizzate dal Framework per registrare le informazioni.

### <a name="sample-custom-logger-configuration"></a>Configurazione del logger personalizzato di esempio

L'esempio:

* È progettato per essere un esempio molto semplice che imposta il colore della console di log in base all'ID evento e al livello di registrazione. In genere i logger non cambiano in base all'ID evento e non sono specifici del livello di registrazione.
* Crea diverse voci della console colori per ogni livello di log e ID evento usando il tipo di configurazione seguente:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

Il codice precedente imposta il livello predefinito su `Warning` e il colore su `Yellow` . Se `EventId` è impostato su 0, tutti gli eventi vengono registrati.

### <a name="create-the-custom-logger"></a>Creare il logger personalizzato

Il `ILogger` nome della categoria di implementazione è in genere l'origine di registrazione. Ad esempio, il tipo in cui viene creato il logger:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

Il codice precedente:

* Crea un'istanza del logger per ogni nome di categoria.
* Archivia `logLevel == _config.LogLevel` `IsEnabled` , quindi ognuno `logLevel` ha un logger univoco. In genere, i logger devono essere abilitati anche per tutti i livelli di log più elevati:

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a>Creare il LoggerProvider personalizzato

`LoggerProvider`È la classe che crea le istanze del logger. Potrebbe non essere necessario creare un'istanza del logger per ogni categoria, ma questa operazione è utile per alcuni logger, ad esempio NLog o log4net. In questo modo è anche possibile scegliere diverse destinazioni di output di registrazione per categoria, se necessario:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

Nel codice precedente <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> Crea una singola istanza del `ColorConsoleLogger` nome per categoria e la archivia in [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) .

### <a name="usage-and-registration-of-the-custom-logger"></a>Utilizzo e registrazione del logger personalizzato

Registrare il logger in `Startup.Configure` :

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

Per il codice precedente, fornire almeno un metodo di estensione per `ILoggerFactory` :

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/logging/loggermessage>
* I bug di registrazione devono essere creati nel repository [github.com/dotnet/Runtime/](https://github.com/dotnet/runtime/issues) .
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Di [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)

.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti. Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Aggiungere provider

Un provider di registrazione visualizza o archivia i log. Il provider Console, ad esempio, visualizza i log nella console, mentre il provider Azure Application Insights li archivia in Azure Application Insights. I log possono essere inviati a più destinazioni aggiungendo più provider.

Per aggiungere un provider, chiamare il metodo di estensione `Add{provider name}` del provider in *Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Il codice precedente richiede riferimenti a `Microsoft.Extensions.Logging` e `Microsoft.Extensions.Configuration`.

Il modello di progetto predefinito chiama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, che aggiunge i provider di registrazione seguenti:

* Console
* Debug
* EventSource (a partire da ASP.NET Core 2.2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Se si usa `CreateDefaultBuilder`, è possibile sostituire i provider predefiniti con quelli di propria scelta. Chiamare <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e aggiungere i provider desiderati.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

Altre informazioni sui [provider di registrazione predefiniti](#built-in-logging-providers) e sui [provider di registrazione di terze parti](#third-party-logging-providers) vengono fornite più avanti in questo articolo.

## <a name="create-logs"></a>Creare log

Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>. In un'app Web o in un servizio ospitato, ottenere un oggetto `ILogger` da un inserimento delle dipendenze. Nelle app console non host, usare `LoggerFactory` per creare un oggetto `ILogger`.

L'esempio seguente di ASP.NET Core crea un logger con `TodoApiSample.Pages.AboutModel` come categoria. La *categoria* del log è una stringa associata a ogni log. L'istanza di `ILogger<T>` fornita dall'inserimento delle dipendenze crea log con nome completo di tipo `T` come categoria. 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Negli esempi seguenti di ASP.NET Core e app console, il logger viene usato per creare log con `Information` come livello. Il *livello* del log indica la gravità dell'evento registrato.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[Livelli](#log-level) e [categorie](#log-category) vengono illustrati in modo dettagliato più avanti in questo articolo.

### <a name="create-logs-in-startup"></a>Creare log nella classe Startup

Per scrivere log nella classe `Startup`, includere un parametro `ILogger` nella firma del costruttore:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Creare log nella classe Program

Per scrivere log nella classe `Program`, ottenere un'istanza di `ILogger` dall'inserimento delle dipendenze:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

La registrazione durante la costruzione dell'host non è supportata direttamente. Tuttavia, è possibile usare un logger separato. Nell'esempio seguente viene usato un logger [Serilog](https://serilog.net/) per eseguire l'accesso `CreateWebHostBuilder` . `AddSerilog`Usa la configurazione statica specificata in `Log.Logger` :

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a>Evitare l'uso di metodi logger asincroni

La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono. Se l'archivio dati di registrazione è lento, non scrivere direttamente al suo interno. Scrivere invece i messaggi di log prima in un archivio veloce e quindi spostarli nell'archivio lento in un secondo momento. Ad esempio, se la registrazione viene eseguita in SQL Server, è preferibile non farlo direttamente in un metodo `Log`, poiché i metodi `Log` sono sincroni. Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server. Per altre informazioni, vedere [questo](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema di GitHub.

## <a name="configuration"></a>Configurazione

La configurazione dei provider di registrazione viene fornita da uno o più provider di configurazione:

* Formati di file (INI, JSON e XML).
* Argomenti della riga di comando.
* Variabili di ambiente.
* Oggetti .NET in memoria.
* Archiviazione con [Secret Manager](xref:security/app-secrets) senza crittografia.
* Un archivio utente non crittografato, come [Azure Key Vault](xref:security/key-vault-configuration).
* Provider personalizzati (installati o creati).

Ad esempio, la configurazione di registrazione viene comunemente fornita dalla sezione `Logging` del file di impostazioni dell'app. L'esempio seguente mostra il contenuto di un file *appsettings.Development.json* tipico:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

La proprietà `Logging` può avere le proprietà `LogLevel` e quella del provider di log (nell'esempio, Console).

La proprietà `LogLevel` in `Logging` specifica il [livello](#log-level) minimo per la registrazione per determinate categorie. Nell'esempio, le categorie `System` e `Microsoft` eseguono la registrazione al livello `Information`, mentre tutte le altre la eseguono al livello `Debug`.

Altre proprietà in `Logging` specificano i provider di registrazione. L'esempio si riferisce al provider Console. Se un provider supporta gli [ambiti del log](#log-scopes), `IncludeScopes` indica se sono abilitati. Una proprietà del provider (ad esempio `Console` nell'esempio) può specificare anche una proprietà `LogLevel`. `LogLevel` in un provider specifica i livelli di registrazione per tale provider.

Se i livelli sono specificati in `Logging.{providername}.LogLevel`, eseguono l'override di eventuali valori impostati in `Logging.LogLevel`. Si consideri, ad esempio, il codice JSON seguente:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

Nel codice JSON precedente, le `Console` impostazioni del provider eseguono l'override del livello di registrazione precedente (impostazione predefinita).

L'API di registrazione non include uno scenario per modificare i livelli di registrazione mentre un'app è in esecuzione. Alcuni provider di configurazione, tuttavia, sono in grado di ricaricare la configurazione, operazione che ha effetto immediato sulla configurazione della registrazione. Ad esempio, il [provider di configurazione file](xref:fundamentals/configuration/index#file-configuration-provider), aggiunto da `CreateDefaultBuilder` per leggere i file di impostazioni, ricarica la configurazione di registrazione per impostazione predefinita. Se la configurazione viene modificata nel codice mentre un'app è in esecuzione, l'app può chiamare [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) per aggiornare la configurazione di registrazione dell'app.

Per informazioni sull'implementazione dei provider di configurazione, vedere <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Esempio di output di registrazione

Con il codice di esempio illustrato nella sezione precedente i log vengono visualizzati nella console quando l'app viene eseguita dalla riga di comando. Ecco un esempio di output della console:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

I log precedenti sono stati generati inviando una richiesta HTTP Get all'app di esempio all'indirizzo `http://localhost:5000/api/todo/0`.

Ecco un esempio degli stessi log visualizzati nella finestra Debug quando si esegue l'app di esempio in Visual Studio:

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

I log creati dalle chiamate a `ILogger` illustrate nella sezione precedente iniziano con "TodoApi". I log che iniziano con le categorie "Microsoft" provengono dal codice del framework ASP.NET Core. ASP.NET Core e il codice dell'applicazione usano la stessa API di registrazione e gli stessi provider.

Il resto di questo articolo spiega alcuni dettagli e le opzioni per la registrazione.

## <a name="nuget-packages"></a>Pacchetti NuGet

Le interfacce `ILogger` e `ILoggerFactory` si trovano in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e le loro implementazioni predefinite si trovano in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Categoria di log

Quando viene creato un oggetto `ILogger`, viene specificata la relativa *categoria*. La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`. La categoria può essere qualsiasi stringa, ma per convenzione si usa il nome della classe, ad esempio "TodoApi.Controllers.TodoController".

Usare `ILogger<T>` per ottenere un'istanza di `ILogger` che usa il nome completo di tipo `T` come categoria:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.

## <a name="log-level"></a>Livello di registrazione

Ogni log specifica un valore <xref:Microsoft.Extensions.Logging.LogLevel>. Il livello di registrazione indica la gravità o l'importanza. È ad esempio possibile scrivere un log `Information` quando un metodo termina normalmente e un log `Warning` quando un metodo restituisce un codice di stato *404 Non trovato*.

Il codice seguente crea i log `Information` e `Warning`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Nel codice precedente, i `MyLogEvents.GetItem` parametri e `MyLogEvents.GetItemNotFound` sono l' [ID evento del log](#log-event-id). Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti. I parametri del metodo sono descritti nella [sezione log message template](#lmt) in questo articolo.

I metodi di registrazione che includono il livello nel nome del metodo (ad esempio `LogInformation` e `LogWarning`) sono [metodi di estensione per ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Questi metodi chiamano un metodo `Log` che accetta un parametro `LogLevel`. È possibile chiamare il metodo `Log` direttamente anziché chiamare uno di questi metodi di estensione, ma la sintassi è relativamente complessa. Per altre informazioni, vedere <xref:Microsoft.Extensions.Logging.ILogger> e il [codice sorgente delle estensioni del logger](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core definisce i livelli di registrazione seguenti, ordinati dal meno grave al più grave.

* Trace = 0

  Per informazioni in genere utili solo per il debug. Questi messaggi possono contenere dati sensibili dell'applicazione e pertanto non devono essere abilitati in un ambiente di produzione. *Disabilitato per impostazione predefinita.*

* Debug = 1

  Per informazioni che possono essere utili per lo sviluppo e il debug. Esempio: `Entering method Configure with flag set to true.` Abilitare i livelli di registrazione `Debug` nell'ambiente di produzione solo in fase di risoluzione dei problemi, per non fare aumentare eccessivamente il volume dei log.

* Information = 2

  Per tenere traccia del flusso generale dell'app. Queste registrazioni hanno in genere un valore a lungo termine. Esempio: `Request received for path /api/todo`

* Warning = 3

  Per gli eventi imprevisti o anomali nel flusso dell'app. Tali eventi potrebbero includere errori o altre condizioni che non provocano l'arresto dell'app, ma che potrebbe essere necessario analizzare. Le eccezioni gestite sono una situazione comune in cui usare il livello di registrazione `Warning`. Esempio: `FileNotFoundException for file quotes.txt.`

* Error = 4

  Per errori ed eccezioni che non possono essere gestiti. Questi messaggi indicano un errore nell'operazione o nell'attività in corso (ad esempio la richiesta HTTP corrente), non un errore a livello di app. Messaggio di registrazione di esempio:`Cannot insert record due to duplicate key violation.`

* Critical = 5

  Per gli errori che richiedono attenzione immediata. Esempi: scenari di perdita di dati, spazio su disco insufficiente.

Usare il livello di registrazione per controllare la quantità di output di log scritto in un supporto di archiviazione specifico o in una finestra. Ad esempio:

* In produzione:
  * La registrazione ai `Trace` `Information` livelli attraverso produce un volume elevato di messaggi di log dettagliati. Per controllare i costi e non superare i limiti di archiviazione dei dati, accedere `Trace` `Information` ai messaggi di livello elevato a un archivio dati a basso costo.
  * L'accesso `Warning` ai `Critical` livelli in genere produce un minor numero di messaggi di log più piccoli. Pertanto, i costi e i limiti di archiviazione in genere non rappresentano un problema, il che comporta una maggiore flessibilità di scelta dell'archivio dati.
* Durante lo sviluppo:
  * `Warning` `Critical` Consente di accedere ai messaggi nella console.
  * Aggiungere `Trace` `Information` messaggi durante la risoluzione dei problemi.

La sezione [Filtro dei log](#log-filtering) più avanti in questo articolo descrive come controllare quali livelli di registrazione gestisce un provider.

ASP.NET Core scrive log per gli eventi del framework. Gli esempi di log illustrati in precedenza in questo articolo escludono i log al di sotto del livello `Information`, quindi non vengono creati log di livello `Debug` o `Trace`. Ecco un esempio di log della console prodotti eseguendo l'app di esempio configurata in modo da mostrare i log di livello `Debug`:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>ID evento di registrazione

Ogni log può specificare un *ID evento*. A tale scopo, l'app di esempio usa una classe `LoggingEvents` definita a livello locale:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Un ID evento associa un set di eventi. Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.

Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo. Il provider Debug non visualizza gli ID evento. Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Modello di messaggio di registrazione

Ogni log specifica un modello di messaggio. Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti. Usare nomi per i segnaposto, non numeri.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti. Nel codice seguente, si noti che i nomi dei parametri non sono in sequenza nel modello di messaggio:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Questo codice crea un messaggio di log con i valori dei parametri in sequenza:

```text
Parameter values: parm1, parm2
```

Il framework di registrazione funziona in questo modo per consentire ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato. Queste informazioni consentono ai provider di registrazione di archiviare i valori dei parametri come campi. Si supponga, ad esempio, che il metodo logger esegua chiamate simili alla seguente:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Se si inviano i log all'archiviazione tabelle di Azure, ogni entità tabella di Azure può avere le proprietà `ID` e `RequestTime`, il che semplifica le query sui dati dei log. Una query può trovare tutti i log entro un determinato intervallo `RequestTime` senza che sia necessario analizzare il messaggio di testo per determinare l'intervallo di tempo.

## <a name="logging-exceptions"></a>Registrazione delle eccezioni

I metodi logger dispongono di overload che consentono di passare un'eccezione, come nell'esempio seguente:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Provider diversi gestiscono le informazioni sulle eccezioni in modi diversi. Ecco un esempio di output del provider Debug dal codice sopra riportato.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtro del log

È possibile specificare un livello di registrazione minimo per un provider e una categoria specifici oppure per tutti i provider o tutte le categorie. Tutti i log sotto il livello minimo non sono passati al provider, quindi non vengono visualizzati o archiviati.

Per eliminare tutti i log, specificare `LogLevel.None` come livello di registrazione minimo. Il valore intero di `LogLevel.None` è 6, maggiore di `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Creare regole di filtro nella configurazione

Il codice del modello di progetto chiama `CreateDefaultBuilder` per impostare la registrazione per i provider console, debug e EventSource (ASP.NET Core 2,2 o versione successiva). Il metodo `CreateDefaultBuilder` imposta inoltre la registrazione per la ricerca della configurazione in una sezione `Logging`, come illustrato [in precedenza in questo articolo](#configuration).

I dati di configurazione specificano i livelli di registrazione minimi in base al provider e alla categoria, come nell'esempio seguente:

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Questo codice JSON crea sei regole di filtro, una per il provider Debug, quattro per il provider Console e una per tutti i provider. Quando viene creato un oggetto `ILogger`, viene scelta una singola regola per ogni provider.

### <a name="filter-rules-in-code"></a>Regole di filtro nel codice

L'esempio seguente illustra come registrare le regole di filtro nel codice:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

Il secondo `AddFilter` specifica il provider Debug tramite il nome del tipo. Il primo `AddFilter` si applica a tutti i provider in quanto non consente di specificare un tipo di provider.

### <a name="how-filtering-rules-are-applied"></a>Applicazione delle regole di filtro

I dati di configurazione e il codice `AddFilter` illustrato negli esempi precedenti creano le regole indicate nella tabella seguente. I primi sei provengono dall'esempio di configurazione e gli ultimi due dall'esempio di codice.

| Number | Provider      | Categorie che iniziano con...          | Livello di registrazione minimo |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Debug         | Tutte le categorie                          | Informazioni       |
| 2      | Console       | Microsoft. AspNetCore. Mvc. Razor . Interno | Avviso           |
| 3      | Console       | Microsoft. AspNetCore. Mvc. Razor .Razor    | Debug             |
| 4      | Console       | Microsoft. AspNetCore. Mvc.Razor          | Errore             |
| 5      | Console       | Tutte le categorie                          | Informazioni       |
| 6      | Tutti i provider | Tutte le categorie                          | Debug             |
| 7      | Tutti i provider | System                                  | Debug             |
| 8      | Debug         | Microsoft                               | Trace             |

Quando viene creato un oggetto `ILogger`, l'oggetto `ILoggerFactory` seleziona una singola regola per ogni provider da applicare al logger. Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate. Tra le regole disponibili viene selezionata la regola più specifica possibile per ogni coppia di categoria e provider.

L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:

* Selezionare tutte le regole corrispondenti al provider o al relativo alias. Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.
* Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo. Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.
* Se sono selezionate più regole, scegliere l'**ultima**.
* Se non è selezionata alcuna regola, usare `MinimumLevel`.

Con l'elenco di regole precedente, si supponga di creare un `ILogger` oggetto per la categoria "Microsoft. AspNetCore. Mvc. Razor .. Razor ViewEngine ":

* Per il provider Debug si applicano le regole 1, 6 e 8. La regola 8 è più specifica, così sarà quella selezionata.
* Per il provider Console si applicano le regole 3, 4, 5 e 6. La regola 3 è la più specifica.

L'istanza di `ILogger` risultante invia i log di livello `Trace` o superiore al provider Debug. I log di livello `Debug` o superiore vengono inviati al provider Console.

### <a name="provider-aliases"></a>Alias dei provider

Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo.  Per i provider predefiniti, usare gli alias seguenti:

* Console
* Debug
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Livello minimo predefinito

Esiste un'impostazione del livello minimo che diventa effettiva solo se non si applica alcuna regola di configurazione o codice per un provider e una categoria specifici. L'esempio seguente illustra come impostare il livello minimo:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Se non si imposta in modo esplicito il livello minimo, il valore predefinito è `Information`, che indica che i log `Trace` e `Debug` vengono ignorati.

### <a name="filter-functions"></a>Funzioni di filtro

Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice. Il codice della funzione ha accesso al tipo di provider, alla categoria e al livello di registrazione. Ad esempio:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a>Livelli e categorie di sistema

Di seguito sono elencate alcune categorie usate da ASP.NET Core ed Entity Framework Core, con note sui log previsti per ognuna:

| Category                            | Note |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnostica generale di ASP.NET Core. |
| Microsoft.AspNetCore.DataProtection | Chiavi considerate, trovate e usate. |
| Microsoft.AspNetCore.HostFiltering  | Host consentiti. |
| Microsoft.AspNetCore.Hosting        | Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste. Assembly di avvio dell'hosting caricati. |
| Microsoft.AspNetCore.Mvc            | MVC e Razor diagnostica. Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni. |
| Microsoft.AspNetCore.Routing        | Informazioni sulla corrispondenza di route. |
| Microsoft.AspNetCore.Server         | Risposte di avvio, arresto e keep-alive per la connessione. Informazioni sui certificati HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | File forniti. |
| Microsoft.EntityFrameworkCore       | Diagnostica generale di Entity Framework Core. Attività e configurazione del database, rilevamento delle modifiche, migrazioni. |

## <a name="log-scopes"></a>Ambiti dei log

 Un *ambito* può raggruppare un set di operazioni logiche. Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set. Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.

Un ambito è un tipo `IDisposable` restituito dal metodo <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e viene mantenuto fino all'eliminazione. Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Il codice seguente abilita gli ambiti per il provider Console:

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> La configurazione dell'opzione del logger della console `IncludeScopes` è necessaria per abilitare la registrazione basata sull'ambito.
>
> Per informazioni sulla configurazione, vedere la sezione [Configurazione](#configuration).

Ogni messaggio di registrazione include le informazioni sull'ambito:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Provider di registrazione predefiniti

ASP.NET Core include i provider seguenti:

* [Console](#console-provider)
* [Eseguire il debug](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Per informazioni su StdOut e la registrazione debug con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console-provider"></a>Provider Console

Il pacchetto del provider [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) invia l'output della registrazione alla console. 

```csharp
logging.AddConsole();
```

Per visualizzare l'output di registrazione del provider Console, aprire un prompt dei comandi nella cartella del progetto ed eseguire il comando seguente:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Provider Debug

Il pacchetto del provider [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) scrive l'output della registrazione usando la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chiamate del metodo `Debug.WriteLine`).

In Linux, questo provider scrive i log in */var/log/message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Provider origine evento

Il pacchetto del provider [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) scrive in un'origine evento multipiattaforma con il nome `Microsoft-Extensions-Logging` . In Windows, il provider utilizza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

Il provider di origine eventi viene aggiunto automaticamente quando `CreateDefaultBuilder` viene chiamato per compilare l'host.

Usare l' [utilità PerfView](https://github.com/Microsoft/perfview) per raccogliere e visualizzare i log. Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.

Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi** (non dimenticare l'asterisco all'inizio della stringa).

![Provider aggiuntivi di PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Provider EventLog di Windows

Il pacchetto di provider [Microsoft.Extensions.Logging.AventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) invia l'output del log al registro eventi di Windows.

```csharp
logging.AddEventLog();
```

Gli [overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) consentono di passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>. Se `null` o non è specificato, vengono usate le impostazioni predefinite seguenti:

* `LogName`: "Applicazione"
* `SourceName`: "Runtime .NET"
* `MachineName`: Viene usato il nome del computer locale.

Gli eventi vengono registrati per un [livello di avviso e un livello superiore](#log-level). Nell'esempio seguente viene impostato il livello di registrazione predefinito del registro eventi su <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a>Provider TraceSource

Il pacchetto di provider [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa le librerie e i provider <xref:System.Diagnostics.TraceSource>.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

Gli [overload di AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) consentono di passare un cambio di origine e un listener di traccia.

Per usare questo provider, un'app deve essere in esecuzione in .NET Framework (invece che in .NET Core). Il provider può instradare i messaggi a diversi [listener](/dotnet/framework/debug-trace-profile/trace-listeners), come <xref:System.Diagnostics.TextWriterTraceListener>, usato nell'app di esempio.

### <a name="azure-app-service-provider"></a>Provider del Servizio app di Azure

Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.

```csharp
logging.AddAzureWebAppDiagnostics();
```

Il pacchetto del provider non è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Quando la destinazione è .NET Framework o il riferimento è il metapacchetto `Microsoft.AspNetCore.App`, aggiungere il pacchetto del provider al progetto. 

Un overload di <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> consente di passare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. L'oggetto impostazioni può eseguire l'override delle impostazioni predefinite, ad esempio il modello di output di registrazione, il nome di BLOB e il limite di dimensioni di file. Il *modello di output* è un modello di messaggio che viene applicato a tutti i log in aggiunta a quanto specificato quando si chiama un metodo `ILogger`.

Quando si distribuisce un'app del servizio app, l'applicazione applica le impostazioni della sezione [Log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) della pagina **Servizio app** del portale di Azure. Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.

* **Registrazione applicazioni (file system)**
* **Registrazione applicazione (BLOB)**

Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*. Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2. Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.

Il provider funziona solo quando il progetto viene eseguito nell'ambiente di Azure. Non ha alcun effetto quando il progetto viene eseguito localmente, in quanto non scrive nulla in file locali o in archivi di sviluppo locali per i BLOB.

#### <a name="azure-log-streaming"></a>Flusso di registrazione di Azure

Il flusso di registrazione di Azure consente di visualizzare l'attività di registrazione in tempo reale da:

* Server applicazioni
* Server Web
* Traccia delle richieste non riuscite

Per configurare il flusso di registrazione di Azure:

* Passare alla pagina **Log del servizio app** dalla pagina del portale dell'app.
* Impostare **Registrazione applicazioni (file system)** su **Attiva**.
* Scegliere il livello di registrazione in **Livello**. Questa impostazione è valida solo per lo streaming dei log di Azure, non per altri provider di registrazione nell'app.

Passare alla pagina **Flusso di registrazione** per visualizzare i messaggi dell'app. I messaggi vengono registrati dall'app tramite l'interfaccia `ILogger`.

### <a name="azure-application-insights-trace-logging"></a>Registrazione di traccia di Azure Application Insights

Il pacchetto di provider [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive log in Azure Application Insights. Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi. Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.

Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core. Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.

Non usare il pacchetto [](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)Microsoft.ApplicationInsights.Web&mdash;, che è per ASP.NET 4.x.

Per altre informazioni, vedere le risorse seguenti:

* [Panoramica di Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.
* [ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.
* [Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.

## <a name="third-party-logging-providers"></a>Provider di registrazione di terze parti

Framework di registrazione di terze parti che usano ASP.NET Core:

* [elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4net](https://logging.apache.org/log4net/) ([repository GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))

Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:

1. Aggiungere un pacchetto NuGet al progetto.
1. Chiamare un `ILoggerFactory` metodo di estensione fornito dal framework di registrazione.

Per altre informazioni, vedere la documentazione di ogni provider. I provider di registrazione di terze parti non sono supportati da Microsoft.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
