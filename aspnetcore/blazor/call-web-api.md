---
title: Chiamare un'API Web da ASP.NET Core Blazor webassembly
author: guardrex
description: Informazioni su come chiamare un'API Web da un' Blazor app webassembly usando Helper JSON, inclusa la creazione di richieste di condivisione di risorse tra le origini (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: abc546cc0079a01e3999b2c7c083235d3fff9b06
ms.sourcegitcommit: e94ecfae6a3ef568fa197da791c8bc595917d17a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82122228"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Chiamare un'API Web da ASP.NET Core Blazer

Di [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Le app [Webassembly Blazer](xref:blazor/hosting-models#blazor-webassembly) chiamano API Web usando un `HttpClient` servizio preconfigurato. Comporre richieste, che possono includere le opzioni dell' [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, usando Helper JSON Blazer <xref:System.Net.Http.HttpRequestMessage>o con. Il `HttpClient` servizio nelle app webassembly di Blazer è incentrato sulla restituzione delle richieste al server di origine. Le indicazioni fornite in questo argomento riguardano solo le app webassembly blazer.

Le app del [Server Blazer](xref:blazor/hosting-models#blazor-server) chiamano API <xref:System.Net.Http.HttpClient> Web usando le istanze, <xref:System.Net.Http.IHttpClientFactory>in genere create usando. Le indicazioni fornite in questo argomento non riguardano le app del server blazer. Quando si sviluppano app Server blazer, seguire le <xref:fundamentals/http-requests>istruzioni riportate in.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([come scaricare](xref:index#how-to-download-a-sample)) &ndash; selezionare l'app *BlazorWebAssemblySample* .

Vedere i componenti seguenti nell'app di esempio *BlazorWebAssemblySample* :

* Chiama API Web (*pages/CallWebAPI. Razor*)
* Tester richieste HTTP (*Components/HTTPRequestTester. Razor*)

## <a name="packages"></a>Pacchetti

Fare riferimento al pacchetto NuGet [System .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) nel file di progetto.

## <a name="add-the-httpclient-service"></a>Aggiungere il servizio HttpClient

In `Program.Main`aggiungere un `HttpClient` servizio se non esiste già:

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Helper HttpClient e JSON

In un'app webassembly blazer, [HttpClient](xref:fundamentals/http-requests) è disponibile come servizio preconfigurato per eseguire richieste al server di origine.

Per impostazione predefinita, un'app del server `HttpClient` Blazer non include un servizio. Fornire un `HttpClient` all'app usando l' [infrastruttura di HttpClient Factory](xref:fundamentals/http-requests).

`HttpClient`gli helper JSON e vengono usati anche per chiamare endpoint dell'API Web di terze parti. `HttpClient`viene implementato usando l' [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) del browser ed è soggetto alle limitazioni, inclusa l'applicazione degli stessi criteri di origine.

L'indirizzo di base del client viene impostato sull'indirizzo del server di origine. Inserire un' `HttpClient` istanza usando la `@inject` direttiva:

```razor
@using System.Net.Http
@inject HttpClient Http
```

Negli esempi seguenti, un'API Web todo elabora le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD). Gli esempi sono basati su una `TodoItem` classe che archivia:

* ID (`Id`, `long`) &ndash; ID univoco dell'elemento.
* Nome (`Name`, `string`) &ndash; nome dell'elemento.
* Status (`IsComplete`, `bool`) &ndash; indica se l'elemento todo è terminato.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

I metodi helper JSON inviano richieste a un URI (un'API Web negli esempi seguenti) ed elaborano la risposta:

* `GetFromJsonAsync`&ndash; Invia una richiesta HTTP GET e analizza il corpo della risposta JSON per creare un oggetto.

  Nel codice seguente, l'oggetto `_todoItems` viene visualizzato dal componente. Il `GetTodoItems` metodo viene attivato al termine del rendering del componente ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Per un esempio completo, vedere l'app di esempio.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash; Invia una richiesta HTTP post, incluso il contenuto con codifica JSON, e analizza il corpo della risposta JSON per creare un oggetto.

  Nel codice seguente, `_newItemName` viene fornito da un elemento associato del componente. Il `AddItem` metodo viene attivato selezionando un `<button>` elemento. Per un esempio completo, vedere l'app di esempio.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  Chiama per `PostAsJsonAsync` restituire un <xref:System.Net.Http.HttpResponseMessage>oggetto. Per deserializzare il contenuto JSON dal messaggio di risposta, usare il `ReadFromJsonAsync<T>` metodo di estensione:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync`&ndash; Invia una richiesta HTTP PUT, incluso il contenuto con codifica JSON.

  Nel codice seguente, `_editItem` i valori per `Name` e `IsCompleted` vengono forniti dagli elementi associati del componente. L'elemento `Id` viene impostato quando l'elemento viene selezionato in un'altra parte dell'interfaccia utente e `EditItem` viene chiamato. Il `SaveItem` metodo viene attivato selezionando l'elemento Save `<button>` . Per un esempio completo, vedere l'app di esempio.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```
  
  Chiama per `PutAsJsonAsync` restituire un <xref:System.Net.Http.HttpResponseMessage>oggetto. Per deserializzare il contenuto JSON dal messaggio di risposta, usare il `ReadFromJsonAsync<T>` metodo di estensione:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>include metodi di estensione aggiuntivi per l'invio di richieste HTTP e la ricezione di risposte HTTP. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) viene usato per inviare una richiesta HTTP DELETE a un'API Web.

Nel codice seguente, l'elemento Delete `<button>` chiama il `DeleteItem` metodo. L'elemento `<input>` associato fornisce l' `id` oggetto dell'elemento da eliminare. Per un esempio completo, vedere l'app di esempio.

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>Condivisione di risorse tra le origini (CORS)

La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina Web. Questa restrizione è detta *criterio della stessa origine*. Il criterio della stessa origine impedisce a un sito dannoso di leggere dati sensibili da un altro sito. Per eseguire richieste dal browser a un endpoint con un'origine diversa, l' *endpoint* deve abilitare la [condivisione di risorse tra le origini (CORS)](https://www.w3.org/TR/cors/).

L' [app di esempio Webassembly blazer (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) illustra l'uso di CORS nel componente API Web Call (*pages/CallWebAPI. Razor*).

Per consentire ad altri siti di effettuare richieste di condivisione di risorse tra le origini (CORS) per l' <xref:security/cors>app, vedere.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient e HttpRequestMessage con le opzioni di richiesta dell'API fetch

Quando si esegue il webassembly in un'app webassembly blazer, [HttpClient](xref:fundamentals/http-requests) usare HttpClient <xref:System.Net.Http.HttpRequestMessage> e per personalizzare le richieste. Ad esempio, è possibile specificare l'URI della richiesta, il metodo HTTP e tutte le intestazioni di richiesta desiderate.

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                JsonContent.Create(new TodoItem
                { 
                    Name: "A New Todo Item",
                    IsComplete: false
                })
        };
        
        requestMessage.Headers.Authorization = 
           new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

L'implementazione di `HttpClient` .NET webassembly USA [WindowOrWorkerGlobalScope. fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). Il recupero consente la configurazione [di diverse opzioni specifiche della richiesta](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Le opzioni di richiesta di recupero HTTP possono `HttpRequestMessage` essere configurate con i metodi di estensione illustrati nella tabella seguente.

| `HttpRequestMessage`Metodo di estensione | Recupera la proprietà della richiesta |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [credenziali](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [integrità](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

È possibile impostare opzioni aggiuntive usando il metodo di `SetBrowserRequestOption` estensione più generico.
 
La risposta HTTP viene in genere memorizzata nel Blazor buffer in un'app webassembly per abilitare il supporto per le letture della sincronizzazione sul contenuto della risposta. Per abilitare il supporto per il flusso di risposta `SetBrowserResponseStreamingEnabled` , usare il metodo di estensione nella richiesta.

Per includere le credenziali in una richiesta tra più origini, usare `SetBrowserRequestCredentials` il metodo di estensione:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Per altre informazioni sulle opzioni di recupero delle API, vedere la pagina relativa alla [documentazione Web MDN: WindowOrWorkerGlobalScope. fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Quando si inviano le credenziali (cookie/intestazioni di autorizzazione) nelle `Authorization` richieste CORS, l'intestazione deve essere consentita dal criterio CORS.

Il criterio seguente include la configurazione per:

* Origin della richiesta`http://localhost:5000`( `https://localhost:5001`,).
* Qualsiasi metodo (verbo).
* `Content-Type`e `Authorization` intestazioni. Per consentire un'intestazione personalizzata (ad esempio, `x-custom-header`), elencare l'intestazione quando <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>si chiama.
* Credenziali impostate dal codice JavaScript lato client (`credentials` proprietà impostata su `include`).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Per ulteriori informazioni, vedere <xref:security/cors> e il componente tester richiesta HTTP dell'app di esempio (*Components/HTTPRequestTester. Razor*).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Richiedere token di accesso aggiuntivi](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Connetti token alle richieste in uscita](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configurazione dell'endpoint HTTPS di gheppio](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Condivisione di risorse tra le origini (CORS) in W3C](https://www.w3.org/TR/cors/)
