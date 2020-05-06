---
title: Sessione in ASP.NET Core
author: rick-anderson
description: Individuare gli approcci per mantenere la sessione tra le richieste.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/app-state
ms.openlocfilehash: c29b58eb14a7962f53f2c8c48067de2f5872fded
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774808"
---
# <a name="session-and-state-management-in-aspnet-core"></a>Gestione delle sessioni e dello stato in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Diana lotti](https://github.com/DianaLaRose)

HTTP è un protocollo senza stato. Per impostazione predefinita, le richieste HTTP sono messaggi indipendenti che non mantengono i valori utente. Questo articolo descrive diversi approcci per mantenere i dati degli utenti tra le richieste.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Gestione dello stato

Lo stato può essere archiviato usando diversi approcci. Ogni approccio è descritto di seguito in questo argomento.

| Approccio con risorsa di archiviazione | Meccanismo di archiviazione |
| ---------------- | ----------------- |
| [Cookie](#cookies) | Cookie HTTP. Può includere dati archiviati usando il codice dell'app sul lato server. |
| [Stato sessione](#session-state) | Cookie HTTP e codice app lato server |
| [TempData](#tempdata) | Cookie HTTP o stato della sessione |
| [Stringhe di query](#query-strings) | Stringhe di query HTTP |
| [Campi nascosti](#hidden-fields) | Campi dei form HTTP |
| [HttpContext.Items](#httpcontextitems) | Codice app lato server |
| [Cache](#cache) | Codice app lato server |

## <a name="cookies"></a>Cookie

I cookie archiviano i dati tra le richieste. Poiché i cookie vengono inviati con ogni richiesta, la loro dimensione deve essere mantenuta al minimo. Idealmente, in un cookie deve essere archiviato solo un identificatore con i dati archiviati dall'app. La maggior parte dei browser limita la dimensione dei cookie a 4096 byte. Per ogni dominio è disponibile solo un numero limitato di cookie.

Poiché i cookie possono essere manomessi, devono essere convalidati dall'app. I cookie possono essere eliminati dagli utenti e scadono nei client. Tuttavia, i cookie sono in genere la forma più durevole di salvataggio permanente dei dati nel client.

I cookie vengono spesso usati per la personalizzazione, ovvero il contenuto viene personalizzato per un utente noto. L'utente viene solo identificato e non autenticato nella maggior parte dei casi. Il cookie può archiviare il nome dell'utente, il nome dell'account o l'ID utente univoco, ad esempio un GUID. Il cookie può essere usato per accedere alle impostazioni personalizzate dell'utente, ad esempio il colore di sfondo del sito Web preferito.

Vedere le [normative generali sulla protezione dei dati (GDPR) dell'Unione europea](https://ec.europa.eu/info/law/law-topic/data-protection) quando si inviano cookie e si gestiscono problemi di privacy. Per altre informazioni, vedere il [supporto per il Regolamento generale sulla protezione dei dati in ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Stato sessione

Lo stato della sessione è uno scenario di ASP.NET Core per l'archiviazione dei dati utente mentre l'utente visualizza un'app Web. Lo stato della sessione usa un archivio gestito dall'app per rendere persistenti i dati tra le richieste provenienti da un client. I dati della sessione sono supportati da una cache e considerati dati temporanei. Il sito deve continuare a funzionare senza i dati della sessione. I dati critici dell'applicazione devono essere archiviati nel database utente e memorizzati nella cache della sessione solo al fine di ottimizzare le prestazioni.

La sessione non è supportata nelle app [SignalR](xref:signalr/index) poiché un [hub SignalR](xref:signalr/hubs) può essere eseguito indipendentemente da un contesto HTTP. Ad esempio, ciò può verificarsi quando una lunga richiesta di polling viene mantenuta aperta da un hub oltre la durata del contesto HTTP della richiesta.

ASP.NET Core gestisce lo stato della sessione fornendo un cookie al client che contiene un ID di sessione. ID sessione cookie:

* Viene inviato all'app con ogni richiesta.
* Viene usato dall'app per recuperare i dati della sessione.

Lo stato della sessione presenta i comportamenti seguenti:

* Il cookie di sessione è specifico per il browser. Le sessioni non sono condivise tra più browser.
* I cookie di sessione vengono eliminati al termine della sessione del browser.
* Se viene ricevuto un cookie per una sessione scaduta, viene creata una nuova sessione che usa lo stesso cookie di sessione.
* Le sessioni vuote non vengono mantenute. La sessione deve avere almeno un valore impostato per salvare in modo permanente la sessione tra le richieste. Se una sessione non viene conservata, viene generato un nuovo ID sessione per ogni nuova richiesta.
* L'app conserva una sessione per un periodo di tempo limitato dopo l'ultima richiesta. L'app imposta il timeout della sessione o usa il valore predefinito, pari a 20 minuti. Lo stato della sessione è ideale per l'archiviazione dei dati utente:
  * Questo è specifico di una particolare sessione.
  * Dove i dati non richiedono l'archiviazione permanente tra le sessioni.
* I dati della sessione vengono eliminati quando viene chiamata l'implementazione [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) o alla scadenza della sessione.
* Non esiste un meccanismo predefinito per indicare al codice app che un browser client è stato chiuso o che il cookie di sessione viene eliminato o è scaduto nel client.
* Per impostazione predefinita, i cookie di stato della sessione non sono contrassegnati come essenziali. Lo stato della sessione non è funzionante a meno che non sia consentito il rilevamento da parte del visitatore del sito. Per altre informazioni, vedere <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Evitare di archiviare dati sensibili nello stato della sessione. L'utente potrebbe non chiudere il browser e cancellare il cookie di sessione. Alcuni browser mantengono i cookie di sessione validi tra diverse finestre del browser. Una sessione potrebbe non essere limitata a un singolo utente. L'utente successivo potrebbe continuare a esplorare l'app con lo stesso cookie di sessione.

Il provider di cache in memoria archivia i dati della sessione nella memoria del server in cui si trova l'app. In uno scenario di server farm:

* Usare *sessioni permanenti* per associare ogni sessione a un'istanza specifica dell'app in un singolo server. Il [Servizio app di Azure](https://azure.microsoft.com/services/app-service/) usa il modulo [Application Request Routing (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) per applicare le sessioni permanenti per impostazione predefinita. Tuttavia le sessioni permanenti possono compromettere la scalabilità e complicare gli aggiornamenti delle app Web. Un approccio migliore è usare la cache distribuita Redis o SQL Server, che non richiede sessioni permanenti. Per altre informazioni, vedere <xref:performance/caching/distributed>.
* Il cookie di sessione viene crittografato con [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector). La protezione dati deve essere configurata correttamente in modo da leggere i cookie di sessione in ogni computer. Per altre informazioni, vedere <xref:security/data-protection/introduction> e [Provider di archiviazione chiavi](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Configurare lo stato della sessione

Il pacchetto [Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) :

* È incluso in modo implicito dal Framework.
* Fornisce il middleware per la gestione dello stato della sessione.

Per abilitare il middleware della sessione, `Startup` deve contenere:

* Una delle cache di memoria [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). L'implementazione `IDistributedCache` viene usata come archivio di backup per la sessione. Per altre informazioni, vedere <xref:performance/caching/distributed>.
* Una chiamata ad [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) in `ConfigureServices`.
* Una chiamata a [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) in `Configure`.

Il codice seguente indica come configurare il provider della sessione in memoria con un'implementazione in memoria predefinita di `IDistributedCache`:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

Il codice precedente imposta un breve timeout per semplificare i test.

L'ordine del middleware è importante.  Chiamare `UseSession` after `UseRouting` e before `UseEndpoints`. Vedere l' [ordine del middleware](xref:fundamentals/middleware/index#order).

[HttpContext. Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) è disponibile dopo che è stato configurato lo stato della sessione.

Non è possibile accedere a `HttpContext.Session` prima di chiamare `UseSession`.

Non è possibile creare un nuovo cookie di sessione dopo che l'app ha iniziato la scrittura nel flusso di risposte. L'eccezione viene registrata nel log del server Web e non è visualizzata nel browser.

### <a name="load-session-state-asynchronously"></a>Caricare lo stato della sessione in modo asincrono

Il provider di sessione predefinito in ASP.NET Core carica in modo asincrono i record di sessione dall'archivio di backup [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) sottostante solo se il metodo [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) viene chiamato in modo esplicito prima dei metodi [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set) o [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove). Se `LoadAsync` non viene chiamato per primo, il record di sessione sottostante viene caricato in modo sincrono e ciò può compromettere le prestazioni su larga scala.

Per fare in modo che le app implementino questo criterio, eseguire il wrapping delle implementazioni [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) e [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) con versioni che generano un'eccezione se il metodo `LoadAsync` non viene chiamato prima di `TryGetValue`, `Set` o `Remove`. Registrare le versioni con wrapping nel contenitore di servizi.

### <a name="session-options"></a>Opzioni di sessione

Per eseguire l'override delle impostazioni predefinite di sessione, usare [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).

| Opzione | Descrizione |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Determina le impostazioni usate per creare il cookie. Per impostazione predefinita, [Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) è [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`). Per impostazione predefinita, [Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) è [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`). [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) per impostazione predefinita è [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`). Per [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) il valore predefinito è `true`. Per [IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) il valore predefinito è `false`. |
| [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | Il valore `IdleTimeout` indica quanto tempo la sessione può rimanere inattiva prima che il relativo contenuto venga abbandonato. Ogni accesso alla sessione reimposta il timeout. Si noti che questa impostazione vale solo per il contenuto della sessione, non per il cookie. Il valore predefinito è 20 minuti. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | L'intervallo di tempo massimo consentito per caricare una sessione dall'archivio o per eseguirne il commit nell'archivio. Questa impostazione può essere applicabile solo alle operazioni asincrone. Questo timeout può essere disattivato usando [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan). Il valore predefinito è 1 minuto. |

Session usa un cookie per tenere traccia e identificare le richieste provenienti da un solo browser. Per impostazione predefinita, questo cookie è denominato `.AspNetCore.Session` e usa il percorso `/`. Poiché il cookie predefinito non specifica un dominio, non viene reso disponibile allo script lato client nella pagina (perché il valore predefinito di [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) è `true`).

Per eseguire l'override delle impostazioni predefinite della sessione con cookie, usare <xref:Microsoft.AspNetCore.Builder.SessionOptions>:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

L'app usa la proprietà [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) per determinare quanto tempo una sessione può rimanere inattiva prima che il relativo contenuto nella cache del server venga abbandonato. Questa proprietà è indipendente dalla scadenza del cookie. Ogni richiesta che passa attraverso il [middleware di sessione](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) reimposta il timeout.

Lo stato della sessione è *non di blocco*. Se due richieste tentano simultaneamente di modificare il contenuto di una sessione, l'ultima richiesta sostituisce la prima. `Session` viene implementata come *sessione coerente*, ovvero tutto il contenuto viene archiviato insieme. Quando due richieste tentano di modificare diversi valori della sessione, l'ultima richiesta può sostituire le modifiche apportate alla sessione dalla prima.

### <a name="set-and-get-session-values"></a>Impostare e ottenere i valori della sessione

Lo stato della sessione è accessibile da una classe [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) di Razor Pages o una classe [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) di MVC con [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session). Questa proprietà è un'implementazione di [ISession](/dotnet/api/microsoft.aspnetcore.http.isession).

L'implementazione `ISession` offre diversi metodi di estensione per impostare e recuperare i valori interi e stringa. I metodi di estensione si trovano nello spazio dei nomi [Microsoft. AspNetCore. http](/dotnet/api/microsoft.aspnetcore.http) .

Metodi di estensione `ISession`:

* [Get(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [SetInt32(ISession, String, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString(ISession, String, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

Nell'esempio seguente viene recuperato il valore di sessione per la chiave `IndexModel.SessionKeyName` (`_Name` nell'app di esempio) in una pagina di Razor Pages:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

L'esempio seguente illustra come impostare e ottenere un intero e una stringa:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Tutti i dati della sessione devono essere serializzati per abilitare uno scenario di cache distribuita, anche quando si usa la cache in memoria. I serializzatori stringa e integer sono forniti dai metodi di estensione di [ISession](/dotnet/api/microsoft.aspnetcore.http.isession). I tipi complessi devono essere serializzati dall'utente usando un altro meccanismo, ad esempio JSON.

Usare il codice di esempio seguente per serializzare gli oggetti:

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Nell'esempio seguente viene illustrato come impostare e ottenere un oggetto serializzabile con `SessionExtensions` la classe:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core espone l'Razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) o il <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>controller. Questa proprietà archivia i dati finché non viene letta in un'altra richiesta. È possibile usare i metodi [Keep (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) e [Peek (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) per esaminare i dati senza eliminare alla fine della richiesta. [Mantenere](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) i contrassegni per la conservazione di tutti gli elementi nel dizionario. `TempData` è:

* Utile per il reindirizzamento quando i dati sono necessari per più di una singola richiesta.
* Implementata `TempData` dai provider utilizzando cookie o lo stato della sessione.

## <a name="tempdata-samples"></a>Esempi di TempData

Si consideri la seguente pagina che consente di creare un cliente:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Viene visualizzata `TempData["Message"]`la pagina seguente:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Nel markup precedente, alla fine della richiesta, `TempData["Message"]` **non** viene eliminato perché `Peek` viene usato. Con l'aggiornamento della pagina viene visualizzato `TempData["Message"]`il contenuto di.

Il markup seguente è simile al codice precedente, ma usa `Keep` per conservare i dati alla fine della richiesta:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

L'esplorazione tra le pagine *IndexPeek* e *IndexKeep* non verrà `TempData["Message"]`eliminata.

Il codice seguente visualizza `TempData["Message"]`, ma alla fine della richiesta, `TempData["Message"]` viene eliminato:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Provider TempData

Il provider TempData basato su cookie viene usato per impostazione predefinita per archiviare TempData nei cookie.

I dati del cookie vengono crittografati tramite [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), codificati con [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder) e quindi suddivisi in blocchi. Le dimensioni massime dei cookie sono inferiori a [4096 byte](http://www.faqs.org/rfcs/rfc2965.html) a causa della crittografia e della suddivisione in blocchi. I dati del cookie non vengono compressi perché la compressione di dati crittografati può comportare problemi di sicurezza, ad esempio con attacchi [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) e [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)). Per altre informazioni sul provider TempData basato sui cookie, vedere [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

### <a name="choose-a-tempdata-provider"></a>Scegliere un provider TempData

La scelta di un provider TempData implica diverse considerazioni, tra cui:

* L'app utilizza già lo stato sessione? In tal caso, l'uso del provider TempData dello stato della sessione non comporta costi aggiuntivi per l'app oltre le dimensioni dei dati.
* L'app usa TempData solo sporadicamente per quantità di dati relativamente ridotte, fino a 500 byte? Se sì, il provider TempData con cookie aggiunge un piccolo carico di lavoro a ogni richiesta che include TempData. Se no, il provider TempData con stato sessione può essere utile per evitare sequenze di andata e ritorno di grandi quantità di dati in ogni richiesta fino a quando il contenuto TempData non viene consumato.
* L'app viene eseguita in un server farm in più server? Se sì, non è necessaria un'ulteriore configurazione per usare il provider TempData con cookie di fuori della protezione dei dati (vedere <xref:security/data-protection/introduction> e [Provider di archiviazione chiavi](xref:security/data-protection/implementation/key-storage-providers)).

La maggior parte dei client Web, ad esempio i Web browser, applica limiti alla dimensione massima di ogni cookie e al numero totale di cookie. Quando si usa il provider TempData cookie, verificare che l'app non superi [questi limiti](http://www.faqs.org/rfcs/rfc2965.html). Considerare la dimensione totale dei dati. Considerare l'aumento delle dimensioni del cookie dovuto a crittografia e suddivisione in blocchi.

### <a name="configure-the-tempdata-provider"></a>Configurare il provider TempData

Il provider TempData basato su cookie è abilitato per impostazione predefinita.

Per abilitare il provider TempData basato sulla sessione, usare il metodo di estensione [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) . È necessaria una sola `AddSessionStateTempDataProvider` chiamata a:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,30)]

## <a name="query-strings"></a>Stringhe di query

È possibile passare una quantità limitata di dati da una richiesta a un'altra aggiungendo i dati alla stringa di query della nuova richiesta. Questo è utile per l'acquisizione dello stato con una modalità persistente, che consente la condivisione dei collegamenti con stato incorporato tramite posta elettronica o social network. Poiché le stringhe di query dell'URL sono pubbliche, non usare mai le stringhe di query per i dati sensibili.

Oltre alla condivisione non intenzionale, i dati nelle stringhe di query possono esporre l'app a attacchi di [richiesta intersito falsa (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) . Qualsiasi stato della sessione mantenuto deve proteggersi dagli attacchi CSRF. Per altre informazioni, vedere [Prevenire attacchi tramite richieste intersito false (XSRF/CSRF)](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Campi nascosti

I dati possono essere salvati in campi modulo nascosti e pubblicati di nuovo nella richiesta successiva. Questo si verifica spesso nei moduli a più pagine. Poiché il client potenzialmente può alterare i dati, l'app deve sempre ripetere la convalida dei dati archiviati nei campi nascosti.

## <a name="httpcontextitems"></a>HttpContext.Items

La raccolta [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) viene usata per archiviare i dati durante l'elaborazione di una singola richiesta. Il contenuto della raccolta viene rimosso al termine dell'elaborazione della richiesta. La raccolta `Items` spesso viene usata per consentire ai componenti o al middleware di comunicare quando operano in momenti diversi durante una richiesta e non è disponibile un metodo diretto per passare i parametri.

Nell'esempio seguente il `Items` [middleware](xref:fundamentals/middleware/index) aggiunge `isVerified` alla raccolta:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

Per il middleware usato solo in una singola app, le chiavi `string` fisse sono accettabili. Il middleware condiviso tra le app deve usare chiavi di oggetti univoche per evitare conflitti di chiave. L'esempio seguente illustra come usare una chiave oggetto univoca definita in una classe middleware:

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Altri elementi di codice possono accedere al valore archiviato in `HttpContext.Items` usando la chiave esposta dalla classe middleware:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Questo approccio ha anche il vantaggio di eliminare l'uso di stringhe chiave nel codice.

## <a name="cache"></a>Cache

La memorizzazione nella cache è un modo efficiente per archiviare e recuperare dati. L'app può controllare la durata degli elementi della cache. Per altre informazioni, vedere <xref:performance/caching/response>.

I dati memorizzati nella cache non sono associati a una richiesta, un utente o una sessione specifici. **Non memorizzare nella cache i dati specifici dell'utente che possono essere recuperati da altre richieste utente.**

Per memorizzare nella cache i dati a <xref:performance/caching/memory>livello di applicazione, vedere.

## <a name="common-errors"></a>Errori comuni

* "Unable to resolve service for type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' while attempting to activate 'Microsoft.AspNetCore.Session.DistributedSessionStore'." (Risoluzione servizio non riuscita per il tipo 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' durante il tentativo di attivazione di 'Microsoft.AspNetCore.Session.DistributedSessionStore').

  Questo problema si verifica in genere quando si verifica un errore `IDistributedCache` durante la configurazione di almeno un'implementazione. Per altre informazioni, vedere <xref:performance/caching/distributed> e <xref:performance/caching/memory>.

Se il middleware della sessione non è in grado di salvare in modo permanente una sessione:

* Il middleware registra l'eccezione e la richiesta continua normalmente.
* Ciò provoca un comportamento imprevedibile.

Il middleware della sessione può non riuscire a salvare in modo permanente una sessione se l'archivio di backup non è disponibile. Ad esempio, un utente archivia un carrello acquisti nella sessione. L'utente aggiunge un articolo al carrello ma il commit ha esito negativo. L'app non riconosce l'errore e segnala all'utente che l'articolo è stato aggiunto al carrello anche se non è vero.

L'approccio consigliato per verificare la presenza di errori consiste `await feature.Session.CommitAsync` nel chiamare quando l'app esegue la scrittura nella sessione. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*> genera un'eccezione se l'archivio di backup non è disponibile. Se `CommitAsync` ha esito negativo, l'app è in grado di elaborare l'eccezione. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*>genera un'eccezione quando l'archivio dati non è disponibile.
  
## <a name="signalr-and-session-state"></a>SignalR e stato della sessione

Le app SignalR non devono usare lo stato della sessione per archiviare le informazioni. Le app SignalR possono archiviare lo stato per `Context.Items` connessione in nell'hub. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Risorse aggiuntive

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose) e [Luke Latham](https://github.com/guardrex)

HTTP è un protocollo senza stato. Senza eseguire ulteriori passaggi, le richieste HTTP sono messaggi indipendenti che non mantengono i valori dell'utente o lo stato delle app. In questo articolo vengono descritti diversi approcci per mantenere lo stato di app e dati utente tra le richieste.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Gestione dello stato

Lo stato può essere archiviato usando diversi approcci. Ogni approccio è descritto di seguito in questo argomento.

| Approccio con risorsa di archiviazione | Meccanismo di archiviazione |
| ---------------- | ----------------- |
| [Cookie](#cookies) | Cookie HTTP (possono includere dati archiviati usando il codice app lato server) |
| [Stato sessione](#session-state) | Cookie HTTP e codice app lato server |
| [TempData](#tempdata) | Cookie HTTP o stato della sessione |
| [Stringhe di query](#query-strings) | Stringhe di query HTTP |
| [Campi nascosti](#hidden-fields) | Campi dei form HTTP |
| [HttpContext.Items](#httpcontextitems) | Codice app lato server |
| [Cache](#cache) | Codice app lato server |
| [Inserimento delle dipendenze](#dependency-injection) | Codice app lato server |

## <a name="cookies"></a>Cookie

I cookie archiviano i dati tra le richieste. Poiché i cookie vengono inviati con ogni richiesta, la loro dimensione deve essere mantenuta al minimo. Idealmente, in un cookie deve essere archiviato solo un identificatore con i dati archiviati dall'app. La maggior parte dei browser limita la dimensione dei cookie a 4096 byte. Per ogni dominio è disponibile solo un numero limitato di cookie.

Poiché i cookie possono essere manomessi, devono essere convalidati dall'app. I cookie possono essere eliminati dagli utenti e scadono nei client. Tuttavia, i cookie sono in genere la forma più durevole di salvataggio permanente dei dati nel client.

I cookie vengono spesso usati per la personalizzazione, ovvero il contenuto viene personalizzato per un utente noto. L'utente viene solo identificato e non autenticato nella maggior parte dei casi. Nel cookie può essere memorizzato il nome dell'utente, il nome dell'account o l'ID utente univoco, ad esempio un GUID. È quindi possibile usare il cookie per accedere alle impostazioni personalizzate dell'utente, ad esempio il colore di sfondo preferito per il sito Web.

Tenere presente il [Regolamento generale sulla protezione dei dati dell'Unione Europea](https://ec.europa.eu/info/law/law-topic/data-protection) quando si emettono i cookie e si gestisce la protezione dei dati. Per altre informazioni, vedere il [supporto per il Regolamento generale sulla protezione dei dati in ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Stato sessione

Lo stato della sessione è uno scenario di ASP.NET Core per l'archiviazione dei dati utente mentre l'utente visualizza un'app Web. Lo stato della sessione usa un archivio gestito dall'app per rendere persistenti i dati tra le richieste provenienti da un client. I dati della sessione sono supportati da una cache e considerati dati temporanei: il sito deve continuare a funzionare senza i dati della sessione. I dati critici dell'applicazione devono essere archiviati nel database utente e memorizzati nella cache della sessione solo al fine di ottimizzare le prestazioni.

> [!NOTE]
> La sessione non è supportata nelle app [SignalR](xref:signalr/index) poiché un [hub SignalR](xref:signalr/hubs) può essere eseguito indipendentemente da un contesto HTTP. Ad esempio, ciò può verificarsi quando una lunga richiesta di polling viene mantenuta aperta da un hub oltre la durata del contesto HTTP della richiesta.

ASP.NET Core mantiene lo stato della sessione assegnando un cookie al client che contiene un ID sessione. Tale ID viene inviato all'app con ogni richiesta. L'app usa l'ID sessione per recuperare i dati della sessione.

Lo stato della sessione presenta i comportamenti seguenti:

* Dato che il cookie di sessione è specifico per il browser, le sessioni non vengono condivise tra i browser.
* I cookie di sessione vengono eliminati al termine della sessione del browser.
* Se viene ricevuto un cookie per una sessione scaduta, viene creata una nuova sessione che usa lo stesso cookie di sessione.
* Le sessioni vuote non vengono conservate: nella sessione deve essere impostato almeno un valore perché la sessione diventi permanente tra le richieste. Se una sessione non viene conservata, viene generato un nuovo ID sessione per ogni nuova richiesta.
* L'app conserva una sessione per un periodo di tempo limitato dopo l'ultima richiesta. L'app imposta il timeout della sessione o usa il valore predefinito, pari a 20 minuti. Lo stato sessione è ideale per archiviare i dati utente specifici per una determinata sessione, ma nel caso in cui i dati non richiedano un'archiviazione permanente tra le sessioni.
* I dati della sessione vengono eliminati quando viene chiamata l'implementazione [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) o alla scadenza della sessione.
* Non esiste un meccanismo predefinito per indicare al codice app che un browser client è stato chiuso o che il cookie di sessione viene eliminato o è scaduto nel client.
* I modelli di pagina ASP.NET Core MVC e Razor includono il supporto del Regolamento generale sulla protezione dei dati (GDPR). I cookie di stato della sessione non sono contrassegnati come fondamentali per impostazione predefinita, pertanto lo stato della sessione non è funzionale, a meno che il rilevamento non sia consentito dal visitatore del sito. Per altre informazioni, vedere <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Evitare di archiviare dati sensibili nello stato della sessione. L'utente potrebbe non chiudere il browser e cancellare il cookie di sessione. Alcuni browser mantengono i cookie di sessione validi tra diverse finestre del browser. Una sessione può non essere limitata a un solo utente: in tal caso l'utente successivo potrebbe continuare a sfogliare l'app usando lo stesso cookie di sessione.

Il provider di cache in memoria archivia i dati della sessione nella memoria del server in cui si trova l'app. In uno scenario di server farm:

* Usare *sessioni permanenti* per associare ogni sessione a un'istanza specifica dell'app in un singolo server. Il [Servizio app di Azure](https://azure.microsoft.com/services/app-service/) usa il modulo [Application Request Routing (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) per applicare le sessioni permanenti per impostazione predefinita. Tuttavia le sessioni permanenti possono compromettere la scalabilità e complicare gli aggiornamenti delle app Web. Un approccio migliore è usare la cache distribuita Redis o SQL Server, che non richiede sessioni permanenti. Per altre informazioni, vedere <xref:performance/caching/distributed>.
* Il cookie di sessione viene crittografato con [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector). La protezione dati deve essere configurata correttamente in modo da leggere i cookie di sessione in ogni computer. Per altre informazioni, vedere <xref:security/data-protection/introduction> e [Provider di archiviazione chiavi](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Configurare lo stato della sessione

Con il pacchetto [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/), incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), è disponibile il middleware per la gestione dello stato della sessione. Per abilitare il middleware della sessione, `Startup` deve contenere:

* Una delle cache di memoria [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). L'implementazione `IDistributedCache` viene usata come archivio di backup per la sessione. Per altre informazioni, vedere <xref:performance/caching/distributed>.
* Una chiamata ad [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) in `ConfigureServices`.
* Una chiamata a [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) in `Configure`.

Il codice seguente indica come configurare il provider della sessione in memoria con un'implementazione in memoria predefinita di `IDistributedCache`:

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

L'ordine del middleware è importante. Nell'esempio precedente si verifica un'eccezione `InvalidOperationException` se `UseSession` viene chiamata dopo `UseMvc`. Per altre informazioni, vedere la sezione relativa all'[ordine del middleware](xref:fundamentals/middleware/index#order).

[HttpContext. Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) è disponibile dopo che è stato configurato lo stato della sessione.

Non è possibile accedere a `HttpContext.Session` prima di chiamare `UseSession`.

Non è possibile creare un nuovo cookie di sessione dopo che l'app ha iniziato la scrittura nel flusso di risposte. L'eccezione viene registrata nel log del server Web e non è visualizzata nel browser.

### <a name="load-session-state-asynchronously"></a>Caricare lo stato della sessione in modo asincrono

Il provider di sessione predefinito in ASP.NET Core carica in modo asincrono i record di sessione dall'archivio di backup [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) sottostante solo se il metodo [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) viene chiamato in modo esplicito prima dei metodi [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set) o [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove). Se `LoadAsync` non viene chiamato per primo, il record di sessione sottostante viene caricato in modo sincrono e ciò può compromettere le prestazioni su larga scala.

Per fare in modo che le app implementino questo criterio, eseguire il wrapping delle implementazioni [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) e [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) con versioni che generano un'eccezione se il metodo `LoadAsync` non viene chiamato prima di `TryGetValue`, `Set` o `Remove`. Registrare le versioni con wrapping nel contenitore di servizi.

### <a name="session-options"></a>Opzioni di sessione

Per eseguire l'override delle impostazioni predefinite di sessione, usare [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).

| Opzione | Descrizione |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Determina le impostazioni usate per creare il cookie. Per impostazione predefinita, [Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) è [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`). Per impostazione predefinita, [Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) è [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`). [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) per impostazione predefinita è [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`). Per [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) il valore predefinito è `true`. Per [IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) il valore predefinito è `false`. |
| [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | Il valore `IdleTimeout` indica quanto tempo la sessione può rimanere inattiva prima che il relativo contenuto venga abbandonato. Ogni accesso alla sessione reimposta il timeout. Si noti che questa impostazione vale solo per il contenuto della sessione, non per il cookie. Il valore predefinito è 20 minuti. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | L'intervallo di tempo massimo consentito per caricare una sessione dall'archivio o per eseguirne il commit nell'archivio. Questa impostazione può essere applicabile solo alle operazioni asincrone. Questo timeout può essere disattivato usando [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan). Il valore predefinito è 1 minuto. |

Session usa un cookie per tenere traccia e identificare le richieste provenienti da un solo browser. Per impostazione predefinita, questo cookie è denominato `.AspNetCore.Session` e usa il percorso `/`. Poiché il cookie predefinito non specifica un dominio, non viene reso disponibile allo script lato client nella pagina (perché il valore predefinito di [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) è `true`).

Per eseguire l'override delle impostazioni predefinite della sessione con cookie, usare `SessionOptions`:

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

L'app usa la proprietà [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) per determinare quanto tempo una sessione può rimanere inattiva prima che il relativo contenuto nella cache del server venga abbandonato. Questa proprietà è indipendente dalla scadenza del cookie. Ogni richiesta che passa attraverso il [middleware di sessione](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) reimposta il timeout.

Lo stato della sessione è *non di blocco*. Se due richieste tentano simultaneamente di modificare il contenuto di una sessione, l'ultima richiesta sostituisce la prima. `Session` viene implementata come *sessione coerente*, ovvero tutto il contenuto viene archiviato insieme. Quando due richieste tentano di modificare diversi valori della sessione, l'ultima richiesta può sostituire le modifiche apportate alla sessione dalla prima.

### <a name="set-and-get-session-values"></a>Impostare e ottenere i valori della sessione

È possibile accedere allo stato della Razor sessione da una classe pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) o da una classe [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) MVC con [HttpContext. Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session). Questa proprietà è un'implementazione di [ISession](/dotnet/api/microsoft.aspnetcore.http.isession).

L'implementazione `ISession` offre diversi metodi di estensione per impostare e recuperare i valori interi e stringa. I metodi di estensione si trovano nello spazio dei nomi [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) (aggiungere un'istruzione `using Microsoft.AspNetCore.Http;` per ottenere l'accesso ai metodi di estensione) quando al pacchetto [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) fa riferimento il progetto. Entrambi i pacchetti sono inclusi nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Metodi di estensione `ISession`:

* [Get(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [SetInt32(ISession, String, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString(ISession, String, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

L'esempio seguente recupera il valore della sessione per `IndexModel.SessionKeyName` la chiave`_Name` (nell'app di esempio) in Razor una pagina di pagine:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

L'esempio seguente illustra come impostare e ottenere un intero e una stringa:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Tutti i dati della sessione devono essere serializzati per abilitare uno scenario di cache distribuita, anche quando si usa la cache in memoria. I serializzatori stringa e integer sono forniti dai metodi di estensione di [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)). I tipi complessi devono essere serializzati dall'utente usando un altro meccanismo, ad esempio JSON.

Aggiungere i seguenti metodi di estensione per impostare e ottenere oggetti serializzabili:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

L'esempio seguente illustra come impostare e ottenere un oggetto serializzabile con i metodi di estensione:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core espone le Razor pagine [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) o controller <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>. Questa proprietà archivia i dati finché non viene letta in un'altra richiesta. È possibile utilizzare i metodi [Keep (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) e [Peek (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) per esaminare i dati senza eliminarli alla fine della richiesta. [Keep ()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) contrassegna tutti gli elementi nel dizionario per la memorizzazione. `TempData`è particolarmente utile per il reindirizzamento quando i dati sono necessari per più di una singola richiesta. `TempData`viene implementato dai `TempData` provider utilizzando cookie o lo stato della sessione.

## <a name="tempdata-samples"></a>Esempi di TempData

Si consideri la seguente pagina che consente di creare un cliente:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Viene visualizzata `TempData["Message"]`la pagina seguente:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Nel markup precedente, alla fine della richiesta, `TempData["Message"]` **non** viene eliminato perché `Peek` viene usato. Verrà visualizzato `TempData["Message"]`l'aggiornamento della pagina.

Il markup seguente è simile al codice precedente, ma usa `Keep` per conservare i dati alla fine della richiesta:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

L'esplorazione tra le pagine *IndexPeek* e *IndexKeep* non verrà `TempData["Message"]`eliminata.

Il codice seguente visualizza `TempData["Message"]`, ma alla fine della richiesta, `TempData["Message"]` viene eliminato:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Provider TempData

Il provider TempData basato su cookie viene usato per impostazione predefinita per archiviare TempData nei cookie.

I dati del cookie vengono crittografati tramite [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), codificati con [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder) e quindi suddivisi in blocchi. Dato che il cookie è suddiviso in blocchi, il limite di dimensione per un singolo cookie di ASP.NET Core 1.x non è applicabile. I dati del cookie non vengono compressi perché la compressione di dati crittografati può comportare problemi di sicurezza, ad esempio con attacchi [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) e [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)). Per altre informazioni sul provider TempData basato sui cookie, vedere [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

### <a name="choose-a-tempdata-provider"></a>Scegliere un provider TempData

La scelta di un provider TempData implica diverse considerazioni, tra cui:

1. L'app utilizza già lo stato sessione? Se sì, l'uso del provider TempData con stato sessione non comporta un carico aggiuntivo per l'app (indipendentemente dalla dimensione dei dati).
2. L'app usa TempData solo quando necessario e per quantità di dati relativamente piccole (fino a 500 byte)? Se sì, il provider TempData con cookie aggiunge un piccolo carico di lavoro a ogni richiesta che include TempData. Se no, il provider TempData con stato sessione può essere utile per evitare sequenze di andata e ritorno di grandi quantità di dati in ogni richiesta fino a quando il contenuto TempData non viene consumato.
3. L'app viene eseguita in un server farm in più server? Se sì, non è necessaria un'ulteriore configurazione per usare il provider TempData con cookie di fuori della protezione dei dati (vedere <xref:security/data-protection/introduction> e [Provider di archiviazione chiavi](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> La maggior parte dei client Web (ad esempio i Web browser) impone limiti per la dimensione massima di ogni cookie, il numero totale di cookie o entrambe le impostazioni. Se si usa il provider TempData con cookie, verificare che l'app non superi tali limiti. Considerare la dimensione totale dei dati. Considerare l'aumento delle dimensioni del cookie dovuto a crittografia e suddivisione in blocchi.

### <a name="configure-the-tempdata-provider"></a>Configurare il provider TempData

Il provider TempData basato su cookie è abilitato per impostazione predefinita.

Per abilitare il provider TempData basato sulla sessione, usare il metodo di estensione [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider):

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

L'ordine del middleware è importante. Nell'esempio precedente si verifica un'eccezione `InvalidOperationException` se `UseSession` viene chiamata dopo `UseMvc`. Per altre informazioni, vedere la sezione relativa all'[ordine del middleware](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Se la destinazione è .NET Framework e si usa il provider TempData basato sulla sessione, aggiungere il pacchetto [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) al progetto.

## <a name="query-strings"></a>Stringhe di query

È possibile passare una quantità limitata di dati da una richiesta a un'altra aggiungendo i dati alla stringa di query della nuova richiesta. Questo è utile per l'acquisizione dello stato con una modalità persistente, che consente la condivisione dei collegamenti con stato incorporato tramite posta elettronica o social network. Poiché le stringhe di query dell'URL sono pubbliche, non usare mai le stringhe di query per i dati sensibili.

OItre alle condivisioni involontarie, i dati presenti nelle stringhe di query possono essere oggetto di attacchi di [falsificazione della richiesta tra siti (CSRF, Cross-Site Request Forgery)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)), che ingannano gli utenti autenticati invitandoli a visitare siti pericolosi. Gli utenti malintenzionati possono quindi sottrarre dati utente dall'app o eseguire azioni dannose per conto dell'utente. Qualsiasi stato dell'app o della sessione mantenuto deve garantire la protezione dagli attacchi CSRF. Per altre informazioni, vedere [Prevenire attacchi tramite richieste intersito false (XSRF/CSRF)](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Campi nascosti

I dati possono essere salvati in campi modulo nascosti e pubblicati di nuovo nella richiesta successiva. Questo si verifica spesso nei moduli a più pagine. Poiché il client potenzialmente può alterare i dati, l'app deve sempre ripetere la convalida dei dati archiviati nei campi nascosti.

## <a name="httpcontextitems"></a>HttpContext.Items

La raccolta [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) viene usata per archiviare i dati durante l'elaborazione di una singola richiesta. Il contenuto della raccolta viene rimosso al termine dell'elaborazione della richiesta. La raccolta `Items` spesso viene usata per consentire ai componenti o al middleware di comunicare quando operano in momenti diversi durante una richiesta e non è disponibile un metodo diretto per passare i parametri.

Nell'esempio seguente [middleware](xref:fundamentals/middleware/index) aggiunge `isVerified` alla raccolta `Items`.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

In punto successivo della pipeline un altro middleware può accedere al valore di `isVerified`:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Per il middleware che viene usato da un'unica app sono accettabili le chiavi `string`. Il middleware condiviso tra le istanze dell'app deve usare chiavi oggetto univoche per evitare conflitti di chiavi. L'esempio seguente illustra come usare una chiave oggetto univoca definita in una classe middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Altri elementi di codice possono accedere al valore archiviato in `HttpContext.Items` usando la chiave esposta dalla classe middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Questo approccio ha anche il vantaggio di eliminare l'uso di stringhe chiave nel codice.

## <a name="cache"></a>Cache

La memorizzazione nella cache è un modo efficiente per archiviare e recuperare dati. L'app può controllare la durata degli elementi della cache.

I dati memorizzati nella cache non sono associati a una richiesta, un utente o una sessione specifici. **Prestare attenzione a non memorizzare nella cache i dati specifici dell'utente che possono essere recuperati dalle richieste di altri utenti.**

Per altre informazioni, vedere <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Inserimento di dipendenze

Usare [Dependency Injection](xref:fundamentals/dependency-injection) (Inserimento di dipendenze) per rendere i dati disponibili a tutti gli utenti:

1. Definire un servizio che contiene i dati. Ad esempio, una classe denominata `MyAppData` è definita come segue:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Aggiungere la classe del servizio a `Startup.ConfigureServices`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Usare la classe del servizio dati:

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>Errori comuni

* "Unable to resolve service for type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' while attempting to activate 'Microsoft.AspNetCore.Session.DistributedSessionStore'." (Risoluzione servizio non riuscita per il tipo 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' durante il tentativo di attivazione di 'Microsoft.AspNetCore.Session.DistributedSessionStore').

  Questo errore dipende in genere dal fatto che almeno un'implementazione `IDistributedCache` non è stata configurata. Per altre informazioni, vedere <xref:performance/caching/distributed> e <xref:performance/caching/memory>.

* Se il middleware di sessione non riesce a rendere permanente una sessione (ad esempio se l'archivio di backup non è disponibile), registra l'eccezione e la richiesta continua normalmente. Ciò provoca un comportamento imprevedibile.

  Ad esempio, un utente archivia un carrello acquisti nella sessione. L'utente aggiunge un articolo al carrello ma il commit ha esito negativo. L'app non riconosce l'errore e segnala all'utente che l'articolo è stato aggiunto al carrello anche se non è vero.

  L'approccio consigliato per verificare la presenza di errori è chiamare `await feature.Session.CommitAsync();` dal codice dell'app quando l'app termina di scrivere nella sessione. `CommitAsync` genera un'eccezione se l'archivio di backup non è disponibile. Se `CommitAsync` ha esito negativo, l'app è in grado di elaborare l'eccezione. `LoadAsync` viene generata nelle stesse condizioni in cui l'archivio dati non è disponibile.
  
## <a name="signalr-and-session-state"></a>SignalRe stato sessione

SignalRle app non devono usare lo stato della sessione per archiviare le informazioni. SignalRle app possono archiviare lo stato per `Context.Items` connessione in nell'hub. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Risorse aggiuntive

<xref:host-and-deploy/web-farm>
::: moniker-end
