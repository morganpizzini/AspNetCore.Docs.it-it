---
title: Autenticazione e autorizzazione in gRPC per ASP.NET Core
author: jamesnk
description: Informazioni su come utilizzare l'autenticazione e l'autorizzazione in gRPC per ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: c0312b186bbb35e3b802984484b7213016d8bf04
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78964433"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Autenticazione e autorizzazione in gRPC per ASP.NET Core

Di [James Newton-King](https://twitter.com/jamesnk)

[Visualizzare o scaricare](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) codice di esempio [(come scaricare)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Autenticare gli utenti che chiamano un servizio gRPCAuthenticate users calling a gRPC service

gRPC può essere utilizzato con [ASP.NET'autenticazione di base](xref:security/authentication/identity) per associare un utente a ogni chiamata.

Di seguito è `Startup.Configure` riportato un esempio di cui utilizza l'autenticazione gRPC e ASP.NET Core:

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> L'ordine in cui si registra il middleware di autenticazione ASP.NET Core è importante. Chiamare `UseAuthentication` sempre `UseAuthorization` `UseRouting` e `UseEndpoints`dopo e prima .

Il meccanismo di autenticazione utilizzato dall'app durante una chiamata deve essere configurato. La configurazione dell'autenticazione viene aggiunta `Startup.ConfigureServices` e sarà diversa a seconda del meccanismo di autenticazione utilizzato dall'app. Per esempi su come proteggere le app ASP.NET Core, vedere Esempi di [autenticazione.](xref:security/authentication/samples)

Una volta che l'autenticazione è stata configurata, `ServerCallContext`l'utente è possibile accedere in un metodi di servizio gRPC tramite il .

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Autenticazione del token bearer

Il client può fornire un token di accesso per l'autenticazione. Il server convalida il token e lo utilizza per identificare l'utente.

Sul server, l'autenticazione con token di connessione viene configurata utilizzando il [middleware JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

Nel client gRPC .NET, il token può essere inviato con chiamate come intestazione:

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

La `ChannelCredentials` configurazione su un canale è un modo alternativo per inviare il token al servizio con chiamate gRPC. La credenziale viene eseguita ogni volta che viene effettuata una chiamata gRPC, evitando la necessità di scrivere codice in più posizioni per passare il token manualmente.

La credenziale nell'esempio seguente configura il canale per l'invio del token con ogni chiamata gRPC:

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>Autenticazione con certificato client

Un client potrebbe in alternativa fornire un certificato client per l'autenticazione. [L'autenticazione](https://tools.ietf.org/html/rfc5246#section-7.4.4) del certificato avviene a livello TLS, molto prima che arrivi a ASP.NET Core.Certificate authentication happens at the TLS level, long before it ever ever to ASP.NET Core. Quando la richiesta entra ASP.NET Core, il pacchetto di [autenticazione](xref:security/authentication/certauth) del certificato client consente di risolvere il certificato in un `ClaimsPrincipal`file .

> [!NOTE]
> L'host deve essere configurato per accettare i certificati client. Vedere [Configurare l'host in modo che richieda certificati](xref:security/authentication/certauth#configure-your-host-to-require-certificates) per informazioni sull'accettazione dei certificati client in Kestrel, IIS e Azure.See configure your host to require certificates for information on accepting client certificates in Kestrel, IIS and Azure.

Nel client gRPC .NET, il certificato `HttpClientHandler` client viene aggiunto a che viene quindi utilizzato per creare il client gRPC:

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Altri meccanismi di autenticazione

Molti ASP.NET meccanismi di autenticazione supportati da Core funzionano con gRPC:

* Azure Active Directory
* Certificato client
* Server identità
* Token JWT
* OAuth 2.0
* OpenID Connect
* WS-Federation

Per ulteriori informazioni sulla configurazione dell'autenticazione nel server, vedere [ASP.NET autenticazione di base](xref:security/authentication/identity).

La configurazione del client gRPC per l'utilizzo dell'autenticazione dipende dal meccanismo di autenticazione in uso. Gli esempi di token di connessione e certificato client precedenti mostrano un paio di modi in cui il client gRPC può essere configurato per inviare metadati di autenticazione con chiamate gRPC:

* I client gRPC `HttpClient` fortemente tipati utilizzano internamente. L'autenticazione può essere configurata su [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)o `HttpClient`aggiungendo istanze [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) personalizzate all'oggetto .
* Ogni chiamata gRPC `CallOptions` ha un argomento facoltativo. Le intestazioni personalizzate possono essere inviate utilizzando la raccolta di intestazioni dell'opzione.

> [!NOTE]
> L'autenticazione di Windows (NTLM/Kerberos/Negotiate) non può essere utilizzata con gRPC. gRPC richiede HTTP/2 e HTTP/2 non supporta l'autenticazione di Windows.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autorizzare gli utenti ad accedere a servizi e metodi di servizioAuthorize users to access services and service methods

Per impostazione predefinita, tutti i metodi in un servizio possono essere chiamati da utenti non autenticati. Per richiedere l'autenticazione, applicare l'attributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) al servizio:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

È possibile utilizzare gli argomenti `[Authorize]` del costruttore e le proprietà dell'attributo per limitare l'accesso solo agli utenti che corrispondono a criteri di [autorizzazione](xref:security/authorization/policies)specifici. Ad esempio, se si dispone `MyAuthorizationPolicy`di un criterio di autorizzazione personalizzato denominato , assicurarsi che solo gli utenti che corrispondono a tale criterio possano accedere al servizio utilizzando il codice seguente:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Ai singoli metodi `[Authorize]` di servizio può essere applicato anche l'attributo. Se l'utente corrente non corrisponde ai criteri applicati sia al metodo **che** alla classe, viene restituito un errore al chiamante:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Bearer Token authentication in ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Configurare l'autenticazione del certificato client in ASP.NET CoreConfigure Client Certificate authentication in ASP.NET Core](xref:security/authentication/certauth)
