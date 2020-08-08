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
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a>Usare i filtri Hub in ASP.NET CoreSignalR

Filtri Hub:

* Sono disponibili in ASP.NET Core 5,0 o versioni successive.
* Consenti l'esecuzione della logica prima e dopo i metodi dell'hub richiamati dai client.

Questo articolo fornisce indicazioni per la scrittura e l'uso di filtri Hub.

## <a name="configure-hub-filters"></a>Configurare i filtri Hub

I filtri hub possono essere applicati a livello globale o per tipo di hub. L'ordine in cui vengono aggiunti i filtri è l'ordine in cui vengono eseguiti i filtri. I filtri dell'hub globale vengono eseguiti prima dei filtri dell'Hub locale.

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

È possibile aggiungere un filtro Hub in uno dei modi seguenti:

* Aggiungere un filtro in base al tipo concreto:

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    Questo problema verrà risolto da inserimento delle dipendenze (DI) o tipo attivato.

* Aggiungere un filtro in base al tipo di runtime:

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    Questa operazione verrà risolta da DI o dal tipo attivato.

* Aggiungere un filtro in base all'istanza:

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    Questa istanza verrà utilizzata come un singleton. Tutte le chiamate al metodo Hub utilizzeranno la stessa istanza.

I filtri Hub vengono creati ed eliminati per ogni chiamata dell'hub. Se si desidera archiviare lo stato globale nel filtro o nessun stato, aggiungere il tipo di filtro Hub a DI come singleton per ottenere prestazioni migliori. In alternativa, aggiungere il filtro come istanza, se possibile.

## <a name="create-hub-filters"></a>Creare filtri Hub

Creare un filtro dichiarando una classe che eredita da `IHubFilter` e aggiungere il `InvokeMethodAsync` metodo. È inoltre disponibile `OnConnectedAsync` e `OnDisconnectedAsync` che può facoltativamente essere implementato per eseguire il wrapping dei `OnConnectedAsync` `OnDisconnectedAsync` Metodi Hub e rispettivamente.

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

I filtri sono molto simili al middleware. Il `next` metodo richiama il filtro successivo. Il filtro finale richiama il metodo dell'hub. I filtri possono inoltre archiviare il risultato da await `next` ed eseguire la logica dopo che il metodo Hub è stato chiamato prima di restituire il risultato da `next` .

Per ignorare una chiamata al metodo dell'hub in un filtro, generare un'eccezione di tipo `HubException` anziché chiamare `next` . Il client riceverà un errore se si aspettava un risultato.

## <a name="use-hub-filters"></a>Usare i filtri Hub

Quando si scrive la logica di filtro, provare a renderla generica usando gli attributi nei metodi dell'hub invece di controllare i nomi dei metodi dell'hub.

Si consideri un filtro che controlla un argomento del metodo dell'hub per le frasi escluse e sostituisce le frasi trovate con `***` .
Per questo esempio, si supponga che una `LanguageFilterAttribute` classe sia definita. La classe dispone di una proprietà denominata `FilterArgument` che è possibile impostare quando si utilizza l'attributo.

1. Posizionare l'attributo nel metodo Hub con un argomento stringa da pulire:

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

1. Definire un filtro hub per verificare l'attributo e sostituire le frasi escluse in un argomento del metodo Hub con `***` :

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

1. Registrare il filtro Hub nel `Startup.ConfigureServices` metodo. Per evitare di reinizializzare l'elenco di frasi escluse per ogni chiamata, il filtro Hub viene registrato come singleton:

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

## <a name="the-hubinvocationcontext-object"></a>Oggetto HubInvocationContext

`HubInvocationContext`Contiene informazioni per la chiamata del metodo dell'hub corrente.

| Proprietà | Descrizione | Type |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Contiene informazioni sulla connessione. | `HubCallerContext` |
| `Hub` | Istanza dell'hub usato per la chiamata del metodo Hub. | `Hub` |
| `HubMethodName` | Nome del metodo Hub richiamato. | `string` |
| `HubMethodArguments` | Elenco di argomenti passati al metodo dell'hub. | `IReadOnlyList<string>` |
| `ServiceProvider` | Provider di servizi con ambito per la chiamata del metodo Hub. | `IServiceProvider` |
| `HubMethod` | Informazioni sul metodo dell'hub. | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a>Oggetto HubLifetimeContext

`HubLifetimeContext`Contiene informazioni per i `OnConnectedAsync` metodi dell' `OnDisconnectedAsync` Hub e.

| Proprietà | Descrizione | Type |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Contiene informazioni sulla connessione. | `HubCallerContext` |
| `Hub` | Istanza dell'hub usato per la chiamata del metodo Hub. | `Hub` |
| `ServiceProvider` | Provider di servizi con ambito per la chiamata del metodo Hub. | `IServiceProvider` |

## <a name="authorization-and-filters"></a>Autorizzazione e filtri

[Autorizzazione degli attributi nei metodi dell'hub](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) eseguita prima dei filtri Hub.
