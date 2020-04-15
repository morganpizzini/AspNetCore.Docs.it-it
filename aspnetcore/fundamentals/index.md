---
title: Nozioni fondamentali su ASP.NET Core
author: rick-anderson
description: Informazioni sui concetti fondamentali per la compilazione di app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
uid: fundamentals/index
ms.openlocfilehash: c675644d8480ef7a5290045067e6cec2ea6f4764
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384053"
---
# <a name="aspnet-core-fundamentals"></a>Nozioni fondamentali su ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Questo articolo fornisce una panoramica degli argomenti chiave per informazioni su come sviluppare app ASP.NET Core.

## <a name="the-startup-class"></a>Classe Startup

All'interno della classe `Startup`:

* Vengono configurati i servizi necessari per l'app.
* La pipeline di gestione delle richieste dell'app è definita, come una serie di componenti middleware.

Ecco un esempio di classe `Startup`:

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

Per altre informazioni, vedere <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Inserimento di dipendenze (servizi)

ASP.NET Core include un framework di inserimento delle dipendenze (DI) incorporato che rende disponibili i servizi configurati in un'app. Ad esempio, un componente di registrazione è un servizio.

Il codice per configurare o *registrare* i servizi viene aggiunto al metodo `Startup.ConfigureServices`. Ad esempio:

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

I servizi vengono in genere risolti da DI utilizzando l'inserimento del costruttore. Con l'inserimento del costruttore, una classe dichiara un parametro del costruttore del tipo richiesto o di un'interfaccia. Il framework DI fornisce un'istanza di questo servizio in fase di esecuzione.

L'esempio seguente usa l'inserimento del costruttore per risolvere un da DI:The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

Se il contenitore IoC (Inversion of Control) incorporato non soddisfa tutte le esigenze di un'app, è possibile usare un contenitore IoC di terze parti.

Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

La pipeline di gestione delle richieste è strutturata come una serie di componenti middleware. Ogni componente esegue `HttpContext` operazioni su un oggetto e richiama il middleware successivo nella pipeline o termina la richiesta.

Per convenzione, un componente middleware viene aggiunto `Use...` alla pipeline `Startup.Configure` richiamando un metodo di estensione nel metodo. Per abilitare il rendering dei file statici, ad esempio, chiamare `UseStaticFiles`.

L'esempio seguente configura una pipeline di gestione delle richieste:The following example configures a request handling pipeline:

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

ASP.NET Core include un ricco set di middleware incorporati. È inoltre possibile scrivere componenti middleware personalizzati.

Per altre informazioni, vedere <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

All'avvio, un'app ASP.NET Core compila un *host.* L'host incapsula tutte le risorse dell'app, ad esempio:The host encapsulates all of the app's resources, such as:

* Un'implementazione del server HTTP
* I componenti middleware
* Registrazione
* Servizi di inserimento delle dipendenze (DI)
* Configurazione

Ci sono due host diversi: 

* Host generico .NET
* Host Web ASP.NET Core

Si consiglia l'host generico .NET. L'host Web ASP.NET Core è disponibile solo per la compatibilità con le versioni precedenti.

L'esempio seguente crea un host generico .NET:The following example creates a .NET Generic Host:

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

I `CreateDefaultBuilder` `ConfigureWebHostDefaults` metodi e configurano un host con un set di opzioni predefinite, ad esempio:

