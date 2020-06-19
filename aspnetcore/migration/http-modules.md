---
title: Eseguire la migrazione di gestori e moduli HTTP a ASP.NET Core middleware
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/http-modules
ms.openlocfilehash: 214e3fa86a1418f04a5e292cdc1b4baac8c75643
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074182"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>Eseguire la migrazione di gestori e moduli HTTP a ASP.NET Core middleware

Questo articolo illustra come eseguire la migrazione di [moduli e gestori HTTP ASP.NET esistenti da System. webserver](/iis/configuration/system.webserver/) a [middleware](xref:fundamentals/middleware/index)ASP.NET Core.

## <a name="modules-and-handlers-revisited"></a>Moduli e gestori rivisitati

Prima di procedere con ASP.NET Core middleware, è necessario riepilogare il funzionamento dei moduli e gestori HTTP:

![Gestore moduli](http-modules/_static/moduleshandlers.png)

**I gestori sono:**

* Classi che implementano [IHttpHandler](/dotnet/api/system.web.ihttphandler)

* Utilizzato per gestire le richieste con un nome file o un'estensione specifica, ad esempio *. report*

* [Configurato](/iis/configuration/system.webserver/handlers/) in *Web.config*

**I moduli sono:**

* Classi che implementano [IHttpModule](/dotnet/api/system.web.ihttpmodule)

* Richiamato per ogni richiesta

