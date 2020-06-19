---
title: Chiamare un'API Web da ASP.NET Core Blazor Webassembly
author: guardrex
description: Informazioni su come chiamare un'API Web da un' Blazor app webassembly usando Helper JSON, inclusa la creazione di richieste di condivisione di risorse tra le origini (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 19e10c4eced47676c633a7d30a26726913373162
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102427"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Chiamare un'API Web da ASP.NET CoreBlazor

Di [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)

Le app [ Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) chiamano API Web usando un servizio preconfigurato <xref:System.Net.Http.HttpClient> . Comporre richieste, che possono includere opzioni [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, usando Blazor Helper JSON o con <xref:System.Net.Http.HttpRequestMessage> . Il <xref:System.Net.Http.HttpClient> servizio nelle Blazor app webassembly è incentrato sulla restituzione delle richieste al server di origine. Le indicazioni fornite in questo argomento riguardano solo le Blazor app webassembly.

Le app [ Blazor Server](xref:blazor/hosting-models#blazor-server) chiamano API Web usando le <xref:System.Net.Http.HttpClient> istanze, in genere create usando <xref:System.Net.Http.IHttpClientFactory> . Le indicazioni fornite in questo argomento non riguardano le Blazor app Server. Quando si sviluppano Blazor app Server, seguire le istruzioni riportate in <xref:fundamentals/http-requests> .

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample)): selezionare l'app *BlazorWebAssemblySample* .

Vedere i componenti seguenti nell'app di esempio *BlazorWebAssemblySample* :

* Chiama API Web (*pages/CallWebAPI. Razor*)
* Tester richieste HTTP (*Components/HTTPRequestTester. Razor*)

## <a name="packages"></a>Pacchetti

Fare riferimento al [System.Net.Http.Js](https://www.nuget.org/packages/System.Net.Http.Json/) nel pacchetto NuGet nel file di progetto.

## <a name="add-the-httpclient-service"></a>Aggiungere il servizio HttpClient

In `Program.Main` aggiungere un <xref:System.Net.Http.HttpClient> servizio se non esiste già:

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Helper HttpClient e JSON

In un' Blazor app webassembly, [HttpClient](xref:fundamentals/http-requests) è disponibile come servizio preconfigurato per eseguire richieste al server di origine.

BlazorPer impostazione predefinita, un'app Server non include un <xref:System.Net.Http.HttpClient> servizio. Fornire un <xref:System.Net.Http.HttpClient> all'app usando l' [infrastruttura di HttpClient Factory](xref:fundamentals/http-requests).

<xref:System.Net.Http.HttpClient>gli helper JSON e vengono usati anche per chiamare endpoint dell'API Web di terze parti. <xref:System.Net.Http.HttpClient>viene implementato usando l' [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) del browser ed è soggetto alle limitazioni, inclusa l'applicazione degli stessi criteri di origine.

L'indirizzo di base del client viene impostato sull'indirizzo del server di origine. Inserire un' <xref:System.Net.Http.HttpClient> istanza usando la [`@inject`](xref:mvc/views/razor#inject) direttiva:

```razor
@using System.Net.Http
@inject HttpClient Http
```

Negli esempi seguenti, un'API Web todo elabora le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD). Gli esempi sono basati su una `TodoItem` classe che archivia:

* ID ( `Id` , `long` ): ID univoco dell'elemento.
* Nome ( `Name` , `string` ): nome dell'elemento.
* Status ( `IsComplete` , `bool` ): indica se l'elemento todo è terminato.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

I metodi helper JSON inviano richieste a un URI (un'API Web negli esempi seguenti) ed elaborano la risposta:

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Invia una richiesta HTTP GET e analizza il corpo della risposta JSON per creare un oggetto.

  Nel codice seguente, l'oggetto `todoItems` viene visualizzato dal componente. Il `GetTodoItems` metodo viene attivato al termine del rendering del componente ([OnInitializedAsync](xref:blazor/components/lifecycle#component-initialization-methods)). Per un esempio completo, vedere l'app di esempio.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Invia una richiesta HTTP POST, incluso il contenuto con codifica JSON, e analizza il corpo della risposta JSON per creare un oggetto.

  Nel codice seguente, `newItemName` viene fornito da un elemento associato del componente. Il `AddItem` metodo viene attivato selezionando un `<button>` elemento. Per un esempio completo, vedere l'app di esempio.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  Chiama per <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> restituire un oggetto <xref:System.Net.Http.HttpResponseMessage> . Per deserializzare il contenuto JSON dal messaggio di risposta, usare il `ReadFromJsonAsync<T>` metodo di estensione:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Invia una richiesta HTTP PUT, incluso il contenuto con codifica JSON.

  Nel codice seguente, `editItem` i valori per `Name` e `IsCompleted` vengono forniti dagli elementi associati del componente. L'elemento `Id` viene impostato quando l'elemento viene selezionato in un'altra parte dell'interfaccia utente e `EditItem` viene chiamato. Il `SaveItem` metodo viene attivato selezionando l'elemento Save `<button>` . Per un esempio completo, vedere l'app di esempio.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  Chiama per <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> restituire un oggetto <xref:System.Net.Http.HttpResponseMessage> . Per deserializzare il contenuto JSON dal messaggio di risposta, usare il <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> metodo di estensione:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>include metodi di estensione aggiuntivi per l'invio di richieste HTTP e la ricezione di risposte HTTP. <xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType>viene usato per inviare una richiesta HTTP DELETE a un'API Web.

Nel codice seguente, l'elemento Delete `<button>` chiama il `DeleteItem` metodo. L' `<input>` elemento associato fornisce l' `id` oggetto dell'elemento da eliminare. Per un esempio completo, vedere l'app di esempio.

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a>Denominato HttpClient con IHttpClientFactory

<xref:System.Net.Http.IHttpClientFactory>i servizi e la configurazione di un oggetto denominato <xref:System.Net.Http.HttpClient> sono supportati.

Fare riferimento al pacchetto NuGet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) nel file di progetto.

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

`FetchData`componente (*pages/fetchData. Razor*):

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a>HttpClient tipizzato

Tipizzato <xref:System.Net.Http.HttpClient> Usa una o più istanze dell'app <xref:System.Net.Http.HttpClient> , predefinite o denominate, per restituire i dati da uno o più endpoint dell'API Web.

*WeatherForecastClient.cs*:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastClient
{
    private readonly HttpClient client;

    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];
    
        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }
    
        return forecasts;
    }
}
```

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

I componenti inseriscono il tipizzato <xref:System.Net.Http.HttpClient> per chiamare l'API Web.

`FetchData`componente (*pages/fetchData. Razor*):

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

## <a name="handle-errors"></a>Gestire gli errori

Quando si verificano errori durante l'interazione con un'API Web, possono essere gestiti dal codice dello sviluppatore. <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>Si prevede ad esempio una risposta JSON dall'API del server con un valore `Content-Type` di `application/json` . Se la risposta non è in formato JSON, la convalida del contenuto genera un'eccezione <xref:System.NotSupportedException> .

Nell'esempio seguente l'endpoint URI per la richiesta di dati meteorologici non è stato digitato correttamente. L'URI deve essere, `WeatherForecast` ma viene visualizzato nella chiamata a `WeatherForcast` (mancante "e").

La <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> chiamata prevede che venga restituito JSON, ma il server restituisce il codice HTML per un'eccezione non gestita nel server con un valore `Content-Type` di `text/html` . L'eccezione non gestita si verifica nel server perché il percorso non è stato trovato e il middleware non può gestire una pagina o una vista per la richiesta.

In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> sul client <xref:System.NotSupportedException> viene generata un'eccezione quando il contenuto della risposta viene convalidato come non JSON. L'eccezione viene rilevata nel `catch` blocco, in cui la logica personalizzata può registrare l'errore o presentare un messaggio di errore descrittivo all'utente:

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> L'esempio precedente è a scopo dimostrativo. Un'app Server API Web può essere configurata in modo da restituire JSON anche quando non esiste un endpoint o si verifica un excpetion non gestito nel server.

Per altre informazioni, vedere <xref:blazor/fundamentals/handle-errors>.

## <a name="cross-origin-resource-sharing-cors"></a>Condivisione di risorse tra le origini (CORS)

La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina Web. Questa restrizione è detta *criterio della stessa origine*. Il criterio della stessa origine impedisce a un sito dannoso di leggere dati sensibili da un altro sito. Per eseguire richieste dal browser a un endpoint con un'origine diversa, l' *endpoint* deve abilitare la [condivisione di risorse tra le origini (CORS)](https://www.w3.org/TR/cors/).

L' [ Blazor app di esempio webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) illustra l'uso di CORS nel componente API Web Call (*pages/CallWebAPI. Razor*).

Per consentire ad altri siti di effettuare richieste di condivisione di risorse tra le origini (CORS) per l'app, vedere <xref:security/cors> .

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/security/webassembly/additional-scenarios>: Include la copertura sull'uso <xref:System.Net.Http.HttpClient> di per creare richieste API Web sicure.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configurazione dell'endpoint HTTPS di gheppio](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Condivisione di risorse tra le origini (CORS) in W3C](https://www.w3.org/TR/cors/)
