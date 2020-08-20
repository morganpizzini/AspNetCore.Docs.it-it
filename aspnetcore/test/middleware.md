---
title: Test del middleware ASP.NET Core
author: tratcher
description: Informazioni su come testare ASP.NET Core middleware con TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
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
uid: test/middleware
ms.openlocfilehash: ed0925259bf3d4fee6c903ff55cdf1dae2355af7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631901"
---
# <a name="test-aspnet-core-middleware"></a>Test del middleware ASP.NET Core

Di [Chris Ross](https://github.com/Tratcher)

Il middleware può essere testato in isolamento con <xref:Microsoft.AspNetCore.TestHost.TestServer> . Consente di:

* Creare un'istanza di una pipeline dell'app contenente solo i componenti che è necessario testare.
* Inviare richieste personalizzate per verificare il comportamento del middleware.

Vantaggi:

* Le richieste vengono inviate in memoria anziché essere serializzate sulla rete.
* In questo modo si evitano ulteriori problemi, ad esempio la gestione delle porte e i certificati HTTPS.
* Le eccezioni nel middleware possono fluire direttamente sul test chiamante.
* È possibile personalizzare le strutture dei dati del server, ad esempio <xref:Microsoft.AspNetCore.Http.HttpContext> , direttamente nel test.

## <a name="set-up-the-testserver"></a>Configurare TestServer

Nel progetto di test creare un test:

* Compilare e avviare un host che usa <xref:Microsoft.AspNetCore.TestHost.TestServer> .
* Aggiungere tutti i servizi necessari usati dal middleware.
* Aggiungere il pacchetto NuGet [Microsoft. AspNetCore. TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) al progetto:
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* Configurare la pipeline di elaborazione per l'uso del middleware per il test.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>Inviare richieste con HttpClient
Invia una richiesta tramite <xref:System.Net.Http.HttpClient> :

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Asserire il risultato. Prima di tutto, creare un'asserzione anziché il risultato previsto. Un'esecuzione iniziale con un'asserzione falsa positiva conferma che il test ha esito negativo quando il middleware viene eseguito correttamente. Eseguire il test e verificare che il test abbia esito negativo.

Nell'esempio seguente il middleware deve restituire un codice di stato 404 (*non trovato*) quando viene richiesto l'endpoint radice. Eseguire la prima esecuzione dei test con `Assert.NotEqual( ... );` , che dovrebbe avere esito negativo:

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Modificare l'asserzione per testare il middleware in condizioni operative normali. Il test finale utilizza `Assert.Equal( ... );` . Eseguire di nuovo il test per verificare che venga superato.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>Inviare richieste con HttpContext

Un'app di test può anche inviare una richiesta tramite [SendAsync (Action \<HttpContext> , CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A). Nell'esempio seguente vengono eseguiti diversi controlli quando `https://example.com/A/Path/?and=query` viene elaborato dal middleware:

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> consente la configurazione diretta di un <xref:Microsoft.AspNetCore.Http.HttpContext> oggetto anziché l'utilizzo delle <xref:System.Net.Http.HttpClient> astrazioni. Usare <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> per modificare le strutture disponibili solo sul server, ad esempio [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) o [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).

Come per l'esempio precedente che ha testato una risposta *404 non trovata* , controllare l'opposto per ogni `Assert` istruzione del test precedente. Il controllo conferma che il test ha esito negativo correttamente quando il middleware funziona normalmente. Dopo aver verificato il funzionamento del test falso positivo, impostare le istruzioni finali `Assert` per le condizioni e i valori previsti per il test. Eseguirlo di nuovo per verificare che il test venga superato.

## <a name="testserver-limitations"></a>Limitazioni di TestServer

TestServer

* È stato creato per replicare i comportamenti del server nel middleware di test.
* Non ***tenta di*** replicare tutti i <xref:System.Net.Http.HttpClient> comportamenti.
* Tenta di concedere al client l'accesso a un maggior controllo sul server e con la massima visibilità su ciò che accade nel server. Ad esempio, può generare eccezioni non generate normalmente da `HttpClient` per comunicare direttamente lo stato del server.
* Per impostazione predefinita, alcune intestazioni specifiche del trasporto non vengono impostate perché non sono in genere rilevanti per il middleware. Per ulteriori informazioni, vedere la sezione successiva.

### <a name="content-length-and-transfer-encoding-headers"></a>Intestazioni Content-Length e Transfer-Encoding

TestServer non ***imposta le*** intestazioni della richiesta o della risposta relative al trasporto, ad esempio [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) o [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding). Le applicazioni devono evitare a seconda di queste intestazioni perché l'utilizzo varia in base a client, scenario e protocollo. Se `Content-Length` e `Transfer-Encoding` sono necessari per testare uno scenario specifico, è possibile specificarli nel test quando si compone <xref:System.Net.Http.HttpRequestMessage> o <xref:Microsoft.AspNetCore.Http.HttpContext> . Per ulteriori informazioni, vedere i seguenti problemi di GitHub:

* [DotNet/aspnetcore # 21677](https://github.com/dotnet/aspnetcore/issues/21677)
* [DotNet/aspnetcore # 18463](https://github.com/dotnet/aspnetcore/issues/18463)
* [DotNet/aspnetcore # 13273](https://github.com/dotnet/aspnetcore/issues/13273)
