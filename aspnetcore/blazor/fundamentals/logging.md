---
title: BlazorRegistrazione ASP.NET Core
author: guardrex
description: Informazioni sulla registrazione nelle Blazor app, inclusa la configurazione a livello di log e su come scrivere messaggi di log dai Razor componenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: 1f4b18bdea02016fb76b75dd01a8fcbeab9b2bc9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402832"
---
# <a name="aspnet-core-blazor-logging"></a><span data-ttu-id="84365-103">BlazorRegistrazione ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84365-103">ASP.NET Core Blazor logging</span></span>

## Blazor WebAssembly

<span data-ttu-id="84365-104">Configurare la registrazione nelle Blazor WebAssembly app con la `WebAssemblyHostBuilder.Logging` Proprietà in `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="84365-104">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="84365-105">La `Logging` proprietà è di tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , pertanto tutti i metodi di estensione disponibili in <xref:Microsoft.Extensions.Logging.ILoggingBuilder> sono disponibili anche in `Logging` .</span><span class="sxs-lookup"><span data-stu-id="84365-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="84365-106">La configurazione della registrazione può essere caricata da file di impostazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="84365-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="84365-107">Per altre informazioni, vedere <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="84365-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## Blazor Server

<span data-ttu-id="84365-108">Per informazioni generali sulla registrazione di ASP.NET Core, vedere <xref:fundamentals/logging/index> .</span><span class="sxs-lookup"><span data-stu-id="84365-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="blazor-webassembly-signalr-net-client-logging"></a>Blazor WebAssembly<span data-ttu-id="84365-109">SignalRRegistrazione client .NET</span><span class="sxs-lookup"><span data-stu-id="84365-109"> SignalR .NET client logging</span></span>

<span data-ttu-id="84365-110">Inserire un oggetto <xref:Microsoft.Extensions.Logging.ILoggerProvider> per aggiungere un oggetto `WebAssemblyConsoleLogger` ai provider di registrazione passati a <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="84365-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="84365-111">Diversamente da un tradizionale <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` è un wrapper per le API di registrazione specifiche del browser (ad esempio, `console.log` ).</span><span class="sxs-lookup"><span data-stu-id="84365-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="84365-112">L'uso di `WebAssemblyConsoleLogger` rende possibile la registrazione in mono all'interno di un contesto del browser.</span><span class="sxs-lookup"><span data-stu-id="84365-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a><span data-ttu-id="84365-113">Componenti di accesso Razor</span><span class="sxs-lookup"><span data-stu-id="84365-113">Log in Razor components</span></span>

<span data-ttu-id="84365-114">I logger rispettano la configurazione di avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="84365-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="84365-115">La `using` direttiva per <xref:Microsoft.Extensions.Logging> è necessaria per supportare i completamenti IntelliSense per le API, ad esempio <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> e <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="84365-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="84365-116">Nell'esempio seguente viene illustrata la registrazione con un <xref:Microsoft.Extensions.Logging.ILogger> nei Razor componenti di:</span><span class="sxs-lookup"><span data-stu-id="84365-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

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

<span data-ttu-id="84365-117">Nell'esempio seguente viene illustrata la registrazione con un <xref:Microsoft.Extensions.Logging.ILoggerFactory> nei Razor componenti di:</span><span class="sxs-lookup"><span data-stu-id="84365-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="84365-118">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="84365-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>
