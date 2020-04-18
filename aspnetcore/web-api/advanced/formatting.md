---
title: Formattare i dati di risposta nell'API Web ASP.NET Core
author: ardalis
description: Informazioni su come formattare i dati di risposta nell'API Web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
uid: web-api/advanced/formatting
ms.openlocfilehash: 392e4905126ffb6801cc55055f1d511f5fa99dd1
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642691"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formattare i dati di risposta nell'API Web ASP.NET Core

Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC include il supporto per la formattazione dei dati di risposta. I dati di risposta possono essere formattati utilizzando formati specifici o in risposta al formato richiesto dal client.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Risultati dell'azione specifica del formato

Alcuni tipi di risultati di azioni sono specifici di un particolare formato, ad esempio <xref:Microsoft.AspNetCore.Mvc.JsonResult> e <xref:Microsoft.AspNetCore.Mvc.ContentResult>. Le azioni possono restituire risultati formattati in un formato particolare, indipendentemente dalle preferenze del client. Ad esempio, `JsonResult` la restituzione restituisce dati in formato JSON. Restituisce `ContentResult` o una stringa restituisce dati di stringa in formato testo normale.

Non è necessaria un'azione per restituire alcun tipo specifico. ASP.NET Core supporta qualsiasi valore restituito dall'oggetto.  I risultati delle azioni che <xref:Microsoft.AspNetCore.Mvc.IActionResult> restituiscono oggetti che <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> non sono tipi vengono serializzati utilizzando l'implementazione appropriata. Per altre informazioni, vedere <xref:web-api/action-return-types>.

Il metodo helper <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> incorporato restituisce dati in formato JSON:The built-in helper method returns JSON-formatted data:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

Il download di esempio restituisce l'elenco degli autori. Utilizzando gli strumenti di sviluppo del browser F12 o [Postman](https://www.getpostman.com/tools) con il codice precedente:

* Viene visualizzata l'intestazione della risposta contenente **content-type:** `application/json; charset=utf-8` .
* Vengono visualizzate le intestazioni della richiesta. Ad esempio, `Accept` l'intestazione. L'intestazione `Accept` viene ignorata dal codice precedente.

Per restituire dati in formato testo normale, usare <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> e l'helper <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

Nel codice precedente, `Content-Type` l'oggetto `text/plain`restituito è . La restituzione di `Content-Type` `text/plain`una stringa fornisce:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Per le azioni con `IActionResult`più tipi restituiti, restituire . Ad esempio, la restituzione di codici di stato HTTP diversi in base al risultato delle operazioni eseguite.

## <a name="content-negotiation"></a>Negoziazione dei contenuti

