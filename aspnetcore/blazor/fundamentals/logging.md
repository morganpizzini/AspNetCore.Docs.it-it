---
title: BlazorRegistrazione ASP.NET Core
author: guardrex
description: Informazioni sulla registrazione nelle Blazor app, inclusa la configurazione a livello di log e su come scrivere messaggi di log dai Razor componenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 78117fa6e9c7d5aed3fb31bbd3afee55b3b5b875
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506708"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="c0632-103">BlazorRegistrazione ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0632-103">ASP.NET Core Blazor logging</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="c0632-104">Configurare la registrazione personalizzata nelle Blazor WebAssembly app con la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> Proprietà.</span><span class="sxs-lookup"><span data-stu-id="c0632-104">Configure custom logging in Blazor WebAssembly apps with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> property.</span></span>

<span data-ttu-id="c0632-105">Aggiungere lo spazio dei nomi per <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> a `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="c0632-105">Add the namespace for <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

<span data-ttu-id="c0632-106">In `Program.Main` di `Program.cs` impostare il livello di registrazione minimo con <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> e aggiungere il provider di registrazione personalizzato:</span><span class="sxs-lookup"><span data-stu-id="c0632-106">In `Program.Main` of `Program.cs`, set the minimum logging level with <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> and add the custom logging provider:</span></span>

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="c0632-107">La `Logging` proprietà è di tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , pertanto tutti i metodi di estensione disponibili in <xref:Microsoft.Extensions.Logging.ILoggingBuilder> sono disponibili anche in `Logging` .</span><span class="sxs-lookup"><span data-stu-id="c0632-107">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="c0632-108">La configurazione della registrazione può essere caricata da file di impostazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="c0632-108">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="c0632-109">Per altre informazioni, vedere <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="c0632-109">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="no-locsignalr-net-client-logging"></a><span data-ttu-id="c0632-110">SignalR Registrazione client .NET</span><span class="sxs-lookup"><span data-stu-id="c0632-110">SignalR .NET client logging</span></span>

<span data-ttu-id="c0632-111">Inserire un oggetto <xref:Microsoft.Extensions.Logging.ILoggerProvider> per aggiungere un oggetto `WebAssemblyConsoleLogger` ai provider di registrazione passati a <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="c0632-111">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="c0632-112">Diversamente da un tradizionale <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` è un wrapper per le API di registrazione specifiche del browser (ad esempio, `console.log` ).</span><span class="sxs-lookup"><span data-stu-id="c0632-112">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="c0632-113">L'uso di `WebAssemblyConsoleLogger` rende possibile la registrazione in mono all'interno di un contesto del browser.</span><span class="sxs-lookup"><span data-stu-id="c0632-113">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

<span data-ttu-id="c0632-114">Aggiungere lo spazio dei nomi per <xref:Microsoft.Extensions.Logging?displayProperty=fullName> e inserire un <xref:Microsoft.Extensions.Logging.ILoggerProvider> elemento nel componente:</span><span class="sxs-lookup"><span data-stu-id="c0632-114">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> and inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> into the component:</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

<span data-ttu-id="c0632-115">Nel [ `OnInitializedAsync` Metodo](xref:blazor/components/lifecycle#component-initialization-methods)del componente usare <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="c0632-115">In the component's [`OnInitializedAsync` method](xref:blazor/components/lifecycle#component-initialization-methods), use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="c0632-116">Per informazioni generali sulla registrazione di ASP.NET Core relative a Blazor Server , vedere <xref:fundamentals/logging/index> .</span><span class="sxs-lookup"><span data-stu-id="c0632-116">For general ASP.NET Core logging guidance that pertains to Blazor Server, see <xref:fundamentals/logging/index>.</span></span>

::: zone-end

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="c0632-117">Componenti di accesso Razor</span><span class="sxs-lookup"><span data-stu-id="c0632-117">Log in Razor components</span></span>

<span data-ttu-id="c0632-118">I logger rispettano la configurazione di avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="c0632-118">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="c0632-119">La `using` direttiva per <xref:Microsoft.Extensions.Logging> è necessaria per supportare i completamenti [IntelliSense](/visualstudio/ide/using-intellisense) per le API, ad esempio <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> e <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="c0632-119">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support [IntelliSense](/visualstudio/ide/using-intellisense) completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="c0632-120">Nell'esempio seguente viene illustrata la registrazione con un <xref:Microsoft.Extensions.Logging.ILogger> in Components.</span><span class="sxs-lookup"><span data-stu-id="c0632-120">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in components.</span></span>

<span data-ttu-id="c0632-121">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="c0632-121">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

<span data-ttu-id="c0632-122">Nell'esempio seguente viene illustrata la registrazione con un <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Components.</span><span class="sxs-lookup"><span data-stu-id="c0632-122">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in components.</span></span>

<span data-ttu-id="c0632-123">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="c0632-123">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a><span data-ttu-id="c0632-124">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c0632-124">Additional resources</span></span>

* <xref:fundamentals/logging/index>
