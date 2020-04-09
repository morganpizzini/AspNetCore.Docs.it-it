---
title: Operazioni di richiesta e risposta in ASP.NET Core
author: jkotalik
description: Informazioni su come leggere il corpo della richiesta e scrivere il corpo della risposta in ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b473fa02e1d23f02bc5d2e15fa54ab7b1dbbb17c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667216"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Operazioni di richiesta e risposta in ASP.NET Core

Di [Justin Kotalik](https://github.com/jkotalik)

Questo articolo illustra come leggere dal corpo della richiesta e scrivere nel corpo della risposta. Il codice per queste operazioni potrebbe essere necessario durante la scrittura di middleware. Al di fuori della scrittura middleware, il codice personalizzato non è in genere necessario perché le operazioni vengono gestite da MVC e pagine Razor.

Esistono due astrazioni per i corpi <xref:System.IO.Stream> di <xref:System.IO.Pipelines.Pipe>richiesta e risposta: e . Per la lettura della richiesta, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) è un <xref:System.IO.Stream> e `HttpRequest.BodyReader` è un <xref:System.IO.Pipelines.PipeReader>. Per la scrittura della risposta, <xref:System.IO.Stream> [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) è un oggetto e `HttpResponse.BodyWriter` viene . <xref:System.IO.Pipelines.PipeWriter>

[Le pipeline](/dotnet/standard/io/pipelines) sono consigliate su flussi. I flussi possono essere più facili da usare per alcune operazioni semplici, ma le pipeline hanno prestazioni migliori e sono più facili da usare nella maggior parte degli scenari. ASP.NET Core sta iniziando a usare le pipeline anziché i flussi internamente. Tra gli esempi sono inclusi:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

I flussi non vengono rimossi dal framework. I flussi continuano a essere utilizzati in .NET e molti `FileStreams` tipi `ResponseCompression`di flusso non dispongono di equivalenti di pipe, ad esempio e .

## <a name="stream-examples"></a>Esempi di flussi

Si supponga che l'obiettivo è quello di creare un middleware che legge l'intero corpo della richiesta come un elenco di stringhe, dividendo su nuove righe. Un'implementazione di flusso semplice potrebbe essere simile alla seguente:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Questo codice funziona, ma esistono alcuni problemi:

* Prima dell'aggiunta a `StringBuilder`, l'esempio crea un'altra stringa (`encodedString`) che viene eliminata immediatamente. Questo processo si verifica per tutti i byte nel flusso, pertanto il risultato è l'allocazione di memoria aggiuntiva rispetto alle dimensioni dell'intero corpo della richiesta.
* L'esempio legge l'intera stringa prima della suddivisione in corrispondenza delle nuove righe. È più efficiente verificare la presenza di nuove righe nella matrice di byte.

Ecco un esempio che risolve alcuni dei problemi precedenti:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Questo esempio precedente:

* Non memorizza nel buffer l'intero corpo della richiesta in un `StringBuilder`, a meno che non siano presenti caratteri di nuova riga.
* Non chiama `Split` sulla stringa.

Tuttavia, esistono ancora sono alcuni problemi:

* Se i caratteri di nuova riga sono di tipo sparse, nella stringa viene memorizzata nel buffer gran parte del corpo della richiesta.
* Il codice continua a`remainingString`creare stringhe ( ) e le aggiunge al buffer di stringa, generando un'allocazione aggiuntiva.

Questi problemi sono risolvibili, ma il codice sta diventando progressivamente più complicato con pochi miglioramenti. Le pipeline consentono di risolvere questi problemi con complicazioni minime per il codice.

## <a name="pipelines"></a>Pipeline

L'esempio seguente mostra come è possibile gestire lo stesso scenario con un `PipeReader`:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Questo esempio consente di risolvere molti problemi delle implementazioni dei flussi:

* Non è necessario un buffer di `PipeReader` stringa perché gli handle di byte che non sono stati utilizzati.
* Le stringhe codificate vengono aggiunte direttamente all'elenco di stringhe restituite.
* La creazione di stringhe non comporta allocazioni, oltre alla memoria usata dalla stringa (ad eccezione della chiamata `ToArray()`).

## <a name="adapters"></a>adapter

`Body` Entrambe `BodyReader/BodyWriter` le proprietà `HttpRequest` `HttpResponse`sono disponibili per e . Quando si `Body` imposta un flusso diverso, un nuovo set di adattatori adatta automaticamente ogni tipo all'altro. Se si `HttpRequest.Body` imposta su `HttpRequest.BodyReader` un nuovo flusso, `PipeReader` viene `HttpRequest.Body`impostato automaticamente su un nuovo che esegue il wrapping di .

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync`viene utilizzato per indicare che le intestazioni `OnStarting` non sono modificabili e per eseguire callback. Quando si utilizza Kestrel `StartAsync` come server, la chiamata prima di utilizzare le `PipeReader` garanzie che la memoria restituita da `GetMemory` appartiene al buffer interno <xref:System.IO.Pipelines.Pipe> di Kestrel anziché a un buffer esterno.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Introducing System.IO.Pipelines](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/) (Introduzione a System.IO.Pipelines)
* <xref:fundamentals/middleware/write>