La negoziazione del contenuto si verifica quando il client specifica [un'intestazione Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Il formato predefinito utilizzato da ASP.NET Core è [JSON](https://json.org/). La negoziazione dei contenuti è:

* Implementato <xref:Microsoft.AspNetCore.Mvc.ObjectResult>da .
* Incorporati i risultati dell'azione specifica del codice di stato restituiti dai metodi helper. I metodi helper dei `ObjectResult`risultati dell'azione sono basati su .

Quando viene restituito un tipo di `ObjectResult`modello, il tipo restituito è .

Il metodo di azione seguente usa i metodi helper `Ok` e `NotFound`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Per impostazione predefinita, `application/json` `text/json`ASP.NET `text/plain` Core supporta i tipi di supporti , e . Strumenti come [Fiddler](https://www.telerik.com/fiddler) o [Postman](https://www.getpostman.com/tools) possono impostare l'intestazione della `Accept` richiesta per specificare il formato restituito. Quando `Accept` l'intestazione contiene un tipo supportato dal server, viene restituito tale tipo. Nella sezione successiva viene illustrato come aggiungere ulteriori formattatori.

Le azioni del controller possono restituire POCO (Plain Old CLR Objects). Quando viene restituito un POCO, il `ObjectResult` runtime crea automaticamente un che esegue il wrapping dell'oggetto. Il client ottiene l'oggetto serializzato formattato. Se l'oggetto restituito `null`è `204 No Content` , viene restituita una risposta.

Restituzione di un tipo di oggetto:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

Nel codice precedente, una richiesta per un `200 OK` alias di autore valido restituisce una risposta con i dati dell'autore. Una richiesta per un `204 No Content` alias non valido restituisce una risposta.

### <a name="the-accept-header"></a>Intestazione Accept

La *negoziazione* del `Accept` contenuto viene eseguita quando nella richiesta viene visualizzata un'intestazione. Quando una richiesta contiene un'intestazione di accettazione, ASP.NET Core:When a request contains an accept header, ASP.NET Core:

* Enumera i tipi di supporto nell'intestazione accept in ordine di preferenza.
* Tenta di trovare un formattatore in grado di produrre una risposta in uno dei formati specificati.

Se non viene trovato alcun formattatore in grado di soddisfare la richiesta del client, ASP.NET Core:

* `406 Not Acceptable` Restituisce <xref:Microsoft.AspNetCore.Mvc.MvcOptions> se è stato impostato oppure -
* Cerca di trovare il primo formattatore in grado di produrre una risposta.

Se non è configurato alcun formattatore per il formato richiesto, viene utilizzato il primo formattatore in grado di formattare l'oggetto. Se `Accept` nella richiesta non viene visualizzata alcuna intestazione:

* Il primo formattatore in grado di gestire l'oggetto viene utilizzato per serializzare la risposta.
* Non c'è nessuna negoziazione in corso. Il server sta determinando il formato da restituire.

Se l'intestazione Accept contiene `*/*` `RespectBrowserAcceptHeader` , l'intestazione viene ignorata a meno che non sia impostata su true su <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.

### <a name="browsers-and-content-negotiation"></a>Browser e negoziazione del contenuto

A differenza dei client API `Accept` tipici, i browser Web forniscono intestazioni. Il browser Web specifica molti formati, inclusi i caratteri jolly. Per impostazione predefinita, quando il framework rileva che la richiesta proviene da un browser:By default, when the framework detects that the request is coming from a browser:

* L'intestazione `Accept` viene ignorata.
* Il contenuto viene restituito in JSON, se non diversamente configurato.

Ciò offre un'esperienza più coerente tra i browser quando si usano le API.

Per configurare un'app per <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> rispettare `true`le intestazioni di accettazione del browser, impostare su:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Configurare i formattatori

Le app che devono supportare formati aggiuntivi possono aggiungere i pacchetti NuGet appropriati e configurare il supporto. Esistono formattatori separati per input e output. I formattatori di input vengono utilizzati [dall'associazione di](xref:mvc/models/model-binding)modelli. I formattatori di output vengono utilizzati per formattare le risposte. Per informazioni sulla creazione di un formattatore personalizzato, vedere [Formattatori personalizzati](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>Aggiungere il supporto per il formato XML

I formattatori <xref:System.Xml.Serialization.XmlSerializer> XML implementati con l'utilizzo vengono configurati chiamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Il codice precedente serializza i risultati utilizzando `XmlSerializer`.

Quando si usa il codice precedente, i metodi del `Accept` controller restituiscono il formato appropriato in base all'intestazione della richiesta.

### <a name="configure-systemtextjson-based-formatters"></a>Configurare i formattatori basati su System.Text.Json

Le funzionalità per i formattatori basati su `System.Text.Json` possono essere configurate tramite `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Le opzioni di serializzazione dell'output, in `JsonResult`base all'azione, possono essere configurate utilizzando . Ad esempio:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Aggiungere il supporto del formato JSON basato su Newtonsoft.Json

Prima di ASP.NET Core 3.0, i formattatori JSON usati predefiniti implementati usando il `Newtonsoft.Json` pacchetto. In ASP.NET Core 3.0 o versione successiva, i formattatori JSON predefiniti sono basati su `System.Text.Json`. Il `Newtonsoft.Json` supporto per formattatori e funzionalità basati è disponibile installando il pacchetto [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet e configurandolo in `Startup.ConfigureServices`.

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Alcune funzionalità potrebbero non `System.Text.Json`funzionare correttamente con formattatori basati su e richiedono un riferimento ai `Newtonsoft.Json`formattatori basati su. Continua a `Newtonsoft.Json`usare i formattatori basati su:Continue using the -based formatters if the app:

* Utilizza `Newtonsoft.Json` gli attributi. Ad esempio, `[JsonProperty]` o `[JsonIgnore]`.
* Personalizza le impostazioni di serializzazione.
* Si basa sulle `Newtonsoft.Json` funzionalità fornite.
* Configura `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Prima di ASP.NET Core 3.0 `JsonResult.SerializerSettings` accetta un'istanza di `JsonSerializerSettings` specifica di `Newtonsoft.Json`.
* Genera documentazione [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).

Le funzionalità `Newtonsoft.Json`per i formattatori `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`basati su possono essere configurate utilizzando:

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

Le opzioni di serializzazione dell'output, in `JsonResult`base all'azione, possono essere configurate utilizzando . Ad esempio:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>Aggiungere il supporto per il formato XML

La formattazione XML richiede il pacchetto [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet.

I formattatori <xref:System.Xml.Serialization.XmlSerializer> XML implementati con l'utilizzo vengono configurati chiamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Il codice precedente serializza i risultati utilizzando `XmlSerializer`.

Quando si usa il codice precedente, i metodi del controller `Accept` devono restituire il formato appropriato in base all'intestazione della richiesta.

::: moniker-end

### <a name="specify-a-format"></a>Specificare un formato

Per limitare i formati [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) di risposta, applicare il filtro. Come la `[Produces]` maggior parte dei [filtri](xref:mvc/controllers/filters), può essere applicato nell'ambito azione, controller o globale:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Il [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro precedente:

* Impone a tutte le azioni all'interno del controller di restituire risposte in formato JSON.
* Se sono configurati altri formattatori e il client specifica un formato diverso, viene restituito JSON.

Per ulteriori informazioni, vedere [Filtri](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Formattatori di casi speciali

Alcuni casi speciali vengono implementati tramite formattatori predefiniti. Per impostazione `string` predefinita, i tipi restituiti sono formattati come *text/plain* (*text/html* se richiesto tramite l'intestazione). `Accept` Questo comportamento può essere eliminato <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>rimuovendo l'oggetto . I formattatori `ConfigureServices` vengono rimossi nel metodo. Le azioni che dispongono `204 No Content` di un `null`tipo restituito di oggetto modello vengono restituite durante la restituzione di . Questo comportamento può essere eliminato <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>rimuovendo l'oggetto . Il codice seguente rimuove `StringOutputFormatter` e `HttpNoContentOutputFormatter`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Senza `StringOutputFormatter`i tipi restituiti dai `string` formati del formattatore JSON incorporati. Se il formattatore JSON incorporato viene rimosso ed è disponibile un `string` formattatore XML, i formati del formattatore XML restituiscono i tipi. In `string` caso contrario, i tipi restituiti restituiscono `406 Not Acceptable`.

Senza `HttpNoContentOutputFormatter`, gli oggetti Null vengono formattati tramite il formattatore configurato. Ad esempio:

* Il formattatore JSON restituisce `null`una risposta con un corpo di .
* Il formattatore XML restituisce un `xsi:nil="true"` elemento XML vuoto con l'attributo impostato.

## <a name="response-format-url-mappings"></a>Mapping URL del formato di risposta

I client possono richiedere un formato particolare come parte dell'URL, ad esempio:

* Nella stringa di query o in parte del percorso.
* Utilizzando un'estensione di file specifica del formato, ad esempio .xml o .json.

Il mapping dal percorso della richiesta deve essere specificato nella route usata dall'API. Ad esempio:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

La route precedente consente di specificare il formato richiesto come estensione di file facoltativa. L'attributo [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) verifica l'esistenza del `RouteData` valore di formato in e esegue il mapping del formato della risposta al formattatore appropriato quando viene creata la risposta.

|           Route        |             Formattatore              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Formattatore di output predefinito    |
| `/api/products/5.json` | Formattatore JSON (se configurato) |
| `/api/products/5.xml`  | Formattatore XML (se configurato)  |
