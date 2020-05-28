---
<span data-ttu-id="ff99a-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ff99a-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ff99a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ff99a-102">'Blazor'</span></span>
- <span data-ttu-id="ff99a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ff99a-103">'Identity'</span></span>
- <span data-ttu-id="ff99a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ff99a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ff99a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ff99a-105">'Razor'</span></span>
- <span data-ttu-id="ff99a-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ff99a-106">'SignalR' uid:</span></span> 

---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="ff99a-107">integrazione delle factory client gRPC in .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff99a-107">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="ff99a-108">l'integrazione di gRPC con `HttpClientFactory` offre un modo centralizzato per creare client gRPC.</span><span class="sxs-lookup"><span data-stu-id="ff99a-108">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="ff99a-109">Può essere usato come alternativa alla configurazione di [istanze client gRPC](xref:grpc/client)autonome.</span><span class="sxs-lookup"><span data-stu-id="ff99a-109">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="ff99a-110">L'integrazione di Factory è disponibile nel pacchetto NuGet [.NET. ClientFactory di Grpc](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="ff99a-110">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="ff99a-111">La Factory offre i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="ff99a-111">The factory offers the following benefits:</span></span>

* <span data-ttu-id="ff99a-112">Fornisce una posizione centralizzata per la configurazione di istanze client gRPC logiche</span><span class="sxs-lookup"><span data-stu-id="ff99a-112">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="ff99a-113">Gestisce la durata dell'oggetto sottostante`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="ff99a-113">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="ff99a-114">Propagazione automatica della scadenza e dell'annullamento in un ASP.NET Core servizio gRPC</span><span class="sxs-lookup"><span data-stu-id="ff99a-114">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="ff99a-115">Registrare i client di gRPC</span><span class="sxs-lookup"><span data-stu-id="ff99a-115">Register gRPC clients</span></span>

<span data-ttu-id="ff99a-116">Per registrare un client gRPC, `AddGrpcClient` è possibile usare il metodo di estensione generico in `Startup.ConfigureServices` , specificando la classe client tipizzata gRPC e l'indirizzo del servizio:</span><span class="sxs-lookup"><span data-stu-id="ff99a-116">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="ff99a-117">Il tipo DI client gRPC è registrato come temporaneo con l'inserimento DI dipendenze.</span><span class="sxs-lookup"><span data-stu-id="ff99a-117">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="ff99a-118">Il client può ora essere inserito e utilizzato direttamente nei tipi creati da DI.</span><span class="sxs-lookup"><span data-stu-id="ff99a-118">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="ff99a-119">ASP.NET Core i controller MVC, gli SignalR Hub e i servizi gRPC sono posti in cui è possibile inserire automaticamente i client gRPC:</span><span class="sxs-lookup"><span data-stu-id="ff99a-119">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a><span data-ttu-id="ff99a-120">Configurare HttpClient</span><span class="sxs-lookup"><span data-stu-id="ff99a-120">Configure HttpClient</span></span>

<span data-ttu-id="ff99a-121">`HttpClientFactory`Crea l'oggetto `HttpClient` utilizzato dal client gRPC.</span><span class="sxs-lookup"><span data-stu-id="ff99a-121">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="ff99a-122">I `HttpClientFactory` metodi standard possono essere usati per aggiungere il middleware della richiesta in uscita o per configurare il sottostante `HttpClientHandler` di `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="ff99a-122">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="ff99a-123">Per altre informazioni, vedere [creare richieste HTTP con IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="ff99a-123">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="ff99a-124">Configurare canale e intercettori</span><span class="sxs-lookup"><span data-stu-id="ff99a-124">Configure Channel and Interceptors</span></span>

<span data-ttu-id="ff99a-125">sono disponibili metodi specifici di gRPC per:</span><span class="sxs-lookup"><span data-stu-id="ff99a-125">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="ff99a-126">Configurare il canale sottostante di un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="ff99a-126">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="ff99a-127">Aggiungere `Interceptor` le istanze che il client userà per eseguire chiamate gRPC.</span><span class="sxs-lookup"><span data-stu-id="ff99a-127">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="ff99a-128">Scadenza e propagazione dell'annullamento</span><span class="sxs-lookup"><span data-stu-id="ff99a-128">Deadline and cancellation propagation</span></span>

<span data-ttu-id="ff99a-129">i client gRPC creati dalla factory in un servizio gRPC possono essere configurati con `EnableCallContextPropagation()` per propagare automaticamente la scadenza e il token di annullamento alle chiamate figlio.</span><span class="sxs-lookup"><span data-stu-id="ff99a-129">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="ff99a-130">Il `EnableCallContextPropagation()` metodo di estensione è disponibile nel pacchetto NuGet [Grpc. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="ff99a-130">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="ff99a-131">La propagazione del contesto di chiamata funziona leggendo la scadenza e il token di annullamento dal contesto della richiesta gRPC corrente e propagando automaticamente le chiamate in uscita effettuate dal client gRPC.</span><span class="sxs-lookup"><span data-stu-id="ff99a-131">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="ff99a-132">La propagazione del contesto di chiamata è un ottimo modo per garantire che gli scenari gRPC complessi e nidificati propaghino sempre la scadenza e l'annullamento.</span><span class="sxs-lookup"><span data-stu-id="ff99a-132">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="ff99a-133">Per impostazione predefinita, `EnableCallContextPropagation` genera un errore se il client viene usato al di fuori del contesto di una chiamata gRPC.</span><span class="sxs-lookup"><span data-stu-id="ff99a-133">By default, `EnableCallContextPropagation` raises an error if the client is used outside the context of a gRPC call.</span></span> <span data-ttu-id="ff99a-134">L'errore è stato progettato per segnalare che non è presente un contesto di chiamata da propagare.</span><span class="sxs-lookup"><span data-stu-id="ff99a-134">The error is designed to alert you that there isn't a call context to propagate.</span></span> <span data-ttu-id="ff99a-135">Se si desidera utilizzare il client all'esterno di un contesto di chiamata, non visualizzare l'errore quando il client è configurato con `SuppressContextNotFoundErrors` :</span><span class="sxs-lookup"><span data-stu-id="ff99a-135">If you want to use the client outside of a call context, suppress the error when the client is configured with `SuppressContextNotFoundErrors`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

<span data-ttu-id="ff99a-136">Per ulteriori informazioni sulle scadenze e sull'annullamento RPC, vedere [ciclo di vita RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="ff99a-136">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff99a-137">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ff99a-137">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
