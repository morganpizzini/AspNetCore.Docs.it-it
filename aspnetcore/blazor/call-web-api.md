---
title: Chiamare un'API Web da ASP.NET Core Blazor webassembly
author: guardrex
description: Informazioni su come chiamare un'API Web da un' Blazor app webassembly usando Helper JSON, inclusa la creazione di richieste di condivisione di risorse tra le origini (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 7476e9dce3fa26948d2091235747f893d805d7be
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967272"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="e16bc-103">Chiamare un'API Web da ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="e16bc-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="e16bc-104">Di [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="e16bc-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="e16bc-105">Le app webassembly chiamano API Web usando un `HttpClient` servizio preconfigurato. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="e16bc-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="e16bc-106">Comporre richieste, che possono includere opzioni [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, Blazor usando Helper JSON o con <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="e16bc-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="e16bc-107">Il `HttpClient` servizio nelle Blazor app webassembly è incentrato sulla restituzione delle richieste al server di origine.</span><span class="sxs-lookup"><span data-stu-id="e16bc-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="e16bc-108">Le indicazioni fornite in questo argomento riguardano Blazor solo le app webassembly.</span><span class="sxs-lookup"><span data-stu-id="e16bc-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="e16bc-109">Le app server chiamano API Web <xref:System.Net.Http.HttpClient> usando le istanze, in <xref:System.Net.Http.IHttpClientFactory>genere create usando. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="e16bc-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="e16bc-110">Le indicazioni fornite in questo argomento non riguardano Blazor le app Server.</span><span class="sxs-lookup"><span data-stu-id="e16bc-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="e16bc-111">Quando si Blazor sviluppano app Server, seguire le <xref:fundamentals/http-requests>istruzioni riportate in.</span><span class="sxs-lookup"><span data-stu-id="e16bc-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="e16bc-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([come scaricare](xref:index#how-to-download-a-sample)) &ndash; selezionare l'app *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="e16bc-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="e16bc-113">Vedere i componenti seguenti nell'app di esempio *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="e16bc-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="e16bc-114">Chiama API Web (*pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="e16bc-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="e16bc-115">Tester richieste HTTP (*Components/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="e16bc-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="e16bc-116">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="e16bc-116">Packages</span></span>

<span data-ttu-id="e16bc-117">Fare riferimento al pacchetto NuGet [System .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="e16bc-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="e16bc-118">Aggiungere il servizio HttpClient</span><span class="sxs-lookup"><span data-stu-id="e16bc-118">Add the HttpClient service</span></span>

<span data-ttu-id="e16bc-119">In `Program.Main`aggiungere un `HttpClient` servizio se non esiste già:</span><span class="sxs-lookup"><span data-stu-id="e16bc-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="e16bc-120">Helper HttpClient e JSON</span><span class="sxs-lookup"><span data-stu-id="e16bc-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="e16bc-121">In un' Blazor app webassembly, [HttpClient](xref:fundamentals/http-requests) è disponibile come servizio preconfigurato per eseguire richieste al server di origine.</span><span class="sxs-lookup"><span data-stu-id="e16bc-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="e16bc-122">Per Blazor impostazione predefinita, un'app `HttpClient` server non include un servizio.</span><span class="sxs-lookup"><span data-stu-id="e16bc-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="e16bc-123">Fornire un `HttpClient` all'app usando l' [infrastruttura di HttpClient Factory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="e16bc-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="e16bc-124">`HttpClient`gli helper JSON e vengono usati anche per chiamare endpoint dell'API Web di terze parti.</span><span class="sxs-lookup"><span data-stu-id="e16bc-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="e16bc-125">`HttpClient`viene implementato usando l' [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) del browser ed è soggetto alle limitazioni, inclusa l'applicazione degli stessi criteri di origine.</span><span class="sxs-lookup"><span data-stu-id="e16bc-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="e16bc-126">L'indirizzo di base del client viene impostato sull'indirizzo del server di origine.</span><span class="sxs-lookup"><span data-stu-id="e16bc-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="e16bc-127">Inserire un' `HttpClient` istanza usando la `@inject` direttiva:</span><span class="sxs-lookup"><span data-stu-id="e16bc-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="e16bc-128">Negli esempi seguenti, un'API Web todo elabora le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD).</span><span class="sxs-lookup"><span data-stu-id="e16bc-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="e16bc-129">Gli esempi sono basati su una `TodoItem` classe che archivia:</span><span class="sxs-lookup"><span data-stu-id="e16bc-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="e16bc-130">ID (`Id`, `long`) &ndash; ID univoco dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="e16bc-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="e16bc-131">Nome (`Name`, `string`) &ndash; nome dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="e16bc-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="e16bc-132">Status (`IsComplete`, `bool`) &ndash; indica se l'elemento todo è terminato.</span><span class="sxs-lookup"><span data-stu-id="e16bc-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="e16bc-133">I metodi helper JSON inviano richieste a un URI (un'API Web negli esempi seguenti) ed elaborano la risposta:</span><span class="sxs-lookup"><span data-stu-id="e16bc-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="e16bc-134">`GetFromJsonAsync`&ndash; Invia una richiesta HTTP GET e analizza il corpo della risposta JSON per creare un oggetto.</span><span class="sxs-lookup"><span data-stu-id="e16bc-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="e16bc-135">Nel codice seguente, l'oggetto `todoItems` viene visualizzato dal componente.</span><span class="sxs-lookup"><span data-stu-id="e16bc-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="e16bc-136">Il `GetTodoItems` metodo viene attivato al termine del rendering del componente ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="e16bc-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="e16bc-137">Per un esempio completo, vedere l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="e16bc-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="e16bc-138">`PostAsJsonAsync`&ndash; Invia una richiesta HTTP post, incluso il contenuto con codifica JSON, e analizza il corpo della risposta JSON per creare un oggetto.</span><span class="sxs-lookup"><span data-stu-id="e16bc-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="e16bc-139">Nel codice seguente, `newItemName` viene fornito da un elemento associato del componente.</span><span class="sxs-lookup"><span data-stu-id="e16bc-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="e16bc-140">Il `AddItem` metodo viene attivato selezionando un `<button>` elemento.</span><span class="sxs-lookup"><span data-stu-id="e16bc-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="e16bc-141">Per un esempio completo, vedere l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="e16bc-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="e16bc-142">Chiama per `PostAsJsonAsync` restituire un <xref:System.Net.Http.HttpResponseMessage>oggetto.</span><span class="sxs-lookup"><span data-stu-id="e16bc-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="e16bc-143">Per deserializzare il contenuto JSON dal messaggio di risposta, usare il `ReadFromJsonAsync<T>` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="e16bc-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="e16bc-144">`PutAsJsonAsync`&ndash; Invia una richiesta HTTP PUT, incluso il contenuto con codifica JSON.</span><span class="sxs-lookup"><span data-stu-id="e16bc-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="e16bc-145">Nel codice seguente, `editItem` i valori per `Name` e `IsCompleted` vengono forniti dagli elementi associati del componente.</span><span class="sxs-lookup"><span data-stu-id="e16bc-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="e16bc-146">L'elemento `Id` viene impostato quando l'elemento viene selezionato in un'altra parte dell'interfaccia utente e `EditItem` viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="e16bc-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="e16bc-147">Il `SaveItem` metodo viene attivato selezionando l'elemento Save `<button>` .</span><span class="sxs-lookup"><span data-stu-id="e16bc-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="e16bc-148">Per un esempio completo, vedere l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="e16bc-148">See the sample app for a complete example.</span></span>

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
          var editItem = todoItems.Single(i => i.Id == id);
          editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="e16bc-149">Chiama per `PutAsJsonAsync` restituire un <xref:System.Net.Http.HttpResponseMessage>oggetto.</span><span class="sxs-lookup"><span data-stu-id="e16bc-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="e16bc-150">Per deserializzare il contenuto JSON dal messaggio di risposta, usare il `ReadFromJsonAsync<T>` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="e16bc-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="e16bc-151"><xref:System.Net.Http>include metodi di estensione aggiuntivi per l'invio di richieste HTTP e la ricezione di risposte HTTP.</span><span class="sxs-lookup"><span data-stu-id="e16bc-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="e16bc-152">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) viene usato per inviare una richiesta HTTP DELETE a un'API Web.</span><span class="sxs-lookup"><span data-stu-id="e16bc-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="e16bc-153">Nel codice seguente, l'elemento Delete `<button>` chiama il `DeleteItem` metodo.</span><span class="sxs-lookup"><span data-stu-id="e16bc-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="e16bc-154">L'elemento `<input>` associato fornisce l' `id` oggetto dell'elemento da eliminare.</span><span class="sxs-lookup"><span data-stu-id="e16bc-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="e16bc-155">Per un esempio completo, vedere l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="e16bc-155">See the sample app for a complete example.</span></span>

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

## <a name="handle-errors"></a><span data-ttu-id="e16bc-156">Gestire gli errori</span><span class="sxs-lookup"><span data-stu-id="e16bc-156">Handle errors</span></span>

<span data-ttu-id="e16bc-157">Quando si verificano errori durante l'interazione con un'API Web, possono essere gestiti dal codice dello sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="e16bc-157">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="e16bc-158">`GetFromJsonAsync` Si prevede ad esempio una risposta JSON dall'API del server con un `Content-Type` valore di `application/json`.</span><span class="sxs-lookup"><span data-stu-id="e16bc-158">For example, `GetFromJsonAsync` expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="e16bc-159">Se la risposta non è in formato JSON, la convalida del <xref:System.NotSupportedException>contenuto genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e16bc-159">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="e16bc-160">Nell'esempio seguente l'endpoint URI per la richiesta di dati meteorologici non è stato digitato correttamente.</span><span class="sxs-lookup"><span data-stu-id="e16bc-160">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="e16bc-161">L'URI deve essere, `WeatherForecast` ma viene visualizzato nella chiamata a `WeatherForcast` (mancante "e").</span><span class="sxs-lookup"><span data-stu-id="e16bc-161">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="e16bc-162">La `GetFromJsonAsync` chiamata prevede che venga restituito JSON, ma il server restituisce il codice HTML per un'eccezione non gestita nel server con un `Content-Type` valore di `text/html`.</span><span class="sxs-lookup"><span data-stu-id="e16bc-162">The `GetFromJsonAsync` call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="e16bc-163">L'eccezione non gestita si verifica nel server perché il percorso non è stato trovato e il middleware non può gestire una pagina o una vista per la richiesta.</span><span class="sxs-lookup"><span data-stu-id="e16bc-163">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="e16bc-164">In `OnInitializedAsync` sul client <xref:System.NotSupportedException> viene generata un'eccezione quando il contenuto della risposta viene convalidato come non JSON.</span><span class="sxs-lookup"><span data-stu-id="e16bc-164">In `OnInitializedAsync` on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="e16bc-165">L'eccezione viene rilevata `catch` nel blocco, in cui la logica personalizzata può registrare l'errore o presentare un messaggio di errore descrittivo all'utente:</span><span class="sxs-lookup"><span data-stu-id="e16bc-165">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="e16bc-166">L'esempio precedente è a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="e16bc-166">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="e16bc-167">Un'app Server API Web può essere configurata in modo da restituire JSON anche quando non esiste un endpoint o si verifica un excpetion non gestito nel server.</span><span class="sxs-lookup"><span data-stu-id="e16bc-167">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="e16bc-168">Per altre informazioni, vedere <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="e16bc-168">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="e16bc-169">Condivisione di risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="e16bc-169">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="e16bc-170">La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="e16bc-170">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="e16bc-171">Questa restrizione è detta *criterio della stessa origine*.</span><span class="sxs-lookup"><span data-stu-id="e16bc-171">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="e16bc-172">Il criterio della stessa origine impedisce a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="e16bc-172">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="e16bc-173">Per eseguire richieste dal browser a un endpoint con un'origine diversa, l' *endpoint* deve abilitare la [condivisione di risorse tra le origini (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="e16bc-173">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="e16bc-174">L' [ Blazor app di esempio webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) illustra l'uso di CORS nel componente API Web Call (*pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="e16bc-174">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="e16bc-175">Per consentire ad altri siti di effettuare richieste di condivisione di risorse tra le origini (CORS) per l' <xref:security/cors>app, vedere.</span><span class="sxs-lookup"><span data-stu-id="e16bc-175">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e16bc-176">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e16bc-176">Additional resources</span></span>

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="e16bc-177">Configurazione dell'endpoint HTTPS di gheppio</span><span class="sxs-lookup"><span data-stu-id="e16bc-177">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="e16bc-178">Condivisione di risorse tra le origini (CORS) in W3C</span><span class="sxs-lookup"><span data-stu-id="e16bc-178">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
