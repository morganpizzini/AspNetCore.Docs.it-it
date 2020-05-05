---
title: Chiamare un'API Web da ASP.NET Core Blazor webassembly
author: guardrex
description: Informazioni su come chiamare un'API Web da un' Blazor app webassembly usando Helper JSON, inclusa la creazione di richieste di condivisione di risorse tra le origini (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: d823db3688e05f6befefacc9f390e0dcdbf329a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767148"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Chiamare un'API Web da ASP.NET CoreBlazor

Di [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Le app webassembly chiamano API Web usando un `HttpClient` servizio preconfigurato. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) Comporre richieste, che possono includere opzioni [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, Blazor usando Helper JSON o con <xref:System.Net.Http.HttpRequestMessage>. Il `HttpClient` servizio nelle Blazor app webassembly è incentrato sulla restituzione delle richieste al server di origine. Le indicazioni fornite in questo argomento riguardano Blazor solo le app webassembly.

Le app server chiamano API Web <xref:System.Net.Http.HttpClient> usando le istanze, in <xref:System.Net.Http.IHttpClientFactory>genere create usando. [ Blazor ](xref:blazor/hosting-models#blazor-server) Le indicazioni fornite in questo argomento non riguardano Blazor le app Server. Quando si Blazor sviluppano app Server, seguire le <xref:fundamentals/http-requests>istruzioni riportate in.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([come scaricare](xref:index#how-to-download-a-sample)) &ndash; selezionare l'app *BlazorWebAssemblySample* .

Vedere i componenti seguenti nell'app di esempio *BlazorWebAssemblySample* :

* Chiama API Web (*pages/CallWebAPI. Razor*)
* Tester richieste HTTP (*Components/HTTPRequestTester. Razor*)

## <a name="packages"></a>Pacchetti

Fare riferimento al pacchetto NuGet [System .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) nel file di progetto.

## <a name="add-the-httpclient-service"></a>Aggiungere il servizio HttpClient

In `Program.Main`aggiungere un `HttpClient` servizio se non esiste già:

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Helper HttpClient e JSON

In un' Blazor app webassembly, [HttpClient](xref:fundamentals/http-requests) è disponibile come servizio preconfigurato per eseguire richieste al server di origine.

Per Blazor impostazione predefinita, un'app `HttpClient` server non include un servizio. Fornire un `HttpClient` all'app usando l' [infrastruttura di HttpClient Factory](xref:fundamentals/http-requests).

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

L' [ Blazor app di esempio webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) illustra l'uso di CORS nel componente API Web Call (*pages/CallWebAPI. Razor*).

Per consentire ad altri siti di effettuare richieste di condivisione di risorse tra le origini (CORS) per l' <xref:security/cors>app, vedere.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configurazione dell'endpoint HTTPS di gheppio](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Condivisione di risorse tra le origini (CORS) in W3C](https://www.w3.org/TR/cors/)
