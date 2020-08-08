---
title: Usare i filtri Hub in ASP.NET CoreSignalR
author: brecon
description: Informazioni su come usare i filtri Hub in ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hub-filters
ms.openlocfilehash: db6439ab4e61d0412148c5ea72b52d429367491b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022147"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="63d3d-103">Usare i filtri Hub in ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="63d3d-103">Use hub filters in ASP.NET Core SignalR</span></span>

<span data-ttu-id="63d3d-104">Filtri Hub:</span><span class="sxs-lookup"><span data-stu-id="63d3d-104">Hub filters:</span></span>

* <span data-ttu-id="63d3d-105">Sono disponibili in ASP.NET Core 5,0 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="63d3d-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="63d3d-106">Consenti l'esecuzione della logica prima e dopo i metodi dell'hub richiamati dai client.</span><span class="sxs-lookup"><span data-stu-id="63d3d-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="63d3d-107">Questo articolo fornisce indicazioni per la scrittura e l'uso di filtri Hub.</span><span class="sxs-lookup"><span data-stu-id="63d3d-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="63d3d-108">Configurare i filtri Hub</span><span class="sxs-lookup"><span data-stu-id="63d3d-108">Configure hub filters</span></span>

<span data-ttu-id="63d3d-109">I filtri hub possono essere applicati a livello globale o per tipo di hub.</span><span class="sxs-lookup"><span data-stu-id="63d3d-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="63d3d-110">L'ordine in cui vengono aggiunti i filtri è l'ordine in cui vengono eseguiti i filtri.</span><span class="sxs-lookup"><span data-stu-id="63d3d-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="63d3d-111">I filtri dell'hub globale vengono eseguiti prima dei filtri dell'Hub locale.</span><span class="sxs-lookup"><span data-stu-id="63d3d-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

<span data-ttu-id="63d3d-112">È possibile aggiungere un filtro Hub in uno dei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="63d3d-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="63d3d-113">Aggiungere un filtro in base al tipo concreto:</span><span class="sxs-lookup"><span data-stu-id="63d3d-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="63d3d-114">Questo problema verrà risolto da inserimento delle dipendenze (DI) o tipo attivato.</span><span class="sxs-lookup"><span data-stu-id="63d3d-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="63d3d-115">Aggiungere un filtro in base al tipo di runtime:</span><span class="sxs-lookup"><span data-stu-id="63d3d-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="63d3d-116">Questa operazione verrà risolta da DI o dal tipo attivato.</span><span class="sxs-lookup"><span data-stu-id="63d3d-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="63d3d-117">Aggiungere un filtro in base all'istanza:</span><span class="sxs-lookup"><span data-stu-id="63d3d-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="63d3d-118">Questa istanza verrà utilizzata come un singleton.</span><span class="sxs-lookup"><span data-stu-id="63d3d-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="63d3d-119">Tutte le chiamate al metodo Hub utilizzeranno la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="63d3d-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="63d3d-120">I filtri Hub vengono creati ed eliminati per ogni chiamata dell'hub.</span><span class="sxs-lookup"><span data-stu-id="63d3d-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="63d3d-121">Se si desidera archiviare lo stato globale nel filtro o nessun stato, aggiungere il tipo di filtro Hub a DI come singleton per ottenere prestazioni migliori.</span><span class="sxs-lookup"><span data-stu-id="63d3d-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="63d3d-122">In alternativa, aggiungere il filtro come istanza, se possibile.</span><span class="sxs-lookup"><span data-stu-id="63d3d-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="63d3d-123">Creare filtri Hub</span><span class="sxs-lookup"><span data-stu-id="63d3d-123">Create hub filters</span></span>

