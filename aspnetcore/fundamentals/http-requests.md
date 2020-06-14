---
title: Effettuare richieste HTTP usando IHttpClientFactory in ASP.NET Core
author: stevejgordon
description: Informazioni sull'uso dell'interfaccia IHttpClientFactory per gestire le istanze di HttpClient logiche in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: a54861945d97728336149d5ffb39952c3d61b7bd
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724263"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="e13d3-103">Effettuare richieste HTTP usando IHttpClientFactory in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e13d3-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e13d3-104">Di [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)e [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="e13d3-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="e13d3-105">È possibile registrare e usare un'interfaccia <xref:System.Net.Http.IHttpClientFactory> per configurare e creare istanze di <xref:System.Net.Http.HttpClient> in un'app.</span><span class="sxs-lookup"><span data-stu-id="e13d3-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="e13d3-106">`IHttpClientFactory`offre i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="e13d3-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="e13d3-107">Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche.</span><span class="sxs-lookup"><span data-stu-id="e13d3-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="e13d3-108">Ad esempio, un client denominato *GitHub* potrebbe essere registrato e configurato per accedere a [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="e13d3-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="e13d3-109">Un client predefinito può essere registrato per l'accesso generale.</span><span class="sxs-lookup"><span data-stu-id="e13d3-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="e13d3-110">Codifica il concetto di middleware in uscita tramite la delega dei gestori in `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="e13d3-111">Fornisce le estensioni per il middleware basato su Polly per sfruttare i vantaggi delegare i gestori in `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="e13d3-112">Gestisce il pool e la durata delle istanze sottostanti `HttpClientMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="e13d3-113">La gestione automatica evita i problemi comuni di DNS (Domain Name System) che si verificano quando si gestiscono manualmente le `HttpClient` durate.</span><span class="sxs-lookup"><span data-stu-id="e13d3-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="e13d3-114">Aggiunge un'esperienza di registrazione configurabile, tramite `ILogger`, per tutte le richieste inviate attraverso i client creati dalla factory.</span><span class="sxs-lookup"><span data-stu-id="e13d3-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="e13d3-115">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e13d3-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="e13d3-116">Il codice di esempio in questo argomento version USA <xref:System.Text.Json> per deserializzare il contenuto JSON restituito nelle risposte http.</span><span class="sxs-lookup"><span data-stu-id="e13d3-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="e13d3-117">Per esempi che usano `Json.NET` e `ReadAsAsync<T>` , usare il selettore di versione per selezionare una versione 2. x di questo argomento.</span><span class="sxs-lookup"><span data-stu-id="e13d3-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="e13d3-118">Modelli di consumo</span><span class="sxs-lookup"><span data-stu-id="e13d3-118">Consumption patterns</span></span>

<span data-ttu-id="e13d3-119">`IHttpClientFactory` può essere usato in un'app in diversi modi:</span><span class="sxs-lookup"><span data-stu-id="e13d3-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="e13d3-120">Uso di base</span><span class="sxs-lookup"><span data-stu-id="e13d3-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="e13d3-121">Client denominati</span><span class="sxs-lookup"><span data-stu-id="e13d3-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="e13d3-122">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="e13d3-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="e13d3-123">Client generati</span><span class="sxs-lookup"><span data-stu-id="e13d3-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="e13d3-124">L'approccio migliore dipende dai requisiti dell'app.</span><span class="sxs-lookup"><span data-stu-id="e13d3-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="e13d3-125">Uso di base</span><span class="sxs-lookup"><span data-stu-id="e13d3-125">Basic usage</span></span>

<span data-ttu-id="e13d3-126">`IHttpClientFactory`può essere registrato chiamando `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="e13d3-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="e13d3-127">`IHttpClientFactory`È possibile richiedere un oggetto usando l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e13d3-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e13d3-128">Il codice seguente usa `IHttpClientFactory` per creare un' `HttpClient` istanza:</span><span class="sxs-lookup"><span data-stu-id="e13d3-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="e13d3-129">L'uso di `IHttpClientFactory` like nell'esempio precedente è un modo efficace per effettuare il refactoring di un'app esistente.</span><span class="sxs-lookup"><span data-stu-id="e13d3-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="e13d3-130">Non ha alcun effetto sul modo in cui `HttpClient` viene usato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="e13d3-131">Nelle posizioni in cui `HttpClient` vengono create le istanze in un'app esistente, sostituire tali occorrenze con le chiamate a <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="e13d3-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="e13d3-132">Client denominati</span><span class="sxs-lookup"><span data-stu-id="e13d3-132">Named clients</span></span>

<span data-ttu-id="e13d3-133">I client denominati sono una scelta ottimale nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="e13d3-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="e13d3-134">L'app richiede molti usi distinti di `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="e13d3-135">Molti `HttpClient` hanno una configurazione diversa.</span><span class="sxs-lookup"><span data-stu-id="e13d3-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="e13d3-136">La configurazione di un oggetto denominato `HttpClient` può essere specificata durante la registrazione in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e13d3-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="e13d3-137">Nel codice precedente il client viene configurato con:</span><span class="sxs-lookup"><span data-stu-id="e13d3-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="e13d3-138">Indirizzo di base `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="e13d3-139">Due intestazioni necessarie per lavorare con l'API GitHub.</span><span class="sxs-lookup"><span data-stu-id="e13d3-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="e13d3-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="e13d3-140">CreateClient</span></span>

<span data-ttu-id="e13d3-141">Ogni volta che <xref:System.Net.Http.IHttpClientFactory.CreateClient*> viene chiamato:</span><span class="sxs-lookup"><span data-stu-id="e13d3-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="e13d3-142">Viene creata una nuova istanza di `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="e13d3-143">Viene chiamata l'azione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-143">The configuration action is called.</span></span>

<span data-ttu-id="e13d3-144">Per creare un client denominato, passare il nome in `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="e13d3-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="e13d3-145">Nel codice precedente non è necessario che la richiesta specifichi un nome host.</span><span class="sxs-lookup"><span data-stu-id="e13d3-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="e13d3-146">Il codice può passare solo il percorso, perché viene usato l'indirizzo di base configurato per il client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="e13d3-147">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="e13d3-147">Typed clients</span></span>

<span data-ttu-id="e13d3-148">Client tipizzati:</span><span class="sxs-lookup"><span data-stu-id="e13d3-148">Typed clients:</span></span>

* <span data-ttu-id="e13d3-149">Offrono le stesse funzionalità dei client denominati senza la necessità di usare le stringhe come chiavi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="e13d3-150">Offrono l'aiuto di IntelliSense e del compilatore quando si usano i client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="e13d3-151">La configurazione e l'interazione con un particolare `HttpClient` può avvenire in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="e13d3-152">Ad esempio, è possibile usare un singolo client tipizzato:</span><span class="sxs-lookup"><span data-stu-id="e13d3-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="e13d3-153">Per un singolo endpoint back-end.</span><span class="sxs-lookup"><span data-stu-id="e13d3-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="e13d3-154">Per incapsulare tutta la logica che riguarda l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="e13d3-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="e13d3-155">Usare DI e può essere inserito laddove necessario nell'app.</span><span class="sxs-lookup"><span data-stu-id="e13d3-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="e13d3-156">Un client tipizzato accetta un `HttpClient` parametro nel relativo costruttore:</span><span class="sxs-lookup"><span data-stu-id="e13d3-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e13d3-157">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="e13d3-157">In the preceding code:</span></span>

* <span data-ttu-id="e13d3-158">La configurazione viene spostata nel client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="e13d3-159">L'oggetto `HttpClient` viene esposto come una proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="e13d3-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="e13d3-160">È possibile creare metodi specifici dell'API che espongono `HttpClient` funzionalità.</span><span class="sxs-lookup"><span data-stu-id="e13d3-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="e13d3-161">Ad esempio, il `GetAspNetDocsIssues` metodo incapsula il codice per recuperare i problemi aperti.</span><span class="sxs-lookup"><span data-stu-id="e13d3-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="e13d3-162">Il codice seguente chiama <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` per registrare una classe client tipizzata:</span><span class="sxs-lookup"><span data-stu-id="e13d3-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="e13d3-163">Il client tipizzato viene registrato come temporaneo nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e13d3-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="e13d3-164">Nel codice precedente, viene `AddHttpClient` registrato `GitHubService` come servizio temporaneo.</span><span class="sxs-lookup"><span data-stu-id="e13d3-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="e13d3-165">Questa registrazione usa un metodo factory per:</span><span class="sxs-lookup"><span data-stu-id="e13d3-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="e13d3-166">Creare un'istanza di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="e13d3-167">Creare un'istanza di `GitHubService` passando l'istanza di `HttpClient` al relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="e13d3-168">Il client tipizzato può essere inserito e usato direttamente:</span><span class="sxs-lookup"><span data-stu-id="e13d3-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="e13d3-169">La configurazione di un client tipizzato può essere specificata durante la registrazione in `Startup.ConfigureServices` , anziché nel costruttore del client tipizzato:</span><span class="sxs-lookup"><span data-stu-id="e13d3-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="e13d3-170">L'oggetto `HttpClient` può essere incapsulato all'interno di un client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="e13d3-171">Anziché esporla come proprietà, definire un metodo che chiama l' `HttpClient` istanza internamente:</span><span class="sxs-lookup"><span data-stu-id="e13d3-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="e13d3-172">Nel codice precedente, l'oggetto `HttpClient` viene archiviato in un campo privato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="e13d3-173">L'accesso a `HttpClient` è tramite il `GetRepos` Metodo pubblico.</span><span class="sxs-lookup"><span data-stu-id="e13d3-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="e13d3-174">Client generati</span><span class="sxs-lookup"><span data-stu-id="e13d3-174">Generated clients</span></span>

<span data-ttu-id="e13d3-175">`IHttpClientFactory`può essere usato in combinazione con librerie di terze parti, ad esempio il [refitting](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="e13d3-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="e13d3-176">Refit è una libreria REST per .NET.</span><span class="sxs-lookup"><span data-stu-id="e13d3-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="e13d3-177">Converte le API REST in interfacce live.</span><span class="sxs-lookup"><span data-stu-id="e13d3-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="e13d3-178">Un'implementazione dell'interfaccia viene generata dinamicamente da `RestService`, usando `HttpClient` per effettuare le chiamate HTTP esterne.</span><span class="sxs-lookup"><span data-stu-id="e13d3-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="e13d3-179">Per rappresentare l'API esterna e la relativa risposta vengono definite un'interfaccia e una risposta:</span><span class="sxs-lookup"><span data-stu-id="e13d3-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="e13d3-180">È possibile aggiungere un client tipizzato usando Refit per generare l'implementazione:</span><span class="sxs-lookup"><span data-stu-id="e13d3-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="e13d3-181">L'interfaccia definita può essere usata dove necessario, con l'implementazione offerta dall'inserimento di dipendenze e da Refit:</span><span class="sxs-lookup"><span data-stu-id="e13d3-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="e13d3-182">Creare richieste POST, PUT e DELETE</span><span class="sxs-lookup"><span data-stu-id="e13d3-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="e13d3-183">Negli esempi precedenti, tutte le richieste HTTP usano il verbo GET HTTP.</span><span class="sxs-lookup"><span data-stu-id="e13d3-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="e13d3-184">`HttpClient`supporta anche altri verbi HTTP, tra cui:</span><span class="sxs-lookup"><span data-stu-id="e13d3-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="e13d3-185">POST</span><span class="sxs-lookup"><span data-stu-id="e13d3-185">POST</span></span>
* <span data-ttu-id="e13d3-186">PUT</span><span class="sxs-lookup"><span data-stu-id="e13d3-186">PUT</span></span>
* <span data-ttu-id="e13d3-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="e13d3-187">DELETE</span></span>
* <span data-ttu-id="e13d3-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="e13d3-188">PATCH</span></span>

<span data-ttu-id="e13d3-189">Per un elenco completo dei verbi HTTP supportati, vedere <xref:System.Net.Http.HttpMethod> .</span><span class="sxs-lookup"><span data-stu-id="e13d3-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="e13d3-190">Nell'esempio seguente viene illustrato come effettuare una richiesta HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="e13d3-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="e13d3-191">Nel codice precedente, il `CreateItemAsync` Metodo:</span><span class="sxs-lookup"><span data-stu-id="e13d3-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="e13d3-192">Serializza il `TodoItem` parametro in JSON usando `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="e13d3-193">Viene utilizzata un'istanza di <xref:System.Text.Json.JsonSerializerOptions> per configurare il processo di serializzazione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="e13d3-194">Crea un'istanza di <xref:System.Net.Http.StringContent> per comprimere il JSON serializzato per l'invio nel corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="e13d3-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="e13d3-195">Chiama <xref:System.Net.Http.HttpClient.PostAsync%2A> per inviare il contenuto JSON all'URL specificato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="e13d3-196">Si tratta di un URL relativo che viene aggiunto a [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span><span class="sxs-lookup"><span data-stu-id="e13d3-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="e13d3-197">Chiama <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> per generare un'eccezione se il codice di stato della risposta non indica esito positivo.</span><span class="sxs-lookup"><span data-stu-id="e13d3-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="e13d3-198">`HttpClient`supporta anche altri tipi di contenuto.</span><span class="sxs-lookup"><span data-stu-id="e13d3-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="e13d3-199">Ad esempio, <xref:System.Net.Http.MultipartContent> e <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="e13d3-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="e13d3-200">Per un elenco completo dei contenuti supportati, vedere <xref:System.Net.Http.HttpContent> .</span><span class="sxs-lookup"><span data-stu-id="e13d3-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="e13d3-201">Nell'esempio seguente viene illustrata una richiesta HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="e13d3-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="e13d3-202">Il codice precedente è molto simile all'esempio di POST.</span><span class="sxs-lookup"><span data-stu-id="e13d3-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="e13d3-203">Il `SaveItemAsync` metodo chiama <xref:System.Net.Http.HttpClient.PutAsync%2A> anziché `PostAsync` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="e13d3-204">Nell'esempio seguente viene illustrata una richiesta HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="e13d3-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="e13d3-205">Nel codice precedente, il `DeleteItemAsync` metodo chiama <xref:System.Net.Http.HttpClient.DeleteAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="e13d3-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="e13d3-206">Poiché le richieste DELETE HTTP non contengono in genere alcun corpo, il `DeleteAsync` metodo non fornisce un overload che accetta un'istanza di `HttpContent` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="e13d3-207">Per ulteriori informazioni sull'utilizzo di verbi HTTP diversi con `HttpClient` , vedere <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="e13d3-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="e13d3-208">Middleware per richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="e13d3-208">Outgoing request middleware</span></span>

<span data-ttu-id="e13d3-209">`HttpClient`ha il concetto di delegare i gestori che possono essere collegati insieme per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="e13d3-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="e13d3-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="e13d3-210">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="e13d3-211">Semplifica la definizione dei gestori da applicare per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-211">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="e13d3-212">Supporta la registrazione e il concatenamento di più gestori per compilare una pipeline middleware per le richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="e13d3-212">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="e13d3-213">Ognuno di questi gestori è in grado di eseguire operazioni prima e dopo la richiesta in uscita.</span><span class="sxs-lookup"><span data-stu-id="e13d3-213">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="e13d3-214">Questo modello:</span><span class="sxs-lookup"><span data-stu-id="e13d3-214">This pattern:</span></span>

  * <span data-ttu-id="e13d3-215">È simile alla pipeline del middleware in ingresso in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e13d3-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="e13d3-216">Fornisce un meccanismo per gestire le problematiche trasversali relative alle richieste HTTP, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e13d3-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="e13d3-217">memorizzazione nella cache</span><span class="sxs-lookup"><span data-stu-id="e13d3-217">caching</span></span>
    * <span data-ttu-id="e13d3-218">gestione degli errori</span><span class="sxs-lookup"><span data-stu-id="e13d3-218">error handling</span></span>
    * <span data-ttu-id="e13d3-219">serializzazione</span><span class="sxs-lookup"><span data-stu-id="e13d3-219">serialization</span></span>
    * <span data-ttu-id="e13d3-220">registrazione</span><span class="sxs-lookup"><span data-stu-id="e13d3-220">logging</span></span>

<span data-ttu-id="e13d3-221">Per creare un gestore di delega:</span><span class="sxs-lookup"><span data-stu-id="e13d3-221">To create a delegating handler:</span></span>

* <span data-ttu-id="e13d3-222">Derivare da <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="e13d3-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="e13d3-223">Eseguire l'override di <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e13d3-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="e13d3-224">Eseguire il codice prima di passare la richiesta al gestore successivo nella pipeline:</span><span class="sxs-lookup"><span data-stu-id="e13d3-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="e13d3-225">Il codice precedente controlla se l' `X-API-KEY` intestazione è presente nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="e13d3-226">Se `X-API-KEY` è mancante, <xref:System.Net.HttpStatusCode.BadRequest> viene restituito.</span><span class="sxs-lookup"><span data-stu-id="e13d3-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="e13d3-227">È possibile aggiungere più di un gestore alla configurazione per un oggetto `HttpClient` con <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="e13d3-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="e13d3-228">Nel codice precedente `ValidateHeaderHandler` viene registrato nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e13d3-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="e13d3-229">L'interfaccia `IHttpClientFactory` crea un ambito di inserimento delle dipendenze separato per ogni gestore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-229">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="e13d3-230">I gestori possono dipendere da servizi di qualsiasi ambito.</span><span class="sxs-lookup"><span data-stu-id="e13d3-230">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="e13d3-231">I servizi da cui dipendono i gestori vengono eliminati al momento dell'eliminazione del gestore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-231">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="e13d3-232">Dopo la registrazione è possibile chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, passando il tipo di gestore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-232">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="e13d3-233">È possibile registrare più gestori nell'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-233">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="e13d3-234">Ogni gestore esegue il wrapping del gestore successivo fino a quando l'elemento `HttpClientHandler` finale non esegue la richiesta:</span><span class="sxs-lookup"><span data-stu-id="e13d3-234">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="e13d3-235">Usare uno degli approcci seguenti per condividere lo stato in base alla richiesta con i gestori di messaggi:</span><span class="sxs-lookup"><span data-stu-id="e13d3-235">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="e13d3-236">Passare i dati nel gestore usando [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="e13d3-236">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="e13d3-237">Usare <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> per accedere alla richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="e13d3-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="e13d3-238">Creare un oggetto di archiviazione <xref:System.Threading.AsyncLocal`1> personalizzato per passare i dati.</span><span class="sxs-lookup"><span data-stu-id="e13d3-238">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="e13d3-239">Usare gestori basati su Polly</span><span class="sxs-lookup"><span data-stu-id="e13d3-239">Use Polly-based handlers</span></span>

<span data-ttu-id="e13d3-240">`IHttpClientFactory`si integra con la libreria di terze parti [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="e13d3-240">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="e13d3-241">Polly è una libreria di gestione degli errori temporanei e di resilienza completa per .NET.</span><span class="sxs-lookup"><span data-stu-id="e13d3-241">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="e13d3-242">Consente agli sviluppatori di esprimere criteri quali Retry, Circuit Breaker, Timeout, Bulkhead Isolation e Fallback in modo fluido e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e13d3-242">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="e13d3-243">Per consentire l'uso dei criteri Polly con le istanze configurate di `HttpClient` sono disponibili metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-243">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="e13d3-244">Le estensioni di Polly supportano l'aggiunta di gestori basati su Polly ai client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-244">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="e13d3-245">Polly richiede il pacchetto NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="e13d3-245">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="e13d3-246">Gestire gli errori temporanei</span><span class="sxs-lookup"><span data-stu-id="e13d3-246">Handle transient faults</span></span>

<span data-ttu-id="e13d3-247">Gli errori si verificano in genere quando le chiamate HTTP esterne sono temporanee.</span><span class="sxs-lookup"><span data-stu-id="e13d3-247">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="e13d3-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>consente di definire un criterio per gestire gli errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="e13d3-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="e13d3-249">I criteri configurati con `AddTransientHttpErrorPolicy` gestiscono le risposte seguenti:</span><span class="sxs-lookup"><span data-stu-id="e13d3-249">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="e13d3-250">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="e13d3-250">HTTP 5xx</span></span>
* <span data-ttu-id="e13d3-251">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="e13d3-251">HTTP 408</span></span>

<span data-ttu-id="e13d3-252">`AddTransientHttpErrorPolicy`consente di accedere a un `PolicyBuilder` oggetto configurato per gestire gli errori che rappresentano un possibile errore temporaneo:</span><span class="sxs-lookup"><span data-stu-id="e13d3-252">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="e13d3-253">Nel codice precedente viene definito un criterio `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-253">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="e13d3-254">Le richieste non riuscite vengono ritentate fino a tre volte con un ritardo di 600 millisecondi tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-254">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="e13d3-255">Selezionare i criteri in modo dinamico</span><span class="sxs-lookup"><span data-stu-id="e13d3-255">Dynamically select policies</span></span>

<span data-ttu-id="e13d3-256">I metodi di estensione vengono forniti per aggiungere gestori basati su Polly, ad esempio <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="e13d3-256">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="e13d3-257">L' `AddPolicyHandler` Overload seguente controlla la richiesta per decidere quali criteri applicare:</span><span class="sxs-lookup"><span data-stu-id="e13d3-257">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="e13d3-258">Nel codice precedente, se la richiesta in uscita è una richiesta HTTP GET viene applicato un timeout di 10 secondi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-258">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="e13d3-259">Per qualsiasi altro metodo HTTP viene usato un timeout di 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-259">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="e13d3-260">Aggiungere più gestori Polly</span><span class="sxs-lookup"><span data-stu-id="e13d3-260">Add multiple Polly handlers</span></span>

<span data-ttu-id="e13d3-261">È comune annidare i criteri Polly:</span><span class="sxs-lookup"><span data-stu-id="e13d3-261">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="e13d3-262">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="e13d3-262">In the preceding example:</span></span>

* <span data-ttu-id="e13d3-263">Vengono aggiunti due gestori.</span><span class="sxs-lookup"><span data-stu-id="e13d3-263">Two handlers are added.</span></span>
* <span data-ttu-id="e13d3-264">Il primo gestore USA <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> per aggiungere un criterio di ripetizione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-264">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="e13d3-265">Le richieste non riuscite vengono ritentate fino a tre volte.</span><span class="sxs-lookup"><span data-stu-id="e13d3-265">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="e13d3-266">La seconda `AddTransientHttpErrorPolicy` chiamata aggiunge un criterio di interruttore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-266">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="e13d3-267">Altre richieste esterne vengono bloccate per 30 secondi se 5 tentativi non riusciti si verificano in sequenza.</span><span class="sxs-lookup"><span data-stu-id="e13d3-267">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="e13d3-268">I criteri dell'interruttore di circuito sono con stato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-268">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="e13d3-269">Tutte le chiamate tramite questo client condividono lo stesso stato di circuito.</span><span class="sxs-lookup"><span data-stu-id="e13d3-269">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="e13d3-270">Aggiungere criteri dal registro Polly</span><span class="sxs-lookup"><span data-stu-id="e13d3-270">Add policies from the Polly registry</span></span>

<span data-ttu-id="e13d3-271">Un approccio alla gestione dei criteri usati di frequente consiste nel definirli una volta e registrarli in un elemento `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-271">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="e13d3-272">Nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e13d3-272">In the following code:</span></span>

* <span data-ttu-id="e13d3-273">Vengono aggiunti i criteri "regular" e "Long".</span><span class="sxs-lookup"><span data-stu-id="e13d3-273">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="e13d3-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>aggiunge i criteri "regular" e "Long" dal registro di sistema.</span><span class="sxs-lookup"><span data-stu-id="e13d3-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="e13d3-275">Per ulteriori informazioni su `IHttpClientFactory` e le integrazioni di Polly, vedere il [wiki di Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="e13d3-275">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="e13d3-276">Gestione di HttpClient e durata</span><span class="sxs-lookup"><span data-stu-id="e13d3-276">HttpClient and lifetime management</span></span>

<span data-ttu-id="e13d3-277">Viene restituita una nuova istanza di `HttpClient` per ogni chiamata di `CreateClient` per `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-277">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="e13d3-278"><xref:System.Net.Http.HttpMessageHandler>Viene creato un oggetto per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-278">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="e13d3-279">La factory gestisce le durate delle istanze di `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-279">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="e13d3-280">`IHttpClientFactory` esegue il pooling delle istanze di `HttpMessageHandler` create dalla factory per ridurre il consumo di risorse.</span><span class="sxs-lookup"><span data-stu-id="e13d3-280">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="e13d3-281">Un'istanza di `HttpMessageHandler` può essere riusata dal pool quando si crea una nuova istanza di `HttpClient` se la relativa durata non è scaduta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-281">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="e13d3-282">Il pooling dei gestori è consigliabile in quanto ogni gestore gestisce in genere le proprie connessioni HTTP sottostanti.</span><span class="sxs-lookup"><span data-stu-id="e13d3-282">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="e13d3-283">La creazione di più gestori del necessario può causare ritardi di connessione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-283">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="e13d3-284">Alcuni gestori mantengono inoltre le connessioni aperte a tempo indefinito, che possono impedire al gestore di reagire alle modifiche DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="e13d3-284">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="e13d3-285">La durata del gestore predefinito è di due minuti.</span><span class="sxs-lookup"><span data-stu-id="e13d3-285">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="e13d3-286">È possibile eseguire l'override del valore predefinito in base al client denominato:</span><span class="sxs-lookup"><span data-stu-id="e13d3-286">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="e13d3-287">`HttpClient`le istanze possono in genere essere considerate come oggetti .NET che **non** richiedono l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-287">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="e13d3-288">L'eliminazione annulla le richieste in uscita e garantisce che l'istanza di `HttpClient` specificata non possa essere usata dopo la chiamata a <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="e13d3-288">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="e13d3-289">`IHttpClientFactory` tiene traccia ed elimina le risorse usate dalle istanze di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-289">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="e13d3-290">Mantenere una sola istanza di `HttpClient` attiva per un lungo periodo di tempo è un modello comune usato prima dell'avvento di `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-290">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="e13d3-291">Questo modello non è più necessario dopo la migrazione a `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-291">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="e13d3-292">Alternative a IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="e13d3-292">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="e13d3-293">`IHttpClientFactory`L'uso di in un'app abilitata per la funzionalità consente di evitare:</span><span class="sxs-lookup"><span data-stu-id="e13d3-293">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="e13d3-294">Problemi di esaurimento delle risorse in base alle istanze di pool `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-294">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="e13d3-295">Problemi di DNS obsoleti tramite il ciclo di `HttpMessageHandler` istanze a intervalli regolari.</span><span class="sxs-lookup"><span data-stu-id="e13d3-295">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="e13d3-296">Esistono modi alternativi per risolvere i problemi precedenti utilizzando un'istanza di lunga durata <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="e13d3-296">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="e13d3-297">Creare un'istanza di all' `SocketsHttpHandler` avvio dell'app e usarla per il ciclo di vita dell'app.</span><span class="sxs-lookup"><span data-stu-id="e13d3-297">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="e13d3-298">Configurare su <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> un valore appropriato in base alle ore di aggiornamento DNS.</span><span class="sxs-lookup"><span data-stu-id="e13d3-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="e13d3-299">Creare `HttpClient` istanze `new HttpClient(handler, disposeHandler: false)` di usando in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="e13d3-299">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="e13d3-300">Gli approcci precedenti risolvono i problemi di gestione delle risorse che vengono `IHttpClientFactory` risolti in modo analogo.</span><span class="sxs-lookup"><span data-stu-id="e13d3-300">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="e13d3-301">Il `SocketsHttpHandler` condivide le connessioni tra le `HttpClient` istanze.</span><span class="sxs-lookup"><span data-stu-id="e13d3-301">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="e13d3-302">Questa condivisione impedisce l'esaurimento del socket.</span><span class="sxs-lookup"><span data-stu-id="e13d3-302">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="e13d3-303">Il `SocketsHttpHandler` ciclo di connessioni in base a `PooledConnectionLifetime` per evitare problemi DNS non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="e13d3-303">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="e13d3-304">Cookie</span><span class="sxs-lookup"><span data-stu-id="e13d3-304">Cookies</span></span>

<span data-ttu-id="e13d3-305">Le `HttpMessageHandler` istanze in pool generano `CookieContainer` oggetti condivisi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-305">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="e13d3-306">La `CookieContainer` condivisione di oggetti imprevista spesso genera codice errato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-306">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="e13d3-307">Per le app che richiedono cookie, prendere in considerazione una delle seguenti operazioni:</span><span class="sxs-lookup"><span data-stu-id="e13d3-307">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="e13d3-308">Disabilitazione della gestione automatica dei cookie</span><span class="sxs-lookup"><span data-stu-id="e13d3-308">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="e13d3-309">Evitando`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="e13d3-309">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="e13d3-310">Chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per disabilitare la gestione automatica dei cookie:</span><span class="sxs-lookup"><span data-stu-id="e13d3-310">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="e13d3-311">Registrazione</span><span class="sxs-lookup"><span data-stu-id="e13d3-311">Logging</span></span>

<span data-ttu-id="e13d3-312">I client creati tramite `IHttpClientFactory` registrano i messaggi di log per tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="e13d3-312">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="e13d3-313">Abilitare il livello di informazioni appropriato nella configurazione di registrazione per visualizzare i messaggi di log predefiniti.</span><span class="sxs-lookup"><span data-stu-id="e13d3-313">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="e13d3-314">La registrazione aggiuntiva, ad esempio quella delle intestazioni delle richieste, è inclusa solo a livello di traccia.</span><span class="sxs-lookup"><span data-stu-id="e13d3-314">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="e13d3-315">La categoria di log usata per ogni client include il nome del client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-315">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="e13d3-316">Un client denominato *MyNamedClient*, ad esempio, registra i messaggi con una categoria "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="e13d3-316">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="e13d3-317">I messaggi con suffisso *LogicalHandler* sono esterni alla pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="e13d3-317">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="e13d3-318">Nella richiesta i messaggi vengono registrati prima che qualsiasi altro gestore nella pipeline l'abbia elaborata.</span><span class="sxs-lookup"><span data-stu-id="e13d3-318">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="e13d3-319">Nella risposta i messaggi vengono registrati dopo che qualsiasi altro gestore nella pipeline ha ricevuto la risposta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-319">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="e13d3-320">La registrazione avviene anche all'interno della pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="e13d3-320">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="e13d3-321">Nell'esempio *MyNamedClient* i messaggi vengono registrati con la categoria di log "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="e13d3-321">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="e13d3-322">Per la richiesta, questo errore si verifica dopo l'esecuzione di tutti gli altri gestori e immediatamente prima dell'invio della richiesta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-322">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="e13d3-323">Nella risposta la registrazione include lo stato della risposta prima di restituirla attraverso la pipeline del gestore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-323">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="e13d3-324">L'abilitazione della registrazione all'esterno e all'interno della pipeline consente l'ispezione delle modifiche apportate da altri gestori nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="e13d3-324">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="e13d3-325">Questo può includere modifiche alle intestazioni delle richieste o al codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-325">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="e13d3-326">L'inclusione del nome del client nella categoria log consente il filtraggio dei log per specifici client denominati.</span><span class="sxs-lookup"><span data-stu-id="e13d3-326">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="e13d3-327">Configurare HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="e13d3-327">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="e13d3-328">Può essere necessario controllare la configurazione dell'elemento `HttpMessageHandler` interno usato da un client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-328">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="e13d3-329">Quando si aggiungono client denominati o tipizzati viene restituito un elemento `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-329">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="e13d3-330">È possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per definire un delegato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-330">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="e13d3-331">Il delegato viene usato per creare e configurare l'elemento `HttpMessageHandler` primario usato dal client:</span><span class="sxs-lookup"><span data-stu-id="e13d3-331">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="e13d3-332">Usare IHttpClientFactory in un'app console</span><span class="sxs-lookup"><span data-stu-id="e13d3-332">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="e13d3-333">In un'app console aggiungere al progetto i riferimenti ai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="e13d3-333">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="e13d3-334">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="e13d3-334">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="e13d3-335">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="e13d3-335">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="e13d3-336">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="e13d3-336">In the following example:</span></span>

* <span data-ttu-id="e13d3-337"><xref:System.Net.Http.IHttpClientFactory> è registrato nel contenitore di servizi dell'[host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e13d3-337"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="e13d3-338">`MyService` crea un'istanza della factory client dal servizio, che viene usata per creare una classe `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-338">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="e13d3-339">`HttpClient` viene usato per recuperare una pagina Web.</span><span class="sxs-lookup"><span data-stu-id="e13d3-339">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="e13d3-340">`Main` crea un ambito per eseguire il metodo `GetPage` del servizio e scrivere i primi 500 caratteri del contenuto della pagina Web nella console.</span><span class="sxs-lookup"><span data-stu-id="e13d3-340">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="e13d3-341">Middleware di propagazione delle intestazioni</span><span class="sxs-lookup"><span data-stu-id="e13d3-341">Header propagation middleware</span></span>

<span data-ttu-id="e13d3-342">La propagazione dell'intestazione è un middleware ASP.NET Core per propagare le intestazioni HTTP dalla richiesta in ingresso alle richieste del client HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="e13d3-342">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="e13d3-343">Per usare la propagazione delle intestazioni:</span><span class="sxs-lookup"><span data-stu-id="e13d3-343">To use header propagation:</span></span>

* <span data-ttu-id="e13d3-344">Fare riferimento al pacchetto [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="e13d3-344">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="e13d3-345">Configurare il middleware e `HttpClient` in `Startup` :</span><span class="sxs-lookup"><span data-stu-id="e13d3-345">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="e13d3-346">Il client include le intestazioni configurate nelle richieste in uscita:</span><span class="sxs-lookup"><span data-stu-id="e13d3-346">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="e13d3-347">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e13d3-347">Additional resources</span></span>

* [<span data-ttu-id="e13d3-348">Usare HttpClientFactory per l'implementazione di richieste HTTP resilienti</span><span class="sxs-lookup"><span data-stu-id="e13d3-348">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="e13d3-349">Implementazione dei tentativi di chiamate HTTP con backoff esponenziale con i criteri di Polly e HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="e13d3-349">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="e13d3-350">Implementazione dello schema Circuit Breaker</span><span class="sxs-lookup"><span data-stu-id="e13d3-350">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="e13d3-351">Come serializzare e deserializzare JSON in .NET</span><span class="sxs-lookup"><span data-stu-id="e13d3-351">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="e13d3-352">Di [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="e13d3-352">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="e13d3-353">È possibile registrare e usare un'interfaccia <xref:System.Net.Http.IHttpClientFactory> per configurare e creare istanze di <xref:System.Net.Http.HttpClient> in un'app.</span><span class="sxs-lookup"><span data-stu-id="e13d3-353">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="e13d3-354">I vantaggi offerti sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="e13d3-354">It offers the following benefits:</span></span>

* <span data-ttu-id="e13d3-355">Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche.</span><span class="sxs-lookup"><span data-stu-id="e13d3-355">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="e13d3-356">Ad esempio, è possibile registrare e configurare un client *github* per accedere a [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="e13d3-356">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="e13d3-357">È possibile registrare un client predefinito per altri scopi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-357">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="e13d3-358">Codifica il concetto di middleware in uscita tramite la delega di gestori in `HttpClient` e offre estensioni per il middleware basato su Polly per sfruttarne i vantaggi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-358">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="e13d3-359">Gestisce il pooling e la durata delle istanze di `HttpClientMessageHandler` sottostanti per evitare problemi DNS comuni che si verificano quando le durate di `HttpClient` vengono gestite manualmente.</span><span class="sxs-lookup"><span data-stu-id="e13d3-359">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="e13d3-360">Aggiunge un'esperienza di registrazione configurabile, tramite `ILogger`, per tutte le richieste inviate attraverso i client creati dalla factory.</span><span class="sxs-lookup"><span data-stu-id="e13d3-360">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="e13d3-361">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e13d3-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="e13d3-362">Modelli di consumo</span><span class="sxs-lookup"><span data-stu-id="e13d3-362">Consumption patterns</span></span>

<span data-ttu-id="e13d3-363">`IHttpClientFactory` può essere usato in un'app in diversi modi:</span><span class="sxs-lookup"><span data-stu-id="e13d3-363">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="e13d3-364">Uso di base</span><span class="sxs-lookup"><span data-stu-id="e13d3-364">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="e13d3-365">Client denominati</span><span class="sxs-lookup"><span data-stu-id="e13d3-365">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="e13d3-366">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="e13d3-366">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="e13d3-367">Client generati</span><span class="sxs-lookup"><span data-stu-id="e13d3-367">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="e13d3-368">Nessuno di questi modi può essere considerato superiore a un altro.</span><span class="sxs-lookup"><span data-stu-id="e13d3-368">None of them are strictly superior to another.</span></span> <span data-ttu-id="e13d3-369">L'approccio migliore dipende dai vincoli dell'app.</span><span class="sxs-lookup"><span data-stu-id="e13d3-369">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="e13d3-370">Uso di base</span><span class="sxs-lookup"><span data-stu-id="e13d3-370">Basic usage</span></span>

<span data-ttu-id="e13d3-371">È possibile registrare `IHttpClientFactory` chiamando il metodo di estensione `AddHttpClient` in `IServiceCollection`, all'interno del metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-371">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="e13d3-372">Dopo la registrazione, il codice può accettare un'interfaccia `IHttpClientFactory` ovunque sia possibile inserire servizi con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e13d3-372">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e13d3-373">`IHttpClientFactory`È possibile utilizzare per creare un' `HttpClient` istanza di:</span><span class="sxs-lookup"><span data-stu-id="e13d3-373">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="e13d3-374">Questo uso di `IHttpClientFactory` è un modo efficace per effettuare il refactoring di un'app esistente.</span><span class="sxs-lookup"><span data-stu-id="e13d3-374">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="e13d3-375">Non influisce in alcun modo sulle modalità d'uso di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-375">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="e13d3-376">Nelle posizioni in cui vengono attualmente create istanze di `HttpClient`, sostituire le occorrenze con una chiamata a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="e13d3-376">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="e13d3-377">Client denominati</span><span class="sxs-lookup"><span data-stu-id="e13d3-377">Named clients</span></span>

<span data-ttu-id="e13d3-378">Se un'app richiede molti usi distinti di `HttpClient`, ognuno con una configurazione diversa, un'opzione è l'uso di **client denominati**.</span><span class="sxs-lookup"><span data-stu-id="e13d3-378">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="e13d3-379">La configurazione di un `HttpClient` denominato può essere specificata durante la registrazione in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-379">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="e13d3-380">Nel codice precedente viene chiamato `AddHttpClient`, in cui viene specificato il nome *github*.</span><span class="sxs-lookup"><span data-stu-id="e13d3-380">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="e13d3-381">Al client viene applicata una configurazione predefinita, ovvero l'indirizzo di base e due intestazioni necessari per l'uso dell'API GitHub.</span><span class="sxs-lookup"><span data-stu-id="e13d3-381">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="e13d3-382">Ogni volta che `CreateClient` viene chiamato, verrà creata una nuova istanza di `HttpClient` e verrà chiamata l'azione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-382">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="e13d3-383">Per usare un client denominato, è possibile passare un parametro di stringa a `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-383">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="e13d3-384">Specificare il nome del client da creare:</span><span class="sxs-lookup"><span data-stu-id="e13d3-384">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="e13d3-385">Nel codice precedente non è necessario che la richiesta specifichi un nome host.</span><span class="sxs-lookup"><span data-stu-id="e13d3-385">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="e13d3-386">Poiché viene usato l'indirizzo di base configurato per il client, è possibile passare solo il percorso.</span><span class="sxs-lookup"><span data-stu-id="e13d3-386">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="e13d3-387">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="e13d3-387">Typed clients</span></span>

<span data-ttu-id="e13d3-388">Client tipizzati:</span><span class="sxs-lookup"><span data-stu-id="e13d3-388">Typed clients:</span></span>

* <span data-ttu-id="e13d3-389">Offrono le stesse funzionalità dei client denominati senza la necessità di usare le stringhe come chiavi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-389">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="e13d3-390">Offrono l'aiuto di IntelliSense e del compilatore quando si usano i client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-390">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="e13d3-391">La configurazione e l'interazione con un particolare `HttpClient` può avvenire in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-391">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="e13d3-392">Per un singolo endpoint back-end è ad esempio possibile usare un unico client tipizzato che incapsuli tutta la logica relativa all'endpoint.</span><span class="sxs-lookup"><span data-stu-id="e13d3-392">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="e13d3-393">Usano l'inserimento di dipendenze e possono essere inseriti dove necessario nell'app.</span><span class="sxs-lookup"><span data-stu-id="e13d3-393">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="e13d3-394">Un client tipizzato accetta un `HttpClient` parametro nel relativo costruttore:</span><span class="sxs-lookup"><span data-stu-id="e13d3-394">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="e13d3-395">Nel codice precedente la configurazione viene spostata nel client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-395">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="e13d3-396">L'oggetto `HttpClient` viene esposto come una proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="e13d3-396">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="e13d3-397">È possibile definire metodi di API specifiche che espongono la funzionalità `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-397">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="e13d3-398">Il metodo `GetAspNetDocsIssues` incapsula il codice necessario per eseguire una query e analizzare gli ultimi problemi aperti in un repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="e13d3-398">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="e13d3-399">Per registrare un client tipizzato è possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> generico all'interno di `Startup.ConfigureServices`, specificando la classe del client tipizzato:</span><span class="sxs-lookup"><span data-stu-id="e13d3-399">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="e13d3-400">Il client tipizzato viene registrato come temporaneo nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e13d3-400">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="e13d3-401">Il client tipizzato può essere inserito e usato direttamente:</span><span class="sxs-lookup"><span data-stu-id="e13d3-401">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="e13d3-402">Se si preferisce, è possibile specificare la configurazione di un client tipizzato durante la registrazione in `Startup.ConfigureServices`, anziché nel relativo costruttore:</span><span class="sxs-lookup"><span data-stu-id="e13d3-402">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="e13d3-403">È possibile incapsulare interamente `HttpClient` all'interno di un client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-403">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="e13d3-404">Anziché esporlo come una proprietà, è possibile specificare metodi pubblici che chiamano l'istanza di `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="e13d3-404">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="e13d3-405">Nel codice precedente `HttpClient` viene archiviato come un campo privato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-405">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="e13d3-406">L'accesso per effettuare chiamate esterne passa attraverso il metodo `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-406">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="e13d3-407">Client generati</span><span class="sxs-lookup"><span data-stu-id="e13d3-407">Generated clients</span></span>

<span data-ttu-id="e13d3-408">È possibile usare `IHttpClientFactory` in combinazione con altre librerie di terze parti, ad esempio [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="e13d3-408">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="e13d3-409">Refit è una libreria REST per .NET.</span><span class="sxs-lookup"><span data-stu-id="e13d3-409">Refit is a REST library for .NET.</span></span> <span data-ttu-id="e13d3-410">Converte le API REST in interfacce live.</span><span class="sxs-lookup"><span data-stu-id="e13d3-410">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="e13d3-411">Un'implementazione dell'interfaccia viene generata dinamicamente da `RestService`, usando `HttpClient` per effettuare le chiamate HTTP esterne.</span><span class="sxs-lookup"><span data-stu-id="e13d3-411">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="e13d3-412">Per rappresentare l'API esterna e la relativa risposta vengono definite un'interfaccia e una risposta:</span><span class="sxs-lookup"><span data-stu-id="e13d3-412">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="e13d3-413">È possibile aggiungere un client tipizzato usando Refit per generare l'implementazione:</span><span class="sxs-lookup"><span data-stu-id="e13d3-413">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="e13d3-414">L'interfaccia definita può essere usata dove necessario, con l'implementazione offerta dall'inserimento di dipendenze e da Refit:</span><span class="sxs-lookup"><span data-stu-id="e13d3-414">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="e13d3-415">Middleware per richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="e13d3-415">Outgoing request middleware</span></span>

<span data-ttu-id="e13d3-416">`HttpClient` include già il concetto di delega di gestori concatenati per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="e13d3-416">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="e13d3-417">`IHttpClientFactory` semplifica la definizione dei gestori da applicare per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-417">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="e13d3-418">Supporta la registrazione e il concatenamento di più gestori per creare una pipeline di middleware per le richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="e13d3-418">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="e13d3-419">Ognuno di questi gestori è in grado di eseguire operazioni prima e dopo la richiesta in uscita.</span><span class="sxs-lookup"><span data-stu-id="e13d3-419">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="e13d3-420">Questo modello è simile alla pipeline di middleware in ingresso in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e13d3-420">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="e13d3-421">Il modello offre un meccanismo per gestire le problematiche trasversali relative alle richieste HTTP, tra cui memorizzazione nella cache, gestione degli errori, serializzazione e registrazione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-421">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="e13d3-422">Per creare un gestore, definire una classe che deriva da <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="e13d3-422">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="e13d3-423">Eseguire l'override del metodo `SendAsync` per eseguire il codice prima di passare la richiesta al gestore successivo nella pipeline:</span><span class="sxs-lookup"><span data-stu-id="e13d3-423">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="e13d3-424">Il codice precedente definisce un gestore di base.</span><span class="sxs-lookup"><span data-stu-id="e13d3-424">The preceding code defines a basic handler.</span></span> <span data-ttu-id="e13d3-425">Verifica se un'intestazione `X-API-KEY` è stata inclusa nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-425">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="e13d3-426">Se l'intestazione non è presente, può evitare la chiamata HTTP e restituire una risposta appropriata.</span><span class="sxs-lookup"><span data-stu-id="e13d3-426">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="e13d3-427">Durante la registrazione, è possibile aggiungere uno o più gestori alla configurazione per un `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-427">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="e13d3-428">Questa attività viene eseguita tramite metodi di estensione in <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e13d3-428">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="e13d3-429">Nel codice precedente `ValidateHeaderHandler` viene registrato nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e13d3-429">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="e13d3-430">L'interfaccia `IHttpClientFactory` crea un ambito di inserimento delle dipendenze separato per ogni gestore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-430">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="e13d3-431">I gestori possono dipendere da servizi di qualsiasi ambito.</span><span class="sxs-lookup"><span data-stu-id="e13d3-431">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="e13d3-432">I servizi da cui dipendono i gestori vengono eliminati al momento dell'eliminazione del gestore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-432">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="e13d3-433">Dopo la registrazione è possibile chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, passando il tipo di gestore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-433">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="e13d3-434">È possibile registrare più gestori nell'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-434">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="e13d3-435">Ogni gestore esegue il wrapping del gestore successivo fino a quando l'elemento `HttpClientHandler` finale non esegue la richiesta:</span><span class="sxs-lookup"><span data-stu-id="e13d3-435">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="e13d3-436">Usare uno degli approcci seguenti per condividere lo stato in base alla richiesta con i gestori di messaggi:</span><span class="sxs-lookup"><span data-stu-id="e13d3-436">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="e13d3-437">Passare i dati al gestore usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-437">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="e13d3-438">Usare `IHttpContextAccessor` per accedere alla richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="e13d3-438">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="e13d3-439">Creare un oggetto di archiviazione `AsyncLocal` personalizzato per passare i dati.</span><span class="sxs-lookup"><span data-stu-id="e13d3-439">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="e13d3-440">Usare gestori basati su Polly</span><span class="sxs-lookup"><span data-stu-id="e13d3-440">Use Polly-based handlers</span></span>

<span data-ttu-id="e13d3-441">`IHttpClientFactory` si integra con una libreria di terze parti piuttosto diffusa denominata [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="e13d3-441">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="e13d3-442">Polly è una libreria di gestione degli errori temporanei e di resilienza completa per .NET.</span><span class="sxs-lookup"><span data-stu-id="e13d3-442">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="e13d3-443">Consente agli sviluppatori di esprimere criteri quali Retry, Circuit Breaker, Timeout, Bulkhead Isolation e Fallback in modo fluido e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e13d3-443">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="e13d3-444">Per consentire l'uso dei criteri Polly con le istanze configurate di `HttpClient` sono disponibili metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-444">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="e13d3-445">Le estensioni Polly:</span><span class="sxs-lookup"><span data-stu-id="e13d3-445">The Polly extensions:</span></span>

* <span data-ttu-id="e13d3-446">Supportano l'aggiunta di gestori basati su Polly ai client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-446">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="e13d3-447">Possono essere usate dopo l'installazione del pacchetto NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="e13d3-447">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="e13d3-448">Il pacchetto non è incluso nel framework condiviso ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e13d3-448">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="e13d3-449">Gestire gli errori temporanei</span><span class="sxs-lookup"><span data-stu-id="e13d3-449">Handle transient faults</span></span>

<span data-ttu-id="e13d3-450">La maggior parte degli errori comuni si verifica quando le chiamate HTTP esterne sono temporanee.</span><span class="sxs-lookup"><span data-stu-id="e13d3-450">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="e13d3-451">Per definire un criterio in grado di gestire gli errori temporanei è disponibile un pratico metodo di estensione denominato `AddTransientHttpErrorPolicy`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-451">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="e13d3-452">I criteri configurati con questo metodo di estensione gestiscono `HttpRequestException`, risposte HTTP 5xx e risposte HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="e13d3-452">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="e13d3-453">L'estensione `AddTransientHttpErrorPolicy` può essere usata all'interno di `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-453">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e13d3-454">L'estensione consente l'accesso a un oggetto `PolicyBuilder` configurato per gestire gli errori che rappresentano un possibile errore temporaneo:</span><span class="sxs-lookup"><span data-stu-id="e13d3-454">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="e13d3-455">Nel codice precedente viene definito un criterio `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-455">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="e13d3-456">Le richieste non riuscite vengono ritentate fino a tre volte con un ritardo di 600 millisecondi tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-456">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="e13d3-457">Selezionare i criteri in modo dinamico</span><span class="sxs-lookup"><span data-stu-id="e13d3-457">Dynamically select policies</span></span>

<span data-ttu-id="e13d3-458">Per aggiungere gestori basati su Polly è possibile usare altri metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-458">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="e13d3-459">Una di queste estensioni è `AddPolicyHandler`, che include più overload.</span><span class="sxs-lookup"><span data-stu-id="e13d3-459">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="e13d3-460">Un overload consente l'ispezione della richiesta al momento della definizione del criterio da applicare:</span><span class="sxs-lookup"><span data-stu-id="e13d3-460">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="e13d3-461">Nel codice precedente, se la richiesta in uscita è una richiesta HTTP GET viene applicato un timeout di 10 secondi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-461">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="e13d3-462">Per qualsiasi altro metodo HTTP viene usato un timeout di 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-462">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="e13d3-463">Aggiungere più gestori Polly</span><span class="sxs-lookup"><span data-stu-id="e13d3-463">Add multiple Polly handlers</span></span>

<span data-ttu-id="e13d3-464">In molti casi i criteri Polly vengono annidati per offrire funzionalità avanzate:</span><span class="sxs-lookup"><span data-stu-id="e13d3-464">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="e13d3-465">Nell'esempio precedente vengono aggiunti due gestori.</span><span class="sxs-lookup"><span data-stu-id="e13d3-465">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="e13d3-466">Il primo usa l'estensione `AddTransientHttpErrorPolicy` per aggiungere criteri di ripetizione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-466">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="e13d3-467">Le richieste non riuscite vengono ritentate fino a tre volte.</span><span class="sxs-lookup"><span data-stu-id="e13d3-467">Failed requests are retried up to three times.</span></span> <span data-ttu-id="e13d3-468">La seconda chiamata a `AddTransientHttpErrorPolicy` aggiunge criteri dell'interruttore di circuito.</span><span class="sxs-lookup"><span data-stu-id="e13d3-468">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="e13d3-469">Ulteriori richieste esterne vengono bloccate per 30 secondi nel caso si verifichino cinque tentativi non riusciti consecutivi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-469">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="e13d3-470">I criteri dell'interruttore di circuito sono con stato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-470">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="e13d3-471">Tutte le chiamate tramite questo client condividono lo stesso stato di circuito.</span><span class="sxs-lookup"><span data-stu-id="e13d3-471">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="e13d3-472">Aggiungere criteri dal registro Polly</span><span class="sxs-lookup"><span data-stu-id="e13d3-472">Add policies from the Polly registry</span></span>

<span data-ttu-id="e13d3-473">Un approccio alla gestione dei criteri usati di frequente consiste nel definirli una volta e registrarli in un elemento `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-473">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="e13d3-474">Per aggiungere un gestore usando un criterio del registro è disponibile un metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="e13d3-474">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="e13d3-475">Nel codice precedente vengono registrati due criteri quando si aggiunge `PolicyRegistry` a `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-475">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="e13d3-476">Per usare un criterio dal registro viene usato il metodo `AddPolicyHandlerFromRegistry` passando il nome del criterio da applicare.</span><span class="sxs-lookup"><span data-stu-id="e13d3-476">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="e13d3-477">Altre informazioni su `IHttpClientFactory` e le integrazioni con Polly sono disponibili nel [wiki di Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="e13d3-477">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="e13d3-478">Gestione di HttpClient e durata</span><span class="sxs-lookup"><span data-stu-id="e13d3-478">HttpClient and lifetime management</span></span>

<span data-ttu-id="e13d3-479">Viene restituita una nuova istanza di `HttpClient` per ogni chiamata di `CreateClient` per `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-479">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="e13d3-480">È presente un <xref:System.Net.Http.HttpMessageHandler> per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-480">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="e13d3-481">La factory gestisce le durate delle istanze di `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-481">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="e13d3-482">`IHttpClientFactory` esegue il pooling delle istanze di `HttpMessageHandler` create dalla factory per ridurre il consumo di risorse.</span><span class="sxs-lookup"><span data-stu-id="e13d3-482">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="e13d3-483">Un'istanza di `HttpMessageHandler` può essere riusata dal pool quando si crea una nuova istanza di `HttpClient` se la relativa durata non è scaduta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-483">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="e13d3-484">Il pooling dei gestori è consigliabile in quanto ogni gestore gestisce in genere le proprie connessioni HTTP sottostanti.</span><span class="sxs-lookup"><span data-stu-id="e13d3-484">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="e13d3-485">La creazione di più gestori del necessario può causare ritardi di connessione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-485">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="e13d3-486">Alcuni gestori mantengono inoltre le connessioni aperte a tempo indefinito. Ciò può impedire al gestore di reagire alle modifiche DNS.</span><span class="sxs-lookup"><span data-stu-id="e13d3-486">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="e13d3-487">La durata del gestore predefinito è di due minuti.</span><span class="sxs-lookup"><span data-stu-id="e13d3-487">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="e13d3-488">Il valore predefinito può essere sottoposto a override per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-488">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="e13d3-489">Per eseguire l'override, chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> nell'elemento `IHttpClientBuilder` restituito al momento della creazione del client:</span><span class="sxs-lookup"><span data-stu-id="e13d3-489">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="e13d3-490">L'eliminazione del client non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="e13d3-490">Disposal of the client isn't required.</span></span> <span data-ttu-id="e13d3-491">L'eliminazione annulla le richieste in uscita e garantisce che l'istanza di `HttpClient` specificata non possa essere usata dopo la chiamata a <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="e13d3-491">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="e13d3-492">`IHttpClientFactory` tiene traccia ed elimina le risorse usate dalle istanze di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-492">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="e13d3-493">Le istanze di `HttpClient` possono essere considerate a livello generale come oggetti .NET che non richiedono l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-493">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="e13d3-494">Mantenere una sola istanza di `HttpClient` attiva per un lungo periodo di tempo è un modello comune usato prima dell'avvento di `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-494">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="e13d3-495">Questo modello non è più necessario dopo la migrazione a `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-495">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="e13d3-496">Alternative a IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="e13d3-496">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="e13d3-497">`IHttpClientFactory`L'uso di in un'app abilitata per la funzionalità consente di evitare:</span><span class="sxs-lookup"><span data-stu-id="e13d3-497">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="e13d3-498">Problemi di esaurimento delle risorse in base alle istanze di pool `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-498">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="e13d3-499">Problemi di DNS obsoleti tramite il ciclo di `HttpMessageHandler` istanze a intervalli regolari.</span><span class="sxs-lookup"><span data-stu-id="e13d3-499">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="e13d3-500">Esistono modi alternativi per risolvere i problemi precedenti utilizzando un'istanza di lunga durata <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="e13d3-500">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="e13d3-501">Creare un'istanza di all' `SocketsHttpHandler` avvio dell'app e usarla per il ciclo di vita dell'app.</span><span class="sxs-lookup"><span data-stu-id="e13d3-501">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="e13d3-502">Configurare su <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> un valore appropriato in base alle ore di aggiornamento DNS.</span><span class="sxs-lookup"><span data-stu-id="e13d3-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="e13d3-503">Creare `HttpClient` istanze `new HttpClient(handler, disposeHandler: false)` di usando in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="e13d3-503">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="e13d3-504">Gli approcci precedenti risolvono i problemi di gestione delle risorse che vengono `IHttpClientFactory` risolti in modo analogo.</span><span class="sxs-lookup"><span data-stu-id="e13d3-504">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="e13d3-505">Il `SocketsHttpHandler` condivide le connessioni tra le `HttpClient` istanze.</span><span class="sxs-lookup"><span data-stu-id="e13d3-505">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="e13d3-506">Questa condivisione impedisce l'esaurimento del socket.</span><span class="sxs-lookup"><span data-stu-id="e13d3-506">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="e13d3-507">Il `SocketsHttpHandler` ciclo di connessioni in base a `PooledConnectionLifetime` per evitare problemi DNS non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="e13d3-507">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="e13d3-508">Cookie</span><span class="sxs-lookup"><span data-stu-id="e13d3-508">Cookies</span></span>

<span data-ttu-id="e13d3-509">Le `HttpMessageHandler` istanze in pool generano `CookieContainer` oggetti condivisi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-509">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="e13d3-510">La `CookieContainer` condivisione di oggetti imprevista spesso genera codice errato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-510">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="e13d3-511">Per le app che richiedono cookie, prendere in considerazione una delle seguenti operazioni:</span><span class="sxs-lookup"><span data-stu-id="e13d3-511">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="e13d3-512">Disabilitazione della gestione automatica dei cookie</span><span class="sxs-lookup"><span data-stu-id="e13d3-512">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="e13d3-513">Evitando`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="e13d3-513">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="e13d3-514">Chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per disabilitare la gestione automatica dei cookie:</span><span class="sxs-lookup"><span data-stu-id="e13d3-514">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="e13d3-515">Registrazione</span><span class="sxs-lookup"><span data-stu-id="e13d3-515">Logging</span></span>

<span data-ttu-id="e13d3-516">I client creati tramite `IHttpClientFactory` registrano i messaggi di log per tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="e13d3-516">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="e13d3-517">Per visualizzare i messaggi di log predefiniti, abilitare il livello di informazioni appropriato nella configurazione di registrazione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-517">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="e13d3-518">La registrazione aggiuntiva, ad esempio quella delle intestazioni delle richieste, è inclusa solo a livello di traccia.</span><span class="sxs-lookup"><span data-stu-id="e13d3-518">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="e13d3-519">La categoria di log usata per ogni client include il nome del client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-519">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="e13d3-520">Un client denominato *MyNamedClient*, ad esempio, registra i messaggi con una categoria `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-520">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="e13d3-521">I messaggi con suffisso *LogicalHandler* sono esterni alla pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="e13d3-521">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="e13d3-522">Nella richiesta i messaggi vengono registrati prima che qualsiasi altro gestore nella pipeline l'abbia elaborata.</span><span class="sxs-lookup"><span data-stu-id="e13d3-522">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="e13d3-523">Nella risposta i messaggi vengono registrati dopo che qualsiasi altro gestore nella pipeline ha ricevuto la risposta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-523">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="e13d3-524">La registrazione avviene anche all'interno della pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="e13d3-524">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="e13d3-525">Nell'esempio *MyNamedClient*, i messaggi vengono registrati nella categoria di log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-525">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="e13d3-526">Per la richiesta, ciò avviene dopo l'esecuzione di tutti gli altri gestori e immediatamente prima che la richiesta sia inviata in rete.</span><span class="sxs-lookup"><span data-stu-id="e13d3-526">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="e13d3-527">Nella risposta la registrazione include lo stato della risposta prima di restituirla attraverso la pipeline del gestore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-527">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="e13d3-528">L'abilitazione della registrazione all'esterno e all'interno della pipeline consente l'ispezione delle modifiche apportate da altri gestori nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="e13d3-528">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="e13d3-529">Le modifiche possono ad esempio riguardare le intestazioni delle richieste o il codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-529">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="e13d3-530">L'inclusione del nome del client nella categoria di log consente di filtrare i log in base a client denominati specifici, se necessario.</span><span class="sxs-lookup"><span data-stu-id="e13d3-530">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="e13d3-531">Configurare HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="e13d3-531">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="e13d3-532">Può essere necessario controllare la configurazione dell'elemento `HttpMessageHandler` interno usato da un client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-532">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="e13d3-533">Quando si aggiungono client denominati o tipizzati viene restituito un elemento `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-533">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="e13d3-534">È possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per definire un delegato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-534">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="e13d3-535">Il delegato viene usato per creare e configurare l'elemento `HttpMessageHandler` primario usato dal client:</span><span class="sxs-lookup"><span data-stu-id="e13d3-535">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="e13d3-536">Usare IHttpClientFactory in un'app console</span><span class="sxs-lookup"><span data-stu-id="e13d3-536">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="e13d3-537">In un'app console aggiungere al progetto i riferimenti ai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="e13d3-537">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="e13d3-538">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="e13d3-538">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="e13d3-539">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="e13d3-539">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="e13d3-540">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="e13d3-540">In the following example:</span></span>

* <span data-ttu-id="e13d3-541"><xref:System.Net.Http.IHttpClientFactory> è registrato nel contenitore di servizi dell'[host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e13d3-541"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="e13d3-542">`MyService` crea un'istanza della factory client dal servizio, che viene usata per creare una classe `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-542">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="e13d3-543">`HttpClient` viene usato per recuperare una pagina Web.</span><span class="sxs-lookup"><span data-stu-id="e13d3-543">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="e13d3-544">`Main` crea un ambito per eseguire il metodo `GetPage` del servizio e scrivere i primi 500 caratteri del contenuto della pagina Web nella console.</span><span class="sxs-lookup"><span data-stu-id="e13d3-544">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="e13d3-545">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e13d3-545">Additional resources</span></span>

* [<span data-ttu-id="e13d3-546">Usare HttpClientFactory per l'implementazione di richieste HTTP resilienti</span><span class="sxs-lookup"><span data-stu-id="e13d3-546">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="e13d3-547">Implementazione dei tentativi di chiamate HTTP con backoff esponenziale con i criteri di Polly e HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="e13d3-547">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="e13d3-548">Implementazione dello schema Circuit Breaker</span><span class="sxs-lookup"><span data-stu-id="e13d3-548">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="e13d3-549">Di [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="e13d3-549">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="e13d3-550">È possibile registrare e usare un'interfaccia <xref:System.Net.Http.IHttpClientFactory> per configurare e creare istanze di <xref:System.Net.Http.HttpClient> in un'app.</span><span class="sxs-lookup"><span data-stu-id="e13d3-550">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="e13d3-551">I vantaggi offerti sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="e13d3-551">It offers the following benefits:</span></span>

* <span data-ttu-id="e13d3-552">Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche.</span><span class="sxs-lookup"><span data-stu-id="e13d3-552">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="e13d3-553">Ad esempio, è possibile registrare e configurare un client *github* per accedere a [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="e13d3-553">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="e13d3-554">È possibile registrare un client predefinito per altri scopi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-554">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="e13d3-555">Codifica il concetto di middleware in uscita tramite la delega di gestori in `HttpClient` e offre estensioni per il middleware basato su Polly per sfruttarne i vantaggi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-555">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="e13d3-556">Gestisce il pooling e la durata delle istanze di `HttpClientMessageHandler` sottostanti per evitare problemi DNS comuni che si verificano quando le durate di `HttpClient` vengono gestite manualmente.</span><span class="sxs-lookup"><span data-stu-id="e13d3-556">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="e13d3-557">Aggiunge un'esperienza di registrazione configurabile, tramite `ILogger`, per tutte le richieste inviate attraverso i client creati dalla factory.</span><span class="sxs-lookup"><span data-stu-id="e13d3-557">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="e13d3-558">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e13d3-558">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e13d3-559">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="e13d3-559">Prerequisites</span></span>

<span data-ttu-id="e13d3-560">I progetti destinati a .NET Framework richiedono l'installazione del pacchetto NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="e13d3-560">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="e13d3-561">I progetti destinati a .NET Core che fanno riferimento al [metapacchetto Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app) sono già inclusi nel pacchetto `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-561">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="e13d3-562">Modelli di consumo</span><span class="sxs-lookup"><span data-stu-id="e13d3-562">Consumption patterns</span></span>

<span data-ttu-id="e13d3-563">`IHttpClientFactory` può essere usato in un'app in diversi modi:</span><span class="sxs-lookup"><span data-stu-id="e13d3-563">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="e13d3-564">Uso di base</span><span class="sxs-lookup"><span data-stu-id="e13d3-564">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="e13d3-565">Client denominati</span><span class="sxs-lookup"><span data-stu-id="e13d3-565">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="e13d3-566">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="e13d3-566">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="e13d3-567">Client generati</span><span class="sxs-lookup"><span data-stu-id="e13d3-567">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="e13d3-568">Nessuno di questi modi può essere considerato superiore a un altro.</span><span class="sxs-lookup"><span data-stu-id="e13d3-568">None of them are strictly superior to another.</span></span> <span data-ttu-id="e13d3-569">L'approccio migliore dipende dai vincoli dell'app.</span><span class="sxs-lookup"><span data-stu-id="e13d3-569">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="e13d3-570">Uso di base</span><span class="sxs-lookup"><span data-stu-id="e13d3-570">Basic usage</span></span>

<span data-ttu-id="e13d3-571">È possibile registrare `IHttpClientFactory` chiamando il metodo di estensione `AddHttpClient` in `IServiceCollection`, all'interno del metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-571">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="e13d3-572">Dopo la registrazione, il codice può accettare un'interfaccia `IHttpClientFactory` ovunque sia possibile inserire servizi con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e13d3-572">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e13d3-573">`IHttpClientFactory`È possibile utilizzare per creare un' `HttpClient` istanza di:</span><span class="sxs-lookup"><span data-stu-id="e13d3-573">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="e13d3-574">Questo uso di `IHttpClientFactory` è un modo efficace per effettuare il refactoring di un'app esistente.</span><span class="sxs-lookup"><span data-stu-id="e13d3-574">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="e13d3-575">Non influisce in alcun modo sulle modalità d'uso di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-575">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="e13d3-576">Nelle posizioni in cui vengono attualmente create istanze di `HttpClient`, sostituire le occorrenze con una chiamata a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="e13d3-576">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="e13d3-577">Client denominati</span><span class="sxs-lookup"><span data-stu-id="e13d3-577">Named clients</span></span>

<span data-ttu-id="e13d3-578">Se un'app richiede molti usi distinti di `HttpClient`, ognuno con una configurazione diversa, un'opzione è l'uso di **client denominati**.</span><span class="sxs-lookup"><span data-stu-id="e13d3-578">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="e13d3-579">La configurazione di un `HttpClient` denominato può essere specificata durante la registrazione in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-579">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="e13d3-580">Nel codice precedente viene chiamato `AddHttpClient`, in cui viene specificato il nome *github*.</span><span class="sxs-lookup"><span data-stu-id="e13d3-580">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="e13d3-581">Al client viene applicata una configurazione predefinita, ovvero l'indirizzo di base e due intestazioni necessari per l'uso dell'API GitHub.</span><span class="sxs-lookup"><span data-stu-id="e13d3-581">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="e13d3-582">Ogni volta che `CreateClient` viene chiamato, verrà creata una nuova istanza di `HttpClient` e verrà chiamata l'azione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-582">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="e13d3-583">Per usare un client denominato, è possibile passare un parametro di stringa a `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-583">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="e13d3-584">Specificare il nome del client da creare:</span><span class="sxs-lookup"><span data-stu-id="e13d3-584">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="e13d3-585">Nel codice precedente non è necessario che la richiesta specifichi un nome host.</span><span class="sxs-lookup"><span data-stu-id="e13d3-585">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="e13d3-586">Poiché viene usato l'indirizzo di base configurato per il client, è possibile passare solo il percorso.</span><span class="sxs-lookup"><span data-stu-id="e13d3-586">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="e13d3-587">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="e13d3-587">Typed clients</span></span>

<span data-ttu-id="e13d3-588">Client tipizzati:</span><span class="sxs-lookup"><span data-stu-id="e13d3-588">Typed clients:</span></span>

* <span data-ttu-id="e13d3-589">Offrono le stesse funzionalità dei client denominati senza la necessità di usare le stringhe come chiavi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-589">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="e13d3-590">Offrono l'aiuto di IntelliSense e del compilatore quando si usano i client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-590">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="e13d3-591">La configurazione e l'interazione con un particolare `HttpClient` può avvenire in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-591">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="e13d3-592">Per un singolo endpoint back-end è ad esempio possibile usare un unico client tipizzato che incapsuli tutta la logica relativa all'endpoint.</span><span class="sxs-lookup"><span data-stu-id="e13d3-592">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="e13d3-593">Usano l'inserimento di dipendenze e possono essere inseriti dove necessario nell'app.</span><span class="sxs-lookup"><span data-stu-id="e13d3-593">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="e13d3-594">Un client tipizzato accetta un `HttpClient` parametro nel relativo costruttore:</span><span class="sxs-lookup"><span data-stu-id="e13d3-594">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="e13d3-595">Nel codice precedente la configurazione viene spostata nel client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-595">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="e13d3-596">L'oggetto `HttpClient` viene esposto come una proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="e13d3-596">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="e13d3-597">È possibile definire metodi di API specifiche che espongono la funzionalità `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-597">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="e13d3-598">Il metodo `GetAspNetDocsIssues` incapsula il codice necessario per eseguire una query e analizzare gli ultimi problemi aperti in un repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="e13d3-598">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="e13d3-599">Per registrare un client tipizzato è possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> generico all'interno di `Startup.ConfigureServices`, specificando la classe del client tipizzato:</span><span class="sxs-lookup"><span data-stu-id="e13d3-599">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="e13d3-600">Il client tipizzato viene registrato come temporaneo nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e13d3-600">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="e13d3-601">Il client tipizzato può essere inserito e usato direttamente:</span><span class="sxs-lookup"><span data-stu-id="e13d3-601">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="e13d3-602">Se si preferisce, è possibile specificare la configurazione di un client tipizzato durante la registrazione in `Startup.ConfigureServices`, anziché nel relativo costruttore:</span><span class="sxs-lookup"><span data-stu-id="e13d3-602">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="e13d3-603">È possibile incapsulare interamente `HttpClient` all'interno di un client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-603">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="e13d3-604">Anziché esporlo come una proprietà, è possibile specificare metodi pubblici che chiamano l'istanza di `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="e13d3-604">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="e13d3-605">Nel codice precedente `HttpClient` viene archiviato come un campo privato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-605">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="e13d3-606">L'accesso per effettuare chiamate esterne passa attraverso il metodo `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-606">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="e13d3-607">Client generati</span><span class="sxs-lookup"><span data-stu-id="e13d3-607">Generated clients</span></span>

<span data-ttu-id="e13d3-608">È possibile usare `IHttpClientFactory` in combinazione con altre librerie di terze parti, ad esempio [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="e13d3-608">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="e13d3-609">Refit è una libreria REST per .NET.</span><span class="sxs-lookup"><span data-stu-id="e13d3-609">Refit is a REST library for .NET.</span></span> <span data-ttu-id="e13d3-610">Converte le API REST in interfacce live.</span><span class="sxs-lookup"><span data-stu-id="e13d3-610">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="e13d3-611">Un'implementazione dell'interfaccia viene generata dinamicamente da `RestService`, usando `HttpClient` per effettuare le chiamate HTTP esterne.</span><span class="sxs-lookup"><span data-stu-id="e13d3-611">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="e13d3-612">Per rappresentare l'API esterna e la relativa risposta vengono definite un'interfaccia e una risposta:</span><span class="sxs-lookup"><span data-stu-id="e13d3-612">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="e13d3-613">È possibile aggiungere un client tipizzato usando Refit per generare l'implementazione:</span><span class="sxs-lookup"><span data-stu-id="e13d3-613">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="e13d3-614">L'interfaccia definita può essere usata dove necessario, con l'implementazione offerta dall'inserimento di dipendenze e da Refit:</span><span class="sxs-lookup"><span data-stu-id="e13d3-614">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="e13d3-615">Middleware per richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="e13d3-615">Outgoing request middleware</span></span>

<span data-ttu-id="e13d3-616">`HttpClient` include già il concetto di delega di gestori concatenati per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="e13d3-616">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="e13d3-617">`IHttpClientFactory` semplifica la definizione dei gestori da applicare per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-617">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="e13d3-618">Supporta la registrazione e il concatenamento di più gestori per creare una pipeline di middleware per le richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="e13d3-618">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="e13d3-619">Ognuno di questi gestori è in grado di eseguire operazioni prima e dopo la richiesta in uscita.</span><span class="sxs-lookup"><span data-stu-id="e13d3-619">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="e13d3-620">Questo modello è simile alla pipeline di middleware in ingresso in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e13d3-620">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="e13d3-621">Il modello offre un meccanismo per gestire le problematiche trasversali relative alle richieste HTTP, tra cui memorizzazione nella cache, gestione degli errori, serializzazione e registrazione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-621">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="e13d3-622">Per creare un gestore, definire una classe che deriva da <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="e13d3-622">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="e13d3-623">Eseguire l'override del metodo `SendAsync` per eseguire il codice prima di passare la richiesta al gestore successivo nella pipeline:</span><span class="sxs-lookup"><span data-stu-id="e13d3-623">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="e13d3-624">Il codice precedente definisce un gestore di base.</span><span class="sxs-lookup"><span data-stu-id="e13d3-624">The preceding code defines a basic handler.</span></span> <span data-ttu-id="e13d3-625">Verifica se un'intestazione `X-API-KEY` è stata inclusa nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-625">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="e13d3-626">Se l'intestazione non è presente, può evitare la chiamata HTTP e restituire una risposta appropriata.</span><span class="sxs-lookup"><span data-stu-id="e13d3-626">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="e13d3-627">Durante la registrazione, è possibile aggiungere uno o più gestori alla configurazione per un `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-627">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="e13d3-628">Questa attività viene eseguita tramite metodi di estensione in <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e13d3-628">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="e13d3-629">Nel codice precedente `ValidateHeaderHandler` viene registrato nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e13d3-629">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="e13d3-630">Il gestore **deve** essere registrato nell'inserimento di dipendenze come servizio temporaneo, senza definizione di ambito.</span><span class="sxs-lookup"><span data-stu-id="e13d3-630">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="e13d3-631">Se il gestore viene registrato come un servizio con ambito e i servizi da cui dipende il gestore sono eliminabili:</span><span class="sxs-lookup"><span data-stu-id="e13d3-631">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="e13d3-632">I servizi del gestore potrebbero essere eliminati prima che il gestore esca dall'ambito.</span><span class="sxs-lookup"><span data-stu-id="e13d3-632">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="e13d3-633">I servizi del gestore eliminati causano un errore del gestore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-633">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="e13d3-634">Dopo la registrazione è possibile chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, passando il tipo di gestore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-634">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="e13d3-635">È possibile registrare più gestori nell'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-635">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="e13d3-636">Ogni gestore esegue il wrapping del gestore successivo fino a quando l'elemento `HttpClientHandler` finale non esegue la richiesta:</span><span class="sxs-lookup"><span data-stu-id="e13d3-636">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="e13d3-637">Usare uno degli approcci seguenti per condividere lo stato in base alla richiesta con i gestori di messaggi:</span><span class="sxs-lookup"><span data-stu-id="e13d3-637">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="e13d3-638">Passare i dati al gestore usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-638">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="e13d3-639">Usare `IHttpContextAccessor` per accedere alla richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="e13d3-639">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="e13d3-640">Creare un oggetto di archiviazione `AsyncLocal` personalizzato per passare i dati.</span><span class="sxs-lookup"><span data-stu-id="e13d3-640">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="e13d3-641">Usare gestori basati su Polly</span><span class="sxs-lookup"><span data-stu-id="e13d3-641">Use Polly-based handlers</span></span>

<span data-ttu-id="e13d3-642">`IHttpClientFactory` si integra con una libreria di terze parti piuttosto diffusa denominata [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="e13d3-642">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="e13d3-643">Polly è una libreria di gestione degli errori temporanei e di resilienza completa per .NET.</span><span class="sxs-lookup"><span data-stu-id="e13d3-643">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="e13d3-644">Consente agli sviluppatori di esprimere criteri quali Retry, Circuit Breaker, Timeout, Bulkhead Isolation e Fallback in modo fluido e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e13d3-644">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="e13d3-645">Per consentire l'uso dei criteri Polly con le istanze configurate di `HttpClient` sono disponibili metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-645">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="e13d3-646">Le estensioni Polly:</span><span class="sxs-lookup"><span data-stu-id="e13d3-646">The Polly extensions:</span></span>

* <span data-ttu-id="e13d3-647">Supportano l'aggiunta di gestori basati su Polly ai client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-647">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="e13d3-648">Possono essere usate dopo l'installazione del pacchetto NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="e13d3-648">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="e13d3-649">Il pacchetto non è incluso nel framework condiviso ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e13d3-649">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="e13d3-650">Gestire gli errori temporanei</span><span class="sxs-lookup"><span data-stu-id="e13d3-650">Handle transient faults</span></span>

<span data-ttu-id="e13d3-651">La maggior parte degli errori comuni si verifica quando le chiamate HTTP esterne sono temporanee.</span><span class="sxs-lookup"><span data-stu-id="e13d3-651">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="e13d3-652">Per definire un criterio in grado di gestire gli errori temporanei è disponibile un pratico metodo di estensione denominato `AddTransientHttpErrorPolicy`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-652">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="e13d3-653">I criteri configurati con questo metodo di estensione gestiscono `HttpRequestException`, risposte HTTP 5xx e risposte HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="e13d3-653">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="e13d3-654">L'estensione `AddTransientHttpErrorPolicy` può essere usata all'interno di `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-654">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e13d3-655">L'estensione consente l'accesso a un oggetto `PolicyBuilder` configurato per gestire gli errori che rappresentano un possibile errore temporaneo:</span><span class="sxs-lookup"><span data-stu-id="e13d3-655">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="e13d3-656">Nel codice precedente viene definito un criterio `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-656">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="e13d3-657">Le richieste non riuscite vengono ritentate fino a tre volte con un ritardo di 600 millisecondi tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-657">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="e13d3-658">Selezionare i criteri in modo dinamico</span><span class="sxs-lookup"><span data-stu-id="e13d3-658">Dynamically select policies</span></span>

<span data-ttu-id="e13d3-659">Per aggiungere gestori basati su Polly è possibile usare altri metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-659">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="e13d3-660">Una di queste estensioni è `AddPolicyHandler`, che include più overload.</span><span class="sxs-lookup"><span data-stu-id="e13d3-660">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="e13d3-661">Un overload consente l'ispezione della richiesta al momento della definizione del criterio da applicare:</span><span class="sxs-lookup"><span data-stu-id="e13d3-661">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="e13d3-662">Nel codice precedente, se la richiesta in uscita è una richiesta HTTP GET viene applicato un timeout di 10 secondi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-662">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="e13d3-663">Per qualsiasi altro metodo HTTP viene usato un timeout di 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-663">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="e13d3-664">Aggiungere più gestori Polly</span><span class="sxs-lookup"><span data-stu-id="e13d3-664">Add multiple Polly handlers</span></span>

<span data-ttu-id="e13d3-665">In molti casi i criteri Polly vengono annidati per offrire funzionalità avanzate:</span><span class="sxs-lookup"><span data-stu-id="e13d3-665">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="e13d3-666">Nell'esempio precedente vengono aggiunti due gestori.</span><span class="sxs-lookup"><span data-stu-id="e13d3-666">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="e13d3-667">Il primo usa l'estensione `AddTransientHttpErrorPolicy` per aggiungere criteri di ripetizione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-667">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="e13d3-668">Le richieste non riuscite vengono ritentate fino a tre volte.</span><span class="sxs-lookup"><span data-stu-id="e13d3-668">Failed requests are retried up to three times.</span></span> <span data-ttu-id="e13d3-669">La seconda chiamata a `AddTransientHttpErrorPolicy` aggiunge criteri dell'interruttore di circuito.</span><span class="sxs-lookup"><span data-stu-id="e13d3-669">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="e13d3-670">Ulteriori richieste esterne vengono bloccate per 30 secondi nel caso si verifichino cinque tentativi non riusciti consecutivi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-670">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="e13d3-671">I criteri dell'interruttore di circuito sono con stato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-671">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="e13d3-672">Tutte le chiamate tramite questo client condividono lo stesso stato di circuito.</span><span class="sxs-lookup"><span data-stu-id="e13d3-672">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="e13d3-673">Aggiungere criteri dal registro Polly</span><span class="sxs-lookup"><span data-stu-id="e13d3-673">Add policies from the Polly registry</span></span>

<span data-ttu-id="e13d3-674">Un approccio alla gestione dei criteri usati di frequente consiste nel definirli una volta e registrarli in un elemento `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-674">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="e13d3-675">Per aggiungere un gestore usando un criterio del registro è disponibile un metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="e13d3-675">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="e13d3-676">Nel codice precedente vengono registrati due criteri quando si aggiunge `PolicyRegistry` a `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-676">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="e13d3-677">Per usare un criterio dal registro viene usato il metodo `AddPolicyHandlerFromRegistry` passando il nome del criterio da applicare.</span><span class="sxs-lookup"><span data-stu-id="e13d3-677">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="e13d3-678">Altre informazioni su `IHttpClientFactory` e le integrazioni con Polly sono disponibili nel [wiki di Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="e13d3-678">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="e13d3-679">Gestione di HttpClient e durata</span><span class="sxs-lookup"><span data-stu-id="e13d3-679">HttpClient and lifetime management</span></span>

<span data-ttu-id="e13d3-680">Viene restituita una nuova istanza di `HttpClient` per ogni chiamata di `CreateClient` per `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-680">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="e13d3-681">È presente un <xref:System.Net.Http.HttpMessageHandler> per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-681">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="e13d3-682">La factory gestisce le durate delle istanze di `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-682">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="e13d3-683">`IHttpClientFactory` esegue il pooling delle istanze di `HttpMessageHandler` create dalla factory per ridurre il consumo di risorse.</span><span class="sxs-lookup"><span data-stu-id="e13d3-683">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="e13d3-684">Un'istanza di `HttpMessageHandler` può essere riusata dal pool quando si crea una nuova istanza di `HttpClient` se la relativa durata non è scaduta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-684">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="e13d3-685">Il pooling dei gestori è consigliabile in quanto ogni gestore gestisce in genere le proprie connessioni HTTP sottostanti.</span><span class="sxs-lookup"><span data-stu-id="e13d3-685">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="e13d3-686">La creazione di più gestori del necessario può causare ritardi di connessione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-686">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="e13d3-687">Alcuni gestori mantengono inoltre le connessioni aperte a tempo indefinito. Ciò può impedire al gestore di reagire alle modifiche DNS.</span><span class="sxs-lookup"><span data-stu-id="e13d3-687">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="e13d3-688">La durata del gestore predefinito è di due minuti.</span><span class="sxs-lookup"><span data-stu-id="e13d3-688">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="e13d3-689">Il valore predefinito può essere sottoposto a override per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-689">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="e13d3-690">Per eseguire l'override, chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> nell'elemento `IHttpClientBuilder` restituito al momento della creazione del client:</span><span class="sxs-lookup"><span data-stu-id="e13d3-690">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="e13d3-691">L'eliminazione del client non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="e13d3-691">Disposal of the client isn't required.</span></span> <span data-ttu-id="e13d3-692">L'eliminazione annulla le richieste in uscita e garantisce che l'istanza di `HttpClient` specificata non possa essere usata dopo la chiamata a <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="e13d3-692">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="e13d3-693">`IHttpClientFactory` tiene traccia ed elimina le risorse usate dalle istanze di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-693">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="e13d3-694">Le istanze di `HttpClient` possono essere considerate a livello generale come oggetti .NET che non richiedono l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-694">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="e13d3-695">Mantenere una sola istanza di `HttpClient` attiva per un lungo periodo di tempo è un modello comune usato prima dell'avvento di `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-695">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="e13d3-696">Questo modello non è più necessario dopo la migrazione a `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-696">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="e13d3-697">Alternative a IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="e13d3-697">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="e13d3-698">`IHttpClientFactory`L'uso di in un'app abilitata per la funzionalità consente di evitare:</span><span class="sxs-lookup"><span data-stu-id="e13d3-698">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="e13d3-699">Problemi di esaurimento delle risorse in base alle istanze di pool `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="e13d3-699">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="e13d3-700">Problemi di DNS obsoleti tramite il ciclo di `HttpMessageHandler` istanze a intervalli regolari.</span><span class="sxs-lookup"><span data-stu-id="e13d3-700">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="e13d3-701">Esistono modi alternativi per risolvere i problemi precedenti utilizzando un'istanza di lunga durata <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="e13d3-701">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="e13d3-702">Creare un'istanza di all' `SocketsHttpHandler` avvio dell'app e usarla per il ciclo di vita dell'app.</span><span class="sxs-lookup"><span data-stu-id="e13d3-702">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="e13d3-703">Configurare su <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> un valore appropriato in base alle ore di aggiornamento DNS.</span><span class="sxs-lookup"><span data-stu-id="e13d3-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="e13d3-704">Creare `HttpClient` istanze `new HttpClient(handler, disposeHandler: false)` di usando in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="e13d3-704">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="e13d3-705">Gli approcci precedenti risolvono i problemi di gestione delle risorse che vengono `IHttpClientFactory` risolti in modo analogo.</span><span class="sxs-lookup"><span data-stu-id="e13d3-705">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="e13d3-706">Il `SocketsHttpHandler` condivide le connessioni tra le `HttpClient` istanze.</span><span class="sxs-lookup"><span data-stu-id="e13d3-706">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="e13d3-707">Questa condivisione impedisce l'esaurimento del socket.</span><span class="sxs-lookup"><span data-stu-id="e13d3-707">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="e13d3-708">Il `SocketsHttpHandler` ciclo di connessioni in base a `PooledConnectionLifetime` per evitare problemi DNS non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="e13d3-708">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="e13d3-709">Cookie</span><span class="sxs-lookup"><span data-stu-id="e13d3-709">Cookies</span></span>

<span data-ttu-id="e13d3-710">Le `HttpMessageHandler` istanze in pool generano `CookieContainer` oggetti condivisi.</span><span class="sxs-lookup"><span data-stu-id="e13d3-710">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="e13d3-711">La `CookieContainer` condivisione di oggetti imprevista spesso genera codice errato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-711">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="e13d3-712">Per le app che richiedono cookie, prendere in considerazione una delle seguenti operazioni:</span><span class="sxs-lookup"><span data-stu-id="e13d3-712">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="e13d3-713">Disabilitazione della gestione automatica dei cookie</span><span class="sxs-lookup"><span data-stu-id="e13d3-713">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="e13d3-714">Evitando`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="e13d3-714">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="e13d3-715">Chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per disabilitare la gestione automatica dei cookie:</span><span class="sxs-lookup"><span data-stu-id="e13d3-715">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="e13d3-716">Registrazione</span><span class="sxs-lookup"><span data-stu-id="e13d3-716">Logging</span></span>

<span data-ttu-id="e13d3-717">I client creati tramite `IHttpClientFactory` registrano i messaggi di log per tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="e13d3-717">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="e13d3-718">Per visualizzare i messaggi di log predefiniti, abilitare il livello di informazioni appropriato nella configurazione di registrazione.</span><span class="sxs-lookup"><span data-stu-id="e13d3-718">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="e13d3-719">La registrazione aggiuntiva, ad esempio quella delle intestazioni delle richieste, è inclusa solo a livello di traccia.</span><span class="sxs-lookup"><span data-stu-id="e13d3-719">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="e13d3-720">La categoria di log usata per ogni client include il nome del client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-720">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="e13d3-721">Un client denominato *MyNamedClient*, ad esempio, registra i messaggi con una categoria `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-721">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="e13d3-722">I messaggi con suffisso *LogicalHandler* sono esterni alla pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="e13d3-722">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="e13d3-723">Nella richiesta i messaggi vengono registrati prima che qualsiasi altro gestore nella pipeline l'abbia elaborata.</span><span class="sxs-lookup"><span data-stu-id="e13d3-723">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="e13d3-724">Nella risposta i messaggi vengono registrati dopo che qualsiasi altro gestore nella pipeline ha ricevuto la risposta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-724">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="e13d3-725">La registrazione avviene anche all'interno della pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="e13d3-725">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="e13d3-726">Nell'esempio *MyNamedClient*, i messaggi vengono registrati nella categoria di log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-726">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="e13d3-727">Per la richiesta, ciò avviene dopo l'esecuzione di tutti gli altri gestori e immediatamente prima che la richiesta sia inviata in rete.</span><span class="sxs-lookup"><span data-stu-id="e13d3-727">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="e13d3-728">Nella risposta la registrazione include lo stato della risposta prima di restituirla attraverso la pipeline del gestore.</span><span class="sxs-lookup"><span data-stu-id="e13d3-728">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="e13d3-729">L'abilitazione della registrazione all'esterno e all'interno della pipeline consente l'ispezione delle modifiche apportate da altri gestori nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="e13d3-729">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="e13d3-730">Le modifiche possono ad esempio riguardare le intestazioni delle richieste o il codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="e13d3-730">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="e13d3-731">L'inclusione del nome del client nella categoria di log consente di filtrare i log in base a client denominati specifici, se necessario.</span><span class="sxs-lookup"><span data-stu-id="e13d3-731">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="e13d3-732">Configurare HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="e13d3-732">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="e13d3-733">Può essere necessario controllare la configurazione dell'elemento `HttpMessageHandler` interno usato da un client.</span><span class="sxs-lookup"><span data-stu-id="e13d3-733">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="e13d3-734">Quando si aggiungono client denominati o tipizzati viene restituito un elemento `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-734">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="e13d3-735">È possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per definire un delegato.</span><span class="sxs-lookup"><span data-stu-id="e13d3-735">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="e13d3-736">Il delegato viene usato per creare e configurare l'elemento `HttpMessageHandler` primario usato dal client:</span><span class="sxs-lookup"><span data-stu-id="e13d3-736">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="e13d3-737">Usare IHttpClientFactory in un'app console</span><span class="sxs-lookup"><span data-stu-id="e13d3-737">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="e13d3-738">In un'app console aggiungere al progetto i riferimenti ai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="e13d3-738">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="e13d3-739">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="e13d3-739">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="e13d3-740">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="e13d3-740">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="e13d3-741">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="e13d3-741">In the following example:</span></span>

* <span data-ttu-id="e13d3-742"><xref:System.Net.Http.IHttpClientFactory> è registrato nel contenitore di servizi dell'[host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e13d3-742"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="e13d3-743">`MyService` crea un'istanza della factory client dal servizio, che viene usata per creare una classe `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e13d3-743">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="e13d3-744">`HttpClient` viene usato per recuperare una pagina Web.</span><span class="sxs-lookup"><span data-stu-id="e13d3-744">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="e13d3-745">`Main` crea un ambito per eseguire il metodo `GetPage` del servizio e scrivere i primi 500 caratteri del contenuto della pagina Web nella console.</span><span class="sxs-lookup"><span data-stu-id="e13d3-745">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="e13d3-746">Middleware di propagazione delle intestazioni</span><span class="sxs-lookup"><span data-stu-id="e13d3-746">Header propagation middleware</span></span>

<span data-ttu-id="e13d3-747">La propagazione dell'intestazione è un middleware supportato dalla community per propagare le intestazioni HTTP dalla richiesta in ingresso alle richieste del client HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="e13d3-747">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="e13d3-748">Per usare la propagazione delle intestazioni:</span><span class="sxs-lookup"><span data-stu-id="e13d3-748">To use header propagation:</span></span>

* <span data-ttu-id="e13d3-749">Fare riferimento alla porta supportata dalla community del pacchetto [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="e13d3-749">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="e13d3-750">ASP.NET Core 3,1 e versioni successive supporta [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="e13d3-750">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="e13d3-751">Configurare il middleware e `HttpClient` in `Startup` :</span><span class="sxs-lookup"><span data-stu-id="e13d3-751">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="e13d3-752">Il client include le intestazioni configurate nelle richieste in uscita:</span><span class="sxs-lookup"><span data-stu-id="e13d3-752">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="e13d3-753">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e13d3-753">Additional resources</span></span>

* [<span data-ttu-id="e13d3-754">Usare HttpClientFactory per l'implementazione di richieste HTTP resilienti</span><span class="sxs-lookup"><span data-stu-id="e13d3-754">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="e13d3-755">Implementazione dei tentativi di chiamate HTTP con backoff esponenziale con i criteri di Polly e HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="e13d3-755">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="e13d3-756">Implementazione dello schema Circuit Breaker</span><span class="sxs-lookup"><span data-stu-id="e13d3-756">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
