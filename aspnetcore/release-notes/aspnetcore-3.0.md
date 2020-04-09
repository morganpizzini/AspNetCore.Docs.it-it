---
title: Novità di ASP.NET Core 3.0
author: rick-anderson
description: Scopri le nuove funzionalità di ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 4886673a9b16b8be8d9a0b0d5c7002a91760544e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976976"
---
# <a name="whats-new-in-aspnet-core-30"></a>Novità di ASP.NET Core 3.0

In questo articolo vengono evidenziate le modifiche più significative in ASP.NET Core 3.0 con collegamenti alla documentazione pertinente.

## Blazor

Blazorè un nuovo framework in ASP.NET Core per la creazione di un'interfaccia utente Web interattiva sul lato client con .NET:Is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:

* Creare interfacce utente interattive avanzate con C# invece di JavaScript.
* Condividere la logica dell'app scritta in .NET sul lato client e sul lato server.
* Eseguire il rendering dell'interfaccia utente come HTML e CSS per un ampio supporto dei browser, inclusi i browser per dispositivi mobili.

Blazorscenari supportati dal framework:

* Componenti riutilizzabili dell'interfaccia utente (componenti Razor)Reusable UI components (Razor components)
* Routing lato client
* Layout dei componenti
* Supporto per l'iniezione di dipendenza
* Moduli e convalida
* Compilare librerie di componenti con librerie di classi RazorBuild component libraries with Razor class libraries
* Interoperabilità JavaScript

Per altre informazioni, vedere <xref:blazor/index>.

### <a name="opno-locblazor-server"></a>BlazorServer

Blazordisaccoppia la logica di rendering dei componenti dalla modalità di applicazione degli aggiornamenti dell'interfaccia utente. BlazorServer fornisce il supporto per l'hosting di componenti Razor nel server in un'app ASP.NET Core. Gli aggiornamenti dell'interfaccia SignalR utente vengono gestiti tramite una connessione. BlazorIl server è supportato in ASP.NET Core 3.0.

### <a name="opno-locblazor-webassembly-preview"></a>BlazorWebAssembly (anteprima)

Blazorle app possono anche essere eseguite direttamente nel browser usando un runtime .NET basato su WebAssembly. BlazorWebAssembly è in anteprima e *non* è supportato in ASP.NET Core 3.0. BlazorWebAssembly sarà supportato in una versione futura di ASP.NET Core.

### <a name="razor-components"></a>Componenti Razor

Blazorle app sono costruite dai componenti. I componenti sono blocchi autonomi dell'interfaccia utente, ad esempio una pagina, una finestra di dialogo o un form. I componenti sono classi .NET normali che definiscono la logica di rendering dell'interfaccia utente e i gestori eventi sul lato client. È possibile creare app Web interattive avanzate senza JavaScript.

I Blazor componenti in vengono in genere creati utilizzando la sintassi Razor, una combinazione naturale di HTML e C. Razor components are similar to Razor Pages and MVC views in that they both use Razor. A differenza delle pagine e delle visualizzazioni, basate su un modello di richiesta-risposta, i componenti vengono usati in modo specifico per la gestione della composizione dell'interfaccia utente.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* È un framework RPC (chiamata di procedura remota) popolare e ad alte prestazioni.
* Offre un approccio orientato al contratto per lo sviluppo di API.
* Utilizza tecnologie moderne come:

  * HTTP/2 per il trasporto.
  * Protocol Buffers come linguaggio di descrizione dell'interfaccia.
  * Formato di serializzazione binario.
* Fornisce funzionalità quali:

  * Authentication
  * Flusso bidirezionale e controllo di flusso.
  * Cancellazione e timeout.

La funzionalità gRPC in ASP.NET Core 3.0 include:

