---
title: Inserimento di dipendenze in controller in ASP.NET Core
author: ardalis
description: Informazioni su come i controller ASP.NET Core MVC richiedono le proprie dipendenze in modo esplicito tramite i rispettivi costruttori, usando l'inserimento delle dipendenze in ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
no-loc:
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 74c43e4f01e5b0d6923bf06f8859341c49088ef6
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635242"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="5ae3e-103">Inserimento di dipendenze in controller in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5ae3e-103">Dependency injection into controllers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5ae3e-104">Di [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="5ae3e-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="5ae3e-105">I controller ASP.NET Core MVC richiedono le proprie dipendenze in modo esplicito tramite costruttori.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="5ae3e-106">ASP.NET Core include il supporto predefinito per l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5ae3e-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5ae3e-107">L'inserimento delle dipendenze rende più facile testare e gestire le app.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="5ae3e-108">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5ae3e-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="5ae3e-109">Inserimento di costruttori</span><span class="sxs-lookup"><span data-stu-id="5ae3e-109">Constructor Injection</span></span>

<span data-ttu-id="5ae3e-110">I servizi vengono aggiunti come parametri del costruttore e il runtime risolve il servizio dal contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="5ae3e-111">In genere i servizi vengono definiti tramite interfacce.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="5ae3e-112">Si consideri ad esempio un'app che richiede l'ora corrente.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="5ae3e-113">L'interfaccia seguente espone il servizio `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="5ae3e-114">Il codice seguente implementa l'interfaccia `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="5ae3e-115">Aggiungere il servizio al contenitore di servizi:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="5ae3e-116">Per altre informazioni su <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, vedere le [durate dei servizi di inserimento delle dipendenze](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="5ae3e-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="5ae3e-117">Il codice seguente visualizza un messaggio di saluto all'utente in base all'orario:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="5ae3e-118">Eseguire l'app e verrà visualizzato un messaggio in base all'orario.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="5ae3e-119">Inserimento di azioni con FromServices</span><span class="sxs-lookup"><span data-stu-id="5ae3e-119">Action injection with FromServices</span></span>

<span data-ttu-id="5ae3e-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> consente di inserire un servizio direttamente in un metodo di azione senza usare l'inserimento del costruttore:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="5ae3e-121">Accedere alle impostazioni da un controller</span><span class="sxs-lookup"><span data-stu-id="5ae3e-121">Access settings from a controller</span></span>

<span data-ttu-id="5ae3e-122">L'accesso alle impostazioni di un'app o alle impostazioni di configurazione da un controller è un tipo di azione comune.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="5ae3e-123">Il *modello di opzioni* descritto in <xref:fundamentals/configuration/options> rappresenta l'approccio consigliato per gestire le impostazioni.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="5ae3e-124">In linea generale, non inserire direttamente <xref:Microsoft.Extensions.Configuration.IConfiguration> in un controller.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="5ae3e-125">Creare una classe che rappresenta le opzioni.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-125">Create a class that represents the options.</span></span> <span data-ttu-id="5ae3e-126">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-126">For example:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="5ae3e-127">Aggiungere la classe di configurazione alla raccolta di servizi:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="5ae3e-128">Configurare l'app per leggere le impostazioni da un file in formato JSON:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="5ae3e-129">Il codice seguente richiede le impostazioni `IOptions<SampleWebSettings>` dal contenitore dei servizi e le usa nel metodo `Index`:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="5ae3e-130">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5ae3e-130">Additional resources</span></span>

* <span data-ttu-id="5ae3e-131">Vedere <xref:mvc/controllers/testing> per informazioni su come semplificare il test del codice richiedendo in modo esplicito le dipendenze nei controller.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="5ae3e-132">[Sostituire il contenitore di inserimento delle dipendenze predefinito con un'implementazione di terze parti](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="5ae3e-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5ae3e-133">Di [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="5ae3e-133">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="5ae3e-134">I controller ASP.NET Core MVC richiedono le proprie dipendenze in modo esplicito tramite costruttori.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-134">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="5ae3e-135">ASP.NET Core include il supporto predefinito per l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5ae3e-135">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5ae3e-136">L'inserimento delle dipendenze rende più facile testare e gestire le app.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-136">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="5ae3e-137">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5ae3e-137">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="5ae3e-138">Inserimento di costruttori</span><span class="sxs-lookup"><span data-stu-id="5ae3e-138">Constructor Injection</span></span>

<span data-ttu-id="5ae3e-139">I servizi vengono aggiunti come parametri del costruttore e il runtime risolve il servizio dal contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-139">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="5ae3e-140">In genere i servizi vengono definiti tramite interfacce.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-140">Services are typically defined using interfaces.</span></span> <span data-ttu-id="5ae3e-141">Si consideri ad esempio un'app che richiede l'ora corrente.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-141">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="5ae3e-142">L'interfaccia seguente espone il servizio `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-142">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="5ae3e-143">Il codice seguente implementa l'interfaccia `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-143">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="5ae3e-144">Aggiungere il servizio al contenitore di servizi:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-144">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="5ae3e-145">Per altre informazioni su <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, vedere le [durate dei servizi di inserimento delle dipendenze](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="5ae3e-145">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="5ae3e-146">Il codice seguente visualizza un messaggio di saluto all'utente in base all'orario:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-146">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="5ae3e-147">Eseguire l'app e verrà visualizzato un messaggio in base all'orario.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-147">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="5ae3e-148">Inserimento di azioni con FromServices</span><span class="sxs-lookup"><span data-stu-id="5ae3e-148">Action injection with FromServices</span></span>

<span data-ttu-id="5ae3e-149"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> consente di inserire un servizio direttamente in un metodo di azione senza usare l'inserimento del costruttore:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-149">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="5ae3e-150">Accedere alle impostazioni da un controller</span><span class="sxs-lookup"><span data-stu-id="5ae3e-150">Access settings from a controller</span></span>

<span data-ttu-id="5ae3e-151">L'accesso alle impostazioni di un'app o alle impostazioni di configurazione da un controller è un tipo di azione comune.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-151">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="5ae3e-152">Il *modello di opzioni* descritto in <xref:fundamentals/configuration/options> rappresenta l'approccio consigliato per gestire le impostazioni.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-152">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="5ae3e-153">In linea generale, non inserire direttamente <xref:Microsoft.Extensions.Configuration.IConfiguration> in un controller.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-153">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="5ae3e-154">Creare una classe che rappresenta le opzioni.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-154">Create a class that represents the options.</span></span> <span data-ttu-id="5ae3e-155">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-155">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="5ae3e-156">Aggiungere la classe di configurazione alla raccolta di servizi:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-156">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="5ae3e-157">Configurare l'app per leggere le impostazioni da un file in formato JSON:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-157">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="5ae3e-158">Il codice seguente richiede le impostazioni `IOptions<SampleWebSettings>` dal contenitore dei servizi e le usa nel metodo `Index`:</span><span class="sxs-lookup"><span data-stu-id="5ae3e-158">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="5ae3e-159">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5ae3e-159">Additional resources</span></span>

* <span data-ttu-id="5ae3e-160">Vedere <xref:mvc/controllers/testing> per informazioni su come semplificare il test del codice richiedendo in modo esplicito le dipendenze nei controller.</span><span class="sxs-lookup"><span data-stu-id="5ae3e-160">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="5ae3e-161">[Sostituire il contenitore di inserimento delle dipendenze predefinito con un'implementazione di terze parti](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="5ae3e-161">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end