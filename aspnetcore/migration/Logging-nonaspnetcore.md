---
title: Eseguire la migrazione da Microsoft. Extensions. Logging 2,1 a 2,2 o 3,0
author: pakrym
description: Informazioni su come eseguire la migrazione di un'applicazione non-ASP.NET Core che usa Microsoft. Extensions. Logging da 2,1 a 2,2 o 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2fd738ed0e0a06d0793e3c624d40a13725b53cd8
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84274232"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="8357c-103">Eseguire la migrazione da Microsoft. Extensions. Logging 2,1 a 2,2 o 3,0</span><span class="sxs-lookup"><span data-stu-id="8357c-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="8357c-104">Questo articolo illustra i passaggi comuni per la migrazione di un'applicazione non-ASP.NET Core che usa `Microsoft.Extensions.Logging` da 2,1 a 2,2 o 3,0.</span><span class="sxs-lookup"><span data-stu-id="8357c-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="8357c-105">Da 2.1 a 2.2</span><span class="sxs-lookup"><span data-stu-id="8357c-105">2.1 to 2.2</span></span>

<span data-ttu-id="8357c-106">Creare `ServiceCollection` e chiamare manualmente `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="8357c-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="8357c-107">2,1 esempio:</span><span class="sxs-lookup"><span data-stu-id="8357c-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="8357c-108">2,2 esempio:</span><span class="sxs-lookup"><span data-stu-id="8357c-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="8357c-109">da 2,1 a 3,0</span><span class="sxs-lookup"><span data-stu-id="8357c-109">2.1 to 3.0</span></span>

<span data-ttu-id="8357c-110">In 3,0 usare `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="8357c-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="8357c-111">2,1 esempio:</span><span class="sxs-lookup"><span data-stu-id="8357c-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="8357c-112">3,0 esempio:</span><span class="sxs-lookup"><span data-stu-id="8357c-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="8357c-113">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8357c-113">Additional resources</span></span>

* <span data-ttu-id="8357c-114">[Pacchetto NuGet Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="8357c-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