* [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; Un framework ASP.NET Core per l'hosting di servizi gRPC. gRPC su ASP.NET Core si integra con le funzionalità standard ASP.NET Core come la registrazione, l'inserimento delle dipendenze (DI), l'autenticazione e l'autorizzazione.
* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; Un client gRPC per .NET Core `HttpClient`che si basa sul familiare file .
* [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC integrazione client con `HttpClientFactory`.

Per altre informazioni, vedere <xref:grpc/index>.

## SignalR

Vedere [ SignalR Aggiornare il codice](xref:migration/22-to-30#signalr) per le istruzioni di migrazione. SignalRora `System.Text.Json` utilizza per serializzare/deserializzare i messaggi JSON. Vedere [Passare a Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) per `Newtonsoft.Json`istruzioni sul ripristino del serializzatore basato.

In JavaScript e .NET SignalRClients for , è stato aggiunto il supporto per la riconnessione automatica. Per impostazione predefinita, il client tenta di riconnettersi immediatamente e riprovare dopo 2, 10 e 30 secondi, se necessario. Se il client si riconnette correttamente, riceve un nuovo ID di connessione. La riconnessione automatica è opt-in:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Gli intervalli di riconnessione possono essere specificati passando una matrice di durate basate su millisecondi:The reconnection intervals can be specified by passing an array of millisecond-based durations:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Un'implementazione personalizzata può essere passata per il controllo completo degli intervalli di riconnessione.

Se la riconnessione non riesce dopo l'ultimo intervallo di riconnessione:

* Il client considera che la connessione è offline.
* Il client smette di provare a riconnettersi.

Durante i tentativi di riconnessione, aggiorna l'interfaccia utente dell'app per notificare all'utente che è in corso il tentativo di riconnessione.

Per fornire feedback dell'interfaccia utente SignalR quando la connessione viene interrotta, l'API client è stata espansa per includere i gestori eventi seguenti:To provide UI feedback when the connection is interrupted, the client API has been expanded to include the following event handlers:

* `onreconnecting`: offre agli sviluppatori l'opportunità di disabilitare l'interfaccia utente o di indire agli utenti che l'app è offline.
* `onreconnected`: offre agli sviluppatori l'opportunità di aggiornare l'interfaccia utente una volta ristabilita la connessione.

Il codice `onreconnecting` seguente usa per aggiornare l'interfaccia utente durante il tentativo di connessione:The following code uses to update the UI while trying to connect:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

Il codice `onreconnected` seguente usa per aggiornare l'interfaccia utente alla connessione:The following code uses to update the UI on connection:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR3.0 e versioni successive fornisce una risorsa personalizzata ai gestori di autorizzazione quando un metodo hub richiede l'autorizzazione. La risorsa è `HubInvocationContext`un'istanza di . Il `HubInvocationContext` include il:

* `HubCallerContext`
* Nome del metodo dell'hub richiamato.
* Argomenti per il metodo dell'hub.

Si consideri l'esempio seguente di un'app di chat room che consente l'accesso a più organizzazioni tramite Azure Active Directory.Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory. Chiunque disponga di un account Microsoft può accedere alla chat, ma solo i membri dell'organizzazione proprietaria possono vietare gli utenti o visualizzare le cronologie delle chat degli utenti. L'app potrebbe limitare determinate funzionalità da utenti specifici.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

Nel codice precedente, `DomainRestrictedRequirement` funge da `IAuthorizationRequirement`oggetto personalizzato. Poiché `HubInvocationContext` il parametro della risorsa viene passato, la logica interna può:Because the resource parameter is being passed in, the internal logic can:

* Esaminare il contesto in cui viene chiamato l'hub.
* Prendere decisioni su come consentire all'utente di eseguire singoli metodi Hub.

I singoli metodi Hub possono essere contrassegnati con il nome del criterio che controlla il codice in fase di esecuzione. Quando i client tentano di `DomainRestrictedRequirement` chiamare singoli metodi Hub, il gestore viene eseguito e controlla l'accesso ai metodi. In base al `DomainRestrictedRequirement` modo in cui i controlli accedono:

* Tutti gli utenti connessi `SendMessage` possono chiamare il metodo .
* Solo gli utenti che `@jabbr.net` hanno effettuato l'accesso con un indirizzo e-mail possono visualizzare le cronologie degli utenti.
* Solo `bob42@jabbr.net` gli utenti possono escludere dalla chat room.

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
```

La `DomainRestricted` creazione del criterio potrebbe comportare:

* In *Startup.cs*, aggiungendo il nuovo criterio.
* Specificare `DomainRestrictedRequirement` il requisito personalizzato come parametro.
* Registrazione `DomainRestricted` con il middleware di autorizzazione.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

SignalRgli hub utilizzano [il routing degli endpoint](xref:fundamentals/routing). SignalRconnessione hub è stata precedentemente eseguita in modo esplicito:hub connection was previously done explicitly:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

Nella versione precedente, gli sviluppatori dovevano collegare controller, pagine Razor e hub in una varietà di posizioni. La connessione esplicita produce una serie di segmenti di routing quasi identici:Explicit connection results in a series of nearly-identical routing segments:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

SignalRGli hub 3.0 possono essere instradati tramite il routing endpoint. Con il routing degli endpoint, `UseRouting`in genere tutto il routing può essere configurato in:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

ASP.NET Core 3.0 SignalR aggiunto:

Streaming da client a server. Con lo streaming da client a server, i metodi `IAsyncEnumerable<T>` `ChannelReader<T>`lato server possono accettare istanze di un oggetto o . Nell'esempio di codice `UploadStream` c'è il seguente esempio di codice, il metodo nell'hub riceverà un flusso di stringhe dal client:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

Le app client .NET `IAsyncEnumerable<T>` `ChannelReader<T>` possono passare `stream` un'istanza o come argomento del `UploadStream` metodo Hub precedente.

Dopo `for` che il ciclo è stato completato e la funzione locale viene chiusa, viene inviato il completamento del flusso:After the loop has completed and the local function exits, the stream completion is sent:

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Le app client SignalR `Subject` JavaScript usano (o un `stream` [oggetto RxJS](https://rxjs.dev/api/index/class/Subject)) per l'argomento del metodo `UploadStream` Hub precedente.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

Il codice JavaScript `subject.next` potrebbe usare il metodo per gestire le stringhe quando vengono acquisite e pronte per essere inviate al server.

```javascript
subject.next("example");
subject.complete();
```

Usando codice come i due frammenti precedenti, è possibile creare esperienze di streaming in tempo reale.

## <a name="new-json-serialization"></a>Nuova serializzazione JSON

ASP.NET Core 3.0 <xref:System.Text.Json> ora usa per impostazione predefinita per la serializzazione JSON:The Core 3.0 now uses by default for JSON serialization:

* Legge e scrive JSON in modo asincrono.
* È ottimizzato per il testo UTF-8.
* Prestazioni in `Newtonsoft.Json`genere superiori a quelle di .

Per aggiungere Json.NET a ASP.NET Core 3.0, vedere Aggiungere il supporto del [formato JSON basato su Newtonsoft.Json.](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)

## <a name="new-razor-directives"></a>Nuove direttive Razor

L'elenco seguente contiene le nuove direttive Razor:The following list contains new Razor directives:

* [`@attribute`](xref:mvc/views/razor#attribute)&ndash; La `@attribute` direttiva applica l'attributo specificato alla classe della pagina o della visualizzazione generata. Ad esempio: `@attribute [Authorize]`.
* [`@implements`](xref:mvc/views/razor#implements)&ndash; La `@implements` direttiva implementa un'interfaccia per la classe generata. Ad esempio: `@implements IDisposable`.

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 supporta l'autenticazione e l'autorizzazione per le API Web e le SPA

ASP.NET Core 3.0 offre l'autenticazione nelle applicazioni a pagina singola (SPA) usando il supporto per l'autorizzazione API Web. ASP.NET core Identity per l'autenticazione e l'archiviazione degli utenti viene combinato con [IdentityServer4](https://identityserver.io/) per l'implementazione di Open ID Connect.

IdentityServer4 è un framework OpenID Connect e OAuth 2.0 per ASP.NET Core 3.0. Abilita le seguenti funzionalità di sicurezza:

* Autenticazione come servizio (AaaS)
* Single Sign-On/Off (SSO) su più tipi di applicazione
* Controllo di accesso per le APIAccess control for APIs
* Gateway federativo

Per ulteriori informazioni, vedere la documentazione di [IdentityServer4](http://docs.identityserver.io/en/latest/index.html) o [Autenticazione e autorizzazione per LE SCA.](xref:security/authentication/identity/spa)

## <a name="certificate-and-kerberos-authentication"></a>Autenticazione basata su certificati e Kerberos

L'autenticazione del certificato richiede:

* Configurazione del server per l'accettazione dei certificati.
* Aggiunta del middleware `Startup.Configure`di autenticazione in .
* Aggiunta del servizio `Startup.ConfigureServices`di autenticazione del certificato in .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Le opzioni per l'autenticazione del certificato includono la possibilità di:Options for certificate authentication include the ability to:

* Accettare certificati autofirmati.
* Verificare la revoca del certificato.
* Verificare che il certificato offerto disponga dei flag di utilizzo corretti.

Dalle proprietà del certificato viene costruita un'entità utente predefinita. L'entità utente contiene un evento che consente l'integrazione o la sostituzione dell'entità. Per altre informazioni, vedere <xref:security/authentication/certauth>.

[L'autenticazione](/windows-server/security/windows-authentication/windows-authentication-overview) di Windows è stata estesa su Linux e macOS. Nelle versioni precedenti, l'autenticazione di Windows era limitata a [IIS](xref:host-and-deploy/iis/index) e [HttpSys](xref:fundamentals/servers/httpsys). In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) è in grado di utilizzare Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)e [NTLM in Windows](/windows-server/security/kerberos/ntlm-overview), Linux e gli host di macOS per windows aggiunti a un dominio. Il supporto Kestrel di questi schemi di autenticazione viene fornito dal pacchetto [Microsoft.AspNetCore.Authentication.Negotiate NuGet.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) Come per gli altri servizi di autenticazione, configurare l'autenticazione a livello di app, quindi configurare il servizio:As with the other authentication services, configure authentication app wide, then configure the service:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Requisiti dell'host:

* Gli host Windows devono avere [i nomi dell'entità servizio](/windows/win32/ad/service-principal-names) (SPN) aggiunti all'account utente che ospita l'app.
* I computer Linux e macOS devono essere aggiunti al dominio.
  * Per il processo Web è necessario creare nomi SPN.
  * [I file Keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) devono essere generati e configurati sul computer host.

Per altre informazioni, vedere <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Modifiche al modello

I modelli di interfaccia utente Web (pagine Razor, MVC con controller e visualizzazioni) sono stati rimossi i seguenti:

* L'interfaccia utente di consenso ai cookie non è più inclusa. Per attivare la funzionalità di consenso ai cookie in <xref:security/gdpr>un'app generata dal modello ASP.NET Core 3.0, vedere .
* Gli script e le risorse statiche correlate sono ora referenziati come file locali anziché utilizzare le reti CDN. Per ulteriori informazioni, vedere Script e gli asset statici correlati a cui [viene ora fatto riferimento come file locali anziché utilizzare reti CDN in base all'ambiente corrente (aspnet/AspNetCore.Docs #14350).](https://github.com/dotnet/AspNetCore.Docs/issues/14350)

Modello angolare aggiornato per l'utilizzo di Angular 8.

Il modello Razor libreria di classi (RCL) per impostazione predefinita è Razor component development per impostazione predefinita. Una nuova opzione di modello in Visual Studio fornisce il supporto dei modelli per pagine e visualizzazioni. Quando si crea un RCL dal modello `--support-pages-and-views` in`dotnet new razorclasslib --support-pages-and-views`una shell dei comandi, passare l'opzione ( ).

## <a name="generic-host"></a>Host generico

I modelli di ASP.NET <xref:fundamentals/host/generic-host>Core 3.0 utilizzano . Le versioni <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>precedenti utilizzate . L'utilizzo di .NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core Generic Host ( ) fornisce una migliore integrazione delle app ASP.NET Core con altri scenari server non specifici del Web. Per ulteriori informazioni, vedere [HostBuilder sostituisce WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Configurazione dell'host

Prima del rilascio di ASP.NET Core 3.0, le variabili di ambiente con `ASPNETCORE_` prefisso venivano caricate per la configurazione host dell'host Web. Nella 3.0, `AddEnvironmentVariables` viene utilizzato per caricare `DOTNET_` le variabili `CreateDefaultBuilder`di ambiente precedute da prefisso per la configurazione dell'host con .

### <a name="changes-to-startup-constructor-injection"></a>Modifiche all'inserimento del costruttore di avvioChanges to Startup constructor injection

L'host generico supporta solo `Startup` i seguenti tipi per l'inserimento del costruttore:

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Tutti i servizi possono comunque essere `Startup.Configure` iniettati direttamente come argomenti al metodo. Per ulteriori informazioni, vedere [L'host generico limita l'inserimento del costruttore di avvio (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).

## <a name="kestrel"></a>Kestrel

* La configurazione di Kestrel è stata aggiornata per la migrazione all'host generico. Nella versione 3.0 Kestrel è configurato nel `ConfigureWebHostDefaults`generatore di host Web fornito da .
* Gli adattatori di connessione sono stati rimossi da Kestrel e sostituiti con il middleware di connessione, che è simile al middleware HTTP nella pipeline ASP.NET Core ma per le connessioni di livello inferiore.
* Il livello di trasporto Kestrel è `Connections.Abstractions`stato esposto come interfaccia pubblica in .
* L'ambiguità tra intestazioni e trailer è stata risolta spostando le intestazioni finali in una nuova raccolta.
* Le API i/o sincrone, ad `HttpRequest.Body.Read`esempio , sono un'origine comune di incudine di thread che causa arresti anomali dell'app. Nella 3.0, `AllowSynchronousIO` è disabilitato per impostazione predefinita.

Per altre informazioni, vedere <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>HTTP/2 abilitato per impostazione predefinita

HTTP/2 è abilitato per impostazione predefinita in Kestrel per gli endpoint HTTPS. Il supporto HTTP/2 per IIS o HTTP.sys è abilitato quando è supportato dal sistema operativo.

## <a name="eventcounters-on-request"></a>EventCounters su richiesta

L'hosting EventSource, `Microsoft.AspNetCore.Hosting`, genera <xref:System.Diagnostics.Tracing.EventCounter> i nuovi tipi seguenti correlati alle richieste in ingresso:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Routing degli endpoint

Endpoint Routing, che consente ai framework (ad esempio MVC) di funzionare bene con middleware, è migliorato:Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:

* L'ordine del middleware e degli endpoint è `Startup.Configure`configurabile nella pipeline di elaborazione delle richieste di .
* Gli endpoint e il middleware si comcompongono bene con altre tecnologie basate su ASP.NET Core, ad esempio i controlli di integrità.
* Gli endpoint possono implementare criteri, ad esempio CORS o autorizzazione, sia nel middleware che in MVC.
* I filtri e gli attributi possono essere posizionati sui metodi nei controller.

Per altre informazioni, vedere <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Controlli di integrità

I controlli di integrità utilizzano il routing degli endpoint con l'host generico. In `Startup.Configure`, `MapHealthChecks` chiamare il generatore di endpoint con l'URL dell'endpoint o il percorso relativo:In , call on the endpoint builder with the endpoint URL or relative path:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Gli endpoint dei controlli di integrità possono:Health Checks endpoints can:

* Specificare uno o più host/porte consentiti.
* Richiedere l'autorizzazione.
* Richiedere CORS.

Per altre informazioni, vedere gli articoli seguenti:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>Pipe su HttpContext

È ora possibile leggere il corpo della richiesta e <xref:System.IO.Pipelines> scrivere il corpo della risposta usando l'API. Il valore di <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader`property fornisce <xref:System.IO.Pipelines.PipeReader> un che può essere utilizzato per leggere il corpo della richiesta. Il valore di <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter`property fornisce <xref:System.IO.Pipelines.PipeWriter> un che può essere utilizzato per scrivere il corpo della risposta. `HttpRequest.BodyReader`è un analogo `HttpRequest.Body` del flusso. `HttpResponse.BodyWriter`è un analogo `HttpResponse.Body` del flusso.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>Segnalazione errori migliorata in IISImproved error reporting in IIS

Gli errori di avvio quando si ospitano applicazioni ASP.NET Core in IIS ora producono dati di diagnostica più ricchi. Questi errori vengono segnalati nel registro eventi di Windows con le tracce dello stack, ove applicabile. Inoltre, tutti gli avvisi, gli errori e le eccezioni non gestite vengono registrati nel registro eventi di Windows.

## <a name="worker-service-and-worker-sdk"></a>Servizio di lavoro e SDK di lavoro

.NET Core 3.0 introduce il nuovo modello di app Servizio di lavoro. Questo modello fornisce un punto di partenza per la scrittura di servizi a esecuzione prolungata in .NET Core.This template provides a starting point for writing long running services in .NET Core.

Per altre informazioni, vedere:

* [.NET Core Workers come servizi Windows](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>Miglioramenti del middleware delle intestazioni inoltrate

Nelle versioni precedenti di <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> ASP.NET <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> Core, le chiamate e le quali erano problematiche quando venivano distribuite in un Linux di Azure o dietro qualsiasi proxy inverso diverso da IIS. La correzione per le versioni precedenti è documentata in [Inoltrare lo schema per i proxy inversi Linux e non IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).

Questo scenario è stato risolto in ASP.NET Core 3.0.This scenario is fixed in ASP.NET Core 3.0. L'host abilita il [middleware Intestazioni inoltrate](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) quando la `ASPNETCORE_FORWARDEDHEADERS_ENABLED` variabile di ambiente è impostata su . `true` `ASPNETCORE_FORWARDEDHEADERS_ENABLED`è impostato `true` su nelle immagini del contenitore.

## <a name="performance-improvements"></a>Miglioramenti delle prestazioni.

ASP.NET Core 3.0 include molti miglioramenti che riducono l'utilizzo della memoria e migliorano la velocità effettiva:ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:

* Riduzione dell'utilizzo della memoria quando si utilizza il contenitore di inserimento delle dipendenze incorporato per i servizi con ambito.
* Riduzione delle allocazioni in tutto il framework, inclusi scenari middleware e routing.
* Riduzione dell'utilizzo della memoria per le connessioni WebSocket.
* Miglioramenti alla riduzione della memoria e alla velocità effettiva per le connessioni HTTPS.
* Nuovo serializzatore JSON ottimizzato e completamente asincrono.
* Miglioramenti all'utilizzo della memoria e alla velocità effettiva dell'analisi dei moduli.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3.0 viene eseguito solo in .NET Core 3.0

A partire da ASP.NET Core 3.0, .NET Framework non è più un framework di destinazione supportato. I progetti destinati a .NET Framework possono continuare in modo completamente supportato utilizzando la versione LTS di [.NET Core 2.1.](https://dotnet.microsoft.com/download/dotnet-core/2.1) La maggior parte dei pacchetti correlati ASP.NET Core 2.1.x sarà supportata a tempo indeterminato, oltre il periodo LTS di tre anni per .NET Core 2.1.

Per informazioni sulla migrazione, vedere [Eseguire il porting del codice da .NET Framework a .NET Core.](/dotnet/core/porting/)

## <a name="use-the-aspnet-core-shared-framework"></a>Usare il framework condiviso ASP.NET CoreUse the ASP.NET Core shared framework

Il framework condiviso ASP.NET Core 3.0, contenuto nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), non richiede più un elemento esplicito `<PackageReference />` nel file di progetto. Viene fatto automaticamente riferimento al `Microsoft.NET.Sdk.Web` framework condiviso quando si usa l'SDK nel file di progetto:The shared framework is automatically referenced when using the SDK in the project file:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>Assembly rimossi dal framework condiviso ASP.NET Core

Gli assembly più importanti rimossi dal framework condiviso ASP.NET Core 3.0 sono:The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:

* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET). Per aggiungere Json.NET a ASP.NET Core 3.0, vedere Aggiungere il supporto del [formato JSON basato su Newtonsoft.Json.](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support) ASP.NET Core 3.0 `System.Text.Json` introduce per la lettura e la scrittura json. Per altre informazioni, vedere [Nuova serializzazione JSON](#new-json-serialization) in questo documento.
* [Entity Framework Core](/ef/core/)

Per un elenco completo degli assembly rimossi dal framework condiviso, vedere [Assembly rimossi da Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755). Per ulteriori informazioni sulla motivazione di questa modifica, vedere [modifiche di rilievo a Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) e Una prima occhiata alle modifiche in ASP.NET Core [3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 