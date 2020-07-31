---
title: Registrazione in .NET Core e ASP.NET Core
author: rick-anderson
description: Informazioni su come usare il framework di registrazione fornito dal pacchetto NuGet Microsoft.Extensions.Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330749"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="83981-103">Registrazione in .NET Core e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83981-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83981-104">Di [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83981-104">By [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="83981-105">.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti.</span><span class="sxs-lookup"><span data-stu-id="83981-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="83981-106">Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.</span><span class="sxs-lookup"><span data-stu-id="83981-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="83981-107">La maggior parte degli esempi di codice illustrati in questo articolo proviene da app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83981-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="83981-108">Le parti specifiche per la registrazione di questi frammenti di codice si applicano a qualsiasi app .NET Core che usa l' [host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="83981-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="83981-109">I modelli di app Web ASP.NET Core usano l'host generico.</span><span class="sxs-lookup"><span data-stu-id="83981-109">The ASP.NET Core web app templates use the Generic Host.</span></span>

<span data-ttu-id="83981-110">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="83981-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="lp"></a>

## <a name="logging-providers"></a><span data-ttu-id="83981-111">Provider di registrazione</span><span class="sxs-lookup"><span data-stu-id="83981-111">Logging providers</span></span>

<span data-ttu-id="83981-112">I provider di registrazione archiviano i log, ad eccezione del `Console` provider che Visualizza i log.</span><span class="sxs-lookup"><span data-stu-id="83981-112">Logging providers store logs, except for the `Console` provider which displays logs.</span></span> <span data-ttu-id="83981-113">Ad esempio, il provider applicazione Azure Insights archivia i log in applicazione Azure Insights.</span><span class="sxs-lookup"><span data-stu-id="83981-113">For example, the Azure Application Insights provider stores logs in Azure Application Insights.</span></span> <span data-ttu-id="83981-114">È possibile abilitare più provider.</span><span class="sxs-lookup"><span data-stu-id="83981-114">Multiple providers can be enabled.</span></span>

<span data-ttu-id="83981-115">I modelli di app Web ASP.NET Core predefiniti:</span><span class="sxs-lookup"><span data-stu-id="83981-115">The default ASP.NET Core web app templates:</span></span>

* <span data-ttu-id="83981-116">Usare l' [host generico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="83981-116">Use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>
* <span data-ttu-id="83981-117">Chiamare <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> , che aggiunge i provider di registrazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-117">Call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>
  * [<span data-ttu-id="83981-118">Console</span><span class="sxs-lookup"><span data-stu-id="83981-118">Console</span></span>](#console)
  * [<span data-ttu-id="83981-119">Eseguire il debug</span><span class="sxs-lookup"><span data-stu-id="83981-119">Debug</span></span>](#debug)
  * [<span data-ttu-id="83981-120">EventSource</span><span class="sxs-lookup"><span data-stu-id="83981-120">EventSource</span></span>](#event-source)
  * <span data-ttu-id="83981-121">[EventLog](#welog): solo Windows</span><span class="sxs-lookup"><span data-stu-id="83981-121">[EventLog](#welog): Windows only</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

<span data-ttu-id="83981-122">Il codice precedente Mostra la `Program` classe creata con i modelli di app web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83981-122">The preceding code shows the `Program` class created with the ASP.NET Core web app templates.</span></span> <span data-ttu-id="83981-123">Le sezioni successive forniscono esempi basati sui modelli di app Web ASP.NET Core, che usano l'host generico.</span><span class="sxs-lookup"><span data-stu-id="83981-123">The next several sections provide samples based on the ASP.NET Core web app templates, which use the Generic Host.</span></span> <span data-ttu-id="83981-124">Le [app console non host](#nhca) sono descritte più avanti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="83981-124">[Non-host console apps](#nhca) are discussed later in this document.</span></span>

<span data-ttu-id="83981-125">Per eseguire l'override del set predefinito di provider di registrazione aggiunto da `Host.CreateDefaultBuilder` , chiamare `ClearProviders` e aggiungere i provider di registrazione richiesti.</span><span class="sxs-lookup"><span data-stu-id="83981-125">To override the default set of logging providers added by `Host.CreateDefaultBuilder`, call `ClearProviders` and add the required logging providers.</span></span> <span data-ttu-id="83981-126">Ad esempio, il seguente codice:</span><span class="sxs-lookup"><span data-stu-id="83981-126">For example, the following code:</span></span>

* <span data-ttu-id="83981-127">Chiama <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> per rimuovere tutte le <xref:Microsoft.Extensions.Logging.ILoggerProvider> istanze dal generatore.</span><span class="sxs-lookup"><span data-stu-id="83981-127">Calls <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> to remove all the <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances from the builder.</span></span>
* <span data-ttu-id="83981-128">Aggiunge il provider di registrazione della [console](#console) .</span><span class="sxs-lookup"><span data-stu-id="83981-128">Adds the [Console](#console) logging provider.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

<span data-ttu-id="83981-129">Per ulteriori provider, vedere:</span><span class="sxs-lookup"><span data-stu-id="83981-129">For additional providers, see:</span></span>

* [<span data-ttu-id="83981-130">Provider di registrazione predefiniti</span><span class="sxs-lookup"><span data-stu-id="83981-130">Built-in logging providers</span></span>](#bilp)
* <span data-ttu-id="83981-131">[Provider di registrazione di terze parti](#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="83981-131">[Third-party logging providers](#third-party-logging-providers).</span></span>

## <a name="create-logs"></a><span data-ttu-id="83981-132">Creare log</span><span class="sxs-lookup"><span data-stu-id="83981-132">Create logs</span></span>

<span data-ttu-id="83981-133">Per creare i log, usare un <xref:Microsoft.Extensions.Logging.ILogger%601> oggetto da [inserimento delle dipendenze](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="83981-133">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object from [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="83981-134">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="83981-134">The following example:</span></span>

* <span data-ttu-id="83981-135">Crea un logger, `ILogger<AboutModel>` , che usa una *categoria* di log del nome completo del tipo `AboutModel` .</span><span class="sxs-lookup"><span data-stu-id="83981-135">Creates a logger, `ILogger<AboutModel>`, which uses a log *category* of the fully qualified name of the type `AboutModel`.</span></span> <span data-ttu-id="83981-136">La categoria del log è una stringa associata a ogni log.</span><span class="sxs-lookup"><span data-stu-id="83981-136">The log category is a string that is associated with each log.</span></span>
* <span data-ttu-id="83981-137">Chiama <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> per registrare il `Information` livello.</span><span class="sxs-lookup"><span data-stu-id="83981-137">Calls <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> to log at the `Information` level.</span></span> <span data-ttu-id="83981-138">Il *livello* del log indica la gravità dell'evento registrato.</span><span class="sxs-lookup"><span data-stu-id="83981-138">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

<span data-ttu-id="83981-139">I [livelli](#log-level) e le [categorie](#log-category) sono descritti in modo più dettagliato più avanti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="83981-139">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this document.</span></span>

<span data-ttu-id="83981-140">Per informazioni su Blazor , vedere [creare log in Blazor e Blazor WebAssembly ](#clib) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="83981-140">For information on Blazor, see [Create logs in Blazor and Blazor WebAssembly](#clib) in this document.</span></span>

<span data-ttu-id="83981-141">[Creare log in Main e startup](#clms) Mostra come creare i log in `Main` e `Startup` .</span><span class="sxs-lookup"><span data-stu-id="83981-141">[Create logs in Main and Startup](#clms) shows how to create logs in `Main` and `Startup`.</span></span>

## <a name="configure-logging"></a><span data-ttu-id="83981-142">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="83981-142">Configure logging</span></span>

<span data-ttu-id="83981-143">La configurazione della registrazione viene comunemente fornita dalla `Logging` sezione di *appSettings*. `{Environment}` file con *estensione JSON* .</span><span class="sxs-lookup"><span data-stu-id="83981-143">Logging configuration is commonly provided by the `Logging` section of *appsettings*.`{Environment}`*.json* files.</span></span> <span data-ttu-id="83981-144">I seguenti *appsettings.Development.jsnel* file vengono generati da ASP.NET Core modelli di app Web:</span><span class="sxs-lookup"><span data-stu-id="83981-144">The following *appsettings.Development.json* file is generated by the ASP.NET Core web app templates:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

<span data-ttu-id="83981-145">Nel codice JSON precedente:</span><span class="sxs-lookup"><span data-stu-id="83981-145">In the preceding JSON:</span></span>

* <span data-ttu-id="83981-146">`"Default"` `"Microsoft"` `"Microsoft.Hosting.Lifetime"` Vengono specificate le categorie, e.</span><span class="sxs-lookup"><span data-stu-id="83981-146">The `"Default"`, `"Microsoft"`, and `"Microsoft.Hosting.Lifetime"` categories are specified.</span></span>
* <span data-ttu-id="83981-147">La `"Microsoft"` categoria si applica a tutte le categorie che iniziano con `"Microsoft"` .</span><span class="sxs-lookup"><span data-stu-id="83981-147">The `"Microsoft"` category applies to all categories that start with `"Microsoft"`.</span></span> <span data-ttu-id="83981-148">Questa impostazione, ad esempio, si applica alla `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` categoria.</span><span class="sxs-lookup"><span data-stu-id="83981-148">For example, this setting applies to the `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` category.</span></span>
* <span data-ttu-id="83981-149">La `"Microsoft"` categoria registra a livello di log `Warning` e superiore.</span><span class="sxs-lookup"><span data-stu-id="83981-149">The `"Microsoft"` category logs at log level `Warning` and higher.</span></span>
* <span data-ttu-id="83981-150">La `"Microsoft.Hosting.Lifetime"` categoria è più specifica della `"Microsoft"` categoria, quindi la `"Microsoft.Hosting.Lifetime"` categoria registra a livello di log "Information" e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="83981-150">The `"Microsoft.Hosting.Lifetime"` category is more specific than the `"Microsoft"` category, so the `"Microsoft.Hosting.Lifetime"` category logs at log level "Information" and higher.</span></span>
* <span data-ttu-id="83981-151">Un provider di log specifico non è specificato, pertanto `LogLevel` si applica a tutti i provider di registrazione abilitati, ad eccezione del [registro eventi di Windows](#welog).</span><span class="sxs-lookup"><span data-stu-id="83981-151">A specific log provider is not specified, so `LogLevel` applies to all the enabled logging providers except for the [Windows EventLog](#welog).</span></span>

<span data-ttu-id="83981-152">La `Logging` proprietà può disporre <xref:Microsoft.Extensions.Logging.LogLevel> di proprietà del provider di log e.</span><span class="sxs-lookup"><span data-stu-id="83981-152">The `Logging` property can have <xref:Microsoft.Extensions.Logging.LogLevel> and log provider properties.</span></span> <span data-ttu-id="83981-153">`LogLevel`Specifica il [livello](#log-level) minimo da registrare per le categorie selezionate.</span><span class="sxs-lookup"><span data-stu-id="83981-153">The `LogLevel` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="83981-154">Nel codice JSON precedente `Information` e `Warning` vengono specificati i livelli di log.</span><span class="sxs-lookup"><span data-stu-id="83981-154">In the preceding JSON, `Information` and `Warning` log levels are specified.</span></span> <span data-ttu-id="83981-155">`LogLevel`indica la gravità del log e varia da 0 a 6:</span><span class="sxs-lookup"><span data-stu-id="83981-155">`LogLevel` indicates the severity of the log and ranges from 0 to 6:</span></span>

<span data-ttu-id="83981-156">`Trace`= 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 e `None` = 6.</span><span class="sxs-lookup"><span data-stu-id="83981-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5, and `None` = 6.</span></span>

<span data-ttu-id="83981-157">Quando `LogLevel` si specifica un oggetto, la registrazione viene abilitata per i messaggi al livello specificato e superiori.</span><span class="sxs-lookup"><span data-stu-id="83981-157">When a `LogLevel` is specified, logging is enabled for messages at the specified level and higher.</span></span> <span data-ttu-id="83981-158">Nel codice JSON precedente, la `Default` categoria viene registrata per `Information` e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="83981-158">In the preceding JSON, the `Default` category is logged for `Information` and higher.</span></span> <span data-ttu-id="83981-159">Ad esempio, `Information` `Warning` `Error` i messaggi,, e `Critical` vengono registrati.</span><span class="sxs-lookup"><span data-stu-id="83981-159">For example, `Information`, `Warning`, `Error`, and `Critical` messages are logged.</span></span> <span data-ttu-id="83981-160">Se non `LogLevel` viene specificato alcun valore, per impostazione predefinita viene impostato il livello di registrazione `Information` .</span><span class="sxs-lookup"><span data-stu-id="83981-160">If no `LogLevel` is specified, logging defaults to the `Information` level.</span></span> <span data-ttu-id="83981-161">Per altre informazioni, vedere [livelli di log](#llvl).</span><span class="sxs-lookup"><span data-stu-id="83981-161">For more information, see [Log levels](#llvl).</span></span>

<span data-ttu-id="83981-162">Una proprietà del provider può specificare una `LogLevel` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="83981-162">A provider property can specify a `LogLevel` property.</span></span> <span data-ttu-id="83981-163">`LogLevel`in un provider specifica i livelli da registrare per quel provider e sostituisce le impostazioni del log non del provider.</span><span class="sxs-lookup"><span data-stu-id="83981-163">`LogLevel` under a provider specifies levels to log for that provider, and overrides the non-provider log settings.</span></span> <span data-ttu-id="83981-164">Si considerino i *appsettings.jsseguenti nel* file:</span><span class="sxs-lookup"><span data-stu-id="83981-164">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

<span data-ttu-id="83981-165">Impostazioni in `Logging.{providername}.LogLevel` impostazioni di sostituzione in `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="83981-165">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="83981-166">Nel codice JSON precedente, il `Debug` livello di log predefinito del provider è impostato su `Information` :</span><span class="sxs-lookup"><span data-stu-id="83981-166">In the preceding JSON, the `Debug` provider's default log level is set to `Information`:</span></span>

`Logging:Debug:LogLevel:Default:Information`

<span data-ttu-id="83981-167">L'impostazione precedente specifica il `Information` livello di registrazione per ogni `Logging:Debug:` categoria eccetto `Microsoft.Hosting` .</span><span class="sxs-lookup"><span data-stu-id="83981-167">The preceding setting specifies the `Information` log level for every `Logging:Debug:` category except `Microsoft.Hosting`.</span></span> <span data-ttu-id="83981-168">Quando viene elencata una categoria specifica, la categoria specifica sostituisce quella predefinita.</span><span class="sxs-lookup"><span data-stu-id="83981-168">When a specific category is listed, the specific category overrides the default category.</span></span> <span data-ttu-id="83981-169">Nel codice JSON precedente, le `Logging:Debug:LogLevel` categorie `"Microsoft.Hosting"` ed `"Default"` eseguire l'override delle impostazioni in`Logging:LogLevel`</span><span class="sxs-lookup"><span data-stu-id="83981-169">In the preceding JSON, the `Logging:Debug:LogLevel` categories `"Microsoft.Hosting"` and `"Default"` override the settings in `Logging:LogLevel`</span></span>

<span data-ttu-id="83981-170">È possibile specificare il livello di registrazione minimo per uno dei seguenti valori:</span><span class="sxs-lookup"><span data-stu-id="83981-170">The minimum log level can be specified for any of:</span></span>

* <span data-ttu-id="83981-171">Provider specifici: ad esempio,`Logging:EventSource:LogLevel:Default:Information`</span><span class="sxs-lookup"><span data-stu-id="83981-171">Specific providers:  For example, `Logging:EventSource:LogLevel:Default:Information`</span></span>
* <span data-ttu-id="83981-172">Categorie specifiche: ad esempio,`Logging:LogLevel:Microsoft:Warning`</span><span class="sxs-lookup"><span data-stu-id="83981-172">Specific categories: For example, `Logging:LogLevel:Microsoft:Warning`</span></span>
* <span data-ttu-id="83981-173">Tutti i provider e tutte le categorie:`Logging:LogLevel:Default:Warning`</span><span class="sxs-lookup"><span data-stu-id="83981-173">All providers and all categories: `Logging:LogLevel:Default:Warning`</span></span>

<span data-ttu-id="83981-174">I log al di sotto del livello minimo ***non***sono:</span><span class="sxs-lookup"><span data-stu-id="83981-174">Any logs below the minimum level are ***not***:</span></span>

* <span data-ttu-id="83981-175">Passato al provider.</span><span class="sxs-lookup"><span data-stu-id="83981-175">Passed to the provider.</span></span>
* <span data-ttu-id="83981-176">Registrato o visualizzato.</span><span class="sxs-lookup"><span data-stu-id="83981-176">Logged or displayed.</span></span>

<span data-ttu-id="83981-177">Per evitare l'eliminazione di tutti i log, specificare [LogLevel. None](xref:Microsoft.Extensions.Logging.LogLevel).</span><span class="sxs-lookup"><span data-stu-id="83981-177">To suppress all logs, specify [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span></span> <span data-ttu-id="83981-178">`LogLevel.None`il valore di è 6, che è maggiore di `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="83981-178">`LogLevel.None` has a value of 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="83981-179">Se un provider supporta gli [ambiti del log](#logscopes), `IncludeScopes` indica se sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="83981-179">If a provider supports [log scopes](#logscopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="83981-180">Per altre informazioni, vedere [ambiti di log](#logscopes)</span><span class="sxs-lookup"><span data-stu-id="83981-180">For more information, see [log scopes](#logscopes)</span></span>

<span data-ttu-id="83981-181">Il *appsettings.jsseguente nel* file contiene tutti i provider abilitati per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="83981-181">The following *appsettings.json* file contains all the providers enabled by default:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

<span data-ttu-id="83981-182">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="83981-182">In the preceding sample:</span></span>

* <span data-ttu-id="83981-183">Le categorie e i livelli non sono valori suggeriti.</span><span class="sxs-lookup"><span data-stu-id="83981-183">The categories and levels are not suggested values.</span></span> <span data-ttu-id="83981-184">L'esempio viene fornito per mostrare tutti i provider predefiniti.</span><span class="sxs-lookup"><span data-stu-id="83981-184">The sample is provided to show all the default providers.</span></span>
* <span data-ttu-id="83981-185">Impostazioni in `Logging.{providername}.LogLevel` impostazioni di sostituzione in `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="83981-185">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="83981-186">Ad esempio, il livello in `Debug.LogLevel.Default` sostituisce il livello in `LogLevel.Default` .</span><span class="sxs-lookup"><span data-stu-id="83981-186">For example, the level in `Debug.LogLevel.Default` overrides the level in `LogLevel.Default`.</span></span>
* <span data-ttu-id="83981-187">Viene usato ogni *alias* del provider predefinito.</span><span class="sxs-lookup"><span data-stu-id="83981-187">Each default provider *alias* is used.</span></span> <span data-ttu-id="83981-188">Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo.</span><span class="sxs-lookup"><span data-stu-id="83981-188">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span> <span data-ttu-id="83981-189">Gli alias dei provider predefiniti sono:</span><span class="sxs-lookup"><span data-stu-id="83981-189">The built-in providers aliases are:</span></span>
  * <span data-ttu-id="83981-190">Console</span><span class="sxs-lookup"><span data-stu-id="83981-190">Console</span></span>
  * <span data-ttu-id="83981-191">Debug</span><span class="sxs-lookup"><span data-stu-id="83981-191">Debug</span></span>
  * <span data-ttu-id="83981-192">EventSource</span><span class="sxs-lookup"><span data-stu-id="83981-192">EventSource</span></span>
  * <span data-ttu-id="83981-193">EventLog</span><span class="sxs-lookup"><span data-stu-id="83981-193">EventLog</span></span>
  * <span data-ttu-id="83981-194">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="83981-194">AzureAppServicesFile</span></span>
  * <span data-ttu-id="83981-195">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="83981-195">AzureAppServicesBlob</span></span>
  * <span data-ttu-id="83981-196">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="83981-196">ApplicationInsights</span></span>

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a><span data-ttu-id="83981-197">Impostare il livello di registrazione per riga di comando, variabili di ambiente e altre configurazioni</span><span class="sxs-lookup"><span data-stu-id="83981-197">Set log level by command line, environment variables, and other configuration</span></span>

<span data-ttu-id="83981-198">Il livello di registrazione può essere impostato da uno qualsiasi dei [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="83981-198">Log level can be set by any of the [configuration providers](xref:fundamentals/configuration/index).</span></span> 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="83981-199">I comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-199">The following commands:</span></span>

* <span data-ttu-id="83981-200">Impostare la chiave ambiente `Logging:LogLevel:Microsoft` su un valore di `Information` in Windows.</span><span class="sxs-lookup"><span data-stu-id="83981-200">Set the environment key `Logging:LogLevel:Microsoft` to a value of `Information` on Windows.</span></span>
* <span data-ttu-id="83981-201">Testare le impostazioni quando si usa un'app creata con i modelli di applicazione Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83981-201">Test the settings when using an app created with the ASP.NET Core web application templates.</span></span> <span data-ttu-id="83981-202">Il `dotnet run` comando deve essere eseguito nella directory del progetto dopo l'uso di `set` .</span><span class="sxs-lookup"><span data-stu-id="83981-202">The `dotnet run` command must be run in the project directory after using `set`.</span></span>

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

<span data-ttu-id="83981-203">Impostazione dell'ambiente precedente:</span><span class="sxs-lookup"><span data-stu-id="83981-203">The preceding environment setting:</span></span>

* <span data-ttu-id="83981-204">Viene impostato solo nei processi avviati dalla finestra di comando in cui sono stati impostati.</span><span class="sxs-lookup"><span data-stu-id="83981-204">Is only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="83981-205">Non viene letto dai browser avviati con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="83981-205">Isn't read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="83981-206">Il comando [Setx](/windows-server/administration/windows-commands/setx) seguente imposta anche la chiave e il valore di ambiente in Windows.</span><span class="sxs-lookup"><span data-stu-id="83981-206">The following [setx](/windows-server/administration/windows-commands/setx) command also sets the environment key and value on Windows.</span></span> <span data-ttu-id="83981-207">Diversamente da `set` , `setx` le impostazioni sono rese permanente.</span><span class="sxs-lookup"><span data-stu-id="83981-207">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="83981-208">Il `/M` Commuter imposta la variabile nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="83981-208">The `/M` switch sets the variable in the system environment.</span></span> <span data-ttu-id="83981-209">Se `/M` non viene usato, viene impostata una variabile di ambiente utente.</span><span class="sxs-lookup"><span data-stu-id="83981-209">If `/M` isn't used, a user environment variable is set.</span></span>

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

<span data-ttu-id="83981-210">In [app Azure servizio](https://azure.microsoft.com/services/app-service/)selezionare **impostazione nuova applicazione** nella pagina **Impostazioni > configurazione** .</span><span class="sxs-lookup"><span data-stu-id="83981-210">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="83981-211">App Azure impostazioni dell'applicazione del servizio sono:</span><span class="sxs-lookup"><span data-stu-id="83981-211">Azure App Service application settings are:</span></span>

* <span data-ttu-id="83981-212">Crittografati a riposo e trasmessi su un canale crittografato.</span><span class="sxs-lookup"><span data-stu-id="83981-212">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="83981-213">Esposto come variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="83981-213">Exposed as environment variables.</span></span>

<span data-ttu-id="83981-214">Per altre informazioni, vedere [App di Azure: Eseguire l'override della configurazione delle app usando il portale di Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="83981-214">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="83981-215">Per altre informazioni sull'impostazione di ASP.NET Core valori di configurazione usando le variabili di ambiente, vedere [variabili di ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="83981-215">For more information on setting ASP.NET Core configuration values using environment variables, see [environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="83981-216">Per informazioni sull'utilizzo di altre origini di configurazione, tra cui la riga di comando, Azure Key Vault, la configurazione app Azure, altri formati di file e altro ancora, vedere <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="83981-216">For information on using other configuration sources, including the command line, Azure Key Vault, Azure App Configuration, other file formats, and more, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="83981-217">Applicazione delle regole di filtro</span><span class="sxs-lookup"><span data-stu-id="83981-217">How filtering rules are applied</span></span>

<span data-ttu-id="83981-218">Quando viene creato un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>, l'oggetto <xref:Microsoft.Extensions.Logging.ILoggerFactory> seleziona una singola regola per ogni provider da applicare al logger.</span><span class="sxs-lookup"><span data-stu-id="83981-218">When an <xref:Microsoft.Extensions.Logging.ILogger%601> object is created, the <xref:Microsoft.Extensions.Logging.ILoggerFactory> object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="83981-219">Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate.</span><span class="sxs-lookup"><span data-stu-id="83981-219">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="83981-220">La regola più specifica per ogni coppia di provider e categoria viene selezionata dalle regole disponibili.</span><span class="sxs-lookup"><span data-stu-id="83981-220">The most specific rule for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="83981-221">L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:</span><span class="sxs-lookup"><span data-stu-id="83981-221">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="83981-222">Selezionare tutte le regole corrispondenti al provider o al relativo alias.</span><span class="sxs-lookup"><span data-stu-id="83981-222">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="83981-223">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.</span><span class="sxs-lookup"><span data-stu-id="83981-223">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="83981-224">Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo.</span><span class="sxs-lookup"><span data-stu-id="83981-224">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="83981-225">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.</span><span class="sxs-lookup"><span data-stu-id="83981-225">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="83981-226">Se sono selezionate più regole, scegliere l'**ultima**.</span><span class="sxs-lookup"><span data-stu-id="83981-226">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="83981-227">Se non è selezionata alcuna regola, usare `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="83981-227">If no rules are selected, use `MinimumLevel`.</span></span>

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a><span data-ttu-id="83981-228">Registrazione dell'output da DotNet Run e Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83981-228">Logging output from dotnet run and Visual Studio</span></span>

<span data-ttu-id="83981-229">Vengono visualizzati i log creati con i [provider di registrazione predefiniti](#lp) :</span><span class="sxs-lookup"><span data-stu-id="83981-229">Logs created with the [default logging providers](#lp) are displayed:</span></span>

* <span data-ttu-id="83981-230">In Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83981-230">In Visual Studio</span></span>
  * <span data-ttu-id="83981-231">Nella finestra di output di debug durante il debug.</span><span class="sxs-lookup"><span data-stu-id="83981-231">In the Debug output window when debugging.</span></span>
  * <span data-ttu-id="83981-232">Nella finestra ASP.NET Core Server Web.</span><span class="sxs-lookup"><span data-stu-id="83981-232">In the ASP.NET Core Web Server window.</span></span>
* <span data-ttu-id="83981-233">Nella finestra della console quando l'app viene eseguita con `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="83981-233">In the console window when the app is run with `dotnet run`.</span></span>

<span data-ttu-id="83981-234">I log che iniziano con le categorie "Microsoft" appartengono a ASP.NET Core codice del Framework.</span><span class="sxs-lookup"><span data-stu-id="83981-234">Logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="83981-235">ASP.NET Core e il codice dell'applicazione usano la stessa API e i provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-235">ASP.NET Core and application code use the same logging API and providers.</span></span>

<a name="lcat"></a>

## <a name="log-category"></a><span data-ttu-id="83981-236">Categoria di log</span><span class="sxs-lookup"><span data-stu-id="83981-236">Log category</span></span>

<span data-ttu-id="83981-237">Quando `ILogger` viene creato un oggetto, viene specificata una *categoria* .</span><span class="sxs-lookup"><span data-stu-id="83981-237">When an `ILogger` object is created, a *category* is specified.</span></span> <span data-ttu-id="83981-238">La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="83981-238">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="83981-239">La stringa Category è arbitraria, ma la convenzione prevede l'uso del nome della classe.</span><span class="sxs-lookup"><span data-stu-id="83981-239">The category string is arbitrary, but the convention is to use the class name.</span></span> <span data-ttu-id="83981-240">Ad esempio, in un controller il nome potrebbe essere `"TodoApi.Controllers.TodoController"` .</span><span class="sxs-lookup"><span data-stu-id="83981-240">For example, in a controller the name might be `"TodoApi.Controllers.TodoController"`.</span></span> <span data-ttu-id="83981-241">Le app Web di ASP.NET Core usano `ILogger<T>` per ottenere automaticamente un' `ILogger` istanza che usa il nome di tipo completo di `T` come categoria:</span><span class="sxs-lookup"><span data-stu-id="83981-241">The ASP.NET Core web apps use `ILogger<T>` to automatically get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="83981-242">Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="83981-242">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

<span data-ttu-id="83981-243">L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="83981-243">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

<a name="llvl"></a>

## <a name="log-level"></a><span data-ttu-id="83981-244">Livello di registrazione</span><span class="sxs-lookup"><span data-stu-id="83981-244">Log level</span></span>

<span data-ttu-id="83981-245">La tabella seguente elenca i <xref:Microsoft.Extensions.Logging.LogLevel> valori, il `Log{LogLevel}` metodo di estensione convenience e l'utilizzo suggerito:</span><span class="sxs-lookup"><span data-stu-id="83981-245">The following table lists the <xref:Microsoft.Extensions.Logging.LogLevel> values, the convenience `Log{LogLevel}` extension method, and the suggested usage:</span></span>

| <span data-ttu-id="83981-246">LogLevel</span><span class="sxs-lookup"><span data-stu-id="83981-246">LogLevel</span></span> | <span data-ttu-id="83981-247">valore</span><span class="sxs-lookup"><span data-stu-id="83981-247">Value</span></span> | <span data-ttu-id="83981-248">Metodo</span><span class="sxs-lookup"><span data-stu-id="83981-248">Method</span></span> | <span data-ttu-id="83981-249">Descrizione</span><span class="sxs-lookup"><span data-stu-id="83981-249">Description</span></span> |
| -------- | ----- | ------ | ----------- |
| [<span data-ttu-id="83981-250">Trace</span><span class="sxs-lookup"><span data-stu-id="83981-250">Trace</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="83981-251">0</span><span class="sxs-lookup"><span data-stu-id="83981-251">0</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | <span data-ttu-id="83981-252">Contiene i messaggi più dettagliati.</span><span class="sxs-lookup"><span data-stu-id="83981-252">Contain the most detailed messages.</span></span> <span data-ttu-id="83981-253">Questi messaggi possono contenere dati sensibili dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-253">These messages may contain sensitive app data.</span></span> <span data-ttu-id="83981-254">Questi messaggi sono disabilitati per impostazione predefinita e ***non*** devono essere abilitati nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="83981-254">These messages are disabled by default and should ***not*** be enabled in production.</span></span> |
| [<span data-ttu-id="83981-255">Eseguire il debug</span><span class="sxs-lookup"><span data-stu-id="83981-255">Debug</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="83981-256">1</span><span class="sxs-lookup"><span data-stu-id="83981-256">1</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | <span data-ttu-id="83981-257">Per il debug e lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="83981-257">For debugging and development.</span></span> <span data-ttu-id="83981-258">Usare con cautela in produzione a causa del volume elevato.</span><span class="sxs-lookup"><span data-stu-id="83981-258">Use with caution in production due to the high volume.</span></span> |
| [<span data-ttu-id="83981-259">Informazioni</span><span class="sxs-lookup"><span data-stu-id="83981-259">Information</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="83981-260">2</span><span class="sxs-lookup"><span data-stu-id="83981-260">2</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | <span data-ttu-id="83981-261">Tiene traccia del flusso generale dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-261">Tracks the general flow of the app.</span></span> <span data-ttu-id="83981-262">Potrebbe avere un valore a lungo termine.</span><span class="sxs-lookup"><span data-stu-id="83981-262">May have long-term value.</span></span> |
| [<span data-ttu-id="83981-263">Warning</span><span class="sxs-lookup"><span data-stu-id="83981-263">Warning</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="83981-264">3</span><span class="sxs-lookup"><span data-stu-id="83981-264">3</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | <span data-ttu-id="83981-265">Per eventi anomali o imprevisti.</span><span class="sxs-lookup"><span data-stu-id="83981-265">For abnormal or unexpected events.</span></span> <span data-ttu-id="83981-266">Include in genere errori o condizioni che non provocano la mancata riuscita dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-266">Typically includes errors or conditions that don't cause the app to fail.</span></span> |
| [<span data-ttu-id="83981-267">Error (Errore) (Error (Errore)e)</span><span class="sxs-lookup"><span data-stu-id="83981-267">Error</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="83981-268">4</span><span class="sxs-lookup"><span data-stu-id="83981-268">4</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | <span data-ttu-id="83981-269">Per errori ed eccezioni che non possono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="83981-269">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="83981-270">Questi messaggi indicano un errore nell'operazione o nella richiesta corrente, non in un errore a livello di app.</span><span class="sxs-lookup"><span data-stu-id="83981-270">These messages indicate a failure in the current operation or request, not an app-wide failure.</span></span> |
| [<span data-ttu-id="83981-271">Critico</span><span class="sxs-lookup"><span data-stu-id="83981-271">Critical</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="83981-272">5</span><span class="sxs-lookup"><span data-stu-id="83981-272">5</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | <span data-ttu-id="83981-273">Per gli errori che richiedono attenzione immediata.</span><span class="sxs-lookup"><span data-stu-id="83981-273">For failures that require immediate attention.</span></span> <span data-ttu-id="83981-274">Esempi: scenari di perdita di dati, spazio su disco insufficiente.</span><span class="sxs-lookup"><span data-stu-id="83981-274">Examples: data loss scenarios, out of disk space.</span></span> |
| [<span data-ttu-id="83981-275">Nessuno</span><span class="sxs-lookup"><span data-stu-id="83981-275">None</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="83981-276">6</span><span class="sxs-lookup"><span data-stu-id="83981-276">6</span></span> | | <span data-ttu-id="83981-277">Specifica che una categoria di registrazione non deve scrivere alcun messaggio.</span><span class="sxs-lookup"><span data-stu-id="83981-277">Specifies that a logging category should not write any messages.</span></span> |

<span data-ttu-id="83981-278">Nella tabella precedente, `LogLevel` è elencato dal livello di gravità più basso a quello più alto.</span><span class="sxs-lookup"><span data-stu-id="83981-278">In the previous table, the `LogLevel` is listed from lowest to highest severity.</span></span>

<span data-ttu-id="83981-279">Il primo parametro del metodo [log](xref:Microsoft.Extensions.Logging.LoggerExtensions) , <xref:Microsoft.Extensions.Logging.LogLevel> , indica la gravità del log.</span><span class="sxs-lookup"><span data-stu-id="83981-279">The [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) method's first parameter, <xref:Microsoft.Extensions.Logging.LogLevel>, indicates the severity of the log.</span></span> <span data-ttu-id="83981-280">Anziché chiamare `Log(LogLevel, ...)` , la maggior parte degli sviluppatori chiama i metodi di estensione [log {LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) .</span><span class="sxs-lookup"><span data-stu-id="83981-280">Rather than calling `Log(LogLevel, ...)`, most developers call the [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) extension methods.</span></span> <span data-ttu-id="83981-281">I `Log{LogLevel}` metodi di estensione [chiamano il metodo log e specificano LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="83981-281">The `Log{LogLevel}` extension methods [call the Log method and specify the LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span> <span data-ttu-id="83981-282">Ad esempio, le due chiamate di registrazione seguenti sono equivalenti dal punto di vista funzionale e producono lo stesso log:</span><span class="sxs-lookup"><span data-stu-id="83981-282">For example, the following two logging calls are functionally equivalent and produce the same log:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

<span data-ttu-id="83981-283">`MyLogEvents.TestItem`ID dell'evento.</span><span class="sxs-lookup"><span data-stu-id="83981-283">`MyLogEvents.TestItem` is the event ID.</span></span> <span data-ttu-id="83981-284">`MyLogEvents`fa parte dell'app di esempio e viene visualizzato nella sezione [ID evento log](#leid) .</span><span class="sxs-lookup"><span data-stu-id="83981-284">`MyLogEvents` is part of the sample app and is displayed in the [Log event ID](#leid) section.</span></span>

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="83981-285">Il codice seguente crea i log `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="83981-285">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="83981-286">Nel codice precedente, il primo `Log{LogLevel}` parametro, `MyLogEvents.GetItem` , è l' [ID evento del log](#leid).</span><span class="sxs-lookup"><span data-stu-id="83981-286">In the preceding code, the first `Log{LogLevel}` parameter,`MyLogEvents.GetItem`, is the [Log event ID](#leid).</span></span> <span data-ttu-id="83981-287">Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti.</span><span class="sxs-lookup"><span data-stu-id="83981-287">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="83981-288">I parametri del metodo sono descritti nella sezione [modello di messaggio](#lmt) più avanti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="83981-288">The method parameters are explained in the [message template](#lmt) section later in this document.</span></span>

<span data-ttu-id="83981-289">Chiamare il `Log{LogLevel}` metodo appropriato per controllare la quantità di output di log scritta in un supporto di archiviazione specifico.</span><span class="sxs-lookup"><span data-stu-id="83981-289">Call the appropriate `Log{LogLevel}` method to control how much log output is written to a particular storage medium.</span></span> <span data-ttu-id="83981-290">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="83981-290">For example:</span></span>

* <span data-ttu-id="83981-291">In produzione:</span><span class="sxs-lookup"><span data-stu-id="83981-291">In production:</span></span>
  * <span data-ttu-id="83981-292">La registrazione a `Trace` `Information` livello di o genera un volume elevato di messaggi di log dettagliati.</span><span class="sxs-lookup"><span data-stu-id="83981-292">Logging at the `Trace` or `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="83981-293">Per controllare i costi e non superare i limiti di archiviazione dei dati, `Trace` `Information` i messaggi di log e di livello in un archivio dati con volumi elevati e a basso costo.</span><span class="sxs-lookup"><span data-stu-id="83981-293">To control costs and not exceed data storage limits, log `Trace` and `Information` level messages to a high-volume, low-cost data store.</span></span> <span data-ttu-id="83981-294">Prendere in considerazione la limitazione `Trace` e le `Information` categorie specifiche.</span><span class="sxs-lookup"><span data-stu-id="83981-294">Consider limiting `Trace` and `Information` to specific categories.</span></span>
  * <span data-ttu-id="83981-295">La registrazione a livello di file `Warning` `Critical` deve produrre pochi messaggi di log.</span><span class="sxs-lookup"><span data-stu-id="83981-295">Logging at `Warning` through `Critical` levels should produce few log messages.</span></span>
    * <span data-ttu-id="83981-296">I costi e i limiti di archiviazione in genere non rappresentano un problema.</span><span class="sxs-lookup"><span data-stu-id="83981-296">Costs and storage limits usually aren't a concern.</span></span>
    * <span data-ttu-id="83981-297">Pochi log consentono una maggiore flessibilità nelle scelte di archivio dati.</span><span class="sxs-lookup"><span data-stu-id="83981-297">Few logs allow more flexibility in data store choices.</span></span>
* <span data-ttu-id="83981-298">In fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="83981-298">In development:</span></span>
  * <span data-ttu-id="83981-299">Impostare su `Warning`.</span><span class="sxs-lookup"><span data-stu-id="83981-299">Set to `Warning`.</span></span>
  * <span data-ttu-id="83981-300">Aggiungere `Trace` o `Information` messaggi durante la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="83981-300">Add `Trace` or `Information` messages when troubleshooting.</span></span> <span data-ttu-id="83981-301">Per limitare l'output, impostare `Trace` o `Information` solo per le categorie sottoposte a ricerca.</span><span class="sxs-lookup"><span data-stu-id="83981-301">To limit output, set `Trace` or `Information` only for the categories under investigation.</span></span>

<span data-ttu-id="83981-302">ASP.NET Core scrive log per gli eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="83981-302">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="83981-303">Si consideri, ad esempio, l'output del log per:</span><span class="sxs-lookup"><span data-stu-id="83981-303">For example, consider the log output for:</span></span>

* <span data-ttu-id="83981-304">RazorApp di pagine creata con i modelli ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83981-304">A Razor Pages app created with the ASP.NET Core templates.</span></span>
* <span data-ttu-id="83981-305">Registrazione impostata su`Logging:Console:LogLevel:Microsoft:Information`</span><span class="sxs-lookup"><span data-stu-id="83981-305">Logging set to `Logging:Console:LogLevel:Microsoft:Information`</span></span>
* <span data-ttu-id="83981-306">Navigazione alla pagina privacy:</span><span class="sxs-lookup"><span data-stu-id="83981-306">Navigation to the Privacy page:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

<span data-ttu-id="83981-307">I set JSON seguenti `Logging:Console:LogLevel:Microsoft:Information` :</span><span class="sxs-lookup"><span data-stu-id="83981-307">The following JSON sets `Logging:Console:LogLevel:Microsoft:Information`:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a><span data-ttu-id="83981-308">ID evento di registrazione</span><span class="sxs-lookup"><span data-stu-id="83981-308">Log event ID</span></span>

<span data-ttu-id="83981-309">Ogni log può specificare un *ID evento*.</span><span class="sxs-lookup"><span data-stu-id="83981-309">Each log can specify an *event ID*.</span></span> <span data-ttu-id="83981-310">L'app di esempio usa la `MyLogEvents` classe per definire gli ID evento:</span><span class="sxs-lookup"><span data-stu-id="83981-310">The sample app uses the `MyLogEvents` class to define event IDs:</span></span>

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="83981-311">Un ID evento associa un set di eventi.</span><span class="sxs-lookup"><span data-stu-id="83981-311">An event ID associates a set of events.</span></span> <span data-ttu-id="83981-312">Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.</span><span class="sxs-lookup"><span data-stu-id="83981-312">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="83981-313">Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo.</span><span class="sxs-lookup"><span data-stu-id="83981-313">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="83981-314">Il provider Debug non visualizza gli ID evento.</span><span class="sxs-lookup"><span data-stu-id="83981-314">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="83981-315">Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:</span><span class="sxs-lookup"><span data-stu-id="83981-315">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

<span data-ttu-id="83981-316">Alcuni provider di registrazione archiviano l'ID evento in un campo, che consente di filtrare l'ID.</span><span class="sxs-lookup"><span data-stu-id="83981-316">Some logging providers store the event ID in a field, which allows for filtering on the ID.</span></span>

<a name="lmt"></a>

## <a name="log-message-template"></a><span data-ttu-id="83981-317">Modello di messaggio di registrazione</span><span class="sxs-lookup"><span data-stu-id="83981-317">Log message template</span></span>
<!-- Review, Each log API uses a message template. -->
<span data-ttu-id="83981-318">Ogni API di log usa un modello di messaggio.</span><span class="sxs-lookup"><span data-stu-id="83981-318">Each log API uses a message template.</span></span> <span data-ttu-id="83981-319">Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti.</span><span class="sxs-lookup"><span data-stu-id="83981-319">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="83981-320">Usare nomi per i segnaposto, non numeri.</span><span class="sxs-lookup"><span data-stu-id="83981-320">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="83981-321">L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="83981-321">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="83981-322">Nel codice seguente i nomi dei parametri sono fuori sequenza nel modello di messaggio:</span><span class="sxs-lookup"><span data-stu-id="83981-322">In the following code, the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="83981-323">Il codice precedente crea un messaggio di log con i valori dei parametri in sequenza:</span><span class="sxs-lookup"><span data-stu-id="83981-323">The preceding code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: param1, param2
```

<span data-ttu-id="83981-324">Questo approccio consente ai provider di registrazione di implementare la [registrazione semantica o strutturata](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span><span class="sxs-lookup"><span data-stu-id="83981-324">This approach allows logging providers to implement [semantic or structured logging](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span></span> <span data-ttu-id="83981-325">Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato.</span><span class="sxs-lookup"><span data-stu-id="83981-325">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="83981-326">Questo consente ai provider di registrazione di archiviare i valori dei parametri come campi.</span><span class="sxs-lookup"><span data-stu-id="83981-326">This enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="83981-327">Si consideri, ad esempio, il seguente metodo logger:</span><span class="sxs-lookup"><span data-stu-id="83981-327">For example, consider the following logger method:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="83981-328">Ad esempio, durante la registrazione nell'archiviazione tabelle di Azure:</span><span class="sxs-lookup"><span data-stu-id="83981-328">For example, when logging to Azure Table Storage:</span></span>

* <span data-ttu-id="83981-329">Ogni entità tabella di Azure può `ID` avere `RequestTime` proprietà e.</span><span class="sxs-lookup"><span data-stu-id="83981-329">Each Azure Table entity can have `ID` and `RequestTime` properties.</span></span>
* <span data-ttu-id="83981-330">Le tabelle con proprietà semplificano le query sui dati registrati.</span><span class="sxs-lookup"><span data-stu-id="83981-330">Tables with properties simplify queries on logged data.</span></span> <span data-ttu-id="83981-331">Una query, ad esempio, può trovare tutti i log all'interno di un `RequestTime` intervallo specifico senza dover analizzare il timeout del messaggio di testo.</span><span class="sxs-lookup"><span data-stu-id="83981-331">For example, a query can find all logs within a particular `RequestTime` range without having to parse the time out of the text message.</span></span>

## <a name="log-exceptions"></a><span data-ttu-id="83981-332">Eccezioni di log</span><span class="sxs-lookup"><span data-stu-id="83981-332">Log exceptions</span></span>

<span data-ttu-id="83981-333">I metodi logger hanno overload che accettano un parametro Exception:</span><span class="sxs-lookup"><span data-stu-id="83981-333">The logger methods have overloads that take an exception parameter:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="83981-334">La registrazione delle eccezioni è specifica del provider.</span><span class="sxs-lookup"><span data-stu-id="83981-334">Exception logging is provider-specific.</span></span>

### <a name="default-log-level"></a><span data-ttu-id="83981-335">Livello di registrazione predefinito</span><span class="sxs-lookup"><span data-stu-id="83981-335">Default log level</span></span>

<span data-ttu-id="83981-336">Se il livello di registrazione predefinito non è impostato, il valore predefinito del livello di registrazione è `Information` .</span><span class="sxs-lookup"><span data-stu-id="83981-336">If the default log level is not set, the default log level value is `Information`.</span></span>

<span data-ttu-id="83981-337">Si consideri ad esempio la seguente app Web:</span><span class="sxs-lookup"><span data-stu-id="83981-337">For example, consider the following web app:</span></span>

* <span data-ttu-id="83981-338">Creato con i modelli di app Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="83981-338">Created with the ASP.NET web app templates.</span></span>
* <span data-ttu-id="83981-339">*appsettings.jsin* e *appsettings.Development.jsin* eliminato o rinominato.</span><span class="sxs-lookup"><span data-stu-id="83981-339">*appsettings.json* and *appsettings.Development.json* deleted or renamed.</span></span>

<span data-ttu-id="83981-340">Con la configurazione precedente, la navigazione alla privacy o home page produce molti `Trace` messaggi, `Debug` e `Information` con `Microsoft` nel nome della categoria.</span><span class="sxs-lookup"><span data-stu-id="83981-340">With the preceding setup, navigating to the privacy or home page produces many `Trace`, `Debug`, and `Information`  messages with `Microsoft` in the category name.</span></span>

<span data-ttu-id="83981-341">Il codice seguente imposta il livello di registrazione predefinito quando il livello di registrazione predefinito non è impostato nella configurazione:</span><span class="sxs-lookup"><span data-stu-id="83981-341">The following code sets the default log level when the default log level is not set in configuration:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
<span data-ttu-id="83981-342">In genere, i livelli di log devono essere specificati nella configurazione e non nel codice.</span><span class="sxs-lookup"><span data-stu-id="83981-342">Generally, log levels should be specified in configuration and not code.</span></span>

### <a name="filter-function"></a><span data-ttu-id="83981-343">Filter - funzione</span><span class="sxs-lookup"><span data-stu-id="83981-343">Filter function</span></span>

<span data-ttu-id="83981-344">Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice:</span><span class="sxs-lookup"><span data-stu-id="83981-344">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

<span data-ttu-id="83981-345">Il codice precedente Visualizza i log della console quando la categoria contiene `Controller` o `Microsoft` e il livello di registrazione è `Information` o superiore.</span><span class="sxs-lookup"><span data-stu-id="83981-345">The preceding code displays console logs when the category contains `Controller` or `Microsoft` and the log level is `Information` or higher.</span></span>

<span data-ttu-id="83981-346">In genere, i livelli di log devono essere specificati nella configurazione e non nel codice.</span><span class="sxs-lookup"><span data-stu-id="83981-346">Generally, log levels should be specified in configuration and not code.</span></span>

## <a name="aspnet-core-and-ef-core-categories"></a><span data-ttu-id="83981-347">Categorie ASP.NET Core e EF Core</span><span class="sxs-lookup"><span data-stu-id="83981-347">ASP.NET Core and EF Core categories</span></span>

<span data-ttu-id="83981-348">La tabella seguente contiene alcune categorie usate da ASP.NET Core e Entity Framework Core, con note sui log:</span><span class="sxs-lookup"><span data-stu-id="83981-348">The following table contains some categories used by ASP.NET Core and Entity Framework Core, with notes about the logs:</span></span>

| <span data-ttu-id="83981-349">Category</span><span class="sxs-lookup"><span data-stu-id="83981-349">Category</span></span>                            | <span data-ttu-id="83981-350">Note</span><span class="sxs-lookup"><span data-stu-id="83981-350">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="83981-351">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="83981-351">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="83981-352">Diagnostica generale di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83981-352">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="83981-353">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="83981-353">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="83981-354">Chiavi considerate, trovate e usate.</span><span class="sxs-lookup"><span data-stu-id="83981-354">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="83981-355">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="83981-355">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="83981-356">Host consentiti.</span><span class="sxs-lookup"><span data-stu-id="83981-356">Hosts allowed.</span></span> |
| <span data-ttu-id="83981-357">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="83981-357">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="83981-358">Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste.</span><span class="sxs-lookup"><span data-stu-id="83981-358">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="83981-359">Assembly di avvio dell'hosting caricati.</span><span class="sxs-lookup"><span data-stu-id="83981-359">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="83981-360">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="83981-360">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="83981-361">MVC e Razor diagnostica.</span><span class="sxs-lookup"><span data-stu-id="83981-361">MVC and Razor diagnostics.</span></span> <span data-ttu-id="83981-362">Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni.</span><span class="sxs-lookup"><span data-stu-id="83981-362">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="83981-363">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="83981-363">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="83981-364">Informazioni sulla corrispondenza di route.</span><span class="sxs-lookup"><span data-stu-id="83981-364">Route matching information.</span></span> |
| <span data-ttu-id="83981-365">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="83981-365">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="83981-366">Risposte di avvio, arresto e keep-alive per la connessione.</span><span class="sxs-lookup"><span data-stu-id="83981-366">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="83981-367">Informazioni sui certificati HTTPS.</span><span class="sxs-lookup"><span data-stu-id="83981-367">HTTPS certificate information.</span></span> |
| <span data-ttu-id="83981-368">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="83981-368">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="83981-369">File forniti.</span><span class="sxs-lookup"><span data-stu-id="83981-369">Files served.</span></span> |
| <span data-ttu-id="83981-370">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="83981-370">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="83981-371">Diagnostica generale di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="83981-371">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="83981-372">Attività e configurazione del database, rilevamento delle modifiche, migrazioni.</span><span class="sxs-lookup"><span data-stu-id="83981-372">Database activity and configuration, change detection, migrations.</span></span> |

<span data-ttu-id="83981-373">Per visualizzare altre categorie nella finestra della console, impostare **appsettings.Development.jssu** come riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="83981-373">To view more categories in the console window, set **appsettings.Development.json** to the following:</span></span>

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a><span data-ttu-id="83981-374">Ambiti dei log</span><span class="sxs-lookup"><span data-stu-id="83981-374">Log scopes</span></span>

 <span data-ttu-id="83981-375">Un *ambito* può raggruppare un set di operazioni logiche.</span><span class="sxs-lookup"><span data-stu-id="83981-375">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="83981-376">Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set.</span><span class="sxs-lookup"><span data-stu-id="83981-376">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="83981-377">Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.</span><span class="sxs-lookup"><span data-stu-id="83981-377">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="83981-378">Un ambito:</span><span class="sxs-lookup"><span data-stu-id="83981-378">A scope:</span></span>

* <span data-ttu-id="83981-379">È un <xref:System.IDisposable> tipo restituito dal <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodo.</span><span class="sxs-lookup"><span data-stu-id="83981-379">Is an <xref:System.IDisposable> type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method.</span></span>
* <span data-ttu-id="83981-380">Dura fino a quando non viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="83981-380">Lasts until it's disposed.</span></span>

<span data-ttu-id="83981-381">I provider seguenti supportano gli ambiti:</span><span class="sxs-lookup"><span data-stu-id="83981-381">The following providers support scopes:</span></span>

* `Console`
* [<span data-ttu-id="83981-382">AzureAppServicesFile e AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="83981-382">AzureAppServicesFile and AzureAppServicesBlob</span></span>](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

<span data-ttu-id="83981-383">Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:</span><span class="sxs-lookup"><span data-stu-id="83981-383">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

<span data-ttu-id="83981-384">Il codice JSON seguente abilita gli ambiti per il provider della console:</span><span class="sxs-lookup"><span data-stu-id="83981-384">The following JSON enables scopes for the console provider:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

<span data-ttu-id="83981-385">Il codice seguente abilita gli ambiti per il provider Console:</span><span class="sxs-lookup"><span data-stu-id="83981-385">The following code enables scopes for the console provider:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

<span data-ttu-id="83981-386">In genere, la registrazione deve essere specificata nella configurazione e non nel codice.</span><span class="sxs-lookup"><span data-stu-id="83981-386">Generally, logging should be specified in configuration and not code.</span></span>

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a><span data-ttu-id="83981-387">Provider di registrazione predefiniti</span><span class="sxs-lookup"><span data-stu-id="83981-387">Built-in logging providers</span></span>

<span data-ttu-id="83981-388">ASP.NET Core include i provider di registrazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-388">ASP.NET Core includes the following logging providers:</span></span>

* [<span data-ttu-id="83981-389">Console</span><span class="sxs-lookup"><span data-stu-id="83981-389">Console</span></span>](#console)
* [<span data-ttu-id="83981-390">Eseguire il debug</span><span class="sxs-lookup"><span data-stu-id="83981-390">Debug</span></span>](#debug)
* [<span data-ttu-id="83981-391">EventSource</span><span class="sxs-lookup"><span data-stu-id="83981-391">EventSource</span></span>](#event-source)
* [<span data-ttu-id="83981-392">EventLog</span><span class="sxs-lookup"><span data-stu-id="83981-392">EventLog</span></span>](#welog)
* [<span data-ttu-id="83981-393">AzureAppServicesFile e AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="83981-393">AzureAppServicesFile and AzureAppServicesBlob</span></span>](#azure-app-service)
* [<span data-ttu-id="83981-394">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="83981-394">ApplicationInsights</span></span>](#azure-application-insights)

<span data-ttu-id="83981-395">Per informazioni `stdout` e per eseguire il debug della registrazione con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .</span><span class="sxs-lookup"><span data-stu-id="83981-395">For information on `stdout` and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console"></a><span data-ttu-id="83981-396">Console</span><span class="sxs-lookup"><span data-stu-id="83981-396">Console</span></span>

<span data-ttu-id="83981-397">Il `Console` provider registra l'output nella console.</span><span class="sxs-lookup"><span data-stu-id="83981-397">The `Console` provider logs output to the console.</span></span> <span data-ttu-id="83981-398">Per altre informazioni sulla visualizzazione dei `Console` log in fase di sviluppo, vedere [registrazione dell'output da DotNet Run e Visual Studio](#dnrvs).</span><span class="sxs-lookup"><span data-stu-id="83981-398">For more information on viewing `Console` logs in development, see [Logging output from dotnet run and Visual Studio](#dnrvs).</span></span>

### <a name="debug"></a><span data-ttu-id="83981-399">Debug</span><span class="sxs-lookup"><span data-stu-id="83981-399">Debug</span></span>

<span data-ttu-id="83981-400">Il `Debug` provider scrive l'output del log usando la classe [System. Diagnostics. debug](/dotnet/api/system.diagnostics.debug) .</span><span class="sxs-lookup"><span data-stu-id="83981-400">The `Debug` provider writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class.</span></span> <span data-ttu-id="83981-401">Chiama per `System.Diagnostics.Debug.WriteLine` scrivere nel `Debug` provider.</span><span class="sxs-lookup"><span data-stu-id="83981-401">Calls to `System.Diagnostics.Debug.WriteLine` write to the `Debug` provider.</span></span>

<span data-ttu-id="83981-402">In Linux il `Debug` percorso del log del provider è dipendente dalla distribuzione e può essere uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-402">On Linux, the `Debug` provider log location is distribution-dependent and may be one of the following:</span></span>

* <span data-ttu-id="83981-403">*/var/log/message*</span><span class="sxs-lookup"><span data-stu-id="83981-403">*/var/log/message*</span></span>
* <span data-ttu-id="83981-404">*/var/log/syslog*</span><span class="sxs-lookup"><span data-stu-id="83981-404">*/var/log/syslog*</span></span>

### <a name="event-source"></a><span data-ttu-id="83981-405">Origine evento</span><span class="sxs-lookup"><span data-stu-id="83981-405">Event Source</span></span>

<span data-ttu-id="83981-406">Il `EventSource` provider scrive in un'origine evento multipiattaforma con il nome `Microsoft-Extensions-Logging` .</span><span class="sxs-lookup"><span data-stu-id="83981-406">The `EventSource` provider writes to a cross-platform event source with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="83981-407">In Windows, il provider utilizza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="83981-407">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="83981-408">strumenti di traccia DotNet</span><span class="sxs-lookup"><span data-stu-id="83981-408">dotnet trace tooling</span></span>

<span data-ttu-id="83981-409">Lo strumento [DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) è uno strumento globale dell'interfaccia della riga di comando multipiattaforma che consente la raccolta di tracce .NET Core di un processo in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="83981-409">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="83981-410">Lo strumento raccoglie i <xref:Microsoft.Extensions.Logging.EventSource> dati del provider utilizzando un <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> .</span><span class="sxs-lookup"><span data-stu-id="83981-410">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="83981-411">Per istruzioni sull'installazione, vedere [DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) .</span><span class="sxs-lookup"><span data-stu-id="83981-411">See [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) for installation instructions.</span></span>

<span data-ttu-id="83981-412">Usare gli strumenti di traccia DotNet per raccogliere una traccia da un'app:</span><span class="sxs-lookup"><span data-stu-id="83981-412">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="83981-413">Eseguire l'app con il `dotnet run` comando.</span><span class="sxs-lookup"><span data-stu-id="83981-413">Run the app with the `dotnet run` command.</span></span>
1. <span data-ttu-id="83981-414">Determinare l'identificatore di processo (PID) dell'app .NET Core:</span><span class="sxs-lookup"><span data-stu-id="83981-414">Determine the process identifier (PID) of the .NET Core app:</span></span>
   * <span data-ttu-id="83981-415">In Windows usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-415">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="83981-416">Gestione attività (CTRL + ALT + CANC)</span><span class="sxs-lookup"><span data-stu-id="83981-416">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="83981-417">comando tasklist</span><span class="sxs-lookup"><span data-stu-id="83981-417">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="83981-418">Comando PowerShell Get-Process</span><span class="sxs-lookup"><span data-stu-id="83981-418">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="83981-419">In Linux usare il [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="83981-419">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="83981-420">Trovare il PID per il processo con lo stesso nome dell'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-420">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="83981-421">Eseguire il `dotnet trace` comando.</span><span class="sxs-lookup"><span data-stu-id="83981-421">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="83981-422">Sintassi generale del comando:</span><span class="sxs-lookup"><span data-stu-id="83981-422">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="83981-423">Quando si usa una shell dei comandi di PowerShell, racchiudere il `--providers` valore tra virgolette singole ( `'` ):</span><span class="sxs-lookup"><span data-stu-id="83981-423">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="83981-424">Nelle piattaforme non Windows aggiungere l' `-f speedscope` opzione per modificare il formato del file di traccia di output in `speedscope` .</span><span class="sxs-lookup"><span data-stu-id="83981-424">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="83981-425">Parola chiave</span><span class="sxs-lookup"><span data-stu-id="83981-425">Keyword</span></span> | <span data-ttu-id="83981-426">Descrizione</span><span class="sxs-lookup"><span data-stu-id="83981-426">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="83981-427">1</span><span class="sxs-lookup"><span data-stu-id="83981-427">1</span></span>       | <span data-ttu-id="83981-428">Registra i metadati relativi a `LoggingEventSource` .</span><span class="sxs-lookup"><span data-stu-id="83981-428">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="83981-429">Non registra gli eventi da `ILogger` ).</span><span class="sxs-lookup"><span data-stu-id="83981-429">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="83981-430">2</span><span class="sxs-lookup"><span data-stu-id="83981-430">2</span></span>       | <span data-ttu-id="83981-431">Attiva l' `Message` evento quando `ILogger.Log()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="83981-431">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="83981-432">Fornisce informazioni in un metodo programmatico (non formattato).</span><span class="sxs-lookup"><span data-stu-id="83981-432">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="83981-433">4</span><span class="sxs-lookup"><span data-stu-id="83981-433">4</span></span>       | <span data-ttu-id="83981-434">Attiva l' `FormatMessage` evento quando `ILogger.Log()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="83981-434">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="83981-435">Fornisce la versione di stringa formattata delle informazioni.</span><span class="sxs-lookup"><span data-stu-id="83981-435">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="83981-436">8</span><span class="sxs-lookup"><span data-stu-id="83981-436">8</span></span>       | <span data-ttu-id="83981-437">Attiva l' `MessageJson` evento quando `ILogger.Log()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="83981-437">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="83981-438">Fornisce una rappresentazione JSON degli argomenti.</span><span class="sxs-lookup"><span data-stu-id="83981-438">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="83981-439">Livello dell'evento</span><span class="sxs-lookup"><span data-stu-id="83981-439">Event Level</span></span> | <span data-ttu-id="83981-440">Description</span><span class="sxs-lookup"><span data-stu-id="83981-440">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="83981-441">0</span><span class="sxs-lookup"><span data-stu-id="83981-441">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="83981-442">1</span><span class="sxs-lookup"><span data-stu-id="83981-442">1</span></span>           | `Critical`      |
   | <span data-ttu-id="83981-443">2</span><span class="sxs-lookup"><span data-stu-id="83981-443">2</span></span>           | `Error`         |
   | <span data-ttu-id="83981-444">3</span><span class="sxs-lookup"><span data-stu-id="83981-444">3</span></span>           | `Warning`       |
   | <span data-ttu-id="83981-445">4</span><span class="sxs-lookup"><span data-stu-id="83981-445">4</span></span>           | `Informational` |
   | <span data-ttu-id="83981-446">5</span><span class="sxs-lookup"><span data-stu-id="83981-446">5</span></span>           | `Verbose`       |

   <span data-ttu-id="83981-447">`FilterSpecs`le voci per `{Logger Category}` e `{Event Level}` rappresentano altre condizioni di filtro del log.</span><span class="sxs-lookup"><span data-stu-id="83981-447">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="83981-448">Separare `FilterSpecs` le voci con un punto e virgola ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="83981-448">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="83981-449">Esempio di utilizzo di una shell dei comandi di Windows (**senza** virgolette singole intorno al `--providers` valore):</span><span class="sxs-lookup"><span data-stu-id="83981-449">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="83981-450">Il comando precedente attiva:</span><span class="sxs-lookup"><span data-stu-id="83981-450">The preceding command activates:</span></span>

   * <span data-ttu-id="83981-451">Il logger dell'origine evento per produrre stringhe formattate ( `4` ) per gli errori ( `2` ).</span><span class="sxs-lookup"><span data-stu-id="83981-451">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="83981-452">`Microsoft.AspNetCore.Hosting`registrazione a `Informational` livello di registrazione ( `4` ).</span><span class="sxs-lookup"><span data-stu-id="83981-452">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="83981-453">Arrestare gli strumenti di traccia DotNet premendo il tasto invio o CTRL + C.</span><span class="sxs-lookup"><span data-stu-id="83981-453">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="83981-454">La traccia viene salvata con il nome *Trace. NetTrace* nella cartella in cui `dotnet trace` viene eseguito il comando.</span><span class="sxs-lookup"><span data-stu-id="83981-454">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="83981-455">Aprire la traccia con [PerfView](#perfview).</span><span class="sxs-lookup"><span data-stu-id="83981-455">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="83981-456">Aprire il file *Trace. NetTrace* ed esplorare gli eventi di traccia.</span><span class="sxs-lookup"><span data-stu-id="83981-456">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="83981-457">Se l'app non compila l'host con `CreateDefaultBuilder` , aggiungere il [provider di origine eventi](#event-source-provider) alla configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-457">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

<span data-ttu-id="83981-458">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="83981-458">For more information, see:</span></span>

* <span data-ttu-id="83981-459">[Trace for Performance Analysis Utility (DotNet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (documentazione di .NET Core)</span><span class="sxs-lookup"><span data-stu-id="83981-459">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="83981-460">[Trace for Performance Analysis Utility (DotNet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentazione del repository GitHub DotNet/Diagnostics)</span><span class="sxs-lookup"><span data-stu-id="83981-460">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="83981-461">[Classe LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (browser API .NET)</span><span class="sxs-lookup"><span data-stu-id="83981-461">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="83981-462">[Origine riferimento LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): per ottenere l'origine riferimento per una versione diversa, modificare il ramo in `release/{Version}` , dove `{Version}` è la versione di ASP.NET Core desiderata.</span><span class="sxs-lookup"><span data-stu-id="83981-462">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="83981-463">[PerfView](#perfview): utile per la visualizzazione delle tracce dell'origine evento.</span><span class="sxs-lookup"><span data-stu-id="83981-463">[Perfview](#perfview): Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="83981-464">PerfView</span><span class="sxs-lookup"><span data-stu-id="83981-464">Perfview</span></span>

<span data-ttu-id="83981-465">Usare l' [utilità PerfView](https://github.com/Microsoft/perfview) per raccogliere e visualizzare i log.</span><span class="sxs-lookup"><span data-stu-id="83981-465">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="83981-466">Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83981-466">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="83981-467">Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi**</span><span class="sxs-lookup"><span data-stu-id="83981-467">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="83981-468">Non perdere all' `*` inizio della stringa.</span><span class="sxs-lookup"><span data-stu-id="83981-468">Don't miss the `*` at the start of the string.</span></span>

<a name="welog"></a>

### <a name="windows-eventlog"></a><span data-ttu-id="83981-469">Registro eventi di Windows</span><span class="sxs-lookup"><span data-stu-id="83981-469">Windows EventLog</span></span>

<span data-ttu-id="83981-470">Il `EventLog` provider invia l'output del log al registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="83981-470">The `EventLog` provider sends log output to the Windows Event Log.</span></span> <span data-ttu-id="83981-471">Diversamente dagli altri provider, il `EventLog` provider ***non*** eredita le impostazioni predefinite non del provider.</span><span class="sxs-lookup"><span data-stu-id="83981-471">Unlike the other providers, the `EventLog` provider does ***not*** inherit the default non-provider settings.</span></span> <span data-ttu-id="83981-472">Se `EventLog` non si specificano le impostazioni del log, il [valore predefinito è LogLevel. Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span><span class="sxs-lookup"><span data-stu-id="83981-472">If `EventLog` log settings aren't specified, they [default to LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span></span>

<span data-ttu-id="83981-473">Per registrare eventi inferiori a <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> , impostare in modo esplicito il livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-473">To log events lower than <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, explicitly set the log level.</span></span> <span data-ttu-id="83981-474">Nell'esempio seguente viene impostato il livello di registrazione predefinito del registro eventi su <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="83981-474">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

<span data-ttu-id="83981-475">Gli [Overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) possono passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> .</span><span class="sxs-lookup"><span data-stu-id="83981-475">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) can pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="83981-476">Se `null` o non è specificato, vengono usate le impostazioni predefinite seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-476">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="83981-477">`LogName`: "Applicazione"</span><span class="sxs-lookup"><span data-stu-id="83981-477">`LogName`: "Application"</span></span>
* <span data-ttu-id="83981-478">`SourceName`: "Runtime .NET"</span><span class="sxs-lookup"><span data-stu-id="83981-478">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="83981-479">`MachineName`: Viene usato il nome del computer locale.</span><span class="sxs-lookup"><span data-stu-id="83981-479">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="83981-480">Il codice seguente modifica il `SourceName` valore di dal valore predefinito di `".NET Runtime"` a `MyLogs` :</span><span class="sxs-lookup"><span data-stu-id="83981-480">The following code changes the `SourceName` from the default value of `".NET Runtime"` to `MyLogs`:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a><span data-ttu-id="83981-481">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="83981-481">Azure App Service</span></span>

<span data-ttu-id="83981-482">Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="83981-482">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

<span data-ttu-id="83981-483">Il pacchetto del provider non è incluso nel framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="83981-483">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="83981-484">Per usare il provider, aggiungere il relativo pacchetto al progetto.</span><span class="sxs-lookup"><span data-stu-id="83981-484">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="83981-485">Per configurare le impostazioni del provider, usare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="83981-485">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

<span data-ttu-id="83981-486">Quando viene distribuito nel servizio app Azure, l'app usa le impostazioni nella sezione [log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) della pagina del **servizio app** del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="83981-486">When deployed to Azure App Service, the app uses the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="83981-487">Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-487">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="83981-488">**Registrazione applicazioni (file system)**</span><span class="sxs-lookup"><span data-stu-id="83981-488">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="83981-489">**Registrazione applicazione (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="83981-489">**Application Logging (Blob)**</span></span>

<span data-ttu-id="83981-490">Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*.</span><span class="sxs-lookup"><span data-stu-id="83981-490">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="83981-491">Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2.</span><span class="sxs-lookup"><span data-stu-id="83981-491">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="83981-492">Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="83981-492">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="83981-493">Questo provider viene registrato solo quando il progetto viene eseguito nell'ambiente Azure.</span><span class="sxs-lookup"><span data-stu-id="83981-493">This provider only logs when the project runs in the Azure environment.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="83981-494">Flusso di registrazione di Azure</span><span class="sxs-lookup"><span data-stu-id="83981-494">Azure log streaming</span></span>

<span data-ttu-id="83981-495">Azure log streaming supporta la visualizzazione delle attività di log in tempo reale da:</span><span class="sxs-lookup"><span data-stu-id="83981-495">Azure log streaming supports viewing log activity in real time from:</span></span>

* <span data-ttu-id="83981-496">Server applicazioni</span><span class="sxs-lookup"><span data-stu-id="83981-496">The app server</span></span>
* <span data-ttu-id="83981-497">Server Web</span><span class="sxs-lookup"><span data-stu-id="83981-497">The web server</span></span>
* <span data-ttu-id="83981-498">Traccia delle richieste non riuscite</span><span class="sxs-lookup"><span data-stu-id="83981-498">Failed request tracing</span></span>

<span data-ttu-id="83981-499">Per configurare il flusso di registrazione di Azure:</span><span class="sxs-lookup"><span data-stu-id="83981-499">To configure Azure log streaming:</span></span>

* <span data-ttu-id="83981-500">Passare alla pagina **log del servizio app** dalla pagina del portale dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-500">Navigate to the **App Service logs** page from the app's portal page.</span></span>
* <span data-ttu-id="83981-501">Impostare **Registrazione applicazioni (file system)** su **Attiva**.</span><span class="sxs-lookup"><span data-stu-id="83981-501">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="83981-502">Scegliere il livello di registrazione in **Livello**.</span><span class="sxs-lookup"><span data-stu-id="83981-502">Choose the log **Level**.</span></span> <span data-ttu-id="83981-503">Questa impostazione è valida solo per lo streaming di log di Azure.</span><span class="sxs-lookup"><span data-stu-id="83981-503">This setting only applies to Azure log streaming.</span></span>

<span data-ttu-id="83981-504">Passare alla pagina del **flusso di log** per visualizzare i log.</span><span class="sxs-lookup"><span data-stu-id="83981-504">Navigate to the **Log Stream** page to view logs.</span></span> <span data-ttu-id="83981-505">I messaggi registrati vengono registrati con l' `ILogger` interfaccia.</span><span class="sxs-lookup"><span data-stu-id="83981-505">The logged messages are logged with the `ILogger` interface.</span></span>

### <a name="azure-application-insights"></a><span data-ttu-id="83981-506">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="83981-506">Azure Application Insights</span></span>

<span data-ttu-id="83981-507">Il pacchetto del provider [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive i log per [applicazione Azure informazioni dettagliate](/azure/azure-monitor/app/cloudservices).</span><span class="sxs-lookup"><span data-stu-id="83981-507">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span></span> <span data-ttu-id="83981-508">Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi.</span><span class="sxs-lookup"><span data-stu-id="83981-508">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="83981-509">Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="83981-509">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="83981-510">Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83981-510">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="83981-511">Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.</span><span class="sxs-lookup"><span data-stu-id="83981-511">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="83981-512">Il pacchetto [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) è per ASP.NET 4. x, non ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83981-512">The [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package is for ASP.NET 4.x, not ASP.NET Core.</span></span>

<span data-ttu-id="83981-513">Per altre informazioni, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-513">For more information, see the following resources:</span></span>

* [<span data-ttu-id="83981-514">Panoramica di Application Insights</span><span class="sxs-lookup"><span data-stu-id="83981-514">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="83981-515">[Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-515">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="83981-516">[ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="83981-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="83981-517">[Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="83981-517">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="83981-518">[Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="83981-518">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="83981-519">Provider di registrazione di terze parti</span><span class="sxs-lookup"><span data-stu-id="83981-519">Third-party logging providers</span></span>

<span data-ttu-id="83981-520">Framework di registrazione di terze parti che usano ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="83981-520">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="83981-521">[elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="83981-521">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="83981-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="83981-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="83981-523">[JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="83981-523">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="83981-524">[KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="83981-524">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="83981-525">[Log4net](https://logging.apache.org/log4net/) ([repository GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="83981-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="83981-526">[Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="83981-526">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="83981-527">[NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="83981-527">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="83981-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([repository GitHub](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span><span class="sxs-lookup"><span data-stu-id="83981-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub repo](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span></span>
* <span data-ttu-id="83981-529">[Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="83981-529">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="83981-530">[Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="83981-530">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="83981-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="83981-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="83981-532">Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="83981-532">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="83981-533">L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:</span><span class="sxs-lookup"><span data-stu-id="83981-533">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="83981-534">Aggiungere un pacchetto NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="83981-534">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="83981-535">Chiamare un `ILoggerFactory` metodo di estensione fornito dal framework di registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-535">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="83981-536">Per altre informazioni, vedere la documentazione di ogni provider.</span><span class="sxs-lookup"><span data-stu-id="83981-536">For more information, see each provider's documentation.</span></span> <span data-ttu-id="83981-537">I provider di registrazione di terze parti non sono supportati da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="83981-537">Third-party logging providers aren't supported by Microsoft.</span></span>

<a name="nhca"></a>

## <a name="non-host-console-app"></a><span data-ttu-id="83981-538">App console non host</span><span class="sxs-lookup"><span data-stu-id="83981-538">Non-host console app</span></span>

<span data-ttu-id="83981-539">Per un esempio di come usare l'host generico in un'app console non Web, vedere il file *Program.cs* dell' [app di esempio attività in background](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( <xref:fundamentals/host/hosted-services> ).</span><span class="sxs-lookup"><span data-stu-id="83981-539">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="83981-540">Il codice di registrazione per le app senza host generico differisce per il modo in cui vengono [aggiunti i provider](#add-providers) e [creati i logger](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="83981-540">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> 

### <a name="logging-providers"></a><span data-ttu-id="83981-541">Provider di registrazione</span><span class="sxs-lookup"><span data-stu-id="83981-541">Logging providers</span></span>

<span data-ttu-id="83981-542">In un'app console non host chiamare il metodo di estensione `Add{provider name}` del provider durante la creazione di un oggetto `LoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="83981-542">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a><span data-ttu-id="83981-543">Creare log</span><span class="sxs-lookup"><span data-stu-id="83981-543">Create logs</span></span>

<span data-ttu-id="83981-544">Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="83981-544">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="83981-545">Utilizzare `LoggerFactory` per creare un oggetto `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="83981-545">Use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="83981-546">Nell'esempio seguente viene creato un logger con `LoggingConsoleApp.Program` come categoria.</span><span class="sxs-lookup"><span data-stu-id="83981-546">The following example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

<span data-ttu-id="83981-547">Negli esempi di ASP.NET CORE seguenti, il logger viene usato per creare log con `Information` come livello.</span><span class="sxs-lookup"><span data-stu-id="83981-547">In the following ASP.NET CORE examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="83981-548">Il *livello* del log indica la gravità dell'evento registrato.</span><span class="sxs-lookup"><span data-stu-id="83981-548">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

<span data-ttu-id="83981-549">I [livelli](#log-level) e le [categorie](#log-category) sono descritti in modo più dettagliato in questo documento.</span><span class="sxs-lookup"><span data-stu-id="83981-549">[Levels](#log-level) and [categories](#log-category) are explained in more detail in this document.</span></span>

<a name="lhc"></a>

## <a name="log-during-host-construction"></a><span data-ttu-id="83981-550">Registra durante la costruzione dell'host</span><span class="sxs-lookup"><span data-stu-id="83981-550">Log during host construction</span></span>

<span data-ttu-id="83981-551">La registrazione durante la costruzione dell'host non è supportata direttamente.</span><span class="sxs-lookup"><span data-stu-id="83981-551">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="83981-552">Tuttavia, è possibile usare un logger separato.</span><span class="sxs-lookup"><span data-stu-id="83981-552">However, a separate logger can be used.</span></span> <span data-ttu-id="83981-553">Nell'esempio seguente viene usato un logger [Serilog](https://serilog.net/) per eseguire l'accesso `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="83981-553">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="83981-554">`AddSerilog`Usa la configurazione statica specificata in `Log.Logger` :</span><span class="sxs-lookup"><span data-stu-id="83981-554">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a><span data-ttu-id="83981-555">Configurare un servizio che dipende da ILogger</span><span class="sxs-lookup"><span data-stu-id="83981-555">Configure a service that depends on ILogger</span></span>

<span data-ttu-id="83981-556">L'inserimento di un logger nel costruttore `Startup` è possibile nelle versioni precedenti di ASP.NET Core perché viene creato un contenitore di inserimento delle dipendenze separato per l'host Web.</span><span class="sxs-lookup"><span data-stu-id="83981-556">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="83981-557">Per informazioni sul motivo per cui viene creato un solo contenitore per l'host generico, vedere l'[annuncio relativo alle modifiche di rilievo](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="83981-557">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="83981-558">Per configurare un servizio da cui dipende `ILogger<T>` , utilizzare l'inserimento del costruttore o fornire un metodo factory.</span><span class="sxs-lookup"><span data-stu-id="83981-558">To configure a service that depends on `ILogger<T>`, use constructor injection or provide a factory method.</span></span> <span data-ttu-id="83981-559">L'approccio con il metodo factory è consigliato solo se non sono disponibili altre opzioni.</span><span class="sxs-lookup"><span data-stu-id="83981-559">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="83981-560">Si consideri, ad esempio, un servizio che necessita `ILogger<T>` di un'istanza fornita da di:</span><span class="sxs-lookup"><span data-stu-id="83981-560">For example, consider a service that needs an `ILogger<T>` instance provided by DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="83981-561">Il codice evidenziato precedente è una [funzione che viene](/dotnet/api/system.func-2) eseguita la prima volta che il contenitore di inserimento di dipendenze deve costruire un'istanza di `MyService` .</span><span class="sxs-lookup"><span data-stu-id="83981-561">The preceding highlighted code is a [Func](/dotnet/api/system.func-2) that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="83981-562">È possibile accedere a uno dei servizi registrati in questo modo.</span><span class="sxs-lookup"><span data-stu-id="83981-562">You can access any of the registered services in this way.</span></span>

<a name="clms"></a>

## <a name="create-logs-in-main"></a><span data-ttu-id="83981-563">Creare log in Main</span><span class="sxs-lookup"><span data-stu-id="83981-563">Create logs in Main</span></span>

<span data-ttu-id="83981-564">Il codice seguente esegue l'accesso `Main` ottenendo un' `ILogger` istanza di dopo la compilazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="83981-564">The following code logs in `Main` by getting an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a><span data-ttu-id="83981-565">Creare log nella classe Startup</span><span class="sxs-lookup"><span data-stu-id="83981-565">Create logs in Startup</span></span>

<span data-ttu-id="83981-566">Il codice seguente scrive i log in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="83981-566">The following code writes logs in `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

<span data-ttu-id="83981-567">La scrittura di log prima del completamento della configurazione del contenitore di inserimento delle dipendenze nel metodo `Startup.ConfigureServices` non è supportata:</span><span class="sxs-lookup"><span data-stu-id="83981-567">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="83981-568">L'inserimento del logger nel costruttore `Startup` non è supportato.</span><span class="sxs-lookup"><span data-stu-id="83981-568">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="83981-569">L'inserimento del logger nella firma del metodo `Startup.ConfigureServices` non è supportato</span><span class="sxs-lookup"><span data-stu-id="83981-569">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="83981-570">Questa restrizione è dovuta al fatto che la registrazione dipende dall'inserimento delle dipendenze e dalla configurazione, che a sua volta dipende dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83981-570">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="83981-571">Il contenitore di inserimento delle dipendenze non viene configurato finché il metodo `ConfigureServices` non è completato.</span><span class="sxs-lookup"><span data-stu-id="83981-571">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="83981-572">Per informazioni sulla configurazione di un servizio che dipende da `ILogger<T>` o perché un inserimento del costruttore di un logger in è stato eseguito `Startup` nelle versioni precedenti, vedere [configurare un servizio che dipende da ILogger](#csdi)</span><span class="sxs-lookup"><span data-stu-id="83981-572">For information on configuring a service that depends on `ILogger<T>` or why constructor injection of a logger into `Startup` worked in earlier versions, see [Configure a service that depends on ILogger](#csdi)</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="83981-573">Evitare l'uso di metodi logger asincroni</span><span class="sxs-lookup"><span data-stu-id="83981-573">No asynchronous logger methods</span></span>

<span data-ttu-id="83981-574">La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="83981-574">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="83981-575">Se un archivio dati di registrazione è lento, non scriverlo direttamente.</span><span class="sxs-lookup"><span data-stu-id="83981-575">If a logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="83981-576">Si consiglia di scrivere i messaggi di log in un archivio veloce inizialmente, quindi di spostarli nell'archivio lento in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="83981-576">Consider writing the log messages to a fast store initially, then moving them to the slow store later.</span></span> <span data-ttu-id="83981-577">Ad esempio, quando si esegue la registrazione in SQL Server, non farlo direttamente in un `Log` metodo, perché i `Log` metodi sono sincroni.</span><span class="sxs-lookup"><span data-stu-id="83981-577">For example, when logging to SQL Server, don't do so directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="83981-578">Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="83981-578">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="83981-579">Per altre informazioni, vedere [questo](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema di GitHub.</span><span class="sxs-lookup"><span data-stu-id="83981-579">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a><span data-ttu-id="83981-580">Modificare i livelli di registrazione in un'app in esecuzione</span><span class="sxs-lookup"><span data-stu-id="83981-580">Change log levels in a running app</span></span>

<span data-ttu-id="83981-581">L'API di registrazione non include uno scenario per modificare i livelli di registrazione mentre un'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="83981-581">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="83981-582">Alcuni provider di configurazione, tuttavia, sono in grado di ricaricare la configurazione, operazione che ha effetto immediato sulla configurazione della registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-582">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="83981-583">Per impostazione predefinita, ad esempio, il [provider di configurazione file](xref:fundamentals/configuration/index#file-configuration-provider)ricarica la configurazione di registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-583">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), reloads logging configuration by default.</span></span> <span data-ttu-id="83981-584">Se la configurazione viene modificata nel codice mentre un'app è in esecuzione, l'app può chiamare [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) per aggiornare la configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-584">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

## <a name="ilogger-and-iloggerfactory"></a><span data-ttu-id="83981-585">ILogger e ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="83981-585">ILogger and ILoggerFactory</span></span>

<span data-ttu-id="83981-586">Le <xref:Microsoft.Extensions.Logging.ILogger%601> <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfacce e e le implementazioni di sono incluse nel .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="83981-586">The <xref:Microsoft.Extensions.Logging.ILogger%601> and <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces and implementations are included in the .NET Core SDK.</span></span> <span data-ttu-id="83981-587">Sono disponibili anche nei pacchetti NuGet seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-587">They are also available in the following NuGet packages:</span></span>  

* <span data-ttu-id="83981-588">Le interfacce si trovano in [Microsoft. Extensions. Logging. abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="83981-588">The interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span></span>
* <span data-ttu-id="83981-589">Le implementazioni predefinite si trovano in [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="83981-589">The default implementations are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a><span data-ttu-id="83981-590">Applicare le regole di filtro del log nel codice</span><span class="sxs-lookup"><span data-stu-id="83981-590">Apply log filter rules in code</span></span>

<span data-ttu-id="83981-591">L'approccio preferito per impostare le regole di filtro del log consiste nell'utilizzare la [configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="83981-591">The preferred approach for setting log filter rules is by using [Configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="83981-592">L'esempio seguente illustra come registrare le regole di filtro nel codice:</span><span class="sxs-lookup"><span data-stu-id="83981-592">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

<span data-ttu-id="83981-593">`logging.AddFilter("System", LogLevel.Debug)`Specifica la `System` categoria e il livello di registrazione `Debug` .</span><span class="sxs-lookup"><span data-stu-id="83981-593">`logging.AddFilter("System", LogLevel.Debug)` specifies the `System` category and log level `Debug`.</span></span> <span data-ttu-id="83981-594">Il filtro viene applicato a tutti i provider perché non è stato configurato un provider specifico.</span><span class="sxs-lookup"><span data-stu-id="83981-594">The filter is applied to all providers because a specific provider was not configured.</span></span>

<span data-ttu-id="83981-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`Specifica</span><span class="sxs-lookup"><span data-stu-id="83981-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` specifies:</span></span>

* <span data-ttu-id="83981-596">`Debug`Provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-596">The `Debug` logging provider.</span></span>
* <span data-ttu-id="83981-597">Livello `Information` di registrazione e superiore.</span><span class="sxs-lookup"><span data-stu-id="83981-597">Log level `Information` and higher.</span></span>
* <span data-ttu-id="83981-598">Tutte le categorie che iniziano con `"Microsoft"` .</span><span class="sxs-lookup"><span data-stu-id="83981-598">All categories starting with `"Microsoft"`.</span></span>

## <a name="create-a-custom-logger"></a><span data-ttu-id="83981-599">Creare un logger personalizzato</span><span class="sxs-lookup"><span data-stu-id="83981-599">Create a custom logger</span></span>

<span data-ttu-id="83981-600">Per aggiungere un logger personalizzato, aggiungere un oggetto <xref:Microsoft.Extensions.Logging.ILoggerProvider> con <xref:Microsoft.Extensions.Logging.ILoggerFactory> :</span><span class="sxs-lookup"><span data-stu-id="83981-600">To add a custom logger, add an <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

<span data-ttu-id="83981-601">`ILoggerProvider`Crea una o più `ILogger` istanze di.</span><span class="sxs-lookup"><span data-stu-id="83981-601">The `ILoggerProvider` creates one or more `ILogger` instances.</span></span> <span data-ttu-id="83981-602">Le `ILogger` istanze vengono utilizzate dal Framework per registrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="83981-602">The `ILogger` instances are used by the framework to log the information.</span></span>

### <a name="sample-custom-logger-configuration"></a><span data-ttu-id="83981-603">Configurazione del logger personalizzato di esempio</span><span class="sxs-lookup"><span data-stu-id="83981-603">Sample custom logger configuration</span></span>

<span data-ttu-id="83981-604">L'esempio:</span><span class="sxs-lookup"><span data-stu-id="83981-604">The sample:</span></span>

* <span data-ttu-id="83981-605">È progettato per essere un esempio molto semplice che imposta il colore della console di log in base all'ID evento e al livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-605">Is designed to be a very basic sample that sets the color of the log console by event ID and log level.</span></span> <span data-ttu-id="83981-606">In genere i logger non cambiano in base all'ID evento e non sono specifici del livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-606">Loggers generally don't change by event ID and are not specific to log level.</span></span>
* <span data-ttu-id="83981-607">Crea diverse voci della console colori per ogni livello di log e ID evento usando il tipo di configurazione seguente:</span><span class="sxs-lookup"><span data-stu-id="83981-607">Creates different color console entries per log level and event ID using the following configuration type:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

<span data-ttu-id="83981-608">Il codice precedente imposta il livello predefinito su `Warning` e il colore su `Yellow` .</span><span class="sxs-lookup"><span data-stu-id="83981-608">The preceding code sets the default level to `Warning` and the color to `Yellow`.</span></span> <span data-ttu-id="83981-609">Se `EventId` è impostato su 0, tutti gli eventi vengono registrati.</span><span class="sxs-lookup"><span data-stu-id="83981-609">If the `EventId` is set to 0, we will log all events.</span></span>

### <a name="create-the-custom-logger"></a><span data-ttu-id="83981-610">Creare il logger personalizzato</span><span class="sxs-lookup"><span data-stu-id="83981-610">Create the custom logger</span></span>

<span data-ttu-id="83981-611">Il `ILogger` nome della categoria di implementazione è in genere l'origine di registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-611">The `ILogger` implementation category name is typically the logging source.</span></span> <span data-ttu-id="83981-612">Ad esempio, il tipo in cui viene creato il logger:</span><span class="sxs-lookup"><span data-stu-id="83981-612">For example, the type where the logger is created:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

<span data-ttu-id="83981-613">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="83981-613">The preceding code:</span></span>

* <span data-ttu-id="83981-614">Crea un'istanza del logger per ogni nome di categoria.</span><span class="sxs-lookup"><span data-stu-id="83981-614">Creates a logger instance per category name.</span></span>
* <span data-ttu-id="83981-615">Archivia `logLevel == _config.LogLevel` `IsEnabled` , quindi ognuno `logLevel` ha un logger univoco.</span><span class="sxs-lookup"><span data-stu-id="83981-615">Checks `logLevel == _config.LogLevel` in `IsEnabled`, so each `logLevel` has a unique logger.</span></span> <span data-ttu-id="83981-616">In genere, i logger devono essere abilitati anche per tutti i livelli di log più elevati:</span><span class="sxs-lookup"><span data-stu-id="83981-616">Generally, loggers should also be enabled for all higher log levels:</span></span>

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a><span data-ttu-id="83981-617">Creare il LoggerProvider personalizzato</span><span class="sxs-lookup"><span data-stu-id="83981-617">Create the custom LoggerProvider</span></span>

<span data-ttu-id="83981-618">`LoggerProvider`È la classe che crea le istanze del logger.</span><span class="sxs-lookup"><span data-stu-id="83981-618">The `LoggerProvider` is the class that creates the logger instances.</span></span> <span data-ttu-id="83981-619">Potrebbe non essere necessario creare un'istanza del logger per ogni categoria, ma questa operazione è utile per alcuni logger, ad esempio NLog o log4net.</span><span class="sxs-lookup"><span data-stu-id="83981-619">Maybe it is not needed to create a logger instance per category, but this makes sense for some Loggers, like NLog or log4net.</span></span> <span data-ttu-id="83981-620">In questo modo è anche possibile scegliere diverse destinazioni di output di registrazione per categoria, se necessario:</span><span class="sxs-lookup"><span data-stu-id="83981-620">Doing this you are also able to choose different logging output targets per category if needed:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

<span data-ttu-id="83981-621">Nel codice precedente <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> Crea una singola istanza del `ColorConsoleLogger` nome per categoria e la archivia in [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) .</span><span class="sxs-lookup"><span data-stu-id="83981-621">In the preceding code, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> creates a single instance of the `ColorConsoleLogger` per category name and stores it in the [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2);</span></span>

### <a name="usage-and-registration-of-the-custom-logger"></a><span data-ttu-id="83981-622">Utilizzo e registrazione del logger personalizzato</span><span class="sxs-lookup"><span data-stu-id="83981-622">Usage and registration of the custom logger</span></span>

<span data-ttu-id="83981-623">Registrare il logger in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="83981-623">Register the logger in the `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

<span data-ttu-id="83981-624">Per il codice precedente, fornire almeno un metodo di estensione per `ILoggerFactory` :</span><span class="sxs-lookup"><span data-stu-id="83981-624">For the preceding code, provide at least one extension method for the `ILoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="83981-625">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="83981-625">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
* <span data-ttu-id="83981-626">I bug di registrazione devono essere creati nel repository [github.com/dotnet/Runtime/](https://github.com/dotnet/runtime/issues) .</span><span class="sxs-lookup"><span data-stu-id="83981-626">Logging bugs should be created in the [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) repo.</span></span>
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83981-627">Di [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="83981-627">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="83981-628">.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti.</span><span class="sxs-lookup"><span data-stu-id="83981-628">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="83981-629">Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.</span><span class="sxs-lookup"><span data-stu-id="83981-629">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="83981-630">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="83981-630">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="83981-631">Aggiungere provider</span><span class="sxs-lookup"><span data-stu-id="83981-631">Add providers</span></span>

<span data-ttu-id="83981-632">Un provider di registrazione visualizza o archivia i log.</span><span class="sxs-lookup"><span data-stu-id="83981-632">A logging provider displays or stores logs.</span></span> <span data-ttu-id="83981-633">Il provider Console, ad esempio, visualizza i log nella console, mentre il provider Azure Application Insights li archivia in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="83981-633">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="83981-634">I log possono essere inviati a più destinazioni aggiungendo più provider.</span><span class="sxs-lookup"><span data-stu-id="83981-634">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="83981-635">Per aggiungere un provider, chiamare il metodo di estensione `Add{provider name}` del provider in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="83981-635">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="83981-636">Il codice precedente richiede riferimenti a `Microsoft.Extensions.Logging` e `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="83981-636">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="83981-637">Il modello di progetto predefinito chiama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, che aggiunge i provider di registrazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-637">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="83981-638">Console</span><span class="sxs-lookup"><span data-stu-id="83981-638">Console</span></span>
* <span data-ttu-id="83981-639">Debug</span><span class="sxs-lookup"><span data-stu-id="83981-639">Debug</span></span>
* <span data-ttu-id="83981-640">EventSource (a partire da ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="83981-640">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="83981-641">Se si usa `CreateDefaultBuilder`, è possibile sostituire i provider predefiniti con quelli di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="83981-641">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="83981-642">Chiamare <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e aggiungere i provider desiderati.</span><span class="sxs-lookup"><span data-stu-id="83981-642">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="83981-643">Altre informazioni sui [provider di registrazione predefiniti](#built-in-logging-providers) e sui [provider di registrazione di terze parti](#third-party-logging-providers) vengono fornite più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="83981-643">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="83981-644">Creare log</span><span class="sxs-lookup"><span data-stu-id="83981-644">Create logs</span></span>

<span data-ttu-id="83981-645">Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="83981-645">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="83981-646">In un'app Web o in un servizio ospitato, ottenere un oggetto `ILogger` da un inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="83981-646">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="83981-647">Nelle app console non host, usare `LoggerFactory` per creare un oggetto `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="83981-647">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="83981-648">L'esempio seguente di ASP.NET Core crea un logger con `TodoApiSample.Pages.AboutModel` come categoria.</span><span class="sxs-lookup"><span data-stu-id="83981-648">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="83981-649">La *categoria* del log è una stringa associata a ogni log.</span><span class="sxs-lookup"><span data-stu-id="83981-649">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="83981-650">L'istanza di `ILogger<T>` fornita dall'inserimento delle dipendenze crea log con nome completo di tipo `T` come categoria.</span><span class="sxs-lookup"><span data-stu-id="83981-650">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="83981-651">Negli esempi seguenti di ASP.NET Core e app console, il logger viene usato per creare log con `Information` come livello.</span><span class="sxs-lookup"><span data-stu-id="83981-651">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="83981-652">Il *livello* del log indica la gravità dell'evento registrato.</span><span class="sxs-lookup"><span data-stu-id="83981-652">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="83981-653">[Livelli](#log-level) e [categorie](#log-category) vengono illustrati in modo dettagliato più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="83981-653">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="83981-654">Creare log nella classe Startup</span><span class="sxs-lookup"><span data-stu-id="83981-654">Create logs in Startup</span></span>

<span data-ttu-id="83981-655">Per scrivere log nella classe `Startup`, includere un parametro `ILogger` nella firma del costruttore:</span><span class="sxs-lookup"><span data-stu-id="83981-655">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="83981-656">Creare log nella classe Program</span><span class="sxs-lookup"><span data-stu-id="83981-656">Create logs in the Program class</span></span>

<span data-ttu-id="83981-657">Per scrivere log nella classe `Program`, ottenere un'istanza di `ILogger` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="83981-657">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="83981-658">La registrazione durante la costruzione dell'host non è supportata direttamente.</span><span class="sxs-lookup"><span data-stu-id="83981-658">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="83981-659">Tuttavia, è possibile usare un logger separato.</span><span class="sxs-lookup"><span data-stu-id="83981-659">However, a separate logger can be used.</span></span> <span data-ttu-id="83981-660">Nell'esempio seguente viene usato un logger [Serilog](https://serilog.net/) per eseguire l'accesso `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="83981-660">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="83981-661">`AddSerilog`Usa la configurazione statica specificata in `Log.Logger` :</span><span class="sxs-lookup"><span data-stu-id="83981-661">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="83981-662">Evitare l'uso di metodi logger asincroni</span><span class="sxs-lookup"><span data-stu-id="83981-662">No asynchronous logger methods</span></span>

<span data-ttu-id="83981-663">La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="83981-663">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="83981-664">Se l'archivio dati di registrazione è lento, non scrivere direttamente al suo interno.</span><span class="sxs-lookup"><span data-stu-id="83981-664">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="83981-665">Scrivere invece i messaggi di log prima in un archivio veloce e quindi spostarli nell'archivio lento in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="83981-665">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="83981-666">Ad esempio, se la registrazione viene eseguita in SQL Server, è preferibile non farlo direttamente in un metodo `Log`, poiché i metodi `Log` sono sincroni.</span><span class="sxs-lookup"><span data-stu-id="83981-666">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="83981-667">Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="83981-667">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="83981-668">Per altre informazioni, vedere [questo](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema di GitHub.</span><span class="sxs-lookup"><span data-stu-id="83981-668">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="83981-669">Configurazione</span><span class="sxs-lookup"><span data-stu-id="83981-669">Configuration</span></span>

<span data-ttu-id="83981-670">La configurazione dei provider di registrazione viene fornita da uno o più provider di configurazione:</span><span class="sxs-lookup"><span data-stu-id="83981-670">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="83981-671">Formati di file (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="83981-671">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="83981-672">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="83981-672">Command-line arguments.</span></span>
* <span data-ttu-id="83981-673">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="83981-673">Environment variables.</span></span>
* <span data-ttu-id="83981-674">Oggetti .NET in memoria.</span><span class="sxs-lookup"><span data-stu-id="83981-674">In-memory .NET objects.</span></span>
* <span data-ttu-id="83981-675">Archiviazione con [Secret Manager](xref:security/app-secrets) senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="83981-675">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="83981-676">Un archivio utente non crittografato, come [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="83981-676">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="83981-677">Provider personalizzati (installati o creati).</span><span class="sxs-lookup"><span data-stu-id="83981-677">Custom providers (installed or created).</span></span>

<span data-ttu-id="83981-678">Ad esempio, la configurazione di registrazione viene comunemente fornita dalla sezione `Logging` del file di impostazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-678">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="83981-679">L'esempio seguente mostra il contenuto di un file *appsettings.Development.json* tipico:</span><span class="sxs-lookup"><span data-stu-id="83981-679">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="83981-680">La proprietà `Logging` può avere le proprietà `LogLevel` e quella del provider di log (nell'esempio, Console).</span><span class="sxs-lookup"><span data-stu-id="83981-680">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="83981-681">La proprietà `LogLevel` in `Logging` specifica il [livello](#log-level) minimo per la registrazione per determinate categorie.</span><span class="sxs-lookup"><span data-stu-id="83981-681">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="83981-682">Nell'esempio, le categorie `System` e `Microsoft` eseguono la registrazione al livello `Information`, mentre tutte le altre la eseguono al livello `Debug`.</span><span class="sxs-lookup"><span data-stu-id="83981-682">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="83981-683">Altre proprietà in `Logging` specificano i provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-683">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="83981-684">L'esempio si riferisce al provider Console.</span><span class="sxs-lookup"><span data-stu-id="83981-684">The example is for the Console provider.</span></span> <span data-ttu-id="83981-685">Se un provider supporta gli [ambiti del log](#log-scopes), `IncludeScopes` indica se sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="83981-685">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="83981-686">Una proprietà del provider (ad esempio `Console` nell'esempio) può specificare anche una proprietà `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="83981-686">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="83981-687">`LogLevel` in un provider specifica i livelli di registrazione per tale provider.</span><span class="sxs-lookup"><span data-stu-id="83981-687">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="83981-688">Se i livelli sono specificati in `Logging.{providername}.LogLevel`, eseguono l'override di eventuali valori impostati in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="83981-688">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span> <span data-ttu-id="83981-689">Si consideri, ad esempio, il codice JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="83981-689">For example, consider the following JSON:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<span data-ttu-id="83981-690">Nel codice JSON precedente, le `Console` impostazioni del provider eseguono l'override del livello di registrazione precedente (impostazione predefinita).</span><span class="sxs-lookup"><span data-stu-id="83981-690">In the preceding JSON, the `Console` provider settings overrides the preceding (default) log level.</span></span>

<span data-ttu-id="83981-691">L'API di registrazione non include uno scenario per modificare i livelli di registrazione mentre un'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="83981-691">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="83981-692">Alcuni provider di configurazione, tuttavia, sono in grado di ricaricare la configurazione, operazione che ha effetto immediato sulla configurazione della registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-692">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="83981-693">Ad esempio, il [provider di configurazione file](xref:fundamentals/configuration/index#file-configuration-provider), aggiunto da `CreateDefaultBuilder` per leggere i file di impostazioni, ricarica la configurazione di registrazione per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="83981-693">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="83981-694">Se la configurazione viene modificata nel codice mentre un'app è in esecuzione, l'app può chiamare [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) per aggiornare la configurazione di registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-694">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="83981-695">Per informazioni sull'implementazione dei provider di configurazione, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="83981-695">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="83981-696">Esempio di output di registrazione</span><span class="sxs-lookup"><span data-stu-id="83981-696">Sample logging output</span></span>

<span data-ttu-id="83981-697">Con il codice di esempio illustrato nella sezione precedente i log vengono visualizzati nella console quando l'app viene eseguita dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="83981-697">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="83981-698">Ecco un esempio di output della console:</span><span class="sxs-lookup"><span data-stu-id="83981-698">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="83981-699">I log precedenti sono stati generati inviando una richiesta HTTP Get all'app di esempio all'indirizzo `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="83981-699">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="83981-700">Ecco un esempio degli stessi log visualizzati nella finestra Debug quando si esegue l'app di esempio in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="83981-700">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="83981-701">I log creati dalle chiamate a `ILogger` illustrate nella sezione precedente iniziano con "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="83981-701">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="83981-702">I log che iniziano con le categorie "Microsoft" provengono dal codice del framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83981-702">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="83981-703">ASP.NET Core e il codice dell'applicazione usano la stessa API di registrazione e gli stessi provider.</span><span class="sxs-lookup"><span data-stu-id="83981-703">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="83981-704">Il resto di questo articolo spiega alcuni dettagli e le opzioni per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-704">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="83981-705">Pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="83981-705">NuGet packages</span></span>

<span data-ttu-id="83981-706">Le interfacce `ILogger` e `ILoggerFactory` si trovano in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e le loro implementazioni predefinite si trovano in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="83981-706">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="83981-707">Categoria di log</span><span class="sxs-lookup"><span data-stu-id="83981-707">Log category</span></span>

<span data-ttu-id="83981-708">Quando viene creato un oggetto `ILogger`, viene specificata la relativa *categoria*.</span><span class="sxs-lookup"><span data-stu-id="83981-708">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="83981-709">La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="83981-709">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="83981-710">La categoria può essere qualsiasi stringa, ma per convenzione si usa il nome della classe, ad esempio "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="83981-710">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="83981-711">Usare `ILogger<T>` per ottenere un'istanza di `ILogger` che usa il nome completo di tipo `T` come categoria:</span><span class="sxs-lookup"><span data-stu-id="83981-711">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="83981-712">Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="83981-712">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="83981-713">L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="83981-713">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="83981-714">Livello di registrazione</span><span class="sxs-lookup"><span data-stu-id="83981-714">Log level</span></span>

<span data-ttu-id="83981-715">Ogni log specifica un valore <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="83981-715">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="83981-716">Il livello di registrazione indica la gravità o l'importanza.</span><span class="sxs-lookup"><span data-stu-id="83981-716">The log level indicates the severity or importance.</span></span> <span data-ttu-id="83981-717">È ad esempio possibile scrivere un log `Information` quando un metodo termina normalmente e un log `Warning` quando un metodo restituisce un codice di stato *404 Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="83981-717">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="83981-718">Il codice seguente crea i log `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="83981-718">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="83981-719">Nel codice precedente, i `MyLogEvents.GetItem` parametri e `MyLogEvents.GetItemNotFound` sono l' [ID evento del log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="83981-719">In the preceding code, the `MyLogEvents.GetItem` and `MyLogEvents.GetItemNotFound` parameters are the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="83981-720">Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti.</span><span class="sxs-lookup"><span data-stu-id="83981-720">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="83981-721">I parametri del metodo sono descritti nella [sezione log message template](#lmt) in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="83981-721">The method parameters are explained in the [Log message template section](#lmt) in this article.</span></span>

<span data-ttu-id="83981-722">I metodi di registrazione che includono il livello nel nome del metodo (ad esempio `LogInformation` e `LogWarning`) sono [metodi di estensione per ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="83981-722">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="83981-723">Questi metodi chiamano un metodo `Log` che accetta un parametro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="83981-723">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="83981-724">È possibile chiamare il metodo `Log` direttamente anziché chiamare uno di questi metodi di estensione, ma la sintassi è relativamente complessa.</span><span class="sxs-lookup"><span data-stu-id="83981-724">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="83981-725">Per altre informazioni, vedere <xref:Microsoft.Extensions.Logging.ILogger> e il [codice sorgente delle estensioni del logger](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="83981-725">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="83981-726">ASP.NET Core definisce i livelli di registrazione seguenti, ordinati dal meno grave al più grave.</span><span class="sxs-lookup"><span data-stu-id="83981-726">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="83981-727">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="83981-727">Trace = 0</span></span>

  <span data-ttu-id="83981-728">Per informazioni in genere utili solo per il debug.</span><span class="sxs-lookup"><span data-stu-id="83981-728">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="83981-729">Questi messaggi possono contenere dati sensibili dell'applicazione e pertanto non devono essere abilitati in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="83981-729">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="83981-730">*Disabilitato per impostazione predefinita.*</span><span class="sxs-lookup"><span data-stu-id="83981-730">*Disabled by default.*</span></span>

* <span data-ttu-id="83981-731">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="83981-731">Debug = 1</span></span>

  <span data-ttu-id="83981-732">Per informazioni che possono essere utili per lo sviluppo e il debug.</span><span class="sxs-lookup"><span data-stu-id="83981-732">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="83981-733">Esempio: `Entering method Configure with flag set to true.` Abilitare i livelli di registrazione `Debug` nell'ambiente di produzione solo in fase di risoluzione dei problemi, per non fare aumentare eccessivamente il volume dei log.</span><span class="sxs-lookup"><span data-stu-id="83981-733">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="83981-734">Information = 2</span><span class="sxs-lookup"><span data-stu-id="83981-734">Information = 2</span></span>

  <span data-ttu-id="83981-735">Per tenere traccia del flusso generale dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-735">For tracking the general flow of the app.</span></span> <span data-ttu-id="83981-736">Queste registrazioni hanno in genere un valore a lungo termine.</span><span class="sxs-lookup"><span data-stu-id="83981-736">These logs typically have some long-term value.</span></span> <span data-ttu-id="83981-737">Esempio: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="83981-737">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="83981-738">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="83981-738">Warning = 3</span></span>

  <span data-ttu-id="83981-739">Per gli eventi imprevisti o anomali nel flusso dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-739">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="83981-740">Tali eventi potrebbero includere errori o altre condizioni che non provocano l'arresto dell'app, ma che potrebbe essere necessario analizzare.</span><span class="sxs-lookup"><span data-stu-id="83981-740">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="83981-741">Le eccezioni gestite sono una situazione comune in cui usare il livello di registrazione `Warning`.</span><span class="sxs-lookup"><span data-stu-id="83981-741">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="83981-742">Esempio: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="83981-742">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="83981-743">Error = 4</span><span class="sxs-lookup"><span data-stu-id="83981-743">Error = 4</span></span>

  <span data-ttu-id="83981-744">Per errori ed eccezioni che non possono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="83981-744">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="83981-745">Questi messaggi indicano un errore nell'operazione o nell'attività in corso (ad esempio la richiesta HTTP corrente), non un errore a livello di app.</span><span class="sxs-lookup"><span data-stu-id="83981-745">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="83981-746">Messaggio di registrazione di esempio:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="83981-746">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="83981-747">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="83981-747">Critical = 5</span></span>

  <span data-ttu-id="83981-748">Per gli errori che richiedono attenzione immediata.</span><span class="sxs-lookup"><span data-stu-id="83981-748">For failures that require immediate attention.</span></span> <span data-ttu-id="83981-749">Esempi: scenari di perdita di dati, spazio su disco insufficiente.</span><span class="sxs-lookup"><span data-stu-id="83981-749">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="83981-750">Usare il livello di registrazione per controllare la quantità di output di log scritto in un supporto di archiviazione specifico o in una finestra.</span><span class="sxs-lookup"><span data-stu-id="83981-750">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="83981-751">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="83981-751">For example:</span></span>

* <span data-ttu-id="83981-752">In produzione:</span><span class="sxs-lookup"><span data-stu-id="83981-752">In production:</span></span>
  * <span data-ttu-id="83981-753">La registrazione ai `Trace` `Information` livelli attraverso produce un volume elevato di messaggi di log dettagliati.</span><span class="sxs-lookup"><span data-stu-id="83981-753">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="83981-754">Per controllare i costi e non superare i limiti di archiviazione dei dati, accedere `Trace` `Information` ai messaggi di livello elevato a un archivio dati a basso costo.</span><span class="sxs-lookup"><span data-stu-id="83981-754">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="83981-755">L'accesso `Warning` ai `Critical` livelli in genere produce un minor numero di messaggi di log più piccoli.</span><span class="sxs-lookup"><span data-stu-id="83981-755">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="83981-756">Pertanto, i costi e i limiti di archiviazione in genere non rappresentano un problema, il che comporta una maggiore flessibilità di scelta dell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="83981-756">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="83981-757">Durante lo sviluppo:</span><span class="sxs-lookup"><span data-stu-id="83981-757">During development:</span></span>
  * <span data-ttu-id="83981-758">`Warning` `Critical` Consente di accedere ai messaggi nella console.</span><span class="sxs-lookup"><span data-stu-id="83981-758">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="83981-759">Aggiungere `Trace` `Information` messaggi durante la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="83981-759">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="83981-760">La sezione [Filtro dei log](#log-filtering) più avanti in questo articolo descrive come controllare quali livelli di registrazione gestisce un provider.</span><span class="sxs-lookup"><span data-stu-id="83981-760">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="83981-761">ASP.NET Core scrive log per gli eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="83981-761">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="83981-762">Gli esempi di log illustrati in precedenza in questo articolo escludono i log al di sotto del livello `Information`, quindi non vengono creati log di livello `Debug` o `Trace`.</span><span class="sxs-lookup"><span data-stu-id="83981-762">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="83981-763">Ecco un esempio di log della console prodotti eseguendo l'app di esempio configurata in modo da mostrare i log di livello `Debug`:</span><span class="sxs-lookup"><span data-stu-id="83981-763">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="83981-764">ID evento di registrazione</span><span class="sxs-lookup"><span data-stu-id="83981-764">Log event ID</span></span>

<span data-ttu-id="83981-765">Ogni log può specificare un *ID evento*.</span><span class="sxs-lookup"><span data-stu-id="83981-765">Each log can specify an *event ID*.</span></span> <span data-ttu-id="83981-766">A tale scopo, l'app di esempio usa una classe `LoggingEvents` definita a livello locale:</span><span class="sxs-lookup"><span data-stu-id="83981-766">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="83981-767">Un ID evento associa un set di eventi.</span><span class="sxs-lookup"><span data-stu-id="83981-767">An event ID associates a set of events.</span></span> <span data-ttu-id="83981-768">Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.</span><span class="sxs-lookup"><span data-stu-id="83981-768">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="83981-769">Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo.</span><span class="sxs-lookup"><span data-stu-id="83981-769">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="83981-770">Il provider Debug non visualizza gli ID evento.</span><span class="sxs-lookup"><span data-stu-id="83981-770">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="83981-771">Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:</span><span class="sxs-lookup"><span data-stu-id="83981-771">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="83981-772">Modello di messaggio di registrazione</span><span class="sxs-lookup"><span data-stu-id="83981-772">Log message template</span></span>

<span data-ttu-id="83981-773">Ogni log specifica un modello di messaggio.</span><span class="sxs-lookup"><span data-stu-id="83981-773">Each log specifies a message template.</span></span> <span data-ttu-id="83981-774">Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti.</span><span class="sxs-lookup"><span data-stu-id="83981-774">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="83981-775">Usare nomi per i segnaposto, non numeri.</span><span class="sxs-lookup"><span data-stu-id="83981-775">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="83981-776">L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="83981-776">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="83981-777">Nel codice seguente, si noti che i nomi dei parametri non sono in sequenza nel modello di messaggio:</span><span class="sxs-lookup"><span data-stu-id="83981-777">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="83981-778">Questo codice crea un messaggio di log con i valori dei parametri in sequenza:</span><span class="sxs-lookup"><span data-stu-id="83981-778">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="83981-779">Il framework di registrazione funziona in questo modo per consentire ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="83981-779">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="83981-780">Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato.</span><span class="sxs-lookup"><span data-stu-id="83981-780">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="83981-781">Queste informazioni consentono ai provider di registrazione di archiviare i valori dei parametri come campi.</span><span class="sxs-lookup"><span data-stu-id="83981-781">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="83981-782">Si supponga, ad esempio, che il metodo logger esegua chiamate simili alla seguente:</span><span class="sxs-lookup"><span data-stu-id="83981-782">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="83981-783">Se si inviano i log all'archiviazione tabelle di Azure, ogni entità tabella di Azure può avere le proprietà `ID` e `RequestTime`, il che semplifica le query sui dati dei log.</span><span class="sxs-lookup"><span data-stu-id="83981-783">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="83981-784">Una query può trovare tutti i log entro un determinato intervallo `RequestTime` senza che sia necessario analizzare il messaggio di testo per determinare l'intervallo di tempo.</span><span class="sxs-lookup"><span data-stu-id="83981-784">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="83981-785">Registrazione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="83981-785">Logging exceptions</span></span>

<span data-ttu-id="83981-786">I metodi logger dispongono di overload che consentono di passare un'eccezione, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="83981-786">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="83981-787">Provider diversi gestiscono le informazioni sulle eccezioni in modi diversi.</span><span class="sxs-lookup"><span data-stu-id="83981-787">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="83981-788">Ecco un esempio di output del provider Debug dal codice sopra riportato.</span><span class="sxs-lookup"><span data-stu-id="83981-788">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="83981-789">Filtro del log</span><span class="sxs-lookup"><span data-stu-id="83981-789">Log filtering</span></span>

<span data-ttu-id="83981-790">È possibile specificare un livello di registrazione minimo per un provider e una categoria specifici oppure per tutti i provider o tutte le categorie.</span><span class="sxs-lookup"><span data-stu-id="83981-790">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="83981-791">Tutti i log sotto il livello minimo non sono passati al provider, quindi non vengono visualizzati o archiviati.</span><span class="sxs-lookup"><span data-stu-id="83981-791">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="83981-792">Per eliminare tutti i log, specificare `LogLevel.None` come livello di registrazione minimo.</span><span class="sxs-lookup"><span data-stu-id="83981-792">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="83981-793">Il valore intero di `LogLevel.None` è 6, maggiore di `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="83981-793">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="83981-794">Creare regole di filtro nella configurazione</span><span class="sxs-lookup"><span data-stu-id="83981-794">Create filter rules in configuration</span></span>

<span data-ttu-id="83981-795">Il codice del modello di progetto chiama `CreateDefaultBuilder` per impostare la registrazione per i provider console, debug e EventSource (ASP.NET Core 2,2 o versione successiva).</span><span class="sxs-lookup"><span data-stu-id="83981-795">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="83981-796">Il metodo `CreateDefaultBuilder` imposta inoltre la registrazione per la ricerca della configurazione in una sezione `Logging`, come illustrato [in precedenza in questo articolo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="83981-796">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="83981-797">I dati di configurazione specificano i livelli di registrazione minimi in base al provider e alla categoria, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="83981-797">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="83981-798">Questo codice JSON crea sei regole di filtro, una per il provider Debug, quattro per il provider Console e una per tutti i provider.</span><span class="sxs-lookup"><span data-stu-id="83981-798">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="83981-799">Quando viene creato un oggetto `ILogger`, viene scelta una singola regola per ogni provider.</span><span class="sxs-lookup"><span data-stu-id="83981-799">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="83981-800">Regole di filtro nel codice</span><span class="sxs-lookup"><span data-stu-id="83981-800">Filter rules in code</span></span>

<span data-ttu-id="83981-801">L'esempio seguente illustra come registrare le regole di filtro nel codice:</span><span class="sxs-lookup"><span data-stu-id="83981-801">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="83981-802">Il secondo `AddFilter` specifica il provider Debug tramite il nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="83981-802">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="83981-803">Il primo `AddFilter` si applica a tutti i provider in quanto non consente di specificare un tipo di provider.</span><span class="sxs-lookup"><span data-stu-id="83981-803">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="83981-804">Applicazione delle regole di filtro</span><span class="sxs-lookup"><span data-stu-id="83981-804">How filtering rules are applied</span></span>

<span data-ttu-id="83981-805">I dati di configurazione e il codice `AddFilter` illustrato negli esempi precedenti creano le regole indicate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="83981-805">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="83981-806">I primi sei provengono dall'esempio di configurazione e gli ultimi due dall'esempio di codice.</span><span class="sxs-lookup"><span data-stu-id="83981-806">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="83981-807">Number</span><span class="sxs-lookup"><span data-stu-id="83981-807">Number</span></span> | <span data-ttu-id="83981-808">Provider</span><span class="sxs-lookup"><span data-stu-id="83981-808">Provider</span></span>      | <span data-ttu-id="83981-809">Categorie che iniziano con...</span><span class="sxs-lookup"><span data-stu-id="83981-809">Categories that begin with ...</span></span>          | <span data-ttu-id="83981-810">Livello di registrazione minimo</span><span class="sxs-lookup"><span data-stu-id="83981-810">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="83981-811">1</span><span class="sxs-lookup"><span data-stu-id="83981-811">1</span></span>      | <span data-ttu-id="83981-812">Debug</span><span class="sxs-lookup"><span data-stu-id="83981-812">Debug</span></span>         | <span data-ttu-id="83981-813">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="83981-813">All categories</span></span>                          | <span data-ttu-id="83981-814">Informazioni</span><span class="sxs-lookup"><span data-stu-id="83981-814">Information</span></span>       |
| <span data-ttu-id="83981-815">2</span><span class="sxs-lookup"><span data-stu-id="83981-815">2</span></span>      | <span data-ttu-id="83981-816">Console</span><span class="sxs-lookup"><span data-stu-id="83981-816">Console</span></span>       | <span data-ttu-id="83981-817">Microsoft. AspNetCore. Mvc. Razor . Interno</span><span class="sxs-lookup"><span data-stu-id="83981-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="83981-818">Avviso</span><span class="sxs-lookup"><span data-stu-id="83981-818">Warning</span></span>           |
| <span data-ttu-id="83981-819">3</span><span class="sxs-lookup"><span data-stu-id="83981-819">3</span></span>      | <span data-ttu-id="83981-820">Console</span><span class="sxs-lookup"><span data-stu-id="83981-820">Console</span></span>       | <span data-ttu-id="83981-821">Microsoft. AspNetCore. Mvc. Razor .Razor</span><span class="sxs-lookup"><span data-stu-id="83981-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="83981-822">Debug</span><span class="sxs-lookup"><span data-stu-id="83981-822">Debug</span></span>             |
| <span data-ttu-id="83981-823">4</span><span class="sxs-lookup"><span data-stu-id="83981-823">4</span></span>      | <span data-ttu-id="83981-824">Console</span><span class="sxs-lookup"><span data-stu-id="83981-824">Console</span></span>       | <span data-ttu-id="83981-825">Microsoft. AspNetCore. Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="83981-825">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="83981-826">Errore</span><span class="sxs-lookup"><span data-stu-id="83981-826">Error</span></span>             |
| <span data-ttu-id="83981-827">5</span><span class="sxs-lookup"><span data-stu-id="83981-827">5</span></span>      | <span data-ttu-id="83981-828">Console</span><span class="sxs-lookup"><span data-stu-id="83981-828">Console</span></span>       | <span data-ttu-id="83981-829">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="83981-829">All categories</span></span>                          | <span data-ttu-id="83981-830">Informazioni</span><span class="sxs-lookup"><span data-stu-id="83981-830">Information</span></span>       |
| <span data-ttu-id="83981-831">6</span><span class="sxs-lookup"><span data-stu-id="83981-831">6</span></span>      | <span data-ttu-id="83981-832">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="83981-832">All providers</span></span> | <span data-ttu-id="83981-833">Tutte le categorie</span><span class="sxs-lookup"><span data-stu-id="83981-833">All categories</span></span>                          | <span data-ttu-id="83981-834">Debug</span><span class="sxs-lookup"><span data-stu-id="83981-834">Debug</span></span>             |
| <span data-ttu-id="83981-835">7</span><span class="sxs-lookup"><span data-stu-id="83981-835">7</span></span>      | <span data-ttu-id="83981-836">Tutti i provider</span><span class="sxs-lookup"><span data-stu-id="83981-836">All providers</span></span> | <span data-ttu-id="83981-837">System</span><span class="sxs-lookup"><span data-stu-id="83981-837">System</span></span>                                  | <span data-ttu-id="83981-838">Debug</span><span class="sxs-lookup"><span data-stu-id="83981-838">Debug</span></span>             |
| <span data-ttu-id="83981-839">8</span><span class="sxs-lookup"><span data-stu-id="83981-839">8</span></span>      | <span data-ttu-id="83981-840">Debug</span><span class="sxs-lookup"><span data-stu-id="83981-840">Debug</span></span>         | <span data-ttu-id="83981-841">Microsoft</span><span class="sxs-lookup"><span data-stu-id="83981-841">Microsoft</span></span>                               | <span data-ttu-id="83981-842">Trace</span><span class="sxs-lookup"><span data-stu-id="83981-842">Trace</span></span>             |

<span data-ttu-id="83981-843">Quando viene creato un oggetto `ILogger`, l'oggetto `ILoggerFactory` seleziona una singola regola per ogni provider da applicare al logger.</span><span class="sxs-lookup"><span data-stu-id="83981-843">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="83981-844">Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate.</span><span class="sxs-lookup"><span data-stu-id="83981-844">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="83981-845">Tra le regole disponibili viene selezionata la regola più specifica possibile per ogni coppia di categoria e provider.</span><span class="sxs-lookup"><span data-stu-id="83981-845">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="83981-846">L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:</span><span class="sxs-lookup"><span data-stu-id="83981-846">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="83981-847">Selezionare tutte le regole corrispondenti al provider o al relativo alias.</span><span class="sxs-lookup"><span data-stu-id="83981-847">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="83981-848">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.</span><span class="sxs-lookup"><span data-stu-id="83981-848">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="83981-849">Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo.</span><span class="sxs-lookup"><span data-stu-id="83981-849">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="83981-850">Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.</span><span class="sxs-lookup"><span data-stu-id="83981-850">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="83981-851">Se sono selezionate più regole, scegliere l'**ultima**.</span><span class="sxs-lookup"><span data-stu-id="83981-851">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="83981-852">Se non è selezionata alcuna regola, usare `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="83981-852">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="83981-853">Con l'elenco di regole precedente, si supponga di creare un `ILogger` oggetto per la categoria "Microsoft. AspNetCore. Mvc. Razor .. Razor ViewEngine ":</span><span class="sxs-lookup"><span data-stu-id="83981-853">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="83981-854">Per il provider Debug si applicano le regole 1, 6 e 8.</span><span class="sxs-lookup"><span data-stu-id="83981-854">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="83981-855">La regola 8 è più specifica, così sarà quella selezionata.</span><span class="sxs-lookup"><span data-stu-id="83981-855">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="83981-856">Per il provider Console si applicano le regole 3, 4, 5 e 6.</span><span class="sxs-lookup"><span data-stu-id="83981-856">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="83981-857">La regola 3 è la più specifica.</span><span class="sxs-lookup"><span data-stu-id="83981-857">Rule 3 is most specific.</span></span>

<span data-ttu-id="83981-858">L'istanza di `ILogger` risultante invia i log di livello `Trace` o superiore al provider Debug.</span><span class="sxs-lookup"><span data-stu-id="83981-858">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="83981-859">I log di livello `Debug` o superiore vengono inviati al provider Console.</span><span class="sxs-lookup"><span data-stu-id="83981-859">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="83981-860">Alias dei provider</span><span class="sxs-lookup"><span data-stu-id="83981-860">Provider aliases</span></span>

<span data-ttu-id="83981-861">Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo.</span><span class="sxs-lookup"><span data-stu-id="83981-861">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="83981-862">Per i provider predefiniti, usare gli alias seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-862">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="83981-863">Console</span><span class="sxs-lookup"><span data-stu-id="83981-863">Console</span></span>
* <span data-ttu-id="83981-864">Debug</span><span class="sxs-lookup"><span data-stu-id="83981-864">Debug</span></span>
* <span data-ttu-id="83981-865">EventSource</span><span class="sxs-lookup"><span data-stu-id="83981-865">EventSource</span></span>
* <span data-ttu-id="83981-866">EventLog</span><span class="sxs-lookup"><span data-stu-id="83981-866">EventLog</span></span>
* <span data-ttu-id="83981-867">TraceSource</span><span class="sxs-lookup"><span data-stu-id="83981-867">TraceSource</span></span>
* <span data-ttu-id="83981-868">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="83981-868">AzureAppServicesFile</span></span>
* <span data-ttu-id="83981-869">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="83981-869">AzureAppServicesBlob</span></span>
* <span data-ttu-id="83981-870">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="83981-870">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="83981-871">Livello minimo predefinito</span><span class="sxs-lookup"><span data-stu-id="83981-871">Default minimum level</span></span>

<span data-ttu-id="83981-872">Esiste un'impostazione del livello minimo che diventa effettiva solo se non si applica alcuna regola di configurazione o codice per un provider e una categoria specifici.</span><span class="sxs-lookup"><span data-stu-id="83981-872">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="83981-873">L'esempio seguente illustra come impostare il livello minimo:</span><span class="sxs-lookup"><span data-stu-id="83981-873">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="83981-874">Se non si imposta in modo esplicito il livello minimo, il valore predefinito è `Information`, che indica che i log `Trace` e `Debug` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="83981-874">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="83981-875">Funzioni di filtro</span><span class="sxs-lookup"><span data-stu-id="83981-875">Filter functions</span></span>

<span data-ttu-id="83981-876">Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice.</span><span class="sxs-lookup"><span data-stu-id="83981-876">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="83981-877">Il codice della funzione ha accesso al tipo di provider, alla categoria e al livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-877">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="83981-878">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="83981-878">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="83981-879">Livelli e categorie di sistema</span><span class="sxs-lookup"><span data-stu-id="83981-879">System categories and levels</span></span>

<span data-ttu-id="83981-880">Di seguito sono elencate alcune categorie usate da ASP.NET Core ed Entity Framework Core, con note sui log previsti per ognuna:</span><span class="sxs-lookup"><span data-stu-id="83981-880">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="83981-881">Category</span><span class="sxs-lookup"><span data-stu-id="83981-881">Category</span></span>                            | <span data-ttu-id="83981-882">Note</span><span class="sxs-lookup"><span data-stu-id="83981-882">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="83981-883">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="83981-883">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="83981-884">Diagnostica generale di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83981-884">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="83981-885">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="83981-885">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="83981-886">Chiavi considerate, trovate e usate.</span><span class="sxs-lookup"><span data-stu-id="83981-886">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="83981-887">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="83981-887">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="83981-888">Host consentiti.</span><span class="sxs-lookup"><span data-stu-id="83981-888">Hosts allowed.</span></span> |
| <span data-ttu-id="83981-889">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="83981-889">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="83981-890">Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste.</span><span class="sxs-lookup"><span data-stu-id="83981-890">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="83981-891">Assembly di avvio dell'hosting caricati.</span><span class="sxs-lookup"><span data-stu-id="83981-891">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="83981-892">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="83981-892">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="83981-893">MVC e Razor diagnostica.</span><span class="sxs-lookup"><span data-stu-id="83981-893">MVC and Razor diagnostics.</span></span> <span data-ttu-id="83981-894">Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni.</span><span class="sxs-lookup"><span data-stu-id="83981-894">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="83981-895">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="83981-895">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="83981-896">Informazioni sulla corrispondenza di route.</span><span class="sxs-lookup"><span data-stu-id="83981-896">Route matching information.</span></span> |
| <span data-ttu-id="83981-897">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="83981-897">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="83981-898">Risposte di avvio, arresto e keep-alive per la connessione.</span><span class="sxs-lookup"><span data-stu-id="83981-898">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="83981-899">Informazioni sui certificati HTTPS.</span><span class="sxs-lookup"><span data-stu-id="83981-899">HTTPS certificate information.</span></span> |
| <span data-ttu-id="83981-900">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="83981-900">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="83981-901">File forniti.</span><span class="sxs-lookup"><span data-stu-id="83981-901">Files served.</span></span> |
| <span data-ttu-id="83981-902">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="83981-902">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="83981-903">Diagnostica generale di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="83981-903">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="83981-904">Attività e configurazione del database, rilevamento delle modifiche, migrazioni.</span><span class="sxs-lookup"><span data-stu-id="83981-904">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="83981-905">Ambiti dei log</span><span class="sxs-lookup"><span data-stu-id="83981-905">Log scopes</span></span>

 <span data-ttu-id="83981-906">Un *ambito* può raggruppare un set di operazioni logiche.</span><span class="sxs-lookup"><span data-stu-id="83981-906">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="83981-907">Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set.</span><span class="sxs-lookup"><span data-stu-id="83981-907">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="83981-908">Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.</span><span class="sxs-lookup"><span data-stu-id="83981-908">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="83981-909">Un ambito è un tipo `IDisposable` restituito dal metodo <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e viene mantenuto fino all'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="83981-909">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="83981-910">Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:</span><span class="sxs-lookup"><span data-stu-id="83981-910">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="83981-911">Il codice seguente abilita gli ambiti per il provider Console:</span><span class="sxs-lookup"><span data-stu-id="83981-911">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="83981-912">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="83981-912">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="83981-913">La configurazione dell'opzione del logger della console `IncludeScopes` è necessaria per abilitare la registrazione basata sull'ambito.</span><span class="sxs-lookup"><span data-stu-id="83981-913">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="83981-914">Per informazioni sulla configurazione, vedere la sezione [Configurazione](#configuration).</span><span class="sxs-lookup"><span data-stu-id="83981-914">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="83981-915">Ogni messaggio di registrazione include le informazioni sull'ambito:</span><span class="sxs-lookup"><span data-stu-id="83981-915">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="83981-916">Provider di registrazione predefiniti</span><span class="sxs-lookup"><span data-stu-id="83981-916">Built-in logging providers</span></span>

<span data-ttu-id="83981-917">ASP.NET Core include i provider seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-917">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="83981-918">Console</span><span class="sxs-lookup"><span data-stu-id="83981-918">Console</span></span>](#console-provider)
* [<span data-ttu-id="83981-919">Eseguire il debug</span><span class="sxs-lookup"><span data-stu-id="83981-919">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="83981-920">EventSource</span><span class="sxs-lookup"><span data-stu-id="83981-920">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="83981-921">EventLog</span><span class="sxs-lookup"><span data-stu-id="83981-921">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="83981-922">TraceSource</span><span class="sxs-lookup"><span data-stu-id="83981-922">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="83981-923">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="83981-923">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="83981-924">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="83981-924">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="83981-925">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="83981-925">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="83981-926">Per informazioni su StdOut e la registrazione debug con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="83981-926">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="83981-927">Provider Console</span><span class="sxs-lookup"><span data-stu-id="83981-927">Console provider</span></span>

<span data-ttu-id="83981-928">Il pacchetto del provider [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) invia l'output della registrazione alla console.</span><span class="sxs-lookup"><span data-stu-id="83981-928">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="83981-929">Per visualizzare l'output di registrazione del provider Console, aprire un prompt dei comandi nella cartella del progetto ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="83981-929">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="83981-930">Provider Debug</span><span class="sxs-lookup"><span data-stu-id="83981-930">Debug provider</span></span>

<span data-ttu-id="83981-931">Il pacchetto del provider [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) scrive l'output della registrazione usando la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chiamate del metodo `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="83981-931">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="83981-932">In Linux, questo provider scrive i log in */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="83981-932">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="83981-933">Provider origine evento</span><span class="sxs-lookup"><span data-stu-id="83981-933">Event Source provider</span></span>

<span data-ttu-id="83981-934">Il pacchetto del provider [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) scrive in un'origine evento multipiattaforma con il nome `Microsoft-Extensions-Logging` .</span><span class="sxs-lookup"><span data-stu-id="83981-934">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="83981-935">In Windows, il provider utilizza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="83981-935">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="83981-936">Il provider di origine eventi viene aggiunto automaticamente quando `CreateDefaultBuilder` viene chiamato per compilare l'host.</span><span class="sxs-lookup"><span data-stu-id="83981-936">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="83981-937">Usare l' [utilità PerfView](https://github.com/Microsoft/perfview) per raccogliere e visualizzare i log.</span><span class="sxs-lookup"><span data-stu-id="83981-937">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="83981-938">Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83981-938">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="83981-939">Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi**</span><span class="sxs-lookup"><span data-stu-id="83981-939">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="83981-940">(non dimenticare l'asterisco all'inizio della stringa).</span><span class="sxs-lookup"><span data-stu-id="83981-940">(Don't miss the asterisk at the start of the string.)</span></span>

![Provider aggiuntivi di PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="83981-942">Provider EventLog di Windows</span><span class="sxs-lookup"><span data-stu-id="83981-942">Windows EventLog provider</span></span>

<span data-ttu-id="83981-943">Il pacchetto di provider [Microsoft.Extensions.Logging.AventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) invia l'output del log al registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="83981-943">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="83981-944">Gli [overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) consentono di passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="83981-944">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="83981-945">Se `null` o non è specificato, vengono usate le impostazioni predefinite seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-945">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="83981-946">`LogName`: "Applicazione"</span><span class="sxs-lookup"><span data-stu-id="83981-946">`LogName`: "Application"</span></span>
* <span data-ttu-id="83981-947">`SourceName`: "Runtime .NET"</span><span class="sxs-lookup"><span data-stu-id="83981-947">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="83981-948">`MachineName`: Viene usato il nome del computer locale.</span><span class="sxs-lookup"><span data-stu-id="83981-948">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="83981-949">Gli eventi vengono registrati per un [livello di avviso e un livello superiore](#log-level).</span><span class="sxs-lookup"><span data-stu-id="83981-949">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="83981-950">Nell'esempio seguente viene impostato il livello di registrazione predefinito del registro eventi su <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="83981-950">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="83981-951">Provider TraceSource</span><span class="sxs-lookup"><span data-stu-id="83981-951">TraceSource provider</span></span>

<span data-ttu-id="83981-952">Il pacchetto di provider [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa le librerie e i provider <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="83981-952">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="83981-953">Gli [overload di AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) consentono di passare un cambio di origine e un listener di traccia.</span><span class="sxs-lookup"><span data-stu-id="83981-953">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="83981-954">Per usare questo provider, un'app deve essere in esecuzione in .NET Framework (invece che in .NET Core).</span><span class="sxs-lookup"><span data-stu-id="83981-954">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="83981-955">Il provider può instradare i messaggi a diversi [listener](/dotnet/framework/debug-trace-profile/trace-listeners), come <xref:System.Diagnostics.TextWriterTraceListener>, usato nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="83981-955">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="83981-956">Provider del Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="83981-956">Azure App Service provider</span></span>

<span data-ttu-id="83981-957">Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="83981-957">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="83981-958">Il pacchetto del provider non è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="83981-958">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="83981-959">Quando la destinazione è .NET Framework o il riferimento è il metapacchetto `Microsoft.AspNetCore.App`, aggiungere il pacchetto del provider al progetto.</span><span class="sxs-lookup"><span data-stu-id="83981-959">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="83981-960">Un overload di <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> consente di passare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="83981-960">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="83981-961">L'oggetto impostazioni può eseguire l'override delle impostazioni predefinite, ad esempio il modello di output di registrazione, il nome di BLOB e il limite di dimensioni di file.</span><span class="sxs-lookup"><span data-stu-id="83981-961">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="83981-962">Il *modello di output* è un modello di messaggio che viene applicato a tutti i log in aggiunta a quanto specificato quando si chiama un metodo `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="83981-962">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="83981-963">Quando si distribuisce un'app del servizio app, l'applicazione applica le impostazioni della sezione [Log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) della pagina **Servizio app** del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="83981-963">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="83981-964">Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-964">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="83981-965">**Registrazione applicazioni (file system)**</span><span class="sxs-lookup"><span data-stu-id="83981-965">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="83981-966">**Registrazione applicazione (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="83981-966">**Application Logging (Blob)**</span></span>

<span data-ttu-id="83981-967">Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*.</span><span class="sxs-lookup"><span data-stu-id="83981-967">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="83981-968">Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2.</span><span class="sxs-lookup"><span data-stu-id="83981-968">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="83981-969">Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="83981-969">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="83981-970">Il provider funziona solo quando il progetto viene eseguito nell'ambiente di Azure.</span><span class="sxs-lookup"><span data-stu-id="83981-970">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="83981-971">Non ha alcun effetto quando il progetto viene eseguito localmente, in quanto non scrive nulla in file locali o in archivi di sviluppo locali per i BLOB.</span><span class="sxs-lookup"><span data-stu-id="83981-971">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="83981-972">Flusso di registrazione di Azure</span><span class="sxs-lookup"><span data-stu-id="83981-972">Azure log streaming</span></span>

<span data-ttu-id="83981-973">Il flusso di registrazione di Azure consente di visualizzare l'attività di registrazione in tempo reale da:</span><span class="sxs-lookup"><span data-stu-id="83981-973">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="83981-974">Server applicazioni</span><span class="sxs-lookup"><span data-stu-id="83981-974">The app server</span></span>
* <span data-ttu-id="83981-975">Server Web</span><span class="sxs-lookup"><span data-stu-id="83981-975">The web server</span></span>
* <span data-ttu-id="83981-976">Traccia delle richieste non riuscite</span><span class="sxs-lookup"><span data-stu-id="83981-976">Failed request tracing</span></span>

<span data-ttu-id="83981-977">Per configurare il flusso di registrazione di Azure:</span><span class="sxs-lookup"><span data-stu-id="83981-977">To configure Azure log streaming:</span></span>

* <span data-ttu-id="83981-978">Passare alla pagina **Log del servizio app** dalla pagina del portale dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-978">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="83981-979">Impostare **Registrazione applicazioni (file system)** su **Attiva**.</span><span class="sxs-lookup"><span data-stu-id="83981-979">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="83981-980">Scegliere il livello di registrazione in **Livello**.</span><span class="sxs-lookup"><span data-stu-id="83981-980">Choose the log **Level**.</span></span> <span data-ttu-id="83981-981">Questa impostazione è valida solo per lo streaming dei log di Azure, non per altri provider di registrazione nell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-981">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="83981-982">Passare alla pagina **Flusso di registrazione** per visualizzare i messaggi dell'app.</span><span class="sxs-lookup"><span data-stu-id="83981-982">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="83981-983">I messaggi vengono registrati dall'app tramite l'interfaccia `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="83981-983">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="83981-984">Registrazione di traccia di Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="83981-984">Azure Application Insights trace logging</span></span>

<span data-ttu-id="83981-985">Il pacchetto di provider [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive log in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="83981-985">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="83981-986">Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi.</span><span class="sxs-lookup"><span data-stu-id="83981-986">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="83981-987">Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="83981-987">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="83981-988">Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83981-988">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="83981-989">Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.</span><span class="sxs-lookup"><span data-stu-id="83981-989">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="83981-990">Non usare il pacchetto [](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)Microsoft.ApplicationInsights.Web&mdash;, che è per ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="83981-990">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="83981-991">Per altre informazioni, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="83981-991">For more information, see the following resources:</span></span>

* [<span data-ttu-id="83981-992">Panoramica di Application Insights</span><span class="sxs-lookup"><span data-stu-id="83981-992">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="83981-993">[Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-993">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="83981-994">[ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="83981-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="83981-995">[Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="83981-995">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="83981-996">[Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="83981-996">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="83981-997">Provider di registrazione di terze parti</span><span class="sxs-lookup"><span data-stu-id="83981-997">Third-party logging providers</span></span>

<span data-ttu-id="83981-998">Framework di registrazione di terze parti che usano ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="83981-998">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="83981-999">[elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="83981-999">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="83981-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="83981-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="83981-1001">[JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="83981-1001">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="83981-1002">[KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="83981-1002">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="83981-1003">[Log4net](https://logging.apache.org/log4net/) ([repository GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="83981-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="83981-1004">[Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="83981-1004">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="83981-1005">[NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="83981-1005">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="83981-1006">[Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="83981-1006">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="83981-1007">[Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="83981-1007">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="83981-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="83981-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="83981-1009">Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="83981-1009">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="83981-1010">L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:</span><span class="sxs-lookup"><span data-stu-id="83981-1010">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="83981-1011">Aggiungere un pacchetto NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="83981-1011">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="83981-1012">Chiamare un `ILoggerFactory` metodo di estensione fornito dal framework di registrazione.</span><span class="sxs-lookup"><span data-stu-id="83981-1012">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="83981-1013">Per altre informazioni, vedere la documentazione di ogni provider.</span><span class="sxs-lookup"><span data-stu-id="83981-1013">For more information, see each provider's documentation.</span></span> <span data-ttu-id="83981-1014">I provider di registrazione di terze parti non sono supportati da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="83981-1014">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83981-1015">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="83981-1015">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
