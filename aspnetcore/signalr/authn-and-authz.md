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
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>Autenticazione e autorizzazione in ASP.NET CoreSignalR

Di [Andrew Stanton-Nurse](https://twitter.com/anurse)

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(procedura per il download)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>Autenticare gli utenti che si SignalR connettono a un hub

SignalRpuò essere usato con [l'autenticazione ASP.NET Core](xref:security/authentication/identity) per associare un utente a ogni connessione. In un hub è possibile accedere ai dati di autenticazione dalla proprietà [HubConnectionContext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) . L'autenticazione consente all'hub di chiamare i metodi su tutte le connessioni associate a un utente. Per ulteriori informazioni, vedere [gestire utenti e gruppi in SignalR ](xref:signalr/groups). Più connessioni possono essere associate a un singolo utente.

Di seguito è riportato un esempio `Startup.Configure` di utilizzo SignalR di e ASP.NET Core l'autenticazione:

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
> L'ordine di registrazione di e del SignalR middleware di autenticazione ASP.NET Core è importante. Chiamare `UseAuthentication` sempre prima `UseSignalR` di in SignalR modo che disponga di un `HttpContext`utente in.

::: moniker-end

### <a name="cookie-authentication"></a>Autenticazione cookie

In un'app basata su browser, l'autenticazione dei cookie consente alle credenziali utente esistenti di eseguire SignalR automaticamente il flusso alle connessioni. Quando si usa il client browser, non è necessaria alcuna configurazione aggiuntiva. Se l'utente è connesso all'app, la connessione eredita SignalR automaticamente l'autenticazione.

I cookie sono un modo specifico del browser per inviare i token di accesso, ma i client non basati su browser possono inviarli. Quando si usa il [client .NET](xref:signalr/dotnet-client), `Cookies` la proprietà può essere configurata nella `.WithUrl` chiamata per fornire un cookie. Tuttavia, l'uso dell'autenticazione basata su cookie dal client .NET richiede che l'app fornisca un'API per lo scambio dei dati di autenticazione per un cookie.

### <a name="bearer-token-authentication"></a>Autenticazione del token di porta

Il client può fornire un token di accesso invece di usare un cookie. Il server convalida il token e lo usa per identificare l'utente. Questa convalida viene eseguita solo quando viene stabilita la connessione. Durante il ciclo di vita della connessione, il server non viene riconvalidato automaticamente per verificare la revoca dei token.

Nel server bearer token autenticazione viene configurata con il [middleware di JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

Nel client JavaScript il token può essere fornito con l'opzione [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

Nel client .NET esiste una proprietà [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) simile che può essere usata per configurare il token:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> La funzione del token di accesso fornita viene chiamata prima di **ogni** richiesta HTTP SignalReffettuata da. Se è necessario rinnovare il token per mantenerla attiva (perché potrebbe scadere durante la connessione), eseguire questa operazione dall'interno di questa funzione e restituire il token aggiornato.

Nelle API Web standard, i token di porta sono inviati in un'intestazione HTTP. Tuttavia, SignalR non è in grado di impostare queste intestazioni nei browser quando si utilizzano alcuni trasporti. Quando si usano WebSocket ed eventi inviati dal server, il token viene trasmesso come parametro della stringa di query. Per supportare questa operazione nel server, è necessaria una configurazione aggiuntiva:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> La stringa di query viene usata nei browser quando ci si connette con WebSocket ed eventi inviati dal server a causa delle limitazioni dell'API del browser. Quando si usa HTTPS, i valori della stringa di query sono protetti dalla connessione TLS. Tuttavia, molti server registrano i valori della stringa di query. Per ulteriori informazioni, vedere [considerazioni sulla sicurezza in SignalRASP.NET Core ](xref:signalr/security). SignalRUsa le intestazioni per trasmettere i token negli ambienti che li supportano, ad esempio i client .NET e Java.

### <a name="cookies-vs-bearer-tokens"></a>Cookie e token di porta 

I cookie sono specifici dei browser. L'invio da altri tipi di client aggiunge complessità rispetto all'invio dei token di porta. Di conseguenza, l'autenticazione dei cookie non è consigliata, a meno che l'app non debba solo autenticare gli utenti dal client browser. L'autenticazione del token di porta è l'approccio consigliato quando si usano client diversi dal client browser.

### <a name="windows-authentication"></a>Autenticazione di Windows

Se [l'autenticazione di Windows](xref:security/authentication/windowsauth) è configurata SignalR nell'app, può usare tale identità per proteggere gli hub. Tuttavia, per inviare messaggi a singoli utenti, è necessario aggiungere un provider di ID utente personalizzato. Il sistema di autenticazione di Windows non fornisce l'attestazione "identificatore nome". SignalRUsa l'attestazione per determinare il nome utente.

Aggiungere una nuova classe che implementi `IUserIdProvider` e recuperi una delle attestazioni dall'utente da utilizzare come identificatore. Ad esempio, per usare l'attestazione "Name" (che è il nome utente di Windows `[Domain]\[Username]`nel formato), creare la classe seguente:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

Invece di `ClaimTypes.Name`, è possibile usare qualsiasi valore di `User` (ad esempio, l'identificatore del SID di Windows e così via).

> [!NOTE]
> Il valore scelto deve essere univoco tra tutti gli utenti del sistema. In caso contrario, un messaggio destinato a un utente potrebbe finire con un altro utente.

Registrare il componente nel `Startup.ConfigureServices` metodo.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

Nel client .NET è necessario abilitare l'autenticazione di Windows impostando la proprietà [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

L'autenticazione di Windows è supportata solo dal client browser quando si usa Microsoft Internet Explorer o Microsoft Edge.

### <a name="use-claims-to-customize-identity-handling"></a>Usare le attestazioni per personalizzare la gestione delle identità

Un'app che autentica gli utenti può derivare gli ID SignalR utente dalle attestazioni utente. Per specificare la SignalR modalità di creazione degli ID `IUserIdProvider` utente, implementare e registrare l'implementazione di.

Il codice di esempio illustra come usare le attestazioni per selezionare l'indirizzo di posta elettronica dell'utente come proprietà di identificazione. 

> [!NOTE]
> Il valore scelto deve essere univoco tra tutti gli utenti del sistema. In caso contrario, un messaggio destinato a un utente potrebbe finire con un altro utente.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

La registrazione dell'account aggiunge un'attestazione `ClaimsTypes.Email` con tipo al database di identità ASP.NET.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Registrare questo componente in `Startup.ConfigureServices`.

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autorizzare gli utenti ad accedere a hub e metodi Hub

Per impostazione predefinita, tutti i metodi in un hub possono essere chiamati da un utente non autenticato. Per richiedere l'autenticazione, applicare l'attributo [autorizza](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) all'hub:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

È possibile utilizzare gli argomenti del costruttore e le proprietà `[Authorize]` dell'attributo per limitare l'accesso solo agli utenti che corrispondono a [criteri di autorizzazione](xref:security/authorization/policies)specifici. Ad esempio, se si dispone di un criterio di autorizzazione `MyAuthorizationPolicy` personalizzato chiamato, è possibile assicurarsi che solo gli utenti che corrispondono a tale criterio possano accedere all'hub usando il codice seguente:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

Per i singoli metodi dell'hub `[Authorize]` è possibile applicare anche l'attributo. Se l'utente corrente non corrisponde ai criteri applicati al metodo, al chiamante viene restituito un errore:

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Usare i gestori di autorizzazione per personalizzare l'autorizzazione del metodo dell'hub

SignalRfornisce una risorsa personalizzata ai gestori di autorizzazione quando un metodo Hub richiede l'autorizzazione. La risorsa è un'istanza di `HubInvocationContext`. `HubInvocationContext` Include `HubCallerContext`, il nome del metodo Hub richiamato e gli argomenti del metodo Hub.

Si consideri l'esempio di una chat room che consente l'accesso a più organizzazioni tramite Azure Active Directory. Chiunque disponga di un account Microsoft può accedere a chat, ma solo i membri dell'organizzazione proprietaria dovrebbero essere in grado di vietare gli utenti o visualizzare le cronologie della chat degli utenti. Inoltre, potrebbe essere necessario limitare determinate funzionalità di determinati utenti. Utilizzando le funzionalità aggiornate di ASP.NET Core 3,0, questa operazione è interamente possibile. Si noti che `DomainRestrictedRequirement` il funge da personalizzato `IAuthorizationRequirement`. Ora che il `HubInvocationContext` parametro Resource viene passato, la logica interna può ispezionare il contesto in cui viene chiamato l'hub e prendere decisioni per consentire all'utente di eseguire singoli metodi dell'hub.

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

In `Startup.ConfigureServices`aggiungere i nuovi criteri, specificando il requisito `DomainRestrictedRequirement` personalizzato come parametro per la creazione del `DomainRestricted` criterio.

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

Nell'esempio precedente, la `DomainRestrictedRequirement` classe è un oggetto `IAuthorizationRequirement` e il relativo `AuthorizationHandler` per quel requisito. È accettabile suddividere questi due componenti in classi separate per separare le problematiche. Un vantaggio dell'approccio dell'esempio è che non è necessario inserire il `AuthorizationHandler` durante l'avvio, perché il requisito e il gestore sono gli stessi.

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* [Autenticazione del token di porta in ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Autorizzazione basata sulle risorse](xref:security/authorization/resourcebased)
