---
title: :::no-loc(Blazor):::Registrazione ASP.NET Core
author: guardrex
description: 'Informazioni sulla registrazione nelle :::no-loc(Blazor)::: app, inclusa la configurazione a livello di log e su come scrivere messaggi di log dai :::no-loc(Razor)::: componenti.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/logging
ms.openlocfilehash: 72d339a4768b734ff33e7642b0af14f3f5725c7b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055984"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="839b4-103">:::no-loc(Blazor):::Registrazione ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="839b4-103">ASP.NET Core :::no-loc(Blazor)::: logging</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="839b4-104">Configurare la registrazione nelle :::no-loc(Blazor WebAssembly)::: app con la `WebAssemblyHostBuilder.Logging` Proprietà in `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="839b4-104">Configure logging in :::no-loc(Blazor WebAssembly)::: apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="839b4-105">La `Logging` proprietà è di tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , pertanto tutti i metodi di estensione disponibili in <xref:Microsoft.Extensions.Logging.ILoggingBuilder> sono disponibili anche in `Logging` .</span><span class="sxs-lookup"><span data-stu-id="839b4-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="839b4-106">La configurazione della registrazione può essere caricata da file di impostazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="839b4-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="839b4-107">Per altre informazioni, vedere <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="839b4-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## :::no-loc(Blazor Server):::

<span data-ttu-id="839b4-108">Per informazioni generali sulla registrazione di ASP.NET Core, vedere <xref:fundamentals/logging/index> .</span><span class="sxs-lookup"><span data-stu-id="839b4-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a><span data-ttu-id="839b4-109">:::no-loc(Blazor WebAssembly)::::::no-loc(SignalR):::Registrazione client .NET</span><span class="sxs-lookup"><span data-stu-id="839b4-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET client logging</span></span>

<span data-ttu-id="839b4-110">Inserire un oggetto <xref:Microsoft.Extensions.Logging.ILoggerProvider> per aggiungere un oggetto `WebAssemblyConsoleLogger` ai provider di registrazione passati a <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="839b4-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="839b4-111">Diversamente da un tradizionale <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` è un wrapper per le API di registrazione specifiche del browser (ad esempio, `console.log` ).</span><span class="sxs-lookup"><span data-stu-id="839b4-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="839b4-112">L'uso di `WebAssemblyConsoleLogger` rende possibile la registrazione in mono all'interno di un contesto del browser.</span><span class="sxs-lookup"><span data-stu-id="839b4-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="839b4-113">Componenti di accesso :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="839b4-113">Log in :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="839b4-114">I logger rispettano la configurazione di avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="839b4-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="839b4-115">La `using` direttiva per <xref:Microsoft.Extensions.Logging> è necessaria per supportare i completamenti IntelliSense per le API, ad esempio <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> e <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="839b4-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="839b4-116">Nell'esempio seguente viene illustrata la registrazione con un <xref:Microsoft.Extensions.Logging.ILogger> nei :::no-loc(Razor)::: componenti di:</span><span class="sxs-lookup"><span data-stu-id="839b4-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="839b4-117">Nell'esempio seguente viene illustrata la registrazione con un <xref:Microsoft.Extensions.Logging.ILoggerFactory> nei :::no-loc(Razor)::: componenti di:</span><span class="sxs-lookup"><span data-stu-id="839b4-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="839b4-118">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="839b4-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>
