---
<span data-ttu-id="2570e-101">title: migrare da Microsoft. Extensions. Logging 2,1 a 2,2 o 3,0 Author: pakrym Descrizione: informazioni su come eseguire la migrazione di un'applicazione non-ASP.NET Core che usa Microsoft. Extensions. Logging da 2,1 a 2,2 o 3,0.</span><span class="sxs-lookup"><span data-stu-id="2570e-101">title: Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0 author: pakrym description: Learn how to migrate a non-ASP.NET Core application that uses Microsoft.Extensions.Logging from 2.1 to 2.2 or 3.0.</span></span>
<span data-ttu-id="2570e-102">ms. Author: pakrym ms. Custom: MVC ms. Date: 01/04/2019 no-loc:</span><span class="sxs-lookup"><span data-stu-id="2570e-102">ms.author: pakrym ms.custom: mvc ms.date: 01/04/2019 no-loc:</span></span>
- <span data-ttu-id="2570e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2570e-103">'Blazor'</span></span>
- <span data-ttu-id="2570e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2570e-104">'Identity'</span></span>
- <span data-ttu-id="2570e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2570e-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="2570e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2570e-106">'Razor'</span></span>
- <span data-ttu-id="2570e-107">SignalRUID '': migrazione/registrazione-nonaspnetcore</span><span class="sxs-lookup"><span data-stu-id="2570e-107">'SignalR' uid: migration/logging-nonaspnetcore</span></span>

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="2570e-108">Eseguire la migrazione da Microsoft. Extensions. Logging 2,1 a 2,2 o 3,0</span><span class="sxs-lookup"><span data-stu-id="2570e-108">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="2570e-109">Questo articolo illustra i passaggi comuni per la migrazione di un'applicazione non-ASP.NET Core che usa `Microsoft.Extensions.Logging` da 2,1 a 2,2 o 3,0.</span><span class="sxs-lookup"><span data-stu-id="2570e-109">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="2570e-110">Da 2.1 a 2.2</span><span class="sxs-lookup"><span data-stu-id="2570e-110">2.1 to 2.2</span></span>

<span data-ttu-id="2570e-111">Creare `ServiceCollection` e chiamare manualmente `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="2570e-111">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="2570e-112">2,1 esempio:</span><span class="sxs-lookup"><span data-stu-id="2570e-112">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="2570e-113">2,2 esempio:</span><span class="sxs-lookup"><span data-stu-id="2570e-113">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="2570e-114">da 2,1 a 3,0</span><span class="sxs-lookup"><span data-stu-id="2570e-114">2.1 to 3.0</span></span>

<span data-ttu-id="2570e-115">In 3,0 usare `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="2570e-115">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="2570e-116">2,1 esempio:</span><span class="sxs-lookup"><span data-stu-id="2570e-116">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="2570e-117">3,0 esempio:</span><span class="sxs-lookup"><span data-stu-id="2570e-117">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="2570e-118">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2570e-118">Additional resources</span></span>

* <span data-ttu-id="2570e-119">[Pacchetto NuGet Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="2570e-119">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