* Usare [Kestrel](#servers) come server Web e abilitare l'integrazione IIS.
* Caricare la configurazione da *appsettings.json*, *appsettings.[EnvironmentName].json*, variabili di ambiente, argomenti della riga di comando e altri origini di configurazione.
* Inviare l'output di registrazione alla console e ai provider di debug.

Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.

### <a name="non-web-scenarios"></a>Scenari non Web

L'host generico consente ad altri tipi di app di usare estensioni del framework trasversali quali la registrazione, l'inserimento delle dipendenze, la configurazione e la gestione del ciclo di vita delle app. Per altre informazioni, vedere <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Server

Un'app ASP.NET Core usa un'implementazione del server HTTP per l'ascolto delle richieste HTTP. Il server espone le richieste all'app sotto forma di insieme di [funzionalità di richiesta](xref:fundamentals/request-features) strutturate in un `HttpContext`.

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core include le implementazioni server seguenti:

* *Kestrel* è un server Web multipiattaforma. Kestrel viene spesso eseguito in una configurazione con proxy inverso tramite [IIS](https://www.iis.net/). In ASP.NET Core 2.0 o versione successiva Kestrel può essere eseguito come server perimetrale pubblico esposto direttamente a Internet.
* *Server HTTP IIS* è un server per Windows che utilizza IIS. Con questo server l'app ASP.NET Core e IIS sono eseguiti nello stesso processo.
* *HTTP.sys* è un server per Windows che non viene usato con IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core fornisce l'implementazione del server multipiattaforma *Kestrel*. In ASP.NET Core 2.0 o versioni successive, Kestrel può essere eseguito come server perimetrale pubblico esposto direttamente a Internet. Kestrel viene spesso eseguito in una configurazione con proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core fornisce l'implementazione del server multipiattaforma *Kestrel*. In ASP.NET Core 2.0 o versioni successive, Kestrel può essere eseguito come server perimetrale pubblico esposto direttamente a Internet. Kestrel viene spesso eseguito in una configurazione con proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).

---

Per altre informazioni, vedere <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Configurazione

ASP.NET Core offre un framework di configurazione che ottiene le impostazioni, ad esempio coppie nome-valore, da un set ordinato di provider di configurazione. I provider di configurazione incorporati sono disponibili per un'ampia gamma di origini, ad esempio file *con estensione json,* *xml,* variabili di ambiente e argomenti della riga di comando. Scrivere provider di configurazione personalizzati per supportare altre origini.

Per [impostazione predefinita,](xref:fundamentals/configuration/index#default)ASP.NET app Core sono configurate per la lettura da *appsettings.json*, le variabili di ambiente, la riga di comando e altro ancora. Quando viene caricata la configurazione dell'app, i valori delle variabili di ambiente sostituiscono i valori da *appsettings.json*.

Il modo migliore per leggere i valori di configurazione correlati consiste nell'utilizzare il modello di [opzioni](xref:fundamentals/configuration/options). Per ulteriori informazioni, consultate Associare dati di [configurazione gerarchici utilizzando il modello](xref:fundamentals/configuration/index#optpat)di opzioni.

Per la gestione di dati di configurazione riservati, ad esempio le password, ASP.NET Core fornisce [Secret Manager](xref:security/app-secrets#secret-manager). Per i segreti di produzione, si consiglia di usare [Azure Key Vault](xref:security/key-vault-configuration).

Per altre informazioni, vedere <xref:fundamentals/configuration/index>.

## <a name="environments"></a>Ambienti

Gli ambienti di `Development` `Staging`esecuzione, `Production`ad esempio , e , sono una nozione di prima classe in ASP.NET Core. Specificare l'ambiente in cui `ASPNETCORE_ENVIRONMENT` è in esecuzione un'app impostando la variabile di ambiente. ASP.NET Core legge tale variabile di ambiente all'avvio dell'app e ne archivia il valore in un'implementazione `IWebHostEnvironment`. Questa implementazione è disponibile in qualsiasi punto di un'app tramite inserimento delle dipendenze (DI).

L'esempio seguente configura l'app per fornire informazioni `Development` dettagliate sull'errore durante l'esecuzione nell'ambiente:The following example configures the app to provide detailed error information when running in the environment:

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

Per altre informazioni, vedere <xref:fundamentals/environments>.

## <a name="logging"></a>Registrazione

ASP.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti. I fornitori disponibili includono:

* Console
* Debug
* Event Tracing for Windows
* Registro eventi di Windows
* TraceSource
* Servizio app di Azure
* Azure Application Insights

Per creare i <xref:Microsoft.Extensions.Logging.ILogger%601> log, risolvere un servizio dall'inserimento <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>delle dipendenze (DI) e chiamare metodi di registrazione quali . Ad esempio:

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

I metodi `LogInformation` di registrazione, ad esempio, supportano un numero qualsiasi di campi. Questi campi vengono in genere `string`utilizzati per costruire un messaggio , ma alcuni provider di registrazione li inviano a un archivio dati come campi separati. Questa funzionalità consente ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Per altre informazioni, vedere <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routing.

Una *route* è un modello URL di cui è stato eseguito il mapping su un gestore. Il gestore è in genere una pagina Razor, un metodo di azione in un controller MVC o un tipo di middleware. Il routing di ASP.NET Core consente di controllare gli URL usati dall'app.

Per altre informazioni, vedere <xref:fundamentals/routing>.

## <a name="error-handling"></a>Gestione degli errori

ASP.NET Core dispone di funzionalità predefinite per la gestione degli errori, ad esempio:

* Una pagina delle eccezioni per gli sviluppatori
* Pagine di errore personalizzate
* Pagine dei codici di stato statiche
* Gestione delle eccezioni durante l'avvio

Per altre informazioni, vedere <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Creare richieste HTTP

Un'implementazione di `IHttpClientFactory` è disponibile per la creazione di istanze `HttpClient`. Il factory:

* Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche. Ad esempio, registrare e configurare un client github per l'accesso a GitHub.For example, register and configure a *github* client for accessing GitHub. Registrare e configurare un client predefinito per altri scopi.
* Supporta la registrazione e il concatenamento di più gestori di delega per creare una pipeline di middleware per le richieste in uscita. Questo modello è simile a ASP.NET pipeline middleware in ingresso di Core.This pattern is similar to ASP.NET Core's inbound middleware pipeline. Il modello fornisce un meccanismo per gestire i problemi trasversali per le richieste HTTP, tra cui la memorizzazione nella cache, la gestione degli errori, la serializzazione e la registrazione.
* Si integra con *Polly*, una famosa libreria di terze parti per la gestione degli errori temporanei.
* Gestisce il pooling e `HttpClientHandler` la durata delle istanze `HttpClient` sottostanti per evitare problemi DNS comuni che si verificano durante la gestione manuale delle durate.
* Aggiunge un'esperienza di <xref:Microsoft.Extensions.Logging.ILogger> registrazione configurabile tramite tutte le richieste inviate tramite client creati dalla factory.

Per altre informazioni, vedere <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Radice del contenuto

La radice del contenuto è il percorso di base per:The content root is the base path for:

* L'eseguibile che ospita l'app (*.exe*).
* Assembly compilati che costituiscono l'app (*.dll*).
* File di contenuto utilizzati dall'app, ad esempio:
  * File Razor (*.cshtml*, *.razor*)
  * File di configurazione (*.json*, *.xml*)
  * File di dati (*.db*)
* La [directory principale Web](#web-root), in genere la cartella *wwwroot.*

Durante lo sviluppo, per impostazione predefinita la radice del contenuto viene la directory radice del progetto. Questa directory è anche il percorso di base sia per i file di contenuto dell'app che per la [radice Web.](#web-root) Specificare una radice del contenuto diversa impostandone il percorso durante [la compilazione dell'host.](#host) Per altre informazioni, vedere [Radice del contenuto](xref:fundamentals/host/generic-host#contentroot).

## <a name="web-root"></a>Radice Web

La radice Web è il percorso di base per i file di risorse statici pubblici, ad esempio:The web root is the base path for public, static resource files, such as:

* Fogli di stile (*.css*)
* JavaScript (*.js*)
* Immagini (*.png*, *.jpg*)

Per impostazione predefinita, i file statici vengono serviti solo dalla directory radice Web e dalle relative sottodirectory. Per impostazione predefinita, il percorso radice del Web è impostato su * *. Specificare una radice Web diversa impostandone il percorso durante [la creazione dell'host.](#host) Per altre informazioni, vedere [Web root](xref:fundamentals/host/generic-host#webroot) (Radice Web).

Impedire la pubblicazione di file in *wwwroot* con [ \<l'elemento](/visualstudio/msbuild/common-msbuild-project-items#content) di progetto Content> nel file di progetto. L'esempio seguente impedisce la pubblicazione del contenuto in *wwwroot/local* e nelle relative sottodirectory:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Nei file *con estensione cshtml* Razor,`~/`tilde-slash ( ) punta alla radice Web. Un percorso `~/` che inizia con viene definito *percorso virtuale*.

Per altre informazioni, vedere <xref:fundamentals/static-files>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Questo articolo contiene una panoramica degli argomenti chiave necessari per comprendere come sviluppare app ASP.NET Core.

## <a name="the-startup-class"></a>Classe Startup

All'interno della classe `Startup`:

* Vengono configurati i servizi necessari per l'app.
* Viene definita la pipeline di gestione delle richieste.

I *servizi* sono componenti usati dall'app. Ad esempio, un componente di registrazione è un servizio. Il codice per configurare o *registrare* i servizi viene aggiunto al metodo `Startup.ConfigureServices`.

La pipeline di gestione delle richieste è composta da una serie di componenti *middleware.* Un componente middleware, ad esempio, potrebbe gestire le richieste per i file statici o reindirizzare le richieste HTTP a HTTPS. Ogni componente middleware esegue operazioni asincrone su un `HttpContext`, quindi richiama il componente middleware successivo della pipeline oppure termina la richiesta. Il codice per configurare la pipeline di gestione delle richieste viene aggiunto al metodo `Startup.Configure`.

Ecco un esempio di classe `Startup`:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

Per altre informazioni, vedere <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Inserimento di dipendenze (servizi)

ASP.NET Core include un framework di inserimento delle dipendenze predefinito che rende disponibili i servizi configurati per le classi di un'app. Un modo per inserire un'istanza di un servizio in una classe consiste nel creare un costruttore con un parametro del tipo richiesto. Il parametro può essere il tipo di servizio o un'interfaccia. Il sistema di inserimento delle dipendenze fornisce il servizio in runtime.

Di seguito viene proposto un esempio di classe che usa l'inserimento delle dipendenze per ottenere un oggetto di contesto Entity Framework Core. La riga evidenziata è un esempio di inserimento costruttore:

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

Nonostante l'inserimento delle dipendenze sia predefinito, è progettato per consentire il collegamento di un contenitore di inversione del controllo (IoC) di terze parti, qualora lo si preferisse.

Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

La pipeline di gestione delle richieste è strutturata come una serie di componenti middleware. Ogni componente esegue operazioni asincrone su un `HttpContext`, quindi richiama il componente middleware successivo nella pipeline oppure termina la richiesta.

Per convenzione viene aggiunto un componente middleware alla pipeline richiamando il relativo metodo di estensione `Use...` nel metodo `Startup.Configure`. Per abilitare il rendering dei file statici, ad esempio, chiamare `UseStaticFiles`.

Il codice evidenziato nell'esempio seguente configura la pipeline di gestione delle richieste:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

ASP.NET Core include un ampio set di middleware predefiniti, ma consente anche di scrivere middleware personalizzati.

Per altre informazioni, vedere <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Un'app ASP.NET Core crea un *host* all'avvio. L'host è un oggetto che incapsula tutte le risorse dell'app, ad esempio:

* Un'implementazione del server HTTP
* I componenti middleware
* Registrazione
* DI
* Configurazione

Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.

Sono disponibili due host: l'host Web e l'host generico. In ASP.NET Core 2.x, l'host generico è destinato solo agli scenari non Web.

Il codice per creare un host si trova in `Program.Main`:

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

Il metodo `CreateDefaultBuilder` configura un host con le opzioni usate comunemente, ad esempio:

* Usare [Kestrel](#servers) come server Web e abilitare l'integrazione IIS.
* Caricare la configurazione da *appsettings.json*, *appsettings.[EnvironmentName].json*, variabili di ambiente, argomenti della riga di comando e altri origini di configurazione.
* Inviare l'output di registrazione alla console e ai provider di debug.

Per altre informazioni, vedere <xref:fundamentals/host/web-host>.

### <a name="non-web-scenarios"></a>Scenari non Web

L'host generico consente ad altri tipi di app di usare estensioni del framework trasversali quali la registrazione, l'inserimento delle dipendenze, la configurazione e la gestione del ciclo di vita delle app. Per altre informazioni, vedere <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Server

Un'app ASP.NET Core usa un'implementazione del server HTTP per l'ascolto delle richieste HTTP. Il server espone le richieste all'app sotto forma di insieme di [funzionalità di richiesta](xref:fundamentals/request-features) strutturate in un `HttpContext`.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core include le implementazioni server seguenti:

* *Kestrel* è un server Web multipiattaforma. Kestrel viene spesso eseguito in una configurazione con proxy inverso tramite [IIS](https://www.iis.net/). Kestrel può essere eseguito come server perimetrale pubblico esposto direttamente a Internet.
* *Server HTTP IIS* è un server per Windows che usa IIS. Con questo server l'app ASP.NET Core e IIS sono eseguiti nello stesso processo.
* *HTTP.sys* è un server per Windows che non viene usato con IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core fornisce l'implementazione del server multipiattaforma *Kestrel*. Kestrel può essere eseguito come server perimetrale pubblico esposto direttamente a Internet. Kestrel viene spesso eseguito in una configurazione con proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core fornisce l'implementazione del server multipiattaforma *Kestrel*. Kestrel può essere eseguito come server perimetrale pubblico esposto direttamente a Internet. Kestrel viene spesso eseguito in una configurazione con proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core include le implementazioni server seguenti:

* *Kestrel* è un server Web multipiattaforma. Kestrel viene spesso eseguito in una configurazione con proxy inverso tramite [IIS](https://www.iis.net/). Kestrel può essere eseguito come server perimetrale pubblico esposto direttamente a Internet.
* *HTTP.sys* è un server per Windows che non viene usato con IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core fornisce l'implementazione del server multipiattaforma *Kestrel*. Kestrel può essere eseguito come server perimetrale pubblico esposto direttamente a Internet. Kestrel viene spesso eseguito in una configurazione con proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core fornisce l'implementazione del server multipiattaforma *Kestrel*. Kestrel può essere eseguito come server perimetrale pubblico esposto direttamente a Internet. Kestrel viene spesso eseguito in una configurazione con proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Per altre informazioni, vedere <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Configurazione

ASP.NET Core offre un framework di configurazione che ottiene le impostazioni, ad esempio coppie nome-valore, da un set ordinato di provider di configurazione. Esistono provider di configurazione predefiniti per un'ampia gamma di origini, ad esempio file con estensione *JSON*, file con estensione *XML*, variabili di ambiente e argomenti della riga di comando. È anche possibile scrivere provider di configurazione personalizzati.

Si potrebbe specificare, ad esempio, che la configurazione proviene da *appsettings.json* e da variabili di ambiente. Quindi, quando viene richiesto il valore di *ConnectionString*, il framework esaminerebbe per primo il file *appsettings.json*. Se il valore venisse trovato qui ma anche in una variabile di ambiente, il valore della variabile di ambiente avrebbe la precedenza.

Per la gestione dei dati di configurazione riservati, ad esempio le password, ASP.NET Core offre uno [strumento Secret Manager](xref:security/app-secrets). Per i segreti di produzione, si consiglia di usare [Azure Key Vault](xref:security/key-vault-configuration).

Per altre informazioni, vedere <xref:fundamentals/configuration/index>.

## <a name="options"></a>Opzioni

Ove possibile, ASP.NET Core segue il *modello di opzioni* per archiviare e recuperare i valori di configurazione. Il modello di opzioni usa le classi per rappresentare i gruppi di impostazioni correlate.

Il codice seguente, ad esempio, imposta le opzioni WebSockets:

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

Per altre informazioni, vedere <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Ambienti

Gli ambienti di esecuzione, ad esempio *Sviluppo*, *Staging* e *Produzione*, sono un concetto fondamentale in ASP.NET Core. È possibile specificare l'ambiente in cui viene eseguita un'app impostando la variabile di ambiente `ASPNETCORE_ENVIRONMENT`. ASP.NET Core legge tale variabile di ambiente all'avvio dell'app e ne archivia il valore in un'implementazione `IHostingEnvironment`. L'oggetto ambiente è disponibile in un punto qualsiasi dell'app tramite l'inserimento delle dipendenze.

Il seguente codice di esempio della classe `Startup` configura l'app in modo che fornisca informazioni dettagliate sull'errore solo quando viene eseguita nell'ambiente di sviluppo:

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

Per altre informazioni, vedere <xref:fundamentals/environments>.

## <a name="logging"></a>Registrazione

ASP.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti. Tra i provider disponibili sono inclusi i seguenti:

* Console
* Debug
* Event Tracing for Windows
* Registro eventi di Windows
* TraceSource
* Servizio app di Azure
* Azure Application Insights

Scrivere i log da un punto qualsiasi del codice di un'app recuperando un oggetto `ILogger` dall'inserimento delle dipendenze e chiamando i metodi di registrazione.

Ecco un esempio di codice che usa un oggetto `ILogger` con inserimento costruttore e chiamate al metodo di registrazione evidenziati.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

L'interfaccia `ILogger` consente di passare qualsiasi numero di campi al provider di registrazione. I campi vengono usati comunemente per costruire una stringa di messaggio, ma il provider può anche inviarli come campi separati a un archivio dati. Questa funzionalità consente ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Per altre informazioni, vedere <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routing.

Una *route* è un modello URL di cui è stato eseguito il mapping su un gestore. Il gestore è in genere una pagina Razor, un metodo di azione in un controller MVC o un tipo di middleware. Il routing di ASP.NET Core consente di controllare gli URL usati dall'app.

Per altre informazioni, vedere <xref:fundamentals/routing>.

## <a name="error-handling"></a>Gestione degli errori

ASP.NET Core dispone di funzionalità predefinite per la gestione degli errori, ad esempio:

* Una pagina delle eccezioni per gli sviluppatori
* Pagine di errore personalizzate
* Pagine dei codici di stato statiche
* Gestione delle eccezioni durante l'avvio

Per altre informazioni, vedere <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Creare richieste HTTP

Un'implementazione di `IHttpClientFactory` è disponibile per la creazione di istanze `HttpClient`. Il factory:

* Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche. Ad esempio, un client github può essere registrato e configurato per accedere a GitHub.For example, a *github* client can be registered and configured to access GitHub. È possibile registrare un client predefinito per altri scopi.
* Supporta la registrazione e il concatenamento di più gestori di delega per creare una pipeline di middleware per le richieste in uscita. Questo modello è simile alla pipeline di middleware in ingresso in ASP.NET Core. Il modello offre un meccanismo per gestire le problematiche trasversali relative alle richieste HTTP, tra cui memorizzazione nella cache, gestione degli errori, serializzazione e registrazione.
* Si integra con *Polly*, una famosa libreria di terze parti per la gestione degli errori temporanei.
* Gestisce il pooling e la durata delle istanze di `HttpClientHandler` sottostanti per evitare problemi DNS comuni che si verificano quando le durate di `HttpClient` vengono gestite manualmente.
* Aggiunge un'esperienza di registrazione configurabile, tramite `ILogger`, per tutte le richieste inviate attraverso i client creati dalla factory.

Per altre informazioni, vedere <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Radice del contenuto

La radice del contenuto è il percorso di base di:

* Eseguibile che ospita l'app (*.exe*).
* Assembly compilati che costituiscono l'app (*.dll*).
* File di contenuto non di codice usati dall'app, ad esempio:
  * File Razor (*.cshtml*, *.razor*)
  * File di configurazione (*.json*, *.xml*)
  * File di dati (*.db*)
* [Radice Web](#web-root), in genere la cartella *wwwroot* pubblicata.

Durante lo sviluppo:

* Per impostazione predefinita, la radice del contenuto è la directory radice del progetto.
* La directory radice del progetto viene utilizzata per creare:
  * Percorso dei file di contenuto non di codice dell'app nella directory radice del progetto.
  * [Radice Web](#web-root), in genere la cartella *wwwroot* nella directory radice del progetto.

È possibile specificare un percorso radice del contenuto alternativo durante [la compilazione dell'host.](#host) Per altre informazioni, vedere <xref:fundamentals/host/web-host#content-root>.

## <a name="web-root"></a>Radice Web

La radice Web è il percorso di base per i file di risorse statici pubblici, non di codice, ad esempio:The web root is the base path to public, non-code, static resource files, such as:

* Fogli di stile (*.css*)
* JavaScript (*.js*)
* Immagini (*.png*, *.jpg*)

I file statici vengono serviti solo per impostazione predefinita dalla directory principale Web (e dalle sottodirectory).

Per impostazione predefinita, il percorso radice del Web ha il valore predefinito di *"content root"/wwwroot*, ma è possibile specificare una radice Web diversa durante [la compilazione dell'host.](#host) Per altre informazioni, vedere [Web root](xref:fundamentals/host/web-host#web-root) (Radice Web).

Impedire la pubblicazione di file in *wwwroot* con [ \<l'elemento](/visualstudio/msbuild/common-msbuild-project-items#content) di progetto Content> nel file di progetto. L'esempio seguente impedisce la pubblicazione del contenuto nella directory *wwwroot/local* e nelle sottodirectory:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Nei file Razor (*.cshtml*), la`~/`tilde-slash ( ) punta alla radice Web. Un percorso `~/` che inizia con viene definito *percorso virtuale*.

Per altre informazioni, vedere <xref:fundamentals/static-files>.

::: moniker-end
