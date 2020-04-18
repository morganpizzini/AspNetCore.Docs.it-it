---
title: Abilitare le richieste cross-origin (CORS) in ASP.NET CoreEnable Cross-Origin Requests (CORS) in ASP.NET Core
author: rick-anderson
description: Scopri come CORS come standard per consentire o rifiutare le richieste tra origini in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642692"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Abilitare le richieste cross-origin (CORS) in ASP.NET CoreEnable Cross-Origin Requests (CORS) in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Di [Rick Anderson](https://twitter.com/RickAndMSFT) e Kirk [Larkin](https://twitter.com/serpent5)

Questo articolo illustra come abilitare CORS in un'app ASP.NET Core.

La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina web. Questa restrizione è *denominata criterio*della stessa origine . I criteri della stessa origine impediscono a un sito dannoso di leggere dati sensibili da un altro sito. In alcuni alcuni punti, potresti voler consentire ad altri siti di effettuare richieste cross-origin alla tua app. Per ulteriori informazioni, vedere l'articolo di [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Condivisione delle risorse tra origini](https://www.w3.org/TR/cors/) (CORS):

* È uno standard W3C che consente a un server di ridurre la stessa origine.
* **Non** è una funzione di sicurezza, CORS rilassa la sicurezza. Un'API non è più sicura consentendo CORS. Per ulteriori informazioni, consultate [Funzionamento di CORS.](#how-cors)
* Consente a un server di consentire in modo esplicito alcune richieste tra origini rifiutandone altre.
* È più sicuro e più flessibile rispetto alle tecniche precedenti, ad esempio [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Stessa origine

Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Questi due URL hanno la stessa origine:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Questi URL hanno origini diverse rispetto ai due URL precedenti:

* `https://example.net`&ndash; Dominio diverso
* `https://www.example.com/foo.html`&ndash; Sottodominio diverso
* `http://example.com/foo.html`&ndash; Schema diverso
* `https://example.com:9000/foo.html`&ndash; Porta diversa

## <a name="enable-cors"></a>Abilitare CORS

Esistono tre modi per abilitare CORS:

* Nel middleware che utilizza un [criterio denominato](#np) o [predefinito](#dp).
* Utilizzo del [routing endpoint](#ecors).
* Con l'attributo [[EnableCors].](#attr)

L'utilizzo dell'attributo [[EnableCors]](#attr) con un criterio denominato fornisce il controllo più condizionato per limitare gli endpoint che supportano CORS.

Ogni approccio è descritto in dettaglio nelle sezioni seguenti.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS con criteri denominati e middleware

IL middleware CORS gestisce le richieste tra origini. Il codice seguente applica criteri CORS a tutti gli endpoint dell'app con le origini specificate:The following code applies a CORS policy to all the app's endpoints with the specified origins:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

Il codice precedente:

* Imposta il nome `_myAllowSpecificOrigins`del criterio su . Il nome del criterio è arbitrario.
* Chiama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> il metodo di `_myAllowSpecificOrigins` estensione e specifica il criterio CORS. `UseCors`aggiunge il middleware CORS. La chiamata `UseCors` a deve `UseRouting`essere `UseAuthorization`effettuata dopo , ma prima di . Per ulteriori informazioni, vedere [Ordine del middleware](xref:fundamentals/middleware/index#middleware-order).
* Chiamate <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con [un'espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). L'espressione <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> lambda accetta un oggetto. [Le opzioni](#cors-policy-options)di `WithOrigins`configurazione, ad esempio , sono descritte più avanti in questo articolo.
* Abilita `_myAllowSpecificOrigins` i criteri CORS per tutti gli endpoint del controller. Vedere [routing degli endpoint](#ecors) per applicare criteri CORS a endpoint specifici.

Con il routing degli endpoint, il middleware CORS `UseRouting` `UseEndpoints` ***deve*** essere configurato per l'esecuzione tra le chiamate a e .

Vedere [Test CORS](#testc) per istruzioni sul test di codice simile al codice precedente.

La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chiamata al metodo aggiunge servizi CORS al contenitore dei servizi dell'app:The method call adds CORS services to the app's service container:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Per ulteriori informazioni, vedere [Opzioni dei criteri CORS](#cpo) in questo documento.

I <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> metodi possono essere concatenati, come illustrato nel codice seguente:The methods can be chained, as shown in the following code:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Nota: l'URL specificato **non** deve`/`contenere una barra finale ( ). Se l'URL `/`termina con `false` , il confronto restituisce e non viene restituita alcuna intestazione.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS con criteri e middleware predefiniti

Il codice evidenziato seguente abilita il criterio CORS predefinito:The following highlighted code enables the default CORS policy:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Il codice precedente applica i criteri CORS predefiniti a tutti gli endpoint del controller.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Abilitare Cors con routing endpoint

L'abilitazione di CORS `RequireCors` in base all'endpoint che utilizza attualmente ***non*** supporta le richieste automatiche di [verifica preliminare.](#apf) Per ulteriori informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/aspnetcore/issues/20709) e [Test corS con routing degli endpoint e [HttpOptions]](#tcer).

Con il routing degli endpoint, CORS può essere <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> abilitato in base all'endpoint usando il set di metodi di estensione:With endpoint routing, CORS can be enabled on a per-endpoint basis using the set of extension methods:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

Nel codice precedente:

* `app.UseCors`abilita il middleware CORS. Poiché un criterio predefinito non `app.UseCors()` è stato configurato, da solo non abilita CORS.
* Gli `/echo` endpoint e del controller consentono le richieste tra origini utilizzando i criteri specificati.
* Gli `/echo2` endpoint e Razor Pages ***non*** consentono richieste tra origini perché non è stato specificato alcun criterio predefinito.

L'attributo [[DisableCors]](#dc) ***non*** disabilita CORS abilitato `RequireCors`dal routing degli endpoint con .

Vedere [Test CORS con routing degli endpoint e [HttpOptions]](#tcer) per istruzioni sul test di codice simile al precedente.

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Abilitare CORS con attributi

L'abilitazione di CORS con l'attributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) e l'applicazione di un criterio denominato solo agli endpoint che richiedono CORS forniscono il controllo migliore.

L'attributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornisce un'alternativa all'applicazione di CORS a livello globale. L'attributo `[EnableCors]` abilita CORS per gli endpoint selezionati, anziché per tutti gli endpoint:The attribute enables CORS for selected endpoints, rather than all endpoints:

* `[EnableCors]`specifica il criterio predefinito.
* `[EnableCors("{Policy String}")]`specifica un criterio denominato.

L'attributo `[EnableCors]` può essere applicato a:

* Pagina Razor`PageModel`
* Controller
* Metodo di azione del controller

È possibile applicare criteri diversi ai controller, `[EnableCors]` ai modelli di pagina o ai metodi di azione con l'attributo. Quando `[EnableCors]` l'attributo viene applicato a un controller, a un modello di pagina o a un metodo di azione e CORS è abilitato nel middleware, vengono applicati ***entrambi i*** criteri. ***Si consiglia di non combinare le politiche. Usa l'attributo*** `[EnableCors]` o il ***middleware, non entrambi nella stessa app.***

Il codice seguente applica criteri diversi a ogni metodo:The following code applies a different policy to each method:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Il codice seguente crea due criteri CORS:The following code creates two CORS policies:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Per il miglior controllo della limitazione delle richieste CORS:

* Utilizzare `[EnableCors("MyPolicy")]` con un criterio denominato.
* Non definire un criterio predefinito.
* Non utilizzare il [routing degli endpoint.](#ecors)

Il codice nella sezione successiva incontra l'elenco precedente.

Vedere [Test CORS](#testc) per istruzioni sul test di codice simile al codice precedente.

<a name="dc"></a>

### <a name="disable-cors"></a>Disabilita CORS

L'attributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***non*** disabilita CORS abilitato dal [routing degli endpoint.](#ecors)

Il codice seguente definisce `"MyPolicy"`i criteri CORS :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Il codice seguente disabilita CORS per l'azione: `GetValues2`

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Il codice precedente:

* Non abilita CORS con [routing endpoint](#ecors).
* Non definisce un [criterio CORS predefinito.](#dp)
* Utilizza [[EnableCors("MyPolicy")]](#attr) per `"MyPolicy"` abilitare i criteri CORS per il controller.
* Disabilita CORS per `GetValues2` il metodo.

Vedere [Test CORS](#testc) per istruzioni sul test del codice precedente.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opzioni dei criteri CORS

In questa sezione vengono descritte le varie opzioni che è possibile impostare in un criterio CORS:

* [Impostare le origini consentite](#set-the-allowed-origins)
* [Impostare i metodi HTTP consentiti](#set-the-allowed-http-methods)
* [Impostare le intestazioni di richiesta consentite](#set-the-allowed-request-headers)
* [Impostare le intestazioni delle risposte esposte](#set-the-exposed-response-headers)
* [Credenziali nelle richieste tra originiCredentials in cross-origin requests](#credentials-in-cross-origin-requests)
* [Impostare l'ora di scadenza preliminare](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>viene chiamato `Startup.ConfigureServices`in . Per alcune opzioni, può essere utile leggere prima la sezione [Come CORS funziona.](#how-cors)

## <a name="set-the-allowed-origins"></a>Impostare le origini consentite

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Consente le richieste CORS da tutte`http` `https`le origini con qualsiasi schema ( o ). `AllowAnyOrigin`non è sicuro perché *qualsiasi sito Web* può effettuare richieste cross-origin all'app.

> [!NOTE]
> Specificando `AllowAnyOrigin` ed `AllowCredentials` è una configurazione non sicura e può comportare la falsificazione di richieste intersito. Il servizio CORS restituisce una risposta CORS non valida quando un'app è configurata con entrambi i metodi.

`AllowAnyOrigin`influisce sulle richieste `Access-Control-Allow-Origin` di verifica preliminare e sull'intestazione. Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Imposta <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la proprietà del criterio in modo che sia una funzione che consente alle origini di corrispondere a un dominio con caratteri jolly configurato durante la valutazione se l'origine è consentita.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Impostare i metodi HTTP consentiti

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Consente qualsiasi metodo HTTP:
* Influisce sulle richieste `Access-Control-Allow-Methods` di verifica preliminare e sull'intestazione. Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Impostare le intestazioni di richiesta consentite

Per consentire l'invio di intestazioni specifiche in una richiesta <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, denominate intestazioni di richiesta [author](https://xhr.spec.whatwg.org/#request), chiamare e specificare le intestazioni consentite:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Per consentire tutte le <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>intestazioni di [richiesta dell'autore,](https://www.w3.org/TR/cors/#author-request-headers)chiamare :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`influisce sulle richieste di verifica preliminare e sull'intestazione [Access-Control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)

Un criterio del middleware CORS `WithHeaders` corrispondente a intestazioni specifiche `Access-Control-Request-Headers` specificate da è `WithHeaders`possibile solo quando le intestazioni inviate corrispondono esattamente alle intestazioni indicate in .

Ad esempio, si consideri un'app configurata come segue:For example, consider an app configured as follows:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

IL middleware CORS rifiuta una richiesta di `Content-Language` verifica preliminare con la seguente intestazione di richiesta perché ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) non è elencato in `WithHeaders`:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

L'app restituisce una risposta *200 OK* ma non restituisce le intestazioni CORS. Pertanto, il browser non tenta la richiesta cross-origin.

### <a name="set-the-exposed-response-headers"></a>Impostare le intestazioni delle risposte esposte

Per impostazione predefinita, il browser non espone tutte le intestazioni di risposta all'app. Per ulteriori informazioni, vedere [W3C Cross-Origin Resource Sharing (Terminologia): Intestazione di risposta semplice](https://www.w3.org/TR/cors/#simple-response-header).

Le intestazioni di risposta disponibili per impostazione predefinita sono:The response headers that are available by default are:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

La specifica CORS chiama queste intestazioni intestazioni di *risposta semplici*. Per rendere disponibili altre intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>per l'app, chiamare :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Credenziali nelle richieste tra originiCredentials in cross-origin requests

Le credenziali richiedono una gestione speciale in una richiesta CORS. Per impostazione predefinita, il browser non invia credenziali con una richiesta cross-origin. Le credenziali includono cookie e schemi di autenticazione HTTP. Per inviare le credenziali con una richiesta `XMLHttpRequest.withCredentials` tra `true`origini diverse, il client deve impostare su .

Utilizzando `XMLHttpRequest` direttamente:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Utilizzo di jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Utilizzo [dell'API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Il server deve consentire le credenziali. Per consentire le credenziali di <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>origine incrociata, chiamare :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

La risposta HTTP `Access-Control-Allow-Credentials` include un'intestazione, che indica al browser che il server consente le credenziali per una richiesta tra origini diverse.

Se il browser invia le credenziali ma la `Access-Control-Allow-Credentials` risposta non include un'intestazione valida, il browser non espone la risposta all'app e la richiesta tra origini ha esito negativo.

Consentire credenziali tra origini è un rischio per la sicurezza. Un sito Web in un altro dominio può inviare le credenziali di un utente connesso all'app per conto dell'utente all'insaputa dell'utente. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

La specifica CORS indica inoltre `"*"` che l'impostazione delle `Access-Control-Allow-Credentials` origini (tutte le origini) non è valida se l'intestazione è presente.

<a name="pref"></a>

## <a name="preflight-requests"></a>Richieste di verifica preliminare

Per alcune richieste CORS, il browser invia una richiesta [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) aggiuntiva prima di effettuare la richiesta effettiva. Questa richiesta viene definita [richiesta di verifica preliminare.](https://developer.mozilla.org/docs/Glossary/Preflight_request) Il browser può ignorare la richiesta di verifica preliminare se ***tutte*** le seguenti condizioni sono vere:

* Il metodo di richiesta è GET, HEAD o POST.
* L'app non imposta intestazioni `Accept`di `Accept-Language` `Content-Language`richiesta `Content-Type`diverse `Last-Event-ID`da , , , o .
* L'intestazione, `Content-Type` se impostata, ha uno dei seguenti valori:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

La regola sulle intestazioni di richiesta impostate per la `setRequestHeader` richiesta `XMLHttpRequest` client si applica alle intestazioni impostate dall'app chiamando sull'oggetto. La specifica CORS chiama queste intestazioni [intestazioni di richiesta dell'autore.](https://www.w3.org/TR/cors/#author-request-headers) La regola non si applica alle intestazioni che `User-Agent` `Host`il `Content-Length`browser può impostare, ad esempio , o .

Di seguito è riportata una risposta di esempio simile alla richiesta di verifica preliminare effettuata dal pulsante **[Put test]** nella sezione [Test CORS](#testc) di questo documento.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

La richiesta di verifica preliminare utilizza il metodo [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) Può includere le seguenti intestazioni:

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): il metodo HTTP che verrà utilizzato per la richiesta effettiva.
* [Access-Control-Request-Headers:](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)elenco di intestazioni di richiesta impostate dall'app nella richiesta effettiva. Come indicato in precedenza, non sono incluse le `User-Agent`intestazioni impostate dal browser, ad esempio .
* [Access-Control-Allow-Methods](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Se la richiesta di verifica preliminare `200 OK` viene negata, l'app restituisce una risposta ma non imposta le intestazioni CORS. Pertanto, il browser non tenta la richiesta cross-origin. Per un esempio di richiesta di verifica preliminare negata, vedere la sezione [Test CORS](#testc) di questo documento.

Utilizzando gli strumenti F12, l'applicazione console mostra un errore simile a uno dei seguenti, a seconda del browser:

* Firefox: Richiesta cross-origin bloccata: la stessa politica di `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`origine non consente la lettura della risorsa remota all'indirizzo . (Motivo: richiesta CORS non riuscita). [Altre informazioni](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Basato su chromium:https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5l'accessohttps://cors3.azurewebsites.netal recupero a ' dall'origine ' è stato bloccato dai criteri CORS: la risposta alla richiesta di verifica preliminare non supera il controllo di accesso: non è presente alcuna intestazione 'Access-Control-Allow-Origin' sulla risorsa richiesta. Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.

Per consentire intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>specifiche, chiamare :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Per consentire tutte le <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>intestazioni di [richiesta dell'autore,](https://www.w3.org/TR/cors/#author-request-headers)chiamare :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

I browser non sono coerenti `Access-Control-Request-Headers`nel modo in cui impostano . Se uno dei due:

* Le intestazioni sono impostate su un valore diverso da`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>è chiamato: includere `Accept` `Content-Type`almeno `Origin`, e , oltre a tutte le intestazioni personalizzate che si desidera supportare.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Codice di richiesta di verifica preliminare automatica

Quando viene applicato il criterio CORS:

* Globalmente chiamando `app.UseCors` `Startup.Configure`in .
* Utilizzo `[EnableCors]` dell'attributo .

ASP.NET Core risponde alla richiesta OPTIONS di verifica preliminare.

L'abilitazione di CORS `RequireCors` in base all'endpoint ***non*** supporta attualmente le richieste di verifica preliminare automatiche.

La sezione [Test CORS](#testc) di questo documento illustra questo comportamento.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>Attributo [HttpOptions] per le richieste di verifica preliminare

Quando CORS è abilitato con il criterio appropriato, ASP.NET Core risponde in genere automaticamente alle richieste di verifica preliminare CORS. In alcuni scenari, questo potrebbe non essere il caso. Ad esempio, utilizzando [CORS con il routing degli endpoint](#ecors).

Il codice seguente usa l'attributo [HttpOptions] per creare endpoint per le richieste OPTIONS:The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Vedere [Test CORS con routing degli endpoint e [HttpOptions]](#tcer) per istruzioni sul test del codice precedente.

### <a name="set-the-preflight-expiration-time"></a>Impostare l'ora di scadenza preliminare

L'intestazione `Access-Control-Max-Age` specifica per quanto tempo la risposta alla richiesta di verifica preliminare può essere memorizzata nella cache. Per impostare questa <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>intestazione, chiamare :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Come funziona CORS

In questa sezione viene descritto cosa accade in una richiesta [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) a livello dei messaggi HTTP.

* CORS **non** è una funzione di sicurezza. CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine.
  * Ad esempio, un attore malintenzionato potrebbe utilizzare [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) sul tuo sito ed eseguire una richiesta intersito al loro sito abilitato CORS per rubare informazioni.
* Un'API non è più sicura consentendo CORS.
  * Spetta al client (browser) applicare CORS. Il server esegue la richiesta e restituisce la risposta, è il client che restituisce un errore e blocca la risposta. Ad esempio, uno dei seguenti strumenti visualizzerà la risposta del server:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Un browser Web immettendo l'URL nella barra degli indirizzi.
* È un modo per un server per consentire ai browser di eseguire una richiesta [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) tra origini che altrimenti sarebbe vietata.
  * I browser senza CORS non possono eseguire richieste cross-origin. Prima di CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) veniva usato per aggirare questa restrizione. JSONP non usa XHR, usa `<script>` il tag per ricevere la risposta. Gli script possono essere caricati tra origini diverse.

La [specifica CORS](https://www.w3.org/TR/cors/) ha introdotto diverse nuove intestazioni HTTP che abilitano le richieste tra origini diverse. Se un browser supporta CORS, imposta automaticamente queste intestazioni per le richieste tra origini diverse. Il codice JavaScript personalizzato non è necessario per abilitare CORS.

Il [pulsante test PUT](https://cors3.azurewebsites.net/test) nell'esempio distribuito [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)

Di seguito è riportato un esempio [Values](https://cors3.azurewebsites.net/) di richiesta `https://cors1.azurewebsites.net/api/values`tra origini dal pulsante di test Valori a . L'intestazione: `Origin`

* Fornisce il dominio del sito che effettua la richiesta.
* È obbligatorio e deve essere diverso dall'host.

**Intestazioni generali**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Intestazioni di risposta**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**Intestazioni della richiesta**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

Nelle `OPTIONS` richieste, il server imposta l'intestazione **di intestazioni** `Access-Control-Allow-Origin: {allowed origin}` di risposta nella risposta. Ad esempio, la richiesta del pulsante `OPTIONS` [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) di [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)distribuita contiene le intestazioni seguenti:

**Intestazioni generali**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Intestazioni di risposta**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**Intestazioni della richiesta**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

Nelle intestazioni **Di risposta**precedenti, il server imposta l'intestazione [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) nella risposta. Il `https://cors1.azurewebsites.net` valore di questa `Origin` intestazione corrisponde all'intestazione della richiesta.

Se <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> viene chiamato, viene restituito il `Access-Control-Allow-Origin: *`valore con caratteri jolly . `AllowAnyOrigin`permette qualsiasi origine.

Se la risposta non `Access-Control-Allow-Origin` include l'intestazione, la richiesta cross-origin ha esito negativo. In particolare, il browser non consente la richiesta. Anche se il server restituisce una risposta corretta, il browser non rende disponibile la risposta all'app client.

<a name="options"></a>

### <a name="display-options-requests"></a>Visualizza richieste OPTIONS

Per impostazione predefinita, i browser Chrome ed Edge non mostrano le richieste OPTIONS nella scheda rete degli strumenti F12. Per visualizzare le richieste OPTIONS in questi browser:

* `chrome://flags/#out-of-blink-cors` o `edge://flags/#out-of-blink-cors`
* disattivare il flag.
* Riavvia.

Firefox mostra le richieste OPTIONS per impostazione predefinita.

## <a name="cors-in-iis"></a>CORS in IIS

Durante la distribuzione in IIS, CORS deve essere eseguito prima dell'autenticazione di Windows se il server non è configurato per consentire l'accesso anonimo. Per supportare questo scenario, il [modulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) deve essere installato e configurato per l'app.

<a name="testc"></a>

## <a name="test-cors"></a>Testare CORS

Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) include codice per testare CORS. Vedere [come scaricare un esempio](xref:index#how-to-download-a-sample). L'esempio è un progetto API con pagine Razor aggiunte:The sample is an API project with Razor Pages added:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`deve essere usato solo per testare un'app di esempio simile al [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)scaricato.

Di `ValuesController` seguito sono disponibili gli endpoint per il test:The following provides the endpoints for testing:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) viene fornito dal pacchetto [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet e visualizza le informazioni sulla route.

Testare il codice di esempio precedente utilizzando uno degli approcci seguenti:Test the preceding sample code by using one of the following approaches:

* Usare l'app [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)di esempio distribuita in . Non è necessario scaricare l'esempio.
* Eseguire l'esempio utilizzando `dotnet run` l'URL predefinito di `https://localhost:5001`.
* Eseguire l'esempio da Visual Studio con la porta impostata `https://localhost:44398`su 44398 per un URL di .

Utilizzando un browser con gli strumenti F12:

* Selezionare il pulsante **Valori** ed esaminare le intestazioni nella scheda **Rete.**
* Selezionare il pulsante **TEST PUT.** Vedere [Visualizzare le richieste OPTIONS](#options) per istruzioni sulla visualizzazione della richiesta OPTIONS. Il **test PUT** crea due richieste, una richiesta di verifica preliminare OPTIONS e la richiesta PUT.
* Selezionare **`GetValues2 [DisableCors]`** il pulsante per attivare una richiesta CORS non riuscita. Come accennato nel documento, la risposta restituisce 200 successo, ma la richiesta CORS non viene effettuata. Selezionare la scheda **Console** per visualizzare l'errore CORS. A seconda del browser, viene visualizzato un errore simile al seguente:

     L'accesso `'https://cors1.azurewebsites.net/api/values/GetValues2'` per `'https://cors3.azurewebsites.net'` recuperare dall'origine è stato bloccato dai criteri CORS: non è presente alcuna intestazione 'Access-Control-Allow-Origin' sulla risorsa richiesta. Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.
     
Gli endpoint abilitati per CORS possono essere testati con uno strumento, ad esempio [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)o [Postman](https://www.getpostman.com/). Quando si utilizza uno strumento, l'origine della richiesta specificata dall'intestazione `Origin` deve essere diversa dall'host che riceve la richiesta. Se la richiesta non è *di origine incrociata* in base al valore dell'intestazione: `Origin`

* Non è necessario che il middleware CORS elabori la richiesta.
* Le intestazioni CORS non vengono restituite nella risposta.

Il comando `curl` seguente utilizza per inviare una richiesta OPTIONS con informazioni:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Test corS con routing degli endpoint e [HttpOptions]

L'abilitazione di CORS `RequireCors` in base all'endpoint che utilizza attualmente ***non*** supporta le richieste automatiche di [verifica preliminare.](#apf) Si consideri il codice seguente che utilizza il [routing degli endpoint per abilitare CORS:](#ecors)

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

Di `TodoItems1Controller` seguito sono disponibili gli endpoint per il test:The following provides endpoints for testing:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Testare il codice precedente dalla pagina di [test](https://cors1.azurewebsites.net/test?number=1) [dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)distribuito.

I pulsanti **Elimina [EnableCors]** e **GET [EnableCors]** hanno esito positivo, perché gli endpoint hanno `[EnableCors]` e rispondono alle richieste di verifica preliminare. Gli altri endpoint hanno esito negativo. Il pulsante **GET** ha esito negativo perché [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) invia:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Di `TodoItems2Controller` seguito vengono forniti endpoint simili, ma viene fornito codice esplicito per rispondere alle richieste OPTIONS:The following provides similar endpoints, but includes explicit code to respond to OPTIONS requests:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Testare il codice precedente dalla pagina di [test](https://cors1.azurewebsites.net/test?number=2) dell'esempio distribuito. Nell'elenco a discesa **Controller** selezionare **Verifica preliminare** e quindi **Imposta controller**. Tutte le chiamate CORS agli `TodoItems2Controller` endpoint hanno esito positivo.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Condivisione risorse tra le origini (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Introduzione al modulo IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

Questo articolo illustra come abilitare CORS in un'app ASP.NET Core.

La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina web. Questa restrizione è *denominata criterio*della stessa origine . I criteri della stessa origine impediscono a un sito dannoso di leggere dati sensibili da un altro sito. In alcuni altri punti, potresti voler consentire ad altri siti di effettuare richieste cross-origin alla tua app. Per ulteriori informazioni, vedere l'articolo di [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Condivisione delle risorse tra origini](https://www.w3.org/TR/cors/) (CORS):

* È uno standard W3C che consente a un server di ridurre la stessa origine.
* **Non** è una funzione di sicurezza, CORS rilassa la sicurezza. Un'API non è più sicura consentendo CORS. Per ulteriori informazioni, consultate [Funzionamento di CORS.](#how-cors)
* Consente a un server di consentire in modo esplicito alcune richieste tra origini rifiutandone altre.
* È più sicuro e più flessibile rispetto alle tecniche precedenti, ad esempio [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Stessa origine

Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Questi due URL hanno la stessa origine:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Questi URL hanno origini diverse rispetto ai due URL precedenti:

* `https://example.net`&ndash; Dominio diverso
* `https://www.example.com/foo.html`&ndash; Sottodominio diverso
* `http://example.com/foo.html`&ndash; Schema diverso
* `https://example.com:9000/foo.html`&ndash; Porta diversa

Internet Explorer non considera la porta quando si confrontano le origini.

## <a name="cors-with-named-policy-and-middleware"></a>CORS con criteri denominati e middleware

IL middleware CORS gestisce le richieste tra origini. Il codice seguente abilita CORS per l'intera app con l'origine specificata:The following code enables CORS for the entire app with the specified origin:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Il codice precedente:

* Imposta il nome\_del criterio su " myAllowSpecificOrigins". Il nome del criterio è arbitrario.
* Chiama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> il metodo di estensione, che abilita CORS.
* Chiamate <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con [un'espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). L'espressione <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> lambda accetta un oggetto. [Le opzioni](#cors-policy-options)di `WithOrigins`configurazione, ad esempio , sono descritte più avanti in questo articolo.

La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chiamata al metodo aggiunge servizi CORS al contenitore dei servizi dell'app:The method call adds CORS services to the app's service container:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Per ulteriori informazioni, vedere [Opzioni dei criteri CORS](#cpo) in questo documento .

Il <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> metodo può concatenare i metodi, come illustrato nel codice seguente:The method can chain methods, as shown in the following code:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Nota: l'URL **non** deve`/`contenere una barra finale ( ). Se l'URL `/`termina con `false` , il confronto restituisce e non viene restituita alcuna intestazione.

The following code applies CORS policies to all the apps endpoints via CORS Middleware:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Nota: `UseCors` deve `UseMvc`essere chiamato prima di .

Vedere [Abilitare CORS in pagine Razor, controller e metodi](#ecors) di azione per applicare i criteri CORS a livello di pagina/controller/azione.

Vedere [Test CORS](#test) per istruzioni sul test di codice simile al codice precedente.

## <a name="enable-cors-with-attributes"></a>Abilitare CORS con attributi

L'attributo [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornisce un'alternativa all'applicazione di CORS a livello globale. L'attributo `[EnableCors]` abilita CORS per i punti finali selezionati, anziché per tutti i punti finali.

Consente `[EnableCors]` di specificare `[EnableCors("{Policy String}")]` il criterio predefinito e di specificare un criterio.

L'attributo `[EnableCors]` può essere applicato a:

* Pagina Razor`PageModel`
* Controller
* Metodo di azione del controller

È possibile applicare criteri diversi a controller/modello `[EnableCors]` di pagina/azione con l'attributo . Quando `[EnableCors]` l'attributo viene applicato a un metodo di azione/modello di pagina/controller e CORS è abilitato nel middleware, vengono applicati ***entrambi i*** criteri. Si consiglia di ***non*** combinare le politiche. Utilizzare `[EnableCors]` l'attributo o il middleware,**non entrambi**. Quando `[EnableCors]`si utilizza , **non** definire un criterio predefinito.

Il codice seguente applica criteri diversi a ogni metodo:The following code applies a different policy to each method:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Il codice seguente crea un criterio predefinito `"AnotherPolicy"`CORS e un criterio denominato :

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Disabilita CORS

L'attributo [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) disabilita CORS per il controller/modello di pagina/azione.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opzioni dei criteri CORS

In questa sezione vengono descritte le varie opzioni che è possibile impostare in un criterio CORS:

* [Impostare le origini consentite](#set-the-allowed-origins)
* [Impostare i metodi HTTP consentiti](#set-the-allowed-http-methods)
* [Impostare le intestazioni di richiesta consentite](#set-the-allowed-request-headers)
* [Impostare le intestazioni delle risposte esposte](#set-the-exposed-response-headers)
* [Credenziali nelle richieste tra originiCredentials in cross-origin requests](#credentials-in-cross-origin-requests)
* [Impostare l'ora di scadenza preliminare](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>viene chiamato `Startup.ConfigureServices`in . Per alcune opzioni, può essere utile leggere prima la sezione [Come CORS funziona.](#how-cors)

## <a name="set-the-allowed-origins"></a>Impostare le origini consentite

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Consente le richieste CORS da tutte`http` `https`le origini con qualsiasi schema ( o ). `AllowAnyOrigin`non è sicuro perché *qualsiasi sito Web* può effettuare richieste cross-origin all'app.

> [!NOTE]
> Specificando `AllowAnyOrigin` ed `AllowCredentials` è una configurazione non sicura e può comportare la falsificazione di richieste intersito. Per un'app sicura, specificare un elenco esatto di origini se il client deve autorizzare se stesso ad accedere alle risorse del server.

`AllowAnyOrigin`influisce sulle richieste `Access-Control-Allow-Origin` di verifica preliminare e sull'intestazione. Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Imposta <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la proprietà del criterio in modo che sia una funzione che consente alle origini di corrispondere a un dominio con caratteri jolly configurato durante la valutazione se l'origine è consentita.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Impostare i metodi HTTP consentiti

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Consente qualsiasi metodo HTTP:
* Influisce sulle richieste `Access-Control-Allow-Methods` di verifica preliminare e sull'intestazione. Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Impostare le intestazioni di richiesta consentite

Per consentire l'invio di intestazioni specifiche in una richiesta <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, denominate intestazioni di richiesta *author*, chiamare e specificare le intestazioni consentite:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Per consentire tutte le <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>intestazioni di richiesta dell'autore, chiamare :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Questa impostazione influisce sulle `Access-Control-Request-Headers` richieste di verifica preliminare e sull'intestazione. Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)

IL middleware CORS consente `Access-Control-Request-Headers` sempre l'invio di quattro intestazioni nell'oggetto indipendentemente dai valori configurati in CorsPolicy.Headers. Questo elenco di intestazioni include:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Ad esempio, si consideri un'app configurata come segue:For example, consider an app configured as follows:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

IL middleware CORS risponde correttamente a una richiesta `Content-Language` di verifica preliminare con l'intestazione della richiesta seguente perché è sempre inserita nella whitelist:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Impostare le intestazioni delle risposte esposte

Per impostazione predefinita, il browser non espone tutte le intestazioni di risposta all'app. Per ulteriori informazioni, vedere [W3C Cross-Origin Resource Sharing (Terminologia): Intestazione di risposta semplice](https://www.w3.org/TR/cors/#simple-response-header).

Le intestazioni di risposta disponibili per impostazione predefinita sono:The response headers that are available by default are:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

La specifica CORS chiama queste intestazioni intestazioni di *risposta semplici*. Per rendere disponibili altre intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>per l'app, chiamare :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Credenziali nelle richieste tra originiCredentials in cross-origin requests

Le credenziali richiedono una gestione speciale in una richiesta CORS. Per impostazione predefinita, il browser non invia credenziali con una richiesta cross-origin. Le credenziali includono cookie e schemi di autenticazione HTTP. Per inviare le credenziali con una richiesta `XMLHttpRequest.withCredentials` tra `true`origini diverse, il client deve impostare su .

Utilizzando `XMLHttpRequest` direttamente:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Utilizzo di jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Utilizzo [dell'API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Il server deve consentire le credenziali. Per consentire le credenziali di <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>origine incrociata, chiamare :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

La risposta HTTP `Access-Control-Allow-Credentials` include un'intestazione, che indica al browser che il server consente le credenziali per una richiesta tra origini diverse.

Se il browser invia le credenziali ma la `Access-Control-Allow-Credentials` risposta non include un'intestazione valida, il browser non espone la risposta all'app e la richiesta tra origini ha esito negativo.

Consentire credenziali tra origini è un rischio per la sicurezza. Un sito Web in un altro dominio può inviare le credenziali di un utente connesso all'app per conto dell'utente all'insaputa dell'utente. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

La specifica CORS indica inoltre `"*"` che l'impostazione delle `Access-Control-Allow-Credentials` origini (tutte le origini) non è valida se l'intestazione è presente.

### <a name="preflight-requests"></a>Richieste di verifica preliminare

Per alcune richieste CORS, il browser invia una richiesta aggiuntiva prima di effettuare la richiesta effettiva. Questa richiesta viene definita *richiesta di verifica preliminare.* Il browser può ignorare la richiesta di verifica preliminare se sono vere le seguenti condizioni:

* Il metodo di richiesta è GET, HEAD o POST.
* L'app non imposta intestazioni `Accept`di `Accept-Language` `Content-Language`richiesta `Content-Type`diverse `Last-Event-ID`da , , , o .
* L'intestazione, `Content-Type` se impostata, ha uno dei seguenti valori:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

La regola sulle intestazioni di richiesta impostate per la `setRequestHeader` richiesta `XMLHttpRequest` client si applica alle intestazioni impostate dall'app chiamando sull'oggetto. La specifica CORS chiama queste intestazioni *intestazioni di richiesta dell'autore.* La regola non si applica alle intestazioni che `User-Agent` `Host`il `Content-Length`browser può impostare, ad esempio , o .

Di seguito è riportato un esempio di richiesta di verifica preliminare:The following is an example of a preflight request:

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

La richiesta di verifica preliminare utilizza il metodo HTTP OPTIONS. Include due intestazioni speciali:

* `Access-Control-Request-Method`: il metodo HTTP che verrà utilizzato per la richiesta effettiva.
* `Access-Control-Request-Headers`: un elenco di intestazioni di richiesta impostate dall'app nella richiesta effettiva. Come indicato in precedenza, non sono incluse le `User-Agent`intestazioni impostate dal browser, ad esempio .

<!-- I think this needs to be removed, was put here accidently -->

Quando CORS è abilitato con il criterio appropriato, ASP.NET Core risponde in genere automaticamente alle richieste di verifica preliminare CORS. Vedere [l'attributo [HttpOptions] per le richieste di verifica preliminare](#pro).

Una richiesta di verifica preliminare CORS può includere un'intestazione, `Access-Control-Request-Headers` che indica al server le intestazioni inviate con la richiesta effettiva.

Per consentire intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>specifiche, chiamare :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Per consentire tutte le <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>intestazioni di richiesta dell'autore, chiamare :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

I browser non sono del tutto `Access-Control-Request-Headers`coerenti nel modo in cui impostano . Se si impostano intestazioni `"*"` su <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>un valore diverso `Accept`da `Content-Type`(o utilizzare ), è necessario includere almeno , e `Origin`, oltre a tutte le intestazioni personalizzate che si desidera supportare.

Di seguito è riportato un esempio di risposta alla richiesta di verifica preliminare (presupponendo che il server consenta la richiesta):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

La risposta `Access-Control-Allow-Methods` include un'intestazione che elenca `Access-Control-Allow-Headers` i metodi consentiti e, facoltativamente, un'intestazione, che elenca le intestazioni consentite. Se la richiesta di verifica preliminare ha esito positivo, il browser invia la richiesta effettiva.

Se la richiesta di verifica preliminare viene negata, l'app restituisce una risposta *200 OK* ma non restituisce le intestazioni CORS. Pertanto, il browser non tenta la richiesta cross-origin.

### <a name="set-the-preflight-expiration-time"></a>Impostare l'ora di scadenza preliminare

L'intestazione `Access-Control-Max-Age` specifica per quanto tempo la risposta alla richiesta di verifica preliminare può essere memorizzata nella cache. Per impostare questa <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>intestazione, chiamare :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Come funziona CORS

In questa sezione viene descritto cosa accade in una richiesta [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) a livello dei messaggi HTTP.

* CORS **non** è una funzione di sicurezza. CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine.
  * Ad esempio, un attore malintenzionato potrebbe utilizzare [Impedisci Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) sul tuo sito ed eseguire una richiesta intersito al loro sito abilitato CORS per rubare informazioni.
* La tua API non è più sicura consentendo CORS.
  * Spetta al client (browser) applicare CORS. Il server esegue la richiesta e restituisce la risposta, è il client che restituisce un errore e blocca la risposta. Ad esempio, uno dei seguenti strumenti visualizzerà la risposta del server:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Un browser Web immettendo l'URL nella barra degli indirizzi.
* È un modo per un server per consentire ai browser di eseguire una richiesta [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) tra origini che altrimenti sarebbe vietata.
  * I browser (senza CORS) non possono eseguire richieste cross-origin. Prima di CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) veniva usato per aggirare questa restrizione. JSONP non usa XHR, usa `<script>` il tag per ricevere la risposta. Gli script possono essere caricati tra origini diverse.

La [specifica CORS](https://www.w3.org/TR/cors/) ha introdotto diverse nuove intestazioni HTTP che abilitano le richieste tra origini diverse. Se un browser supporta CORS, imposta automaticamente queste intestazioni per le richieste tra origini diverse. Il codice JavaScript personalizzato non è necessario per abilitare CORS.

Di seguito è riportato un esempio di richiesta tra origini. L'intestazione `Origin` fornisce il dominio del sito che effettua la richiesta. L'intestazione `Origin` è obbligatoria e deve essere diversa dall'host.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Se il server consente la `Access-Control-Allow-Origin` richiesta, imposta l'intestazione nella risposta. Il valore di questa `Origin` intestazione corrisponde all'intestazione `"*"`della richiesta o è il valore jolly , ovvero è consentita qualsiasi origine:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Se la risposta non `Access-Control-Allow-Origin` include l'intestazione, la richiesta cross-origin ha esito negativo. In particolare, il browser non consente la richiesta. Anche se il server restituisce una risposta corretta, il browser non rende disponibile la risposta all'app client.

<a name="test"></a>

## <a name="test-cors"></a>Testare CORS

Per testare CORS:

1. [Creare un progetto API.](xref:tutorials/first-web-api) In alternativa, è possibile [scaricare l'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Abilitare CORS utilizzando uno degli approcci in questo documento. Ad esempio:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`deve essere usato solo per testare un'app di esempio simile al [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)scaricato.

1. Creare un progetto di app Web (pagine Razor o MVC). L'esempio usa pagine Razor.The sample uses Razor Pages. È possibile creare l'app Web nella stessa soluzione del progetto API.
1. Aggiungere il codice evidenziato seguente al file *Index.cshtml:*

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. Nel codice precedente sostituire `url: 'https://<web app>.azurewebsites.net/api/values/1',` con l'URL dell'app distribuita.
1. Distribuire il progetto API. Ad esempio, [distribuire in Azure .For](xref:host-and-deploy/azure-apps/index)example, deploy to Azure .
1. Eseguire le pagine Razor o l'app MVC dal desktop e fare clic sul pulsante **Test.** Utilizzare gli strumenti F12 per esaminare i messaggi di errore.
1. Rimuovere l'origine `WithOrigins` localhost da e distribuire l'app. In alternativa, eseguire l'app client con una porta diversa. Ad esempio, eseguire da Visual Studio.For example, run from Visual Studio.
1. Eseguire il test con l'app client. Gli errori CORS restituiscono un errore, ma il messaggio di errore non è disponibile per JavaScript. Utilizzare la scheda della console negli strumenti F12 per visualizzare l'errore. A seconda del browser, viene visualizzato un errore (nella console degli strumenti F12) simile al seguente:

   * Utilizzo di Microsoft Edge:

     **SEC7120: [CORS] `https://localhost:44375` L'origine `https://localhost:44375` non è stata trovata nell'intestazione di risposta Access-Control-Allow-Origin per la risorsa cross-origin in`https://webapi.azurewebsites.net/api/values/1`**

   * Utilizzo di Chrome:

     **L'accesso a `https://webapi.azurewebsites.net/api/values/1` XMLHttpRequest all'origine `https://localhost:44375` è stato bloccato dai criteri CORS: non è presente alcuna intestazione 'Access-Control-Allow-Origin' nella risorsa richiesta.**
     
Gli endpoint abilitati per CORS possono essere testati con uno strumento, ad esempio [Fiddler](https://www.telerik.com/fiddler) o [Postman](https://www.getpostman.com/). Quando si utilizza uno strumento, l'origine della richiesta specificata dall'intestazione `Origin` deve essere diversa dall'host che riceve la richiesta. Se la richiesta non è *di origine incrociata* in base al valore dell'intestazione: `Origin`

* Non è necessario che il middleware CORS elabori la richiesta.
* Le intestazioni CORS non vengono restituite nella risposta.

## <a name="cors-in-iis"></a>CORS in IIS

Durante la distribuzione in IIS, CORS deve essere eseguito prima dell'autenticazione di Windows se il server non è configurato per consentire l'accesso anonimo. Per supportare questo scenario, il [modulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) deve essere installato e configurato per l'app.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Condivisione risorse tra le origini (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Introduzione al modulo IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
