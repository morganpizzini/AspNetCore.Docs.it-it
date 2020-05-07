---
title: Test del middleware ASP.NET Core
author: tratcher
description: Informazioni su come testare ASP.NET Core middleware con TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876437"
---
# <a name="test-aspnet-core-middleware"></a>Test del middleware ASP.NET Core

Di [Chris Ross](https://github.com/Tratcher)

Il middleware può essere testato in isolamento <xref:Microsoft.AspNetCore.TestHost.TestServer>con. Consente di:

* Creare un'istanza di una pipeline dell'app contenente solo i componenti che è necessario testare.
* Inviare richieste personalizzate per verificare il comportamento del middleware.

Vantaggi:

* Le richieste vengono inviate in memoria anziché essere serializzate sulla rete.
* In questo modo si evitano ulteriori problemi, ad esempio la gestione delle porte e i certificati HTTPS.
* Le eccezioni nel middleware possono fluire direttamente sul test chiamante.
* È possibile personalizzare le strutture dei dati del server, ad <xref:Microsoft.AspNetCore.Http.HttpContext>esempio, direttamente nel test.

## <a name="set-up-the-testserver"></a>Configurare TestServer

Nel progetto di test creare un test:

* Compilare e avviare un host che usa <xref:Microsoft.AspNetCore.TestHost.TestServer>.
* Aggiungere tutti i servizi necessari usati dal middleware.
* Configurare la pipeline di elaborazione per l'uso del middleware per il test.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>Inviare richieste con HttpClient
Invia una richiesta tramite <xref:System.Net.Http.HttpClient>:

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Asserire il risultato. Prima di tutto, creare un'asserzione anziché il risultato previsto. Un'esecuzione iniziale con un'asserzione falsa positiva conferma che il test ha esito negativo quando il middleware viene eseguito correttamente. Eseguire il test e verificare che il test abbia esito negativo.

Nell'esempio seguente il middleware deve restituire un codice di stato 404 (*non trovato*) quando viene richiesto l'endpoint radice. Eseguire la prima esecuzione dei test `Assert.NotEqual( ... );`con, che dovrebbe avere esito negativo:

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Modificare l'asserzione per testare il middleware in condizioni operative normali. Il test finale utilizza `Assert.Equal( ... );`. Eseguire di nuovo il test per verificare che venga superato.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>Inviare richieste con HttpContext

Un'app di test può anche inviare una richiesta usando [SendAsync (\<Action HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A). Nell'esempio seguente vengono eseguiti diversi controlli quando `https://example.com/A/Path/?and=query` viene elaborato dal middleware:

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

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>consente la configurazione diretta di <xref:Microsoft.AspNetCore.Http.HttpContext> un oggetto anziché l'utilizzo <xref:System.Net.Http.HttpClient> delle astrazioni. Usare <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> per modificare le strutture disponibili solo sul server, ad esempio [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) o [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).

Come per l'esempio precedente che ha testato una risposta *404 non trovata* , controllare l'opposto per ogni `Assert` istruzione del test precedente. Il controllo conferma che il test ha esito negativo correttamente quando il middleware funziona normalmente. Dopo aver verificato il funzionamento del test falso positivo, impostare le istruzioni finali `Assert` per le condizioni e i valori previsti per il test. Eseguirlo di nuovo per verificare che il test venga superato.
