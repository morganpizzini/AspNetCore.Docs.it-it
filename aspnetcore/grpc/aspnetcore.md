---
title: Servizi gRPC con ASP.NET Core
author: juntaoluo
description: Scopri i concetti di base quando scrivi servizi gRPC con ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 6107704a4b4d9c629a7abe907efd5b1932019130
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667629"
---
# <a name="grpc-services-with-aspnet-core"></a>Servizi gRPC con ASP.NET Core

Questo documento mostra come iniziare a utilizzare i servizi gRPC usando ASP.NET Core.This document shows how to get started with gRPC services using ASP.NET Core.

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Introduzione all'uso del servizio gRPC in ASP.NET Core

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([procedura per il download](xref:index#how-to-download-a-sample)).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vedere [Introduzione ai servizi gRPC](xref:tutorials/grpc/grpc-start) per istruzioni dettagliate su come creare un progetto gRPC.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

Eseguire `dotnet new grpc -o GrpcGreeter` dalla riga di comando.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Aggiungere servizi gRPC a un'app ASP.NET Core

gRPC richiede il pacchetto [Grpc.AspNetCore.](https://www.nuget.org/packages/Grpc.AspNetCore)

### <a name="configure-grpc"></a>Configurare gRPC

In *Startup.cs*:

* gRPC è abilitato `AddGrpc` con il metodo .
* Ogni servizio gRPC viene aggiunto alla `MapGrpcService` pipeline di routing tramite il metodo.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET core di base e le funzionalità condividono la pipeline di routing, pertanto un'app può essere configurata per servire gestori di richieste aggiuntivi. I gestori richieste aggiuntivi, ad esempio i controller MVC, funzionano in parallelo con i servizi gRPC configurati.

### <a name="configure-kestrel"></a>Configurare Kestrel

Endpoint Kestrel gRPC:

* Richiedi HTTP/2.
* Deve essere protetto con [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).

#### <a name="http2"></a>HTTP/2

gRPC richiede HTTP/2. gRPC per ASP.NET Core convalida [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.

Kestrel [supporta HTTP/2](xref:fundamentals/servers/kestrel#http2-support) nella maggior parte dei sistemi operativi moderni. Gli endpoint Kestrel sono configurati per supportare le connessioni HTTP/1.1 e HTTP/2 per impostazione predefinita.

#### <a name="tls"></a>TLS

Gli endpoint Kestrel utilizzati per gRPC devono essere protetti con TLS. In fase di sviluppo, un endpoint `https://localhost:5001` protetto con TLS viene creato automaticamente quando è presente il certificato di sviluppo ASP.NET Core.In development, an endpoint secured with TLS is automatically created at when the ASP.NET Core development certificate is present. Non è richiesta alcuna configurazione. Un `https` prefisso verifica che l'endpoint Kestrel utilizzi TLS.

Nell'ambiente di produzione, TLS deve essere configurato in modo esplicito. Nell'esempio appsettings.json seguente viene fornito un endpoint HTTP/2 protetto con TLS:In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

In alternativa, gli endpoint Kestrel possono essere configurati in *Program.cs:*

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a>Negoziazione del protocollo

TLS viene utilizzato per più che proteggere le comunicazioni. L'handshake [TLS Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) viene utilizzato per negoziare il protocollo di connessione tra il client e il server quando un endpoint supporta più protocolli. Questa negoziazione determina se la connessione utilizza HTTP/1.1 o HTTP/2.

Se un endpoint HTTP/2 è configurato senza TLS, [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) dell'endpoint deve essere impostato su `HttpProtocols.Http2`. Un endpoint con più protocolli `HttpProtocols.Http1AndHttp2`(ad esempio, ) non può essere utilizzato senza TLS perché non esiste alcuna negoziazione. Tutte le connessioni all'endpoint non protetto vengono predefinite per HTTP/1.1 e le chiamate gRPC hanno esito negativo.

Per ulteriori informazioni sull'abilitazione di HTTP/2 e TLS con Kestrel, vedere [Configurazione dell'endpoint Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> macOS non supporta ASP.NET Core gRPC con TLS. Per eseguire correttamente i servizi gRPC in macOS, è necessaria una configurazione aggiuntiva. Per altre informazioni, vedere [Non è possibile avviare un'app ASP.NET Core gRPC in macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="integration-with-aspnet-core-apis"></a>Integrazione con le API di base di ASP.NET

I servizi gRPC hanno accesso completo alle funzionalità di ASP.NET Core, ad esempio [Inserimento](xref:fundamentals/dependency-injection) delle dipendenze (DI) e [Registrazione](xref:fundamentals/logging/index). Ad esempio, l'implementazione del servizio può risolvere un servizio di logger dal contenitore DI tramite il costruttore:For example, the service implementation can resolve a logger service from the DI container via the constructor:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Per impostazione predefinita, l'implementazione del servizio gRPC può risolvere altri servizi DI con qualsiasi durata (Singleton, Ambito o Transitorio).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Risolvere HttpContext nei metodi gRPCResolve HttpContext in gRPC methods

L'API gRPC fornisce l'accesso ad alcuni dati del messaggio HTTP/2, ad esempio il metodo, l'host, l'intestazione e i trailer. L'accesso `ServerCallContext` è tramite l'argomento passato a ogni metodo gRPC:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`non fornisce l'accesso completo a `HttpContext` tutte le API ASP.NET. Il `GetHttpContext` metodo di estensione `HttpContext` fornisce l'accesso completo al messaggio HTTP/2 sottostante nelle API ASP.NET:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
