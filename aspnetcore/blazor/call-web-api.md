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
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="17696-103">Chiamare un'API Web da ASP.NET CoreCall a web API from ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="17696-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="17696-104">Di [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), e Juan De la [Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="17696-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="17696-105">Le app WebAssembly chiamano le API `HttpClient` Web usando un servizio preconfigurato. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="17696-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="17696-106">Comporre le richieste, che possono includere le Blazor opzioni [dell'API JavaScript Fetch,](https://developer.mozilla.org/docs/Web/API/Fetch_API) usando helper JSON o con <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="17696-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="17696-107">Il `HttpClient` servizio Blazor nelle app WebAssembly è incentrato sull'esecuzione di richieste al server di origine.</span><span class="sxs-lookup"><span data-stu-id="17696-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="17696-108">Le indicazioni contenute in questo argomento Blazor riguardano solo le app WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="17696-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="17696-109">Le app server chiamano <xref:System.Net.Http.HttpClient> le API Web <xref:System.Net.Http.IHttpClientFactory>usando le istanze, in genere create utilizzando . [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="17696-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="17696-110">Le indicazioni contenute in questo argomento Blazor non riguardano le app server.</span><span class="sxs-lookup"><span data-stu-id="17696-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="17696-111">Quando Blazor si sviluppano app <xref:fundamentals/http-requests>server, seguire le indicazioni in .</span><span class="sxs-lookup"><span data-stu-id="17696-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="17696-112">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( come[scaricare](xref:index#how-to-download-a-sample)) &ndash; Selezionare l'app *BlazorWebAssemblySample.*</span><span class="sxs-lookup"><span data-stu-id="17696-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="17696-113">Vedere i componenti seguenti nell'app di esempio *BlazorWebAssemblySample:See* the following components in the BlazorWebAssemblySample sample app:</span><span class="sxs-lookup"><span data-stu-id="17696-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="17696-114">Chiamare api Web (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="17696-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="17696-115">Tester richieste HTTP (*Componenti/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="17696-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="17696-116">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="17696-116">Packages</span></span>

<span data-ttu-id="17696-117">Fare riferimento *all'oggetto sperimentale* [BlazorMicrosoft.AspNetCore. . Pacchetto HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="17696-117">Reference the *experimental* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet package in the project file.</span></span> <span data-ttu-id="17696-118">`Microsoft.AspNetCore.Blazor.HttpClient`è basato su `HttpClient` e [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).</span><span class="sxs-lookup"><span data-stu-id="17696-118">`Microsoft.AspNetCore.Blazor.HttpClient` is based on `HttpClient` and [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).</span></span>

<span data-ttu-id="17696-119">Per utilizzare un'API stabile, utilizzare il pacchetto [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) , che utilizza [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="17696-119">To use a stable API, use the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) package, which uses [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span> <span data-ttu-id="17696-120">L'uso dell'API stable in `Microsoft.AspNet.WebApi.Client` non fornisce gli helper JSON descritti `Microsoft.AspNetCore.Blazor.HttpClient` in questo argomento, che sono univoci per il pacchetto sperimentale.</span><span class="sxs-lookup"><span data-stu-id="17696-120">Using the stable API in `Microsoft.AspNet.WebApi.Client` doesn't provide the JSON helpers described in this topic, which are unique to the experimental `Microsoft.AspNetCore.Blazor.HttpClient` package.</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="17696-121">Helper HttpClient e JSON</span><span class="sxs-lookup"><span data-stu-id="17696-121">HttpClient and JSON helpers</span></span>

<span data-ttu-id="17696-122">In Blazor un'app WebAssembly [HttpClient](xref:fundamentals/http-requests) è disponibile come servizio preconfigurato per l'esecuzione di richieste al server di origine.</span><span class="sxs-lookup"><span data-stu-id="17696-122">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="17696-123">Un'app Blazor Server non `HttpClient` include un servizio per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="17696-123">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="17696-124">Fornire `HttpClient` un all'app utilizzando l'infrastruttura [di fabbrica HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="17696-124">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="17696-125">`HttpClient`e gli helper JSON vengono utilizzati anche per chiamare endpoint API Web di terze parti.</span><span class="sxs-lookup"><span data-stu-id="17696-125">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="17696-126">`HttpClient`viene implementato utilizzando [l'API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) del browser ed è soggetto alle sue limitazioni, inclusa l'applicazione degli stessi criteri di origine.</span><span class="sxs-lookup"><span data-stu-id="17696-126">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="17696-127">L'indirizzo di base del client è impostato sull'indirizzo del server di origine.</span><span class="sxs-lookup"><span data-stu-id="17696-127">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="17696-128">Inserire `HttpClient` un'istanza `@inject` usando la direttiva:Inject an instance using the directive:</span><span class="sxs-lookup"><span data-stu-id="17696-128">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="17696-129">Negli esempi seguenti, un'API Web Todo elabora le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD).</span><span class="sxs-lookup"><span data-stu-id="17696-129">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="17696-130">Gli esempi sono `TodoItem` basati su una classe che archivia:</span><span class="sxs-lookup"><span data-stu-id="17696-130">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="17696-131">ID`Id`( `long` &ndash; , ) ID univoco dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="17696-131">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="17696-132">Nome`Name`( `string` &ndash; , ) Nome dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="17696-132">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="17696-133">Stato`IsComplete`( `bool` &ndash; , ) Indicazione se l'elemento Todo è terminato.</span><span class="sxs-lookup"><span data-stu-id="17696-133">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="17696-134">I metodi helper JSON inviano richieste a un URI (un'API Web negli esempi seguenti) ed elaborano la risposta:</span><span class="sxs-lookup"><span data-stu-id="17696-134">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="17696-135">`GetJsonAsync`&ndash; Invia una richiesta HTTP GET e analizza il corpo della risposta JSON per creare un oggetto.</span><span class="sxs-lookup"><span data-stu-id="17696-135">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="17696-136">Nel codice seguente, `_todoItems` vengono visualizzati dal componente.</span><span class="sxs-lookup"><span data-stu-id="17696-136">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="17696-137">Il `GetTodoItems` metodo viene attivato al termine del rendering del componente ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="17696-137">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="17696-138">Vedere l'app di esempio per un esempio completo.</span><span class="sxs-lookup"><span data-stu-id="17696-138">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="17696-139">`PostJsonAsync`&ndash; Invia una richiesta HTTP POST, incluso il contenuto con codifica JSON, e analizza il corpo della risposta JSON per creare un oggetto.</span><span class="sxs-lookup"><span data-stu-id="17696-139">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="17696-140">Nel codice seguente, `_newItemName` viene fornito da un elemento associato del componente.</span><span class="sxs-lookup"><span data-stu-id="17696-140">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="17696-141">Il `AddItem` metodo viene attivato `<button>` selezionando un elemento.</span><span class="sxs-lookup"><span data-stu-id="17696-141">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="17696-142">Vedere l'app di esempio per un esempio completo.</span><span class="sxs-lookup"><span data-stu-id="17696-142">See the sample app for a complete example.</span></span>

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

* <span data-ttu-id="17696-143">`PutJsonAsync`&ndash; Invia una richiesta HTTP PUT, incluso il contenuto con codifica JSON.</span><span class="sxs-lookup"><span data-stu-id="17696-143">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="17696-144">Nel codice seguente, `_editItem` `Name` i `IsCompleted` valori per e vengono forniti dagli elementi associati del componente.</span><span class="sxs-lookup"><span data-stu-id="17696-144">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="17696-145">L'elemento `Id` viene impostato quando l'elemento viene selezionato `EditItem` in un'altra parte dell'interfaccia utente e viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="17696-145">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="17696-146">Il `SaveItem` metodo viene attivato selezionando `<button>` il Save elemento.</span><span class="sxs-lookup"><span data-stu-id="17696-146">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="17696-147">Vedere l'app di esempio per un esempio completo.</span><span class="sxs-lookup"><span data-stu-id="17696-147">See the sample app for a complete example.</span></span>

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

<span data-ttu-id="17696-148"><xref:System.Net.Http>include metodi di estensione aggiuntivi per l'invio di richieste HTTP e la ricezione di risposte HTTP.</span><span class="sxs-lookup"><span data-stu-id="17696-148"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="17696-149">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) viene utilizzato per inviare una richiesta HTTP DELETE a un'API Web.HttpClient.DeleteAsync is used to send an HTTP DELETE request to a web API.</span><span class="sxs-lookup"><span data-stu-id="17696-149">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="17696-150">Nel codice seguente, `<button>` il Delete `DeleteItem` elemento chiama il metodo .</span><span class="sxs-lookup"><span data-stu-id="17696-150">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="17696-151">L'elemento associato `<input>` `id` fornisce l'oggetto dell'elemento da eliminare.</span><span class="sxs-lookup"><span data-stu-id="17696-151">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="17696-152">Vedere l'app di esempio per un esempio completo.</span><span class="sxs-lookup"><span data-stu-id="17696-152">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="17696-153">Condivisione delle risorse tra origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="17696-153">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="17696-154">La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="17696-154">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="17696-155">Questa restrizione è *denominata criterio*della stessa origine .</span><span class="sxs-lookup"><span data-stu-id="17696-155">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="17696-156">I criteri della stessa origine impediscono a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="17696-156">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="17696-157">Per effettuare richieste dal browser a un endpoint con un'origine diversa, *l'endpoint* deve abilitare la [condivisione delle risorse tra origini (CORS, Cross-Origin Resource Sharing).](https://www.w3.org/TR/cors/)</span><span class="sxs-lookup"><span data-stu-id="17696-157">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="17696-158">[ Blazor Nell'app di esempio WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) viene illustrato l'utilizzo di CORS nel componente API Web chiamata (*Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="17696-158">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="17696-159">Per consentire ad altri siti di effettuare richieste di condivisione <xref:security/cors>delle risorse tra origini (CORS) all'app, vedere .</span><span class="sxs-lookup"><span data-stu-id="17696-159">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="17696-160">HttpClient e HttpRequestMessage con opzioni di richiesta dell'API Fetch</span><span class="sxs-lookup"><span data-stu-id="17696-160">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="17696-161">Quando si esegue su Blazor WebAssembly in un'app WebAssembly, utilizzare [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> personalizzare le richieste.</span><span class="sxs-lookup"><span data-stu-id="17696-161">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="17696-162">Ad esempio, è possibile specificare l'URI della richiesta, il metodo HTTP e le intestazioni di richiesta desiderate.</span><span class="sxs-lookup"><span data-stu-id="17696-162">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

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

<span data-ttu-id="17696-163">Per ulteriori informazioni sulle opzioni dell'API Fetch, vedere [Documenti Web MDN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="17696-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="17696-164">Quando si inviano credenziali (cookie di autorizzazione/intestazioni) nelle richieste CORS, l'intestazione `Authorization` deve essere consentita dai criteri CORS.</span><span class="sxs-lookup"><span data-stu-id="17696-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="17696-165">Il criterio seguente include la configurazione per:</span><span class="sxs-lookup"><span data-stu-id="17696-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="17696-166">Origini della`http://localhost:5000`richiesta `https://localhost:5001`( , ).</span><span class="sxs-lookup"><span data-stu-id="17696-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="17696-167">Qualsiasi metodo (verbo).</span><span class="sxs-lookup"><span data-stu-id="17696-167">Any method (verb).</span></span>
* <span data-ttu-id="17696-168">`Content-Type`e `Authorization` intestazioni.</span><span class="sxs-lookup"><span data-stu-id="17696-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="17696-169">Per consentire un'intestazione `x-custom-header`personalizzata, ad <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>esempio , elencare l'intestazione quando si chiama .</span><span class="sxs-lookup"><span data-stu-id="17696-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="17696-170">Credenziali impostate dal codice JavaScript`credentials` lato `include`client ( proprietà impostata su ).</span><span class="sxs-lookup"><span data-stu-id="17696-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="17696-171">Per ulteriori informazioni, vedere <xref:security/cors> e il componente HTTP Request Tester dell'app di esempio (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="17696-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17696-172">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="17696-172">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="17696-173">Configurazione dell'endpoint HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="17696-173">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="17696-174">Cross Origin Resource Sharing (CORS) presso W3C</span><span class="sxs-lookup"><span data-stu-id="17696-174">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
