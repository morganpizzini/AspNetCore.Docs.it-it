---
title: Eseguire la migrazione da ASP.NET ad ASP.NET Core 2.0
author: isaac2004
description: Ricevere indicazioni per la migrazione di applicazioni ASP.NET MVC o API Web esistenti a ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
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
uid: migration/mvc2
ms.openlocfilehash: bd2c33d35a3433532b48f6615a81adac8d03b9ee
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634540"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="f4143-103">Eseguire la migrazione da ASP.NET ad ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="f4143-103">Migrate from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="f4143-104">Di [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="f4143-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="f4143-105">Questo articolo offre una guida di riferimento per la migrazione delle applicazioni ASP.NET ad ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="f4143-105">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f4143-106">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="f4143-106">Prerequisites</span></span>

<span data-ttu-id="f4143-107">Installare **uno** dei seguenti download da [.NET: Windows](https://dotnet.microsoft.com/download):</span><span class="sxs-lookup"><span data-stu-id="f4143-107">Install **one** of the following from [.NET Downloads: Windows](https://dotnet.microsoft.com/download):</span></span>

* <span data-ttu-id="f4143-108">.NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="f4143-108">.NET Core SDK</span></span>
* <span data-ttu-id="f4143-109">Visual Studio per Windows</span><span class="sxs-lookup"><span data-stu-id="f4143-109">Visual Studio for Windows</span></span>
  * <span data-ttu-id="f4143-110">**ASP.NET e carico di lavoro di sviluppo Web**</span><span class="sxs-lookup"><span data-stu-id="f4143-110">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="f4143-111">Carico di lavoro di **sviluppo multipiattaforma .NET Core**</span><span class="sxs-lookup"><span data-stu-id="f4143-111">**.NET Core cross-platform development** workload</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="f4143-112">Framework di destinazione</span><span class="sxs-lookup"><span data-stu-id="f4143-112">Target frameworks</span></span>

<span data-ttu-id="f4143-113">I progetti di ASP.NET Core 2.0 offrono agli sviluppatori la flessibilità necessaria per scegliere .NET Core, .NET Framework o entrambi come destinazione.</span><span class="sxs-lookup"><span data-stu-id="f4143-113">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="f4143-114">Vedere [Scelta di .NET Core o .NET Framework per le app server](/dotnet/standard/choosing-core-framework-server) per determinare quale framework di destinazione è più appropriato.</span><span class="sxs-lookup"><span data-stu-id="f4143-114">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="f4143-115">Quando la destinazione è .NET Framework, i progetti devono fare riferimento a singoli pacchetti NuGet.</span><span class="sxs-lookup"><span data-stu-id="f4143-115">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="f4143-116">La scelta di .NET Core come destinazione consente di eliminare numerosi riferimenti espliciti ai pacchetti, grazie al [metapacchetto](xref:fundamentals/metapackage) di ASP.NET 2.0 Core.</span><span class="sxs-lookup"><span data-stu-id="f4143-116">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="f4143-117">Installare il metapacchetto `Microsoft.AspNetCore.All` nel progetto:</span><span class="sxs-lookup"><span data-stu-id="f4143-117">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="f4143-118">Quando si usa il metapacchetto, con l'app non viene distribuito alcun pacchetto a cui si fa riferimento nel metapacchetto.</span><span class="sxs-lookup"><span data-stu-id="f4143-118">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="f4143-119">L'archivio di runtime di .NET Core include questi asset, che vengono precompilati per migliorare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="f4143-119">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="f4143-120"><xref:fundamentals/metapackage>Per ulteriori informazioni, vedere.</span><span class="sxs-lookup"><span data-stu-id="f4143-120">See <xref:fundamentals/metapackage> for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="f4143-121">Differenze di struttura del progetto</span><span class="sxs-lookup"><span data-stu-id="f4143-121">Project structure differences</span></span>

<span data-ttu-id="f4143-122">Il formato di file *CSPROJ* è stato semplificato in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f4143-122">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="f4143-123">Alcune modifiche importanti includono:</span><span class="sxs-lookup"><span data-stu-id="f4143-123">Some notable changes include:</span></span>

* <span data-ttu-id="f4143-124">L'inclusione esplicita dei file non è necessaria affinché i file vengano considerati parte del progetto.</span><span class="sxs-lookup"><span data-stu-id="f4143-124">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="f4143-125">In questo modo si riduce il rischio di conflitti di merge XML quando si lavora con team di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="f4143-125">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
* <span data-ttu-id="f4143-126">Non sono presenti riferimenti basati su GUID ad altri progetti e questo migliora la leggibilità dei file.</span><span class="sxs-lookup"><span data-stu-id="f4143-126">There are no GUID-based references to other projects, which improves file readability.</span></span>
* <span data-ttu-id="f4143-127">Il file può essere modificato senza scaricarlo in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f4143-127">The file can be edited without unloading it in Visual Studio:</span></span>

  ![Modificare l'opzione CSPROJ del menu di scelta rapida in Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="f4143-129">Sostituzione di file Global.asax</span><span class="sxs-lookup"><span data-stu-id="f4143-129">Global.asax file replacement</span></span>

<span data-ttu-id="f4143-130">In ASP.NET Core è stato introdotto un nuovo meccanismo per l'avvio automatico delle app.</span><span class="sxs-lookup"><span data-stu-id="f4143-130">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="f4143-131">Il punto di ingresso per le applicazioni ASP.NET è il file *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="f4143-131">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="f4143-132">Attività quali la configurazione della route e le registrazioni di area e filtro vengono gestite nel file *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="f4143-132">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="f4143-133">Con questo approccio l'applicazione e il server a cui viene distribuita vengono accoppiati in un modo che interferisce con l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="f4143-133">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="f4143-134">Al fine di disaccoppiare gli elementi, è stata introdotta la funzionalità [OWIN](https://owin.org/) che offre un modo più semplice di usare più framework insieme.</span><span class="sxs-lookup"><span data-stu-id="f4143-134">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="f4143-135">OWIN offre una pipeline per aggiungere solo i moduli necessari.</span><span class="sxs-lookup"><span data-stu-id="f4143-135">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="f4143-136">L'ambiente host accetta una funzione di [avvio](xref:fundamentals/startup) per configurare i servizi e la pipeline delle richieste dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="f4143-136">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="f4143-137">`Startup` registra un set di middleware con l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="f4143-137">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="f4143-138">Per ogni richiesta, l'applicazione chiama ognuno dei componenti middleware con il puntatore iniziale di un elenco collegato a un set esistente di gestori.</span><span class="sxs-lookup"><span data-stu-id="f4143-138">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="f4143-139">Ogni componente middleware può aggiungere uno o più gestori alla pipeline di gestione delle richieste.</span><span class="sxs-lookup"><span data-stu-id="f4143-139">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="f4143-140">Questa operazione viene eseguita restituendo un riferimento al gestore che rappresenta il nuovo inizio dell'elenco.</span><span class="sxs-lookup"><span data-stu-id="f4143-140">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="f4143-141">Ogni gestore è responsabile della memorizzazione e della chiamata del gestore successivo nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="f4143-141">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="f4143-142">Con ASP.NET Core, il punto di ingresso a un'applicazione è `Startup` e non esiste più una dipendenza da *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="f4143-142">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="f4143-143">Quando si usa OWIN con .NET Framework, usare una pipeline simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="f4143-143">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="f4143-144">Ciò consente di configurare le route predefinite e impostare come predefinito XmlSerialization per Json.</span><span class="sxs-lookup"><span data-stu-id="f4143-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="f4143-145">Aggiungere altro middleware a questa pipeline in base alle esigenze, ad esempio caricamento di servizi, impostazioni di configurazione, file statici e così via.</span><span class="sxs-lookup"><span data-stu-id="f4143-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="f4143-146">ASP.NET Core usa un approccio simile, ma non si basa su OWIN per gestire la voce.</span><span class="sxs-lookup"><span data-stu-id="f4143-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="f4143-147">L'operazione viene invece eseguita usando il metodo *Program.cs* `Main` (simile alle applicazioni console) e `Startup` viene caricato da tale posizione.</span><span class="sxs-lookup"><span data-stu-id="f4143-147">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="f4143-148">`Startup` deve includere un metodo `Configure`.</span><span class="sxs-lookup"><span data-stu-id="f4143-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="f4143-149">In `Configure` aggiungere il middleware necessario alla pipeline.</span><span class="sxs-lookup"><span data-stu-id="f4143-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="f4143-150">Nell'esempio seguente, estratto dal modello di sito Web predefinito, vengono usati vari metodi di estensione per configurare la pipeline con il supporto per:</span><span class="sxs-lookup"><span data-stu-id="f4143-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="f4143-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="f4143-151">BrowserLink</span></span>](https://vswebessentials.com/features/browserlink)
* <span data-ttu-id="f4143-152">Pagine di errore</span><span class="sxs-lookup"><span data-stu-id="f4143-152">Error pages</span></span>
* <span data-ttu-id="f4143-153">File statici</span><span class="sxs-lookup"><span data-stu-id="f4143-153">Static files</span></span>
* <span data-ttu-id="f4143-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="f4143-154">ASP.NET Core MVC</span></span>
* Identity

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="f4143-155">L'host e applicazione sono stati disaccoppiati e questo offre la possibilità di passare a una piattaforma diversa in futuro.</span><span class="sxs-lookup"><span data-stu-id="f4143-155">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="f4143-156">Per un riferimento più approfondito a ASP.NET Core avvio e middleware, vedere <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="f4143-156">For a more in-depth reference to ASP.NET Core Startup and Middleware, see <xref:fundamentals/startup>.</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="f4143-157">Archiviazione delle configurazioni</span><span class="sxs-lookup"><span data-stu-id="f4143-157">Storing configurations</span></span>

<span data-ttu-id="f4143-158">ASP.NET supporta le impostazioni di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="f4143-158">ASP.NET supports storing settings.</span></span> <span data-ttu-id="f4143-159">Tali impostazioni vengono usate, ad esempio, per supportare l'ambiente in cui vengono distribuite le applicazioni.</span><span class="sxs-lookup"><span data-stu-id="f4143-159">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="f4143-160">Una prassi comune era archiviare tutte le coppie chiave-valore personalizzate nella sezione `<appSettings>` del file *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="f4143-160">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="f4143-161">Le applicazioni leggono queste impostazioni usando la raccolta `ConfigurationManager.AppSettings` nello spazio dei nomi `System.Configuration`:</span><span class="sxs-lookup"><span data-stu-id="f4143-161">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="f4143-162">ASP.NET Core è in grado di archiviare i dati di configurazione per l'applicazione in tutti i file e di caricarli come parte dell'avvio automatico del middleware.</span><span class="sxs-lookup"><span data-stu-id="f4143-162">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="f4143-163">Il file predefinito usato nei modelli di progetto è *appSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f4143-163">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="f4143-164">Il caricamento del file in un'istanza di `IConfiguration` all'interno dell'applicazione viene eseguito in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f4143-164">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="f4143-165">L'app legge da `Configuration` per ottenere le impostazioni:</span><span class="sxs-lookup"><span data-stu-id="f4143-165">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="f4143-166">Sono disponibili estensioni di questo approccio che aumentano l'efficacia del processo, ad esempio l'uso dell'[inserimento delle dipendenze](xref:fundamentals/dependency-injection) per caricare un servizio con questi valori.</span><span class="sxs-lookup"><span data-stu-id="f4143-166">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="f4143-167">L'approccio con inserimento delle dipendenze offre un set fortemente tipizzato di oggetti di configurazione.</span><span class="sxs-lookup"><span data-stu-id="f4143-167">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

<span data-ttu-id="f4143-168">**Nota:** Per un riferimento più approfondito alla configurazione di ASP.NET Core, vedere <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="f4143-168">**Note:** For a more in-depth reference to ASP.NET Core configuration, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="f4143-169">Inserimento delle dipendenze nativo</span><span class="sxs-lookup"><span data-stu-id="f4143-169">Native dependency injection</span></span>

<span data-ttu-id="f4143-170">Un obiettivo importante nella compilazione di applicazioni scalabili di grandi dimensioni è l'accoppiamento libero di componenti e servizi.</span><span class="sxs-lookup"><span data-stu-id="f4143-170">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="f4143-171">L' [inserimento di dipendenze](xref:fundamentals/dependency-injection) è una tecnica comune per ottenere questo aspetto ed è un componente nativo di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f4143-171">[Dependency injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="f4143-172">Nelle applicazioni ASP.NET gli sviluppatori si affidano a una libreria di terze parti per implementare l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f4143-172">In ASP.NET applications, developers rely on a third-party library to implement dependency injection.</span></span> <span data-ttu-id="f4143-173">Una di queste librerie è [Unity](https://github.com/unitycontainer/unity) e fa parte di Modelli e procedure Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f4143-173">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="f4143-174">Un esempio di configurazione dell'inserimento delle dipendenze con Unity consiste nell'implementazione `IDependencyResolver` di che esegue il wrapping di un `UnityContainer` :</span><span class="sxs-lookup"><span data-stu-id="f4143-174">An example of setting up dependency injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="f4143-175">Creare un'istanza di `UnityContainer`, registrare il servizio e impostare il sistema di risoluzione delle dipendenze di `HttpConfiguration` sulla nuova istanza di `UnityResolver` per il contenitore:</span><span class="sxs-lookup"><span data-stu-id="f4143-175">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="f4143-176">Inserire `IProductRepository` dove necessario:</span><span class="sxs-lookup"><span data-stu-id="f4143-176">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="f4143-177">Poiché l'inserimento delle dipendenze fa parte di ASP.NET Core, è possibile aggiungere il servizio in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f4143-177">Because dependency injection is part of ASP.NET Core, you can add your service in the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="f4143-178">Il repository può essere inserito in qualsiasi posizione, analogamente a Unity.</span><span class="sxs-lookup"><span data-stu-id="f4143-178">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="f4143-179">Per ulteriori informazioni sull'inserimento delle dipendenze in ASP.NET Core, vedere <xref:fundamentals/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="f4143-179">For more information on dependency injection in ASP.NET Core, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="f4143-180">Gestione dei file statici</span><span class="sxs-lookup"><span data-stu-id="f4143-180">Serving static files</span></span>

<span data-ttu-id="f4143-181">Una parte importante dello sviluppo Web è la possibilità di distribuire asset statici sul lato client.</span><span class="sxs-lookup"><span data-stu-id="f4143-181">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="f4143-182">Gli esempi più comuni di file statici sono HTML, CSS, Javascript e immagini.</span><span class="sxs-lookup"><span data-stu-id="f4143-182">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="f4143-183">Questi file devono essere salvati nella posizione di pubblicazione dell'app (o della rete CDN) con riferimenti che ne consentano il caricamento da parte di una richiesta.</span><span class="sxs-lookup"><span data-stu-id="f4143-183">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="f4143-184">Questo processo è stato modificato in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f4143-184">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="f4143-185">In ASP.NET i file statici vengono archiviati in directory diverse e viene fatto riferimento ai file nelle viste.</span><span class="sxs-lookup"><span data-stu-id="f4143-185">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="f4143-186">In ASP.NET Core i file statici vengono archiviati nella radice Web (\* &lt; &gt; /wwwroot radice del contenuto\*), a meno che non sia configurato diversamente.</span><span class="sxs-lookup"><span data-stu-id="f4143-186">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="f4143-187">I file vengono caricati nella pipeline delle richieste chiamando il metodo di estensione `UseStaticFiles` da `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f4143-187">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

<span data-ttu-id="f4143-188">**Nota:** se la destinazione è .NET Framework, installare il pacchetto NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="f4143-188">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="f4143-189">Ad esempio, un asset immagine nella cartella *wwwroot/images* è accessibile al browser in corrispondenza di una posizione come `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="f4143-189">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="f4143-190">**Nota:** Per informazioni più dettagliate sulla conservazione dei file statici in ASP.NET Core, vedere <xref:fundamentals/static-files> .</span><span class="sxs-lookup"><span data-stu-id="f4143-190">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see <xref:fundamentals/static-files>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f4143-191">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f4143-191">Additional resources</span></span>

* [<span data-ttu-id="f4143-192">Portabilità in .NET Core - Librerie</span><span class="sxs-lookup"><span data-stu-id="f4143-192">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
