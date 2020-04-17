---
title: Chiamare un'API Web Blazor da ASP.NET Core WebAssemblyCall a web API from ASP.NET Core WebAssembly
author: guardrex
description: Informazioni su come chiamare un'API Web da un'app Blazor WebAssembly usando helper JSON, incluse le richieste di condivisione delle risorse tra origini (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 2f2d4150f4fa1e7f47310f2a88b816f445cd1d3a
ms.sourcegitcommit: 49c91ad4b69f4f8032394cbf2d5ae1b19a7f863b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81544856"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="2611e-103">Chiamare un'API Web da ASP.NET CoreCall a web API from ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="2611e-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="2611e-104">Di [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), e Juan De la [Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="2611e-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2611e-105">Le app WebAssembly chiamano le API `HttpClient` Web usando un servizio preconfigurato. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="2611e-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="2611e-106">Comporre le richieste, che possono includere le Blazor opzioni [dell'API JavaScript Fetch,](https://developer.mozilla.org/docs/Web/API/Fetch_API) usando helper JSON o con <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="2611e-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="2611e-107">Il `HttpClient` servizio Blazor nelle app WebAssembly è incentrato sull'esecuzione di richieste al server di origine.</span><span class="sxs-lookup"><span data-stu-id="2611e-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="2611e-108">Le indicazioni contenute in questo argomento Blazor riguardano solo le app WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="2611e-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="2611e-109">Le app server chiamano <xref:System.Net.Http.HttpClient> le API Web <xref:System.Net.Http.IHttpClientFactory>usando le istanze, in genere create utilizzando . [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="2611e-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="2611e-110">Le indicazioni contenute in questo argomento Blazor non riguardano le app server.</span><span class="sxs-lookup"><span data-stu-id="2611e-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="2611e-111">Quando Blazor si sviluppano app <xref:fundamentals/http-requests>server, seguire le indicazioni in .</span><span class="sxs-lookup"><span data-stu-id="2611e-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="2611e-112">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( come[scaricare](xref:index#how-to-download-a-sample)) &ndash; Selezionare l'app *BlazorWebAssemblySample.*</span><span class="sxs-lookup"><span data-stu-id="2611e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="2611e-113">Vedere i componenti seguenti nell'app di esempio *BlazorWebAssemblySample:See* the following components in the BlazorWebAssemblySample sample app:</span><span class="sxs-lookup"><span data-stu-id="2611e-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="2611e-114">Chiamare api Web (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="2611e-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="2611e-115">Tester richieste HTTP (*Componenti/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="2611e-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="2611e-116">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="2611e-116">Packages</span></span>

<span data-ttu-id="2611e-117">Fare riferimento al pacchetto [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="2611e-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="2611e-118">Aggiungere il servizio HttpClient</span><span class="sxs-lookup"><span data-stu-id="2611e-118">Add the HttpClient service</span></span>

<span data-ttu-id="2611e-119">In `Program.Main`, `HttpClient` aggiungere un servizio se non esiste già:</span><span class="sxs-lookup"><span data-stu-id="2611e-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="2611e-120">Helper HttpClient e JSON</span><span class="sxs-lookup"><span data-stu-id="2611e-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="2611e-121">In Blazor un'app WebAssembly [HttpClient](xref:fundamentals/http-requests) è disponibile come servizio preconfigurato per l'esecuzione di richieste al server di origine.</span><span class="sxs-lookup"><span data-stu-id="2611e-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="2611e-122">Un'app Blazor Server non `HttpClient` include un servizio per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="2611e-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="2611e-123">Fornire `HttpClient` un all'app utilizzando l'infrastruttura [di fabbrica HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="2611e-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="2611e-124">`HttpClient`e gli helper JSON vengono utilizzati anche per chiamare endpoint API Web di terze parti.</span><span class="sxs-lookup"><span data-stu-id="2611e-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="2611e-125">`HttpClient`viene implementato utilizzando [l'API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) del browser ed è soggetto alle sue limitazioni, inclusa l'applicazione degli stessi criteri di origine.</span><span class="sxs-lookup"><span data-stu-id="2611e-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="2611e-126">L'indirizzo di base del client è impostato sull'indirizzo del server di origine.</span><span class="sxs-lookup"><span data-stu-id="2611e-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="2611e-127">Inserire `HttpClient` un'istanza `@inject` usando la direttiva:Inject an instance using the directive:</span><span class="sxs-lookup"><span data-stu-id="2611e-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="2611e-128">Negli esempi seguenti, un'API Web Todo elabora le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD).</span><span class="sxs-lookup"><span data-stu-id="2611e-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="2611e-129">Gli esempi sono `TodoItem` basati su una classe che archivia:</span><span class="sxs-lookup"><span data-stu-id="2611e-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="2611e-130">ID`Id`( `long` &ndash; , ) ID univoco dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="2611e-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="2611e-131">Nome`Name`( `string` &ndash; , ) Nome dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="2611e-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="2611e-132">Stato`IsComplete`( `bool` &ndash; , ) Indicazione se l'elemento Todo è terminato.</span><span class="sxs-lookup"><span data-stu-id="2611e-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="2611e-133">I metodi helper JSON inviano richieste a un URI (un'API Web negli esempi seguenti) ed elaborano la risposta:</span><span class="sxs-lookup"><span data-stu-id="2611e-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="2611e-134">`GetFromJsonAsync`&ndash; Invia una richiesta HTTP GET e analizza il corpo della risposta JSON per creare un oggetto.</span><span class="sxs-lookup"><span data-stu-id="2611e-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="2611e-135">Nel codice seguente, `_todoItems` vengono visualizzati dal componente.</span><span class="sxs-lookup"><span data-stu-id="2611e-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="2611e-136">Il `GetTodoItems` metodo viene attivato al termine del rendering del componente ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="2611e-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="2611e-137">Vedere l'app di esempio per un esempio completo.</span><span class="sxs-lookup"><span data-stu-id="2611e-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="2611e-138">`PostAsJsonAsync`&ndash; Invia una richiesta HTTP POST, incluso il contenuto con codifica JSON, e analizza il corpo della risposta JSON per creare un oggetto.</span><span class="sxs-lookup"><span data-stu-id="2611e-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="2611e-139">Nel codice seguente, `_newItemName` viene fornito da un elemento associato del componente.</span><span class="sxs-lookup"><span data-stu-id="2611e-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="2611e-140">Il `AddItem` metodo viene attivato `<button>` selezionando un elemento.</span><span class="sxs-lookup"><span data-stu-id="2611e-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="2611e-141">Vedere l'app di esempio per un esempio completo.</span><span class="sxs-lookup"><span data-stu-id="2611e-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="2611e-142">Chiamate `PostAsJsonAsync` per <xref:System.Net.Http.HttpResponseMessage>restituire un file .</span><span class="sxs-lookup"><span data-stu-id="2611e-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span>

* <span data-ttu-id="2611e-143">`PutAsJsonAsync`&ndash; Invia una richiesta HTTP PUT, incluso il contenuto con codifica JSON.</span><span class="sxs-lookup"><span data-stu-id="2611e-143">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="2611e-144">Nel codice seguente, `_editItem` `Name` i `IsCompleted` valori per e vengono forniti dagli elementi associati del componente.</span><span class="sxs-lookup"><span data-stu-id="2611e-144">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="2611e-145">L'elemento `Id` viene impostato quando l'elemento viene selezionato `EditItem` in un'altra parte dell'interfaccia utente e viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="2611e-145">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="2611e-146">Il `SaveItem` metodo viene attivato selezionando `<button>` il Save elemento.</span><span class="sxs-lookup"><span data-stu-id="2611e-146">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="2611e-147">Vedere l'app di esempio per un esempio completo.</span><span class="sxs-lookup"><span data-stu-id="2611e-147">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="2611e-148">Chiamate `PutAsJsonAsync` per <xref:System.Net.Http.HttpResponseMessage>restituire un file .</span><span class="sxs-lookup"><span data-stu-id="2611e-148">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span>

<span data-ttu-id="2611e-149"><xref:System.Net.Http>include metodi di estensione aggiuntivi per l'invio di richieste HTTP e la ricezione di risposte HTTP.</span><span class="sxs-lookup"><span data-stu-id="2611e-149"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="2611e-150">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) viene utilizzato per inviare una richiesta HTTP DELETE a un'API Web.HttpClient.DeleteAsync is used to send an HTTP DELETE request to a web API.</span><span class="sxs-lookup"><span data-stu-id="2611e-150">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="2611e-151">Nel codice seguente, `<button>` il Delete `DeleteItem` elemento chiama il metodo .</span><span class="sxs-lookup"><span data-stu-id="2611e-151">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="2611e-152">L'elemento associato `<input>` `id` fornisce l'oggetto dell'elemento da eliminare.</span><span class="sxs-lookup"><span data-stu-id="2611e-152">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="2611e-153">Vedere l'app di esempio per un esempio completo.</span><span class="sxs-lookup"><span data-stu-id="2611e-153">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="2611e-154">Condivisione delle risorse tra origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="2611e-154">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="2611e-155">La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="2611e-155">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="2611e-156">Questa restrizione è *denominata criterio*della stessa origine .</span><span class="sxs-lookup"><span data-stu-id="2611e-156">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="2611e-157">I criteri della stessa origine impediscono a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="2611e-157">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="2611e-158">Per effettuare richieste dal browser a un endpoint con un'origine diversa, *l'endpoint* deve abilitare la [condivisione delle risorse tra origini (CORS, Cross-Origin Resource Sharing).](https://www.w3.org/TR/cors/)</span><span class="sxs-lookup"><span data-stu-id="2611e-158">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="2611e-159">[ Blazor Nell'app di esempio WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) viene illustrato l'utilizzo di CORS nel componente API Web chiamata (*Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="2611e-159">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="2611e-160">Per consentire ad altri siti di effettuare richieste di condivisione <xref:security/cors>delle risorse tra origini (CORS) all'app, vedere .</span><span class="sxs-lookup"><span data-stu-id="2611e-160">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="2611e-161">HttpClient e HttpRequestMessage con opzioni di richiesta dell'API Fetch</span><span class="sxs-lookup"><span data-stu-id="2611e-161">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="2611e-162">Quando si esegue su Blazor WebAssembly in un'app WebAssembly, utilizzare [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> personalizzare le richieste.</span><span class="sxs-lookup"><span data-stu-id="2611e-162">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="2611e-163">Ad esempio, è possibile specificare l'URI della richiesta, il metodo HTTP e le intestazioni di richiesta desiderate.</span><span class="sxs-lookup"><span data-stu-id="2611e-163">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

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

<span data-ttu-id="2611e-164">Per ulteriori informazioni sulle opzioni dell'API Fetch, vedere [Documenti Web MDN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="2611e-164">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="2611e-165">Quando si inviano credenziali (cookie di autorizzazione/intestazioni) nelle richieste CORS, l'intestazione `Authorization` deve essere consentita dai criteri CORS.</span><span class="sxs-lookup"><span data-stu-id="2611e-165">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="2611e-166">Il criterio seguente include la configurazione per:</span><span class="sxs-lookup"><span data-stu-id="2611e-166">The following policy includes configuration for:</span></span>

* <span data-ttu-id="2611e-167">Origini della`http://localhost:5000`richiesta `https://localhost:5001`( , ).</span><span class="sxs-lookup"><span data-stu-id="2611e-167">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="2611e-168">Qualsiasi metodo (verbo).</span><span class="sxs-lookup"><span data-stu-id="2611e-168">Any method (verb).</span></span>
* <span data-ttu-id="2611e-169">`Content-Type`e `Authorization` intestazioni.</span><span class="sxs-lookup"><span data-stu-id="2611e-169">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="2611e-170">Per consentire un'intestazione `x-custom-header`personalizzata, ad <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>esempio , elencare l'intestazione quando si chiama .</span><span class="sxs-lookup"><span data-stu-id="2611e-170">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="2611e-171">Credenziali impostate dal codice JavaScript`credentials` lato `include`client ( proprietà impostata su ).</span><span class="sxs-lookup"><span data-stu-id="2611e-171">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="2611e-172">Per ulteriori informazioni, vedere <xref:security/cors> e il componente HTTP Request Tester dell'app di esempio (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="2611e-172">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2611e-173">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2611e-173">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="2611e-174">Configurazione dell'endpoint HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="2611e-174">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="2611e-175">Cross Origin Resource Sharing (CORS) presso W3C</span><span class="sxs-lookup"><span data-stu-id="2611e-175">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
