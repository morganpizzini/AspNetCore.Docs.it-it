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
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="98187-103">Autenticazione e autorizzazione in gRPC per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98187-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="98187-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="98187-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="98187-105">[Visualizzare o scaricare](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) codice di esempio [(come scaricare)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="98187-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="98187-106">Autenticare gli utenti che chiamano un servizio gRPCAuthenticate users calling a gRPC service</span><span class="sxs-lookup"><span data-stu-id="98187-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="98187-107">gRPC può essere utilizzato con [ASP.NET'autenticazione di base](xref:security/authentication/identity) per associare un utente a ogni chiamata.</span><span class="sxs-lookup"><span data-stu-id="98187-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="98187-108">Di seguito è `Startup.Configure` riportato un esempio di cui utilizza l'autenticazione gRPC e ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="98187-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="98187-109">L'ordine in cui si registra il middleware di autenticazione ASP.NET Core è importante.</span><span class="sxs-lookup"><span data-stu-id="98187-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="98187-110">Chiamare `UseAuthentication` sempre `UseAuthorization` `UseRouting` e `UseEndpoints`dopo e prima .</span><span class="sxs-lookup"><span data-stu-id="98187-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="98187-111">Il meccanismo di autenticazione utilizzato dall'app durante una chiamata deve essere configurato.</span><span class="sxs-lookup"><span data-stu-id="98187-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="98187-112">La configurazione dell'autenticazione viene aggiunta `Startup.ConfigureServices` e sarà diversa a seconda del meccanismo di autenticazione utilizzato dall'app.</span><span class="sxs-lookup"><span data-stu-id="98187-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="98187-113">Per esempi su come proteggere le app ASP.NET Core, vedere Esempi di [autenticazione.](xref:security/authentication/samples)</span><span class="sxs-lookup"><span data-stu-id="98187-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="98187-114">Una volta che l'autenticazione è stata configurata, `ServerCallContext`l'utente è possibile accedere in un metodi di servizio gRPC tramite il .</span><span class="sxs-lookup"><span data-stu-id="98187-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="98187-115">Autenticazione del token bearer</span><span class="sxs-lookup"><span data-stu-id="98187-115">Bearer token authentication</span></span>

<span data-ttu-id="98187-116">Il client può fornire un token di accesso per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="98187-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="98187-117">Il server convalida il token e lo utilizza per identificare l'utente.</span><span class="sxs-lookup"><span data-stu-id="98187-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="98187-118">Sul server, l'autenticazione con token di connessione viene configurata utilizzando il [middleware JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="98187-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="98187-119">Nel client gRPC .NET, il token può essere inviato con chiamate come intestazione:</span><span class="sxs-lookup"><span data-stu-id="98187-119">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

<span data-ttu-id="98187-120">La `ChannelCredentials` configurazione su un canale è un modo alternativo per inviare il token al servizio con chiamate gRPC.</span><span class="sxs-lookup"><span data-stu-id="98187-120">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="98187-121">La credenziale viene eseguita ogni volta che viene effettuata una chiamata gRPC, evitando la necessità di scrivere codice in più posizioni per passare il token manualmente.</span><span class="sxs-lookup"><span data-stu-id="98187-121">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="98187-122">La credenziale nell'esempio seguente configura il canale per l'invio del token con ogni chiamata gRPC:</span><span class="sxs-lookup"><span data-stu-id="98187-122">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="98187-123">Autenticazione con certificato client</span><span class="sxs-lookup"><span data-stu-id="98187-123">Client certificate authentication</span></span>

<span data-ttu-id="98187-124">Un client potrebbe in alternativa fornire un certificato client per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="98187-124">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="98187-125">[L'autenticazione](https://tools.ietf.org/html/rfc5246#section-7.4.4) del certificato avviene a livello TLS, molto prima che arrivi a ASP.NET Core.Certificate authentication happens at the TLS level, long before it ever ever to ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="98187-125">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="98187-126">Quando la richiesta entra ASP.NET Core, il pacchetto di [autenticazione](xref:security/authentication/certauth) del certificato client consente di risolvere il certificato in un `ClaimsPrincipal`file .</span><span class="sxs-lookup"><span data-stu-id="98187-126">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="98187-127">L'host deve essere configurato per accettare i certificati client.</span><span class="sxs-lookup"><span data-stu-id="98187-127">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="98187-128">Vedere [Configurare l'host in modo che richieda certificati](xref:security/authentication/certauth#configure-your-host-to-require-certificates) per informazioni sull'accettazione dei certificati client in Kestrel, IIS e Azure.See configure your host to require certificates for information on accepting client certificates in Kestrel, IIS and Azure.</span><span class="sxs-lookup"><span data-stu-id="98187-128">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="98187-129">Nel client gRPC .NET, il certificato `HttpClientHandler` client viene aggiunto a che viene quindi utilizzato per creare il client gRPC:</span><span class="sxs-lookup"><span data-stu-id="98187-129">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="98187-130">Altri meccanismi di autenticazione</span><span class="sxs-lookup"><span data-stu-id="98187-130">Other authentication mechanisms</span></span>

<span data-ttu-id="98187-131">Molti ASP.NET meccanismi di autenticazione supportati da Core funzionano con gRPC:</span><span class="sxs-lookup"><span data-stu-id="98187-131">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="98187-132">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="98187-132">Azure Active Directory</span></span>
* <span data-ttu-id="98187-133">Certificato client</span><span class="sxs-lookup"><span data-stu-id="98187-133">Client Certificate</span></span>
* <span data-ttu-id="98187-134">Server identità</span><span class="sxs-lookup"><span data-stu-id="98187-134">IdentityServer</span></span>
* <span data-ttu-id="98187-135">Token JWT</span><span class="sxs-lookup"><span data-stu-id="98187-135">JWT Token</span></span>
* <span data-ttu-id="98187-136">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="98187-136">OAuth 2.0</span></span>
* <span data-ttu-id="98187-137">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="98187-137">OpenID Connect</span></span>
* <span data-ttu-id="98187-138">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="98187-138">WS-Federation</span></span>

<span data-ttu-id="98187-139">Per ulteriori informazioni sulla configurazione dell'autenticazione nel server, vedere [ASP.NET autenticazione di base](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="98187-139">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="98187-140">La configurazione del client gRPC per l'utilizzo dell'autenticazione dipende dal meccanismo di autenticazione in uso.</span><span class="sxs-lookup"><span data-stu-id="98187-140">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="98187-141">Gli esempi di token di connessione e certificato client precedenti mostrano un paio di modi in cui il client gRPC può essere configurato per inviare metadati di autenticazione con chiamate gRPC:</span><span class="sxs-lookup"><span data-stu-id="98187-141">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="98187-142">I client gRPC `HttpClient` fortemente tipati utilizzano internamente.</span><span class="sxs-lookup"><span data-stu-id="98187-142">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="98187-143">L'autenticazione può essere configurata su [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)o `HttpClient`aggiungendo istanze [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) personalizzate all'oggetto .</span><span class="sxs-lookup"><span data-stu-id="98187-143">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="98187-144">Ogni chiamata gRPC `CallOptions` ha un argomento facoltativo.</span><span class="sxs-lookup"><span data-stu-id="98187-144">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="98187-145">Le intestazioni personalizzate possono essere inviate utilizzando la raccolta di intestazioni dell'opzione.</span><span class="sxs-lookup"><span data-stu-id="98187-145">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="98187-146">L'autenticazione di Windows (NTLM/Kerberos/Negotiate) non può essere utilizzata con gRPC.</span><span class="sxs-lookup"><span data-stu-id="98187-146">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="98187-147">gRPC richiede HTTP/2 e HTTP/2 non supporta l'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="98187-147">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="98187-148">Autorizzare gli utenti ad accedere a servizi e metodi di servizioAuthorize users to access services and service methods</span><span class="sxs-lookup"><span data-stu-id="98187-148">Authorize users to access services and service methods</span></span>

<span data-ttu-id="98187-149">Per impostazione predefinita, tutti i metodi in un servizio possono essere chiamati da utenti non autenticati.</span><span class="sxs-lookup"><span data-stu-id="98187-149">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="98187-150">Per richiedere l'autenticazione, applicare l'attributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) al servizio:</span><span class="sxs-lookup"><span data-stu-id="98187-150">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="98187-151">È possibile utilizzare gli argomenti `[Authorize]` del costruttore e le proprietà dell'attributo per limitare l'accesso solo agli utenti che corrispondono a criteri di [autorizzazione](xref:security/authorization/policies)specifici.</span><span class="sxs-lookup"><span data-stu-id="98187-151">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="98187-152">Ad esempio, se si dispone `MyAuthorizationPolicy`di un criterio di autorizzazione personalizzato denominato , assicurarsi che solo gli utenti che corrispondono a tale criterio possano accedere al servizio utilizzando il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="98187-152">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="98187-153">Ai singoli metodi `[Authorize]` di servizio può essere applicato anche l'attributo.</span><span class="sxs-lookup"><span data-stu-id="98187-153">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="98187-154">Se l'utente corrente non corrisponde ai criteri applicati sia al metodo **che** alla classe, viene restituito un errore al chiamante:</span><span class="sxs-lookup"><span data-stu-id="98187-154">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="98187-155">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="98187-155">Additional resources</span></span>

* [<span data-ttu-id="98187-156">Bearer Token authentication in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98187-156">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="98187-157">Configurare l'autenticazione del certificato client in ASP.NET CoreConfigure Client Certificate authentication in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98187-157">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
