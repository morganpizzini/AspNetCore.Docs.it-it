---
title: Usare gRPC nelle app del browser
author: jamesnk
description: Informazioni su come configurare i servizi gRPC in ASP.NET Core per essere richiamabili dalle app del browser usando gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 6f66a94b41e6e13550396e2e19fdf48f9dc63d46
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106598"
---
# <a name="use-grpc-in-browser-apps"></a>Usare gRPC nelle app del browser

Di [James Newton-King](https://twitter.com/jamesnk)

Non è possibile chiamare un servizio HTTP/2 gRPC da un'app basata su browser. [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) è un protocollo che consente alle app e JavaScript del browser Blazor di chiamare i servizi gRPC. Questo articolo illustra come usare gRPC-Web in .NET Core.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web in ASP.NET Core e inviato

Sono disponibili due opzioni per l'aggiunta di gRPC-Web a un'app ASP.NET Core:

* Supportare gRPC-Web insieme a gRPC HTTP/2 in ASP.NET Core. Questa opzione utilizza il middleware fornito dal `Grpc.AspNetCore.Web` pacchetto.
* Usare il supporto gRPC-Web del [proxy inviato](https://www.envoyproxy.io/) per tradurre GRPC-Web in gRPC http/2. La chiamata tradotta viene quindi trasmessa nell'app ASP.NET Core.

Ogni approccio presenta vantaggi e svantaggi. Se si usa già l'inviato come proxy nell'ambiente dell'app, potrebbe essere utile usarlo anche per fornire il supporto gRPC-Web. Se si vuole una soluzione semplice per gRPC-Web che richiede solo ASP.NET Core, `Grpc.AspNetCore.Web` è una scelta ottimale.

## <a name="configure-grpc-web-in-aspnet-core"></a>Configurare gRPC-Web in ASP.NET Core

i servizi gRPC ospitati in ASP.NET Core possono essere configurati per supportare gRPC-Web insieme a HTTP/2 gRPC. gRPC-Web non richiede alcuna modifica ai servizi. L'unica modifica è la configurazione di avvio.

Per abilitare gRPC-Web con un servizio gRPC ASP.NET Core:

* Aggiungere un riferimento al pacchetto [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .
* Configurare l'app per l'uso di gRPC-Web aggiungendo `UseGrpcWeb` e `EnableGrpcWeb` a *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Il codice precedente:

* Aggiunge il middleware gRPC-Web, `UseGrpcWeb` , dopo il routing e prima degli endpoint.
* Specifica che il `endpoints.MapGrpcService<GreeterService>()` metodo supporta gRPC-Web con `EnableGrpcWeb` . 

In alternativa, è possibile configurare il middleware gRPC-Web in modo che tutti i servizi supportino gRPC-Web per impostazione predefinita e `EnableGrpcWeb` non siano necessari. Specificare `new GrpcWebOptions { DefaultEnabled = true }` quando viene aggiunto il middleware.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> Esiste un problema noto che causa l'esito negativo di gRPC-Web quando è [ospitato da http. sys](xref:fundamentals/servers/httpsys) in .NET Core 3. x.
>
> Una soluzione alternativa per ottenere gRPC-Web che lavora su http. sys è disponibile [qui](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web e CORS

La sicurezza del browser impedisce a una pagina Web di eseguire richieste a un dominio diverso da quello che ha gestito la pagina Web. Questa restrizione si applica all'esecuzione di chiamate gRPC-Web con le app browser. Ad esempio, un'app browser servita da `https://www.contoso.com` viene bloccata dalla chiamata a gRPC-Web Services ospitata in `https://services.contoso.com` . La condivisione di risorse tra le origini (CORS) può essere usata per attenuare questa restrizione.

Per consentire all'app browser di eseguire chiamate web gRPC tra le origini, configurare [CORS in ASP.NET Core](xref:security/cors). Usare il supporto CORS incorporato ed esporre intestazioni specifiche di gRPC con <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Il codice precedente:

* Chiama `AddCors` per aggiungere servizi CORS e configura un criterio CORS che espone intestazioni specifiche di gRPC.
* Chiama `UseCors` per aggiungere il middleware CORS dopo il routing e prima degli endpoint.
* Specifica che il `endpoints.MapGrpcService<GreeterService>()` metodo supporta cors con `RequiresCors` .

## <a name="call-grpc-web-from-the-browser"></a>Chiamare gRPC-Web dal browser

Le app browser possono usare gRPC-Web per chiamare i servizi gRPC. Esistono alcuni requisiti e limitazioni quando si chiamano i servizi gRPC con gRPC-Web dal browser:

* Il server deve essere stato configurato per supportare gRPC-Web.
* Il flusso client e le chiamate di streaming bidirezionali non sono supportate. Il flusso del server è supportato.
* Per chiamare i servizi gRPC in un dominio diverso è necessario configurare [CORS](xref:security/cors) nel server.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-client Web

È disponibile un client JavaScript gRPC-Web. Per istruzioni su come usare gRPC-Web da JavaScript, vedere [scrivere codice client JavaScript con gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Configurare gRPC-Web con il client gRPC .NET

Il client gRPC .NET può essere configurato per effettuare chiamate gRPC-Web. Questa operazione è utile per le app [ Blazor webassembly](xref:blazor/index#blazor-webassembly) , che sono ospitate nel browser e hanno le stesse limitazioni http del codice JavaScript. La chiamata di gRPC-Web con un client .NET equivale a [http/2 gRPC](xref:grpc/client). L'unica modifica è la modalità di creazione del canale.

Per usare gRPC-Web:

* Aggiungere un riferimento al pacchetto [Grpc .NET. client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .
* Verificare che il riferimento a [Grpc .NET. client](https://www.nuget.org/packages/Grpc.Net.Client) Package sia 2.29.0 o versione successiva.
* Configurare il canale per l'utilizzo di `GrpcWebHandler` :

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Il codice precedente:

* Configura un canale per l'uso di gRPC-Web.
* Crea un client ed effettua una chiamata utilizzando il canale.

`GrpcWebHandler`in sono disponibili le opzioni di configurazione seguenti:

* **InnerHandler**: oggetto sottostante <xref:System.Net.Http.HttpMessageHandler> che esegue la richiesta http gRPC, ad esempio `HttpClientHandler` .
* **GrpcWebMode**: tipo di enumerazione che specifica se la richiesta http gRPC `Content-Type` è `application/grpc-web` o `application/grpc-web-text` .
    * `GrpcWebMode.GrpcWeb`Configura il contenuto da inviare senza codifica. Valore predefinito.
    * `GrpcWebMode.GrpcWebText`Configura il contenuto con codifica Base64. Obbligatorio per le chiamate di streaming del server nei browser.
* **HttpVersion**: protocollo http `Version` usato per impostare [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) sulla richiesta http gRPC sottostante. gRPC-Web non richiede una versione specifica e non esegue l'override dell'impostazione predefinita, a meno che non sia specificato.

> [!IMPORTANT]
> I client gRPC generati hanno metodi Sync e Async per la chiamata di metodi unaria. Ad esempio, `SayHello` è Sync ed `SayHelloAsync` è Async. La chiamata di un metodo di sincronizzazione in un' Blazor app Webassembly provocherà la mancata risposta dell'app. I metodi asincroni devono essere sempre usati in Blazor webassembly.

## <a name="additional-resources"></a>Risorse aggiuntive

* [progetto GitHub di gRPC per client Web](https://github.com/grpc/grpc-web)
* <xref:security/cors>
