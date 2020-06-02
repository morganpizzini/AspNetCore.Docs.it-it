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
ms.openlocfilehash: ae33218d6944c62a08e677592ac0c66f9026b15f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766550"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="863fe-103">Effettuare richieste HTTP usando IHttpClientFactory in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="863fe-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="863fe-104">Di [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)e [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="863fe-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="863fe-105">È possibile registrare e usare un'interfaccia <xref:System.Net.Http.IHttpClientFactory> per configurare e creare istanze di <xref:System.Net.Http.HttpClient> in un'app.</span><span class="sxs-lookup"><span data-stu-id="863fe-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="863fe-106">`IHttpClientFactory`offre i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="863fe-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="863fe-107">Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche.</span><span class="sxs-lookup"><span data-stu-id="863fe-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="863fe-108">Ad esempio, un client denominato *GitHub* potrebbe essere registrato e configurato per accedere a [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="863fe-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="863fe-109">Un client predefinito può essere registrato per l'accesso generale.</span><span class="sxs-lookup"><span data-stu-id="863fe-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="863fe-110">Codifica il concetto di middleware in uscita tramite la delega dei gestori in `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="863fe-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="863fe-111">Fornisce le estensioni per il middleware basato su Polly per sfruttare i vantaggi delegare i gestori in `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="863fe-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="863fe-112">Gestisce il pool e la durata delle istanze sottostanti `HttpClientMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="863fe-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="863fe-113">La gestione automatica evita i problemi comuni di DNS (Domain Name System) che si verificano quando si gestiscono manualmente le `HttpClient` durate.</span><span class="sxs-lookup"><span data-stu-id="863fe-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="863fe-114">Aggiunge un'esperienza di registrazione configurabile, tramite `ILogger`, per tutte le richieste inviate attraverso i client creati dalla factory.</span><span class="sxs-lookup"><span data-stu-id="863fe-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="863fe-115">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="863fe-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="863fe-116">Il codice di esempio in questo argomento version USA <xref:System.Text.Json> per deserializzare il contenuto JSON restituito nelle risposte http.</span><span class="sxs-lookup"><span data-stu-id="863fe-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="863fe-117">Per esempi che usano `Json.NET` e `ReadAsAsync<T>` , usare il selettore di versione per selezionare una versione 2. x di questo argomento.</span><span class="sxs-lookup"><span data-stu-id="863fe-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="863fe-118">Modelli di consumo</span><span class="sxs-lookup"><span data-stu-id="863fe-118">Consumption patterns</span></span>

<span data-ttu-id="863fe-119">`IHttpClientFactory` può essere usato in un'app in diversi modi:</span><span class="sxs-lookup"><span data-stu-id="863fe-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="863fe-120">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="863fe-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="863fe-121">Client denominati</span><span class="sxs-lookup"><span data-stu-id="863fe-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="863fe-122">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="863fe-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="863fe-123">Client generati</span><span class="sxs-lookup"><span data-stu-id="863fe-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="863fe-124">L'approccio migliore dipende dai requisiti dell'app.</span><span class="sxs-lookup"><span data-stu-id="863fe-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="863fe-125">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="863fe-125">Basic usage</span></span>

<span data-ttu-id="863fe-126">`IHttpClientFactory`può essere registrato chiamando `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="863fe-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="863fe-127">`IHttpClientFactory`È possibile richiedere un oggetto usando l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="863fe-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="863fe-128">Il codice seguente usa `IHttpClientFactory` per creare un' `HttpClient` istanza:</span><span class="sxs-lookup"><span data-stu-id="863fe-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="863fe-129">L'uso di `IHttpClientFactory` like nell'esempio precedente è un modo efficace per effettuare il refactoring di un'app esistente.</span><span class="sxs-lookup"><span data-stu-id="863fe-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="863fe-130">Non ha alcun effetto sul modo in cui `HttpClient` viene usato.</span><span class="sxs-lookup"><span data-stu-id="863fe-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="863fe-131">Nelle posizioni in cui `HttpClient` vengono create le istanze in un'app esistente, sostituire tali occorrenze con le chiamate a <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="863fe-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="863fe-132">Client denominati</span><span class="sxs-lookup"><span data-stu-id="863fe-132">Named clients</span></span>

<span data-ttu-id="863fe-133">I client denominati sono una scelta ottimale nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="863fe-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="863fe-134">L'app richiede molti usi distinti di `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="863fe-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="863fe-135">Molti `HttpClient` hanno una configurazione diversa.</span><span class="sxs-lookup"><span data-stu-id="863fe-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="863fe-136">La configurazione di un oggetto denominato `HttpClient` può essere specificata durante la registrazione in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="863fe-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="863fe-137">Nel codice precedente il client viene configurato con:</span><span class="sxs-lookup"><span data-stu-id="863fe-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="863fe-138">Indirizzo di base `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="863fe-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="863fe-139">Due intestazioni necessarie per lavorare con l'API GitHub.</span><span class="sxs-lookup"><span data-stu-id="863fe-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="863fe-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="863fe-140">CreateClient</span></span>

<span data-ttu-id="863fe-141">Ogni volta che <xref:System.Net.Http.IHttpClientFactory.CreateClient*> viene chiamato:</span><span class="sxs-lookup"><span data-stu-id="863fe-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="863fe-142">Viene creata una nuova istanza di `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="863fe-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="863fe-143">Viene chiamata l'azione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="863fe-143">The configuration action is called.</span></span>

<span data-ttu-id="863fe-144">Per creare un client denominato, passare il nome in `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="863fe-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="863fe-145">Nel codice precedente non è necessario che la richiesta specifichi un nome host.</span><span class="sxs-lookup"><span data-stu-id="863fe-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="863fe-146">Il codice può passare solo il percorso, perché viene usato l'indirizzo di base configurato per il client.</span><span class="sxs-lookup"><span data-stu-id="863fe-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="863fe-147">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="863fe-147">Typed clients</span></span>

<span data-ttu-id="863fe-148">Client tipizzati:</span><span class="sxs-lookup"><span data-stu-id="863fe-148">Typed clients:</span></span>

* <span data-ttu-id="863fe-149">Offrono le stesse funzionalità dei client denominati senza la necessità di usare le stringhe come chiavi.</span><span class="sxs-lookup"><span data-stu-id="863fe-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="863fe-150">Offrono l'aiuto di IntelliSense e del compilatore quando si usano i client.</span><span class="sxs-lookup"><span data-stu-id="863fe-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="863fe-151">La configurazione e l'interazione con un particolare `HttpClient` può avvenire in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="863fe-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="863fe-152">Ad esempio, è possibile usare un singolo client tipizzato:</span><span class="sxs-lookup"><span data-stu-id="863fe-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="863fe-153">Per un singolo endpoint back-end.</span><span class="sxs-lookup"><span data-stu-id="863fe-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="863fe-154">Per incapsulare tutta la logica che riguarda l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="863fe-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="863fe-155">Usare DI e può essere inserito laddove necessario nell'app.</span><span class="sxs-lookup"><span data-stu-id="863fe-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="863fe-156">Un client tipizzato accetta un `HttpClient` parametro nel relativo costruttore:</span><span class="sxs-lookup"><span data-stu-id="863fe-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="863fe-157">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="863fe-157">In the preceding code:</span></span>

* <span data-ttu-id="863fe-158">La configurazione viene spostata nel client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="863fe-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="863fe-159">L'oggetto `HttpClient` viene esposto come una proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="863fe-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="863fe-160">È possibile creare metodi specifici dell'API che espongono `HttpClient` funzionalità.</span><span class="sxs-lookup"><span data-stu-id="863fe-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="863fe-161">Ad esempio, il `GetAspNetDocsIssues` metodo incapsula il codice per recuperare i problemi aperti.</span><span class="sxs-lookup"><span data-stu-id="863fe-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="863fe-162">Il codice seguente chiama <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` per registrare una classe client tipizzata:</span><span class="sxs-lookup"><span data-stu-id="863fe-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="863fe-163">Il client tipizzato viene registrato come temporaneo nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="863fe-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="863fe-164">Nel codice precedente, viene `AddHttpClient` registrato `GitHubService` come servizio temporaneo.</span><span class="sxs-lookup"><span data-stu-id="863fe-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="863fe-165">Questa registrazione usa un metodo factory per:</span><span class="sxs-lookup"><span data-stu-id="863fe-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="863fe-166">Creare un'istanza di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="863fe-167">Creare un'istanza di `GitHubService` passando l'istanza di `HttpClient` al relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="863fe-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="863fe-168">Il client tipizzato può essere inserito e usato direttamente:</span><span class="sxs-lookup"><span data-stu-id="863fe-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="863fe-169">La configurazione di un client tipizzato può essere specificata durante la registrazione in `Startup.ConfigureServices` , anziché nel costruttore del client tipizzato:</span><span class="sxs-lookup"><span data-stu-id="863fe-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="863fe-170">L'oggetto `HttpClient` può essere incapsulato all'interno di un client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="863fe-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="863fe-171">Anziché esporla come proprietà, definire un metodo che chiama l' `HttpClient` istanza internamente:</span><span class="sxs-lookup"><span data-stu-id="863fe-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="863fe-172">Nel codice precedente, l'oggetto `HttpClient` viene archiviato in un campo privato.</span><span class="sxs-lookup"><span data-stu-id="863fe-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="863fe-173">L'accesso a `HttpClient` è tramite il `GetRepos` Metodo pubblico.</span><span class="sxs-lookup"><span data-stu-id="863fe-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="863fe-174">Client generati</span><span class="sxs-lookup"><span data-stu-id="863fe-174">Generated clients</span></span>

<span data-ttu-id="863fe-175">`IHttpClientFactory`può essere usato in combinazione con librerie di terze parti, ad esempio il [refitting](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="863fe-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="863fe-176">Refit è una libreria REST per .NET.</span><span class="sxs-lookup"><span data-stu-id="863fe-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="863fe-177">Converte le API REST in interfacce live.</span><span class="sxs-lookup"><span data-stu-id="863fe-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="863fe-178">Un'implementazione dell'interfaccia viene generata dinamicamente da `RestService`, usando `HttpClient` per effettuare le chiamate HTTP esterne.</span><span class="sxs-lookup"><span data-stu-id="863fe-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="863fe-179">Per rappresentare l'API esterna e la relativa risposta vengono definite un'interfaccia e una risposta:</span><span class="sxs-lookup"><span data-stu-id="863fe-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="863fe-180">È possibile aggiungere un client tipizzato usando Refit per generare l'implementazione:</span><span class="sxs-lookup"><span data-stu-id="863fe-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="863fe-181">L'interfaccia definita può essere usata dove necessario, con l'implementazione offerta dall'inserimento di dipendenze e da Refit:</span><span class="sxs-lookup"><span data-stu-id="863fe-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="863fe-182">Middleware per richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="863fe-182">Outgoing request middleware</span></span>

<span data-ttu-id="863fe-183">`HttpClient`ha il concetto di delegare i gestori che possono essere collegati insieme per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="863fe-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="863fe-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="863fe-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="863fe-185">Semplifica la definizione dei gestori da applicare per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="863fe-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="863fe-186">Supporta la registrazione e il concatenamento di più gestori per compilare una pipeline middleware per le richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="863fe-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="863fe-187">Ognuno di questi gestori è in grado di eseguire operazioni prima e dopo la richiesta in uscita.</span><span class="sxs-lookup"><span data-stu-id="863fe-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="863fe-188">Questo modello:</span><span class="sxs-lookup"><span data-stu-id="863fe-188">This pattern:</span></span>

  * <span data-ttu-id="863fe-189">È simile alla pipeline del middleware in ingresso in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="863fe-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="863fe-190">Fornisce un meccanismo per gestire le problematiche trasversali relative alle richieste HTTP, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="863fe-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="863fe-191">memorizzazione nella cache</span><span class="sxs-lookup"><span data-stu-id="863fe-191">caching</span></span>
    * <span data-ttu-id="863fe-192">gestione degli errori</span><span class="sxs-lookup"><span data-stu-id="863fe-192">error handling</span></span>
    * <span data-ttu-id="863fe-193">serializzazione</span><span class="sxs-lookup"><span data-stu-id="863fe-193">serialization</span></span>
    * <span data-ttu-id="863fe-194">registrazione</span><span class="sxs-lookup"><span data-stu-id="863fe-194">logging</span></span>

<span data-ttu-id="863fe-195">Per creare un gestore di delega:</span><span class="sxs-lookup"><span data-stu-id="863fe-195">To create a delegating handler:</span></span>

* <span data-ttu-id="863fe-196">Derivare da <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="863fe-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="863fe-197">Eseguire l'override di <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="863fe-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="863fe-198">Eseguire il codice prima di passare la richiesta al gestore successivo nella pipeline:</span><span class="sxs-lookup"><span data-stu-id="863fe-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="863fe-199">Il codice precedente controlla se l' `X-API-KEY` intestazione è presente nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="863fe-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="863fe-200">Se `X-API-KEY` è mancante, <xref:System.Net.HttpStatusCode.BadRequest> viene restituito.</span><span class="sxs-lookup"><span data-stu-id="863fe-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="863fe-201">È possibile aggiungere più di un gestore alla configurazione per un oggetto `HttpClient` con <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="863fe-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="863fe-202">Nel codice precedente `ValidateHeaderHandler` viene registrato nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="863fe-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="863fe-203">L'interfaccia `IHttpClientFactory` crea un ambito di inserimento delle dipendenze separato per ogni gestore.</span><span class="sxs-lookup"><span data-stu-id="863fe-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="863fe-204">I gestori possono dipendere da servizi di qualsiasi ambito.</span><span class="sxs-lookup"><span data-stu-id="863fe-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="863fe-205">I servizi da cui dipendono i gestori vengono eliminati al momento dell'eliminazione del gestore.</span><span class="sxs-lookup"><span data-stu-id="863fe-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="863fe-206">Dopo la registrazione è possibile chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, passando il tipo di gestore.</span><span class="sxs-lookup"><span data-stu-id="863fe-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="863fe-207">È possibile registrare più gestori nell'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="863fe-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="863fe-208">Ogni gestore esegue il wrapping del gestore successivo fino a quando l'elemento `HttpClientHandler` finale non esegue la richiesta:</span><span class="sxs-lookup"><span data-stu-id="863fe-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="863fe-209">Usare uno degli approcci seguenti per condividere lo stato in base alla richiesta con i gestori di messaggi:</span><span class="sxs-lookup"><span data-stu-id="863fe-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="863fe-210">Passare i dati nel gestore usando [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="863fe-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="863fe-211">Usare <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> per accedere alla richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="863fe-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="863fe-212">Creare un oggetto di archiviazione <xref:System.Threading.AsyncLocal`1> personalizzato per passare i dati.</span><span class="sxs-lookup"><span data-stu-id="863fe-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="863fe-213">Usare gestori basati su Polly</span><span class="sxs-lookup"><span data-stu-id="863fe-213">Use Polly-based handlers</span></span>

<span data-ttu-id="863fe-214">`IHttpClientFactory`si integra con la libreria di terze parti [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="863fe-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="863fe-215">Polly è una libreria di gestione degli errori temporanei e di resilienza completa per .NET.</span><span class="sxs-lookup"><span data-stu-id="863fe-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="863fe-216">Consente agli sviluppatori di esprimere criteri quali Retry, Circuit Breaker, Timeout, Bulkhead Isolation e Fallback in modo fluido e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="863fe-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="863fe-217">Per consentire l'uso dei criteri Polly con le istanze configurate di `HttpClient` sono disponibili metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="863fe-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="863fe-218">Le estensioni di Polly supportano l'aggiunta di gestori basati su Polly ai client.</span><span class="sxs-lookup"><span data-stu-id="863fe-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="863fe-219">Polly richiede il pacchetto NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="863fe-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="863fe-220">Gestire gli errori temporanei</span><span class="sxs-lookup"><span data-stu-id="863fe-220">Handle transient faults</span></span>

<span data-ttu-id="863fe-221">Gli errori si verificano in genere quando le chiamate HTTP esterne sono temporanee.</span><span class="sxs-lookup"><span data-stu-id="863fe-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="863fe-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>consente di definire un criterio per gestire gli errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="863fe-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="863fe-223">I criteri configurati con `AddTransientHttpErrorPolicy` gestiscono le risposte seguenti:</span><span class="sxs-lookup"><span data-stu-id="863fe-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="863fe-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="863fe-224">HTTP 5xx</span></span>
* <span data-ttu-id="863fe-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="863fe-225">HTTP 408</span></span>

<span data-ttu-id="863fe-226">`AddTransientHttpErrorPolicy`consente di accedere a un `PolicyBuilder` oggetto configurato per gestire gli errori che rappresentano un possibile errore temporaneo:</span><span class="sxs-lookup"><span data-stu-id="863fe-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="863fe-227">Nel codice precedente viene definito un criterio `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="863fe-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="863fe-228">Le richieste non riuscite vengono ritentate fino a tre volte con un ritardo di 600 millisecondi tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="863fe-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="863fe-229">Selezionare i criteri in modo dinamico</span><span class="sxs-lookup"><span data-stu-id="863fe-229">Dynamically select policies</span></span>

<span data-ttu-id="863fe-230">I metodi di estensione vengono forniti per aggiungere gestori basati su Polly, ad esempio <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="863fe-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="863fe-231">L' `AddPolicyHandler` Overload seguente controlla la richiesta per decidere quali criteri applicare:</span><span class="sxs-lookup"><span data-stu-id="863fe-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="863fe-232">Nel codice precedente, se la richiesta in uscita è una richiesta HTTP GET viene applicato un timeout di 10 secondi.</span><span class="sxs-lookup"><span data-stu-id="863fe-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="863fe-233">Per qualsiasi altro metodo HTTP viene usato un timeout di 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="863fe-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="863fe-234">Aggiungere più gestori Polly</span><span class="sxs-lookup"><span data-stu-id="863fe-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="863fe-235">È comune annidare i criteri Polly:</span><span class="sxs-lookup"><span data-stu-id="863fe-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="863fe-236">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="863fe-236">In the preceding example:</span></span>

* <span data-ttu-id="863fe-237">Vengono aggiunti due gestori.</span><span class="sxs-lookup"><span data-stu-id="863fe-237">Two handlers are added.</span></span>
* <span data-ttu-id="863fe-238">Il primo gestore USA <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> per aggiungere un criterio di ripetizione.</span><span class="sxs-lookup"><span data-stu-id="863fe-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="863fe-239">Le richieste non riuscite vengono ritentate fino a tre volte.</span><span class="sxs-lookup"><span data-stu-id="863fe-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="863fe-240">La seconda `AddTransientHttpErrorPolicy` chiamata aggiunge un criterio di interruttore.</span><span class="sxs-lookup"><span data-stu-id="863fe-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="863fe-241">Altre richieste esterne vengono bloccate per 30 secondi se 5 tentativi non riusciti si verificano in sequenza.</span><span class="sxs-lookup"><span data-stu-id="863fe-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="863fe-242">I criteri dell'interruttore di circuito sono con stato.</span><span class="sxs-lookup"><span data-stu-id="863fe-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="863fe-243">Tutte le chiamate tramite questo client condividono lo stesso stato di circuito.</span><span class="sxs-lookup"><span data-stu-id="863fe-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="863fe-244">Aggiungere criteri dal registro Polly</span><span class="sxs-lookup"><span data-stu-id="863fe-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="863fe-245">Un approccio alla gestione dei criteri usati di frequente consiste nel definirli una volta e registrarli in un elemento `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="863fe-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="863fe-246">Nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="863fe-246">In the following code:</span></span>

* <span data-ttu-id="863fe-247">Vengono aggiunti i criteri "regular" e "Long".</span><span class="sxs-lookup"><span data-stu-id="863fe-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="863fe-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>aggiunge i criteri "regular" e "Long" dal registro di sistema.</span><span class="sxs-lookup"><span data-stu-id="863fe-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="863fe-249">Per ulteriori informazioni su `IHttpClientFactory` e le integrazioni di Polly, vedere il [wiki di Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="863fe-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="863fe-250">Gestione di HttpClient e durata</span><span class="sxs-lookup"><span data-stu-id="863fe-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="863fe-251">Viene restituita una nuova istanza di `HttpClient` per ogni chiamata di `CreateClient` per `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="863fe-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="863fe-252"><xref:System.Net.Http.HttpMessageHandler>Viene creato un oggetto per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="863fe-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="863fe-253">La factory gestisce le durate delle istanze di `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="863fe-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="863fe-254">`IHttpClientFactory` esegue il pooling delle istanze di `HttpMessageHandler` create dalla factory per ridurre il consumo di risorse.</span><span class="sxs-lookup"><span data-stu-id="863fe-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="863fe-255">Un'istanza di `HttpMessageHandler` può essere riusata dal pool quando si crea una nuova istanza di `HttpClient` se la relativa durata non è scaduta.</span><span class="sxs-lookup"><span data-stu-id="863fe-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="863fe-256">Il pooling dei gestori è consigliabile in quanto ogni gestore gestisce in genere le proprie connessioni HTTP sottostanti.</span><span class="sxs-lookup"><span data-stu-id="863fe-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="863fe-257">La creazione di più gestori del necessario può causare ritardi di connessione.</span><span class="sxs-lookup"><span data-stu-id="863fe-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="863fe-258">Alcuni gestori mantengono inoltre le connessioni aperte a tempo indefinito, che possono impedire al gestore di reagire alle modifiche DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="863fe-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="863fe-259">La durata del gestore predefinito è di due minuti.</span><span class="sxs-lookup"><span data-stu-id="863fe-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="863fe-260">È possibile eseguire l'override del valore predefinito in base al client denominato:</span><span class="sxs-lookup"><span data-stu-id="863fe-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="863fe-261">`HttpClient`le istanze possono in genere essere considerate come oggetti .NET che **non** richiedono l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="863fe-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="863fe-262">L'eliminazione annulla le richieste in uscita e garantisce che l'istanza di `HttpClient` specificata non possa essere usata dopo la chiamata a <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="863fe-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="863fe-263">`IHttpClientFactory` tiene traccia ed elimina le risorse usate dalle istanze di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="863fe-264">Mantenere una sola istanza di `HttpClient` attiva per un lungo periodo di tempo è un modello comune usato prima dell'avvento di `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="863fe-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="863fe-265">Questo modello non è più necessario dopo la migrazione a `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="863fe-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="863fe-266">Alternative a IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="863fe-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="863fe-267">`IHttpClientFactory`L'uso di in un'app abilitata per la funzionalità consente di evitare:</span><span class="sxs-lookup"><span data-stu-id="863fe-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="863fe-268">Problemi di esaurimento delle risorse in base alle istanze di pool `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="863fe-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="863fe-269">Problemi di DNS obsoleti tramite il ciclo di `HttpMessageHandler` istanze a intervalli regolari.</span><span class="sxs-lookup"><span data-stu-id="863fe-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="863fe-270">Esistono modi alternativi per risolvere i problemi precedenti utilizzando un'istanza di lunga durata <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="863fe-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="863fe-271">Creare un'istanza di all' `SocketsHttpHandler` avvio dell'app e usarla per il ciclo di vita dell'app.</span><span class="sxs-lookup"><span data-stu-id="863fe-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="863fe-272">Configurare su <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> un valore appropriato in base alle ore di aggiornamento DNS.</span><span class="sxs-lookup"><span data-stu-id="863fe-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="863fe-273">Creare `HttpClient` istanze `new HttpClient(handler, disposeHandler: false)` di usando in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="863fe-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="863fe-274">Gli approcci precedenti risolvono i problemi di gestione delle risorse che vengono `IHttpClientFactory` risolti in modo analogo.</span><span class="sxs-lookup"><span data-stu-id="863fe-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="863fe-275">Il `SocketsHttpHandler` condivide le connessioni tra le `HttpClient` istanze.</span><span class="sxs-lookup"><span data-stu-id="863fe-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="863fe-276">Questa condivisione impedisce l'esaurimento del socket.</span><span class="sxs-lookup"><span data-stu-id="863fe-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="863fe-277">Il `SocketsHttpHandler` ciclo di connessioni in base a `PooledConnectionLifetime` per evitare problemi DNS non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="863fe-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="863fe-278">Cookie</span><span class="sxs-lookup"><span data-stu-id="863fe-278">Cookies</span></span>

<span data-ttu-id="863fe-279">Le `HttpMessageHandler` istanze in pool generano `CookieContainer` oggetti condivisi.</span><span class="sxs-lookup"><span data-stu-id="863fe-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="863fe-280">La `CookieContainer` condivisione di oggetti imprevista spesso genera codice errato.</span><span class="sxs-lookup"><span data-stu-id="863fe-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="863fe-281">Per le app che richiedono cookie, prendere in considerazione una delle seguenti operazioni:</span><span class="sxs-lookup"><span data-stu-id="863fe-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="863fe-282">Disabilitazione della gestione automatica dei cookie</span><span class="sxs-lookup"><span data-stu-id="863fe-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="863fe-283">Evitando`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="863fe-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="863fe-284">Chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per disabilitare la gestione automatica dei cookie:</span><span class="sxs-lookup"><span data-stu-id="863fe-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="863fe-285">Registrazione</span><span class="sxs-lookup"><span data-stu-id="863fe-285">Logging</span></span>

<span data-ttu-id="863fe-286">I client creati tramite `IHttpClientFactory` registrano i messaggi di log per tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="863fe-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="863fe-287">Abilitare il livello di informazioni appropriato nella configurazione di registrazione per visualizzare i messaggi di log predefiniti.</span><span class="sxs-lookup"><span data-stu-id="863fe-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="863fe-288">La registrazione aggiuntiva, ad esempio quella delle intestazioni delle richieste, è inclusa solo a livello di traccia.</span><span class="sxs-lookup"><span data-stu-id="863fe-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="863fe-289">La categoria di log usata per ogni client include il nome del client.</span><span class="sxs-lookup"><span data-stu-id="863fe-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="863fe-290">Un client denominato *MyNamedClient*, ad esempio, registra i messaggi con una categoria "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="863fe-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="863fe-291">I messaggi con suffisso *LogicalHandler* sono esterni alla pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="863fe-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="863fe-292">Nella richiesta i messaggi vengono registrati prima che qualsiasi altro gestore nella pipeline l'abbia elaborata.</span><span class="sxs-lookup"><span data-stu-id="863fe-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="863fe-293">Nella risposta i messaggi vengono registrati dopo che qualsiasi altro gestore nella pipeline ha ricevuto la risposta.</span><span class="sxs-lookup"><span data-stu-id="863fe-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="863fe-294">La registrazione avviene anche all'interno della pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="863fe-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="863fe-295">Nell'esempio *MyNamedClient* i messaggi vengono registrati con la categoria di log "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="863fe-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="863fe-296">Per la richiesta, questo errore si verifica dopo l'esecuzione di tutti gli altri gestori e immediatamente prima dell'invio della richiesta.</span><span class="sxs-lookup"><span data-stu-id="863fe-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="863fe-297">Nella risposta la registrazione include lo stato della risposta prima di restituirla attraverso la pipeline del gestore.</span><span class="sxs-lookup"><span data-stu-id="863fe-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="863fe-298">L'abilitazione della registrazione all'esterno e all'interno della pipeline consente l'ispezione delle modifiche apportate da altri gestori nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="863fe-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="863fe-299">Questo può includere modifiche alle intestazioni delle richieste o al codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="863fe-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="863fe-300">L'inclusione del nome del client nella categoria log consente il filtraggio dei log per specifici client denominati.</span><span class="sxs-lookup"><span data-stu-id="863fe-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="863fe-301">Configurare HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="863fe-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="863fe-302">Può essere necessario controllare la configurazione dell'elemento `HttpMessageHandler` interno usato da un client.</span><span class="sxs-lookup"><span data-stu-id="863fe-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="863fe-303">Quando si aggiungono client denominati o tipizzati viene restituito un elemento `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="863fe-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="863fe-304">È possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per definire un delegato.</span><span class="sxs-lookup"><span data-stu-id="863fe-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="863fe-305">Il delegato viene usato per creare e configurare l'elemento `HttpMessageHandler` primario usato dal client:</span><span class="sxs-lookup"><span data-stu-id="863fe-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="863fe-306">Usare IHttpClientFactory in un'app console</span><span class="sxs-lookup"><span data-stu-id="863fe-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="863fe-307">In un'app console aggiungere al progetto i riferimenti ai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="863fe-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="863fe-308">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="863fe-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="863fe-309">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="863fe-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="863fe-310">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="863fe-310">In the following example:</span></span>

* <span data-ttu-id="863fe-311"><xref:System.Net.Http.IHttpClientFactory> è registrato nel contenitore di servizi dell'[host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="863fe-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="863fe-312">`MyService` crea un'istanza della factory client dal servizio, che viene usata per creare una classe `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="863fe-313">`HttpClient` viene usato per recuperare una pagina Web.</span><span class="sxs-lookup"><span data-stu-id="863fe-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="863fe-314">`Main` crea un ambito per eseguire il metodo `GetPage` del servizio e scrivere i primi 500 caratteri del contenuto della pagina Web nella console.</span><span class="sxs-lookup"><span data-stu-id="863fe-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="863fe-315">Middleware di propagazione delle intestazioni</span><span class="sxs-lookup"><span data-stu-id="863fe-315">Header propagation middleware</span></span>

<span data-ttu-id="863fe-316">La propagazione dell'intestazione è un middleware ASP.NET Core per propagare le intestazioni HTTP dalla richiesta in ingresso alle richieste del client HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="863fe-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="863fe-317">Per usare la propagazione delle intestazioni:</span><span class="sxs-lookup"><span data-stu-id="863fe-317">To use header propagation:</span></span>

* <span data-ttu-id="863fe-318">Fare riferimento al pacchetto [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="863fe-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="863fe-319">Configurare il middleware e `HttpClient` in `Startup` :</span><span class="sxs-lookup"><span data-stu-id="863fe-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="863fe-320">Il client include le intestazioni configurate nelle richieste in uscita:</span><span class="sxs-lookup"><span data-stu-id="863fe-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="863fe-321">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="863fe-321">Additional resources</span></span>

* [<span data-ttu-id="863fe-322">Usare HttpClientFactory per l'implementazione di richieste HTTP resilienti</span><span class="sxs-lookup"><span data-stu-id="863fe-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="863fe-323">Implementazione dei tentativi di chiamate HTTP con backoff esponenziale con i criteri di Polly e HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="863fe-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="863fe-324">Implementazione dello schema Circuit Breaker</span><span class="sxs-lookup"><span data-stu-id="863fe-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="863fe-325">Come serializzare e deserializzare JSON in .NET</span><span class="sxs-lookup"><span data-stu-id="863fe-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="863fe-326">Di [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="863fe-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="863fe-327">È possibile registrare e usare un'interfaccia <xref:System.Net.Http.IHttpClientFactory> per configurare e creare istanze di <xref:System.Net.Http.HttpClient> in un'app.</span><span class="sxs-lookup"><span data-stu-id="863fe-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="863fe-328">I vantaggi offerti sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="863fe-328">It offers the following benefits:</span></span>

* <span data-ttu-id="863fe-329">Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche.</span><span class="sxs-lookup"><span data-stu-id="863fe-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="863fe-330">Ad esempio, è possibile registrare e configurare un client *github* per accedere a [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="863fe-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="863fe-331">È possibile registrare un client predefinito per altri scopi.</span><span class="sxs-lookup"><span data-stu-id="863fe-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="863fe-332">Codifica il concetto di middleware in uscita tramite la delega di gestori in `HttpClient` e offre estensioni per il middleware basato su Polly per sfruttarne i vantaggi.</span><span class="sxs-lookup"><span data-stu-id="863fe-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="863fe-333">Gestisce il pooling e la durata delle istanze di `HttpClientMessageHandler` sottostanti per evitare problemi DNS comuni che si verificano quando le durate di `HttpClient` vengono gestite manualmente.</span><span class="sxs-lookup"><span data-stu-id="863fe-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="863fe-334">Aggiunge un'esperienza di registrazione configurabile, tramite `ILogger`, per tutte le richieste inviate attraverso i client creati dalla factory.</span><span class="sxs-lookup"><span data-stu-id="863fe-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="863fe-335">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="863fe-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="863fe-336">Modelli di consumo</span><span class="sxs-lookup"><span data-stu-id="863fe-336">Consumption patterns</span></span>

<span data-ttu-id="863fe-337">`IHttpClientFactory` può essere usato in un'app in diversi modi:</span><span class="sxs-lookup"><span data-stu-id="863fe-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="863fe-338">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="863fe-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="863fe-339">Client denominati</span><span class="sxs-lookup"><span data-stu-id="863fe-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="863fe-340">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="863fe-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="863fe-341">Client generati</span><span class="sxs-lookup"><span data-stu-id="863fe-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="863fe-342">Nessuno di questi modi può essere considerato superiore a un altro.</span><span class="sxs-lookup"><span data-stu-id="863fe-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="863fe-343">L'approccio migliore dipende dai vincoli dell'app.</span><span class="sxs-lookup"><span data-stu-id="863fe-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="863fe-344">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="863fe-344">Basic usage</span></span>

<span data-ttu-id="863fe-345">È possibile registrare `IHttpClientFactory` chiamando il metodo di estensione `AddHttpClient` in `IServiceCollection`, all'interno del metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="863fe-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="863fe-346">Dopo la registrazione, il codice può accettare un'interfaccia `IHttpClientFactory` ovunque sia possibile inserire servizi con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="863fe-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="863fe-347">`IHttpClientFactory`È possibile utilizzare per creare un' `HttpClient` istanza di:</span><span class="sxs-lookup"><span data-stu-id="863fe-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="863fe-348">Questo uso di `IHttpClientFactory` è un modo efficace per effettuare il refactoring di un'app esistente.</span><span class="sxs-lookup"><span data-stu-id="863fe-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="863fe-349">Non influisce in alcun modo sulle modalità d'uso di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="863fe-350">Nelle posizioni in cui vengono attualmente create istanze di `HttpClient`, sostituire le occorrenze con una chiamata a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="863fe-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="863fe-351">Client denominati</span><span class="sxs-lookup"><span data-stu-id="863fe-351">Named clients</span></span>

<span data-ttu-id="863fe-352">Se un'app richiede molti usi distinti di `HttpClient`, ognuno con una configurazione diversa, un'opzione è l'uso di **client denominati**.</span><span class="sxs-lookup"><span data-stu-id="863fe-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="863fe-353">La configurazione di un `HttpClient` denominato può essere specificata durante la registrazione in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="863fe-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="863fe-354">Nel codice precedente viene chiamato `AddHttpClient`, in cui viene specificato il nome *github*.</span><span class="sxs-lookup"><span data-stu-id="863fe-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="863fe-355">Al client viene applicata una configurazione predefinita, ovvero l'indirizzo di base e due intestazioni necessari per l'uso dell'API GitHub.</span><span class="sxs-lookup"><span data-stu-id="863fe-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="863fe-356">Ogni volta che `CreateClient` viene chiamato, verrà creata una nuova istanza di `HttpClient` e verrà chiamata l'azione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="863fe-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="863fe-357">Per usare un client denominato, è possibile passare un parametro di stringa a `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="863fe-358">Specificare il nome del client da creare:</span><span class="sxs-lookup"><span data-stu-id="863fe-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="863fe-359">Nel codice precedente non è necessario che la richiesta specifichi un nome host.</span><span class="sxs-lookup"><span data-stu-id="863fe-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="863fe-360">Poiché viene usato l'indirizzo di base configurato per il client, è possibile passare solo il percorso.</span><span class="sxs-lookup"><span data-stu-id="863fe-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="863fe-361">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="863fe-361">Typed clients</span></span>

<span data-ttu-id="863fe-362">Client tipizzati:</span><span class="sxs-lookup"><span data-stu-id="863fe-362">Typed clients:</span></span>

* <span data-ttu-id="863fe-363">Offrono le stesse funzionalità dei client denominati senza la necessità di usare le stringhe come chiavi.</span><span class="sxs-lookup"><span data-stu-id="863fe-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="863fe-364">Offrono l'aiuto di IntelliSense e del compilatore quando si usano i client.</span><span class="sxs-lookup"><span data-stu-id="863fe-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="863fe-365">La configurazione e l'interazione con un particolare `HttpClient` può avvenire in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="863fe-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="863fe-366">Per un singolo endpoint back-end è ad esempio possibile usare un unico client tipizzato che incapsuli tutta la logica relativa all'endpoint.</span><span class="sxs-lookup"><span data-stu-id="863fe-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="863fe-367">Usano l'inserimento di dipendenze e possono essere inseriti dove necessario nell'app.</span><span class="sxs-lookup"><span data-stu-id="863fe-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="863fe-368">Un client tipizzato accetta un `HttpClient` parametro nel relativo costruttore:</span><span class="sxs-lookup"><span data-stu-id="863fe-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="863fe-369">Nel codice precedente la configurazione viene spostata nel client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="863fe-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="863fe-370">L'oggetto `HttpClient` viene esposto come una proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="863fe-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="863fe-371">È possibile definire metodi di API specifiche che espongono la funzionalità `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="863fe-372">Il metodo `GetAspNetDocsIssues` incapsula il codice necessario per eseguire una query e analizzare gli ultimi problemi aperti in un repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="863fe-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="863fe-373">Per registrare un client tipizzato è possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> generico all'interno di `Startup.ConfigureServices`, specificando la classe del client tipizzato:</span><span class="sxs-lookup"><span data-stu-id="863fe-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="863fe-374">Il client tipizzato viene registrato come temporaneo nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="863fe-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="863fe-375">Il client tipizzato può essere inserito e usato direttamente:</span><span class="sxs-lookup"><span data-stu-id="863fe-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="863fe-376">Se si preferisce, è possibile specificare la configurazione di un client tipizzato durante la registrazione in `Startup.ConfigureServices`, anziché nel relativo costruttore:</span><span class="sxs-lookup"><span data-stu-id="863fe-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="863fe-377">È possibile incapsulare interamente `HttpClient` all'interno di un client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="863fe-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="863fe-378">Anziché esporlo come una proprietà, è possibile specificare metodi pubblici che chiamano l'istanza di `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="863fe-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="863fe-379">Nel codice precedente `HttpClient` viene archiviato come un campo privato.</span><span class="sxs-lookup"><span data-stu-id="863fe-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="863fe-380">L'accesso per effettuare chiamate esterne passa attraverso il metodo `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="863fe-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="863fe-381">Client generati</span><span class="sxs-lookup"><span data-stu-id="863fe-381">Generated clients</span></span>

<span data-ttu-id="863fe-382">È possibile usare `IHttpClientFactory` in combinazione con altre librerie di terze parti, ad esempio [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="863fe-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="863fe-383">Refit è una libreria REST per .NET.</span><span class="sxs-lookup"><span data-stu-id="863fe-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="863fe-384">Converte le API REST in interfacce live.</span><span class="sxs-lookup"><span data-stu-id="863fe-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="863fe-385">Un'implementazione dell'interfaccia viene generata dinamicamente da `RestService`, usando `HttpClient` per effettuare le chiamate HTTP esterne.</span><span class="sxs-lookup"><span data-stu-id="863fe-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="863fe-386">Per rappresentare l'API esterna e la relativa risposta vengono definite un'interfaccia e una risposta:</span><span class="sxs-lookup"><span data-stu-id="863fe-386">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="863fe-387">È possibile aggiungere un client tipizzato usando Refit per generare l'implementazione:</span><span class="sxs-lookup"><span data-stu-id="863fe-387">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="863fe-388">L'interfaccia definita può essere usata dove necessario, con l'implementazione offerta dall'inserimento di dipendenze e da Refit:</span><span class="sxs-lookup"><span data-stu-id="863fe-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="863fe-389">Middleware per richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="863fe-389">Outgoing request middleware</span></span>

<span data-ttu-id="863fe-390">`HttpClient` include già il concetto di delega di gestori concatenati per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="863fe-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="863fe-391">`IHttpClientFactory` semplifica la definizione dei gestori da applicare per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="863fe-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="863fe-392">Supporta la registrazione e il concatenamento di più gestori per creare una pipeline di middleware per le richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="863fe-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="863fe-393">Ognuno di questi gestori è in grado di eseguire operazioni prima e dopo la richiesta in uscita.</span><span class="sxs-lookup"><span data-stu-id="863fe-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="863fe-394">Questo modello è simile alla pipeline di middleware in ingresso in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="863fe-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="863fe-395">Il modello offre un meccanismo per gestire le problematiche trasversali relative alle richieste HTTP, tra cui memorizzazione nella cache, gestione degli errori, serializzazione e registrazione.</span><span class="sxs-lookup"><span data-stu-id="863fe-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="863fe-396">Per creare un gestore, definire una classe che deriva da <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="863fe-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="863fe-397">Eseguire l'override del metodo `SendAsync` per eseguire il codice prima di passare la richiesta al gestore successivo nella pipeline:</span><span class="sxs-lookup"><span data-stu-id="863fe-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="863fe-398">Il codice precedente definisce un gestore di base.</span><span class="sxs-lookup"><span data-stu-id="863fe-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="863fe-399">Verifica se un'intestazione `X-API-KEY` è stata inclusa nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="863fe-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="863fe-400">Se l'intestazione non è presente, può evitare la chiamata HTTP e restituire una risposta appropriata.</span><span class="sxs-lookup"><span data-stu-id="863fe-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="863fe-401">Durante la registrazione, è possibile aggiungere uno o più gestori alla configurazione per un `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="863fe-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="863fe-402">Questa attività viene eseguita tramite metodi di estensione in <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="863fe-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="863fe-403">Nel codice precedente `ValidateHeaderHandler` viene registrato nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="863fe-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="863fe-404">L'interfaccia `IHttpClientFactory` crea un ambito di inserimento delle dipendenze separato per ogni gestore.</span><span class="sxs-lookup"><span data-stu-id="863fe-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="863fe-405">I gestori possono dipendere da servizi di qualsiasi ambito.</span><span class="sxs-lookup"><span data-stu-id="863fe-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="863fe-406">I servizi da cui dipendono i gestori vengono eliminati al momento dell'eliminazione del gestore.</span><span class="sxs-lookup"><span data-stu-id="863fe-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="863fe-407">Dopo la registrazione è possibile chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, passando il tipo di gestore.</span><span class="sxs-lookup"><span data-stu-id="863fe-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="863fe-408">È possibile registrare più gestori nell'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="863fe-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="863fe-409">Ogni gestore esegue il wrapping del gestore successivo fino a quando l'elemento `HttpClientHandler` finale non esegue la richiesta:</span><span class="sxs-lookup"><span data-stu-id="863fe-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="863fe-410">Usare uno degli approcci seguenti per condividere lo stato in base alla richiesta con i gestori di messaggi:</span><span class="sxs-lookup"><span data-stu-id="863fe-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="863fe-411">Passare i dati al gestore usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="863fe-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="863fe-412">Usare `IHttpContextAccessor` per accedere alla richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="863fe-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="863fe-413">Creare un oggetto di archiviazione `AsyncLocal` personalizzato per passare i dati.</span><span class="sxs-lookup"><span data-stu-id="863fe-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="863fe-414">Usare gestori basati su Polly</span><span class="sxs-lookup"><span data-stu-id="863fe-414">Use Polly-based handlers</span></span>

<span data-ttu-id="863fe-415">`IHttpClientFactory` si integra con una libreria di terze parti piuttosto diffusa denominata [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="863fe-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="863fe-416">Polly è una libreria di gestione degli errori temporanei e di resilienza completa per .NET.</span><span class="sxs-lookup"><span data-stu-id="863fe-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="863fe-417">Consente agli sviluppatori di esprimere criteri quali Retry, Circuit Breaker, Timeout, Bulkhead Isolation e Fallback in modo fluido e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="863fe-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="863fe-418">Per consentire l'uso dei criteri Polly con le istanze configurate di `HttpClient` sono disponibili metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="863fe-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="863fe-419">Le estensioni Polly:</span><span class="sxs-lookup"><span data-stu-id="863fe-419">The Polly extensions:</span></span>

* <span data-ttu-id="863fe-420">Supportano l'aggiunta di gestori basati su Polly ai client.</span><span class="sxs-lookup"><span data-stu-id="863fe-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="863fe-421">Possono essere usate dopo l'installazione del pacchetto NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="863fe-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="863fe-422">Il pacchetto non è incluso nel framework condiviso ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="863fe-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="863fe-423">Gestire gli errori temporanei</span><span class="sxs-lookup"><span data-stu-id="863fe-423">Handle transient faults</span></span>

<span data-ttu-id="863fe-424">La maggior parte degli errori comuni si verifica quando le chiamate HTTP esterne sono temporanee.</span><span class="sxs-lookup"><span data-stu-id="863fe-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="863fe-425">Per definire un criterio in grado di gestire gli errori temporanei è disponibile un pratico metodo di estensione denominato `AddTransientHttpErrorPolicy`.</span><span class="sxs-lookup"><span data-stu-id="863fe-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="863fe-426">I criteri configurati con questo metodo di estensione gestiscono `HttpRequestException`, risposte HTTP 5xx e risposte HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="863fe-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="863fe-427">L'estensione `AddTransientHttpErrorPolicy` può essere usata all'interno di `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="863fe-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="863fe-428">L'estensione consente l'accesso a un oggetto `PolicyBuilder` configurato per gestire gli errori che rappresentano un possibile errore temporaneo:</span><span class="sxs-lookup"><span data-stu-id="863fe-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="863fe-429">Nel codice precedente viene definito un criterio `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="863fe-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="863fe-430">Le richieste non riuscite vengono ritentate fino a tre volte con un ritardo di 600 millisecondi tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="863fe-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="863fe-431">Selezionare i criteri in modo dinamico</span><span class="sxs-lookup"><span data-stu-id="863fe-431">Dynamically select policies</span></span>

<span data-ttu-id="863fe-432">Per aggiungere gestori basati su Polly è possibile usare altri metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="863fe-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="863fe-433">Una di queste estensioni è `AddPolicyHandler`, che include più overload.</span><span class="sxs-lookup"><span data-stu-id="863fe-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="863fe-434">Un overload consente l'ispezione della richiesta al momento della definizione del criterio da applicare:</span><span class="sxs-lookup"><span data-stu-id="863fe-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="863fe-435">Nel codice precedente, se la richiesta in uscita è una richiesta HTTP GET viene applicato un timeout di 10 secondi.</span><span class="sxs-lookup"><span data-stu-id="863fe-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="863fe-436">Per qualsiasi altro metodo HTTP viene usato un timeout di 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="863fe-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="863fe-437">Aggiungere più gestori Polly</span><span class="sxs-lookup"><span data-stu-id="863fe-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="863fe-438">In molti casi i criteri Polly vengono annidati per offrire funzionalità avanzate:</span><span class="sxs-lookup"><span data-stu-id="863fe-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="863fe-439">Nell'esempio precedente vengono aggiunti due gestori.</span><span class="sxs-lookup"><span data-stu-id="863fe-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="863fe-440">Il primo usa l'estensione `AddTransientHttpErrorPolicy` per aggiungere criteri di ripetizione.</span><span class="sxs-lookup"><span data-stu-id="863fe-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="863fe-441">Le richieste non riuscite vengono ritentate fino a tre volte.</span><span class="sxs-lookup"><span data-stu-id="863fe-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="863fe-442">La seconda chiamata a `AddTransientHttpErrorPolicy` aggiunge criteri dell'interruttore di circuito.</span><span class="sxs-lookup"><span data-stu-id="863fe-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="863fe-443">Ulteriori richieste esterne vengono bloccate per 30 secondi nel caso si verifichino cinque tentativi non riusciti consecutivi.</span><span class="sxs-lookup"><span data-stu-id="863fe-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="863fe-444">I criteri dell'interruttore di circuito sono con stato.</span><span class="sxs-lookup"><span data-stu-id="863fe-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="863fe-445">Tutte le chiamate tramite questo client condividono lo stesso stato di circuito.</span><span class="sxs-lookup"><span data-stu-id="863fe-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="863fe-446">Aggiungere criteri dal registro Polly</span><span class="sxs-lookup"><span data-stu-id="863fe-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="863fe-447">Un approccio alla gestione dei criteri usati di frequente consiste nel definirli una volta e registrarli in un elemento `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="863fe-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="863fe-448">Per aggiungere un gestore usando un criterio del registro è disponibile un metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="863fe-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="863fe-449">Nel codice precedente vengono registrati due criteri quando si aggiunge `PolicyRegistry` a `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="863fe-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="863fe-450">Per usare un criterio dal registro viene usato il metodo `AddPolicyHandlerFromRegistry` passando il nome del criterio da applicare.</span><span class="sxs-lookup"><span data-stu-id="863fe-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="863fe-451">Altre informazioni su `IHttpClientFactory` e le integrazioni con Polly sono disponibili nel [wiki di Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="863fe-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="863fe-452">Gestione di HttpClient e durata</span><span class="sxs-lookup"><span data-stu-id="863fe-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="863fe-453">Viene restituita una nuova istanza di `HttpClient` per ogni chiamata di `CreateClient` per `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="863fe-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="863fe-454">È presente un <xref:System.Net.Http.HttpMessageHandler> per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="863fe-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="863fe-455">La factory gestisce le durate delle istanze di `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="863fe-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="863fe-456">`IHttpClientFactory` esegue il pooling delle istanze di `HttpMessageHandler` create dalla factory per ridurre il consumo di risorse.</span><span class="sxs-lookup"><span data-stu-id="863fe-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="863fe-457">Un'istanza di `HttpMessageHandler` può essere riusata dal pool quando si crea una nuova istanza di `HttpClient` se la relativa durata non è scaduta.</span><span class="sxs-lookup"><span data-stu-id="863fe-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="863fe-458">Il pooling dei gestori è consigliabile in quanto ogni gestore gestisce in genere le proprie connessioni HTTP sottostanti.</span><span class="sxs-lookup"><span data-stu-id="863fe-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="863fe-459">La creazione di più gestori del necessario può causare ritardi di connessione.</span><span class="sxs-lookup"><span data-stu-id="863fe-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="863fe-460">Alcuni gestori mantengono inoltre le connessioni aperte a tempo indefinito. Ciò può impedire al gestore di reagire alle modifiche DNS.</span><span class="sxs-lookup"><span data-stu-id="863fe-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="863fe-461">La durata del gestore predefinito è di due minuti.</span><span class="sxs-lookup"><span data-stu-id="863fe-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="863fe-462">Il valore predefinito può essere sottoposto a override per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="863fe-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="863fe-463">Per eseguire l'override, chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> nell'elemento `IHttpClientBuilder` restituito al momento della creazione del client:</span><span class="sxs-lookup"><span data-stu-id="863fe-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="863fe-464">L'eliminazione del client non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="863fe-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="863fe-465">L'eliminazione annulla le richieste in uscita e garantisce che l'istanza di `HttpClient` specificata non possa essere usata dopo la chiamata a <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="863fe-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="863fe-466">`IHttpClientFactory` tiene traccia ed elimina le risorse usate dalle istanze di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="863fe-467">Le istanze di `HttpClient` possono essere considerate a livello generale come oggetti .NET che non richiedono l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="863fe-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="863fe-468">Mantenere una sola istanza di `HttpClient` attiva per un lungo periodo di tempo è un modello comune usato prima dell'avvento di `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="863fe-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="863fe-469">Questo modello non è più necessario dopo la migrazione a `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="863fe-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="863fe-470">Alternative a IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="863fe-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="863fe-471">`IHttpClientFactory`L'uso di in un'app abilitata per la funzionalità consente di evitare:</span><span class="sxs-lookup"><span data-stu-id="863fe-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="863fe-472">Problemi di esaurimento delle risorse in base alle istanze di pool `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="863fe-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="863fe-473">Problemi di DNS obsoleti tramite il ciclo di `HttpMessageHandler` istanze a intervalli regolari.</span><span class="sxs-lookup"><span data-stu-id="863fe-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="863fe-474">Esistono modi alternativi per risolvere i problemi precedenti utilizzando un'istanza di lunga durata <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="863fe-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="863fe-475">Creare un'istanza di all' `SocketsHttpHandler` avvio dell'app e usarla per il ciclo di vita dell'app.</span><span class="sxs-lookup"><span data-stu-id="863fe-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="863fe-476">Configurare su <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> un valore appropriato in base alle ore di aggiornamento DNS.</span><span class="sxs-lookup"><span data-stu-id="863fe-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="863fe-477">Creare `HttpClient` istanze `new HttpClient(handler, disposeHandler: false)` di usando in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="863fe-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="863fe-478">Gli approcci precedenti risolvono i problemi di gestione delle risorse che vengono `IHttpClientFactory` risolti in modo analogo.</span><span class="sxs-lookup"><span data-stu-id="863fe-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="863fe-479">Il `SocketsHttpHandler` condivide le connessioni tra le `HttpClient` istanze.</span><span class="sxs-lookup"><span data-stu-id="863fe-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="863fe-480">Questa condivisione impedisce l'esaurimento del socket.</span><span class="sxs-lookup"><span data-stu-id="863fe-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="863fe-481">Il `SocketsHttpHandler` ciclo di connessioni in base a `PooledConnectionLifetime` per evitare problemi DNS non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="863fe-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="863fe-482">Cookie</span><span class="sxs-lookup"><span data-stu-id="863fe-482">Cookies</span></span>

<span data-ttu-id="863fe-483">Le `HttpMessageHandler` istanze in pool generano `CookieContainer` oggetti condivisi.</span><span class="sxs-lookup"><span data-stu-id="863fe-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="863fe-484">La `CookieContainer` condivisione di oggetti imprevista spesso genera codice errato.</span><span class="sxs-lookup"><span data-stu-id="863fe-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="863fe-485">Per le app che richiedono cookie, prendere in considerazione una delle seguenti operazioni:</span><span class="sxs-lookup"><span data-stu-id="863fe-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="863fe-486">Disabilitazione della gestione automatica dei cookie</span><span class="sxs-lookup"><span data-stu-id="863fe-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="863fe-487">Evitando`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="863fe-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="863fe-488">Chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per disabilitare la gestione automatica dei cookie:</span><span class="sxs-lookup"><span data-stu-id="863fe-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="863fe-489">Registrazione</span><span class="sxs-lookup"><span data-stu-id="863fe-489">Logging</span></span>

<span data-ttu-id="863fe-490">I client creati tramite `IHttpClientFactory` registrano i messaggi di log per tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="863fe-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="863fe-491">Per visualizzare i messaggi di log predefiniti, abilitare il livello di informazioni appropriato nella configurazione di registrazione.</span><span class="sxs-lookup"><span data-stu-id="863fe-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="863fe-492">La registrazione aggiuntiva, ad esempio quella delle intestazioni delle richieste, è inclusa solo a livello di traccia.</span><span class="sxs-lookup"><span data-stu-id="863fe-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="863fe-493">La categoria di log usata per ogni client include il nome del client.</span><span class="sxs-lookup"><span data-stu-id="863fe-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="863fe-494">Un client denominato *MyNamedClient*, ad esempio, registra i messaggi con una categoria `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="863fe-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="863fe-495">I messaggi con suffisso *LogicalHandler* sono esterni alla pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="863fe-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="863fe-496">Nella richiesta i messaggi vengono registrati prima che qualsiasi altro gestore nella pipeline l'abbia elaborata.</span><span class="sxs-lookup"><span data-stu-id="863fe-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="863fe-497">Nella risposta i messaggi vengono registrati dopo che qualsiasi altro gestore nella pipeline ha ricevuto la risposta.</span><span class="sxs-lookup"><span data-stu-id="863fe-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="863fe-498">La registrazione avviene anche all'interno della pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="863fe-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="863fe-499">Nell'esempio *MyNamedClient*, i messaggi vengono registrati nella categoria di log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="863fe-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="863fe-500">Per la richiesta, ciò avviene dopo l'esecuzione di tutti gli altri gestori e immediatamente prima che la richiesta sia inviata in rete.</span><span class="sxs-lookup"><span data-stu-id="863fe-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="863fe-501">Nella risposta la registrazione include lo stato della risposta prima di restituirla attraverso la pipeline del gestore.</span><span class="sxs-lookup"><span data-stu-id="863fe-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="863fe-502">L'abilitazione della registrazione all'esterno e all'interno della pipeline consente l'ispezione delle modifiche apportate da altri gestori nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="863fe-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="863fe-503">Le modifiche possono ad esempio riguardare le intestazioni delle richieste o il codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="863fe-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="863fe-504">L'inclusione del nome del client nella categoria di log consente di filtrare i log in base a client denominati specifici, se necessario.</span><span class="sxs-lookup"><span data-stu-id="863fe-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="863fe-505">Configurare HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="863fe-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="863fe-506">Può essere necessario controllare la configurazione dell'elemento `HttpMessageHandler` interno usato da un client.</span><span class="sxs-lookup"><span data-stu-id="863fe-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="863fe-507">Quando si aggiungono client denominati o tipizzati viene restituito un elemento `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="863fe-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="863fe-508">È possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per definire un delegato.</span><span class="sxs-lookup"><span data-stu-id="863fe-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="863fe-509">Il delegato viene usato per creare e configurare l'elemento `HttpMessageHandler` primario usato dal client:</span><span class="sxs-lookup"><span data-stu-id="863fe-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="863fe-510">Usare IHttpClientFactory in un'app console</span><span class="sxs-lookup"><span data-stu-id="863fe-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="863fe-511">In un'app console aggiungere al progetto i riferimenti ai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="863fe-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="863fe-512">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="863fe-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="863fe-513">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="863fe-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="863fe-514">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="863fe-514">In the following example:</span></span>

* <span data-ttu-id="863fe-515"><xref:System.Net.Http.IHttpClientFactory> è registrato nel contenitore di servizi dell'[host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="863fe-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="863fe-516">`MyService` crea un'istanza della factory client dal servizio, che viene usata per creare una classe `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="863fe-517">`HttpClient` viene usato per recuperare una pagina Web.</span><span class="sxs-lookup"><span data-stu-id="863fe-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="863fe-518">`Main` crea un ambito per eseguire il metodo `GetPage` del servizio e scrivere i primi 500 caratteri del contenuto della pagina Web nella console.</span><span class="sxs-lookup"><span data-stu-id="863fe-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="863fe-519">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="863fe-519">Additional resources</span></span>

* [<span data-ttu-id="863fe-520">Usare HttpClientFactory per l'implementazione di richieste HTTP resilienti</span><span class="sxs-lookup"><span data-stu-id="863fe-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="863fe-521">Implementazione dei tentativi di chiamate HTTP con backoff esponenziale con i criteri di Polly e HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="863fe-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="863fe-522">Implementazione dello schema Circuit Breaker</span><span class="sxs-lookup"><span data-stu-id="863fe-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="863fe-523">Di [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="863fe-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="863fe-524">È possibile registrare e usare un'interfaccia <xref:System.Net.Http.IHttpClientFactory> per configurare e creare istanze di <xref:System.Net.Http.HttpClient> in un'app.</span><span class="sxs-lookup"><span data-stu-id="863fe-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="863fe-525">I vantaggi offerti sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="863fe-525">It offers the following benefits:</span></span>

* <span data-ttu-id="863fe-526">Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche.</span><span class="sxs-lookup"><span data-stu-id="863fe-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="863fe-527">Ad esempio, è possibile registrare e configurare un client *github* per accedere a [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="863fe-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="863fe-528">È possibile registrare un client predefinito per altri scopi.</span><span class="sxs-lookup"><span data-stu-id="863fe-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="863fe-529">Codifica il concetto di middleware in uscita tramite la delega di gestori in `HttpClient` e offre estensioni per il middleware basato su Polly per sfruttarne i vantaggi.</span><span class="sxs-lookup"><span data-stu-id="863fe-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="863fe-530">Gestisce il pooling e la durata delle istanze di `HttpClientMessageHandler` sottostanti per evitare problemi DNS comuni che si verificano quando le durate di `HttpClient` vengono gestite manualmente.</span><span class="sxs-lookup"><span data-stu-id="863fe-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="863fe-531">Aggiunge un'esperienza di registrazione configurabile, tramite `ILogger`, per tutte le richieste inviate attraverso i client creati dalla factory.</span><span class="sxs-lookup"><span data-stu-id="863fe-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="863fe-532">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="863fe-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="863fe-533">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="863fe-533">Prerequisites</span></span>

<span data-ttu-id="863fe-534">I progetti destinati a .NET Framework richiedono l'installazione del pacchetto NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="863fe-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="863fe-535">I progetti destinati a .NET Core che fanno riferimento al [metapacchetto Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app) sono già inclusi nel pacchetto `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="863fe-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="863fe-536">Modelli di consumo</span><span class="sxs-lookup"><span data-stu-id="863fe-536">Consumption patterns</span></span>

<span data-ttu-id="863fe-537">`IHttpClientFactory` può essere usato in un'app in diversi modi:</span><span class="sxs-lookup"><span data-stu-id="863fe-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="863fe-538">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="863fe-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="863fe-539">Client denominati</span><span class="sxs-lookup"><span data-stu-id="863fe-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="863fe-540">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="863fe-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="863fe-541">Client generati</span><span class="sxs-lookup"><span data-stu-id="863fe-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="863fe-542">Nessuno di questi modi può essere considerato superiore a un altro.</span><span class="sxs-lookup"><span data-stu-id="863fe-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="863fe-543">L'approccio migliore dipende dai vincoli dell'app.</span><span class="sxs-lookup"><span data-stu-id="863fe-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="863fe-544">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="863fe-544">Basic usage</span></span>

<span data-ttu-id="863fe-545">È possibile registrare `IHttpClientFactory` chiamando il metodo di estensione `AddHttpClient` in `IServiceCollection`, all'interno del metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="863fe-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="863fe-546">Dopo la registrazione, il codice può accettare un'interfaccia `IHttpClientFactory` ovunque sia possibile inserire servizi con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="863fe-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="863fe-547">`IHttpClientFactory`È possibile utilizzare per creare un' `HttpClient` istanza di:</span><span class="sxs-lookup"><span data-stu-id="863fe-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="863fe-548">Questo uso di `IHttpClientFactory` è un modo efficace per effettuare il refactoring di un'app esistente.</span><span class="sxs-lookup"><span data-stu-id="863fe-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="863fe-549">Non influisce in alcun modo sulle modalità d'uso di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="863fe-550">Nelle posizioni in cui vengono attualmente create istanze di `HttpClient`, sostituire le occorrenze con una chiamata a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="863fe-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="863fe-551">Client denominati</span><span class="sxs-lookup"><span data-stu-id="863fe-551">Named clients</span></span>

<span data-ttu-id="863fe-552">Se un'app richiede molti usi distinti di `HttpClient`, ognuno con una configurazione diversa, un'opzione è l'uso di **client denominati**.</span><span class="sxs-lookup"><span data-stu-id="863fe-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="863fe-553">La configurazione di un `HttpClient` denominato può essere specificata durante la registrazione in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="863fe-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="863fe-554">Nel codice precedente viene chiamato `AddHttpClient`, in cui viene specificato il nome *github*.</span><span class="sxs-lookup"><span data-stu-id="863fe-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="863fe-555">Al client viene applicata una configurazione predefinita, ovvero l'indirizzo di base e due intestazioni necessari per l'uso dell'API GitHub.</span><span class="sxs-lookup"><span data-stu-id="863fe-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="863fe-556">Ogni volta che `CreateClient` viene chiamato, verrà creata una nuova istanza di `HttpClient` e verrà chiamata l'azione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="863fe-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="863fe-557">Per usare un client denominato, è possibile passare un parametro di stringa a `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="863fe-558">Specificare il nome del client da creare:</span><span class="sxs-lookup"><span data-stu-id="863fe-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="863fe-559">Nel codice precedente non è necessario che la richiesta specifichi un nome host.</span><span class="sxs-lookup"><span data-stu-id="863fe-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="863fe-560">Poiché viene usato l'indirizzo di base configurato per il client, è possibile passare solo il percorso.</span><span class="sxs-lookup"><span data-stu-id="863fe-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="863fe-561">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="863fe-561">Typed clients</span></span>

<span data-ttu-id="863fe-562">Client tipizzati:</span><span class="sxs-lookup"><span data-stu-id="863fe-562">Typed clients:</span></span>

* <span data-ttu-id="863fe-563">Offrono le stesse funzionalità dei client denominati senza la necessità di usare le stringhe come chiavi.</span><span class="sxs-lookup"><span data-stu-id="863fe-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="863fe-564">Offrono l'aiuto di IntelliSense e del compilatore quando si usano i client.</span><span class="sxs-lookup"><span data-stu-id="863fe-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="863fe-565">La configurazione e l'interazione con un particolare `HttpClient` può avvenire in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="863fe-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="863fe-566">Per un singolo endpoint back-end è ad esempio possibile usare un unico client tipizzato che incapsuli tutta la logica relativa all'endpoint.</span><span class="sxs-lookup"><span data-stu-id="863fe-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="863fe-567">Usano l'inserimento di dipendenze e possono essere inseriti dove necessario nell'app.</span><span class="sxs-lookup"><span data-stu-id="863fe-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="863fe-568">Un client tipizzato accetta un `HttpClient` parametro nel relativo costruttore:</span><span class="sxs-lookup"><span data-stu-id="863fe-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="863fe-569">Nel codice precedente la configurazione viene spostata nel client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="863fe-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="863fe-570">L'oggetto `HttpClient` viene esposto come una proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="863fe-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="863fe-571">È possibile definire metodi di API specifiche che espongono la funzionalità `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="863fe-572">Il metodo `GetAspNetDocsIssues` incapsula il codice necessario per eseguire una query e analizzare gli ultimi problemi aperti in un repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="863fe-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="863fe-573">Per registrare un client tipizzato è possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> generico all'interno di `Startup.ConfigureServices`, specificando la classe del client tipizzato:</span><span class="sxs-lookup"><span data-stu-id="863fe-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="863fe-574">Il client tipizzato viene registrato come temporaneo nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="863fe-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="863fe-575">Il client tipizzato può essere inserito e usato direttamente:</span><span class="sxs-lookup"><span data-stu-id="863fe-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="863fe-576">Se si preferisce, è possibile specificare la configurazione di un client tipizzato durante la registrazione in `Startup.ConfigureServices`, anziché nel relativo costruttore:</span><span class="sxs-lookup"><span data-stu-id="863fe-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="863fe-577">È possibile incapsulare interamente `HttpClient` all'interno di un client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="863fe-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="863fe-578">Anziché esporlo come una proprietà, è possibile specificare metodi pubblici che chiamano l'istanza di `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="863fe-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="863fe-579">Nel codice precedente `HttpClient` viene archiviato come un campo privato.</span><span class="sxs-lookup"><span data-stu-id="863fe-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="863fe-580">L'accesso per effettuare chiamate esterne passa attraverso il metodo `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="863fe-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="863fe-581">Client generati</span><span class="sxs-lookup"><span data-stu-id="863fe-581">Generated clients</span></span>

<span data-ttu-id="863fe-582">È possibile usare `IHttpClientFactory` in combinazione con altre librerie di terze parti, ad esempio [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="863fe-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="863fe-583">Refit è una libreria REST per .NET.</span><span class="sxs-lookup"><span data-stu-id="863fe-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="863fe-584">Converte le API REST in interfacce live.</span><span class="sxs-lookup"><span data-stu-id="863fe-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="863fe-585">Un'implementazione dell'interfaccia viene generata dinamicamente da `RestService`, usando `HttpClient` per effettuare le chiamate HTTP esterne.</span><span class="sxs-lookup"><span data-stu-id="863fe-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="863fe-586">Per rappresentare l'API esterna e la relativa risposta vengono definite un'interfaccia e una risposta:</span><span class="sxs-lookup"><span data-stu-id="863fe-586">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="863fe-587">È possibile aggiungere un client tipizzato usando Refit per generare l'implementazione:</span><span class="sxs-lookup"><span data-stu-id="863fe-587">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="863fe-588">L'interfaccia definita può essere usata dove necessario, con l'implementazione offerta dall'inserimento di dipendenze e da Refit:</span><span class="sxs-lookup"><span data-stu-id="863fe-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="863fe-589">Middleware per richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="863fe-589">Outgoing request middleware</span></span>

<span data-ttu-id="863fe-590">`HttpClient` include già il concetto di delega di gestori concatenati per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="863fe-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="863fe-591">`IHttpClientFactory` semplifica la definizione dei gestori da applicare per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="863fe-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="863fe-592">Supporta la registrazione e il concatenamento di più gestori per creare una pipeline di middleware per le richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="863fe-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="863fe-593">Ognuno di questi gestori è in grado di eseguire operazioni prima e dopo la richiesta in uscita.</span><span class="sxs-lookup"><span data-stu-id="863fe-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="863fe-594">Questo modello è simile alla pipeline di middleware in ingresso in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="863fe-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="863fe-595">Il modello offre un meccanismo per gestire le problematiche trasversali relative alle richieste HTTP, tra cui memorizzazione nella cache, gestione degli errori, serializzazione e registrazione.</span><span class="sxs-lookup"><span data-stu-id="863fe-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="863fe-596">Per creare un gestore, definire una classe che deriva da <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="863fe-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="863fe-597">Eseguire l'override del metodo `SendAsync` per eseguire il codice prima di passare la richiesta al gestore successivo nella pipeline:</span><span class="sxs-lookup"><span data-stu-id="863fe-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="863fe-598">Il codice precedente definisce un gestore di base.</span><span class="sxs-lookup"><span data-stu-id="863fe-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="863fe-599">Verifica se un'intestazione `X-API-KEY` è stata inclusa nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="863fe-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="863fe-600">Se l'intestazione non è presente, può evitare la chiamata HTTP e restituire una risposta appropriata.</span><span class="sxs-lookup"><span data-stu-id="863fe-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="863fe-601">Durante la registrazione, è possibile aggiungere uno o più gestori alla configurazione per un `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="863fe-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="863fe-602">Questa attività viene eseguita tramite metodi di estensione in <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="863fe-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="863fe-603">Nel codice precedente `ValidateHeaderHandler` viene registrato nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="863fe-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="863fe-604">Il gestore **deve** essere registrato nell'inserimento di dipendenze come servizio temporaneo, senza definizione di ambito.</span><span class="sxs-lookup"><span data-stu-id="863fe-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="863fe-605">Se il gestore viene registrato come un servizio con ambito e i servizi da cui dipende il gestore sono eliminabili:</span><span class="sxs-lookup"><span data-stu-id="863fe-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="863fe-606">I servizi del gestore potrebbero essere eliminati prima che il gestore esca dall'ambito.</span><span class="sxs-lookup"><span data-stu-id="863fe-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="863fe-607">I servizi del gestore eliminati causano un errore del gestore.</span><span class="sxs-lookup"><span data-stu-id="863fe-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="863fe-608">Dopo la registrazione è possibile chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, passando il tipo di gestore.</span><span class="sxs-lookup"><span data-stu-id="863fe-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="863fe-609">È possibile registrare più gestori nell'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="863fe-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="863fe-610">Ogni gestore esegue il wrapping del gestore successivo fino a quando l'elemento `HttpClientHandler` finale non esegue la richiesta:</span><span class="sxs-lookup"><span data-stu-id="863fe-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="863fe-611">Usare uno degli approcci seguenti per condividere lo stato in base alla richiesta con i gestori di messaggi:</span><span class="sxs-lookup"><span data-stu-id="863fe-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="863fe-612">Passare i dati al gestore usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="863fe-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="863fe-613">Usare `IHttpContextAccessor` per accedere alla richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="863fe-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="863fe-614">Creare un oggetto di archiviazione `AsyncLocal` personalizzato per passare i dati.</span><span class="sxs-lookup"><span data-stu-id="863fe-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="863fe-615">Usare gestori basati su Polly</span><span class="sxs-lookup"><span data-stu-id="863fe-615">Use Polly-based handlers</span></span>

<span data-ttu-id="863fe-616">`IHttpClientFactory` si integra con una libreria di terze parti piuttosto diffusa denominata [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="863fe-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="863fe-617">Polly è una libreria di gestione degli errori temporanei e di resilienza completa per .NET.</span><span class="sxs-lookup"><span data-stu-id="863fe-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="863fe-618">Consente agli sviluppatori di esprimere criteri quali Retry, Circuit Breaker, Timeout, Bulkhead Isolation e Fallback in modo fluido e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="863fe-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="863fe-619">Per consentire l'uso dei criteri Polly con le istanze configurate di `HttpClient` sono disponibili metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="863fe-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="863fe-620">Le estensioni Polly:</span><span class="sxs-lookup"><span data-stu-id="863fe-620">The Polly extensions:</span></span>

* <span data-ttu-id="863fe-621">Supportano l'aggiunta di gestori basati su Polly ai client.</span><span class="sxs-lookup"><span data-stu-id="863fe-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="863fe-622">Possono essere usate dopo l'installazione del pacchetto NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="863fe-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="863fe-623">Il pacchetto non è incluso nel framework condiviso ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="863fe-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="863fe-624">Gestire gli errori temporanei</span><span class="sxs-lookup"><span data-stu-id="863fe-624">Handle transient faults</span></span>

<span data-ttu-id="863fe-625">La maggior parte degli errori comuni si verifica quando le chiamate HTTP esterne sono temporanee.</span><span class="sxs-lookup"><span data-stu-id="863fe-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="863fe-626">Per definire un criterio in grado di gestire gli errori temporanei è disponibile un pratico metodo di estensione denominato `AddTransientHttpErrorPolicy`.</span><span class="sxs-lookup"><span data-stu-id="863fe-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="863fe-627">I criteri configurati con questo metodo di estensione gestiscono `HttpRequestException`, risposte HTTP 5xx e risposte HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="863fe-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="863fe-628">L'estensione `AddTransientHttpErrorPolicy` può essere usata all'interno di `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="863fe-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="863fe-629">L'estensione consente l'accesso a un oggetto `PolicyBuilder` configurato per gestire gli errori che rappresentano un possibile errore temporaneo:</span><span class="sxs-lookup"><span data-stu-id="863fe-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="863fe-630">Nel codice precedente viene definito un criterio `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="863fe-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="863fe-631">Le richieste non riuscite vengono ritentate fino a tre volte con un ritardo di 600 millisecondi tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="863fe-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="863fe-632">Selezionare i criteri in modo dinamico</span><span class="sxs-lookup"><span data-stu-id="863fe-632">Dynamically select policies</span></span>

<span data-ttu-id="863fe-633">Per aggiungere gestori basati su Polly è possibile usare altri metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="863fe-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="863fe-634">Una di queste estensioni è `AddPolicyHandler`, che include più overload.</span><span class="sxs-lookup"><span data-stu-id="863fe-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="863fe-635">Un overload consente l'ispezione della richiesta al momento della definizione del criterio da applicare:</span><span class="sxs-lookup"><span data-stu-id="863fe-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="863fe-636">Nel codice precedente, se la richiesta in uscita è una richiesta HTTP GET viene applicato un timeout di 10 secondi.</span><span class="sxs-lookup"><span data-stu-id="863fe-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="863fe-637">Per qualsiasi altro metodo HTTP viene usato un timeout di 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="863fe-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="863fe-638">Aggiungere più gestori Polly</span><span class="sxs-lookup"><span data-stu-id="863fe-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="863fe-639">In molti casi i criteri Polly vengono annidati per offrire funzionalità avanzate:</span><span class="sxs-lookup"><span data-stu-id="863fe-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="863fe-640">Nell'esempio precedente vengono aggiunti due gestori.</span><span class="sxs-lookup"><span data-stu-id="863fe-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="863fe-641">Il primo usa l'estensione `AddTransientHttpErrorPolicy` per aggiungere criteri di ripetizione.</span><span class="sxs-lookup"><span data-stu-id="863fe-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="863fe-642">Le richieste non riuscite vengono ritentate fino a tre volte.</span><span class="sxs-lookup"><span data-stu-id="863fe-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="863fe-643">La seconda chiamata a `AddTransientHttpErrorPolicy` aggiunge criteri dell'interruttore di circuito.</span><span class="sxs-lookup"><span data-stu-id="863fe-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="863fe-644">Ulteriori richieste esterne vengono bloccate per 30 secondi nel caso si verifichino cinque tentativi non riusciti consecutivi.</span><span class="sxs-lookup"><span data-stu-id="863fe-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="863fe-645">I criteri dell'interruttore di circuito sono con stato.</span><span class="sxs-lookup"><span data-stu-id="863fe-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="863fe-646">Tutte le chiamate tramite questo client condividono lo stesso stato di circuito.</span><span class="sxs-lookup"><span data-stu-id="863fe-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="863fe-647">Aggiungere criteri dal registro Polly</span><span class="sxs-lookup"><span data-stu-id="863fe-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="863fe-648">Un approccio alla gestione dei criteri usati di frequente consiste nel definirli una volta e registrarli in un elemento `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="863fe-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="863fe-649">Per aggiungere un gestore usando un criterio del registro è disponibile un metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="863fe-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="863fe-650">Nel codice precedente vengono registrati due criteri quando si aggiunge `PolicyRegistry` a `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="863fe-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="863fe-651">Per usare un criterio dal registro viene usato il metodo `AddPolicyHandlerFromRegistry` passando il nome del criterio da applicare.</span><span class="sxs-lookup"><span data-stu-id="863fe-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="863fe-652">Altre informazioni su `IHttpClientFactory` e le integrazioni con Polly sono disponibili nel [wiki di Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="863fe-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="863fe-653">Gestione di HttpClient e durata</span><span class="sxs-lookup"><span data-stu-id="863fe-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="863fe-654">Viene restituita una nuova istanza di `HttpClient` per ogni chiamata di `CreateClient` per `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="863fe-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="863fe-655">È presente un <xref:System.Net.Http.HttpMessageHandler> per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="863fe-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="863fe-656">La factory gestisce le durate delle istanze di `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="863fe-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="863fe-657">`IHttpClientFactory` esegue il pooling delle istanze di `HttpMessageHandler` create dalla factory per ridurre il consumo di risorse.</span><span class="sxs-lookup"><span data-stu-id="863fe-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="863fe-658">Un'istanza di `HttpMessageHandler` può essere riusata dal pool quando si crea una nuova istanza di `HttpClient` se la relativa durata non è scaduta.</span><span class="sxs-lookup"><span data-stu-id="863fe-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="863fe-659">Il pooling dei gestori è consigliabile in quanto ogni gestore gestisce in genere le proprie connessioni HTTP sottostanti.</span><span class="sxs-lookup"><span data-stu-id="863fe-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="863fe-660">La creazione di più gestori del necessario può causare ritardi di connessione.</span><span class="sxs-lookup"><span data-stu-id="863fe-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="863fe-661">Alcuni gestori mantengono inoltre le connessioni aperte a tempo indefinito. Ciò può impedire al gestore di reagire alle modifiche DNS.</span><span class="sxs-lookup"><span data-stu-id="863fe-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="863fe-662">La durata del gestore predefinito è di due minuti.</span><span class="sxs-lookup"><span data-stu-id="863fe-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="863fe-663">Il valore predefinito può essere sottoposto a override per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="863fe-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="863fe-664">Per eseguire l'override, chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> nell'elemento `IHttpClientBuilder` restituito al momento della creazione del client:</span><span class="sxs-lookup"><span data-stu-id="863fe-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="863fe-665">L'eliminazione del client non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="863fe-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="863fe-666">L'eliminazione annulla le richieste in uscita e garantisce che l'istanza di `HttpClient` specificata non possa essere usata dopo la chiamata a <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="863fe-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="863fe-667">`IHttpClientFactory` tiene traccia ed elimina le risorse usate dalle istanze di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="863fe-668">Le istanze di `HttpClient` possono essere considerate a livello generale come oggetti .NET che non richiedono l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="863fe-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="863fe-669">Mantenere una sola istanza di `HttpClient` attiva per un lungo periodo di tempo è un modello comune usato prima dell'avvento di `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="863fe-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="863fe-670">Questo modello non è più necessario dopo la migrazione a `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="863fe-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="863fe-671">Alternative a IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="863fe-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="863fe-672">`IHttpClientFactory`L'uso di in un'app abilitata per la funzionalità consente di evitare:</span><span class="sxs-lookup"><span data-stu-id="863fe-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="863fe-673">Problemi di esaurimento delle risorse in base alle istanze di pool `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="863fe-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="863fe-674">Problemi di DNS obsoleti tramite il ciclo di `HttpMessageHandler` istanze a intervalli regolari.</span><span class="sxs-lookup"><span data-stu-id="863fe-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="863fe-675">Esistono modi alternativi per risolvere i problemi precedenti utilizzando un'istanza di lunga durata <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="863fe-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="863fe-676">Creare un'istanza di all' `SocketsHttpHandler` avvio dell'app e usarla per il ciclo di vita dell'app.</span><span class="sxs-lookup"><span data-stu-id="863fe-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="863fe-677">Configurare su <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> un valore appropriato in base alle ore di aggiornamento DNS.</span><span class="sxs-lookup"><span data-stu-id="863fe-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="863fe-678">Creare `HttpClient` istanze `new HttpClient(handler, disposeHandler: false)` di usando in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="863fe-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="863fe-679">Gli approcci precedenti risolvono i problemi di gestione delle risorse che vengono `IHttpClientFactory` risolti in modo analogo.</span><span class="sxs-lookup"><span data-stu-id="863fe-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="863fe-680">Il `SocketsHttpHandler` condivide le connessioni tra le `HttpClient` istanze.</span><span class="sxs-lookup"><span data-stu-id="863fe-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="863fe-681">Questa condivisione impedisce l'esaurimento del socket.</span><span class="sxs-lookup"><span data-stu-id="863fe-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="863fe-682">Il `SocketsHttpHandler` ciclo di connessioni in base a `PooledConnectionLifetime` per evitare problemi DNS non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="863fe-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="863fe-683">Cookie</span><span class="sxs-lookup"><span data-stu-id="863fe-683">Cookies</span></span>

<span data-ttu-id="863fe-684">Le `HttpMessageHandler` istanze in pool generano `CookieContainer` oggetti condivisi.</span><span class="sxs-lookup"><span data-stu-id="863fe-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="863fe-685">La `CookieContainer` condivisione di oggetti imprevista spesso genera codice errato.</span><span class="sxs-lookup"><span data-stu-id="863fe-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="863fe-686">Per le app che richiedono cookie, prendere in considerazione una delle seguenti operazioni:</span><span class="sxs-lookup"><span data-stu-id="863fe-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="863fe-687">Disabilitazione della gestione automatica dei cookie</span><span class="sxs-lookup"><span data-stu-id="863fe-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="863fe-688">Evitando`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="863fe-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="863fe-689">Chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per disabilitare la gestione automatica dei cookie:</span><span class="sxs-lookup"><span data-stu-id="863fe-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="863fe-690">Registrazione</span><span class="sxs-lookup"><span data-stu-id="863fe-690">Logging</span></span>

<span data-ttu-id="863fe-691">I client creati tramite `IHttpClientFactory` registrano i messaggi di log per tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="863fe-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="863fe-692">Per visualizzare i messaggi di log predefiniti, abilitare il livello di informazioni appropriato nella configurazione di registrazione.</span><span class="sxs-lookup"><span data-stu-id="863fe-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="863fe-693">La registrazione aggiuntiva, ad esempio quella delle intestazioni delle richieste, è inclusa solo a livello di traccia.</span><span class="sxs-lookup"><span data-stu-id="863fe-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="863fe-694">La categoria di log usata per ogni client include il nome del client.</span><span class="sxs-lookup"><span data-stu-id="863fe-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="863fe-695">Un client denominato *MyNamedClient*, ad esempio, registra i messaggi con una categoria `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="863fe-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="863fe-696">I messaggi con suffisso *LogicalHandler* sono esterni alla pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="863fe-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="863fe-697">Nella richiesta i messaggi vengono registrati prima che qualsiasi altro gestore nella pipeline l'abbia elaborata.</span><span class="sxs-lookup"><span data-stu-id="863fe-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="863fe-698">Nella risposta i messaggi vengono registrati dopo che qualsiasi altro gestore nella pipeline ha ricevuto la risposta.</span><span class="sxs-lookup"><span data-stu-id="863fe-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="863fe-699">La registrazione avviene anche all'interno della pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="863fe-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="863fe-700">Nell'esempio *MyNamedClient*, i messaggi vengono registrati nella categoria di log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="863fe-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="863fe-701">Per la richiesta, ciò avviene dopo l'esecuzione di tutti gli altri gestori e immediatamente prima che la richiesta sia inviata in rete.</span><span class="sxs-lookup"><span data-stu-id="863fe-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="863fe-702">Nella risposta la registrazione include lo stato della risposta prima di restituirla attraverso la pipeline del gestore.</span><span class="sxs-lookup"><span data-stu-id="863fe-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="863fe-703">L'abilitazione della registrazione all'esterno e all'interno della pipeline consente l'ispezione delle modifiche apportate da altri gestori nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="863fe-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="863fe-704">Le modifiche possono ad esempio riguardare le intestazioni delle richieste o il codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="863fe-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="863fe-705">L'inclusione del nome del client nella categoria di log consente di filtrare i log in base a client denominati specifici, se necessario.</span><span class="sxs-lookup"><span data-stu-id="863fe-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="863fe-706">Configurare HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="863fe-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="863fe-707">Può essere necessario controllare la configurazione dell'elemento `HttpMessageHandler` interno usato da un client.</span><span class="sxs-lookup"><span data-stu-id="863fe-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="863fe-708">Quando si aggiungono client denominati o tipizzati viene restituito un elemento `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="863fe-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="863fe-709">È possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per definire un delegato.</span><span class="sxs-lookup"><span data-stu-id="863fe-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="863fe-710">Il delegato viene usato per creare e configurare l'elemento `HttpMessageHandler` primario usato dal client:</span><span class="sxs-lookup"><span data-stu-id="863fe-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="863fe-711">Usare IHttpClientFactory in un'app console</span><span class="sxs-lookup"><span data-stu-id="863fe-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="863fe-712">In un'app console aggiungere al progetto i riferimenti ai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="863fe-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="863fe-713">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="863fe-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="863fe-714">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="863fe-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="863fe-715">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="863fe-715">In the following example:</span></span>

* <span data-ttu-id="863fe-716"><xref:System.Net.Http.IHttpClientFactory> è registrato nel contenitore di servizi dell'[host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="863fe-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="863fe-717">`MyService` crea un'istanza della factory client dal servizio, che viene usata per creare una classe `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="863fe-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="863fe-718">`HttpClient` viene usato per recuperare una pagina Web.</span><span class="sxs-lookup"><span data-stu-id="863fe-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="863fe-719">`Main` crea un ambito per eseguire il metodo `GetPage` del servizio e scrivere i primi 500 caratteri del contenuto della pagina Web nella console.</span><span class="sxs-lookup"><span data-stu-id="863fe-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="863fe-720">Middleware di propagazione delle intestazioni</span><span class="sxs-lookup"><span data-stu-id="863fe-720">Header propagation middleware</span></span>

<span data-ttu-id="863fe-721">La propagazione dell'intestazione è un middleware supportato dalla community per propagare le intestazioni HTTP dalla richiesta in ingresso alle richieste del client HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="863fe-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="863fe-722">Per usare la propagazione delle intestazioni:</span><span class="sxs-lookup"><span data-stu-id="863fe-722">To use header propagation:</span></span>

* <span data-ttu-id="863fe-723">Fare riferimento alla porta supportata dalla community del pacchetto [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="863fe-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="863fe-724">ASP.NET Core 3,1 e versioni successive supporta [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="863fe-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="863fe-725">Configurare il middleware e `HttpClient` in `Startup` :</span><span class="sxs-lookup"><span data-stu-id="863fe-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="863fe-726">Il client include le intestazioni configurate nelle richieste in uscita:</span><span class="sxs-lookup"><span data-stu-id="863fe-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="863fe-727">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="863fe-727">Additional resources</span></span>

* [<span data-ttu-id="863fe-728">Usare HttpClientFactory per l'implementazione di richieste HTTP resilienti</span><span class="sxs-lookup"><span data-stu-id="863fe-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="863fe-729">Implementazione dei tentativi di chiamate HTTP con backoff esponenziale con i criteri di Polly e HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="863fe-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="863fe-730">Implementazione dello schema Circuit Breaker</span><span class="sxs-lookup"><span data-stu-id="863fe-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
