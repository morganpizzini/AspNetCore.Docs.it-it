---
title: Usare gRPC nelle app del browser
author: jamesnk
description: Informazioni su come configurare i servizi gRPC su ASP.NET Core come richiamabili dalle app browser tramite gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 0bb8157525ccd32991d8925816c1b599c3d21a92
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977145"
---
# <a name="use-grpc-in-browser-apps"></a>Usare gRPC nelle app del browser

Di [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **Il supporto gRPC-Web in .NET è sperimentale**
>
> gRPC-Web per .NET è un progetto sperimentale, non un prodotto impegnato. Vogliamo:
>
> * Verificare che il nostro approccio all'implementazione di gRPC-Web funzioni.
> * Ottenere commenti e suggerimenti su se questo approccio è utile per gli sviluppatori .NET rispetto al modo tradizionale di configurare gRPC-Web tramite un proxy.
>
> Si prega [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) di lasciare un feedback a per garantire che costruiamo qualcosa che gli sviluppatori piace e sono produttivi con.

Non è possibile chiamare un servizio gRPC HTTP/2 da un'app basata su browser. [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) è un protocollo che consente alle app JavaScript e Blazor del browser di chiamare i servizi gRPC. In questo articolo viene illustrato come utilizzare gRPC-Web in .NET Core.

## <a name="configure-grpc-web-in-aspnet-core"></a>Configurare gRPC-Web in ASP.NET Core

I servizi gRPC ospitati in ASP.NET Core possono essere configurati per supportare gRPC-Web insieme a HTTP/2 gRPC. gRPC-Web non richiede alcuna modifica ai servizi. L'unica modifica è la configurazione di avvio.

Per abilitare gRPC-Web con un servizio gRPC ASP.NET Core:

* Aggiungere un riferimento al pacchetto [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)
* Configura l'app per l'utilizzo `AddGrpcWeb` `UseGrpcWeb` di gRPC-Web aggiungendo e *Startup.cs:*

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Il codice precedente:

* Aggiunge il middleware gRPC-Web, `UseGrpcWeb`, dopo il routing e prima degli endpoint.
* Specifica che `endpoints.MapGrpcService<GreeterService>()` il metodo supporta gRPC-Web con `EnableGrpcWeb`. 

In alternativa, configurare tutti i servizi `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` per supportare gRPC-Web aggiungendo a ConfigureServices.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> Esiste un problema noto che causa l'esito negativo di gRPC-Web quando [ospitato da Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.
>
> Una soluzione alternativa per ottenere gRPC-Web lavorando su Http.sys è disponibile [qui](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web e CORS

La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina web. Questa restrizione si applica all'esecuzione di chiamate gRPC-Web con le app browser. Ad esempio, a un'app browser servita da `https://www.contoso.com` viene `https://services.contoso.com`impedito di chiamare servizi Web gRPC ospitati su . Condivisione tra risorse tra origini (CORS, Cross Origin Resource Sharing) può essere utilizzato per allentare questa restrizione.

Per consentire all'app browser di effettuare chiamate gRPC-Web di origine incrociata, configura [CORS in ASP.NET Core](xref:security/cors). Utilizzare il supporto CORS incorporato ed esporre intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>specifiche di gRPC con .

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Il codice precedente:

* Chiamate `AddCors` per aggiungere servizi CORS e configura un criterio CORS che espone intestazioni specifiche gRPC.
* Chiamate `UseCors` per aggiungere il middleware CORS dopo il routing e prima degli endpoint.
* Specifica che `endpoints.MapGrpcService<GreeterService>()` il metodo supporta `RequiresCors`CORS con .

## <a name="call-grpc-web-from-the-browser"></a>Chiamare gRPC-Web dal browser

Le app browser possono utilizzare gRPC-Web per chiamare i servizi gRPC. Esistono alcuni requisiti e limitazioni quando si chiamano i servizi gRPC con gRPC-Web dal browser:

* Il server deve essere stato configurato per supportare gRPC-Web.
* Lo streaming client e le chiamate di streaming bidirezionale non sono supportati. Lo streaming del server è supportato.
* La chiamata ai servizi gRPC su un dominio diverso richiede la configurazione di [CORS](xref:security/cors) sul server.

### <a name="javascript-grpc-web-client"></a>Client JavaScript gRPC-Web

È disponibile un client JavaScript gRPC-Web. Per istruzioni su come utilizzare gRPC-Web da JavaScript, consultate [Scrivere codice client JavaScript con gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Configurare gRPC-Web con il client gRPC .NET

Il client gRPC .NET può essere configurato per effettuare chiamate gRPC-Web. Ciò è utile per le applicazioni [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) che sono ospitate nel browser e hanno le stesse limitazioni HTTP del codice JavaScript. Chiamare gRPC-Web con un client .NET equivale [a HTTP/2 gRPC](xref:grpc/client). L'unica modifica è la modalità di creazione del canale.

Per utilizzare gRPC-Web:

* Aggiungere un riferimento al pacchetto [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)
* Verificare che il riferimento al pacchetto [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) sia 2.27.0 o superiore.
* Configurare il canale `GrpcWebHandler`per l'utilizzo di :

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Il codice precedente:

* Configura un canale per l'utilizzo di gRPC-Web.
* Crea un client ed effettua una chiamata utilizzando il canale.

L'utente `GrpcWebHandler` ha le seguenti opzioni di configurazione al momento della creazione:

* **InnerHandler:** sottostante <xref:System.Net.Http.HttpMessageHandler> che effettua la richiesta HTTP `HttpClientHandler`gRPC, ad esempio .
* Mode ( **Modalità**) : Tipo di `Content-Type` enumerazione che specifica se la richiesta di richiesta HTTP gRPC è `application/grpc-web` o . `application/grpc-web-text`
    * `GrpcWebMode.GrpcWeb`configura l'invio del contenuto senza codifica. Valore predefinito.
    * `GrpcWebMode.GrpcWebText`configura il contenuto in base 64 codificato. Obbligatorio per le chiamate di streaming server nei browser.
* **HttpVersion:** protocollo `Version` HTTP utilizzato per impostare [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) nella richiesta HTTP gRPC sottostante. gRPC-Web non richiede una versione specifica e non sostituisce il valore predefinito a meno che non venga specificato.

> [!IMPORTANT]
> I client gRPC generati dispongono di metodi sincroni e asincroni per la chiamata di metodi unario. Ad esempio, `SayHello` è `SayHelloAsync` la sincronizzazione ed è asincrona. La chiamata di un metodo di sincronizzazione in un'applicazione Blazor WebAssembly causerà l'app non risponde. I metodi asincroni devono essere sempre utilizzati in Blazor WebAssembly.

## <a name="additional-resources"></a>Risorse aggiuntive

* [progetto gRPC per client Web GitHub](https://github.com/grpc/grpc-web)
* <xref:security/cors>
