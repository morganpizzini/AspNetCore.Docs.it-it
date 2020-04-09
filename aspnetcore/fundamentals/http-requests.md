---
title: Effettuare richieste HTTP usando IHttpClientFactory in ASP.NET Core
author: stevejgordon
description: Informazioni sull'uso dell'interfaccia IHttpClientFactory per gestire le istanze di HttpClient logiche in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
uid: fundamentals/http-requests
ms.openlocfilehash: 912be34ae0ee25837a94aab65443f15b17ab4556
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661686"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="a80b9-103">Effettuare richieste HTTP usando IHttpClientFactory in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a80b9-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a80b9-104">Di [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), Steve [Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), e [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="a80b9-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="a80b9-105">È possibile registrare e usare un'interfaccia <xref:System.Net.Http.IHttpClientFactory> per configurare e creare istanze di <xref:System.Net.Http.HttpClient> in un'app.</span><span class="sxs-lookup"><span data-stu-id="a80b9-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="a80b9-106">`IHttpClientFactory`offre i seguenti vantaggi:</span><span class="sxs-lookup"><span data-stu-id="a80b9-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="a80b9-107">Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche.</span><span class="sxs-lookup"><span data-stu-id="a80b9-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="a80b9-108">Ad esempio, un client denominato *github* potrebbe essere registrato e configurato per accedere a [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="a80b9-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="a80b9-109">Un client predefinito può essere registrato per l'accesso generale.</span><span class="sxs-lookup"><span data-stu-id="a80b9-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="a80b9-110">Codifica il concetto di middleware in uscita tramite i gestori di delega in `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="a80b9-111">Fornisce estensioni per il middleware basato su Polly per sfruttare i vantaggi della delega dei gestori in `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="a80b9-112">Gestisce il pooling e `HttpClientMessageHandler` la durata delle istanze sottostanti.</span><span class="sxs-lookup"><span data-stu-id="a80b9-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="a80b9-113">La gestione automatica evita i problemi comuni di `HttpClient` DNS (Domain Name System) che si verificano durante la gestione manuale delle durate.</span><span class="sxs-lookup"><span data-stu-id="a80b9-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="a80b9-114">Aggiunge un'esperienza di registrazione configurabile, tramite `ILogger`, per tutte le richieste inviate attraverso i client creati dalla factory.</span><span class="sxs-lookup"><span data-stu-id="a80b9-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="a80b9-115">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a80b9-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="a80b9-116">Il codice di esempio <xref:System.Text.Json> in questa versione dell'argomento usa per deserializzare il contenuto JSON restituito nelle risposte HTTP.</span><span class="sxs-lookup"><span data-stu-id="a80b9-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="a80b9-117">Per gli `Json.NET` esempi `ReadAsAsync<T>`che utilizzano e , utilizzare il selettore di versione per selezionare una versione 2.x di questo argomento.</span><span class="sxs-lookup"><span data-stu-id="a80b9-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="a80b9-118">Modelli di consumo</span><span class="sxs-lookup"><span data-stu-id="a80b9-118">Consumption patterns</span></span>

<span data-ttu-id="a80b9-119">`IHttpClientFactory` può essere usato in un'app in diversi modi:</span><span class="sxs-lookup"><span data-stu-id="a80b9-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="a80b9-120">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="a80b9-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="a80b9-121">Client denominati</span><span class="sxs-lookup"><span data-stu-id="a80b9-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="a80b9-122">Client tipizzato</span><span class="sxs-lookup"><span data-stu-id="a80b9-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="a80b9-123">Client generati</span><span class="sxs-lookup"><span data-stu-id="a80b9-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="a80b9-124">L'approccio migliore dipende dai requisiti dell'app.</span><span class="sxs-lookup"><span data-stu-id="a80b9-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="a80b9-125">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="a80b9-125">Basic usage</span></span>

<span data-ttu-id="a80b9-126">`IHttpClientFactory`possono essere registrati `AddHttpClient`chiamando :</span><span class="sxs-lookup"><span data-stu-id="a80b9-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="a80b9-127">Un `IHttpClientFactory` oggetto può essere richiesto utilizzando [l'inserimento delle dipendenze (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a80b9-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a80b9-128">Il codice `IHttpClientFactory` seguente usa `HttpClient` per creare un'istanza:The following code uses to create an instance:</span><span class="sxs-lookup"><span data-stu-id="a80b9-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="a80b9-129">L'uso `IHttpClientFactory` di like nell'esempio precedente è un buon modo per eseguire il refactoring di un'app esistente.</span><span class="sxs-lookup"><span data-stu-id="a80b9-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="a80b9-130">Non ha alcun `HttpClient` impatto su come viene utilizzato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="a80b9-131">Nelle posizioni `HttpClient` in cui vengono create istanze in un'app esistente, sostituire tali occorrenze con chiamate a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="a80b9-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="a80b9-132">Client denominati</span><span class="sxs-lookup"><span data-stu-id="a80b9-132">Named clients</span></span>

<span data-ttu-id="a80b9-133">I client denominati sono una buona scelta quando:</span><span class="sxs-lookup"><span data-stu-id="a80b9-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="a80b9-134">L'applicazione richiede molti `HttpClient`usi distinti di .</span><span class="sxs-lookup"><span data-stu-id="a80b9-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="a80b9-135">Molti `HttpClient`s hanno una configurazione diversa.</span><span class="sxs-lookup"><span data-stu-id="a80b9-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="a80b9-136">La configurazione `HttpClient` per un nome `Startup.ConfigureServices`può essere specificata durante la registrazione in:</span><span class="sxs-lookup"><span data-stu-id="a80b9-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="a80b9-137">Nel codice precedente il client è configurato con:</span><span class="sxs-lookup"><span data-stu-id="a80b9-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="a80b9-138">L'indirizzo `https://api.github.com/`di base .</span><span class="sxs-lookup"><span data-stu-id="a80b9-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="a80b9-139">Due intestazioni necessarie per lavorare con l'API GitHub.</span><span class="sxs-lookup"><span data-stu-id="a80b9-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="a80b9-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="a80b9-140">CreateClient</span></span>

<span data-ttu-id="a80b9-141">Ogni <xref:System.Net.Http.IHttpClientFactory.CreateClient*> volta è chiamato:</span><span class="sxs-lookup"><span data-stu-id="a80b9-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="a80b9-142">Viene creata `HttpClient` una nuova istanza di.</span><span class="sxs-lookup"><span data-stu-id="a80b9-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="a80b9-143">L'azione di configurazione viene chiamata.</span><span class="sxs-lookup"><span data-stu-id="a80b9-143">The configuration action is called.</span></span>

<span data-ttu-id="a80b9-144">Per creare un client denominato, `CreateClient`passarne il nome in :</span><span class="sxs-lookup"><span data-stu-id="a80b9-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="a80b9-145">Nel codice precedente non è necessario che la richiesta specifichi un nome host.</span><span class="sxs-lookup"><span data-stu-id="a80b9-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="a80b9-146">Il codice può passare solo il percorso, poiché viene utilizzato l'indirizzo di base configurato per il client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="a80b9-147">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="a80b9-147">Typed clients</span></span>

<span data-ttu-id="a80b9-148">Client tipizzati:</span><span class="sxs-lookup"><span data-stu-id="a80b9-148">Typed clients:</span></span>

* <span data-ttu-id="a80b9-149">Offrono le stesse funzionalità dei client denominati senza la necessità di usare le stringhe come chiavi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="a80b9-150">Offrono l'aiuto di IntelliSense e del compilatore quando si usano i client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="a80b9-151">La configurazione e l'interazione con un particolare `HttpClient` può avvenire in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="a80b9-152">Ad esempio, è possibile utilizzare un singolo client tipizzato:For example, a single typed client might be used:</span><span class="sxs-lookup"><span data-stu-id="a80b9-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="a80b9-153">Per un singolo endpoint back-end.</span><span class="sxs-lookup"><span data-stu-id="a80b9-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="a80b9-154">Per incapsulare tutta la logica che gestisce l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="a80b9-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="a80b9-155">Lavora con DI e può essere iniettato dove richiesto nell'app.</span><span class="sxs-lookup"><span data-stu-id="a80b9-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="a80b9-156">Un client tipizzato `HttpClient` accetta un parametro nel relativo costruttore:A typed client accepts an parameter in its constructor:</span><span class="sxs-lookup"><span data-stu-id="a80b9-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="a80b9-157">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="a80b9-157">In the preceding code:</span></span>

* <span data-ttu-id="a80b9-158">La configurazione viene spostata nel client digitato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="a80b9-159">L'oggetto `HttpClient` viene esposto come una proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="a80b9-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="a80b9-160">È possibile creare metodi specifici `HttpClient` dell'API che espongono funzionalità.</span><span class="sxs-lookup"><span data-stu-id="a80b9-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="a80b9-161">Ad esempio, `GetAspNetDocsIssues` il metodo incapsula il codice per recuperare i problemi di apertura.</span><span class="sxs-lookup"><span data-stu-id="a80b9-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="a80b9-162">Il codice <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> seguente `Startup.ConfigureServices` chiama per registrare una classe client tipizzata:The following code calls in to register a typed client class:</span><span class="sxs-lookup"><span data-stu-id="a80b9-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="a80b9-163">Il client tipizzato viene registrato come temporaneo nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="a80b9-164">Nel codice precedente, `AddHttpClient` registra `GitHubService` come servizio temporaneo.</span><span class="sxs-lookup"><span data-stu-id="a80b9-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="a80b9-165">Questa registrazione utilizza un metodo factory per:This registration uses a factory method to:</span><span class="sxs-lookup"><span data-stu-id="a80b9-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="a80b9-166">Creare un'istanza di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="a80b9-167">Creare un'istanza di `GitHubService`, `HttpClient` passando l'istanza di al relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="a80b9-168">Il client tipizzato può essere inserito e usato direttamente:</span><span class="sxs-lookup"><span data-stu-id="a80b9-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="a80b9-169">La configurazione per un client tipizzato `Startup.ConfigureServices`può essere specificata durante la registrazione in , anziché nel costruttore del client tipizzato:</span><span class="sxs-lookup"><span data-stu-id="a80b9-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="a80b9-170">L'oggetto `HttpClient` può essere incapsulato all'interno di un client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="a80b9-171">Anziché esporlo come proprietà, definire un `HttpClient` metodo che chiama internamente l'istanza:</span><span class="sxs-lookup"><span data-stu-id="a80b9-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="a80b9-172">Nel codice precedente, `HttpClient` l'oggetto viene archiviato in un campo privato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="a80b9-173">L'accesso `HttpClient` al è `GetRepos` con il metodo pubblico.</span><span class="sxs-lookup"><span data-stu-id="a80b9-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="a80b9-174">Client generati</span><span class="sxs-lookup"><span data-stu-id="a80b9-174">Generated clients</span></span>

<span data-ttu-id="a80b9-175">`IHttpClientFactory`può essere utilizzato in combinazione con librerie di terze parti come [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="a80b9-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="a80b9-176">Refit è una libreria REST per .NET.</span><span class="sxs-lookup"><span data-stu-id="a80b9-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="a80b9-177">Converte le API REST in interfacce live.</span><span class="sxs-lookup"><span data-stu-id="a80b9-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="a80b9-178">Un'implementazione dell'interfaccia viene generata dinamicamente da `RestService`, usando `HttpClient` per effettuare le chiamate HTTP esterne.</span><span class="sxs-lookup"><span data-stu-id="a80b9-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="a80b9-179">Per rappresentare l'API esterna e la relativa risposta vengono definite un'interfaccia e una risposta:</span><span class="sxs-lookup"><span data-stu-id="a80b9-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="a80b9-180">È possibile aggiungere un client tipizzato usando Refit per generare l'implementazione:</span><span class="sxs-lookup"><span data-stu-id="a80b9-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="a80b9-181">L'interfaccia definita può essere usata dove necessario, con l'implementazione offerta dall'inserimento di dipendenze e da Refit:</span><span class="sxs-lookup"><span data-stu-id="a80b9-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="a80b9-182">Middleware per richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="a80b9-182">Outgoing request middleware</span></span>

<span data-ttu-id="a80b9-183">`HttpClient`ha il concetto di delega di gestori che possono essere collegati tra loro per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="a80b9-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="a80b9-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="a80b9-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="a80b9-185">Semplifica la definizione dei gestori da applicare per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="a80b9-186">Supporta la registrazione e il concatenamento di più gestori per compilare una pipeline middleware di richiesta in uscita.</span><span class="sxs-lookup"><span data-stu-id="a80b9-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="a80b9-187">Ognuno di questi gestori è in grado di eseguire operazioni prima e dopo la richiesta in uscita.</span><span class="sxs-lookup"><span data-stu-id="a80b9-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="a80b9-188">Questo modello:</span><span class="sxs-lookup"><span data-stu-id="a80b9-188">This pattern:</span></span>

  * <span data-ttu-id="a80b9-189">È simile alla pipeline middleware in ingresso in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a80b9-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="a80b9-190">Fornisce un meccanismo per gestire i problemi trasversali relativi alle richieste HTTP, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a80b9-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="a80b9-191">memorizzazione nella cache</span><span class="sxs-lookup"><span data-stu-id="a80b9-191">caching</span></span>
    * <span data-ttu-id="a80b9-192">gestione degli errori</span><span class="sxs-lookup"><span data-stu-id="a80b9-192">error handling</span></span>
    * <span data-ttu-id="a80b9-193">serializzazione</span><span class="sxs-lookup"><span data-stu-id="a80b9-193">serialization</span></span>
    * <span data-ttu-id="a80b9-194">registrazione</span><span class="sxs-lookup"><span data-stu-id="a80b9-194">logging</span></span>

<span data-ttu-id="a80b9-195">Per creare un gestore di delega:To create a delegating handler:</span><span class="sxs-lookup"><span data-stu-id="a80b9-195">To create a delegating handler:</span></span>

* <span data-ttu-id="a80b9-196">Derivare <xref:System.Net.Http.DelegatingHandler>da .</span><span class="sxs-lookup"><span data-stu-id="a80b9-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="a80b9-197">Eseguire l'override di <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="a80b9-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="a80b9-198">Eseguire il codice prima di passare la richiesta al gestore successivo nella pipeline:Execute code before passing the request to the next handler in the pipeline:</span><span class="sxs-lookup"><span data-stu-id="a80b9-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="a80b9-199">Il codice precedente controlla `X-API-KEY` se l'intestazione è nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="a80b9-200">Se `X-API-KEY` manca, <xref:System.Net.HttpStatusCode.BadRequest> viene restituito.</span><span class="sxs-lookup"><span data-stu-id="a80b9-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="a80b9-201">È possibile aggiungere più di un `HttpClient` gestore alla configurazione di un con: <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName></span><span class="sxs-lookup"><span data-stu-id="a80b9-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="a80b9-202">Nel codice precedente `ValidateHeaderHandler` viene registrato nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="a80b9-203">L'interfaccia `IHttpClientFactory` crea un ambito di inserimento delle dipendenze separato per ogni gestore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="a80b9-204">I gestori possono dipendere da servizi di qualsiasi ambito.</span><span class="sxs-lookup"><span data-stu-id="a80b9-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="a80b9-205">I servizi da cui dipendono i gestori vengono eliminati al momento dell'eliminazione del gestore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="a80b9-206">Dopo la registrazione è possibile chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, passando il tipo di gestore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="a80b9-207">È possibile registrare più gestori nell'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="a80b9-208">Ogni gestore esegue il wrapping del gestore successivo fino a quando l'elemento `HttpClientHandler` finale non esegue la richiesta:</span><span class="sxs-lookup"><span data-stu-id="a80b9-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="a80b9-209">Usare uno degli approcci seguenti per condividere lo stato in base alla richiesta con i gestori di messaggi:</span><span class="sxs-lookup"><span data-stu-id="a80b9-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="a80b9-210">Passare i dati nel gestore utilizzando [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="a80b9-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="a80b9-211">Usare <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> per accedere alla richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="a80b9-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="a80b9-212">Creare un oggetto di archiviazione <xref:System.Threading.AsyncLocal`1> personalizzato per passare i dati.</span><span class="sxs-lookup"><span data-stu-id="a80b9-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="a80b9-213">Usare gestori basati su Polly</span><span class="sxs-lookup"><span data-stu-id="a80b9-213">Use Polly-based handlers</span></span>

<span data-ttu-id="a80b9-214">`IHttpClientFactory`si integra con la biblioteca di terze parti [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="a80b9-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="a80b9-215">Polly è una libreria di gestione degli errori temporanei e di resilienza completa per .NET.</span><span class="sxs-lookup"><span data-stu-id="a80b9-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="a80b9-216">Consente agli sviluppatori di esprimere criteri quali Retry, Circuit Breaker, Timeout, Bulkhead Isolation e Fallback in modo fluido e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="a80b9-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="a80b9-217">Per consentire l'uso dei criteri Polly con le istanze configurate di `HttpClient` sono disponibili metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="a80b9-218">Le estensioni Polly supportano l'aggiunta di gestori basati su Polly ai client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="a80b9-219">Polly richiede il pacchetto [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="a80b9-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="a80b9-220">Gestire gli errori temporanei</span><span class="sxs-lookup"><span data-stu-id="a80b9-220">Handle transient faults</span></span>

<span data-ttu-id="a80b9-221">Gli errori si verificano in genere quando le chiamate HTTP esterne sono temporanee.</span><span class="sxs-lookup"><span data-stu-id="a80b9-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="a80b9-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>consente di definire un criterio per gestire gli errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="a80b9-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="a80b9-223">I criteri `AddTransientHttpErrorPolicy` configurati con gestiscono le risposte seguenti:Policies configured with handle the following responses:</span><span class="sxs-lookup"><span data-stu-id="a80b9-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="a80b9-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="a80b9-224">HTTP 5xx</span></span>
* <span data-ttu-id="a80b9-225">HTTP 408 (informazioni in due)</span><span class="sxs-lookup"><span data-stu-id="a80b9-225">HTTP 408</span></span>

<span data-ttu-id="a80b9-226">`AddTransientHttpErrorPolicy`fornisce l'accesso a un `PolicyBuilder` oggetto configurato per gestire gli errori che rappresentano un possibile errore temporaneo:</span><span class="sxs-lookup"><span data-stu-id="a80b9-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="a80b9-227">Nel codice precedente viene definito un criterio `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="a80b9-228">Le richieste non riuscite vengono ritentate fino a tre volte con un ritardo di 600 millisecondi tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="a80b9-229">Selezionare i criteri in modo dinamico</span><span class="sxs-lookup"><span data-stu-id="a80b9-229">Dynamically select policies</span></span>

<span data-ttu-id="a80b9-230">Vengono forniti metodi di estensione per aggiungere gestori <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>basati su Polly, ad esempio .</span><span class="sxs-lookup"><span data-stu-id="a80b9-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="a80b9-231">L'overload seguente `AddPolicyHandler` esamina la richiesta per decidere quale criterio applicare:</span><span class="sxs-lookup"><span data-stu-id="a80b9-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="a80b9-232">Nel codice precedente, se la richiesta in uscita è una richiesta HTTP GET viene applicato un timeout di 10 secondi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="a80b9-233">Per qualsiasi altro metodo HTTP viene usato un timeout di 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="a80b9-234">Aggiungere più gestori Polly</span><span class="sxs-lookup"><span data-stu-id="a80b9-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="a80b9-235">È comune nidificare i criteri di Polly:It's common to nest Polly policies:</span><span class="sxs-lookup"><span data-stu-id="a80b9-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="a80b9-236">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="a80b9-236">In the preceding example:</span></span>

* <span data-ttu-id="a80b9-237">Vengono aggiunti due gestori.</span><span class="sxs-lookup"><span data-stu-id="a80b9-237">Two handlers are added.</span></span>
* <span data-ttu-id="a80b9-238">Il primo <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> gestore utilizza per aggiungere un criterio di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="a80b9-239">Le richieste non riuscite vengono ritentate fino a tre volte.</span><span class="sxs-lookup"><span data-stu-id="a80b9-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="a80b9-240">La `AddTransientHttpErrorPolicy` seconda chiamata aggiunge un criterio di interruttore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="a80b9-241">Altre richieste esterne vengono bloccate per 30 secondi se 5 tentativi non riusciti si verificano in sequenza.</span><span class="sxs-lookup"><span data-stu-id="a80b9-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="a80b9-242">I criteri dell'interruttore di circuito sono con stato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="a80b9-243">Tutte le chiamate tramite questo client condividono lo stesso stato di circuito.</span><span class="sxs-lookup"><span data-stu-id="a80b9-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="a80b9-244">Aggiungere criteri dal registro Polly</span><span class="sxs-lookup"><span data-stu-id="a80b9-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="a80b9-245">Un approccio alla gestione dei criteri usati di frequente consiste nel definirli una volta e registrarli in un elemento `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="a80b9-246">Nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="a80b9-246">In the following code:</span></span>

* <span data-ttu-id="a80b9-247">Vengono aggiunte le forze di polizia "regolari" e "lunghe".</span><span class="sxs-lookup"><span data-stu-id="a80b9-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="a80b9-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>aggiunge i criteri "regolari" e "lunghi" dal Registro di sistema.</span><span class="sxs-lookup"><span data-stu-id="a80b9-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="a80b9-249">Per ulteriori `IHttpClientFactory` informazioni sulle integrazioni e Polly, consultate il wiki di [Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="a80b9-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="a80b9-250">Gestione di HttpClient e durata</span><span class="sxs-lookup"><span data-stu-id="a80b9-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="a80b9-251">Viene restituita una nuova istanza di `HttpClient` per ogni chiamata di `CreateClient` per `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="a80b9-252">Viene <xref:System.Net.Http.HttpMessageHandler> creato un client per client denominato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="a80b9-253">La factory gestisce le durate delle istanze di `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="a80b9-254">`IHttpClientFactory` esegue il pooling delle istanze di `HttpMessageHandler` create dalla factory per ridurre il consumo di risorse.</span><span class="sxs-lookup"><span data-stu-id="a80b9-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="a80b9-255">Un'istanza di `HttpMessageHandler` può essere riusata dal pool quando si crea una nuova istanza di `HttpClient` se la relativa durata non è scaduta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="a80b9-256">Il pooling dei gestori è consigliabile in quanto ogni gestore gestisce in genere le proprie connessioni HTTP sottostanti.</span><span class="sxs-lookup"><span data-stu-id="a80b9-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="a80b9-257">La creazione di più gestori del necessario può causare ritardi di connessione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="a80b9-258">Alcuni gestori mantengono le connessioni aperte anche a tempo indeterminato, il che può impedire al gestore di reagire alle modifiche DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="a80b9-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="a80b9-259">La durata del gestore predefinito è di due minuti.</span><span class="sxs-lookup"><span data-stu-id="a80b9-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="a80b9-260">Il valore predefinito può essere sostituito in base al client denominato:The default value can be overridden on a pre named client basis:</span><span class="sxs-lookup"><span data-stu-id="a80b9-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="a80b9-261">`HttpClient`le istanze possono in genere essere considerate come oggetti .NET **che non** richiedono l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="a80b9-262">L'eliminazione annulla le richieste in uscita e garantisce che l'istanza di `HttpClient` specificata non possa essere usata dopo la chiamata a <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="a80b9-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="a80b9-263">`IHttpClientFactory` tiene traccia ed elimina le risorse usate dalle istanze di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="a80b9-264">Mantenere una sola istanza di `HttpClient` attiva per un lungo periodo di tempo è un modello comune usato prima dell'avvento di `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="a80b9-265">Questo modello non è più necessario dopo la migrazione a `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="a80b9-266">Alternative a IHttpClientFactoryAlternatives to IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="a80b9-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="a80b9-267">L'uso in un'app abilitata per DI consente di evitare:Using `IHttpClientFactory` in a DI-enabled app avoids:</span><span class="sxs-lookup"><span data-stu-id="a80b9-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="a80b9-268">Problemi di esaurimento delle `HttpMessageHandler` risorse mediante il pooling delle istanze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="a80b9-269">Problemi DNS non `HttpMessageHandler` aggiornati causati dalle istanze di ciclismo a intervalli regolari.</span><span class="sxs-lookup"><span data-stu-id="a80b9-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="a80b9-270">Esistono modi alternativi per risolvere i problemi <xref:System.Net.Http.SocketsHttpHandler> precedenti utilizzando un'istanza di lunga durata.</span><span class="sxs-lookup"><span data-stu-id="a80b9-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="a80b9-271">Crea un'istanza di `SocketsHttpHandler` quando l'app viene avviata e usala per tutta la vita dell'app.</span><span class="sxs-lookup"><span data-stu-id="a80b9-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="a80b9-272">Configurare <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> un valore appropriato in base ai tempi di aggiornamento DNS.</span><span class="sxs-lookup"><span data-stu-id="a80b9-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="a80b9-273">Creare `HttpClient` istanze in `new HttpClient(handler, disposeHandler: false)` base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="a80b9-274">Gli approcci precedenti risolvono `IHttpClientFactory` i problemi di gestione delle risorse che risolvono in modo simile.</span><span class="sxs-lookup"><span data-stu-id="a80b9-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="a80b9-275">Le `SocketsHttpHandler` connessioni `HttpClient` azionarie tra le istanze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="a80b9-276">Questa condivisione impedisce l'esaurimento dei socket.</span><span class="sxs-lookup"><span data-stu-id="a80b9-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="a80b9-277">Le `SocketsHttpHandler` connessioni cicli `PooledConnectionLifetime` in base a per evitare problemi DNS non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="a80b9-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="a80b9-278">Cookie</span><span class="sxs-lookup"><span data-stu-id="a80b9-278">Cookies</span></span>

<span data-ttu-id="a80b9-279">Le istanze `HttpMessageHandler` in `CookieContainer` pool determinano la condivisione degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="a80b9-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="a80b9-280">La `CookieContainer` condivisione imprevista degli oggetti spesso genera codice non corretto.</span><span class="sxs-lookup"><span data-stu-id="a80b9-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="a80b9-281">Per le app che richiedono cookie, considerare:</span><span class="sxs-lookup"><span data-stu-id="a80b9-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="a80b9-282">Disattivazione della gestione automatica dei cookie</span><span class="sxs-lookup"><span data-stu-id="a80b9-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="a80b9-283">Evitando`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="a80b9-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="a80b9-284">Chiamata <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per disabilitare la gestione automatica dei cookie:</span><span class="sxs-lookup"><span data-stu-id="a80b9-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="a80b9-285">Registrazione</span><span class="sxs-lookup"><span data-stu-id="a80b9-285">Logging</span></span>

<span data-ttu-id="a80b9-286">I client creati tramite `IHttpClientFactory` registrano i messaggi di log per tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="a80b9-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="a80b9-287">Abilitare il livello di informazioni appropriato nella configurazione di registrazione per visualizzare i messaggi di log predefiniti.</span><span class="sxs-lookup"><span data-stu-id="a80b9-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="a80b9-288">La registrazione aggiuntiva, ad esempio quella delle intestazioni delle richieste, è inclusa solo a livello di traccia.</span><span class="sxs-lookup"><span data-stu-id="a80b9-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="a80b9-289">La categoria di log usata per ogni client include il nome del client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="a80b9-290">Un client denominato *MyNamedClient*, ad esempio, registra i messaggi con una categoria di "System.Net.Http.HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="a80b9-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="a80b9-291">I messaggi con suffisso *LogicalHandler* sono esterni alla pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="a80b9-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="a80b9-292">Nella richiesta i messaggi vengono registrati prima che qualsiasi altro gestore nella pipeline l'abbia elaborata.</span><span class="sxs-lookup"><span data-stu-id="a80b9-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="a80b9-293">Nella risposta i messaggi vengono registrati dopo che qualsiasi altro gestore nella pipeline ha ricevuto la risposta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="a80b9-294">La registrazione avviene anche all'interno della pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="a80b9-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="a80b9-295">Nell'esempio *MyNamedClient,* tali messaggi vengono registrati con la categoria di registro "System.Net.Http.HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="a80b9-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="a80b9-296">Per la richiesta, ciò si verifica dopo l'esecuzione di tutti gli altri gestori e immediatamente prima dell'invio della richiesta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="a80b9-297">Nella risposta la registrazione include lo stato della risposta prima di restituirla attraverso la pipeline del gestore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="a80b9-298">L'abilitazione della registrazione all'esterno e all'interno della pipeline consente l'ispezione delle modifiche apportate da altri gestori nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="a80b9-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="a80b9-299">Ciò può includere modifiche alle intestazioni di richiesta o al codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="a80b9-300">L'inclusione del nome del client nella categoria di log consente il filtro dei log per client denominati specifici.</span><span class="sxs-lookup"><span data-stu-id="a80b9-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="a80b9-301">Configurare HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="a80b9-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="a80b9-302">Può essere necessario controllare la configurazione dell'elemento `HttpMessageHandler` interno usato da un client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="a80b9-303">Quando si aggiungono client denominati o tipizzati viene restituito un elemento `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="a80b9-304">È possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per definire un delegato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="a80b9-305">Il delegato viene usato per creare e configurare l'elemento `HttpMessageHandler` primario usato dal client:</span><span class="sxs-lookup"><span data-stu-id="a80b9-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="a80b9-306">Usare IHttpClientFactory in un'app console</span><span class="sxs-lookup"><span data-stu-id="a80b9-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="a80b9-307">In un'app console aggiungere al progetto i riferimenti ai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="a80b9-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="a80b9-308">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="a80b9-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="a80b9-309">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="a80b9-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="a80b9-310">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="a80b9-310">In the following example:</span></span>

* <span data-ttu-id="a80b9-311"><xref:System.Net.Http.IHttpClientFactory> è registrato nel contenitore di servizi dell'[host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="a80b9-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="a80b9-312">`MyService` crea un'istanza della factory client dal servizio, che viene usata per creare una classe `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="a80b9-313">`HttpClient` viene usato per recuperare una pagina Web.</span><span class="sxs-lookup"><span data-stu-id="a80b9-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="a80b9-314">`Main` crea un ambito per eseguire il metodo `GetPage` del servizio e scrivere i primi 500 caratteri del contenuto della pagina Web nella console.</span><span class="sxs-lookup"><span data-stu-id="a80b9-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="a80b9-315">Middleware di propagazione dell'intestazione</span><span class="sxs-lookup"><span data-stu-id="a80b9-315">Header propagation middleware</span></span>

<span data-ttu-id="a80b9-316">La propagazione dell'intestazione è un middleware di ASP.NET Core per propagare le intestazioni HTTP dalla richiesta in ingresso alle richieste del client HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="a80b9-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="a80b9-317">Per utilizzare la propagazione dell'intestazione:</span><span class="sxs-lookup"><span data-stu-id="a80b9-317">To use header propagation:</span></span>

* <span data-ttu-id="a80b9-318">Fare riferimento al pacchetto [Microsoft.AspNetCore.HeaderPropagation.](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)</span><span class="sxs-lookup"><span data-stu-id="a80b9-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="a80b9-319">Configurare il `HttpClient` middleware e in: `Startup`</span><span class="sxs-lookup"><span data-stu-id="a80b9-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="a80b9-320">Il client include le intestazioni configurate nelle richieste in uscita:The client includes the configured headers on outbound requests:</span><span class="sxs-lookup"><span data-stu-id="a80b9-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="a80b9-321">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a80b9-321">Additional resources</span></span>

* [<span data-ttu-id="a80b9-322">Usare HttpClientFactory per l'implementazione di richieste HTTP resilienti</span><span class="sxs-lookup"><span data-stu-id="a80b9-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="a80b9-323">Implementazione dei tentativi di chiamate HTTP con backoff esponenziale con i criteri di Polly e HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="a80b9-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="a80b9-324">Implementazione dello schema Circuit Breaker</span><span class="sxs-lookup"><span data-stu-id="a80b9-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="a80b9-325">Come serializzare e deserializzare JSON in .NET</span><span class="sxs-lookup"><span data-stu-id="a80b9-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="a80b9-326">Di [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="a80b9-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="a80b9-327">È possibile registrare e usare un'interfaccia <xref:System.Net.Http.IHttpClientFactory> per configurare e creare istanze di <xref:System.Net.Http.HttpClient> in un'app.</span><span class="sxs-lookup"><span data-stu-id="a80b9-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="a80b9-328">I vantaggi offerti sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="a80b9-328">It offers the following benefits:</span></span>

* <span data-ttu-id="a80b9-329">Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche.</span><span class="sxs-lookup"><span data-stu-id="a80b9-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="a80b9-330">Ad esempio, è possibile registrare e configurare un client *github* per accedere a [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="a80b9-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="a80b9-331">È possibile registrare un client predefinito per altri scopi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="a80b9-332">Codifica il concetto di middleware in uscita tramite la delega di gestori in `HttpClient` e offre estensioni per il middleware basato su Polly per sfruttarne i vantaggi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="a80b9-333">Gestisce il pooling e la durata delle istanze di `HttpClientMessageHandler` sottostanti per evitare problemi DNS comuni che si verificano quando le durate di `HttpClient` vengono gestite manualmente.</span><span class="sxs-lookup"><span data-stu-id="a80b9-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="a80b9-334">Aggiunge un'esperienza di registrazione configurabile, tramite `ILogger`, per tutte le richieste inviate attraverso i client creati dalla factory.</span><span class="sxs-lookup"><span data-stu-id="a80b9-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="a80b9-335">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a80b9-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="a80b9-336">Modelli di consumo</span><span class="sxs-lookup"><span data-stu-id="a80b9-336">Consumption patterns</span></span>

<span data-ttu-id="a80b9-337">`IHttpClientFactory` può essere usato in un'app in diversi modi:</span><span class="sxs-lookup"><span data-stu-id="a80b9-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="a80b9-338">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="a80b9-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="a80b9-339">Client denominati</span><span class="sxs-lookup"><span data-stu-id="a80b9-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="a80b9-340">Client tipizzato</span><span class="sxs-lookup"><span data-stu-id="a80b9-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="a80b9-341">Client generati</span><span class="sxs-lookup"><span data-stu-id="a80b9-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="a80b9-342">Nessuno di questi modi può essere considerato superiore a un altro.</span><span class="sxs-lookup"><span data-stu-id="a80b9-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="a80b9-343">L'approccio migliore dipende dai vincoli dell'app.</span><span class="sxs-lookup"><span data-stu-id="a80b9-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="a80b9-344">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="a80b9-344">Basic usage</span></span>

<span data-ttu-id="a80b9-345">È possibile registrare `IHttpClientFactory` chiamando il metodo di estensione `AddHttpClient` in `IServiceCollection`, all'interno del metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="a80b9-346">Dopo la registrazione, il codice può accettare un'interfaccia `IHttpClientFactory` ovunque sia possibile inserire servizi con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a80b9-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a80b9-347">L'oggetto `IHttpClientFactory` può essere `HttpClient` utilizzato per creare un'istanza:The can be used to create an instance:</span><span class="sxs-lookup"><span data-stu-id="a80b9-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="a80b9-348">Questo uso di `IHttpClientFactory` è un modo efficace per effettuare il refactoring di un'app esistente.</span><span class="sxs-lookup"><span data-stu-id="a80b9-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="a80b9-349">Non influisce in alcun modo sulle modalità d'uso di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="a80b9-350">Nelle posizioni in cui vengono attualmente create istanze di `HttpClient`, sostituire le occorrenze con una chiamata a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="a80b9-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="a80b9-351">Client denominati</span><span class="sxs-lookup"><span data-stu-id="a80b9-351">Named clients</span></span>

<span data-ttu-id="a80b9-352">Se un'app richiede molti usi distinti di `HttpClient`, ognuno con una configurazione diversa, un'opzione è l'uso di **client denominati**.</span><span class="sxs-lookup"><span data-stu-id="a80b9-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="a80b9-353">La configurazione di un `HttpClient` denominato può essere specificata durante la registrazione in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="a80b9-354">Nel codice precedente viene chiamato `AddHttpClient`, in cui viene specificato il nome *github*.</span><span class="sxs-lookup"><span data-stu-id="a80b9-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="a80b9-355">Al client viene applicata una configurazione predefinita, ovvero l'indirizzo di base e due intestazioni necessari per l'uso dell'API GitHub.</span><span class="sxs-lookup"><span data-stu-id="a80b9-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="a80b9-356">Ogni volta che `CreateClient` viene chiamato, verrà creata una nuova istanza di `HttpClient` e verrà chiamata l'azione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="a80b9-357">Per usare un client denominato, è possibile passare un parametro di stringa a `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="a80b9-358">Specificare il nome del client da creare:</span><span class="sxs-lookup"><span data-stu-id="a80b9-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="a80b9-359">Nel codice precedente non è necessario che la richiesta specifichi un nome host.</span><span class="sxs-lookup"><span data-stu-id="a80b9-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="a80b9-360">Poiché viene usato l'indirizzo di base configurato per il client, è possibile passare solo il percorso.</span><span class="sxs-lookup"><span data-stu-id="a80b9-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="a80b9-361">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="a80b9-361">Typed clients</span></span>

<span data-ttu-id="a80b9-362">Client tipizzati:</span><span class="sxs-lookup"><span data-stu-id="a80b9-362">Typed clients:</span></span>

* <span data-ttu-id="a80b9-363">Offrono le stesse funzionalità dei client denominati senza la necessità di usare le stringhe come chiavi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="a80b9-364">Offrono l'aiuto di IntelliSense e del compilatore quando si usano i client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="a80b9-365">La configurazione e l'interazione con un particolare `HttpClient` può avvenire in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="a80b9-366">Per un singolo endpoint back-end è ad esempio possibile usare un unico client tipizzato che incapsuli tutta la logica relativa all'endpoint.</span><span class="sxs-lookup"><span data-stu-id="a80b9-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="a80b9-367">Usano l'inserimento di dipendenze e possono essere inseriti dove necessario nell'app.</span><span class="sxs-lookup"><span data-stu-id="a80b9-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="a80b9-368">Un client tipizzato `HttpClient` accetta un parametro nel relativo costruttore:A typed client accepts an parameter in its constructor:</span><span class="sxs-lookup"><span data-stu-id="a80b9-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="a80b9-369">Nel codice precedente la configurazione viene spostata nel client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="a80b9-370">L'oggetto `HttpClient` viene esposto come una proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="a80b9-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="a80b9-371">È possibile definire metodi di API specifiche che espongono la funzionalità `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="a80b9-372">Il metodo `GetAspNetDocsIssues` incapsula il codice necessario per eseguire una query e analizzare gli ultimi problemi aperti in un repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="a80b9-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="a80b9-373">Per registrare un client tipizzato è possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> generico all'interno di `Startup.ConfigureServices`, specificando la classe del client tipizzato:</span><span class="sxs-lookup"><span data-stu-id="a80b9-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="a80b9-374">Il client tipizzato viene registrato come temporaneo nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="a80b9-375">Il client tipizzato può essere inserito e usato direttamente:</span><span class="sxs-lookup"><span data-stu-id="a80b9-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="a80b9-376">Se si preferisce, è possibile specificare la configurazione di un client tipizzato durante la registrazione in `Startup.ConfigureServices`, anziché nel relativo costruttore:</span><span class="sxs-lookup"><span data-stu-id="a80b9-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="a80b9-377">È possibile incapsulare interamente `HttpClient` all'interno di un client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="a80b9-378">Anziché esporlo come una proprietà, è possibile specificare metodi pubblici che chiamano l'istanza di `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="a80b9-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="a80b9-379">Nel codice precedente `HttpClient` viene archiviato come un campo privato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="a80b9-380">L'accesso per effettuare chiamate esterne passa attraverso il metodo `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="a80b9-381">Client generati</span><span class="sxs-lookup"><span data-stu-id="a80b9-381">Generated clients</span></span>

<span data-ttu-id="a80b9-382">È possibile usare `IHttpClientFactory` in combinazione con altre librerie di terze parti, ad esempio [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="a80b9-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="a80b9-383">Refit è una libreria REST per .NET.</span><span class="sxs-lookup"><span data-stu-id="a80b9-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="a80b9-384">Converte le API REST in interfacce live.</span><span class="sxs-lookup"><span data-stu-id="a80b9-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="a80b9-385">Un'implementazione dell'interfaccia viene generata dinamicamente da `RestService`, usando `HttpClient` per effettuare le chiamate HTTP esterne.</span><span class="sxs-lookup"><span data-stu-id="a80b9-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="a80b9-386">Per rappresentare l'API esterna e la relativa risposta vengono definite un'interfaccia e una risposta:</span><span class="sxs-lookup"><span data-stu-id="a80b9-386">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="a80b9-387">È possibile aggiungere un client tipizzato usando Refit per generare l'implementazione:</span><span class="sxs-lookup"><span data-stu-id="a80b9-387">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="a80b9-388">L'interfaccia definita può essere usata dove necessario, con l'implementazione offerta dall'inserimento di dipendenze e da Refit:</span><span class="sxs-lookup"><span data-stu-id="a80b9-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="a80b9-389">Middleware per richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="a80b9-389">Outgoing request middleware</span></span>

<span data-ttu-id="a80b9-390">`HttpClient` include già il concetto di delega di gestori concatenati per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="a80b9-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="a80b9-391">`IHttpClientFactory` semplifica la definizione dei gestori da applicare per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="a80b9-392">Supporta la registrazione e il concatenamento di più gestori per creare una pipeline di middleware per le richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="a80b9-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="a80b9-393">Ognuno di questi gestori è in grado di eseguire operazioni prima e dopo la richiesta in uscita.</span><span class="sxs-lookup"><span data-stu-id="a80b9-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="a80b9-394">Questo modello è simile alla pipeline di middleware in ingresso in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a80b9-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="a80b9-395">Il modello offre un meccanismo per gestire le problematiche trasversali relative alle richieste HTTP, tra cui memorizzazione nella cache, gestione degli errori, serializzazione e registrazione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="a80b9-396">Per creare un gestore, definire una classe che deriva da <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="a80b9-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="a80b9-397">Eseguire l'override del metodo `SendAsync` per eseguire il codice prima di passare la richiesta al gestore successivo nella pipeline:</span><span class="sxs-lookup"><span data-stu-id="a80b9-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="a80b9-398">Il codice precedente definisce un gestore di base.</span><span class="sxs-lookup"><span data-stu-id="a80b9-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="a80b9-399">Verifica se un'intestazione `X-API-KEY` è stata inclusa nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="a80b9-400">Se l'intestazione non è presente, può evitare la chiamata HTTP e restituire una risposta appropriata.</span><span class="sxs-lookup"><span data-stu-id="a80b9-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="a80b9-401">Durante la registrazione, è possibile aggiungere uno o `HttpClient`più gestori alla configurazione per un oggetto .</span><span class="sxs-lookup"><span data-stu-id="a80b9-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="a80b9-402">Questa attività viene eseguita tramite metodi di estensione in <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a80b9-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="a80b9-403">Nel codice precedente `ValidateHeaderHandler` viene registrato nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="a80b9-404">L'interfaccia `IHttpClientFactory` crea un ambito di inserimento delle dipendenze separato per ogni gestore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="a80b9-405">I gestori possono dipendere da servizi di qualsiasi ambito.</span><span class="sxs-lookup"><span data-stu-id="a80b9-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="a80b9-406">I servizi da cui dipendono i gestori vengono eliminati al momento dell'eliminazione del gestore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="a80b9-407">Dopo la registrazione è possibile chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, passando il tipo di gestore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="a80b9-408">È possibile registrare più gestori nell'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="a80b9-409">Ogni gestore esegue il wrapping del gestore successivo fino a quando l'elemento `HttpClientHandler` finale non esegue la richiesta:</span><span class="sxs-lookup"><span data-stu-id="a80b9-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="a80b9-410">Usare uno degli approcci seguenti per condividere lo stato in base alla richiesta con i gestori di messaggi:</span><span class="sxs-lookup"><span data-stu-id="a80b9-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="a80b9-411">Passare i dati al gestore usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="a80b9-412">Usare `IHttpContextAccessor` per accedere alla richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="a80b9-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="a80b9-413">Creare un oggetto di archiviazione `AsyncLocal` personalizzato per passare i dati.</span><span class="sxs-lookup"><span data-stu-id="a80b9-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="a80b9-414">Usare gestori basati su Polly</span><span class="sxs-lookup"><span data-stu-id="a80b9-414">Use Polly-based handlers</span></span>

<span data-ttu-id="a80b9-415">`IHttpClientFactory` si integra con una libreria di terze parti piuttosto diffusa denominata [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="a80b9-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="a80b9-416">Polly è una libreria di gestione degli errori temporanei e di resilienza completa per .NET.</span><span class="sxs-lookup"><span data-stu-id="a80b9-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="a80b9-417">Consente agli sviluppatori di esprimere criteri quali Retry, Circuit Breaker, Timeout, Bulkhead Isolation e Fallback in modo fluido e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="a80b9-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="a80b9-418">Per consentire l'uso dei criteri Polly con le istanze configurate di `HttpClient` sono disponibili metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="a80b9-419">Le estensioni Polly:</span><span class="sxs-lookup"><span data-stu-id="a80b9-419">The Polly extensions:</span></span>

* <span data-ttu-id="a80b9-420">Supportano l'aggiunta di gestori basati su Polly ai client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="a80b9-421">Possono essere usate dopo l'installazione del pacchetto NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="a80b9-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="a80b9-422">Il pacchetto non è incluso nel framework condiviso ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a80b9-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="a80b9-423">Gestire gli errori temporanei</span><span class="sxs-lookup"><span data-stu-id="a80b9-423">Handle transient faults</span></span>

<span data-ttu-id="a80b9-424">La maggior parte degli errori comuni si verifica quando le chiamate HTTP esterne sono temporanee.</span><span class="sxs-lookup"><span data-stu-id="a80b9-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="a80b9-425">Per definire un criterio in grado di gestire gli errori temporanei è disponibile un pratico metodo di estensione denominato `AddTransientHttpErrorPolicy`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="a80b9-426">I criteri configurati con questo metodo di estensione gestiscono `HttpRequestException`, risposte HTTP 5xx e risposte HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="a80b9-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="a80b9-427">L'estensione `AddTransientHttpErrorPolicy` può essere usata all'interno di `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a80b9-428">L'estensione consente l'accesso a un oggetto `PolicyBuilder` configurato per gestire gli errori che rappresentano un possibile errore temporaneo:</span><span class="sxs-lookup"><span data-stu-id="a80b9-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="a80b9-429">Nel codice precedente viene definito un criterio `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="a80b9-430">Le richieste non riuscite vengono ritentate fino a tre volte con un ritardo di 600 millisecondi tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="a80b9-431">Selezionare i criteri in modo dinamico</span><span class="sxs-lookup"><span data-stu-id="a80b9-431">Dynamically select policies</span></span>

<span data-ttu-id="a80b9-432">Per aggiungere gestori basati su Polly è possibile usare altri metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="a80b9-433">Una di queste estensioni è `AddPolicyHandler`, che include più overload.</span><span class="sxs-lookup"><span data-stu-id="a80b9-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="a80b9-434">Un overload consente l'ispezione della richiesta al momento della definizione del criterio da applicare:</span><span class="sxs-lookup"><span data-stu-id="a80b9-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="a80b9-435">Nel codice precedente, se la richiesta in uscita è una richiesta HTTP GET viene applicato un timeout di 10 secondi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="a80b9-436">Per qualsiasi altro metodo HTTP viene usato un timeout di 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="a80b9-437">Aggiungere più gestori Polly</span><span class="sxs-lookup"><span data-stu-id="a80b9-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="a80b9-438">In molti casi i criteri Polly vengono annidati per offrire funzionalità avanzate:</span><span class="sxs-lookup"><span data-stu-id="a80b9-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="a80b9-439">Nell'esempio precedente vengono aggiunti due gestori.</span><span class="sxs-lookup"><span data-stu-id="a80b9-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="a80b9-440">Il primo usa l'estensione `AddTransientHttpErrorPolicy` per aggiungere criteri di ripetizione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="a80b9-441">Le richieste non riuscite vengono ritentate fino a tre volte.</span><span class="sxs-lookup"><span data-stu-id="a80b9-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="a80b9-442">La seconda chiamata a `AddTransientHttpErrorPolicy` aggiunge criteri dell'interruttore di circuito.</span><span class="sxs-lookup"><span data-stu-id="a80b9-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="a80b9-443">Ulteriori richieste esterne vengono bloccate per 30 secondi nel caso si verifichino cinque tentativi non riusciti consecutivi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="a80b9-444">I criteri dell'interruttore di circuito sono con stato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="a80b9-445">Tutte le chiamate tramite questo client condividono lo stesso stato di circuito.</span><span class="sxs-lookup"><span data-stu-id="a80b9-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="a80b9-446">Aggiungere criteri dal registro Polly</span><span class="sxs-lookup"><span data-stu-id="a80b9-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="a80b9-447">Un approccio alla gestione dei criteri usati di frequente consiste nel definirli una volta e registrarli in un elemento `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="a80b9-448">Per aggiungere un gestore usando un criterio del registro è disponibile un metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="a80b9-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="a80b9-449">Nel codice precedente vengono registrati due criteri quando si aggiunge `PolicyRegistry` a `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="a80b9-450">Per usare un criterio dal registro viene usato il metodo `AddPolicyHandlerFromRegistry` passando il nome del criterio da applicare.</span><span class="sxs-lookup"><span data-stu-id="a80b9-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="a80b9-451">Altre informazioni su `IHttpClientFactory` e le integrazioni con Polly sono disponibili nel [wiki di Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="a80b9-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="a80b9-452">Gestione di HttpClient e durata</span><span class="sxs-lookup"><span data-stu-id="a80b9-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="a80b9-453">Viene restituita una nuova istanza di `HttpClient` per ogni chiamata di `CreateClient` per `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="a80b9-454">È presente un <xref:System.Net.Http.HttpMessageHandler> per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="a80b9-455">La factory gestisce le durate delle istanze di `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="a80b9-456">`IHttpClientFactory` esegue il pooling delle istanze di `HttpMessageHandler` create dalla factory per ridurre il consumo di risorse.</span><span class="sxs-lookup"><span data-stu-id="a80b9-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="a80b9-457">Un'istanza di `HttpMessageHandler` può essere riusata dal pool quando si crea una nuova istanza di `HttpClient` se la relativa durata non è scaduta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="a80b9-458">Il pooling dei gestori è consigliabile in quanto ogni gestore gestisce in genere le proprie connessioni HTTP sottostanti.</span><span class="sxs-lookup"><span data-stu-id="a80b9-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="a80b9-459">La creazione di più gestori del necessario può causare ritardi di connessione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="a80b9-460">Alcuni gestori mantengono inoltre le connessioni aperte a tempo indefinito. Ciò può impedire al gestore di reagire alle modifiche DNS.</span><span class="sxs-lookup"><span data-stu-id="a80b9-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="a80b9-461">La durata del gestore predefinito è di due minuti.</span><span class="sxs-lookup"><span data-stu-id="a80b9-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="a80b9-462">Il valore predefinito può essere sottoposto a override per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="a80b9-463">Per eseguire l'override, chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> nell'elemento `IHttpClientBuilder` restituito al momento della creazione del client:</span><span class="sxs-lookup"><span data-stu-id="a80b9-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="a80b9-464">L'eliminazione del client non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="a80b9-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="a80b9-465">L'eliminazione annulla le richieste in uscita e garantisce che l'istanza di `HttpClient` specificata non possa essere usata dopo la chiamata a <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="a80b9-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="a80b9-466">`IHttpClientFactory` tiene traccia ed elimina le risorse usate dalle istanze di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="a80b9-467">Le istanze di `HttpClient` possono essere considerate a livello generale come oggetti .NET che non richiedono l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="a80b9-468">Mantenere una sola istanza di `HttpClient` attiva per un lungo periodo di tempo è un modello comune usato prima dell'avvento di `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="a80b9-469">Questo modello non è più necessario dopo la migrazione a `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="a80b9-470">Alternative a IHttpClientFactoryAlternatives to IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="a80b9-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="a80b9-471">L'uso in un'app abilitata per DI consente di evitare:Using `IHttpClientFactory` in a DI-enabled app avoids:</span><span class="sxs-lookup"><span data-stu-id="a80b9-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="a80b9-472">Problemi di esaurimento delle `HttpMessageHandler` risorse mediante il pooling delle istanze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="a80b9-473">Problemi DNS non `HttpMessageHandler` aggiornati causati dalle istanze di ciclismo a intervalli regolari.</span><span class="sxs-lookup"><span data-stu-id="a80b9-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="a80b9-474">Esistono modi alternativi per risolvere i problemi <xref:System.Net.Http.SocketsHttpHandler> precedenti utilizzando un'istanza di lunga durata.</span><span class="sxs-lookup"><span data-stu-id="a80b9-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="a80b9-475">Crea un'istanza di `SocketsHttpHandler` quando l'app viene avviata e usala per tutta la vita dell'app.</span><span class="sxs-lookup"><span data-stu-id="a80b9-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="a80b9-476">Configurare <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> un valore appropriato in base ai tempi di aggiornamento DNS.</span><span class="sxs-lookup"><span data-stu-id="a80b9-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="a80b9-477">Creare `HttpClient` istanze in `new HttpClient(handler, disposeHandler: false)` base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="a80b9-478">Gli approcci precedenti risolvono `IHttpClientFactory` i problemi di gestione delle risorse che risolvono in modo simile.</span><span class="sxs-lookup"><span data-stu-id="a80b9-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="a80b9-479">Le `SocketsHttpHandler` connessioni `HttpClient` azionarie tra le istanze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="a80b9-480">Questa condivisione impedisce l'esaurimento dei socket.</span><span class="sxs-lookup"><span data-stu-id="a80b9-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="a80b9-481">Le `SocketsHttpHandler` connessioni cicli `PooledConnectionLifetime` in base a per evitare problemi DNS non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="a80b9-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="a80b9-482">Cookie</span><span class="sxs-lookup"><span data-stu-id="a80b9-482">Cookies</span></span>

<span data-ttu-id="a80b9-483">Le istanze `HttpMessageHandler` in `CookieContainer` pool determinano la condivisione degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="a80b9-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="a80b9-484">La `CookieContainer` condivisione imprevista degli oggetti spesso genera codice non corretto.</span><span class="sxs-lookup"><span data-stu-id="a80b9-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="a80b9-485">Per le app che richiedono cookie, considerare:</span><span class="sxs-lookup"><span data-stu-id="a80b9-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="a80b9-486">Disattivazione della gestione automatica dei cookie</span><span class="sxs-lookup"><span data-stu-id="a80b9-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="a80b9-487">Evitando`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="a80b9-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="a80b9-488">Chiamata <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per disabilitare la gestione automatica dei cookie:</span><span class="sxs-lookup"><span data-stu-id="a80b9-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="a80b9-489">Registrazione</span><span class="sxs-lookup"><span data-stu-id="a80b9-489">Logging</span></span>

<span data-ttu-id="a80b9-490">I client creati tramite `IHttpClientFactory` registrano i messaggi di log per tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="a80b9-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="a80b9-491">Per visualizzare i messaggi di log predefiniti, abilitare il livello di informazioni appropriato nella configurazione di registrazione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="a80b9-492">La registrazione aggiuntiva, ad esempio quella delle intestazioni delle richieste, è inclusa solo a livello di traccia.</span><span class="sxs-lookup"><span data-stu-id="a80b9-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="a80b9-493">La categoria di log usata per ogni client include il nome del client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="a80b9-494">Un client denominato *MyNamedClient*, ad esempio, registra i messaggi con una categoria `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="a80b9-495">I messaggi con suffisso *LogicalHandler* sono esterni alla pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="a80b9-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="a80b9-496">Nella richiesta i messaggi vengono registrati prima che qualsiasi altro gestore nella pipeline l'abbia elaborata.</span><span class="sxs-lookup"><span data-stu-id="a80b9-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="a80b9-497">Nella risposta i messaggi vengono registrati dopo che qualsiasi altro gestore nella pipeline ha ricevuto la risposta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="a80b9-498">La registrazione avviene anche all'interno della pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="a80b9-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="a80b9-499">Nell'esempio *MyNamedClient*, i messaggi vengono registrati nella categoria di log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="a80b9-500">Per la richiesta, ciò avviene dopo l'esecuzione di tutti gli altri gestori e immediatamente prima che la richiesta sia inviata in rete.</span><span class="sxs-lookup"><span data-stu-id="a80b9-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="a80b9-501">Nella risposta la registrazione include lo stato della risposta prima di restituirla attraverso la pipeline del gestore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="a80b9-502">L'abilitazione della registrazione all'esterno e all'interno della pipeline consente l'ispezione delle modifiche apportate da altri gestori nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="a80b9-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="a80b9-503">Le modifiche possono ad esempio riguardare le intestazioni delle richieste o il codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="a80b9-504">L'inclusione del nome del client nella categoria di log consente di filtrare i log in base a client denominati specifici, se necessario.</span><span class="sxs-lookup"><span data-stu-id="a80b9-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="a80b9-505">Configurare HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="a80b9-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="a80b9-506">Può essere necessario controllare la configurazione dell'elemento `HttpMessageHandler` interno usato da un client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="a80b9-507">Quando si aggiungono client denominati o tipizzati viene restituito un elemento `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="a80b9-508">È possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per definire un delegato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="a80b9-509">Il delegato viene usato per creare e configurare l'elemento `HttpMessageHandler` primario usato dal client:</span><span class="sxs-lookup"><span data-stu-id="a80b9-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="a80b9-510">Usare IHttpClientFactory in un'app console</span><span class="sxs-lookup"><span data-stu-id="a80b9-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="a80b9-511">In un'app console aggiungere al progetto i riferimenti ai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="a80b9-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="a80b9-512">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="a80b9-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="a80b9-513">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="a80b9-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="a80b9-514">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="a80b9-514">In the following example:</span></span>

* <span data-ttu-id="a80b9-515"><xref:System.Net.Http.IHttpClientFactory> è registrato nel contenitore di servizi dell'[host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="a80b9-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="a80b9-516">`MyService` crea un'istanza della factory client dal servizio, che viene usata per creare una classe `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="a80b9-517">`HttpClient` viene usato per recuperare una pagina Web.</span><span class="sxs-lookup"><span data-stu-id="a80b9-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="a80b9-518">`Main` crea un ambito per eseguire il metodo `GetPage` del servizio e scrivere i primi 500 caratteri del contenuto della pagina Web nella console.</span><span class="sxs-lookup"><span data-stu-id="a80b9-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="a80b9-519">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a80b9-519">Additional resources</span></span>

* [<span data-ttu-id="a80b9-520">Usare HttpClientFactory per l'implementazione di richieste HTTP resilienti</span><span class="sxs-lookup"><span data-stu-id="a80b9-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="a80b9-521">Implementazione dei tentativi di chiamate HTTP con backoff esponenziale con i criteri di Polly e HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="a80b9-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="a80b9-522">Implementazione dello schema Circuit Breaker</span><span class="sxs-lookup"><span data-stu-id="a80b9-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="a80b9-523">Di [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="a80b9-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="a80b9-524">È possibile registrare e usare un'interfaccia <xref:System.Net.Http.IHttpClientFactory> per configurare e creare istanze di <xref:System.Net.Http.HttpClient> in un'app.</span><span class="sxs-lookup"><span data-stu-id="a80b9-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="a80b9-525">I vantaggi offerti sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="a80b9-525">It offers the following benefits:</span></span>

* <span data-ttu-id="a80b9-526">Offre una posizione centrale per la denominazione e la configurazione di istanze di `HttpClient` logiche.</span><span class="sxs-lookup"><span data-stu-id="a80b9-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="a80b9-527">Ad esempio, è possibile registrare e configurare un client *github* per accedere a [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="a80b9-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="a80b9-528">È possibile registrare un client predefinito per altri scopi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="a80b9-529">Codifica il concetto di middleware in uscita tramite la delega di gestori in `HttpClient` e offre estensioni per il middleware basato su Polly per sfruttarne i vantaggi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="a80b9-530">Gestisce il pooling e la durata delle istanze di `HttpClientMessageHandler` sottostanti per evitare problemi DNS comuni che si verificano quando le durate di `HttpClient` vengono gestite manualmente.</span><span class="sxs-lookup"><span data-stu-id="a80b9-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="a80b9-531">Aggiunge un'esperienza di registrazione configurabile, tramite `ILogger`, per tutte le richieste inviate attraverso i client creati dalla factory.</span><span class="sxs-lookup"><span data-stu-id="a80b9-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="a80b9-532">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a80b9-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a80b9-533">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="a80b9-533">Prerequisites</span></span>

<span data-ttu-id="a80b9-534">I progetti destinati a .NET Framework richiedono l'installazione del pacchetto NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="a80b9-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="a80b9-535">I progetti destinati a .NET Core che fanno riferimento al [metapacchetto Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app) sono già inclusi nel pacchetto `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="a80b9-536">Modelli di consumo</span><span class="sxs-lookup"><span data-stu-id="a80b9-536">Consumption patterns</span></span>

<span data-ttu-id="a80b9-537">`IHttpClientFactory` può essere usato in un'app in diversi modi:</span><span class="sxs-lookup"><span data-stu-id="a80b9-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="a80b9-538">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="a80b9-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="a80b9-539">Client denominati</span><span class="sxs-lookup"><span data-stu-id="a80b9-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="a80b9-540">Client tipizzato</span><span class="sxs-lookup"><span data-stu-id="a80b9-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="a80b9-541">Client generati</span><span class="sxs-lookup"><span data-stu-id="a80b9-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="a80b9-542">Nessuno di questi modi può essere considerato superiore a un altro.</span><span class="sxs-lookup"><span data-stu-id="a80b9-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="a80b9-543">L'approccio migliore dipende dai vincoli dell'app.</span><span class="sxs-lookup"><span data-stu-id="a80b9-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="a80b9-544">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="a80b9-544">Basic usage</span></span>

<span data-ttu-id="a80b9-545">È possibile registrare `IHttpClientFactory` chiamando il metodo di estensione `AddHttpClient` in `IServiceCollection`, all'interno del metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="a80b9-546">Dopo la registrazione, il codice può accettare un'interfaccia `IHttpClientFactory` ovunque sia possibile inserire servizi con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a80b9-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a80b9-547">L'oggetto `IHttpClientFactory` può essere `HttpClient` utilizzato per creare un'istanza:The can be used to create an instance:</span><span class="sxs-lookup"><span data-stu-id="a80b9-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="a80b9-548">Questo uso di `IHttpClientFactory` è un modo efficace per effettuare il refactoring di un'app esistente.</span><span class="sxs-lookup"><span data-stu-id="a80b9-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="a80b9-549">Non influisce in alcun modo sulle modalità d'uso di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="a80b9-550">Nelle posizioni in cui vengono attualmente create istanze di `HttpClient`, sostituire le occorrenze con una chiamata a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="a80b9-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="a80b9-551">Client denominati</span><span class="sxs-lookup"><span data-stu-id="a80b9-551">Named clients</span></span>

<span data-ttu-id="a80b9-552">Se un'app richiede molti usi distinti di `HttpClient`, ognuno con una configurazione diversa, un'opzione è l'uso di **client denominati**.</span><span class="sxs-lookup"><span data-stu-id="a80b9-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="a80b9-553">La configurazione di un `HttpClient` denominato può essere specificata durante la registrazione in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="a80b9-554">Nel codice precedente viene chiamato `AddHttpClient`, in cui viene specificato il nome *github*.</span><span class="sxs-lookup"><span data-stu-id="a80b9-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="a80b9-555">Al client viene applicata una configurazione predefinita, ovvero l'indirizzo di base e due intestazioni necessari per l'uso dell'API GitHub.</span><span class="sxs-lookup"><span data-stu-id="a80b9-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="a80b9-556">Ogni volta che `CreateClient` viene chiamato, verrà creata una nuova istanza di `HttpClient` e verrà chiamata l'azione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="a80b9-557">Per usare un client denominato, è possibile passare un parametro di stringa a `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="a80b9-558">Specificare il nome del client da creare:</span><span class="sxs-lookup"><span data-stu-id="a80b9-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="a80b9-559">Nel codice precedente non è necessario che la richiesta specifichi un nome host.</span><span class="sxs-lookup"><span data-stu-id="a80b9-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="a80b9-560">Poiché viene usato l'indirizzo di base configurato per il client, è possibile passare solo il percorso.</span><span class="sxs-lookup"><span data-stu-id="a80b9-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="a80b9-561">Client tipizzati</span><span class="sxs-lookup"><span data-stu-id="a80b9-561">Typed clients</span></span>

<span data-ttu-id="a80b9-562">Client tipizzati:</span><span class="sxs-lookup"><span data-stu-id="a80b9-562">Typed clients:</span></span>

* <span data-ttu-id="a80b9-563">Offrono le stesse funzionalità dei client denominati senza la necessità di usare le stringhe come chiavi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="a80b9-564">Offrono l'aiuto di IntelliSense e del compilatore quando si usano i client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="a80b9-565">La configurazione e l'interazione con un particolare `HttpClient` può avvenire in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="a80b9-566">Per un singolo endpoint back-end è ad esempio possibile usare un unico client tipizzato che incapsuli tutta la logica relativa all'endpoint.</span><span class="sxs-lookup"><span data-stu-id="a80b9-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="a80b9-567">Usano l'inserimento di dipendenze e possono essere inseriti dove necessario nell'app.</span><span class="sxs-lookup"><span data-stu-id="a80b9-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="a80b9-568">Un client tipizzato `HttpClient` accetta un parametro nel relativo costruttore:A typed client accepts an parameter in its constructor:</span><span class="sxs-lookup"><span data-stu-id="a80b9-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="a80b9-569">Nel codice precedente la configurazione viene spostata nel client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="a80b9-570">L'oggetto `HttpClient` viene esposto come una proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="a80b9-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="a80b9-571">È possibile definire metodi di API specifiche che espongono la funzionalità `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="a80b9-572">Il metodo `GetAspNetDocsIssues` incapsula il codice necessario per eseguire una query e analizzare gli ultimi problemi aperti in un repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="a80b9-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="a80b9-573">Per registrare un client tipizzato è possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> generico all'interno di `Startup.ConfigureServices`, specificando la classe del client tipizzato:</span><span class="sxs-lookup"><span data-stu-id="a80b9-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="a80b9-574">Il client tipizzato viene registrato come temporaneo nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="a80b9-575">Il client tipizzato può essere inserito e usato direttamente:</span><span class="sxs-lookup"><span data-stu-id="a80b9-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="a80b9-576">Se si preferisce, è possibile specificare la configurazione di un client tipizzato durante la registrazione in `Startup.ConfigureServices`, anziché nel relativo costruttore:</span><span class="sxs-lookup"><span data-stu-id="a80b9-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="a80b9-577">È possibile incapsulare interamente `HttpClient` all'interno di un client tipizzato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="a80b9-578">Anziché esporlo come una proprietà, è possibile specificare metodi pubblici che chiamano l'istanza di `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="a80b9-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="a80b9-579">Nel codice precedente `HttpClient` viene archiviato come un campo privato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="a80b9-580">L'accesso per effettuare chiamate esterne passa attraverso il metodo `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="a80b9-581">Client generati</span><span class="sxs-lookup"><span data-stu-id="a80b9-581">Generated clients</span></span>

<span data-ttu-id="a80b9-582">È possibile usare `IHttpClientFactory` in combinazione con altre librerie di terze parti, ad esempio [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="a80b9-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="a80b9-583">Refit è una libreria REST per .NET.</span><span class="sxs-lookup"><span data-stu-id="a80b9-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="a80b9-584">Converte le API REST in interfacce live.</span><span class="sxs-lookup"><span data-stu-id="a80b9-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="a80b9-585">Un'implementazione dell'interfaccia viene generata dinamicamente da `RestService`, usando `HttpClient` per effettuare le chiamate HTTP esterne.</span><span class="sxs-lookup"><span data-stu-id="a80b9-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="a80b9-586">Per rappresentare l'API esterna e la relativa risposta vengono definite un'interfaccia e una risposta:</span><span class="sxs-lookup"><span data-stu-id="a80b9-586">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="a80b9-587">È possibile aggiungere un client tipizzato usando Refit per generare l'implementazione:</span><span class="sxs-lookup"><span data-stu-id="a80b9-587">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="a80b9-588">L'interfaccia definita può essere usata dove necessario, con l'implementazione offerta dall'inserimento di dipendenze e da Refit:</span><span class="sxs-lookup"><span data-stu-id="a80b9-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="a80b9-589">Middleware per richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="a80b9-589">Outgoing request middleware</span></span>

<span data-ttu-id="a80b9-590">`HttpClient` include già il concetto di delega di gestori concatenati per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="a80b9-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="a80b9-591">`IHttpClientFactory` semplifica la definizione dei gestori da applicare per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="a80b9-592">Supporta la registrazione e il concatenamento di più gestori per creare una pipeline di middleware per le richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="a80b9-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="a80b9-593">Ognuno di questi gestori è in grado di eseguire operazioni prima e dopo la richiesta in uscita.</span><span class="sxs-lookup"><span data-stu-id="a80b9-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="a80b9-594">Questo modello è simile alla pipeline di middleware in ingresso in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a80b9-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="a80b9-595">Il modello offre un meccanismo per gestire le problematiche trasversali relative alle richieste HTTP, tra cui memorizzazione nella cache, gestione degli errori, serializzazione e registrazione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="a80b9-596">Per creare un gestore, definire una classe che deriva da <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="a80b9-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="a80b9-597">Eseguire l'override del metodo `SendAsync` per eseguire il codice prima di passare la richiesta al gestore successivo nella pipeline:</span><span class="sxs-lookup"><span data-stu-id="a80b9-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="a80b9-598">Il codice precedente definisce un gestore di base.</span><span class="sxs-lookup"><span data-stu-id="a80b9-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="a80b9-599">Verifica se un'intestazione `X-API-KEY` è stata inclusa nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="a80b9-600">Se l'intestazione non è presente, può evitare la chiamata HTTP e restituire una risposta appropriata.</span><span class="sxs-lookup"><span data-stu-id="a80b9-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="a80b9-601">Durante la registrazione, è possibile aggiungere uno o `HttpClient`più gestori alla configurazione per un oggetto .</span><span class="sxs-lookup"><span data-stu-id="a80b9-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="a80b9-602">Questa attività viene eseguita tramite metodi di estensione in <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a80b9-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="a80b9-603">Nel codice precedente `ValidateHeaderHandler` viene registrato nell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="a80b9-604">Il gestore **deve** essere registrato nell'inserimento di dipendenze come servizio temporaneo, senza definizione di ambito.</span><span class="sxs-lookup"><span data-stu-id="a80b9-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="a80b9-605">Se il gestore viene registrato come un servizio con ambito e i servizi da cui dipende il gestore sono eliminabili:</span><span class="sxs-lookup"><span data-stu-id="a80b9-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="a80b9-606">I servizi del gestore potrebbero essere eliminati prima che il gestore esca dall'ambito.</span><span class="sxs-lookup"><span data-stu-id="a80b9-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="a80b9-607">I servizi del gestore eliminati causano un errore del gestore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="a80b9-608">Dopo la registrazione è possibile chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, passando il tipo di gestore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="a80b9-609">È possibile registrare più gestori nell'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="a80b9-610">Ogni gestore esegue il wrapping del gestore successivo fino a quando l'elemento `HttpClientHandler` finale non esegue la richiesta:</span><span class="sxs-lookup"><span data-stu-id="a80b9-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="a80b9-611">Usare uno degli approcci seguenti per condividere lo stato in base alla richiesta con i gestori di messaggi:</span><span class="sxs-lookup"><span data-stu-id="a80b9-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="a80b9-612">Passare i dati al gestore usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="a80b9-613">Usare `IHttpContextAccessor` per accedere alla richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="a80b9-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="a80b9-614">Creare un oggetto di archiviazione `AsyncLocal` personalizzato per passare i dati.</span><span class="sxs-lookup"><span data-stu-id="a80b9-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="a80b9-615">Usare gestori basati su Polly</span><span class="sxs-lookup"><span data-stu-id="a80b9-615">Use Polly-based handlers</span></span>

<span data-ttu-id="a80b9-616">`IHttpClientFactory` si integra con una libreria di terze parti piuttosto diffusa denominata [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="a80b9-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="a80b9-617">Polly è una libreria di gestione degli errori temporanei e di resilienza completa per .NET.</span><span class="sxs-lookup"><span data-stu-id="a80b9-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="a80b9-618">Consente agli sviluppatori di esprimere criteri quali Retry, Circuit Breaker, Timeout, Bulkhead Isolation e Fallback in modo fluido e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="a80b9-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="a80b9-619">Per consentire l'uso dei criteri Polly con le istanze configurate di `HttpClient` sono disponibili metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="a80b9-620">Le estensioni Polly:</span><span class="sxs-lookup"><span data-stu-id="a80b9-620">The Polly extensions:</span></span>

* <span data-ttu-id="a80b9-621">Supportano l'aggiunta di gestori basati su Polly ai client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="a80b9-622">Possono essere usate dopo l'installazione del pacchetto NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="a80b9-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="a80b9-623">Il pacchetto non è incluso nel framework condiviso ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a80b9-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="a80b9-624">Gestire gli errori temporanei</span><span class="sxs-lookup"><span data-stu-id="a80b9-624">Handle transient faults</span></span>

<span data-ttu-id="a80b9-625">La maggior parte degli errori comuni si verifica quando le chiamate HTTP esterne sono temporanee.</span><span class="sxs-lookup"><span data-stu-id="a80b9-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="a80b9-626">Per definire un criterio in grado di gestire gli errori temporanei è disponibile un pratico metodo di estensione denominato `AddTransientHttpErrorPolicy`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="a80b9-627">I criteri configurati con questo metodo di estensione gestiscono `HttpRequestException`, risposte HTTP 5xx e risposte HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="a80b9-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="a80b9-628">L'estensione `AddTransientHttpErrorPolicy` può essere usata all'interno di `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a80b9-629">L'estensione consente l'accesso a un oggetto `PolicyBuilder` configurato per gestire gli errori che rappresentano un possibile errore temporaneo:</span><span class="sxs-lookup"><span data-stu-id="a80b9-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="a80b9-630">Nel codice precedente viene definito un criterio `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="a80b9-631">Le richieste non riuscite vengono ritentate fino a tre volte con un ritardo di 600 millisecondi tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="a80b9-632">Selezionare i criteri in modo dinamico</span><span class="sxs-lookup"><span data-stu-id="a80b9-632">Dynamically select policies</span></span>

<span data-ttu-id="a80b9-633">Per aggiungere gestori basati su Polly è possibile usare altri metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="a80b9-634">Una di queste estensioni è `AddPolicyHandler`, che include più overload.</span><span class="sxs-lookup"><span data-stu-id="a80b9-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="a80b9-635">Un overload consente l'ispezione della richiesta al momento della definizione del criterio da applicare:</span><span class="sxs-lookup"><span data-stu-id="a80b9-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="a80b9-636">Nel codice precedente, se la richiesta in uscita è una richiesta HTTP GET viene applicato un timeout di 10 secondi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="a80b9-637">Per qualsiasi altro metodo HTTP viene usato un timeout di 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="a80b9-638">Aggiungere più gestori Polly</span><span class="sxs-lookup"><span data-stu-id="a80b9-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="a80b9-639">In molti casi i criteri Polly vengono annidati per offrire funzionalità avanzate:</span><span class="sxs-lookup"><span data-stu-id="a80b9-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="a80b9-640">Nell'esempio precedente vengono aggiunti due gestori.</span><span class="sxs-lookup"><span data-stu-id="a80b9-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="a80b9-641">Il primo usa l'estensione `AddTransientHttpErrorPolicy` per aggiungere criteri di ripetizione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="a80b9-642">Le richieste non riuscite vengono ritentate fino a tre volte.</span><span class="sxs-lookup"><span data-stu-id="a80b9-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="a80b9-643">La seconda chiamata a `AddTransientHttpErrorPolicy` aggiunge criteri dell'interruttore di circuito.</span><span class="sxs-lookup"><span data-stu-id="a80b9-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="a80b9-644">Ulteriori richieste esterne vengono bloccate per 30 secondi nel caso si verifichino cinque tentativi non riusciti consecutivi.</span><span class="sxs-lookup"><span data-stu-id="a80b9-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="a80b9-645">I criteri dell'interruttore di circuito sono con stato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="a80b9-646">Tutte le chiamate tramite questo client condividono lo stesso stato di circuito.</span><span class="sxs-lookup"><span data-stu-id="a80b9-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="a80b9-647">Aggiungere criteri dal registro Polly</span><span class="sxs-lookup"><span data-stu-id="a80b9-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="a80b9-648">Un approccio alla gestione dei criteri usati di frequente consiste nel definirli una volta e registrarli in un elemento `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="a80b9-649">Per aggiungere un gestore usando un criterio del registro è disponibile un metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="a80b9-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="a80b9-650">Nel codice precedente vengono registrati due criteri quando si aggiunge `PolicyRegistry` a `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="a80b9-651">Per usare un criterio dal registro viene usato il metodo `AddPolicyHandlerFromRegistry` passando il nome del criterio da applicare.</span><span class="sxs-lookup"><span data-stu-id="a80b9-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="a80b9-652">Altre informazioni su `IHttpClientFactory` e le integrazioni con Polly sono disponibili nel [wiki di Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="a80b9-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="a80b9-653">Gestione di HttpClient e durata</span><span class="sxs-lookup"><span data-stu-id="a80b9-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="a80b9-654">Viene restituita una nuova istanza di `HttpClient` per ogni chiamata di `CreateClient` per `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="a80b9-655">È presente un <xref:System.Net.Http.HttpMessageHandler> per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="a80b9-656">La factory gestisce le durate delle istanze di `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="a80b9-657">`IHttpClientFactory` esegue il pooling delle istanze di `HttpMessageHandler` create dalla factory per ridurre il consumo di risorse.</span><span class="sxs-lookup"><span data-stu-id="a80b9-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="a80b9-658">Un'istanza di `HttpMessageHandler` può essere riusata dal pool quando si crea una nuova istanza di `HttpClient` se la relativa durata non è scaduta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="a80b9-659">Il pooling dei gestori è consigliabile in quanto ogni gestore gestisce in genere le proprie connessioni HTTP sottostanti.</span><span class="sxs-lookup"><span data-stu-id="a80b9-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="a80b9-660">La creazione di più gestori del necessario può causare ritardi di connessione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="a80b9-661">Alcuni gestori mantengono inoltre le connessioni aperte a tempo indefinito. Ciò può impedire al gestore di reagire alle modifiche DNS.</span><span class="sxs-lookup"><span data-stu-id="a80b9-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="a80b9-662">La durata del gestore predefinito è di due minuti.</span><span class="sxs-lookup"><span data-stu-id="a80b9-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="a80b9-663">Il valore predefinito può essere sottoposto a override per ogni client denominato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="a80b9-664">Per eseguire l'override, chiamare <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> nell'elemento `IHttpClientBuilder` restituito al momento della creazione del client:</span><span class="sxs-lookup"><span data-stu-id="a80b9-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="a80b9-665">L'eliminazione del client non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="a80b9-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="a80b9-666">L'eliminazione annulla le richieste in uscita e garantisce che l'istanza di `HttpClient` specificata non possa essere usata dopo la chiamata a <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="a80b9-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="a80b9-667">`IHttpClientFactory` tiene traccia ed elimina le risorse usate dalle istanze di `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="a80b9-668">Le istanze di `HttpClient` possono essere considerate a livello generale come oggetti .NET che non richiedono l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="a80b9-669">Mantenere una sola istanza di `HttpClient` attiva per un lungo periodo di tempo è un modello comune usato prima dell'avvento di `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="a80b9-670">Questo modello non è più necessario dopo la migrazione a `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="a80b9-671">Alternative a IHttpClientFactoryAlternatives to IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="a80b9-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="a80b9-672">L'uso in un'app abilitata per DI consente di evitare:Using `IHttpClientFactory` in a DI-enabled app avoids:</span><span class="sxs-lookup"><span data-stu-id="a80b9-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="a80b9-673">Problemi di esaurimento delle `HttpMessageHandler` risorse mediante il pooling delle istanze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="a80b9-674">Problemi DNS non `HttpMessageHandler` aggiornati causati dalle istanze di ciclismo a intervalli regolari.</span><span class="sxs-lookup"><span data-stu-id="a80b9-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="a80b9-675">Esistono modi alternativi per risolvere i problemi <xref:System.Net.Http.SocketsHttpHandler> precedenti utilizzando un'istanza di lunga durata.</span><span class="sxs-lookup"><span data-stu-id="a80b9-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="a80b9-676">Crea un'istanza di `SocketsHttpHandler` quando l'app viene avviata e usala per tutta la vita dell'app.</span><span class="sxs-lookup"><span data-stu-id="a80b9-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="a80b9-677">Configurare <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> un valore appropriato in base ai tempi di aggiornamento DNS.</span><span class="sxs-lookup"><span data-stu-id="a80b9-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="a80b9-678">Creare `HttpClient` istanze in `new HttpClient(handler, disposeHandler: false)` base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="a80b9-679">Gli approcci precedenti risolvono `IHttpClientFactory` i problemi di gestione delle risorse che risolvono in modo simile.</span><span class="sxs-lookup"><span data-stu-id="a80b9-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="a80b9-680">Le `SocketsHttpHandler` connessioni `HttpClient` azionarie tra le istanze.</span><span class="sxs-lookup"><span data-stu-id="a80b9-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="a80b9-681">Questa condivisione impedisce l'esaurimento dei socket.</span><span class="sxs-lookup"><span data-stu-id="a80b9-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="a80b9-682">Le `SocketsHttpHandler` connessioni cicli `PooledConnectionLifetime` in base a per evitare problemi DNS non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="a80b9-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="a80b9-683">Cookie</span><span class="sxs-lookup"><span data-stu-id="a80b9-683">Cookies</span></span>

<span data-ttu-id="a80b9-684">Le istanze `HttpMessageHandler` in `CookieContainer` pool determinano la condivisione degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="a80b9-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="a80b9-685">La `CookieContainer` condivisione imprevista degli oggetti spesso genera codice non corretto.</span><span class="sxs-lookup"><span data-stu-id="a80b9-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="a80b9-686">Per le app che richiedono cookie, considerare:</span><span class="sxs-lookup"><span data-stu-id="a80b9-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="a80b9-687">Disattivazione della gestione automatica dei cookie</span><span class="sxs-lookup"><span data-stu-id="a80b9-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="a80b9-688">Evitando`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="a80b9-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="a80b9-689">Chiamata <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per disabilitare la gestione automatica dei cookie:</span><span class="sxs-lookup"><span data-stu-id="a80b9-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="a80b9-690">Registrazione</span><span class="sxs-lookup"><span data-stu-id="a80b9-690">Logging</span></span>

<span data-ttu-id="a80b9-691">I client creati tramite `IHttpClientFactory` registrano i messaggi di log per tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="a80b9-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="a80b9-692">Per visualizzare i messaggi di log predefiniti, abilitare il livello di informazioni appropriato nella configurazione di registrazione.</span><span class="sxs-lookup"><span data-stu-id="a80b9-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="a80b9-693">La registrazione aggiuntiva, ad esempio quella delle intestazioni delle richieste, è inclusa solo a livello di traccia.</span><span class="sxs-lookup"><span data-stu-id="a80b9-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="a80b9-694">La categoria di log usata per ogni client include il nome del client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="a80b9-695">Un client denominato *MyNamedClient*, ad esempio, registra i messaggi con una categoria `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="a80b9-696">I messaggi con suffisso *LogicalHandler* sono esterni alla pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="a80b9-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="a80b9-697">Nella richiesta i messaggi vengono registrati prima che qualsiasi altro gestore nella pipeline l'abbia elaborata.</span><span class="sxs-lookup"><span data-stu-id="a80b9-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="a80b9-698">Nella risposta i messaggi vengono registrati dopo che qualsiasi altro gestore nella pipeline ha ricevuto la risposta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="a80b9-699">La registrazione avviene anche all'interno della pipeline del gestore delle richieste.</span><span class="sxs-lookup"><span data-stu-id="a80b9-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="a80b9-700">Nell'esempio *MyNamedClient*, i messaggi vengono registrati nella categoria di log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="a80b9-701">Per la richiesta, ciò avviene dopo l'esecuzione di tutti gli altri gestori e immediatamente prima che la richiesta sia inviata in rete.</span><span class="sxs-lookup"><span data-stu-id="a80b9-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="a80b9-702">Nella risposta la registrazione include lo stato della risposta prima di restituirla attraverso la pipeline del gestore.</span><span class="sxs-lookup"><span data-stu-id="a80b9-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="a80b9-703">L'abilitazione della registrazione all'esterno e all'interno della pipeline consente l'ispezione delle modifiche apportate da altri gestori nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="a80b9-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="a80b9-704">Le modifiche possono ad esempio riguardare le intestazioni delle richieste o il codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="a80b9-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="a80b9-705">L'inclusione del nome del client nella categoria di log consente di filtrare i log in base a client denominati specifici, se necessario.</span><span class="sxs-lookup"><span data-stu-id="a80b9-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="a80b9-706">Configurare HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="a80b9-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="a80b9-707">Può essere necessario controllare la configurazione dell'elemento `HttpMessageHandler` interno usato da un client.</span><span class="sxs-lookup"><span data-stu-id="a80b9-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="a80b9-708">Quando si aggiungono client denominati o tipizzati viene restituito un elemento `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="a80b9-709">È possibile usare il metodo di estensione <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> per definire un delegato.</span><span class="sxs-lookup"><span data-stu-id="a80b9-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="a80b9-710">Il delegato viene usato per creare e configurare l'elemento `HttpMessageHandler` primario usato dal client:</span><span class="sxs-lookup"><span data-stu-id="a80b9-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="a80b9-711">Usare IHttpClientFactory in un'app console</span><span class="sxs-lookup"><span data-stu-id="a80b9-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="a80b9-712">In un'app console aggiungere al progetto i riferimenti ai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="a80b9-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="a80b9-713">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="a80b9-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="a80b9-714">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="a80b9-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="a80b9-715">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="a80b9-715">In the following example:</span></span>

* <span data-ttu-id="a80b9-716"><xref:System.Net.Http.IHttpClientFactory> è registrato nel contenitore di servizi dell'[host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="a80b9-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="a80b9-717">`MyService` crea un'istanza della factory client dal servizio, che viene usata per creare una classe `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a80b9-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="a80b9-718">`HttpClient` viene usato per recuperare una pagina Web.</span><span class="sxs-lookup"><span data-stu-id="a80b9-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="a80b9-719">`Main` crea un ambito per eseguire il metodo `GetPage` del servizio e scrivere i primi 500 caratteri del contenuto della pagina Web nella console.</span><span class="sxs-lookup"><span data-stu-id="a80b9-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="a80b9-720">Middleware di propagazione dell'intestazione</span><span class="sxs-lookup"><span data-stu-id="a80b9-720">Header propagation middleware</span></span>

<span data-ttu-id="a80b9-721">La propagazione delle intestazioni è un middleware supportato dalla community per propagare le intestazioni HTTP dalla richiesta in ingresso alle richieste del client HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="a80b9-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="a80b9-722">Per utilizzare la propagazione dell'intestazione:</span><span class="sxs-lookup"><span data-stu-id="a80b9-722">To use header propagation:</span></span>

* <span data-ttu-id="a80b9-723">Fare riferimento alla porta supportata dalla community del pacchetto [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="a80b9-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="a80b9-724">ASP.NET Core 3.1 e versioni successive supporta [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="a80b9-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="a80b9-725">Configurare il `HttpClient` middleware e in: `Startup`</span><span class="sxs-lookup"><span data-stu-id="a80b9-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="a80b9-726">Il client include le intestazioni configurate nelle richieste in uscita:The client includes the configured headers on outbound requests:</span><span class="sxs-lookup"><span data-stu-id="a80b9-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="a80b9-727">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a80b9-727">Additional resources</span></span>

* [<span data-ttu-id="a80b9-728">Usare HttpClientFactory per l'implementazione di richieste HTTP resilienti</span><span class="sxs-lookup"><span data-stu-id="a80b9-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="a80b9-729">Implementazione dei tentativi di chiamate HTTP con backoff esponenziale con i criteri di Polly e HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="a80b9-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="a80b9-730">Implementazione dello schema Circuit Breaker</span><span class="sxs-lookup"><span data-stu-id="a80b9-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