* In grado di eseguire il cortocircuito (arrestare l'ulteriore elaborazione di una richiesta)

* In grado di aggiungere alla risposta HTTP o di crearne di propri

* [Configurato](/iis/configuration/system.webserver/modules/) in *Web.config*

**L'ordine in cui i moduli elaborano le richieste in ingresso è determinato da:**

1. Il [ciclo di vita dell'applicazione](https://msdn.microsoft.com/library/ms227673.aspx), ovvero eventi di serie generati da ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)e così via. Ogni modulo può creare un gestore per uno o più eventi.

2. Per lo stesso evento, l'ordine in cui sono configurati in *Web.config*.

Oltre ai moduli, è possibile aggiungere i gestori per gli eventi del ciclo di vita al file *Global.asax.cs* . Questi gestori vengono eseguiti dopo i gestori nei moduli configurati.

## <a name="from-handlers-and-modules-to-middleware"></a>Da gestori e moduli al middleware

**Il middleware è più semplice dei moduli e gestori HTTP:**

* Moduli, gestori, *Global.asax.cs*, *Web.config* (eccetto la configurazione di IIS) e il ciclo di vita dell'applicazione sono finiti

* I ruoli di moduli e gestori sono stati rilevati dal middleware

* Il middleware viene configurato usando codice anziché in *Web.config*

::: moniker range=">= aspnetcore-3.0"

* La creazione di [rami della pipeline](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) consente di inviare richieste a un middleware specifico, in base non solo all'URL, ma anche alle intestazioni delle richieste, alle stringhe di query e così via.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* La creazione di [rami della pipeline](xref:fundamentals/middleware/index#use-run-and-map) consente di inviare richieste a un middleware specifico, in base non solo all'URL, ma anche alle intestazioni delle richieste, alle stringhe di query e così via.

::: moniker-end

**Il middleware è molto simile ai moduli:**

* Richiamato in linea di base per ogni richiesta

* In grado di eseguire il cortocircuito di una richiesta, [non passando la richiesta al middleware successivo](#http-modules-shortcircuiting-middleware)

* In grado di creare la propria risposta HTTP

**Il middleware e i moduli vengono elaborati in un ordine diverso:**

* L'ordine del middleware si basa sull'ordine in cui vengono inseriti nella pipeline delle richieste, mentre l'ordine dei moduli è basato principalmente sugli eventi del [ciclo di vita dell'applicazione](https://msdn.microsoft.com/library/ms227673.aspx)

* L'ordine del middleware per le risposte è il contrario rispetto a quello per le richieste, mentre l'ordine dei moduli è lo stesso per le richieste e le risposte

* Vedere [creare una pipeline middleware con IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![Middleware](http-modules/_static/middleware.png)

Si noti come nell'immagine precedente il middleware di autenticazione ha corto circuito della richiesta.

## <a name="migrating-module-code-to-middleware"></a>Migrazione del codice del modulo al middleware

Un modulo HTTP esistente sarà simile al seguente:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

Come illustrato nella pagina [middleware](xref:fundamentals/middleware/index) , un middleware ASP.NET Core è una classe che espone un `Invoke` metodo che accetta un oggetto `HttpContext` e restituisce un oggetto `Task` . Il nuovo middleware sarà simile al seguente:

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

Il modello middleware precedente è stato ricavato dalla sezione sulla [scrittura del middleware](xref:fundamentals/middleware/write).

La classe helper *MyMiddlewareExtensions* rende più semplice la configurazione del middleware nella `Startup` classe. Il `UseMyMiddleware` metodo aggiunge la classe middleware alla pipeline della richiesta. I servizi richiesti dal middleware vengono inseriti nel costruttore del middleware.

<a name="http-modules-shortcircuiting-middleware"></a>

Il modulo potrebbe terminare una richiesta, ad esempio se l'utente non è autorizzato:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

Un middleware gestisce questa operazione non chiamando il `Invoke` middleware successivo nella pipeline. Tenere presente che questa operazione non termina completamente la richiesta, perché i middleware precedenti verranno comunque richiamati quando la risposta ripercorre la pipeline.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

Quando si esegue la migrazione della funzionalità del modulo al nuovo middleware, è possibile che il codice non venga compilato perché la `HttpContext` classe è cambiata in modo significativo in ASP.NET Core. [Successivamente](#migrating-to-the-new-httpcontext), verrà illustrato come eseguire la migrazione al nuovo ASP.NET Core HttpContext.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Migrazione dell'inserimento del modulo nella pipeline delle richieste

I moduli HTTP vengono in genere aggiunti alla pipeline di richieste usando *Web.config*:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

Per convertirlo, [aggiungere il nuovo middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) alla pipeline delle richieste nella `Startup` classe:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

Il punto esatto della pipeline in cui si inserisce il nuovo middleware dipende dall'evento gestito come modulo ( `BeginRequest` , e `EndRequest` così via) e dall'ordine nell'elenco dei moduli in *Web.config*.

Come indicato in precedenza, non esiste alcun ciclo di vita dell'applicazione in ASP.NET Core e l'ordine in cui le risposte vengono elaborate dal middleware differisce dall'ordine usato dai moduli. Questo potrebbe rendere più complessa la decisione di ordinamento.

Se l'ordinamento diventa un problema, è possibile suddividere il modulo in più componenti middleware che possono essere ordinati in modo indipendente.

## <a name="migrating-handler-code-to-middleware"></a>Migrazione del codice del gestore al middleware

Un gestore HTTP ha un aspetto simile al seguente:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

Nel progetto di ASP.NET Core, è necessario tradurlo in un middleware simile al seguente:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

Questo middleware è molto simile al middleware corrispondente ai moduli. L'unica differenza reale è che in questo caso non viene chiamata `_next.Invoke(context)` . Questa operazione è sensata, perché il gestore si trova alla fine della pipeline delle richieste, quindi non ci sarà alcun middleware successivo da richiamare.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>Migrazione dell'inserimento del gestore nella pipeline delle richieste

La configurazione di un gestore HTTP viene eseguita in *Web.config* e ha un aspetto simile al seguente:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

È possibile eseguire la conversione aggiungendo il middleware del nuovo gestore alla pipeline delle richieste nella `Startup` classe, simile a middleware convertito da moduli. Il problema con questo approccio è che invia tutte le richieste al nuovo middleware del gestore. Tuttavia, si desidera che le richieste con una determinata estensione raggiungano il middleware. Questo consentirebbe di ottenere le stesse funzionalità con il gestore HTTP.

Una soluzione consiste nell'eseguire il branching della pipeline per le richieste con una determinata estensione, usando il `MapWhen` metodo di estensione. Questa operazione viene eseguita nello stesso `Configure` metodo in cui si aggiunge l'altro middleware:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen`accetta i parametri seguenti:

1. Espressione lambda che accetta l'oggetto `HttpContext` e restituisce `true` se la richiesta deve andare al di sotto del ramo. Ciò significa che è possibile creare branch richieste non solo in base alla relativa estensione, ma anche su intestazioni di richiesta, parametri della stringa di query e così via.

2. Espressione lambda che accetta un oggetto `IApplicationBuilder` e aggiunge tutto il middleware per il ramo. Ciò significa che è possibile aggiungere un middleware aggiuntivo al ramo davanti al middleware del gestore.

Middleware aggiunto alla pipeline prima che il ramo venga richiamato in tutte le richieste; il ramo non avrà alcun effetto su di essi.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Caricamento delle opzioni del middleware usando il modello di opzioni

Alcuni moduli e gestori hanno opzioni di configurazione archiviate in *Web.config*. Tuttavia, in ASP.NET Core viene usato un nuovo modello di configurazione al posto di *Web.config*.

Il nuovo [sistema di configurazione](xref:fundamentals/configuration/index) offre le opzioni seguenti per risolvere il problema:

* Inserire direttamente le opzioni nel middleware, come illustrato nella [sezione successiva](#loading-middleware-options-through-direct-injection).

* Usare il [modello di opzioni](xref:fundamentals/configuration/options):

1. Creare una classe che contenga le opzioni del middleware, ad esempio:

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. Archiviare i valori delle opzioni

   Il sistema di configurazione consente di archiviare i valori delle opzioni ovunque si desideri. Tuttavia, la maggior parte dei siti USA *appsettings.json*, quindi prendiamo questo approccio:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *MyMiddlewareOptionsSection* è il nome di una sezione. Non deve corrispondere al nome della classe Options.

3. Associare i valori delle opzioni alla classe Options

    Il modello di opzioni usa il Framework di inserimento delle dipendenze di ASP.NET Core per associare il tipo di opzioni (ad esempio `MyMiddlewareOptions` ) a un `MyMiddlewareOptions` oggetto con le opzioni effettive.

    Aggiornare la `Startup` classe:

   1. Se si usa *appsettings.json*, aggiungerlo al generatore di configurazione nel `Startup` costruttore:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. Configurare il servizio opzioni:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. Associare le opzioni alla classe Options:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. Inserire le opzioni nel costruttore del middleware. Questa operazione è simile all'inserimento di opzioni in un controller.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   Il metodo di estensione [UseMiddleware](#http-modules-usemiddleware) che aggiunge il middleware al `IApplicationBuilder` si occupa dell'inserimento delle dipendenze.

   Questo non è limitato agli `IOptions` oggetti. Tutti gli altri oggetti richiesti dal middleware possono essere inseriti in questo modo.

## <a name="loading-middleware-options-through-direct-injection"></a>Caricamento delle opzioni del middleware tramite l'inserimento diretto

Il modello Options presenta il vantaggio di creare un accoppiamento libero tra i valori delle opzioni e i relativi consumer. Una volta associata una classe Options con i valori effettivi delle opzioni, qualsiasi altra classe può ottenere l'accesso alle opzioni tramite il Framework di inserimento delle dipendenze. Non è necessario passare i valori delle opzioni.

Questa operazione si interrompe anche se si vuole usare lo stesso middleware due volte, con opzioni diverse. Ad esempio un middleware di autorizzazione usato in rami diversi che consentono ruoli diversi. Non è possibile associare due oggetti Options diversi a una classe di opzioni.

La soluzione consiste nell'ottenere gli oggetti Options con i valori effettivi delle opzioni nella `Startup` classe e passarli direttamente a ogni istanza del middleware.

1. Aggiungi una seconda chiave alla *appsettings.js*

   Per aggiungere un secondo set di opzioni all' *appsettings.jssu* file, usare una nuova chiave per identificarla in modo univoco:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. Recuperare i valori delle opzioni e passarli al middleware. Il `Use...` metodo di estensione, che aggiunge il middleware alla pipeline, è una posizione logica per passare i valori delle opzioni: 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. Consentire al middleware di prendere un parametro options. Fornire un overload del `Use...` metodo di estensione (che accetta il parametro options e lo passa a `UseMiddleware` ). Quando `UseMiddleware` viene chiamato con parametri, passa i parametri al costruttore del middleware quando crea un'istanza dell'oggetto middleware.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   Si noti che in questo modo viene eseguito il wrapping dell'oggetto options in un `OptionsWrapper` oggetto. Implementa `IOptions` , come previsto dal costruttore del middleware.

## <a name="migrating-to-the-new-httpcontext"></a>Migrazione al nuovo HttpContext

Si è visto in precedenza che il `Invoke` metodo nel middleware accetta un parametro di tipo `HttpContext` :

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext`è stato modificato in modo significativo in ASP.NET Core. In questa sezione viene illustrato come tradurre le proprietà più comunemente utilizzate di [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) nel nuovo oggetto `Microsoft.AspNetCore.Http.HttpContext` .

### <a name="httpcontext"></a>HttpContext

**HttpContext. Items** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**ID richiesta univoco (nessuna controparte System. Web. HttpContext)**

Fornisce un ID univoco per ogni richiesta. Molto utile da includere nei log.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext. Request

**HttpContext. Request. HttpMethod** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext. Request. QueryString** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext. Request. URL** e **HttpContext. Request. RawUrl** vengono convertiti in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext. Request. IsSecureConnection** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext. Request. UserHostAddress** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext. Request. cookies** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext. Request. RequestContext. RouteData** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext. Request. Headers** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext. Request. UserAgent** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext. Request. UrlReferrer** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext. Request. ContentType** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext. Request. Form** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Leggere i valori del modulo solo se il sottotipo di contenuto è *x-www-form-urlencoded* o *form-data*.

**HttpContext. Request. InputStream** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Usare questo codice solo in un middleware di tipo gestore, alla fine di una pipeline.
>
>È possibile leggere il corpo non elaborato come illustrato sopra solo una volta per ogni richiesta. Il middleware che tenta di leggere il corpo dopo la prima lettura leggerà un corpo vuoto.
>
>Questa operazione non si applica alla lettura di un modulo, come illustrato in precedenza, perché viene eseguita da un buffer.

### <a name="httpcontextresponse"></a>HttpContext. Response

**HttpContext. Response. status** e **HttpContext. Response. StatusDescription** vengono convertiti in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext. Response. ContentEncoding** e **HttpContext. Response. ContentType** sono convertibili in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

Anche **HttpContext. Response. ContentType** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext. Response. output** viene convertito in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext. Response. TransmitFile**

[Qui](../fundamentals/request-features.md#middleware-and-request-features)viene illustrato come servire un file.

**HttpContext. Response. Headers**

L'invio di intestazioni di risposta è complicato dal fatto che, se si impostano elementi che sono stati scritti nel corpo della risposta, non verranno inviati.

La soluzione consiste nell'impostare un metodo di callback che verrà chiamato immediatamente prima di iniziare la scrittura della risposta. Questa operazione viene eseguita in modo ottimale all'inizio del `Invoke` metodo nel middleware. Si tratta di un metodo di callback che imposta le intestazioni della risposta.

Il codice seguente imposta un metodo di callback denominato `SetHeaders` :

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

Il `SetHeaders` metodo di callback avrà un aspetto simile al seguente:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext. Response. cookies**

I cookie passano al browser in un'intestazione di risposta *set-cookie* . Di conseguenza, l'invio di cookie richiede lo stesso callback utilizzato per inviare le intestazioni di risposta:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

Il `SetCookies` metodo di callback avrà un aspetto simile al seguente:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Cenni preliminari sui gestori HTTP e sui moduli HTTP](/iis/configuration/system.webserver/)
* [Configuration](xref:fundamentals/configuration/index)
* [Avvio dell'applicazione](xref:fundamentals/startup)
* [Middleware](xref:fundamentals/middleware/index)
