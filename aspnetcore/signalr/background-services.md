---
title: ASP.NET Core SignalR host nei servizi in background
author: bradygaster
description: Informazioni su come inviare messaggi ai SignalR client da classi BackgroundService di .NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: d5f1668d601f520939956985e46c62f3a5bdfcfa
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777293"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>ASP.NET Core SignalR host nei servizi in background

Di [Brady Gaster](https://twitter.com/bradygaster)

Questo articolo fornisce indicazioni per:

* Hub SignalR di hosting usando un processo di lavoro in background ospitato con ASP.NET Core.
* Invio di messaggi ai client connessi dall'interno di un [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)di .NET Core.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(procedura per il download)](xref:index#how-to-download-a-sample)

## <a name="enable-signalr-in-startup"></a>Abilita SignalR all'avvio

::: moniker range=">= aspnetcore-3.0"

L'hosting SignalR di hub ASP.NET Core nel contesto di un processo di lavoro in background è identico all'hosting di un hub in un'app Web ASP.NET Core. Nel `Startup.ConfigureServices` metodo, la chiamata `services.AddSignalR` di aggiunge i servizi richiesti al livello di inserimento delle dipendenze di ASP.NET Core per SignalRsupportare. In `Startup.Configure`il `MapHub` metodo viene chiamato nel `UseEndpoints` callback per connettere gli endpoint dell'hub nella pipeline della richiesta ASP.NET Core.

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

L'hosting SignalR di hub ASP.NET Core nel contesto di un processo di lavoro in background è identico all'hosting di un hub in un'app Web ASP.NET Core. Nel `Startup.ConfigureServices` metodo, la chiamata `services.AddSignalR` di aggiunge i servizi richiesti al livello di inserimento delle dipendenze di ASP.NET Core per SignalRsupportare. In `Startup.Configure`viene chiamato `UseSignalR` il metodo per connettere gli endpoint dell'hub nella pipeline della richiesta ASP.NET Core.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

Nell'esempio precedente, la `ClockHub` classe implementa la `Hub<T>` classe per creare un hub fortemente tipizzato. `ClockHub` È stato configurato nella `Startup` classe per rispondere alle richieste all'endpoint `/hubs/clock`.

Per altre informazioni sugli hub fortemente tipizzati, vedere [usare hub SignalR in per ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Questa funzionalità non è limitata alla classe di [>Hub\<t](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Anche tutte le classi che ereditano dall' [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), ad esempio [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funzioneranno.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

L'interfaccia utilizzata dalla fortemente tipizzata `ClockHub` è l' `IClock` interfaccia.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Chiamare un SignalR Hub da un servizio in background

Durante l'avvio, `Worker` la classe, `BackgroundService`a, viene abilitata utilizzando `AddHostedService`.

```csharp
services.AddHostedService<Worker>();
```

Poiché SignalR è abilitato anche durante la `Startup` fase, in cui ogni hub è collegato a un singolo endpoint nella pipeline di richieste http di ASP.NET Core, ogni hub è rappresentato da un `IHubContext<T>` nel server. Utilizzando le funzionalità DI DI ASP.NET Core, altre classi di cui è stata creata un'istanza `BackgroundService` dal livello di hosting, come le Razor classi, le classi controller MVC o i modelli di pagina, possono ottenere riferimenti `IHubContext<ClockHub, IClock>` a Hub sul lato server accettando istanze di durante la costruzione.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Poiché il `ExecuteAsync` metodo viene chiamato in modo iterativo nel servizio in background, la data e l'ora correnti del server vengono inviate ai client connessi `ClockHub`tramite.

## <a name="react-to-signalr-events-with-background-services"></a>Reagire SignalR agli eventi con servizi in background

Analogamente a un'app a pagina singola che usa SignalR il client JavaScript per o un'app desktop .NET può usare <xref:signalr/dotnet-client>usando, `BackgroundService` un' `IHostedService` implementazione di o può essere usata anche per SignalR connettersi agli hub e rispondere agli eventi.

La `ClockHubClient` classe implementa l' `IClock` interfaccia e l' `IHostedService` interfaccia. In questo modo può essere abilitata `Startup` durante l'esecuzione continua e rispondere agli eventi dell'hub dal server.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Durante l' `ClockHubClient` inizializzazione, crea un'istanza di `HubConnection` un oggetto e `IClock.ShowTime` Abilita il metodo come gestore per l' `ShowTime` evento dell'hub.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

Nell' `IHostedService.StartAsync` implementazione di `HubConnection` viene avviato in modo asincrono.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Durante il `IHostedService.StopAsync` metodo, l' `HubConnection` oggetto viene eliminato in modo asincrono.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Operazioni preliminari](xref:tutorials/signalr)
* [Hub](xref:signalr/hubs)
* [Pubblicazione in Azure](xref:signalr/publish-to-azure-web-app)
* [Hub fortemente tipizzati](xref:signalr/hubs#strongly-typed-hubs)
