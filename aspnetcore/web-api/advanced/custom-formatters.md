---
title: Formattatori personalizzati nell'API Web ASP.NET Core
author: rick-anderson
description: Informazioni su come creare e usare i formattatori personalizzati nelle API Web ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 0836fc288a015adb9a6223c5a2b681b1b03bded4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777319"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Formattatori personalizzati nell'API Web ASP.NET Core

[Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core MVC supporta lo scambio di dati in API Web mediante formattatori di input e di output. I formattatori di input vengono utilizzati dall' [associazione di modelli](xref:mvc/models/model-binding). I formattatori di output vengono utilizzati per [formattare le risposte](xref:web-api/advanced/formatting).

Il Framework fornisce formattatori di input e output predefiniti per JSON e XML. Fornisce un formattatore di output incorporato per il testo normale, ma non fornisce un formattatore di input per testo normale.

In questo articolo viene illustrato come aggiungere supporto per altri formati creando formattatori personalizzati. Per un esempio di formattatore di input personalizzato per testo normale, vedere [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) su GitHub.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Quando usare i formattatori personalizzati

Utilizzare un formattatore personalizzato quando si desidera che il processo di [negoziazione del contenuto](xref:web-api/advanced/formatting#content-negotiation) supporti un tipo di contenuto non supportato dai formattatori predefiniti.

Ad esempio, se alcuni dei client per l'API Web gestiscono il formato [Protobuf](https://github.com/google/protobuf), si potrebbe voler usare Protobuf con tali client per una maggiore efficienza. Oppure potrebbe essere necessario che l'API Web invii nomi di contatto e indirizzi nel formato [vCard](https://wikipedia.org/wiki/VCard), un formato usato comunemente per lo scambio di dati di contatto. L'app di esempio disponibile in questo articolo implementa un semplice formattatore vCard.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Panoramica sull'uso di un formattatore personalizzato

Di seguito sono elencati i passaggi per creare e usare un formattatore personalizzato:

* Creare una classe formattatore di output se si vogliono serializzare i dati da inviare al client.
* Creare una classe formattatore di input se si vogliono deserializzare i dati ricevuti dal client.
* Aggiungere le istanze dei formattatori alle raccolte `InputFormatters` e `OutputFormatters` in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).

Nelle sezioni seguenti sono disponibili indicazioni ed esempi di codici per ognuno dei passaggi specificati.

## <a name="how-to-create-a-custom-formatter-class"></a>Come creare una classe formattatore personalizzato

Per creare un formattatore:

* Derivare la classe dalla classe di base appropriata.
* Specificare i formati di testo multimediali validi e le codifiche nel costruttore.
* Eseguire l'override dei metodi `CanReadType`/`CanWriteType`
* Eseguire l'override dei metodi `ReadRequestBodyAsync`/`WriteResponseBodyAsync`
  
### <a name="derive-from-the-appropriate-base-class"></a>Derivare dalla classe di base appropriata

Per i formati multimediali di testo (ad esempio, vCard), derivare dalla classe [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) o [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Per un esempio di formattatore di input, vedere l'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

Per i tipi binari, derivare dalla classe di base [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) o [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter).

### <a name="specify-valid-media-types-and-encodings"></a>Specificare i formati multimediali validi e le codifiche

Nel costruttore specificare i formati multimediali validi e le codifiche aggiungendo alle raccolte `SupportedMediaTypes` e `SupportedEncodings`.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

Per un esempio di formattatore di input, vedere l'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

> [!NOTE]
> In una classe formattatore non è possibile inserire le dipendenze del costruttore. Ad esempio, non è possibile ottenere un logger aggiungendo un parametro di logger al costruttore. Per accedere ai servizi, usare l'oggetto di contesto che viene passato ai metodi. Nell'esempio di codice riportato [di seguito](#read-write) viene illustrato come eseguire tale operazione.

### <a name="override-canreadtypecanwritetype"></a>Eseguire l'override di CanReadType/CanWriteType

Eseguire l'override dei metodi `CanReadType` o `CanWriteType` per specificare il tipo in cui eseguire la deserializzazione o il tipo da cui eseguire la serializzazione. Ad esempio, potrebbe essere possibile creare solo un testo vCard da un tipo `Contact` e viceversa.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

Per un esempio di formattatore di input, vedere l'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

#### <a name="the-canwriteresult-method"></a>Metodo CanWriteResult

In alcuni scenari è necessario eseguire l'override di `CanWriteResult` anziché di `CanWriteType`. Usare `CanWriteResult` se vengono soddisfatte le condizioni seguenti:

* Il metodo di azione restituisce una classe modello.
* Sono disponibili classi derivate che possono essere restituite in fase di esecuzione.
* In fase di esecuzione è necessario conoscere quale classe derivata è stata restituita dall'azione.

Ad esempio, si supponga che la firma del metodo di azione restituisca un tipo `Person`, ma può restituire un tipo `Student` o `Instructor` che deriva da `Person`. Se il formattatore deve gestire solo oggetti `Student`, controllare il tipo di [oggetto](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) nell'oggetto di contesto specificato per il metodo `CanWriteResult`. Si noti che non è necessario usare `CanWriteResult` quando il metodo di azione restituisce `IActionResult`; in tal caso, il metodo `CanWriteType` riceve il tipo di runtime.

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a>Eseguire l'override di ReadRequestBodyAsync/WriteResponseBodyAsync

Le effettive operazioni di deserializzazione o serializzazione vengono eseguite in `ReadRequestBodyAsync` o `WriteResponseBodyAsync`. Le righe evidenziate nell'esempio seguente indicano come ottenere i servizi dal contenitore dell'inserimento delle dipendenze. Non è possibile ottenerli dai parametri del costruttore.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

Per un esempio di formattatore di input, vedere l'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Come configurare MVC per usare un formattatore personalizzato

Per usare un formattatore personalizzato, aggiungere un'istanza della classe formattatore alla raccolta `InputFormatters` o `OutputFormatters`.

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

I formattatori vengono valutati nell'ordine d'inserimento. Il primo ha la precedenza.

## <a name="next-steps"></a>Passaggi successivi

* [App di esempio per questo documento](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), che implementa semplici formattatori di input e output vCard. L'app legge e scrive vCard simili all'esempio seguente:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR]
uid:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

Per visualizzare l'output vCard, eseguire l'applicazione, inviare una richiesta Get con intestazione Accept "text/vcard" a `http://localhost:63313/api/contacts/` (se l'applicazione è eseguita da Visual Studio) o a `http://localhost:5000/api/contacts/` (se l'applicazione è eseguita dalla riga di comando).

Per aggiungere un file vCard alla raccolta di contatti in memoria, inviare una richiesta Post allo stesso URL, con intestazione Content-Type "text/vcard" e con testo vCard nel corpo, formattato come illustrato nell'esempio precedente.
