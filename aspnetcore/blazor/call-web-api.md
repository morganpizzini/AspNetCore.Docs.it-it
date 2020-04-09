---
title: Chiamare un'API Web da ASP.NET CoreCall a web API from ASP.NET CoreBlazor
author: guardrex
description: Informazioni su come chiamare un'API Web da un'app Blazor usando helper JSON, inclusa la creazione di richieste di condivisione delle risorse tra origini (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: e6996f0e6731b05038d0a9329152b8afd5f6796d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660146"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>Chiamare un'API Web da ASP.NET CoreCall a web API from ASP.NET CoreBlazor

Di [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), e Juan De la [Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Le app WebAssembly chiamano le API `HttpClient` Web usando un servizio preconfigurato. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) Comporre le richieste, che possono includere le Blazor opzioni [dell'API JavaScript Fetch,](https://developer.mozilla.org/docs/Web/API/Fetch_API) usando helper JSON o con <xref:System.Net.Http.HttpRequestMessage>. Il `HttpClient` servizio Blazor nelle app WebAssembly è incentrato sull'esecuzione di richieste al server di origine. Le indicazioni contenute in questo argomento Blazor riguardano solo le app WebAssembly.

Le app server chiamano <xref:System.Net.Http.HttpClient> le API Web <xref:System.Net.Http.IHttpClientFactory>usando le istanze, in genere create utilizzando . [ Blazor ](xref:blazor/hosting-models#blazor-server) Le indicazioni contenute in questo argomento Blazor non riguardano le app server. Quando Blazor si sviluppano app <xref:fundamentals/http-requests>server, seguire le indicazioni in .

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( come[scaricare](xref:index#how-to-download-a-sample)) &ndash; Selezionare l'app *BlazorWebAssemblySample.*

Vedere i componenti seguenti nell'app di esempio *BlazorWebAssemblySample:See* the following components in the BlazorWebAssemblySample sample app:

* Chiamare api Web (*Pages/CallWebAPI.razor*)
* Tester richieste HTTP (*Componenti/HTTPRequestTester.razor*)

## <a name="packages"></a>Pacchetti

Fare riferimento *all'oggetto sperimentale* [BlazorMicrosoft.AspNetCore. . Pacchetto HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet nel file di progetto. `Microsoft.AspNetCore.Blazor.HttpClient`è basato su `HttpClient` e [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).

Per utilizzare un'API stabile, utilizzare il pacchetto [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) , che utilizza [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm). L'uso dell'API stable in `Microsoft.AspNet.WebApi.Client` non fornisce gli helper JSON descritti `Microsoft.AspNetCore.Blazor.HttpClient` in questo argomento, che sono univoci per il pacchetto sperimentale.

## <a name="httpclient-and-json-helpers"></a>Helper HttpClient e JSON

In Blazor un'app WebAssembly [HttpClient](xref:fundamentals/http-requests) è disponibile come servizio preconfigurato per l'esecuzione di richieste al server di origine.

Un'app Blazor Server non `HttpClient` include un servizio per impostazione predefinita. Fornire `HttpClient` un all'app utilizzando l'infrastruttura [di fabbrica HttpClient](xref:fundamentals/http-requests).

`HttpClient`e gli helper JSON vengono utilizzati anche per chiamare endpoint API Web di terze parti. `HttpClient`viene implementato utilizzando [l'API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) del browser ed è soggetto alle sue limitazioni, inclusa l'applicazione degli stessi criteri di origine.

L'indirizzo di base del client è impostato sull'indirizzo del server di origine. Inserire `HttpClient` un'istanza `@inject` usando la direttiva:Inject an instance using the directive:

```razor
@using System.Net.Http
@inject HttpClient Http
```

Negli esempi seguenti, un'API Web Todo elabora le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD). Gli esempi sono `TodoItem` basati su una classe che archivia:

* ID`Id`( `long` &ndash; , ) ID univoco dell'elemento.
* Nome`Name`( `string` &ndash; , ) Nome dell'elemento.
* Stato`IsComplete`( `bool` &ndash; , ) Indicazione se l'elemento Todo è terminato.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

I metodi helper JSON inviano richieste a un URI (un'API Web negli esempi seguenti) ed elaborano la risposta:

* `GetJsonAsync`&ndash; Invia una richiesta HTTP GET e analizza il corpo della risposta JSON per creare un oggetto.

  Nel codice seguente, `_todoItems` vengono visualizzati dal componente. Il `GetTodoItems` metodo viene attivato al termine del rendering del componente ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Vedere l'app di esempio per un esempio completo.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostJsonAsync`&ndash; Invia una richiesta HTTP POST, incluso il contenuto con codifica JSON, e analizza il corpo della risposta JSON per creare un oggetto.

  Nel codice seguente, `_newItemName` viene fornito da un elemento associato del componente. Il `AddItem` metodo viene attivato `<button>` selezionando un elemento. Vedere l'app di esempio per un esempio completo.

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
          await Http.PostJsonAsync("api/TodoItems", addItem);
      }
  }
  ```

* `PutJsonAsync`&ndash; Invia una richiesta HTTP PUT, incluso il contenuto con codifica JSON.

  Nel codice seguente, `_editItem` `Name` i `IsCompleted` valori per e vengono forniti dagli elementi associati del componente. L'elemento `Id` viene impostato quando l'elemento viene selezionato `EditItem` in un'altra parte dell'interfaccia utente e viene chiamato. Il `SaveItem` metodo viene attivato selezionando `<button>` il Save elemento. Vedere l'app di esempio per un esempio completo.

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
          await Http.PutJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```

<xref:System.Net.Http>include metodi di estensione aggiuntivi per l'invio di richieste HTTP e la ricezione di risposte HTTP. [HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) viene utilizzato per inviare una richiesta HTTP DELETE a un'API Web.HttpClient.DeleteAsync is used to send an HTTP DELETE request to a web API.

Nel codice seguente, `<button>` il Delete `DeleteItem` elemento chiama il metodo . L'elemento associato `<input>` `id` fornisce l'oggetto dell'elemento da eliminare. Vedere l'app di esempio per un esempio completo.

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

## <a name="cross-origin-resource-sharing-cors"></a>Condivisione delle risorse tra origini (CORS)

La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina Web. Questa restrizione è *denominata criterio*della stessa origine . I criteri della stessa origine impediscono a un sito dannoso di leggere dati sensibili da un altro sito. Per effettuare richieste dal browser a un endpoint con un'origine diversa, *l'endpoint* deve abilitare la [condivisione delle risorse tra origini (CORS, Cross-Origin Resource Sharing).](https://www.w3.org/TR/cors/)

[ Blazor Nell'app di esempio WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) viene illustrato l'utilizzo di CORS nel componente API Web chiamata (*Pages/CallWebAPI.razor*).

Per consentire ad altri siti di effettuare richieste di condivisione <xref:security/cors>delle risorse tra origini (CORS) all'app, vedere .

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient e HttpRequestMessage con opzioni di richiesta dell'API Fetch

Quando si esegue su Blazor WebAssembly in un'app WebAssembly, utilizzare [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> personalizzare le richieste. Ad esempio, è possibile specificare l'URI della richiesta, il metodo HTTP e le intestazioni di richiesta desiderate.

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

Per ulteriori informazioni sulle opzioni dell'API Fetch, vedere [Documenti Web MDN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Quando si inviano credenziali (cookie di autorizzazione/intestazioni) nelle richieste CORS, l'intestazione `Authorization` deve essere consentita dai criteri CORS.

Il criterio seguente include la configurazione per:

* Origini della`http://localhost:5000`richiesta `https://localhost:5001`( , ).
* Qualsiasi metodo (verbo).
* `Content-Type`e `Authorization` intestazioni. Per consentire un'intestazione `x-custom-header`personalizzata, ad <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>esempio , elencare l'intestazione quando si chiama .
* Credenziali impostate dal codice JavaScript`credentials` lato `include`client ( proprietà impostata su ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Per ulteriori informazioni, vedere <xref:security/cors> e il componente HTTP Request Tester dell'app di esempio (*Components/HTTPRequestTester.razor*).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configurazione dell'endpoint HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Cross Origin Resource Sharing (CORS) presso W3C](https://www.w3.org/TR/cors/)
