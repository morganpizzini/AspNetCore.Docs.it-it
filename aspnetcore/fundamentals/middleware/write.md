---
<span data-ttu-id="25ecc-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="25ecc-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ecc-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ecc-102">'Blazor'</span></span>
- <span data-ttu-id="25ecc-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ecc-103">'Identity'</span></span>
- <span data-ttu-id="25ecc-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ecc-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ecc-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ecc-105">'Razor'</span></span>
- <span data-ttu-id="25ecc-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="25ecc-106">'SignalR' uid:</span></span> 

---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="25ecc-107">Scrivere middleware di ASP.NET Core personalizzato</span><span class="sxs-lookup"><span data-stu-id="25ecc-107">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="25ecc-108">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="25ecc-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="25ecc-109">Il middleware è un software che viene assemblato in una pipeline dell'app per gestire richieste e risposte.</span><span class="sxs-lookup"><span data-stu-id="25ecc-109">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="25ecc-110">ASP.NET Core offre un ampio set di componenti middleware integrati, ma in alcuni scenari si potrebbe voler scrivere un middleware personalizzato.</span><span class="sxs-lookup"><span data-stu-id="25ecc-110">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="25ecc-111">In questo argomento viene descritto come scrivere un middleware *basato sulle convenzioni* .</span><span class="sxs-lookup"><span data-stu-id="25ecc-111">This topic describes how to write *convention-based* middleware.</span></span> <span data-ttu-id="25ecc-112">Per un approccio che usa la tipizzazione forte e l'attivazione per richiesta, vedere <xref:fundamentals/middleware/extensibility> .</span><span class="sxs-lookup"><span data-stu-id="25ecc-112">For an approach that uses strong typing and per-request activation, see <xref:fundamentals/middleware/extensibility>.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="25ecc-113">Classe middleware</span><span class="sxs-lookup"><span data-stu-id="25ecc-113">Middleware class</span></span>

<span data-ttu-id="25ecc-114">Il middleware è in genere incapsulato in una classe ed esposto con un metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="25ecc-114">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="25ecc-115">Si consideri il middleware seguente, che specifica le impostazioni cultura per la richiesta corrente da una stringa di query:</span><span class="sxs-lookup"><span data-stu-id="25ecc-115">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="25ecc-116">Il codice di esempio precedente viene usato per illustrare la creazione di un componente middleware.</span><span class="sxs-lookup"><span data-stu-id="25ecc-116">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="25ecc-117">Per il supporto di localizzazione incorporato di ASP.NET Core, vedere <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="25ecc-117">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="25ecc-118">Testare il middleware passando le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="25ecc-118">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="25ecc-119">Ad esempio, richiedere `https://localhost:5001/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="25ecc-119">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="25ecc-120">Il codice seguente sposta il delegato middleware in una classe:</span><span class="sxs-lookup"><span data-stu-id="25ecc-120">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="25ecc-121">La classe middleware deve includere:</span><span class="sxs-lookup"><span data-stu-id="25ecc-121">The middleware class must include:</span></span>

* <span data-ttu-id="25ecc-122">Un costruttore pubblico con un parametro di tipo <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span><span class="sxs-lookup"><span data-stu-id="25ecc-122">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="25ecc-123">Un metodo pubblico denominato `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="25ecc-123">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="25ecc-124">Questo metodo deve:</span><span class="sxs-lookup"><span data-stu-id="25ecc-124">This method must:</span></span>
  * <span data-ttu-id="25ecc-125">Restituire `Task`.</span><span class="sxs-lookup"><span data-stu-id="25ecc-125">Return a `Task`.</span></span>
  * <span data-ttu-id="25ecc-126">Accettare un primo parametro di tipo <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="25ecc-126">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="25ecc-127">I parametri aggiuntivi per il costruttore e `Invoke`/`InvokeAsync` vengono popolati dall'[inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="25ecc-127">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="25ecc-128">Dipendenze del middleware</span><span class="sxs-lookup"><span data-stu-id="25ecc-128">Middleware dependencies</span></span>

<span data-ttu-id="25ecc-129">Il middleware deve seguire il [principio delle dipendenze esplicite](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) esponendo le dipendenze nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="25ecc-129">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="25ecc-130">Il middleware viene costruito una volta per ogni *durata applicazione*.</span><span class="sxs-lookup"><span data-stu-id="25ecc-130">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="25ecc-131">Se è necessario condividere servizi con il middleware all'interno di una richiesta, vedere la sezione [Dipendenze del middleware per richiesta](#per-request-middleware-dependencies).</span><span class="sxs-lookup"><span data-stu-id="25ecc-131">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="25ecc-132">I componenti middleware possono risolvere le dipendenze dall'[inserimento di dipendenze](xref:fundamentals/dependency-injection) mediante i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="25ecc-132">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="25ecc-133">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) può anche accettare direttamente parametri aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="25ecc-133">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="25ecc-134">Dipendenze del middleware per richiesta</span><span class="sxs-lookup"><span data-stu-id="25ecc-134">Per-request middleware dependencies</span></span>

<span data-ttu-id="25ecc-135">Poiché il middleware viene creato all'avvio dell'app e non per richiesta, i servizi di durata *con ambito* usati dai costruttori del middleware non vengono condivisi con altri tipi di inserimento di dipendenze durante ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="25ecc-135">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="25ecc-136">Se è necessario condividere un servizio *con ambito* tra il proprio middleware e altri tipi, aggiungere i servizi alla firma del metodo `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="25ecc-136">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="25ecc-137">Il metodo `Invoke` può accettare parametri aggiuntivi popolati dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="25ecc-137">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a><span data-ttu-id="25ecc-138">Metodo di estensione del middleware</span><span class="sxs-lookup"><span data-stu-id="25ecc-138">Middleware extension method</span></span>

<span data-ttu-id="25ecc-139">Il metodo di estensione seguente espone il middleware tramite <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span><span class="sxs-lookup"><span data-stu-id="25ecc-139">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="25ecc-140">Il codice seguente chiama il middleware da `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="25ecc-140">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="25ecc-141">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="25ecc-141">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
