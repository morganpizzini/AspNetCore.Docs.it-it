---
title: Introduzione a ASP.NET Core
author: rick-anderson
description: Ottieni un'introduzione a ASP.NET Core, un framework open source multipiattaforma, ad alte prestazioni per la creazione di app moderne, abilitate per il cloud e connesse a Internet.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: c5a5a0ada996d88cb9252da25b5580fe0cf46f0b
ms.sourcegitcommit: 636efd1afc0a1e6fd4b12ae3a542917b356abb93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81615942"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="17756-103">Introduzione a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17756-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="17756-104">Di [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), e [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="17756-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="17756-105">ASP.NET Core è un framework open [source](https://github.com/dotnet/aspnetcore) multipiattaforma, ad alte prestazioni e open source per la creazione di app moderne, abilitate per il cloud e connesse a Internet.</span><span class="sxs-lookup"><span data-stu-id="17756-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="17756-106">Con ASP.NET Core, è possibile:</span><span class="sxs-lookup"><span data-stu-id="17756-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="17756-107">Crea app e servizi Web, app [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) e back-end per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="17756-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="17756-108">Usare gli strumenti di sviluppo preferiti in Windows, macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="17756-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="17756-109">Distribuire nel cloud o in locale.</span><span class="sxs-lookup"><span data-stu-id="17756-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="17756-110">Eseguire su [.NET Core](/dotnet/core/introduction).</span><span class="sxs-lookup"><span data-stu-id="17756-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="17756-111">Perché scegliere ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="17756-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="17756-112">Milioni di sviluppatori utilizzano o hanno utilizzato [ASP.NET 4.x](/aspnet/overview) per creare app Web.</span><span class="sxs-lookup"><span data-stu-id="17756-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="17756-113">ASP.NET Core è una riprogettazione di ASP.NET 4.x, incluse le modifiche architettoniche che si traducono in un framework più snello e modulare.</span><span class="sxs-lookup"><span data-stu-id="17756-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="17756-114">Compilare API web e interfaccia utente web tramite ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="17756-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="17756-115">ASP.NET Core MVC offre funzionalità per la compilazione di [API Web](xref:tutorials/first-web-api) e [app Web](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="17756-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="17756-116">Il [Model-View-Controller (MVC)](xref:mvc/overview) consente di rendere le API web e le app web testabili.</span><span class="sxs-lookup"><span data-stu-id="17756-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="17756-117">[Razor Pages](xref:razor-pages/index) è un modello di programmazione basato su pagine che semplifica la creazione dell'interfaccia utente Web.</span><span class="sxs-lookup"><span data-stu-id="17756-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="17756-118">[Il markup Razor](xref:mvc/views/razor) offre una sintassi produttiva per le [pagine Razor](xref:razor-pages/index) e le [visualizzazioni MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="17756-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="17756-119">Gli [helper tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei file Razor.</span><span class="sxs-lookup"><span data-stu-id="17756-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="17756-120">Il supporto incorporato per [più formati di dati e per la negoziazione del contenuto](xref:web-api/advanced/formatting) consente alle API web di raggiungere una vasta gamma di client, inclusi i browser e i dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="17756-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="17756-121">L'[associazione di modelli](xref:mvc/models/model-binding) esegue automaticamente il mapping dei dati dalle richieste HTTP ai parametri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="17756-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="17756-122">La [convalida dei modelli](xref:mvc/models/validation) esegue automaticamente la convalida sul lato server e sul lato client.</span><span class="sxs-lookup"><span data-stu-id="17756-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="17756-123">Sviluppo lato client</span><span class="sxs-lookup"><span data-stu-id="17756-123">Client-side development</span></span>

<span data-ttu-id="17756-124">ASP.NET Core si integra perfettamente con popolari framework e librerie sul lato client, inclusi [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) e [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="17756-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="17756-125">Per altre informazioni, vedere <xref:blazor/index> e gli argomenti correlati in *Sviluppo lato client*.</span><span class="sxs-lookup"><span data-stu-id="17756-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="17756-126">ASP.NET framework di destinazione principali</span><span class="sxs-lookup"><span data-stu-id="17756-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="17756-127">ASP.NET Core 3.x and later can only target .NET Core.</span><span class="sxs-lookup"><span data-stu-id="17756-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="17756-128">In genere, ASP.NET Core è costituito da librerie [.NET Standard.Generally, a Core](/dotnet/standard/net-standard) is composed of .NET Standard libraries.</span><span class="sxs-lookup"><span data-stu-id="17756-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="17756-129">Le librerie scritte con .NET Standard 2.0 supportano l'esecuzione su [qualsiasi piattaforma .NET che implementa .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="17756-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="17756-130">Usare .NET Core come destinazione offre diversi vantaggi, che aumentano con ogni versione.</span><span class="sxs-lookup"><span data-stu-id="17756-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="17756-131">Alcuni vantaggi di .NET Core in .NET Framework sono:</span><span class="sxs-lookup"><span data-stu-id="17756-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="17756-132">Funzionamento multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="17756-132">Cross-platform.</span></span> <span data-ttu-id="17756-133">Funziona su Windows, macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="17756-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="17756-134">prestazioni migliorate</span><span class="sxs-lookup"><span data-stu-id="17756-134">Improved performance</span></span>
* [<span data-ttu-id="17756-135">Controllo delle versioni affiancate</span><span class="sxs-lookup"><span data-stu-id="17756-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="17756-136">Nuove API</span><span class="sxs-lookup"><span data-stu-id="17756-136">New APIs</span></span>
* <span data-ttu-id="17756-137">Aprire origine</span><span class="sxs-lookup"><span data-stu-id="17756-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="17756-138">Percorso di apprendimento consigliato</span><span class="sxs-lookup"><span data-stu-id="17756-138">Recommended learning path</span></span>

<span data-ttu-id="17756-139">Ti consigliamo la seguente sequenza di esercitazioni per un'introduzione allo sviluppo di app ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="17756-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="17756-140">Seguire un'esercitazione per il tipo di app che si vuole sviluppare o gestire.</span><span class="sxs-lookup"><span data-stu-id="17756-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="17756-141">Tipo di app</span><span class="sxs-lookup"><span data-stu-id="17756-141">App type</span></span>  |<span data-ttu-id="17756-142">Scenario</span><span class="sxs-lookup"><span data-stu-id="17756-142">Scenario</span></span>  |<span data-ttu-id="17756-143">Esercitazione</span><span class="sxs-lookup"><span data-stu-id="17756-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="17756-144">app Web</span><span class="sxs-lookup"><span data-stu-id="17756-144">Web app</span></span>                   | <span data-ttu-id="17756-145">Nuovo sviluppo dell'interfaccia utente Web sul lato server</span><span class="sxs-lookup"><span data-stu-id="17756-145">New server-side web UI development</span></span> |[<span data-ttu-id="17756-146">Introduzione a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="17756-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="17756-147">app Web</span><span class="sxs-lookup"><span data-stu-id="17756-147">Web app</span></span>                   | <span data-ttu-id="17756-148">Gestione di un'app MVC</span><span class="sxs-lookup"><span data-stu-id="17756-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="17756-149">Introduzione a MVC</span><span class="sxs-lookup"><span data-stu-id="17756-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="17756-150">app Web</span><span class="sxs-lookup"><span data-stu-id="17756-150">Web app</span></span>                   | <span data-ttu-id="17756-151">Sviluppo dell'interfaccia utente Web lato clientClient-side web UI development</span><span class="sxs-lookup"><span data-stu-id="17756-151">Client-side web UI development</span></span> |[<span data-ttu-id="17756-152">Inizia a usare Blazor</span><span class="sxs-lookup"><span data-stu-id="17756-152">Get started with Blazor</span></span>](xref:tutorials/first-blazor-app) |
   |<span data-ttu-id="17756-153">API Web</span><span class="sxs-lookup"><span data-stu-id="17756-153">Web API</span></span>                   | <span data-ttu-id="17756-154">Servizi HTTP RESTful</span><span class="sxs-lookup"><span data-stu-id="17756-154">RESTful HTTP services</span></span> |<span data-ttu-id="17756-155">[Creare un'API WebCreate a web API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="17756-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="17756-156">App Chiamata di procedura remota</span><span class="sxs-lookup"><span data-stu-id="17756-156">Remote Procedure Call app</span></span> | <span data-ttu-id="17756-157">Servizi basati su contratto che utilizzano i buffer di protocollo</span><span class="sxs-lookup"><span data-stu-id="17756-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="17756-158">Introduzione all'uso di un servizio gRPC</span><span class="sxs-lookup"><span data-stu-id="17756-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="17756-159">App in tempo reale</span><span class="sxs-lookup"><span data-stu-id="17756-159">Real-time app</span></span>             | <span data-ttu-id="17756-160">Comunicazione bidirezionale tra server e client connessi</span><span class="sxs-lookup"><span data-stu-id="17756-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="17756-161">Introduzione a SignalR</span><span class="sxs-lookup"><span data-stu-id="17756-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="17756-162">Seguire un'esercitazione che illustra come eseguire l'accesso ai dati di base.</span><span class="sxs-lookup"><span data-stu-id="17756-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="17756-163">Scenario</span><span class="sxs-lookup"><span data-stu-id="17756-163">Scenario</span></span>  |<span data-ttu-id="17756-164">Esercitazione</span><span class="sxs-lookup"><span data-stu-id="17756-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="17756-165">Nuovo sviluppo</span><span class="sxs-lookup"><span data-stu-id="17756-165">New development</span></span>        |[<span data-ttu-id="17756-166">Razor Pages con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="17756-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="17756-167">Gestione di un'app MVC</span><span class="sxs-lookup"><span data-stu-id="17756-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="17756-168">MVC con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="17756-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="17756-169">Leggi una panoramica di ASP.NET [fondamentali](xref:fundamentals/index) di base che si applicano a tutti i tipi di app.</span><span class="sxs-lookup"><span data-stu-id="17756-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="17756-170">Sfogliare il sommario per altri argomenti di interesse.</span><span class="sxs-lookup"><span data-stu-id="17756-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="17756-171">&dagger;C'è anche un [tutorial API web interattivo](/learn/modules/build-web-api-net-core).</span><span class="sxs-lookup"><span data-stu-id="17756-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="17756-172">Non è richiesta alcuna installazione locale di strumenti di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="17756-172">No local installation of development tools is required.</span></span> <span data-ttu-id="17756-173">Il codice viene eseguito in [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) nel browser e [l'urlo](https://curl.haxx.se/) viene usato per il test.</span><span class="sxs-lookup"><span data-stu-id="17756-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="17756-174">Eseguire la migrazione da .NET FrameworkMigrate from .NET Framework</span><span class="sxs-lookup"><span data-stu-id="17756-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="17756-175">Per una guida di riferimento alla migrazione di <xref:migration/proper-to-2x/index>ASP.NET applicazioni 4.x a ASP.NET Core, vedere .</span><span class="sxs-lookup"><span data-stu-id="17756-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="17756-176">ASP.NET Core è un framework open [source](https://github.com/dotnet/aspnetcore) multipiattaforma, ad alte prestazioni e open source per la creazione di app moderne, abilitate per il cloud e connesse a Internet.</span><span class="sxs-lookup"><span data-stu-id="17756-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="17756-177">Con ASP.NET Core, è possibile:</span><span class="sxs-lookup"><span data-stu-id="17756-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="17756-178">Crea app e servizi Web, app [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) e back-end per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="17756-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="17756-179">Usare gli strumenti di sviluppo preferiti in Windows, macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="17756-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="17756-180">Distribuire nel cloud o in locale.</span><span class="sxs-lookup"><span data-stu-id="17756-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="17756-181">Eseguire in [.NET Core o .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="17756-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="17756-182">Perché scegliere ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="17756-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="17756-183">Milioni di sviluppatori utilizzano o hanno utilizzato [ASP.NET 4.x](/aspnet/overview) per creare app Web.</span><span class="sxs-lookup"><span data-stu-id="17756-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="17756-184">ASP.NET Core è una riprogettazione di ASP.NET 4.x, con modifiche a livello di architettura che comportano un framework più efficiente e modulare.</span><span class="sxs-lookup"><span data-stu-id="17756-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="17756-185">Compilare API web e interfaccia utente web tramite ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="17756-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="17756-186">ASP.NET Core MVC offre funzionalità per la compilazione di [API Web](xref:tutorials/first-web-api) e [app Web](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="17756-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="17756-187">Il [Model-View-Controller (MVC)](xref:mvc/overview) consente di rendere le API web e le app web testabili.</span><span class="sxs-lookup"><span data-stu-id="17756-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="17756-188">[Razor Pages](xref:razor-pages/index) è un modello di programmazione basato su pagine che semplifica la creazione dell'interfaccia utente Web.</span><span class="sxs-lookup"><span data-stu-id="17756-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="17756-189">[Il markup Razor](xref:mvc/views/razor) offre una sintassi produttiva per le [pagine Razor](xref:razor-pages/index) e le [visualizzazioni MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="17756-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="17756-190">Gli [helper tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei file Razor.</span><span class="sxs-lookup"><span data-stu-id="17756-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="17756-191">Il supporto incorporato per [più formati di dati e per la negoziazione del contenuto](xref:web-api/advanced/formatting) consente alle API web di raggiungere una vasta gamma di client, inclusi i browser e i dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="17756-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="17756-192">L'[associazione di modelli](xref:mvc/models/model-binding) esegue automaticamente il mapping dei dati dalle richieste HTTP ai parametri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="17756-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="17756-193">La [convalida dei modelli](xref:mvc/models/validation) esegue automaticamente la convalida sul lato server e sul lato client.</span><span class="sxs-lookup"><span data-stu-id="17756-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="17756-194">Sviluppo lato client</span><span class="sxs-lookup"><span data-stu-id="17756-194">Client-side development</span></span>

<span data-ttu-id="17756-195">ASP.NET Core si integra perfettamente con popolari framework e librerie sul lato client, inclusi [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) e [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="17756-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="17756-196">Per altre informazioni, vedere <xref:blazor/index> e gli argomenti correlati in *Sviluppo lato client*.</span><span class="sxs-lookup"><span data-stu-id="17756-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="17756-197">ASP.NET Core per .NET Framework</span><span class="sxs-lookup"><span data-stu-id="17756-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="17756-198">ASP.NET Core 2.x può avere come destinazione .NET Core o .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="17756-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="17756-199">Le app ASP.NET Core destinate a .NET Framework non sono multipiattaforma, ma funzionano solo in Windows.</span><span class="sxs-lookup"><span data-stu-id="17756-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="17756-200">ASP.NET Core 2.x è costituito a livello generale da librerie [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="17756-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="17756-201">Le librerie scritte con .NET Standard 2.0 supportano l'esecuzione su [qualsiasi piattaforma .NET che implementa .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="17756-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="17756-202">ASP.NET Core 2.x è supportato nelle versioni di .NET Framework che implementano .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="17756-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="17756-203">È consigliata la versione più recente di .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="17756-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="17756-204">.NET Framework 4.6.1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="17756-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="17756-205">L'esecuzione di ASP.NET Core 3.0 e versioni successive sarà consentita solo in .NET Core.</span><span class="sxs-lookup"><span data-stu-id="17756-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="17756-206">Per altri dettagli riguardanti questa modifica, vedere [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Una prima occhiata alle modifiche previste per ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="17756-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="17756-207">Usare .NET Core come destinazione offre diversi vantaggi, che aumentano con ogni versione.</span><span class="sxs-lookup"><span data-stu-id="17756-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="17756-208">Alcuni vantaggi di .NET Core in .NET Framework sono:</span><span class="sxs-lookup"><span data-stu-id="17756-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="17756-209">Funzionamento multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="17756-209">Cross-platform.</span></span> <span data-ttu-id="17756-210">Esecuzione con macOS, Linux e Windows.</span><span class="sxs-lookup"><span data-stu-id="17756-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="17756-211">prestazioni migliorate</span><span class="sxs-lookup"><span data-stu-id="17756-211">Improved performance</span></span>
* [<span data-ttu-id="17756-212">Controllo delle versioni affiancate</span><span class="sxs-lookup"><span data-stu-id="17756-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="17756-213">Nuove API</span><span class="sxs-lookup"><span data-stu-id="17756-213">New APIs</span></span>
* <span data-ttu-id="17756-214">Aprire origine</span><span class="sxs-lookup"><span data-stu-id="17756-214">Open source</span></span>

<span data-ttu-id="17756-215">È in corso un'intensa attività volta a colmare il divario da .NET Framework a .NET Core relativo alle API.</span><span class="sxs-lookup"><span data-stu-id="17756-215">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="17756-216">[Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) ha reso disponibili in .NET Core migliaia di API solo per Windows.</span><span class="sxs-lookup"><span data-stu-id="17756-216">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="17756-217">Queste API non erano disponibili in .NET Core 1. x.</span><span class="sxs-lookup"><span data-stu-id="17756-217">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="17756-218">Percorso di apprendimento consigliato</span><span class="sxs-lookup"><span data-stu-id="17756-218">Recommended learning path</span></span>

<span data-ttu-id="17756-219">Per un'introduzione allo sviluppo delle app ASP.NET Core, è consigliabile eseguire la sequenza di esercitazioni e articoli seguente:</span><span class="sxs-lookup"><span data-stu-id="17756-219">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="17756-220">Seguire un'esercitazione per il tipo di app che si desidera sviluppare o gestire.</span><span class="sxs-lookup"><span data-stu-id="17756-220">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="17756-221">Tipo di app</span><span class="sxs-lookup"><span data-stu-id="17756-221">App type</span></span>  |<span data-ttu-id="17756-222">Scenario</span><span class="sxs-lookup"><span data-stu-id="17756-222">Scenario</span></span>  |<span data-ttu-id="17756-223">Esercitazione</span><span class="sxs-lookup"><span data-stu-id="17756-223">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="17756-224">app Web</span><span class="sxs-lookup"><span data-stu-id="17756-224">Web app</span></span>                   | <span data-ttu-id="17756-225">Per un nuovo sviluppo</span><span class="sxs-lookup"><span data-stu-id="17756-225">For new development</span></span>        |[<span data-ttu-id="17756-226">Introduzione a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="17756-226">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="17756-227">app Web</span><span class="sxs-lookup"><span data-stu-id="17756-227">Web app</span></span>                   | <span data-ttu-id="17756-228">Per gestire un'app MVC</span><span class="sxs-lookup"><span data-stu-id="17756-228">For maintaining an MVC app</span></span> |[<span data-ttu-id="17756-229">Introduzione a MVC</span><span class="sxs-lookup"><span data-stu-id="17756-229">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="17756-230">API Web</span><span class="sxs-lookup"><span data-stu-id="17756-230">Web API</span></span>                   |                            |<span data-ttu-id="17756-231">[Creare un'API WebCreate a web API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="17756-231">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="17756-232">App in tempo reale</span><span class="sxs-lookup"><span data-stu-id="17756-232">Real-time app</span></span>             |                            |[<span data-ttu-id="17756-233">Introduzione a SignalR</span><span class="sxs-lookup"><span data-stu-id="17756-233">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="17756-234">Seguire un'esercitazione che illustra come eseguire l'accesso ai dati di base.</span><span class="sxs-lookup"><span data-stu-id="17756-234">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="17756-235">Scenario</span><span class="sxs-lookup"><span data-stu-id="17756-235">Scenario</span></span>  |<span data-ttu-id="17756-236">Esercitazione</span><span class="sxs-lookup"><span data-stu-id="17756-236">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="17756-237">Per un nuovo sviluppo</span><span class="sxs-lookup"><span data-stu-id="17756-237">For new development</span></span>        |[<span data-ttu-id="17756-238">Razor Pages con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="17756-238">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="17756-239">Per gestire un'app MVC</span><span class="sxs-lookup"><span data-stu-id="17756-239">For maintaining an MVC app</span></span> |[<span data-ttu-id="17756-240">MVC con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="17756-240">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="17756-241">Leggi una panoramica di ASP.NET [fondamentali](xref:fundamentals/index) di base che si applicano a tutti i tipi di app.</span><span class="sxs-lookup"><span data-stu-id="17756-241">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="17756-242">Esplorare il Sommario per cercare altri argomenti di interesse.</span><span class="sxs-lookup"><span data-stu-id="17756-242">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="17756-243">&dagger;C'è anche un [tutorial API web che si segue interamente nel browser](/learn/modules/build-web-api-net-core), nessuna installazione IDE locale necessaria.</span><span class="sxs-lookup"><span data-stu-id="17756-243">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="17756-244">Il codice viene eseguito in un'[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) e per il testing viene usato [curl](https://curl.haxx.se/).</span><span class="sxs-lookup"><span data-stu-id="17756-244">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="17756-245">Eseguire la migrazione da .NET FrameworkMigrate from .NET Framework</span><span class="sxs-lookup"><span data-stu-id="17756-245">Migrate from .NET Framework</span></span>

<span data-ttu-id="17756-246">Per una guida di riferimento alla migrazione <xref:migration/proper-to-2x/index>di ASP.NET app a ASP.NET Core, vedere .</span><span class="sxs-lookup"><span data-stu-id="17756-246">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="17756-247">Come scaricare un esempio</span><span class="sxs-lookup"><span data-stu-id="17756-247">How to download a sample</span></span>

<span data-ttu-id="17756-248">Molti articoli ed esercitazioni includono collegamenti al codice di esempio.</span><span class="sxs-lookup"><span data-stu-id="17756-248">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="17756-249">[Scaricare il file ZIP del repository ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="17756-249">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="17756-250">Decomprimere il file *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="17756-250">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="17756-251">Usare l'URL nel collegamento di esempio per passare alla directory di esempio.</span><span class="sxs-lookup"><span data-stu-id="17756-251">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="17756-252">Direttive per il preprocessore nel codice di esempio</span><span class="sxs-lookup"><span data-stu-id="17756-252">Preprocessor directives in sample code</span></span>

<span data-ttu-id="17756-253">Per illustrare più scenari, `#define` `#if-#else/#elif-#endif` le app di esempio usano le direttive e del preprocessore per compilare ed eseguire in modo selettivo diverse sezioni di codice di esempio.</span><span class="sxs-lookup"><span data-stu-id="17756-253">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="17756-254">Per gli esempi che usano questo `#define` approccio, impostare la direttiva all'inizio dei file di C , per definire il simbolo associato allo scenario che si desidera eseguire.</span><span class="sxs-lookup"><span data-stu-id="17756-254">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="17756-255">Alcuni esempi richiedono la definizione del simbolo nella parte superiore di più file per eseguire uno scenario.</span><span class="sxs-lookup"><span data-stu-id="17756-255">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="17756-256">Ad esempio, l'elenco di simboli `#define` seguente indica che sono disponibili quattro scenari, ovvero uno scenario per simbolo.</span><span class="sxs-lookup"><span data-stu-id="17756-256">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="17756-257">La configurazione di esempio corrente esegue lo scenario `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="17756-257">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="17756-258">Per modificare l'esempio in modo che venga eseguito lo scenario `ExpandDefault`, definire il simbolo `ExpandDefault` e lasciare i simboli rimanenti con commento:</span><span class="sxs-lookup"><span data-stu-id="17756-258">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="17756-259">Per altre informazioni sull'uso delle [direttive del preprocessore C#](/dotnet/csharp/language-reference/preprocessor-directives/) per compilare in modo selettivo le sezioni di codice, vedere [#define (Riferimenti per C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) e [#if (Riferimenti per C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="17756-259">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="17756-260">Aree del codice di esempio</span><span class="sxs-lookup"><span data-stu-id="17756-260">Regions in sample code</span></span>

<span data-ttu-id="17756-261">Alcune app di esempio contengono sezioni di codice racchiuse tra [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) e [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) direttive di C.</span><span class="sxs-lookup"><span data-stu-id="17756-261">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="17756-262">Il sistema di compilazione della documentazione inserisce queste aree negli argomenti della documentazione visualizzabile.</span><span class="sxs-lookup"><span data-stu-id="17756-262">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="17756-263">I nomi delle aree in genere contengono la parola "frammento".</span><span class="sxs-lookup"><span data-stu-id="17756-263">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="17756-264">L'esempio seguente mostra un'area denominata `snippet_WebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="17756-264">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="17756-265">Il file markdown dell'argomento fa riferimento al frammento di codice C# precedente con la riga seguente:</span><span class="sxs-lookup"><span data-stu-id="17756-265">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="17756-266">È possibile ignorare (o `#region` rimuovere) le direttive e `#endregion` che circondano il codice.</span><span class="sxs-lookup"><span data-stu-id="17756-266">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="17756-267">Non modificare il codice all'interno di queste direttive se si prevede di eseguire gli scenari di esempio descritti nell'argomento.</span><span class="sxs-lookup"><span data-stu-id="17756-267">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="17756-268">È possibile modificare il codice durante la sperimentazione con altri scenari.</span><span class="sxs-lookup"><span data-stu-id="17756-268">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="17756-269">Per altre informazioni, vedere [Contribuire alla documentazione ASP.NET: Frammenti di codice](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="17756-269">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="17756-270">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="17756-270">Next steps</span></span>

<span data-ttu-id="17756-271">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="17756-271">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="17756-272">Nozioni fondamentali su ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17756-272">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="17756-273">[La trasmissione settimanale della community di ASP.NET](https://live.asp.net/) offre una presentazione dei progressi e dei piani del team.</span><span class="sxs-lookup"><span data-stu-id="17756-273">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="17756-274">Vengono segnalati nuovi blog e software di terze parti.</span><span class="sxs-lookup"><span data-stu-id="17756-274">It features new blogs and third-party software.</span></span>
