---
title: Proteggere le Blazor app di ASP.NET Core ServerSecure ASP.NET Core Server apps
author: guardrex
description: Informazioni su come ridurre le Blazor minacce alla sicurezza per le app server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626014"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Proteggere ASP.NET le app di core Blazor Server

Di [Javier Calvarro Nelson](https://github.com/javiercn)

Le app Blazor Server adottano un modello di elaborazione dei dati *con stato,* in cui il server e il client mantengono una relazione di lunga durata. Lo stato persistente viene mantenuto da un [circuito](xref:blazor/state-management), che può estendersi su connessioni potenzialmente longeve.

Quando un utente visita un sito Blazor Server, il server crea un circuito nella memoria del server. Il circuito indica al browser il contenuto di cui eseguire il rendering e risponde agli eventi, ad esempio quando l'utente seleziona un pulsante nell'interfaccia utente. Per eseguire queste azioni, un circuito richiama le funzioni JavaScript nel browser dell'utente e i metodi .NET sul server. Questa interazione bidirezionale basata su JavaScript viene definita [interoperabilità JavaScript (interoperabilità JS).](xref:blazor/call-javascript-from-dotnet)

Poiché l'interoperabilità JS si verifica su Internet e il client utilizza un browser remoto, le app Blazor Server condividono la maggior parte dei problemi di sicurezza delle app Web. Questo argomento descrive le minacce comuni alle app blazor Server e fornisce indicazioni sull'attenuazione delle minacce incentrate sulle app con connessione Internet.This topic describes common threats to Blazor Server apps and provides threat to Blazor Server apps and provides threat a threat mitigation guidance focused on Internet-facing apps.

In ambienti vincolati, ad esempio all'interno di reti aziendali o Intranet, alcune delle indicazioni per la mitigazione:

* Non si applica nell'ambiente vincolato.
* Non vale i costi da implementare perché il rischio per la sicurezza è basso in un ambiente vincolato.

## <a name="blazor-server-project-template"></a>Modello di progetto Blazor Server

Il modello di progetto Blazor Server può essere configurato per l'autenticazione quando viene creato il progetto.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Seguire le indicazioni di <xref:blazor/get-started> Visual Studio nell'articolo per creare un nuovo progetto Blazor Server con un meccanismo di autenticazione.

Dopo aver scelto il modello **App server Blazor** nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core**, selezionare **Modifica** in **Autenticazione **.

Viene visualizzata una finestra di dialogo che offre lo stesso set di meccanismi di autenticazione disponibili per altri progetti ASP.NET Core:

* **Nessuna autenticazione**
* **Account utente individuali** &ndash; Gli account utente possono essere archiviati:
  * All'interno dell'app usando il sistema di [identità](xref:security/authentication/identity) di ASP.NET Core.
  * Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Account aziendali o dell'istituto di istruzione**
* **Autenticazione di Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Seguire le linee guida <xref:blazor/get-started> del codice di Visual Studio nell'articolo per creare un nuovo progetto Blazor Server con un meccanismo di autenticazione:Follow the Visual Studio Code guidance in the article to create a new Blazor Server project with an authentication mechanism:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.

| Meccanismo di autenticazione                                                                 | Valore della proprietà `{AUTHENTICATION}` |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Nessuna autenticazione                                                                        | `None`                   |
| Utenti<br>individuali archiviati nell'app con ASP.NET Core Identity.                        | `Individual`             |
| Utenti<br>individuali archiviati in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Account aziendali o dell'istituto di istruzione<br>Autenticazione aziendale per un singolo tenant.            | `SingleOrg`              |
| Account aziendali o dell'istituto di istruzione<br>Autenticazione aziendale per più tenant.           | `MultiOrg`               |
| Autenticazione di Windows                                                                   | `Windows`                |

Il comando crea una cartella denominata con il valore fornito per il segnaposto `{APP NAME}` e il nome della cartella viene usato come nome dell'app. Per altre informazioni, vedere il comando [dotnet new](/dotnet/core/tools/dotnet-new) nella guida per .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Seguire le linee guida di <xref:blazor/get-started> Visual Studio per Mac nell'articolo.

1. Nel passaggio Configura la **nuova app Blazor Server** selezionare **Autenticazione singola (in-app)** dall'elenco a discesa **Autenticazione.**

1. L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET identità di base.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

Seguire le indicazioni dell'interfaccia <xref:blazor/get-started> della riga di comando di .NET Core nell'articolo per creare un nuovo progetto Blazor Server con un meccanismo di autenticazione:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.

| Meccanismo di autenticazione                                                                 | Valore della proprietà `{AUTHENTICATION}` |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Nessuna autenticazione                                                                        | `None`                   |
| Utenti<br>individuali archiviati nell'app con ASP.NET Core Identity.                        | `Individual`             |
| Utenti<br>individuali archiviati in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Account aziendali o dell'istituto di istruzione<br>Autenticazione aziendale per un singolo tenant.            | `SingleOrg`              |
| Account aziendali o dell'istituto di istruzione<br>Autenticazione aziendale per più tenant.           | `MultiOrg`               |
| Autenticazione di Windows                                                                   | `Windows`                |

Il comando crea una cartella denominata con il valore fornito per il segnaposto `{APP NAME}` e il nome della cartella viene usato come nome dell'app. Per altre informazioni, vedere il comando [dotnet new](/dotnet/core/tools/dotnet-new) nella guida per .NET Core.

---

## <a name="pass-tokens-to-a-blazor-server-app"></a>Passare token a un'app Blazor ServerPass tokens to a Blazor Server app

Autenticare l'app Blazor Server come si farebbe con un normale Razor Pages o MVC app. Effettuare il provisioning e salvare i token nel cookie di autenticazione. Ad esempio:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

Per il codice di `Startup.ConfigureServices` esempio, incluso un esempio completo, vedere passaggio di [token a un'applicazione Blazor lato server](https://github.com/javiercn/blazor-server-aad-sample).

Definisci una classe da passare allo stato iniziale dell'app con i token di accesso e aggiornamento:Define a class to pass in the initial app state with the access and refresh tokens:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Definire un servizio provider di token con ambito che può essere usato all'interno dell'app Blazor per risolvere i token da DI:Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from DI:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

In `Startup.ConfigureServices`, aggiungere servizi per:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

Nel file *_Host.cshtml* creare e `InitialApplicationState` creare un'istanza di e passarlo come parametro all'app:In the _Host.cshtml file, create and instance of and pass it as a parameter to the app:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

Nel `App` componente (*App.razor*), risolvere il servizio e inizializzarlo con i dati del parametro :

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Nel servizio che effettua una richiesta API sicura, inserire il provider di token e recuperare il token per chiamare l'API:In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a>Esaurimento delle risorse

L'esaurimento delle risorse può verificarsi quando un client interagisce con il server e fa sì che il server consumi risorse eccessive. Un consumo eccessivo di risorse influisce principalmente:Excessive resource consumption primarily affects:

* [CPU](#cpu)
* [Memoria](#memory)
* [Connessioni client](#client-connections)

Gli attacchi DoS (Denial of Service) in genere cercano di esaurire le risorse di un'app o di un server. Tuttavia, l'esaurimento delle risorse non è necessariamente il risultato di un attacco al sistema. Ad esempio, le risorse limitate possono essere esaurite a causa dell'elevata richiesta degli utenti. DoS è trattato ulteriormente nella sezione [Attacchi DoS (Denial of Service).](#denial-of-service-dos-attacks)

Le risorse esterne al framework Blazor, ad esempio database e handle di file (utilizzati per leggere e scrivere file), possono anche verificarsi l'esaurimento delle risorse. Per altre informazioni, vedere <xref:performance/performance-best-practices>.

### <a name="cpu"></a>CPU

L'esaurimento della CPU può verificarsi quando uno o più client forzano il server a eseguire un lavoro intensivo della CPU.

Si consideri, ad esempio, un'app Blazor Server che calcola un *numero Fibonnacci*. Un numero Fibonnacci viene prodotto da una sequenza di Fibonnacci, dove ogni numero nella sequenza è la somma dei due numeri precedenti. La quantità di lavoro necessaria per raggiungere la risposta dipende dalla lunghezza della sequenza e dalla dimensione del valore iniziale. Se l'app non limita la richiesta di un client, i calcoli che richiedono un utilizzo intensivo della CPU possono dominare il tempo della CPU e ridurre le prestazioni di altre attività. L'utilizzo eccessivo delle risorse è un problema di sicurezza che influisce sulla disponibilità.

L'esaurimento della CPU è un problema per tutte le app pubbliche. Nelle normali app Web, le richieste e le connessioni soffrono per la protezione, ma le app Blazor Server non forniscono le stesse misure di sicurezza. Le app del server Blazor devono includere controlli e limiti appropriati prima di eseguire un lavoro potenzialmente intensivo della CPU.

### <a name="memory"></a>Memoria

L'esaurimento della memoria può verificarsi quando uno o più client forzano il server a utilizzare una grande quantità di memoria.

Si consideri, ad esempio, un'app lato server Blazor con un componente che accetta e visualizza un elenco di elementi. Se l'app Blazor non limita il numero di elementi consentiti o il numero di elementi di cui viene eseguito il rendering al client, l'elaborazione e il rendering che richiedono un utilizzo intensivo della memoria possono dominare la memoria del server fino al punto in cui le prestazioni del server ne risentono. Il server potrebbe bloccarsi o rallentare al punto che sembra essere in modo anomalo.

Si consideri lo scenario seguente per la gestione e la visualizzazione di un elenco di elementi relativi a uno scenario di esaurimento della memoria potenziale nel server:

* Gli elementi `List<MyItem>` in una proprietà o in un campo utilizzano la memoria del server. Se l'app consente all'elenco di elementi di crescere illimitato, c'è il rischio che il server esaurisca la memoria. L'esaurimento della memoria causa la fine della sessione corrente (arresto anomalo) e tutte le sessioni simultanee nell'istanza del server ricevono un'eccezione di memoria insufficiente. Per evitare che si verifichi questo scenario, l'app deve usare una struttura di dati che impone un limite di elementi per gli utenti simultanei.
* Se uno schema di paging non viene utilizzato per il rendering, il server utilizza memoria aggiuntiva per gli oggetti che non sono visibili nell'interfaccia utente. Senza un limite al numero di elementi, le richieste di memoria potrebbero esaurire la memoria disponibile del server. Per evitare questo scenario, utilizzare uno dei seguenti approcci:
  * Utilizzare gli elenchi impaginati durante il rendering.
  * Visualizzare solo i primi 100-1.000 elementi e richiedere all'utente di immettere i criteri di ricerca per trovare gli elementi oltre gli elementi visualizzati.
  * Per uno scenario di rendering più avanzato, implementare elenchi o griglie che supportano *la virtualizzazione.* Utilizzando la virtualizzazione, gli elenchi eseguono il rendering solo di un sottoinsieme di elementi attualmente visibili all'utente. Quando l'utente interagisce con la barra di scorrimento nell'interfaccia utente, il componente esegue il rendering solo degli elementi necessari per la visualizzazione. Gli elementi che non sono attualmente necessari per la visualizzazione possono essere conservati nella memoria secondaria, che è l'approccio ideale. Gli elementi non visualizzati possono anche essere conservati in memoria, il che è meno ideale.

Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly. La differenza principale è che in molti dei framework dell'interfaccia utente la memoria utilizzata dall'app appartiene al client e influisce solo su tale singolo client. Ad esempio, un'app Blazor WebAssembly viene eseguita interamente sul client e utilizza solo le risorse di memoria client. Nello scenario Blazor Server, la memoria utilizzata dall'app appartiene al server e viene condivisa tra i client nell'istanza del server.

Le richieste di memoria lato server sono una considerazione per tutte le applicazioni Blazor Server. Tuttavia, la maggior parte delle app Web sono senza stato e la memoria utilizzata durante l'elaborazione di una richiesta viene rilasciata quando viene restituita la risposta. Come raccomandazione generale, non consentire ai client di allocare una quantità di memoria non associata come in qualsiasi altra app sul lato server che rende persistenti le connessioni client. La memoria utilizzata da un'app Blazor Server persiste per un periodo di tempo più lungo rispetto a una singola richiesta.

> [!NOTE]
> Durante lo sviluppo, è possibile utilizzare un profiler o acquisire una traccia per valutare le richieste di memoria dei client. Un profiler o una traccia non acquisiscono la memoria allocata a un client specifico. Per acquisire l'utilizzo della memoria di un client specifico durante lo sviluppo, acquisire un dump ed esaminare la richiesta di memoria di tutti gli oggetti radicati nel circuito di un utente.

### <a name="client-connections"></a>Connessioni client

L'esaurimento della connessione può verificarsi quando uno o più client aprono troppe connessioni simultanee al server, impedendo ad altri client di stabilire nuove connessioni.

I client Blazor stabiliscono una singola connessione per sessione e mantengono aperta la connessione per tutto il tempo in cui la finestra del browser è aperta. Le richieste sul server di mantenere tutte le connessioni non sono specifiche per le app Blazor. Data la natura persistente delle connessioni e la natura con stato delle app del server Blazor, l'esaurimento della connessione è un rischio maggiore per la disponibilità dell'app.

Per impostazione predefinita, non esiste alcun limite al numero di connessioni per utente per un'app Blazor Server. Se l'app richiede un limite di connessione, adottare uno o più degli approcci seguenti:

* Richiedere l'autenticazione, che limita naturalmente la capacità degli utenti non autorizzati di connettersi all'app. Affinché questo scenario sia efficace, è necessario impedire agli utenti di eseguire il provisioning di nuovi utenti a livello di volontà.
* Limitare il numero di connessioni per utente. La limitazione delle connessioni può essere eseguita tramite i seguenti approcci. Esercitare attenzione per consentire agli utenti legittimi di accedere all'app (ad esempio, quando viene stabilito un limite di connessione in base all'indirizzo IP del client).
  * A livello di app:
    * Estendibilità del routing degli endpoint.
    * Richiedere l'autenticazione per connettersi all'app e tenere traccia delle sessioni attive per utente.
    * Rifiuta nuove sessioni al raggiungimento di un limite.
    * Proxy WebSocket connessioni a un'app tramite l'uso di un proxy, ad esempio il [servizio SignalR](/azure/azure-signalr/signalr-overview) di Azure che multiplexa le connessioni dai client a un'app. Ciò fornisce a un'app una capacità di connessione maggiore di quella che un singolo client può stabilire, impedendo a un client di esaurire le connessioni al server.
  * A livello di server: usare un proxy/gateway davanti all'app. Ad esempio, [il portefronte](/azure/frontdoor/front-door-overview) di Azure consente di definire, gestire e monitorare il routing globale del traffico Web a un'app.

## <a name="denial-of-service-dos-attacks"></a>Attacchi DoS (Denial of Service)

Gli attacchi DoS (Denial of Service) coinvolgono un client che causa l'esaurimento di una o più risorse da parte del server che rende l'app non disponibile. Le app di Blazor Server includono alcuni limiti predefiniti e si basano su altri limiti ASP.NET Core e SignalR per proteggersi dagli attacchi DoS:

| Limite dell'app Blazor Server                            | Descrizione | Predefinito |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | Numero massimo di circuiti disconnessi che un determinato server conserva in memoria alla volta. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | Quantità massima di tempo in cui un circuito disconnesso viene mantenuto in memoria prima di essere abbattuto. | 3 minuti |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | Quantità massima di tempo di attesa del server prima del timeout di una chiamata asincrona di funzione JavaScript. | 1 minuto |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | Numero massimo di batch di rendering non riconosciuti che il server conserva in memoria per circuito in un determinato momento per supportare una riconnessione affidabile. Dopo aver raggiunto il limite, il server interrompe la produzione di nuovi batch di rendering fino a quando uno o più batch non sono stati riconosciuti dal client. | 10 |


| SignalR e ASP.NET Limite Core             | Descrizione | Predefinito |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | Dimensione del messaggio per un singolo messaggio. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Interazioni con il browser (client)

Un client interagisce con il server tramite l'invio di eventi di interoperabilità JS e il completamento del rendering. La comunicazione di interoperabilità JS va in entrambe le direzioni tra JavaScript e .NET:

* Gli eventi del browser vengono inviati dal client al server in modo asincrono.
* Il server risponde in modo asincrono nuovo il rendering dell'interfaccia utente in base alle esigenze.

### <a name="javascript-functions-invoked-from-net"></a>Funzioni JavaScript richiamate da .NET

Per le chiamate da metodi .NET a JavaScript:

* Tutte le chiamate hanno un timeout configurabile dopo <xref:System.OperationCanceledException> il quale hanno esito negativo, restituendo un al chiamante.
  * Esiste un timeout predefinito per`CircuitOptions.JSInteropDefaultCallTimeout`le chiamate ( ) di un minuto. Per configurare questo <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>limite, vedere .
  * È possibile fornire un token di annullamento per controllare l'annullamento per ogni chiamata. Fare affidamento sul timeout di chiamata predefinito, se possibile, e per tutte le chiamate al client se viene fornito un token di annullamento.
* Il risultato di una chiamata JavaScript non può essere considerato attendibile. Il Blazor client dell'app in esecuzione nel browser cerca la funzione JavaScript da richiamare. La funzione viene richiamata e viene generato il risultato o un errore. Un client dannoso può tentare di:
  * Causa un problema nell'app restituendo un errore dalla funzione JavaScript.
  * Indurre un comportamento indesiderato sul server restituendo un risultato imprevisto dalla funzione JavaScript.

Prendere le seguenti precauzioni per proteggersi dagli scenari precedenti:

* Eseguire il wrapping delle chiamate di interoperabilità JS all'interno di istruzioni [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) per tenere conto degli errori che potrebbero verificarsi durante le chiamate. Per altre informazioni, vedere <xref:blazor/handle-errors#javascript-interop>.
* Convalidare i dati restituiti dalle chiamate di interoperabilità JS, inclusi i messaggi di errore, prima di eseguire qualsiasi azione.

### <a name="net-methods-invoked-from-the-browser"></a>Metodi .NET richiamati dal browser

Non considerare attendibili le chiamate da JavaScript ai metodi .NET. Quando un metodo .NET viene esposto a JavaScript, considerare come viene richiamato il metodo .NET:When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:

* Tratta qualsiasi metodo .NET esposto a JavaScript come se si trattasse di un endpoint pubblico per l'app.
  * Convalidare l'input.
    * Assicurarsi che i valori siano compresi negli intervalli previsti.
    * Assicurarsi che l'utente disponga dell'autorizzazione per eseguire l'azione richiesta.
  * Non allocare una quantità eccessiva di risorse come parte della chiamata al metodo .NET. Ad esempio, eseguire controlli e porre limiti all'utilizzo della CPU e della memoria.
  * Tenere conto del fatto che i metodi statici e di istanza possono essere esposti ai client JavaScript. Evitare di condividere lo stato tra le sessioni, a meno che la progettazione non richieda lo stato di condivisione con vincoli appropriati.
    * Per i metodi `DotNetReference` di istanza esposti tramite oggetti creati in origine tramite inserimento delle dipendenze (DI), gli oggetti devono essere registrati come oggetti con ambito. Questo vale per qualsiasi Blazor servizio DI utilizzato dall'app Server.
    * Per i metodi statici, evitare di stabilire uno stato che non può essere definito nel client a meno che l'app non condivida in modo esplicito lo stato in base alla progettazione tra tutti gli utenti in un'istanza del server.
  * Evitare di passare i dati forniti dall'utente nei parametri alle chiamate JavaScript. Se il passaggio dei dati nei parametri è assolutamente necessario, assicurarsi che il codice JavaScript gestisca il passaggio dei dati senza introdurre vulnerabilità [di cross-site scripting (XSS).](#cross-site-scripting-xss) Ad esempio, non scrivere dati forniti dall'utente nel modello DOM `innerHTML` (Document Object Model) impostando la proprietà di un elemento. Prendere in considerazione l'utilizzo di `eval` criteri di sicurezza del contenuto [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) per disabilitare e altre primitive JavaScript non sicure.
* Evitare di implementare l'invio personalizzato delle chiamate .NET in cima all'implementazione di invio del framework. L'esposizione di metodi .NET al browser è Blazor uno scenario avanzato, non consigliato per lo sviluppo generale.

### <a name="events"></a>Events

Gli eventi forniscono Blazor un punto di ingresso a un'app Server.Events provide an entry point to a Server app. Le stesse regole per la protezione degli endpoint Blazor nelle app Web si applicano alla gestione degli eventi nelle app server. Un client dannoso può inviare tutti i dati che desidera inviare come payload per un evento.

Ad esempio:

* Un evento di `<select>` modifica per un oggetto potrebbe inviare un valore che non è all'interno delle opzioni che l'app ha presentato al client.
* Un `<input>` può inviare dati di testo al server, ignorando la convalida lato client.

L'app deve convalidare i dati per qualsiasi evento che gestisce l'app. Il Blazor framework [forma i componenti](xref:blazor/forms-validation) che eseguono convalide di base. Se l'app usa componenti di moduli personalizzati, è necessario scrivere codice personalizzato per convalidare i dati dell'evento in base alle esigenze.

BlazorGli eventi del server sono asincroni, pertanto è possibile inviare più eventi al server prima che l'app abbia il tempo di reagire producendo un nuovo rendering. Ciò ha alcune implicazioni di sicurezza da considerare. La limitazione delle azioni client nell'app deve essere eseguita all'interno dei gestori eventi e non dipende dallo stato di visualizzazione di cui è stato eseguito il rendering corrente.

Si consideri un componente contatore che deve consentire all'utente di incrementare un contatore per un massimo di tre volte. Il pulsante per incrementare il contatore `count`è basato in modo condizionale sul valore di :

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Un client può inviare uno o più eventi di incremento prima che il framework produca un nuovo rendering di questo componente. Il risultato `count` è che l'oggetto può essere incrementato *di tre volte* dall'utente perché il pulsante non viene rimosso dall'interfaccia utente abbastanza rapidamente. Il modo corretto per raggiungere `count` il limite di tre incrementi è illustrato nell'esempio seguente:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

Aggiungendo il `if (count < 3) { ... }` controllo all'interno del `count` gestore, la decisione di incrementare si basa sullo stato corrente dell'app. La decisione non si basa sullo stato dell'interfaccia utente come nell'esempio precedente, che potrebbe essere temporaneamente obsoleto.

### <a name="guard-against-multiple-dispatches"></a>Guardia contro più dispacci

Se un callback di eventi richiama un'operazione a esecuzione prolungata in modo asincrono, ad esempio il recupero di dati da un database o da un servizio esterno, è consigliabile usare una protezione. La guardia può impedire all'utente di accodare più operazioni mentre l'operazione è in corso con il feedback visivo. Il codice del `isLoading` `true` componente `GetForecastAsync` seguente imposta su while ottiene i dati dal server. Mentre `isLoading` `true`è , il pulsante è disabilitato nell'interfaccia utente:

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

Il modello di protezione illustrato nell'esempio precedente funziona se l'operazione in background viene eseguita in modo asincrono con il `async` - `await` modello.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Annullare in anticipo ed evitare l'uso dopo la smaltimento

Oltre a utilizzare una guardia come descritto nella sezione [Protezione contro più dispacci,](#guard-against-multiple-dispatches) è consigliabile utilizzare un <xref:System.Threading.CancellationToken> oggetto per annullare le operazioni a esecuzione prolungata quando il componente viene eliminato. Questo approccio ha l'ulteriore vantaggio di evitare l'uso dopo la smaltimento nei componenti:This approach has the added vantaggio of avoiding *use-after-dispose* in components:

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Evitare eventi che producono grandi quantità di datiAvoid events that produce large amounts of data

Alcuni eventi DOM, `oninput` `onscroll`ad esempio o , possono produrre una grande quantità di dati. Evitare di Blazor utilizzare questi eventi nelle app server.

## <a name="additional-security-guidance"></a>Ulteriori indicazioni sulla sicurezza

Le linee guida per la Blazor protezione di ASP.NET le app Core si applicano alle app server e sono illustrate nelle sezioni seguenti:

* [Registrazione e dati sensibili](#logging-and-sensitive-data)
* [Proteggere le informazioni in transito con HTTPS](#protect-information-in-transit-with-https)
* [Cross-site scripting (XSS)](#cross-site-scripting-xss))
* [Protezione tra origini](#cross-origin-protection)
* [Click-jacking](#click-jacking)
* [Reindirizzamenti aperti](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Registrazione e dati sensibili

Le interazioni di interoperabilità JS tra il client e <xref:Microsoft.Extensions.Logging.ILogger> il server vengono registrate nei log del server con istanze. Blazorevita la registrazione di informazioni riservate, ad esempio eventi effettivi o input e output di interoperabilità JS.

Quando si verifica un errore sul server, il framework notifica al client e annulla la sessione. Per impostazione predefinita, il client riceve un messaggio di errore generico che può essere visualizzato negli strumenti di sviluppo del browser.

L'errore sul lato client non include lo stack di chiamate e non fornisce dettagli sulla causa dell'errore, ma i log del server contengono tali informazioni. Ai fini dello sviluppo, le informazioni sensibili sugli errori possono essere rese disponibili al client abilitando errori dettagliati.

Abilita errori dettagliati con:

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`chiave di configurazione. Ad esempio, `ASPNETCORE_DETAILEDERRORS` impostare la variabile `true`di ambiente su un valore di .

> [!WARNING]
> L'esposizione di informazioni sugli errori ai client su Internet è un rischio per la sicurezza che deve sempre essere evitato.

### <a name="protect-information-in-transit-with-https"></a>Proteggere le informazioni in transito con HTTPS

BlazorIl SignalR server utilizza per la comunicazione tra il client e il server. BlazorServer utilizza in SignalR genere il trasporto che negozia, che in genere è WebSockets.

BlazorServer non garantisce l'integrità e la riservatezza dei dati inviati tra il server e il client. Utilizzare sempre HTTPS.

### <a name="cross-site-scripting-xss"></a>Cross-site scripting (XSS)

Il Cross-site scripting (XSS) consente a un'entità non autorizzata di eseguire logica arbitraria nel contesto del browser. Un'app compromessa potrebbe potenzialmente eseguire codice arbitrario sul client. La vulnerabilità potrebbe essere utilizzato per eseguire potenzialmente una serie di azioni dannose contro il server:

* Inviare eventi falsi/non validi al server.
* Completamenti di rendering non riusciti/non validi dell'invio.
* Evitare di inviare completamenti di rendering.
* Inviare chiamate di interoperabilità da JavaScript a .NET.
* Modificare la risposta delle chiamate di interoperabilità da .NET a JavaScript.
* Evitare di inviare .NET ai risultati di interoperabilità JS.

Il Blazor framework del server adotta le misure necessarie per la protezione da alcune delle minacce precedenti:

* Interrompe la produzione di nuovi aggiornamenti dell'interfaccia utente se il client non riconosce i batch di rendering. Configurato `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`con .
* Timeout di qualsiasi chiamata da .NET a JavaScript dopo un minuto senza ricevere una risposta dal client. Configurato `CircuitOptions.JSInteropDefaultCallTimeout`con .
* Esegue la convalida di base su tutti gli input provenienti dal browser durante l'interoperabilità JS:
  * I riferimenti .NET sono validi e del tipo previsto dal metodo .NET.
  * Il formato non corretto dei dati non è corretto.
  * Il numero corretto di argomenti per il metodo è presente nel payload.
  * Gli argomenti o il risultato possono essere deserializzati correttamente prima di richiamare il metodo .
* Esegue la convalida di base in tutti gli input provenienti dal browser dagli eventi inviati:
  * L'evento ha un tipo valido.
  * I dati per l'evento possono essere deserializzati.
  * C'è un gestore eventi associato all'evento.

Oltre alle misure di sicurezza implementate dal framework, l'app deve essere codificata dallo sviluppatore per proteggersi dalle minacce e intraprendere le azioni appropriate:In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:

* Convalidare sempre i dati durante la gestione degli eventi.
* Adottare le misure appropriate dopo aver ricevuto dati non validi:
  * Ignorare i dati e restituire. Ciò consente all'app di continuare l'elaborazione delle richieste.
  * Se l'app determina che l'input è illegittimo e non può essere prodotto dal client legittimo, genera un'eccezione. La generazione di un'eccezione abbatte il circuito e termina la sessione.
* Non considerare attendibile il messaggio di errore fornito dai completamenti batch di rendering inclusi nei log. L'errore viene fornito dal client e non può in genere essere considerato attendibile, in quanto il client potrebbe essere compromesso.
* Non considerare attendibile l'input nelle chiamate di interoperabilità JS in entrambe le direzioni tra JavaScript e metodi .NET.
* L'app è responsabile della convalida che il contenuto degli argomenti e dei risultati sia valido, anche se gli argomenti o i risultati vengono deserializzati correttamente.

Affinché esista una vulnerabilità XSS, l'app deve incorporare l'input dell'utente nella pagina di cui è stato eseguito il rendering. BlazorI componenti server eseguono un passaggio in fase di compilazione in cui il markup in un file *con estensione razor* viene trasformato in logica procedurale di C. In fase di esecuzione, la logica di C' compila una *struttura ad albero* di rendering che descrive gli elementi, il testo e i componenti figlio. Questo viene applicato al DOM del browser tramite una sequenza di istruzioni JavaScript (o viene serializzato in HTML nel caso di prerendering):

* L'input dell'utente eseguito il `@someStringValue`rendering tramite la normale sintassi Razor (ad esempio, ) non espone una vulnerabilità XSS perché la sintassi Razor viene aggiunta al DOM tramite comandi che possono scrivere solo testo. Anche se il valore include markup HTML, il valore viene visualizzato come testo statico. Durante il prerendering, l'output è codificato in HTML, che visualizza anche il contenuto come testo statico.
* I tag di script non sono consentiti e non devono essere inclusi nell'albero di rendering dei componenti dell'app. Se un tag di script è incluso nel markup di un componente, viene generato un errore in fase di compilazione.
* Gli autori dei componenti possono creare componenti in C, senza usare Razor. L'autore del componente è responsabile dell'utilizzo delle API corrette quando si genera l'output. Ad esempio, `builder.AddContent(0, someUserSuppliedString)` utilizzare e *non* `builder.AddMarkupContent(0, someUserSuppliedString)`, poiché quest'ultimo potrebbe creare una vulnerabilità XSS.

Come parte della protezione contro gli attacchi XSS, prendere in considerazione l'implementazione di attenuazioni XSS, ad esempio [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).

Per altre informazioni, vedere <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Protezione tra origini

Gli attacchi tra origini coinvolgono un client di origine diversa che esegue un'azione sul server. L'azione dannoso è in genere una richiesta GET o un modulo POST (Cross-Site Request Forgery, CSRF), ma l'apertura di un WebSocket dannoso è anche possibile. BlazorLe app server offrono [le SignalR stesse garanzie che qualsiasi altra app che usa il protocollo hub offre:](xref:signalr/security)

* BlazorÈ possibile accedere alle app server tra origini diverse, a meno che non vengano adottate misure aggiuntive per impedirlo. Per disabilitare l'accesso tra origini, disabilitare CORS nell'endpoint aggiungendo il `DisableCorsAttribute` middleware CORS alla pipeline e aggiungendo il server ai metadati dell'endpoint Blazor o limitare il set di origini consentite [configurando SignalR la condivisione](xref:signalr/security#cross-origin-resource-sharing)delle risorse tra origini.
* Se CORS è abilitato, potrebbero essere necessari passaggi aggiuntivi per proteggere l'app in base alla configurazione di CORS. Se CORS è abilitato a livello globale, Blazor CORS può `DisableCorsAttribute` essere disabilitato per `hub.MapBlazorHub()`l'hub Server aggiungendo i metadati ai metadati dell'endpoint dopo la chiamata a .

Per altre informazioni, vedere <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Click-jacking

Click-jacking comporta il rendering `<iframe>` di un sito come un all'interno di un sito da un'origine diversa al fine di indurre l'utente a eseguire azioni sul sito sotto attacco.

Per proteggere un'app dal `<iframe>`rendering all'interno di un `X-Frame-Options` oggetto , usa Criteri di sicurezza del contenuto [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) e l'intestazione. Per ulteriori informazioni, consultate [Documenti Web MDN: Opzioni X-Frame](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Reindirizzamenti aperti

Quando Blazor viene avviata una sessione dell'app Server, il server esegue la convalida di base degli URL inviati come parte dell'avvio della sessione. Il framework verifica che l'URL di base sia un elemento padre dell'URL corrente prima di stabilire il circuito. Il framework non esegue ulteriori controlli.

Quando un utente seleziona un collegamento sul client, l'URL del collegamento viene inviato al server, che determina l'azione da eseguire. Ad esempio, l'app può eseguire una navigazione sul lato client o indicare al browser per passare alla nuova posizione.

I componenti possono anche attivare le `NavigationManager`richieste di navigazione a livello di codice tramite l'utilizzo di . In tali scenari, l'app potrebbe eseguire una navigazione sul lato client o indicare al browser di passare alla nuova posizione.

I componenti devono:

* Evitare di utilizzare l'input dell'utente come parte degli argomenti della chiamata di navigazione.
* Convalidare gli argomenti per assicurarsi che la destinazione sia consentita dall'app.

In caso contrario, un utente malintenzionato può forzare il browser a passare a un sito controllato da un utente malintenzionato. In questo scenario, l'utente malintenzionato trucchi l'applicazione `NavigationManager.Navigate` in utilizzare l'input dell'utente come parte della chiamata del metodo.

Questo consiglio si applica anche quando si esegue il rendering dei collegamenti come parte dell'app:

* Se possibile, utilizzare collegamenti relativi.
* Verificare che le destinazioni dei collegamenti assoluti siano valide prima di includerle in una pagina.

Per altre informazioni, vedere <xref:security/preventing-open-redirects>.

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

Per istruzioni su autenticazione <xref:security/blazor/index>e autorizzazione, vedere .

## <a name="security-checklist"></a>Elenco di controllo relativo alla sicurezza

Il seguente elenco di considerazioni sulla sicurezza non è esaustivo:

* Convalidare gli argomenti dagli eventi.
* Convalidare gli input e i risultati dalle chiamate di interoperabilità JS.
* Evitare di utilizzare (o convalidare in anticipo) l'input dell'utente per le chiamate di interoperabilità da .NET a JS.
* Impedire al client di allocare una quantità di memoria non associata.
  * Dati all'interno del componente.
  * `DotNetObject`riferimenti restituiti al client.
* Protezione da più dispacci.
* Annullare le operazioni a esecuzione prolungata quando il componente viene eliminato.
* Evitare eventi che producono grandi quantità di dati.
* Evitare di usare l'input dell'utente come parte delle chiamate `NavigationManager.Navigate` e convalidare l'input dell'utente per gli URL rispetto a un set di origini consentite, se inevitabile.
* Non prendere decisioni di autorizzazione in base allo stato dell'interfaccia utente, ma solo dallo stato del componente.
* Valutare la possibilità di utilizzare i criteri di [sicurezza del contenuto (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) per la protezione contro gli attacchi XSS.
* Prendi in considerazione l'uso di CSP e [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) per proteggerti dal click-jacking.
* Verificare che le impostazioni CORS siano appropriate Blazor quando si abilita CORS o si disabilita esplicitamente CORS per le app.
* Verifica che i limiti sul lato server per l'app Blazor forniscano un'esperienza utente accettabile senza livelli di rischio inaccettabili.
