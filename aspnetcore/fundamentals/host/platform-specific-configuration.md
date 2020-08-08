---
title: Usare assembly di avvio dell'hosting in ASP.NET Core
author: rick-anderson
description: Informazioni su come migliorare un'app ASP.NET Core da un assembly esterno con un'implementazione IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 13728bca9d382bad39a85144ae9efd5b63a05dc4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017389"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="a6a61-103">Usare assembly di avvio dell'hosting in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6a61-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="a6a61-104">Di [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="a6a61-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a6a61-105">Un' <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementazione di (host Startup) aggiunge miglioramenti a un'app all'avvio da un assembly esterno.</span><span class="sxs-lookup"><span data-stu-id="a6a61-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="a6a61-106">Una libreria esterna può ad esempio usare un'implementazione di avvio dell'hosting per offrire servizi o provider di configurazione aggiuntivi a un'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="a6a61-107">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a6a61-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="a6a61-108">Attributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="a6a61-108">HostingStartup attribute</span></span>

<span data-ttu-id="a6a61-109">Un attributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica la presenza di un assembly di avvio dell'hosting da attivare in fase di runtime.</span><span class="sxs-lookup"><span data-stu-id="a6a61-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="a6a61-110">L'assembly di ingresso o l'assembly contenente la classe `Startup` viene analizzato automaticamente per la ricerca dell'attributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="a6a61-111">L'elenco di assembly in cui cercare gli attributi `HostingStartup` viene caricato in fase di runtime dalla configurazione in [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="a6a61-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="a6a61-112">L'elenco di assembly da escludere dalla ricerca viene caricato da [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="a6a61-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="a6a61-113">Nell'esempio seguente lo spazio dei nomi dell'assembly di avvio dell'hosting è `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="a6a61-114">La classe che contiene il codice di avvio dell'hosting è `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="a6a61-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="a6a61-115">L'attributo `HostingStartup` si trova in genere nel file della classe di implementazione `IHostingStartup` dell'assembly di avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="a6a61-116">Individuare gli assembly di avvio dell'hosting caricati</span><span class="sxs-lookup"><span data-stu-id="a6a61-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="a6a61-117">Per individuare gli assembly di avvio dell'hosting caricati, abilitare la registrazione e controllare i log dell'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="a6a61-118">Gli errori che si verificano durante il caricamento degli assembly vengono registrati.</span><span class="sxs-lookup"><span data-stu-id="a6a61-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="a6a61-119">Gli assembly di avvio dell'hosting caricati vengono registrati a livello di debug e tutti gli errori vengono registrati.</span><span class="sxs-lookup"><span data-stu-id="a6a61-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="a6a61-120">Disabilitare il caricamento automatico di assembly di avvio dell'hosting</span><span class="sxs-lookup"><span data-stu-id="a6a61-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="a6a61-121">Per disabilitare il caricamento automatico degli assembly di avvio dell'hosting, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="a6a61-122">Per evitare il caricamento di tutti gli assembly di avvio dell'hosting, impostare uno degli elementi seguenti su `true` o `1`:</span><span class="sxs-lookup"><span data-stu-id="a6a61-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="a6a61-123">Impedisci l'impostazione di configurazione host di avvio:</span><span class="sxs-lookup"><span data-stu-id="a6a61-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="a6a61-124">La variabile di ambiente `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="a6a61-125">Per impedire il caricamento di specifici assembly di avvio dell'hosting, impostare uno degli elementi seguenti su una stringa delimitata da punti e virgola di assembly di avvio dell'hosting da escludere all'avvio:</span><span class="sxs-lookup"><span data-stu-id="a6a61-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="a6a61-126">Impostazione configurazione host di esclusione dell'avvio dell'hosting:</span><span class="sxs-lookup"><span data-stu-id="a6a61-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="a6a61-127">La variabile di ambiente `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="a6a61-128">Se l'impostazione di configurazione host e la variabile di ambiente sono entrambe impostate, l'impostazione host controlla il comportamento.</span><span class="sxs-lookup"><span data-stu-id="a6a61-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="a6a61-129">La disabilitazione degli assembly di avvio dell'hosting tramite l'impostazione host o la variabile di ambiente ne determina la disabilitazione globale e la possibile disabilitazione di diverse caratteristiche di un'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="a6a61-130">Project</span><span class="sxs-lookup"><span data-stu-id="a6a61-130">Project</span></span>

<span data-ttu-id="a6a61-131">Creare l'avvio dell'hosting con uno dei tipi di progetto seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="a6a61-132">Libreria di classi</span><span class="sxs-lookup"><span data-stu-id="a6a61-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="a6a61-133">App console senza un punto di ingresso</span><span class="sxs-lookup"><span data-stu-id="a6a61-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="a6a61-134">Libreria di classi</span><span class="sxs-lookup"><span data-stu-id="a6a61-134">Class library</span></span>

<span data-ttu-id="a6a61-135">È possibile includere un miglioramento di avvio dell'hosting in una libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="a6a61-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="a6a61-136">La libreria contiene un attributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="a6a61-137">Il [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) include un' Razor app Pages, *HostingStartupApp*e una libreria di classi, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="a6a61-138">La libreria di classi:</span><span class="sxs-lookup"><span data-stu-id="a6a61-138">The class library:</span></span>

* <span data-ttu-id="a6a61-139">Contiene una classe di avvio dell'hosting, `ServiceKeyInjection`, che implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="a6a61-140">`ServiceKeyInjection`aggiunge una coppia di stringhe di servizio alla configurazione dell'app usando il provider di configurazione in memoria ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="a6a61-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="a6a61-141">Include un attributo `HostingStartup` che identifica lo spazio dei nomi e la classe di avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="a6a61-142">Il `ServiceKeyInjection` metodo della classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Usa un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> per aggiungere miglioramenti a un'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="a6a61-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="a6a61-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="a6a61-144">La pagina Indice dell'app legge ed esegue il rendering dei valori di configurazione per le due chiavi impostate dall'assembly di avvio dell'hosting della libreria di classi:</span><span class="sxs-lookup"><span data-stu-id="a6a61-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="a6a61-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a6a61-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="a6a61-146">Il [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) include anche un progetto di pacchetto NuGet che offre un avvio dell'hosting separato, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="a6a61-147">Il pacchetto ha le stesse caratteristiche della libreria di classi descritta in precedenza.</span><span class="sxs-lookup"><span data-stu-id="a6a61-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="a6a61-148">Il pacchetto:</span><span class="sxs-lookup"><span data-stu-id="a6a61-148">The package:</span></span>

* <span data-ttu-id="a6a61-149">Contiene una classe di avvio dell'hosting, `ServiceKeyInjection`, che implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="a6a61-150">`ServiceKeyInjection` aggiunge una coppia di stringhe di servizio alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="a6a61-151">Include un attributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="a6a61-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="a6a61-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="a6a61-153">La pagina Indice dell'app legge ed esegue il rendering dei valori di configurazione per le due chiavi impostate dall'assembly di avvio dell'hosting del pacchetto:</span><span class="sxs-lookup"><span data-stu-id="a6a61-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="a6a61-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a6a61-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="a6a61-155">App console senza un punto di ingresso</span><span class="sxs-lookup"><span data-stu-id="a6a61-155">Console app without an entry point</span></span>

<span data-ttu-id="a6a61-156">*Questo approccio è disponibile solo per le app .NET Core, non .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="a6a61-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="a6a61-157">Un miglioramento di avvio dell'hosting dinamico che non richiede un riferimento in fase di compilazione per l'attivazione può essere fornito in un'app console senza un punto di ingresso che contiene un attributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="a6a61-158">La pubblicazione dell'app console produce un assembly di avvio dell'hosting che può essere utilizzato dall'archivio di runtime.</span><span class="sxs-lookup"><span data-stu-id="a6a61-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="a6a61-159">In questo processo viene usata un'app console senza un punto di ingresso perché:</span><span class="sxs-lookup"><span data-stu-id="a6a61-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="a6a61-160">È necessario un file di dipendenze per utilizzare l'avvio dell'hosting nell'assembly di avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="a6a61-161">Un file di dipendenze è una risorsa di app eseguibile prodotta dalla pubblicazione di un'app, non una libreria.</span><span class="sxs-lookup"><span data-stu-id="a6a61-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="a6a61-162">Una libreria non può essere aggiunta direttamente all'[archivio dei pacchetti di runtime](/dotnet/core/deploying/runtime-store) che richiede un progetto eseguibile indirizzato al runtime condiviso.</span><span class="sxs-lookup"><span data-stu-id="a6a61-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="a6a61-163">Durante la creazione di un avvio dell'hosting dinamico:</span><span class="sxs-lookup"><span data-stu-id="a6a61-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="a6a61-164">Viene creato un assembly di avvio dell'hosting dall'app console senza un punto di ingresso che:</span><span class="sxs-lookup"><span data-stu-id="a6a61-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="a6a61-165">Include una classe che contiene l'implementazione `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="a6a61-166">Include un attributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) per identificare la classe di implementazione `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="a6a61-167">L'app console viene pubblicata per ottenere le dipendenze dell'avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="a6a61-168">Una conseguenza della pubblicazione dell'app console è che le dipendenze non usate vengono eliminate dal file di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="a6a61-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="a6a61-169">Il file delle dipendenze viene modificato per impostare il percorso di runtime dell'assembly di avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="a6a61-170">L'assembly di avvio dell'hosting e il relativo file di dipendenze vengono posizionati nell'archivio dei pacchetti di runtime.</span><span class="sxs-lookup"><span data-stu-id="a6a61-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="a6a61-171">Per individuare l'assembly di avvio dell'hosting e il relativo file di dipendenze, questi elementi sono elencati in una coppia di variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a6a61-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="a6a61-172">L'app console fa riferimento al pacchetto [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="a6a61-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="a6a61-173">Un attributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica una classe come implementazione di `IHostingStartup` per il caricamento e l'esecuzione durante la compilazione dell'oggetto <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="a6a61-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="a6a61-174">Nell'esempio seguente, lo spazio dei nomi è `StartupEnhancement` e la classe è `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="a6a61-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="a6a61-175">Una classe implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="a6a61-176">Il metodo della classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Usa un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> per aggiungere miglioramenti a un'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="a6a61-177">Nell'assembly di avvio dell'hosting `IHostingStartup.Configure` viene chiamato dal runtime prima di `Startup.Configure` nel codice utente, il che consente al codice utente di sovrascrivere qualsiasi configurazione fornita dall'assembly di avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="a6a61-178">Quando si compila un progetto `IHostingStartup`, il file delle dipendenze (*.deps.json*) imposta il percorso di `runtime` dell'assembly sulla cartella *bin*:</span><span class="sxs-lookup"><span data-stu-id="a6a61-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="a6a61-179">Viene visualizzata solo una parte del file.</span><span class="sxs-lookup"><span data-stu-id="a6a61-179">Only part of the file is shown.</span></span> <span data-ttu-id="a6a61-180">Il nome dell'assembly nell'esempio è `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="a6a61-181">Configurazione fornita dall'avvio dell'hosting</span><span class="sxs-lookup"><span data-stu-id="a6a61-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="a6a61-182">Esistono due approcci per la gestione di configurazione, a seconda che si voglia dare la precedenza alla configurazione d avvio dell'hosting o alla configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="a6a61-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="a6a61-183">Fornire la configurazione all'app usando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> per caricare la configurazione dopo l'esecuzione dei delegati <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> dell'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="a6a61-184">La configurazione di avvio dell'hosting ha la priorità rispetto alla configurazione dell'app con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="a6a61-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="a6a61-185">Fornire la configurazione all'app usando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> per caricare la configurazione prima dell'esecuzione dei delegati <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> dell'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="a6a61-186">I valori di configurazione dell'app hanno la priorità rispetto a quelli forniti dall'avvio dell'hosting usando questo approccio.</span><span class="sxs-lookup"><span data-stu-id="a6a61-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="a6a61-187">Specificare l'assembly di avvio dell'hosting</span><span class="sxs-lookup"><span data-stu-id="a6a61-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="a6a61-188">Per un avvio dell'hosting fornito da una libreria di classi o da un'app console, specificare il nome dell'assembly di avvio dell'hosting nella variabile di ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="a6a61-189">La variabile di ambiente è un elenco di assembly delimitato da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="a6a61-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="a6a61-190">L'attributo `HostingStartup` viene cercato solo negli assembly di avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="a6a61-191">Per l'app di esempio *HostingStartupApp*, per individuare gli avvii dell'hosting descritti in precedenza, la variabile di ambiente viene impostata sul valore seguente:</span><span class="sxs-lookup"><span data-stu-id="a6a61-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="a6a61-192">È inoltre possibile impostare un assembly di avvio dell'hosting utilizzando l'impostazione di configurazione host di assembly di avvio host:</span><span class="sxs-lookup"><span data-stu-id="a6a61-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="a6a61-193">Quando sono presenti più montaggi di avvio host, i relativi <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metodi vengono eseguiti nell'ordine in cui sono elencati gli assembly.</span><span class="sxs-lookup"><span data-stu-id="a6a61-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="a6a61-194">Activation</span><span class="sxs-lookup"><span data-stu-id="a6a61-194">Activation</span></span>

<span data-ttu-id="a6a61-195">Le opzioni di attivazione dell'avvio dell'hosting sono:</span><span class="sxs-lookup"><span data-stu-id="a6a61-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="a6a61-196">[Archivio di runtime](#runtime-store): l'attivazione non richiede un riferimento in fase di compilazione per l'attivazione.</span><span class="sxs-lookup"><span data-stu-id="a6a61-196">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="a6a61-197">L'app di esempio inserisce l'assembly di avvio dell'hosting e i file di dipendenze in una cartella *deployment* per facilitare la distribuzione dell'avvio dell'hosting in un ambiente con più computer.</span><span class="sxs-lookup"><span data-stu-id="a6a61-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="a6a61-198">La cartella *deployment* include anche uno script PowerShell che crea o modifica le variabili di ambiente nel sistema di distribuzione per abilitare l'avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="a6a61-199">Riferimento in fase di compilazione richiesto per l'attivazione</span><span class="sxs-lookup"><span data-stu-id="a6a61-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="a6a61-200">Pacchetto NuGet</span><span class="sxs-lookup"><span data-stu-id="a6a61-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="a6a61-201">Cartella bin del progetto</span><span class="sxs-lookup"><span data-stu-id="a6a61-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="a6a61-202">Archivio di runtime</span><span class="sxs-lookup"><span data-stu-id="a6a61-202">Runtime store</span></span>

<span data-ttu-id="a6a61-203">L'implementazione dell'avvio dell'hosting viene inserita nell'[archivio di runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="a6a61-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="a6a61-204">L'app migliorata non richiede un riferimento in fase di compilazione all'assembly.</span><span class="sxs-lookup"><span data-stu-id="a6a61-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="a6a61-205">Dopo la compilazione dell'avvio dell'hosting, viene generato un archivio di runtime usando il file di progetto del manifesto e il comando [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="a6a61-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="a6a61-206">Nell'app di esempio (progetto *RuntimeStore*) viene usato il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="a6a61-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="a6a61-207">Per consentire al runtime di individuare l'archivio di runtime, il percorso dell'archivio di runtime viene aggiunto alla variabile di ambiente `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="a6a61-208">**Modificare e inserire il file di dipendenze dell'avvio dell'hosting**</span><span class="sxs-lookup"><span data-stu-id="a6a61-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="a6a61-209">Per attivare il miglioramento senza un riferimento al pacchetto per il miglioramento, specificare le dipendenze aggiuntive per il runtime con `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="a6a61-210">`additionalDeps` consente di:</span><span class="sxs-lookup"><span data-stu-id="a6a61-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="a6a61-211">Estendere il grafo della libreria dell'app fornendo un set di file *.deps.json* aggiuntivi da unire al file *.deps.json* proprio dell'app all'avvio.</span><span class="sxs-lookup"><span data-stu-id="a6a61-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="a6a61-212">Rendere l'assembly di avvio dell'hosting individuabile e caricabile.</span><span class="sxs-lookup"><span data-stu-id="a6a61-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="a6a61-213">L'approccio consigliato per la generazione del file delle dipendenze aggiuntive è:</span><span class="sxs-lookup"><span data-stu-id="a6a61-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="a6a61-214">Eseguire `dotnet publish` sul file manifesto dell'archivio di runtime indicato nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="a6a61-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="a6a61-215">Rimuovere il riferimento al manifesto dalle librerie e la `runtime` sezione del *.deps.jsrisultante sul* file.</span><span class="sxs-lookup"><span data-stu-id="a6a61-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="a6a61-216">Nel progetto di esempio la proprietà `store.manifest/1.0.0` viene rimossa da `targets` e dalla sezione `libraries`:</span><span class="sxs-lookup"><span data-stu-id="a6a61-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="a6a61-217">Posizionare il file *.deps.json* nel percorso seguente:</span><span class="sxs-lookup"><span data-stu-id="a6a61-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="a6a61-218">`{ADDITIONAL DEPENDENCIES PATH}`: Percorso aggiunto alla `DOTNET_ADDITIONAL_DEPS` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a6a61-218">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="a6a61-219">`{SHARED FRAMEWORK NAME}`: Framework condiviso necessario per questo file di dipendenze aggiuntivo.</span><span class="sxs-lookup"><span data-stu-id="a6a61-219">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="a6a61-220">`{SHARED FRAMEWORK VERSION}`: Versione minima del Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a6a61-220">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="a6a61-221">`{ENHANCEMENT ASSEMBLY NAME}`: Nome dell'assembly della funzionalità avanzata.</span><span class="sxs-lookup"><span data-stu-id="a6a61-221">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="a6a61-222">Nell'app di esempio (progetto *RuntimeStore*), il file di dipendenze aggiuntive viene posizionato nel percorso seguente:</span><span class="sxs-lookup"><span data-stu-id="a6a61-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="a6a61-223">Affinché il runtime possa individuare il percorso dell'archivio di runtime, il percorso del file di dipendenze aggiuntive viene aggiunto alla variabile di ambiente `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="a6a61-224">Nell'app di esempio (progetto *RuntimeStore*), la creazione dell'archivio di runtime e la generazione del file di dipendenze aggiuntive vengono eseguite tramite uno script di [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="a6a61-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="a6a61-225">Per esempi che illustrano come impostare le variabili di ambiente per diversi sistemi operativi, vedere [Usare più ambienti](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="a6a61-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="a6a61-226">**Distribuzione**</span><span class="sxs-lookup"><span data-stu-id="a6a61-226">**Deployment**</span></span>

<span data-ttu-id="a6a61-227">Per facilitare la distribuzione di un avvio dell'hosting in un ambiente con più computer, l'app di esempio crea una cartella *deplyment* nell'output pubblicato che contiene:</span><span class="sxs-lookup"><span data-stu-id="a6a61-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="a6a61-228">L'archivio di runtime dell'avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="a6a61-229">Il file di dipendenze dell'avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="a6a61-230">Uno script di PowerShell che crea o modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` e `DOTNET_ADDITIONAL_DEPS` per supportare l'attivazione dell'avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="a6a61-231">Eseguire lo script da un prompt dei comandi di PowerShell amministrativo del sistema di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="a6a61-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="a6a61-232">Pacchetto NuGet</span><span class="sxs-lookup"><span data-stu-id="a6a61-232">NuGet package</span></span>

<span data-ttu-id="a6a61-233">È possibile includere un miglioramento di avvio dell'hosting in un pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="a6a61-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="a6a61-234">Il pacchetto include un attributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="a6a61-235">I tipi di avvio dell'hosting offerti dal pacchetto vengono resi disponibili all'app usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="a6a61-236">Il file di progetto dell'app migliorata crea un riferimento al pacchetto per l'avvio dell'hosting nel file di progetto dell'app (un riferimento in fase di compilazione).</span><span class="sxs-lookup"><span data-stu-id="a6a61-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="a6a61-237">Dopo aver inserito il riferimento in fase di compilazione, l'assembly di avvio dell'hosting e tutte le relative dipendenze vengono incorporate nel file delle dipendenze dell'app (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="a6a61-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="a6a61-238">Questo approccio si applica a un pacchetto di assembly di avvio dell'hosting pubblicato in [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="a6a61-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="a6a61-239">Il file di dipendenze dell'avvio dell'hosting viene reso disponibile all'app migliorata come descritto nella sezione [Archivio di runtime](#runtime-store) (senza un riferimento in fase di compilazione).</span><span class="sxs-lookup"><span data-stu-id="a6a61-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="a6a61-240">Per altre informazioni su pacchetti NuGet e l'archivio di runtime, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="a6a61-241">Come creare un pacchetto NuGet con strumenti multipiattaforma</span><span class="sxs-lookup"><span data-stu-id="a6a61-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="a6a61-242">Pubblicazione di pacchetti</span><span class="sxs-lookup"><span data-stu-id="a6a61-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="a6a61-243">Archivio pacchetti di runtime</span><span class="sxs-lookup"><span data-stu-id="a6a61-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="a6a61-244">Cartella bin del progetto</span><span class="sxs-lookup"><span data-stu-id="a6a61-244">Project bin folder</span></span>

<span data-ttu-id="a6a61-245">È possibile inserire un avvio dell'hosting tramite un assembly distribuito tramite *bin* nell'app migliorata.</span><span class="sxs-lookup"><span data-stu-id="a6a61-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="a6a61-246">I tipi di avvio dell'hosting offerti dall'assembly vengono resi disponibili all'app usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="a6a61-247">Il file di progetto dell'app migliorata crea un riferimento all'assembly nell'avvio dell'hosting startup (riferimento in fase di compilazione).</span><span class="sxs-lookup"><span data-stu-id="a6a61-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="a6a61-248">Dopo aver inserito il riferimento in fase di compilazione, l'assembly di avvio dell'hosting e tutte le relative dipendenze vengono incorporate nel file delle dipendenze dell'app (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="a6a61-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="a6a61-249">Questo approccio si applica quando lo scenario di distribuzione richiede la creazione di un riferimento in fase di compilazione all'assembly dell'avvio dell'hosting (file *DLL*) e lo spostamento dell'assembly in uno dei due elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="a6a61-250">Il progetto che lo utilizza.</span><span class="sxs-lookup"><span data-stu-id="a6a61-250">The consuming project.</span></span>
  * <span data-ttu-id="a6a61-251">Una posizione accessibile dal progetto che lo utilizza.</span><span class="sxs-lookup"><span data-stu-id="a6a61-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="a6a61-252">Il file di dipendenze dell'avvio dell'hosting viene reso disponibile all'app migliorata come descritto nella sezione [Archivio di runtime](#runtime-store) (senza un riferimento in fase di compilazione).</span><span class="sxs-lookup"><span data-stu-id="a6a61-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="a6a61-253">Quando la destinazione è .NET Framework, l'assembly può essere caricato nel contesto di caricamento predefinito, che in .NET Framework significa che l'assembly si trova in una delle posizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="a6a61-254">Percorso di base dell'applicazione: la cartella *bin* in cui si trova l'eseguibile dell'app (con*estensione exe*).</span><span class="sxs-lookup"><span data-stu-id="a6a61-254">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="a6a61-255">Global assembly cache (GAC): la GAC archivia gli assembly condivisi da diverse app .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a6a61-255">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="a6a61-256">Per ulteriori informazioni, vedere [procedura: installare un assembly nella global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) nella documentazione di .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a6a61-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="a6a61-257">Codice di esempio</span><span class="sxs-lookup"><span data-stu-id="a6a61-257">Sample code</span></span>

<span data-ttu-id="a6a61-258">Il [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([come eseguire il download](xref:index#how-to-download-a-sample)) illustra gli scenari di implementazione dell'avvio dell'hosting:</span><span class="sxs-lookup"><span data-stu-id="a6a61-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="a6a61-259">Due assembly di avvio dell'hosting (librerie di classi) impostano ognuno una coppia chiave-valore di configurazione in memoria:</span><span class="sxs-lookup"><span data-stu-id="a6a61-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="a6a61-260">Pacchetto NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="a6a61-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="a6a61-261">Libreria di classi (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="a6a61-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="a6a61-262">L'avvio dell'hosting viene attivato da un assembly distribuito tramite archivio di runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="a6a61-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="a6a61-263">All'avvio l'assembly aggiunge all'app due middleware che offrono informazioni di diagnostica su:</span><span class="sxs-lookup"><span data-stu-id="a6a61-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="a6a61-264">Servizi registrati</span><span class="sxs-lookup"><span data-stu-id="a6a61-264">Registered services</span></span>
  * <span data-ttu-id="a6a61-265">Indirizzo (schema, host, base del percorso, percorso, stringa di query)</span><span class="sxs-lookup"><span data-stu-id="a6a61-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="a6a61-266">Connessione (IP remoto, porta remota, IP locale, porta locale, certificato client)</span><span class="sxs-lookup"><span data-stu-id="a6a61-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="a6a61-267">Intestazioni della richiesta</span><span class="sxs-lookup"><span data-stu-id="a6a61-267">Request headers</span></span>
  * <span data-ttu-id="a6a61-268">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a6a61-268">Environment variables</span></span>

<span data-ttu-id="a6a61-269">Per eseguire l'esempio:</span><span class="sxs-lookup"><span data-stu-id="a6a61-269">To run the sample:</span></span>

<span data-ttu-id="a6a61-270">**Attivazione da un pacchetto NuGet**</span><span class="sxs-lookup"><span data-stu-id="a6a61-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="a6a61-271">Compilare il pacchetto *HostingStartupPackage* con il comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="a6a61-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="a6a61-272">Aggiungere il nome dell'assembly del pacchetto *HostingStartupPackage* alla variabile di ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="a6a61-273">Compila ed esegui l'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-273">Compile and run the app.</span></span> <span data-ttu-id="a6a61-274">L'app migliorata include un riferimento al pacchetto (riferimento in fase di compilazione).</span><span class="sxs-lookup"><span data-stu-id="a6a61-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="a6a61-275">`<PropertyGroup>` nel file di progetto dell'app specifica l'output del progetto del pacchetto (*../HostingStartupPackage/bin/Debug*) come origine del pacchetto.</span><span class="sxs-lookup"><span data-stu-id="a6a61-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="a6a61-276">Ciò consente all'app di usare il pacchetto senza caricare il pacchetto in [NuGet.org](https://www.nuget.org/). Per ulteriori informazioni, vedere le note nel file di progetto di HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="a6a61-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="a6a61-277">Si noti che i valori di chiave di configurazione del servizio di cui è stato eseguito il rendering tramite la pagina Indice corrispondono ai valori impostati dal metodo `ServiceKeyInjection.Configure` del pacchetto.</span><span class="sxs-lookup"><span data-stu-id="a6a61-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="a6a61-278">Se si apportano modifiche al progetto *HostingStartupPackage* e lo si ricompila, cancellare le cache del pacchetto NuGet locale per assicurarsi che *HostingStartupApp* riceva il pacchetto aggiornato e non un pacchetto non aggiornato proveniente dalla cache locale.</span><span class="sxs-lookup"><span data-stu-id="a6a61-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="a6a61-279">Per cancellare le cache NuGet locali, eseguire il comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) seguente:</span><span class="sxs-lookup"><span data-stu-id="a6a61-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="a6a61-280">**Attivazione da una libreria di classi**</span><span class="sxs-lookup"><span data-stu-id="a6a61-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="a6a61-281">Compilare la libreria di classi *HostingStartupLibrary* con il comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="a6a61-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="a6a61-282">Aggiungere il nome dell'assembly della libreria di classi di *HostingStartupLibrary* alla variabile di ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="a6a61-283">Distribuire tramite *bin* l'assembly della libreria di classi all'app copiando il file *HostingStartupLibrary.dll* dall'output compilato della libreria di classi alla cartella *bin/Debug* dell'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="a6a61-284">Compila ed esegui l'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-284">Compile and run the app.</span></span> <span data-ttu-id="a6a61-285">`<ItemGroup>`Nel file di progetto dell'app fa riferimento all'assembly della libreria di classi (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (riferimento in fase di compilazione).</span><span class="sxs-lookup"><span data-stu-id="a6a61-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="a6a61-286">Per altre informazioni, vedere le note nel file di progetto di HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="a6a61-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="a6a61-287">Si noti che i valori di chiave di configurazione del servizio di cui è stato eseguito il rendering tramite la pagina Indice corrispondono ai valori impostati dal metodo `ServiceKeyInjection.Configure` della libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="a6a61-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="a6a61-288">**Attivazione da un assembly distribuito tramite l'archivio di runtime**</span><span class="sxs-lookup"><span data-stu-id="a6a61-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="a6a61-289">Il progetto *StartupDiagnostics* usa [PowerShell](/powershell/scripting/powershell-scripting) per modificare il relativo file *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="a6a61-290">PowerShell viene installato per impostazione predefinita in Windows a partire da Windows 7 SP1 e Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="a6a61-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="a6a61-291">Per ottenere PowerShell su altre piattaforme, vedere [installazione di diverse versioni di PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="a6a61-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="a6a61-292">Eseguire lo script *build.ps1* nella cartella *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="a6a61-293">Lo script:</span><span class="sxs-lookup"><span data-stu-id="a6a61-293">The script:</span></span>
   * <span data-ttu-id="a6a61-294">Genera il `StartupDiagnostics` pacchetto nella cartella *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="a6a61-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="a6a61-295">Genera l'archivio di runtime per `StartupDiagnostics` nella cartella *store*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="a6a61-296">Il comando `dotnet store` nello script usa l' [identificatore di runtime (RID)](/dotnet/core/rid-catalog)`win7-x64` per un avvio dell'hosting distribuito su Windows.</span><span class="sxs-lookup"><span data-stu-id="a6a61-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="a6a61-297">Quando si specifica l'avvio dell'hosting per un runtime diverso, immettere il RID corretto nella riga 37 dello script.</span><span class="sxs-lookup"><span data-stu-id="a6a61-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="a6a61-298">L'archivio di runtime di verrà `StartupDiagnostics` spostato in un secondo momento nell'archivio di runtime dell'utente o del sistema nel computer in cui verrà utilizzato l'assembly.</span><span class="sxs-lookup"><span data-stu-id="a6a61-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="a6a61-299">Il percorso di installazione dell'archivio di runtime utente per l' `StartupDiagnostics` assembly è *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="a6a61-300">Genera `additionalDeps` per `StartupDiagnostics` nella cartella *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="a6a61-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="a6a61-301">Le dipendenze aggiuntive verranno spostate in un secondo momento alle dipendenze aggiuntive del sistema o dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a6a61-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="a6a61-302">Il `StartupDiagnostics` percorso di installazione delle dipendenze aggiuntive dell'utente è *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="a6a61-303">Posiziona il file *deploy.ps1* nella cartella *deployment*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="a6a61-304">Eseguire lo script *deploy.ps1* nella cartella *deployment*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="a6a61-305">Lo script aggiunge:</span><span class="sxs-lookup"><span data-stu-id="a6a61-305">The script appends:</span></span>
   * <span data-ttu-id="a6a61-306">`StartupDiagnostics` alla variabile di ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="a6a61-307">Percorso delle dipendenze di avvio dell'hosting (nella cartella di *distribuzione* del progetto RuntimeStore) alla `DOTNET_ADDITIONAL_DEPS` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a6a61-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="a6a61-308">Percorso dell'archivio di runtime (nella cartella di *distribuzione* del progetto RuntimeStore) alla `DOTNET_SHARED_STORE` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a6a61-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="a6a61-309">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="a6a61-309">Run the sample app.</span></span>
1. <span data-ttu-id="a6a61-310">Richiedere l'endpoint `/services` per visualizzare i servizi registrati dell'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="a6a61-311">Richiedere l'endpoint `/diag` per visualizzare le informazioni di diagnostica.</span><span class="sxs-lookup"><span data-stu-id="a6a61-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a6a61-312">Un' <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementazione di (host Startup) aggiunge miglioramenti a un'app all'avvio da un assembly esterno.</span><span class="sxs-lookup"><span data-stu-id="a6a61-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="a6a61-313">Una libreria esterna può ad esempio usare un'implementazione di avvio dell'hosting per offrire servizi o provider di configurazione aggiuntivi a un'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="a6a61-314">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a6a61-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="a6a61-315">Attributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="a6a61-315">HostingStartup attribute</span></span>

<span data-ttu-id="a6a61-316">Un attributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica la presenza di un assembly di avvio dell'hosting da attivare in fase di runtime.</span><span class="sxs-lookup"><span data-stu-id="a6a61-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="a6a61-317">L'assembly di ingresso o l'assembly contenente la classe `Startup` viene analizzato automaticamente per la ricerca dell'attributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="a6a61-318">L'elenco di assembly in cui cercare gli attributi `HostingStartup` viene caricato in fase di runtime dalla configurazione in [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="a6a61-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="a6a61-319">L'elenco di assembly da escludere dalla ricerca viene caricato da [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="a6a61-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="a6a61-320">Per altre informazioni, vedere [Host Web: Assembly di avvio dell'hosting](xref:fundamentals/host/web-host#hosting-startup-assemblies) e [Host Web: Assembly di avvio dell'hosting da escludere](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="a6a61-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="a6a61-321">Nell'esempio seguente lo spazio dei nomi dell'assembly di avvio dell'hosting è `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="a6a61-322">La classe che contiene il codice di avvio dell'hosting è `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="a6a61-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="a6a61-323">L'attributo `HostingStartup` si trova in genere nel file della classe di implementazione `IHostingStartup` dell'assembly di avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="a6a61-324">Individuare gli assembly di avvio dell'hosting caricati</span><span class="sxs-lookup"><span data-stu-id="a6a61-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="a6a61-325">Per individuare gli assembly di avvio dell'hosting caricati, abilitare la registrazione e controllare i log dell'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="a6a61-326">Gli errori che si verificano durante il caricamento degli assembly vengono registrati.</span><span class="sxs-lookup"><span data-stu-id="a6a61-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="a6a61-327">Gli assembly di avvio dell'hosting caricati vengono registrati a livello di debug e tutti gli errori vengono registrati.</span><span class="sxs-lookup"><span data-stu-id="a6a61-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="a6a61-328">Disabilitare il caricamento automatico di assembly di avvio dell'hosting</span><span class="sxs-lookup"><span data-stu-id="a6a61-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="a6a61-329">Per disabilitare il caricamento automatico degli assembly di avvio dell'hosting, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="a6a61-330">Per evitare il caricamento di tutti gli assembly di avvio dell'hosting, impostare uno degli elementi seguenti su `true` o `1`:</span><span class="sxs-lookup"><span data-stu-id="a6a61-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="a6a61-331">Impostazione di configurazione host per [impedire l'avvio dell'hosting](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="a6a61-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="a6a61-332">La variabile di ambiente `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="a6a61-333">Per impedire il caricamento di specifici assembly di avvio dell'hosting, impostare uno degli elementi seguenti su una stringa delimitata da punti e virgola di assembly di avvio dell'hosting da escludere all'avvio:</span><span class="sxs-lookup"><span data-stu-id="a6a61-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="a6a61-334">Impostazione di configurazione host per [assembly di avvio dell'hosting da escludere](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="a6a61-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="a6a61-335">La variabile di ambiente `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="a6a61-336">Se l'impostazione di configurazione host e la variabile di ambiente sono entrambe impostate, l'impostazione host controlla il comportamento.</span><span class="sxs-lookup"><span data-stu-id="a6a61-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="a6a61-337">La disabilitazione degli assembly di avvio dell'hosting tramite l'impostazione host o la variabile di ambiente ne determina la disabilitazione globale e la possibile disabilitazione di diverse caratteristiche di un'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="a6a61-338">Project</span><span class="sxs-lookup"><span data-stu-id="a6a61-338">Project</span></span>

<span data-ttu-id="a6a61-339">Creare l'avvio dell'hosting con uno dei tipi di progetto seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="a6a61-340">Libreria di classi</span><span class="sxs-lookup"><span data-stu-id="a6a61-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="a6a61-341">App console senza un punto di ingresso</span><span class="sxs-lookup"><span data-stu-id="a6a61-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="a6a61-342">Libreria di classi</span><span class="sxs-lookup"><span data-stu-id="a6a61-342">Class library</span></span>

<span data-ttu-id="a6a61-343">È possibile includere un miglioramento di avvio dell'hosting in una libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="a6a61-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="a6a61-344">La libreria contiene un attributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="a6a61-345">Il [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) include un' Razor app Pages, *HostingStartupApp*e una libreria di classi, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="a6a61-346">La libreria di classi:</span><span class="sxs-lookup"><span data-stu-id="a6a61-346">The class library:</span></span>

* <span data-ttu-id="a6a61-347">Contiene una classe di avvio dell'hosting, `ServiceKeyInjection`, che implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="a6a61-348">`ServiceKeyInjection`aggiunge una coppia di stringhe di servizio alla configurazione dell'app usando il provider di configurazione in memoria ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="a6a61-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="a6a61-349">Include un attributo `HostingStartup` che identifica lo spazio dei nomi e la classe di avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="a6a61-350">Il `ServiceKeyInjection` metodo della classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Usa un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> per aggiungere miglioramenti a un'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="a6a61-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="a6a61-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="a6a61-352">La pagina Indice dell'app legge ed esegue il rendering dei valori di configurazione per le due chiavi impostate dall'assembly di avvio dell'hosting della libreria di classi:</span><span class="sxs-lookup"><span data-stu-id="a6a61-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="a6a61-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a6a61-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="a6a61-354">Il [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) include anche un progetto di pacchetto NuGet che offre un avvio dell'hosting separato, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="a6a61-355">Il pacchetto ha le stesse caratteristiche della libreria di classi descritta in precedenza.</span><span class="sxs-lookup"><span data-stu-id="a6a61-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="a6a61-356">Il pacchetto:</span><span class="sxs-lookup"><span data-stu-id="a6a61-356">The package:</span></span>

* <span data-ttu-id="a6a61-357">Contiene una classe di avvio dell'hosting, `ServiceKeyInjection`, che implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="a6a61-358">`ServiceKeyInjection` aggiunge una coppia di stringhe di servizio alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="a6a61-359">Include un attributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="a6a61-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="a6a61-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="a6a61-361">La pagina Indice dell'app legge ed esegue il rendering dei valori di configurazione per le due chiavi impostate dall'assembly di avvio dell'hosting del pacchetto:</span><span class="sxs-lookup"><span data-stu-id="a6a61-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="a6a61-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a6a61-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="a6a61-363">App console senza un punto di ingresso</span><span class="sxs-lookup"><span data-stu-id="a6a61-363">Console app without an entry point</span></span>

<span data-ttu-id="a6a61-364">*Questo approccio è disponibile solo per le app .NET Core, non .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="a6a61-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="a6a61-365">Un miglioramento di avvio dell'hosting dinamico che non richiede un riferimento in fase di compilazione per l'attivazione può essere fornito in un'app console senza un punto di ingresso che contiene un attributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="a6a61-366">La pubblicazione dell'app console produce un assembly di avvio dell'hosting che può essere utilizzato dall'archivio di runtime.</span><span class="sxs-lookup"><span data-stu-id="a6a61-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="a6a61-367">In questo processo viene usata un'app console senza un punto di ingresso perché:</span><span class="sxs-lookup"><span data-stu-id="a6a61-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="a6a61-368">È necessario un file di dipendenze per utilizzare l'avvio dell'hosting nell'assembly di avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="a6a61-369">Un file di dipendenze è una risorsa di app eseguibile prodotta dalla pubblicazione di un'app, non una libreria.</span><span class="sxs-lookup"><span data-stu-id="a6a61-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="a6a61-370">Una libreria non può essere aggiunta direttamente all'[archivio dei pacchetti di runtime](/dotnet/core/deploying/runtime-store) che richiede un progetto eseguibile indirizzato al runtime condiviso.</span><span class="sxs-lookup"><span data-stu-id="a6a61-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="a6a61-371">Durante la creazione di un avvio dell'hosting dinamico:</span><span class="sxs-lookup"><span data-stu-id="a6a61-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="a6a61-372">Viene creato un assembly di avvio dell'hosting dall'app console senza un punto di ingresso che:</span><span class="sxs-lookup"><span data-stu-id="a6a61-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="a6a61-373">Include una classe che contiene l'implementazione `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="a6a61-374">Include un attributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) per identificare la classe di implementazione `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="a6a61-375">L'app console viene pubblicata per ottenere le dipendenze dell'avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="a6a61-376">Una conseguenza della pubblicazione dell'app console è che le dipendenze non usate vengono eliminate dal file di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="a6a61-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="a6a61-377">Il file delle dipendenze viene modificato per impostare il percorso di runtime dell'assembly di avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="a6a61-378">L'assembly di avvio dell'hosting e il relativo file di dipendenze vengono posizionati nell'archivio dei pacchetti di runtime.</span><span class="sxs-lookup"><span data-stu-id="a6a61-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="a6a61-379">Per individuare l'assembly di avvio dell'hosting e il relativo file di dipendenze, questi elementi sono elencati in una coppia di variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a6a61-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="a6a61-380">L'app console fa riferimento al pacchetto [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="a6a61-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="a6a61-381">Un attributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica una classe come implementazione di `IHostingStartup` per il caricamento e l'esecuzione durante la compilazione dell'oggetto <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="a6a61-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="a6a61-382">Nell'esempio seguente, lo spazio dei nomi è `StartupEnhancement` e la classe è `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="a6a61-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="a6a61-383">Una classe implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="a6a61-384">Il metodo della classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Usa un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> per aggiungere miglioramenti a un'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="a6a61-385">Nell'assembly di avvio dell'hosting `IHostingStartup.Configure` viene chiamato dal runtime prima di `Startup.Configure` nel codice utente, il che consente al codice utente di sovrascrivere qualsiasi configurazione fornita dall'assembly di avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="a6a61-386">Quando si compila un progetto `IHostingStartup`, il file delle dipendenze (*.deps.json*) imposta il percorso di `runtime` dell'assembly sulla cartella *bin*:</span><span class="sxs-lookup"><span data-stu-id="a6a61-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="a6a61-387">Viene visualizzata solo una parte del file.</span><span class="sxs-lookup"><span data-stu-id="a6a61-387">Only part of the file is shown.</span></span> <span data-ttu-id="a6a61-388">Il nome dell'assembly nell'esempio è `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="a6a61-389">Configurazione fornita dall'avvio dell'hosting</span><span class="sxs-lookup"><span data-stu-id="a6a61-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="a6a61-390">Esistono due approcci per la gestione di configurazione, a seconda che si voglia dare la precedenza alla configurazione d avvio dell'hosting o alla configurazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="a6a61-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="a6a61-391">Fornire la configurazione all'app usando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> per caricare la configurazione dopo l'esecuzione dei delegati <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> dell'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="a6a61-392">La configurazione di avvio dell'hosting ha la priorità rispetto alla configurazione dell'app con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="a6a61-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="a6a61-393">Fornire la configurazione all'app usando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> per caricare la configurazione prima dell'esecuzione dei delegati <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> dell'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="a6a61-394">I valori di configurazione dell'app hanno la priorità rispetto a quelli forniti dall'avvio dell'hosting usando questo approccio.</span><span class="sxs-lookup"><span data-stu-id="a6a61-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="a6a61-395">Specificare l'assembly di avvio dell'hosting</span><span class="sxs-lookup"><span data-stu-id="a6a61-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="a6a61-396">Per un avvio dell'hosting fornito da una libreria di classi o da un'app console, specificare il nome dell'assembly di avvio dell'hosting nella variabile di ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="a6a61-397">La variabile di ambiente è un elenco di assembly delimitato da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="a6a61-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="a6a61-398">L'attributo `HostingStartup` viene cercato solo negli assembly di avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="a6a61-399">Per l'app di esempio *HostingStartupApp*, per individuare gli avvii dell'hosting descritti in precedenza, la variabile di ambiente viene impostata sul valore seguente:</span><span class="sxs-lookup"><span data-stu-id="a6a61-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="a6a61-400">È possibile impostare l'assembly di avvio dell'hosting anche tramite l'impostazione di configurazione host [Assembly di avvio dell'hosting](xref:fundamentals/host/web-host#hosting-startup-assemblies).</span><span class="sxs-lookup"><span data-stu-id="a6a61-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="a6a61-401">Quando sono presenti più montaggi di avvio host, i relativi <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metodi vengono eseguiti nell'ordine in cui sono elencati gli assembly.</span><span class="sxs-lookup"><span data-stu-id="a6a61-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="a6a61-402">Activation</span><span class="sxs-lookup"><span data-stu-id="a6a61-402">Activation</span></span>

<span data-ttu-id="a6a61-403">Le opzioni di attivazione dell'avvio dell'hosting sono:</span><span class="sxs-lookup"><span data-stu-id="a6a61-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="a6a61-404">[Archivio di runtime](#runtime-store): l'attivazione non richiede un riferimento in fase di compilazione per l'attivazione.</span><span class="sxs-lookup"><span data-stu-id="a6a61-404">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="a6a61-405">L'app di esempio inserisce l'assembly di avvio dell'hosting e i file di dipendenze in una cartella *deployment* per facilitare la distribuzione dell'avvio dell'hosting in un ambiente con più computer.</span><span class="sxs-lookup"><span data-stu-id="a6a61-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="a6a61-406">La cartella *deployment* include anche uno script PowerShell che crea o modifica le variabili di ambiente nel sistema di distribuzione per abilitare l'avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="a6a61-407">Riferimento in fase di compilazione richiesto per l'attivazione</span><span class="sxs-lookup"><span data-stu-id="a6a61-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="a6a61-408">Pacchetto NuGet</span><span class="sxs-lookup"><span data-stu-id="a6a61-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="a6a61-409">Cartella bin del progetto</span><span class="sxs-lookup"><span data-stu-id="a6a61-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="a6a61-410">Archivio di runtime</span><span class="sxs-lookup"><span data-stu-id="a6a61-410">Runtime store</span></span>

<span data-ttu-id="a6a61-411">L'implementazione dell'avvio dell'hosting viene inserita nell'[archivio di runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="a6a61-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="a6a61-412">L'app migliorata non richiede un riferimento in fase di compilazione all'assembly.</span><span class="sxs-lookup"><span data-stu-id="a6a61-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="a6a61-413">Dopo la compilazione dell'avvio dell'hosting, viene generato un archivio di runtime usando il file di progetto del manifesto e il comando [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="a6a61-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="a6a61-414">Nell'app di esempio (progetto *RuntimeStore*) viene usato il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="a6a61-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="a6a61-415">Per consentire al runtime di individuare l'archivio di runtime, il percorso dell'archivio di runtime viene aggiunto alla variabile di ambiente `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="a6a61-416">**Modificare e inserire il file di dipendenze dell'avvio dell'hosting**</span><span class="sxs-lookup"><span data-stu-id="a6a61-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="a6a61-417">Per attivare il miglioramento senza un riferimento al pacchetto per il miglioramento, specificare le dipendenze aggiuntive per il runtime con `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="a6a61-418">`additionalDeps` consente di:</span><span class="sxs-lookup"><span data-stu-id="a6a61-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="a6a61-419">Estendere il grafo della libreria dell'app fornendo un set di file *.deps.json* aggiuntivi da unire al file *.deps.json* proprio dell'app all'avvio.</span><span class="sxs-lookup"><span data-stu-id="a6a61-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="a6a61-420">Rendere l'assembly di avvio dell'hosting individuabile e caricabile.</span><span class="sxs-lookup"><span data-stu-id="a6a61-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="a6a61-421">L'approccio consigliato per la generazione del file delle dipendenze aggiuntive è:</span><span class="sxs-lookup"><span data-stu-id="a6a61-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="a6a61-422">Eseguire `dotnet publish` sul file manifesto dell'archivio di runtime indicato nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="a6a61-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="a6a61-423">Rimuovere il riferimento al manifesto dalle librerie e la `runtime` sezione del *.deps.jsrisultante sul* file.</span><span class="sxs-lookup"><span data-stu-id="a6a61-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="a6a61-424">Nel progetto di esempio la proprietà `store.manifest/1.0.0` viene rimossa da `targets` e dalla sezione `libraries`:</span><span class="sxs-lookup"><span data-stu-id="a6a61-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="a6a61-425">Posizionare il file *.deps.json* nel percorso seguente:</span><span class="sxs-lookup"><span data-stu-id="a6a61-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="a6a61-426">`{ADDITIONAL DEPENDENCIES PATH}`: Percorso aggiunto alla `DOTNET_ADDITIONAL_DEPS` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a6a61-426">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="a6a61-427">`{SHARED FRAMEWORK NAME}`: Framework condiviso necessario per questo file di dipendenze aggiuntivo.</span><span class="sxs-lookup"><span data-stu-id="a6a61-427">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="a6a61-428">`{SHARED FRAMEWORK VERSION}`: Versione minima del Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a6a61-428">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="a6a61-429">`{ENHANCEMENT ASSEMBLY NAME}`: Nome dell'assembly della funzionalità avanzata.</span><span class="sxs-lookup"><span data-stu-id="a6a61-429">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="a6a61-430">Nell'app di esempio (progetto *RuntimeStore*), il file di dipendenze aggiuntive viene posizionato nel percorso seguente:</span><span class="sxs-lookup"><span data-stu-id="a6a61-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="a6a61-431">Affinché il runtime possa individuare il percorso dell'archivio di runtime, il percorso del file di dipendenze aggiuntive viene aggiunto alla variabile di ambiente `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="a6a61-432">Nell'app di esempio (progetto *RuntimeStore*), la creazione dell'archivio di runtime e la generazione del file di dipendenze aggiuntive vengono eseguite tramite uno script di [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="a6a61-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="a6a61-433">Per esempi che illustrano come impostare le variabili di ambiente per diversi sistemi operativi, vedere [Usare più ambienti](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="a6a61-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="a6a61-434">**Distribuzione**</span><span class="sxs-lookup"><span data-stu-id="a6a61-434">**Deployment**</span></span>

<span data-ttu-id="a6a61-435">Per facilitare la distribuzione di un avvio dell'hosting in un ambiente con più computer, l'app di esempio crea una cartella *deplyment* nell'output pubblicato che contiene:</span><span class="sxs-lookup"><span data-stu-id="a6a61-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="a6a61-436">L'archivio di runtime dell'avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="a6a61-437">Il file di dipendenze dell'avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="a6a61-438">Uno script di PowerShell che crea o modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` e `DOTNET_ADDITIONAL_DEPS` per supportare l'attivazione dell'avvio dell'hosting.</span><span class="sxs-lookup"><span data-stu-id="a6a61-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="a6a61-439">Eseguire lo script da un prompt dei comandi di PowerShell amministrativo del sistema di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="a6a61-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="a6a61-440">Pacchetto NuGet</span><span class="sxs-lookup"><span data-stu-id="a6a61-440">NuGet package</span></span>

<span data-ttu-id="a6a61-441">È possibile includere un miglioramento di avvio dell'hosting in un pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="a6a61-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="a6a61-442">Il pacchetto include un attributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="a6a61-443">I tipi di avvio dell'hosting offerti dal pacchetto vengono resi disponibili all'app usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="a6a61-444">Il file di progetto dell'app migliorata crea un riferimento al pacchetto per l'avvio dell'hosting nel file di progetto dell'app (un riferimento in fase di compilazione).</span><span class="sxs-lookup"><span data-stu-id="a6a61-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="a6a61-445">Dopo aver inserito il riferimento in fase di compilazione, l'assembly di avvio dell'hosting e tutte le relative dipendenze vengono incorporate nel file delle dipendenze dell'app (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="a6a61-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="a6a61-446">Questo approccio si applica a un pacchetto di assembly di avvio dell'hosting pubblicato in [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="a6a61-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="a6a61-447">Il file di dipendenze dell'avvio dell'hosting viene reso disponibile all'app migliorata come descritto nella sezione [Archivio di runtime](#runtime-store) (senza un riferimento in fase di compilazione).</span><span class="sxs-lookup"><span data-stu-id="a6a61-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="a6a61-448">Per altre informazioni su pacchetti NuGet e l'archivio di runtime, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="a6a61-449">Come creare un pacchetto NuGet con strumenti multipiattaforma</span><span class="sxs-lookup"><span data-stu-id="a6a61-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="a6a61-450">Pubblicazione di pacchetti</span><span class="sxs-lookup"><span data-stu-id="a6a61-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="a6a61-451">Archivio pacchetti di runtime</span><span class="sxs-lookup"><span data-stu-id="a6a61-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="a6a61-452">Cartella bin del progetto</span><span class="sxs-lookup"><span data-stu-id="a6a61-452">Project bin folder</span></span>

<span data-ttu-id="a6a61-453">È possibile inserire un avvio dell'hosting tramite un assembly distribuito tramite *bin* nell'app migliorata.</span><span class="sxs-lookup"><span data-stu-id="a6a61-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="a6a61-454">I tipi di avvio dell'hosting offerti dall'assembly vengono resi disponibili all'app usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="a6a61-455">Il file di progetto dell'app migliorata crea un riferimento all'assembly nell'avvio dell'hosting startup (riferimento in fase di compilazione).</span><span class="sxs-lookup"><span data-stu-id="a6a61-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="a6a61-456">Dopo aver inserito il riferimento in fase di compilazione, l'assembly di avvio dell'hosting e tutte le relative dipendenze vengono incorporate nel file delle dipendenze dell'app (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="a6a61-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="a6a61-457">Questo approccio si applica quando lo scenario di distribuzione richiede la creazione di un riferimento in fase di compilazione all'assembly dell'avvio dell'hosting (file *DLL*) e lo spostamento dell'assembly in uno dei due elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="a6a61-458">Il progetto che lo utilizza.</span><span class="sxs-lookup"><span data-stu-id="a6a61-458">The consuming project.</span></span>
  * <span data-ttu-id="a6a61-459">Una posizione accessibile dal progetto che lo utilizza.</span><span class="sxs-lookup"><span data-stu-id="a6a61-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="a6a61-460">Il file di dipendenze dell'avvio dell'hosting viene reso disponibile all'app migliorata come descritto nella sezione [Archivio di runtime](#runtime-store) (senza un riferimento in fase di compilazione).</span><span class="sxs-lookup"><span data-stu-id="a6a61-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="a6a61-461">Quando la destinazione è .NET Framework, l'assembly può essere caricato nel contesto di caricamento predefinito, che in .NET Framework significa che l'assembly si trova in una delle posizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a6a61-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="a6a61-462">Percorso di base dell'applicazione: la cartella *bin* in cui si trova l'eseguibile dell'app (con*estensione exe*).</span><span class="sxs-lookup"><span data-stu-id="a6a61-462">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="a6a61-463">Global assembly cache (GAC): la GAC archivia gli assembly condivisi da diverse app .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a6a61-463">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="a6a61-464">Per ulteriori informazioni, vedere [procedura: installare un assembly nella global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) nella documentazione di .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a6a61-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="a6a61-465">Codice di esempio</span><span class="sxs-lookup"><span data-stu-id="a6a61-465">Sample code</span></span>

<span data-ttu-id="a6a61-466">Il [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([come eseguire il download](xref:index#how-to-download-a-sample)) illustra gli scenari di implementazione dell'avvio dell'hosting:</span><span class="sxs-lookup"><span data-stu-id="a6a61-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="a6a61-467">Due assembly di avvio dell'hosting (librerie di classi) impostano ognuno una coppia chiave-valore di configurazione in memoria:</span><span class="sxs-lookup"><span data-stu-id="a6a61-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="a6a61-468">Pacchetto NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="a6a61-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="a6a61-469">Libreria di classi (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="a6a61-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="a6a61-470">L'avvio dell'hosting viene attivato da un assembly distribuito tramite archivio di runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="a6a61-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="a6a61-471">All'avvio l'assembly aggiunge all'app due middleware che offrono informazioni di diagnostica su:</span><span class="sxs-lookup"><span data-stu-id="a6a61-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="a6a61-472">Servizi registrati</span><span class="sxs-lookup"><span data-stu-id="a6a61-472">Registered services</span></span>
  * <span data-ttu-id="a6a61-473">Indirizzo (schema, host, base del percorso, percorso, stringa di query)</span><span class="sxs-lookup"><span data-stu-id="a6a61-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="a6a61-474">Connessione (IP remoto, porta remota, IP locale, porta locale, certificato client)</span><span class="sxs-lookup"><span data-stu-id="a6a61-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="a6a61-475">Intestazioni della richiesta</span><span class="sxs-lookup"><span data-stu-id="a6a61-475">Request headers</span></span>
  * <span data-ttu-id="a6a61-476">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="a6a61-476">Environment variables</span></span>

<span data-ttu-id="a6a61-477">Per eseguire l'esempio:</span><span class="sxs-lookup"><span data-stu-id="a6a61-477">To run the sample:</span></span>

<span data-ttu-id="a6a61-478">**Attivazione da un pacchetto NuGet**</span><span class="sxs-lookup"><span data-stu-id="a6a61-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="a6a61-479">Compilare il pacchetto *HostingStartupPackage* con il comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="a6a61-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="a6a61-480">Aggiungere il nome dell'assembly del pacchetto *HostingStartupPackage* alla variabile di ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="a6a61-481">Compila ed esegui l'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-481">Compile and run the app.</span></span> <span data-ttu-id="a6a61-482">L'app migliorata include un riferimento al pacchetto (riferimento in fase di compilazione).</span><span class="sxs-lookup"><span data-stu-id="a6a61-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="a6a61-483">`<PropertyGroup>` nel file di progetto dell'app specifica l'output del progetto del pacchetto (*../HostingStartupPackage/bin/Debug*) come origine del pacchetto.</span><span class="sxs-lookup"><span data-stu-id="a6a61-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="a6a61-484">Ciò consente all'app di usare il pacchetto senza caricare il pacchetto in [NuGet.org](https://www.nuget.org/). Per ulteriori informazioni, vedere le note nel file di progetto di HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="a6a61-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="a6a61-485">Si noti che i valori di chiave di configurazione del servizio di cui è stato eseguito il rendering tramite la pagina Indice corrispondono ai valori impostati dal metodo `ServiceKeyInjection.Configure` del pacchetto.</span><span class="sxs-lookup"><span data-stu-id="a6a61-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="a6a61-486">Se si apportano modifiche al progetto *HostingStartupPackage* e lo si ricompila, cancellare le cache del pacchetto NuGet locale per assicurarsi che *HostingStartupApp* riceva il pacchetto aggiornato e non un pacchetto non aggiornato proveniente dalla cache locale.</span><span class="sxs-lookup"><span data-stu-id="a6a61-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="a6a61-487">Per cancellare le cache NuGet locali, eseguire il comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) seguente:</span><span class="sxs-lookup"><span data-stu-id="a6a61-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="a6a61-488">**Attivazione da una libreria di classi**</span><span class="sxs-lookup"><span data-stu-id="a6a61-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="a6a61-489">Compilare la libreria di classi *HostingStartupLibrary* con il comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="a6a61-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="a6a61-490">Aggiungere il nome dell'assembly della libreria di classi di *HostingStartupLibrary* alla variabile di ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="a6a61-491">Distribuire tramite *bin* l'assembly della libreria di classi all'app copiando il file *HostingStartupLibrary.dll* dall'output compilato della libreria di classi alla cartella *bin/Debug* dell'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="a6a61-492">Compila ed esegui l'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-492">Compile and run the app.</span></span> <span data-ttu-id="a6a61-493">`<ItemGroup>` nel file di progetto dell'app fa riferimento all'assembly della libreria di classi (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (riferimento in fase di compilazione).</span><span class="sxs-lookup"><span data-stu-id="a6a61-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="a6a61-494">Per altre informazioni, vedere le note nel file di progetto di HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="a6a61-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="a6a61-495">Si noti che i valori di chiave di configurazione del servizio di cui è stato eseguito il rendering tramite la pagina Indice corrispondono ai valori impostati dal metodo `ServiceKeyInjection.Configure` della libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="a6a61-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="a6a61-496">**Attivazione da un assembly distribuito tramite l'archivio di runtime**</span><span class="sxs-lookup"><span data-stu-id="a6a61-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="a6a61-497">Il progetto *StartupDiagnostics* usa [PowerShell](/powershell/scripting/powershell-scripting) per modificare il relativo file *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="a6a61-498">PowerShell viene installato per impostazione predefinita in Windows a partire da Windows 7 SP1 e Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="a6a61-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="a6a61-499">Per ottenere PowerShell su altre piattaforme, vedere [installazione di diverse versioni di PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="a6a61-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="a6a61-500">Eseguire lo script *build.ps1* nella cartella *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="a6a61-501">Lo script:</span><span class="sxs-lookup"><span data-stu-id="a6a61-501">The script:</span></span>
   * <span data-ttu-id="a6a61-502">Genera il `StartupDiagnostics` pacchetto nella cartella *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="a6a61-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="a6a61-503">Genera l'archivio di runtime per `StartupDiagnostics` nella cartella *store*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="a6a61-504">Il comando `dotnet store` nello script usa l' [identificatore di runtime (RID)](/dotnet/core/rid-catalog)`win7-x64` per un avvio dell'hosting distribuito su Windows.</span><span class="sxs-lookup"><span data-stu-id="a6a61-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="a6a61-505">Quando si specifica l'avvio dell'hosting per un runtime diverso, immettere il RID corretto nella riga 37 dello script.</span><span class="sxs-lookup"><span data-stu-id="a6a61-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="a6a61-506">L'archivio di runtime di verrà `StartupDiagnostics` spostato in un secondo momento nell'archivio di runtime dell'utente o del sistema nel computer in cui verrà utilizzato l'assembly.</span><span class="sxs-lookup"><span data-stu-id="a6a61-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="a6a61-507">Il percorso di installazione dell'archivio di runtime utente per l' `StartupDiagnostics` assembly è *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="a6a61-508">Genera `additionalDeps` per `StartupDiagnostics` nella cartella *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="a6a61-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="a6a61-509">Le dipendenze aggiuntive verranno spostate in un secondo momento alle dipendenze aggiuntive del sistema o dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a6a61-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="a6a61-510">Il `StartupDiagnostics` percorso di installazione delle dipendenze aggiuntive dell'utente è *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. NETCore. app/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="a6a61-511">Posiziona il file *deploy.ps1* nella cartella *deployment*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="a6a61-512">Eseguire lo script *deploy.ps1* nella cartella *deployment*.</span><span class="sxs-lookup"><span data-stu-id="a6a61-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="a6a61-513">Lo script aggiunge:</span><span class="sxs-lookup"><span data-stu-id="a6a61-513">The script appends:</span></span>
   * <span data-ttu-id="a6a61-514">`StartupDiagnostics` alla variabile di ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="a6a61-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="a6a61-515">Percorso delle dipendenze di avvio dell'hosting (nella cartella di *distribuzione* del progetto RuntimeStore) alla `DOTNET_ADDITIONAL_DEPS` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a6a61-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="a6a61-516">Percorso dell'archivio di runtime (nella cartella di *distribuzione* del progetto RuntimeStore) alla `DOTNET_SHARED_STORE` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="a6a61-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="a6a61-517">Eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="a6a61-517">Run the sample app.</span></span>
1. <span data-ttu-id="a6a61-518">Richiedere l'endpoint `/services` per visualizzare i servizi registrati dell'app.</span><span class="sxs-lookup"><span data-stu-id="a6a61-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="a6a61-519">Richiedere l'endpoint `/diag` per visualizzare le informazioni di diagnostica.</span><span class="sxs-lookup"><span data-stu-id="a6a61-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