<span data-ttu-id="63d3d-124">Creare un filtro dichiarando una classe che eredita da `IHubFilter` e aggiungere il `InvokeMethodAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="63d3d-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="63d3d-125">È inoltre disponibile `OnConnectedAsync` e `OnDisconnectedAsync` che può facoltativamente essere implementato per eseguire il wrapping dei `OnConnectedAsync` `OnDisconnectedAsync` Metodi Hub e rispettivamente.</span><span class="sxs-lookup"><span data-stu-id="63d3d-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

<span data-ttu-id="63d3d-126">I filtri sono molto simili al middleware.</span><span class="sxs-lookup"><span data-stu-id="63d3d-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="63d3d-127">Il `next` metodo richiama il filtro successivo.</span><span class="sxs-lookup"><span data-stu-id="63d3d-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="63d3d-128">Il filtro finale richiama il metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="63d3d-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="63d3d-129">I filtri possono inoltre archiviare il risultato da await `next` ed eseguire la logica dopo che il metodo Hub è stato chiamato prima di restituire il risultato da `next` .</span><span class="sxs-lookup"><span data-stu-id="63d3d-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="63d3d-130">Per ignorare una chiamata al metodo dell'hub in un filtro, generare un'eccezione di tipo `HubException` anziché chiamare `next` .</span><span class="sxs-lookup"><span data-stu-id="63d3d-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="63d3d-131">Il client riceverà un errore se si aspettava un risultato.</span><span class="sxs-lookup"><span data-stu-id="63d3d-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="63d3d-132">Usare i filtri Hub</span><span class="sxs-lookup"><span data-stu-id="63d3d-132">Use hub filters</span></span>

<span data-ttu-id="63d3d-133">Quando si scrive la logica di filtro, provare a renderla generica usando gli attributi nei metodi dell'hub invece di controllare i nomi dei metodi dell'hub.</span><span class="sxs-lookup"><span data-stu-id="63d3d-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="63d3d-134">Si consideri un filtro che controlla un argomento del metodo dell'hub per le frasi escluse e sostituisce le frasi trovate con `***` .</span><span class="sxs-lookup"><span data-stu-id="63d3d-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="63d3d-135">Per questo esempio, si supponga che una `LanguageFilterAttribute` classe sia definita.</span><span class="sxs-lookup"><span data-stu-id="63d3d-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="63d3d-136">La classe dispone di una proprietà denominata `FilterArgument` che è possibile impostare quando si utilizza l'attributo.</span><span class="sxs-lookup"><span data-stu-id="63d3d-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="63d3d-137">Posizionare l'attributo nel metodo Hub con un argomento stringa da pulire:</span><span class="sxs-lookup"><span data-stu-id="63d3d-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. <span data-ttu-id="63d3d-138">Definire un filtro hub per verificare l'attributo e sostituire le frasi escluse in un argomento del metodo Hub con `***` :</span><span class="sxs-lookup"><span data-stu-id="63d3d-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `***`:</span></span>

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "***");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. <span data-ttu-id="63d3d-139">Registrare il filtro Hub nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="63d3d-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="63d3d-140">Per evitare di reinizializzare l'elenco di frasi escluse per ogni chiamata, il filtro Hub viene registrato come singleton:</span><span class="sxs-lookup"><span data-stu-id="63d3d-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="63d3d-141">Oggetto HubInvocationContext</span><span class="sxs-lookup"><span data-stu-id="63d3d-141">The HubInvocationContext object</span></span>

<span data-ttu-id="63d3d-142">`HubInvocationContext`Contiene informazioni per la chiamata del metodo dell'hub corrente.</span><span class="sxs-lookup"><span data-stu-id="63d3d-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="63d3d-143">Proprietà</span><span class="sxs-lookup"><span data-stu-id="63d3d-143">Property</span></span> | <span data-ttu-id="63d3d-144">Descrizione</span><span class="sxs-lookup"><span data-stu-id="63d3d-144">Description</span></span> | <span data-ttu-id="63d3d-145">Type</span><span class="sxs-lookup"><span data-stu-id="63d3d-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="63d3d-146">`HubCallerContext`Contiene informazioni sulla connessione.</span><span class="sxs-lookup"><span data-stu-id="63d3d-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="63d3d-147">Istanza dell'hub usato per la chiamata del metodo Hub.</span><span class="sxs-lookup"><span data-stu-id="63d3d-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="63d3d-148">Nome del metodo Hub richiamato.</span><span class="sxs-lookup"><span data-stu-id="63d3d-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="63d3d-149">Elenco di argomenti passati al metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="63d3d-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="63d3d-150">Provider di servizi con ambito per la chiamata del metodo Hub.</span><span class="sxs-lookup"><span data-stu-id="63d3d-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="63d3d-151">Informazioni sul metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="63d3d-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="63d3d-152">Oggetto HubLifetimeContext</span><span class="sxs-lookup"><span data-stu-id="63d3d-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="63d3d-153">`HubLifetimeContext`Contiene informazioni per i `OnConnectedAsync` metodi dell' `OnDisconnectedAsync` Hub e.</span><span class="sxs-lookup"><span data-stu-id="63d3d-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="63d3d-154">Proprietà</span><span class="sxs-lookup"><span data-stu-id="63d3d-154">Property</span></span> | <span data-ttu-id="63d3d-155">Descrizione</span><span class="sxs-lookup"><span data-stu-id="63d3d-155">Description</span></span> | <span data-ttu-id="63d3d-156">Type</span><span class="sxs-lookup"><span data-stu-id="63d3d-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="63d3d-157">`HubCallerContext`Contiene informazioni sulla connessione.</span><span class="sxs-lookup"><span data-stu-id="63d3d-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="63d3d-158">Istanza dell'hub usato per la chiamata del metodo Hub.</span><span class="sxs-lookup"><span data-stu-id="63d3d-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="63d3d-159">Provider di servizi con ambito per la chiamata del metodo Hub.</span><span class="sxs-lookup"><span data-stu-id="63d3d-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="63d3d-160">Autorizzazione e filtri</span><span class="sxs-lookup"><span data-stu-id="63d3d-160">Authorization and filters</span></span>

<span data-ttu-id="63d3d-161">[Autorizzazione degli attributi nei metodi dell'hub](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) eseguita prima dei filtri Hub.</span><span class="sxs-lookup"><span data-stu-id="63d3d-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>
