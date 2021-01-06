---
title: Operazioni di richiesta e risposta in ASP.NET Core
author: jkotalik
description: Informazioni su come leggere il corpo della richiesta e scrivere il corpo della risposta in ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/request-response
ms.openlocfilehash: 5ad39821778ea58097169def85a940a06f1d036e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "96513109"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Operazioni di richiesta e risposta in ASP.NET Core

Di [Justin Kotalik](https://github.com/jkotalik)

Questo articolo illustra come leggere dal corpo della richiesta e scrivere nel corpo della risposta. Il codice per queste operazioni potrebbe essere necessario quando si scrive il middleware. Al di fuori della scrittura del middleware, il codice personalizzato non è in genere necessario perché le operazioni vengono gestite da MVC e dalle Razor pagine.

Esistono due astrazioni per i corpi di richiesta e risposta: <xref:System.IO.Stream> e <xref:System.IO.Pipelines.Pipe> . Per la lettura della richiesta, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> è un oggetto <xref:System.IO.Stream> e `HttpRequest.BodyReader` è un oggetto <xref:System.IO.Pipelines.PipeReader> . Per la scrittura della risposta, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> è un oggetto <xref:System.IO.Stream> e `HttpResponse.BodyWriter` è un oggetto <xref:System.IO.Pipelines.PipeWriter> .

Le [pipeline](/dotnet/standard/io/pipelines) sono consigliate per i flussi. I flussi possono essere più facili da usare per alcune operazioni semplici, ma le pipeline hanno prestazioni migliori e sono più facili da usare nella maggior parte degli scenari. ASP.NET Core sta iniziando a usare le pipeline anziché i flussi internamente. Tra gli esempi sono inclusi:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

I flussi non vengono rimossi dal Framework. I flussi continuano a essere usati in .NET e molti tipi di flusso non hanno equivalenti di pipe, ad esempio `FileStreams` e `ResponseCompression` .

## <a name="stream-examples"></a>Esempi di flussi

<!-- see "fundamentals\middleware\request-response\static\TestPipes.JPG for testing sample -->

Si supponga che l'obiettivo sia quello di creare un middleware che legga l'intero corpo della richiesta come un elenco di stringhe, suddividendo le nuove righe. Un'implementazione di flusso semplice potrebbe essere simile alla seguente:

> [!WARNING]
> Il codice seguente:
> * Viene utilizzato per illustrare i problemi relativi alla mancata utilizzo di una pipe per la lettura del corpo della richiesta.
> * Non può essere usato nelle app di produzione.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Questo codice funziona, ma esistono alcuni problemi:

* Prima dell'aggiunta a `StringBuilder`, l'esempio crea un'altra stringa (`encodedString`) che viene eliminata immediatamente. Questo processo si verifica per tutti i byte nel flusso, pertanto il risultato è l'allocazione di memoria aggiuntiva rispetto alle dimensioni dell'intero corpo della richiesta.
* L'esempio legge l'intera stringa prima della suddivisione in corrispondenza delle nuove righe. È più efficiente verificare la presenza di nuove righe nella matrice di byte.

Di seguito è riportato un esempio in cui vengono corretti alcuni dei problemi precedenti:

> [!WARNING]
> Il codice seguente:
> * Viene usato per illustrare le soluzioni ad alcuni problemi del codice precedente, senza risolvere tutti i problemi.
> * Non può essere usato nelle app di produzione.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Questo esempio precedente:

* Non memorizza nel buffer l'intero corpo della richiesta in un `StringBuilder`, a meno che non siano presenti caratteri di nuova riga.
* Non chiama `Split` sulla stringa.

Tuttavia, esistono ancora sono alcuni problemi:

* Se i caratteri di nuova riga sono di tipo sparse, gran parte del corpo della richiesta viene memorizzato nel buffer nella stringa.
* Il codice continua a creare stringhe ( `remainingString` ) e le aggiunge al buffer di stringa, il che comporta un'allocazione aggiuntiva.

Questi problemi sono risolvibili, ma il codice sta diventando progressivamente più complicato con un lieve miglioramento. Le pipeline consentono di risolvere questi problemi con complicazioni minime per il codice.

## <a name="pipelines"></a>Pipelines

Nell'esempio seguente viene illustrato come è possibile gestire lo stesso scenario utilizzando un [PipeReader](/dotnet/standard/io/pipelines#pipe):

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Questo esempio consente di risolvere molti problemi delle implementazioni dei flussi:

* Non è necessario un buffer di stringa perché gestisce i `PipeReader` byte che non sono stati usati.
* Le stringhe codificate vengono aggiunte direttamente all'elenco di stringhe restituite.
* A parte la `ToArray` chiamata e la memoria usata dalla stringa, la creazione di stringhe è priva di allocazione.

## <a name="adapters"></a>adapter

Le `Body` `BodyReader` proprietà, e `BodyWriter` sono disponibili per `HttpRequest` e `HttpResponse` . Quando si imposta `Body` su un flusso diverso, un nuovo set di adapter adatta automaticamente ogni tipo all'altro. Se si imposta `HttpRequest.Body` su un nuovo flusso, `HttpRequest.BodyReader` viene impostato automaticamente su un nuovo oggetto `PipeReader` che esegue il wrapping `HttpRequest.Body` .

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync` viene utilizzato per indicare che le intestazioni non sono modificabili e per eseguire i `OnStarting` callback. Quando si usa gheppio come server, `StartAsync` la chiamata di prima di usare `PipeReader` garantisce che la memoria restituita da `GetMemory` appartenga a un buffer interno di Gheppio <xref:System.IO.Pipelines.Pipe> anziché a un buffer esterno.

## <a name="additional-resources"></a>Risorse aggiuntive

* [System. IO. Pipelines in .NET](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
