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
ms.openlocfilehash: 89fbb9d52d99d0eff6656eb6a5a9b4e1c01bc65c
ms.sourcegitcommit: 1833870ad0845326fb764fef1b530a07b9b5b099
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85347086"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Formattatori personalizzati nell'API Web ASP.NET Core

Di [Kirk Larkin](https://twitter.com/serpent5) e [Tom Dykstra](https://github.com/tdykstra).

ASP.NET Core MVC supporta lo scambio di dati in API Web mediante formattatori di input e di output. I formattatori di input vengono utilizzati dall' [associazione di modelli](xref:mvc/models/model-binding). I formattatori di output vengono utilizzati per [formattare le risposte](xref:web-api/advanced/formatting).

Il Framework fornisce formattatori di input e output predefiniti per JSON e XML. Fornisce un formattatore di output incorporato per il testo normale, ma non fornisce un formattatore di input per testo normale.

In questo articolo viene illustrato come aggiungere supporto per altri formati creando formattatori personalizzati. Per un esempio di formattatore di input di testo normale personalizzato, vedere [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) su GitHub.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Quando usare i formattatori personalizzati

Usare un formattatore personalizzato per aggiungere il supporto per un tipo di contenuto non gestito dai formattatori Bult.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Panoramica sull'uso di un formattatore personalizzato

Per creare un formattatore personalizzato:

* Per la serializzazione dei dati inviati al client, creare una classe formattatore di output.
* Per i dati deserialzing ricevuti dal client, creare una classe formattatore di input.
* Aggiungere istanze delle classi formatter alle `InputFormatters` raccolte e `OutputFormatters` in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).

## <a name="how-to-create-a-custom-formatter-class"></a>Come creare una classe formattatore personalizzato

Per creare un formattatore:

* Derivare la classe dalla classe di base appropriata. L'app di esempio deriva da <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> e <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Specificare i formati di testo multimediali validi e le codifiche nel costruttore.
* Eseguire l'override dei metodi <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> e <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>.
* Eseguire l'override dei metodi <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> e `WriteResponseBodyAsync`.

Nel codice seguente viene illustrata la `VcardOutputFormatter` classe dell' [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=snippet)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Derivare dalla classe di base appropriata

Per i formati multimediali di testo (ad esempio, vCard), derivare dalla classe [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) o [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Per i tipi binari, derivare dalla classe di base [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) o [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter).

### <a name="specify-valid-media-types-and-encodings"></a>Specificare i formati multimediali validi e le codifiche

Nel costruttore specificare i formati multimediali validi e le codifiche aggiungendo alle raccolte `SupportedMediaTypes` e `SupportedEncodings`.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=ctor)]

Una classe formatter **non** può utilizzare l'inserimento del costruttore per le relative dipendenze. Ad esempio, `ILogger<VcardOutputFormatter>` non può essere aggiunto come parametro al costruttore. Per accedere ai servizi, usare l'oggetto di contesto che viene passato ai metodi. Un esempio di codice in questo articolo e nell' [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) illustra come eseguire questa operazione.

### <a name="override-canreadtype-and-canwritetype"></a>Eseguire l'override di CanReadType e CanWriteType

Specificare il tipo in cui eseguire la deserializzazione o serializzare eseguendo l'override dei `CanReadType` `CanWriteType` metodi o. Ad esempio, la creazione di testo vCard da un `Contact` tipo e viceversa.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a>Metodo CanWriteResult

In alcuni scenari `CanWriteResult` è necessario eseguire l'override di anziché `CanWriteType` . Usare `CanWriteResult` se vengono soddisfatte le condizioni seguenti:

* Il metodo di azione restituisce una classe di modello.
* Sono disponibili classi derivate che possono essere restituite in fase di esecuzione.
* La classe derivata restituita dall'azione deve essere nota in fase di esecuzione.

Si supponga, ad esempio, che il metodo di azione:

* La firma restituisce un `Person` tipo.
* Può restituire un `Student` `Instructor` tipo o che deriva da `Person` . 

Affinché il formattatore gestisca solo `Student` oggetti, controllare il tipo di [oggetto](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) nell'oggetto di contesto fornito al `CanWriteResult` metodo. Quando il metodo di azione restituisce `IActionResult` :

* Non è necessario usare `CanWriteResult` .
* Il `CanWriteType` metodo riceve il tipo di Runtime.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>Eseguire l'override di ReadRequestBodyAsync e WriteResponseBodyAsync

La deserializzazione o la serializzazione viene eseguita in `ReadRequestBodyAsync` o `WriteResponseBodyAsync` . Nell'esempio seguente viene illustrato come ottenere servizi dal contenitore di inserimento delle dipendenze. Non è possibile ottenere i servizi dai parametri del costruttore.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=writeresponse)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Come configurare MVC per usare un formattatore personalizzato

Per usare un formattatore personalizzato, aggiungere un'istanza della classe formattatore alla raccolta `InputFormatters` o `OutputFormatters`.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/3.1sample/Startup.cs?name=mvcoptions)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

I formattatori vengono valutati nell'ordine d'inserimento. Il primo ha la precedenza.

## <a name="the-completed-vcardinputformatter-class"></a>Classe completata `VcardInputFormatter`

Nel codice seguente viene illustrata la `VcardInputFormatter` classe dell' [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardInputFormatter.cs?name=snippet)]

## <a name="test-the-app"></a>Testare l'app

[Eseguire l'app di esempio per questo articolo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), che implementa i formattatori di input e output vCard di base. L'app legge e scrive le vCard simili alle seguenti:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

Per visualizzare l'output della vCard, eseguire l'app e inviare una richiesta GET con l'intestazione Accept `text/vcard` a `https://localhost:5001/api/contacts` .

Per aggiungere un vCard alla raccolta di contatti in memoria:

* Inviare una `Post` richiesta a `/api/contacts` con uno strumento come il post.
* Impostare l'intestazione `Content-Type` su `text/vcard`.
* Imposta il `vCard` testo nel corpo, formattato come l'esempio precedente.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
