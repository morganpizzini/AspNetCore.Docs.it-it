---
title: Autenticazione e autorizzazione in ASP.NET CoreSignalR
author: bradygaster
description: Informazioni su come usare l'autenticazione e l'autorizzazione SignalRin ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 0f0bb2040d2407817c91f64a4769e6601c37a07d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775284"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="47d6b-103">Autenticazione e autorizzazione in ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="47d6b-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="47d6b-104">Di [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="47d6b-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="47d6b-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(procedura per il download)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="47d6b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="47d6b-106">Autenticare gli utenti che si SignalR connettono a un hub</span><span class="sxs-lookup"><span data-stu-id="47d6b-106">Authenticate users connecting to a SignalR hub</span></span>

SignalR<span data-ttu-id="47d6b-107">può essere usato con [l'autenticazione ASP.NET Core](xref:security/authentication/identity) per associare un utente a ogni connessione.</span><span class="sxs-lookup"><span data-stu-id="47d6b-107"> can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="47d6b-108">In un hub è possibile accedere ai dati di autenticazione dalla proprietà [HubConnectionContext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) .</span><span class="sxs-lookup"><span data-stu-id="47d6b-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="47d6b-109">L'autenticazione consente all'hub di chiamare i metodi su tutte le connessioni associate a un utente.</span><span class="sxs-lookup"><span data-stu-id="47d6b-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="47d6b-110">Per ulteriori informazioni, vedere [gestire utenti e gruppi in SignalR ](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="47d6b-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="47d6b-111">Più connessioni possono essere associate a un singolo utente.</span><span class="sxs-lookup"><span data-stu-id="47d6b-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="47d6b-112">Di seguito è riportato un esempio `Startup.Configure` di utilizzo SignalR di e ASP.NET Core l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="47d6b-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="47d6b-113">L'ordine di registrazione di e del SignalR middleware di autenticazione ASP.NET Core è importante.</span><span class="sxs-lookup"><span data-stu-id="47d6b-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="47d6b-114">Chiamare `UseAuthentication` sempre prima `UseSignalR` di in SignalR modo che disponga di un `HttpContext`utente in.</span><span class="sxs-lookup"><span data-stu-id="47d6b-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="47d6b-115">Autenticazione cookie</span><span class="sxs-lookup"><span data-stu-id="47d6b-115">Cookie authentication</span></span>

<span data-ttu-id="47d6b-116">In un'app basata su browser, l'autenticazione dei cookie consente alle credenziali utente esistenti di eseguire SignalR automaticamente il flusso alle connessioni.</span><span class="sxs-lookup"><span data-stu-id="47d6b-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="47d6b-117">Quando si usa il client browser, non è necessaria alcuna configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="47d6b-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="47d6b-118">Se l'utente è connesso all'app, la connessione eredita SignalR automaticamente l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="47d6b-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="47d6b-119">I cookie sono un modo specifico del browser per inviare i token di accesso, ma i client non basati su browser possono inviarli.</span><span class="sxs-lookup"><span data-stu-id="47d6b-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="47d6b-120">Quando si usa il [client .NET](xref:signalr/dotnet-client), `Cookies` la proprietà può essere configurata nella `.WithUrl` chiamata per fornire un cookie.</span><span class="sxs-lookup"><span data-stu-id="47d6b-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="47d6b-121">Tuttavia, l'uso dell'autenticazione basata su cookie dal client .NET richiede che l'app fornisca un'API per lo scambio dei dati di autenticazione per un cookie.</span><span class="sxs-lookup"><span data-stu-id="47d6b-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="47d6b-122">Autenticazione del token di porta</span><span class="sxs-lookup"><span data-stu-id="47d6b-122">Bearer token authentication</span></span>

<span data-ttu-id="47d6b-123">Il client può fornire un token di accesso invece di usare un cookie.</span><span class="sxs-lookup"><span data-stu-id="47d6b-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="47d6b-124">Il server convalida il token e lo usa per identificare l'utente.</span><span class="sxs-lookup"><span data-stu-id="47d6b-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="47d6b-125">Questa convalida viene eseguita solo quando viene stabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="47d6b-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="47d6b-126">Durante il ciclo di vita della connessione, il server non viene riconvalidato automaticamente per verificare la revoca dei token.</span><span class="sxs-lookup"><span data-stu-id="47d6b-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="47d6b-127">Nel server bearer token autenticazione viene configurata con il [middleware di JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="47d6b-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="47d6b-128">Nel client JavaScript il token può essere fornito con l'opzione [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .</span><span class="sxs-lookup"><span data-stu-id="47d6b-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="47d6b-129">Nel client .NET esiste una proprietà [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) simile che può essere usata per configurare il token:</span><span class="sxs-lookup"><span data-stu-id="47d6b-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="47d6b-130">La funzione del token di accesso fornita viene chiamata prima di **ogni** richiesta HTTP SignalReffettuata da.</span><span class="sxs-lookup"><span data-stu-id="47d6b-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="47d6b-131">Se è necessario rinnovare il token per mantenerla attiva (perché potrebbe scadere durante la connessione), eseguire questa operazione dall'interno di questa funzione e restituire il token aggiornato.</span><span class="sxs-lookup"><span data-stu-id="47d6b-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="47d6b-132">Nelle API Web standard, i token di porta sono inviati in un'intestazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="47d6b-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="47d6b-133">Tuttavia, SignalR non è in grado di impostare queste intestazioni nei browser quando si utilizzano alcuni trasporti.</span><span class="sxs-lookup"><span data-stu-id="47d6b-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="47d6b-134">Quando si usano WebSocket ed eventi inviati dal server, il token viene trasmesso come parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="47d6b-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="47d6b-135">Per supportare questa operazione nel server, è necessaria una configurazione aggiuntiva:</span><span class="sxs-lookup"><span data-stu-id="47d6b-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="47d6b-136">La stringa di query viene usata nei browser quando ci si connette con WebSocket ed eventi inviati dal server a causa delle limitazioni dell'API del browser.</span><span class="sxs-lookup"><span data-stu-id="47d6b-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="47d6b-137">Quando si usa HTTPS, i valori della stringa di query sono protetti dalla connessione TLS.</span><span class="sxs-lookup"><span data-stu-id="47d6b-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="47d6b-138">Tuttavia, molti server registrano i valori della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="47d6b-138">However, many servers log query string values.</span></span> <span data-ttu-id="47d6b-139">Per ulteriori informazioni, vedere [considerazioni sulla sicurezza in SignalRASP.NET Core ](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="47d6b-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> SignalR<span data-ttu-id="47d6b-140">Usa le intestazioni per trasmettere i token negli ambienti che li supportano, ad esempio i client .NET e Java.</span><span class="sxs-lookup"><span data-stu-id="47d6b-140"> uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="47d6b-141">Cookie e token di porta</span><span class="sxs-lookup"><span data-stu-id="47d6b-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="47d6b-142">I cookie sono specifici dei browser.</span><span class="sxs-lookup"><span data-stu-id="47d6b-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="47d6b-143">L'invio da altri tipi di client aggiunge complessità rispetto all'invio dei token di porta.</span><span class="sxs-lookup"><span data-stu-id="47d6b-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="47d6b-144">Di conseguenza, l'autenticazione dei cookie non è consigliata, a meno che l'app non debba solo autenticare gli utenti dal client browser.</span><span class="sxs-lookup"><span data-stu-id="47d6b-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="47d6b-145">L'autenticazione del token di porta è l'approccio consigliato quando si usano client diversi dal client browser.</span><span class="sxs-lookup"><span data-stu-id="47d6b-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="47d6b-146">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="47d6b-146">Windows authentication</span></span>

<span data-ttu-id="47d6b-147">Se [l'autenticazione di Windows](xref:security/authentication/windowsauth) è configurata SignalR nell'app, può usare tale identità per proteggere gli hub.</span><span class="sxs-lookup"><span data-stu-id="47d6b-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="47d6b-148">Tuttavia, per inviare messaggi a singoli utenti, è necessario aggiungere un provider di ID utente personalizzato.</span><span class="sxs-lookup"><span data-stu-id="47d6b-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="47d6b-149">Il sistema di autenticazione di Windows non fornisce l'attestazione "identificatore nome".</span><span class="sxs-lookup"><span data-stu-id="47d6b-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> SignalR<span data-ttu-id="47d6b-150">Usa l'attestazione per determinare il nome utente.</span><span class="sxs-lookup"><span data-stu-id="47d6b-150"> uses the claim to determine the user name.</span></span>

<span data-ttu-id="47d6b-151">Aggiungere una nuova classe che implementi `IUserIdProvider` e recuperi una delle attestazioni dall'utente da utilizzare come identificatore.</span><span class="sxs-lookup"><span data-stu-id="47d6b-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="47d6b-152">Ad esempio, per usare l'attestazione "Name" (che è il nome utente di Windows `[Domain]\[Username]`nel formato), creare la classe seguente:</span><span class="sxs-lookup"><span data-stu-id="47d6b-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="47d6b-153">Invece di `ClaimTypes.Name`, è possibile usare qualsiasi valore di `User` (ad esempio, l'identificatore del SID di Windows e così via).</span><span class="sxs-lookup"><span data-stu-id="47d6b-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="47d6b-154">Il valore scelto deve essere univoco tra tutti gli utenti del sistema.</span><span class="sxs-lookup"><span data-stu-id="47d6b-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="47d6b-155">In caso contrario, un messaggio destinato a un utente potrebbe finire con un altro utente.</span><span class="sxs-lookup"><span data-stu-id="47d6b-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="47d6b-156">Registrare il componente nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="47d6b-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="47d6b-157">Nel client .NET è necessario abilitare l'autenticazione di Windows impostando la proprietà [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :</span><span class="sxs-lookup"><span data-stu-id="47d6b-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="47d6b-158">L'autenticazione di Windows è supportata solo dal client browser quando si usa Microsoft Internet Explorer o Microsoft Edge.</span><span class="sxs-lookup"><span data-stu-id="47d6b-158">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="47d6b-159">Usare le attestazioni per personalizzare la gestione delle identità</span><span class="sxs-lookup"><span data-stu-id="47d6b-159">Use claims to customize identity handling</span></span>

<span data-ttu-id="47d6b-160">Un'app che autentica gli utenti può derivare gli ID SignalR utente dalle attestazioni utente.</span><span class="sxs-lookup"><span data-stu-id="47d6b-160">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="47d6b-161">Per specificare la SignalR modalità di creazione degli ID `IUserIdProvider` utente, implementare e registrare l'implementazione di.</span><span class="sxs-lookup"><span data-stu-id="47d6b-161">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="47d6b-162">Il codice di esempio illustra come usare le attestazioni per selezionare l'indirizzo di posta elettronica dell'utente come proprietà di identificazione.</span><span class="sxs-lookup"><span data-stu-id="47d6b-162">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="47d6b-163">Il valore scelto deve essere univoco tra tutti gli utenti del sistema.</span><span class="sxs-lookup"><span data-stu-id="47d6b-163">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="47d6b-164">In caso contrario, un messaggio destinato a un utente potrebbe finire con un altro utente.</span><span class="sxs-lookup"><span data-stu-id="47d6b-164">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="47d6b-165">La registrazione dell'account aggiunge un'attestazione `ClaimsTypes.Email` con tipo al database di identità ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="47d6b-165">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="47d6b-166">Registrare questo componente in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="47d6b-166">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="47d6b-167">Autorizzare gli utenti ad accedere a hub e metodi Hub</span><span class="sxs-lookup"><span data-stu-id="47d6b-167">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="47d6b-168">Per impostazione predefinita, tutti i metodi in un hub possono essere chiamati da un utente non autenticato.</span><span class="sxs-lookup"><span data-stu-id="47d6b-168">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="47d6b-169">Per richiedere l'autenticazione, applicare l'attributo [autorizza](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) all'hub:</span><span class="sxs-lookup"><span data-stu-id="47d6b-169">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="47d6b-170">È possibile utilizzare gli argomenti del costruttore e le proprietà `[Authorize]` dell'attributo per limitare l'accesso solo agli utenti che corrispondono a [criteri di autorizzazione](xref:security/authorization/policies)specifici.</span><span class="sxs-lookup"><span data-stu-id="47d6b-170">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="47d6b-171">Ad esempio, se si dispone di un criterio di autorizzazione `MyAuthorizationPolicy` personalizzato chiamato, è possibile assicurarsi che solo gli utenti che corrispondono a tale criterio possano accedere all'hub usando il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="47d6b-171">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="47d6b-172">Per i singoli metodi dell'hub `[Authorize]` è possibile applicare anche l'attributo.</span><span class="sxs-lookup"><span data-stu-id="47d6b-172">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="47d6b-173">Se l'utente corrente non corrisponde ai criteri applicati al metodo, al chiamante viene restituito un errore:</span><span class="sxs-lookup"><span data-stu-id="47d6b-173">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="47d6b-174">Usare i gestori di autorizzazione per personalizzare l'autorizzazione del metodo dell'hub</span><span class="sxs-lookup"><span data-stu-id="47d6b-174">Use authorization handlers to customize hub method authorization</span></span>

SignalR<span data-ttu-id="47d6b-175">fornisce una risorsa personalizzata ai gestori di autorizzazione quando un metodo Hub richiede l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="47d6b-175"> provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="47d6b-176">La risorsa è un'istanza di `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="47d6b-176">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="47d6b-177">`HubInvocationContext` Include `HubCallerContext`, il nome del metodo Hub richiamato e gli argomenti del metodo Hub.</span><span class="sxs-lookup"><span data-stu-id="47d6b-177">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="47d6b-178">Si consideri l'esempio di una chat room che consente l'accesso a più organizzazioni tramite Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="47d6b-178">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="47d6b-179">Chiunque disponga di un account Microsoft può accedere a chat, ma solo i membri dell'organizzazione proprietaria dovrebbero essere in grado di vietare gli utenti o visualizzare le cronologie della chat degli utenti.</span><span class="sxs-lookup"><span data-stu-id="47d6b-179">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="47d6b-180">Inoltre, potrebbe essere necessario limitare determinate funzionalità di determinati utenti.</span><span class="sxs-lookup"><span data-stu-id="47d6b-180">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="47d6b-181">Utilizzando le funzionalità aggiornate di ASP.NET Core 3,0, questa operazione è interamente possibile.</span><span class="sxs-lookup"><span data-stu-id="47d6b-181">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="47d6b-182">Si noti che `DomainRestrictedRequirement` il funge da personalizzato `IAuthorizationRequirement`.</span><span class="sxs-lookup"><span data-stu-id="47d6b-182">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="47d6b-183">Ora che il `HubInvocationContext` parametro Resource viene passato, la logica interna può ispezionare il contesto in cui viene chiamato l'hub e prendere decisioni per consentire all'utente di eseguire singoli metodi dell'hub.</span><span class="sxs-lookup"><span data-stu-id="47d6b-183">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="47d6b-184">In `Startup.ConfigureServices`aggiungere i nuovi criteri, specificando il requisito `DomainRestrictedRequirement` personalizzato come parametro per la creazione del `DomainRestricted` criterio.</span><span class="sxs-lookup"><span data-stu-id="47d6b-184">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="47d6b-185">Nell'esempio precedente, la `DomainRestrictedRequirement` classe è un oggetto `IAuthorizationRequirement` e il relativo `AuthorizationHandler` per quel requisito.</span><span class="sxs-lookup"><span data-stu-id="47d6b-185">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="47d6b-186">È accettabile suddividere questi due componenti in classi separate per separare le problematiche.</span><span class="sxs-lookup"><span data-stu-id="47d6b-186">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="47d6b-187">Un vantaggio dell'approccio dell'esempio è che non è necessario inserire il `AuthorizationHandler` durante l'avvio, perché il requisito e il gestore sono gli stessi.</span><span class="sxs-lookup"><span data-stu-id="47d6b-187">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="47d6b-188">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="47d6b-188">Additional resources</span></span>

* [<span data-ttu-id="47d6b-189">Autenticazione del token di porta in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47d6b-189">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="47d6b-190">Autorizzazione basata sulle risorse</span><span class="sxs-lookup"><span data-stu-id="47d6b-190">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
