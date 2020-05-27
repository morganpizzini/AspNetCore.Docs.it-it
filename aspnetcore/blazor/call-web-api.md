---
<span data-ttu-id="2cb7b-101">title: "chiamare un'API Web da ASP.NET Core Blazor webassembly" Author: Description: "informazioni su come chiamare un'API Web da un' Blazor app webassembly usando Helper JSON, inclusa la creazione di richieste di condivisione di risorse tra le origini (CORS)."</span><span class="sxs-lookup"><span data-stu-id="2cb7b-101">title: 'Call a web API from ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to call a web API from a Blazor WebAssembly app using JSON helpers, including making cross-origin resource sharing (CORS) requests.'</span></span>
<span data-ttu-id="2cb7b-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2cb7b-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cb7b-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cb7b-103">'Blazor'</span></span>
- <span data-ttu-id="2cb7b-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cb7b-104">'Identity'</span></span>
- <span data-ttu-id="2cb7b-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cb7b-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cb7b-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cb7b-106">'Razor'</span></span>
- <span data-ttu-id="2cb7b-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="2cb7b-107">'SignalR' uid:</span></span> 

---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="2cb7b-108">Chiamare un'API Web da ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="2cb7b-108">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="2cb7b-109">Di [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="2cb7b-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

<span data-ttu-id="2cb7b-110">Le app [ Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) chiamano API Web usando un servizio preconfigurato <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="2cb7b-110">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="2cb7b-111">Comporre richieste, che possono includere opzioni [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, usando Blazor Helper JSON o con <xref:System.Net.Http.HttpRequestMessage> .</span><span class="sxs-lookup"><span data-stu-id="2cb7b-111">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="2cb7b-112">Il <xref:System.Net.Http.HttpClient> servizio nelle Blazor app webassembly è incentrato sulla restituzione delle richieste al server di origine.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-112">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="2cb7b-113">Le indicazioni fornite in questo argomento riguardano solo le Blazor app webassembly.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-113">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="2cb7b-114">Le app [ Blazor Server](xref:blazor/hosting-models#blazor-server) chiamano API Web usando le <xref:System.Net.Http.HttpClient> istanze, in genere create usando <xref:System.Net.Http.IHttpClientFactory> .</span><span class="sxs-lookup"><span data-stu-id="2cb7b-114">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="2cb7b-115">Le indicazioni fornite in questo argomento non riguardano le Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-115">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="2cb7b-116">Quando si sviluppano Blazor app Server, seguire le istruzioni riportate in <xref:fundamentals/http-requests> .</span><span class="sxs-lookup"><span data-stu-id="2cb7b-116">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="2cb7b-117">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample)): selezionare l'app *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="2cb7b-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="2cb7b-118">Vedere i componenti seguenti nell'app di esempio *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="2cb7b-118">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="2cb7b-119">Chiama API Web (*pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="2cb7b-119">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="2cb7b-120">Tester richieste HTTP (*Components/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="2cb7b-120">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="2cb7b-121">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="2cb7b-121">Packages</span></span>

<span data-ttu-id="2cb7b-122">Fare riferimento al pacchetto NuGet [System .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-122">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="2cb7b-123">Aggiungere il servizio HttpClient</span><span class="sxs-lookup"><span data-stu-id="2cb7b-123">Add the HttpClient service</span></span>

<span data-ttu-id="2cb7b-124">In `Program.Main` aggiungere un <xref:System.Net.Http.HttpClient> servizio se non esiste già:</span><span class="sxs-lookup"><span data-stu-id="2cb7b-124">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="2cb7b-125">Helper HttpClient e JSON</span><span class="sxs-lookup"><span data-stu-id="2cb7b-125">HttpClient and JSON helpers</span></span>

<span data-ttu-id="2cb7b-126">In un' Blazor app webassembly, [HttpClient](xref:fundamentals/http-requests) è disponibile come servizio preconfigurato per eseguire richieste al server di origine.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-126">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="2cb7b-127">BlazorPer impostazione predefinita, un'app Server non include un <xref:System.Net.Http.HttpClient> servizio.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-127">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="2cb7b-128">Fornire un <xref:System.Net.Http.HttpClient> all'app usando l' [infrastruttura di HttpClient Factory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="2cb7b-128">Provide an <xref:System.Net.Http.HttpClient> to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="2cb7b-129"><xref:System.Net.Http.HttpClient>gli helper JSON e vengono usati anche per chiamare endpoint dell'API Web di terze parti.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-129"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="2cb7b-130"><xref:System.Net.Http.HttpClient>viene implementato usando l' [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) del browser ed è soggetto alle limitazioni, inclusa l'applicazione degli stessi criteri di origine.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-130"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="2cb7b-131">L'indirizzo di base del client viene impostato sull'indirizzo del server di origine.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-131">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="2cb7b-132">Inserire un' <xref:System.Net.Http.HttpClient> istanza usando la [`@inject`](xref:mvc/views/razor#inject) direttiva:</span><span class="sxs-lookup"><span data-stu-id="2cb7b-132">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="2cb7b-133">Negli esempi seguenti, un'API Web todo elabora le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD).</span><span class="sxs-lookup"><span data-stu-id="2cb7b-133">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="2cb7b-134">Gli esempi sono basati su una `TodoItem` classe che archivia:</span><span class="sxs-lookup"><span data-stu-id="2cb7b-134">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="2cb7b-135">ID ( `Id` , `long` ) &ndash; ID univoco dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-135">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="2cb7b-136">Nome ( `Name` , `string` ) &ndash; nome dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-136">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="2cb7b-137">Status ( `IsComplete` , `bool` ) &ndash; indica se l'elemento todo è terminato.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-137">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="2cb7b-138">I metodi helper JSON inviano richieste a un URI (un'API Web negli esempi seguenti) ed elaborano la risposta:</span><span class="sxs-lookup"><span data-stu-id="2cb7b-138">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="2cb7b-139"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>&ndash;Invia una richiesta HTTP GET e analizza il corpo della risposta JSON per creare un oggetto.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-139"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="2cb7b-140">Nel codice seguente, l'oggetto `todoItems` viene visualizzato dal componente.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-140">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="2cb7b-141">Il `GetTodoItems` metodo viene attivato al termine del rendering del componente ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="2cb7b-141">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="2cb7b-142">Per un esempio completo, vedere l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-142">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="2cb7b-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>&ndash;Invia una richiesta HTTP post, incluso il contenuto con codifica JSON, e analizza il corpo della risposta JSON per creare un oggetto.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="2cb7b-144">Nel codice seguente, `newItemName` viene fornito da un elemento associato del componente.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-144">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="2cb7b-145">Il `AddItem` metodo viene attivato selezionando un `<button>` elemento.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-145">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="2cb7b-146">Per un esempio completo, vedere l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-146">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="2cb7b-147">Chiama per <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> restituire un oggetto <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="2cb7b-147">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="2cb7b-148">Per deserializzare il contenuto JSON dal messaggio di risposta, usare il `ReadFromJsonAsync<T>` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="2cb7b-148">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="2cb7b-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>&ndash;Invia una richiesta HTTP PUT, incluso il contenuto con codifica JSON.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="2cb7b-150">Nel codice seguente, `editItem` i valori per `Name` e `IsCompleted` vengono forniti dagli elementi associati del componente.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-150">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="2cb7b-151">L'elemento `Id` viene impostato quando l'elemento viene selezionato in un'altra parte dell'interfaccia utente e `EditItem` viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-151">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="2cb7b-152">Il `SaveItem` metodo viene attivato selezionando l'elemento Save `<button>` .</span><span class="sxs-lookup"><span data-stu-id="2cb7b-152">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="2cb7b-153">Per un esempio completo, vedere l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-153">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="2cb7b-154">Chiama per <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> restituire un oggetto <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="2cb7b-154">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="2cb7b-155">Per deserializzare il contenuto JSON dal messaggio di risposta, usare il <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="2cb7b-155">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="2cb7b-156"><xref:System.Net.Http>include metodi di estensione aggiuntivi per l'invio di richieste HTTP e la ricezione di risposte HTTP.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-156"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="2cb7b-157"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType>viene usato per inviare una richiesta HTTP DELETE a un'API Web.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-157"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="2cb7b-158">Nel codice seguente, l'elemento Delete `<button>` chiama il `DeleteItem` metodo.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-158">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="2cb7b-159">L' `<input>` elemento associato fornisce l' `id` oggetto dell'elemento da eliminare.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-159">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="2cb7b-160">Per un esempio completo, vedere l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-160">See the sample app for a complete example.</span></span>

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="2cb7b-161">Denominato HttpClient con IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="2cb7b-161">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="2cb7b-162"><xref:System.Net.Http.IHttpClientFactory>i servizi e la configurazione di un oggetto denominato <xref:System.Net.Http.HttpClient> sono supportati.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-162"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="2cb7b-163">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="2cb7b-163">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="2cb7b-164">`FetchData`componente (*pages/fetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="2cb7b-164">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="2cb7b-165">HttpClient tipizzato</span><span class="sxs-lookup"><span data-stu-id="2cb7b-165">Typed HttpClient</span></span>

<span data-ttu-id="2cb7b-166">Tipizzato <xref:System.Net.Http.HttpClient> Usa una o più istanze dell'app <xref:System.Net.Http.HttpClient> , predefinite o denominate, per restituire i dati da uno o più endpoint dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-166">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="2cb7b-167">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cb7b-167">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="2cb7b-168">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="2cb7b-168">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="2cb7b-169">I componenti inseriscono il tipizzato <xref:System.Net.Http.HttpClient> per chiamare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-169">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="2cb7b-170">`FetchData`componente (*pages/fetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="2cb7b-170">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="handle-errors"></a><span data-ttu-id="2cb7b-171">Gestire gli errori</span><span class="sxs-lookup"><span data-stu-id="2cb7b-171">Handle errors</span></span>

<span data-ttu-id="2cb7b-172">Quando si verificano errori durante l'interazione con un'API Web, possono essere gestiti dal codice dello sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-172">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="2cb7b-173"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>Si prevede ad esempio una risposta JSON dall'API del server con un valore `Content-Type` di `application/json` .</span><span class="sxs-lookup"><span data-stu-id="2cb7b-173">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="2cb7b-174">Se la risposta non è in formato JSON, la convalida del contenuto genera un'eccezione <xref:System.NotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="2cb7b-174">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="2cb7b-175">Nell'esempio seguente l'endpoint URI per la richiesta di dati meteorologici non è stato digitato correttamente.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-175">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="2cb7b-176">L'URI deve essere, `WeatherForecast` ma viene visualizzato nella chiamata a `WeatherForcast` (mancante "e").</span><span class="sxs-lookup"><span data-stu-id="2cb7b-176">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="2cb7b-177">La <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> chiamata prevede che venga restituito JSON, ma il server restituisce il codice HTML per un'eccezione non gestita nel server con un valore `Content-Type` di `text/html` .</span><span class="sxs-lookup"><span data-stu-id="2cb7b-177">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="2cb7b-178">L'eccezione non gestita si verifica nel server perché il percorso non è stato trovato e il middleware non può gestire una pagina o una vista per la richiesta.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-178">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="2cb7b-179">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> sul client <xref:System.NotSupportedException> viene generata un'eccezione quando il contenuto della risposta viene convalidato come non JSON.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-179">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="2cb7b-180">L'eccezione viene rilevata nel `catch` blocco, in cui la logica personalizzata può registrare l'errore o presentare un messaggio di errore descrittivo all'utente:</span><span class="sxs-lookup"><span data-stu-id="2cb7b-180">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="2cb7b-181">L'esempio precedente è a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-181">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="2cb7b-182">Un'app Server API Web può essere configurata in modo da restituire JSON anche quando non esiste un endpoint o si verifica un excpetion non gestito nel server.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-182">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="2cb7b-183">Per altre informazioni, vedere <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-183">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="2cb7b-184">Condivisione di risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="2cb7b-184">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="2cb7b-185">La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-185">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="2cb7b-186">Questa restrizione è detta *criterio della stessa origine*.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-186">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="2cb7b-187">Il criterio della stessa origine impedisce a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-187">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="2cb7b-188">Per eseguire richieste dal browser a un endpoint con un'origine diversa, l' *endpoint* deve abilitare la [condivisione di risorse tra le origini (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="2cb7b-188">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="2cb7b-189">L' [ Blazor app di esempio webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) illustra l'uso di CORS nel componente API Web Call (*pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="2cb7b-189">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="2cb7b-190">Per consentire ad altri siti di effettuare richieste di condivisione di risorse tra le origini (CORS) per l'app, vedere <xref:security/cors> .</span><span class="sxs-lookup"><span data-stu-id="2cb7b-190">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2cb7b-191">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2cb7b-191">Additional resources</span></span>

* <span data-ttu-id="2cb7b-192"><xref:security/blazor/webassembly/additional-scenarios>&ndash;Include la copertura sull'uso <xref:System.Net.Http.HttpClient> di per creare richieste API Web sicure.</span><span class="sxs-lookup"><span data-stu-id="2cb7b-192"><xref:security/blazor/webassembly/additional-scenarios> &ndash; Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="2cb7b-193">Configurazione dell'endpoint HTTPS di gheppio</span><span class="sxs-lookup"><span data-stu-id="2cb7b-193">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="2cb7b-194">Condivisione di risorse tra le origini (CORS) in W3C</span><span class="sxs-lookup"><span data-stu-id="2cb7b-194">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
