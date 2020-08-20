---
title: Introduzione a ASP.NET Core
author: rick-anderson
description: Introduzione a ASP.NET Core, un Framework multipiattaforma, ad alte prestazioni, open source per la compilazione di app moderne, abilitate per il cloud e connesse a Internet.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: index
ms.openlocfilehash: f1235337748bacdb6eaa775a5853238bf729e605
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634553"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="91841-103">Introduzione a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91841-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="91841-104">Di [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), e [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="91841-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="91841-105">ASP.NET Core è un Framework multipiattaforma, ad alte prestazioni, [Open Source](https://github.com/dotnet/aspnetcore) per la compilazione di app moderne, abilitate per il cloud e connesse a Internet.</span><span class="sxs-lookup"><span data-stu-id="91841-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="91841-106">Con ASP.NET Core, è possibile:</span><span class="sxs-lookup"><span data-stu-id="91841-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="91841-107">Creazione di servizi e app Web, [Internet delle cose](https://www.microsoft.com/internet-of-things/) app e backend per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="91841-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="91841-108">Usare gli strumenti di sviluppo preferiti in Windows, macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="91841-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="91841-109">Distribuire nel cloud o in locale.</span><span class="sxs-lookup"><span data-stu-id="91841-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="91841-110">Eseguire in [.NET Core](/dotnet/core/introduction).</span><span class="sxs-lookup"><span data-stu-id="91841-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="91841-111">Perché scegliere ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="91841-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="91841-112">Milioni di sviluppatori usano o hanno usato [ASP.NET 4. x](/aspnet/overview) per creare app Web.</span><span class="sxs-lookup"><span data-stu-id="91841-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="91841-113">ASP.NET Core è una riprogettazione di ASP.NET 4. x, incluse le modifiche architetturali che hanno come risultato un Framework più snello e modulare.</span><span class="sxs-lookup"><span data-stu-id="91841-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="91841-114">Compilare API web e interfaccia utente web tramite ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="91841-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="91841-115">ASP.NET Core MVC offre funzionalità per la compilazione di [API Web](xref:tutorials/first-web-api) e [app Web](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="91841-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="91841-116">Il [Model-View-Controller (MVC)](xref:mvc/overview) consente di rendere le API web e le app web testabili.</span><span class="sxs-lookup"><span data-stu-id="91841-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="91841-117">[ Razor Pages](xref:razor-pages/index) è un modello di programmazione basato su pagine che rende la creazione di un'interfaccia utente Web più semplice e produttiva.</span><span class="sxs-lookup"><span data-stu-id="91841-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="91841-118">il [ Razor markup](xref:mvc/views/razor) fornisce una sintassi produttiva per le [ Razor pagine](xref:razor-pages/index) e le [visualizzazioni MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="91841-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="91841-119">Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.</span><span class="sxs-lookup"><span data-stu-id="91841-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="91841-120">Il supporto incorporato per [più formati di dati e per la negoziazione del contenuto](xref:web-api/advanced/formatting) consente alle API web di raggiungere una vasta gamma di client, inclusi i browser e i dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="91841-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="91841-121">L'[associazione di modelli](xref:mvc/models/model-binding) esegue automaticamente il mapping dei dati dalle richieste HTTP ai parametri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="91841-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="91841-122">La [convalida dei modelli](xref:mvc/models/validation) esegue automaticamente la convalida sul lato server e sul lato client.</span><span class="sxs-lookup"><span data-stu-id="91841-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="91841-123">Sviluppo lato client</span><span class="sxs-lookup"><span data-stu-id="91841-123">Client-side development</span></span>

<span data-ttu-id="91841-124">ASP.NET Core si integra facilmente con le librerie e i framework lato client più diffusi, tra cui [Blazor](xref:blazor/index) , [angolare](xref:spa/angular), [reagire](xref:spa/react)e [bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="91841-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="91841-125">Per altre informazioni, vedere <xref:blazor/index> e gli argomenti correlati in *Sviluppo lato client*.</span><span class="sxs-lookup"><span data-stu-id="91841-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="91841-126">Framework di destinazione ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91841-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="91841-127">ASP.NET Core 3. x e versioni successive possono essere destinati solo a .NET Core.</span><span class="sxs-lookup"><span data-stu-id="91841-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="91841-128">In genere, ASP.NET Core è costituito da librerie di [.NET standard](/dotnet/standard/net-standard) .</span><span class="sxs-lookup"><span data-stu-id="91841-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="91841-129">Le librerie scritte con .NET Standard 2.0 supportano l'esecuzione su [qualsiasi piattaforma .NET che implementa .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="91841-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="91841-130">Usare .NET Core come destinazione offre diversi vantaggi, che aumentano con ogni versione.</span><span class="sxs-lookup"><span data-stu-id="91841-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="91841-131">Alcuni vantaggi di .NET Core in .NET Framework sono:</span><span class="sxs-lookup"><span data-stu-id="91841-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="91841-132">Funzionamento multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="91841-132">Cross-platform.</span></span> <span data-ttu-id="91841-133">Viene eseguito in Windows, macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="91841-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="91841-134">prestazioni migliorate</span><span class="sxs-lookup"><span data-stu-id="91841-134">Improved performance</span></span>
* [<span data-ttu-id="91841-135">Controllo delle versioni affiancato</span><span class="sxs-lookup"><span data-stu-id="91841-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="91841-136">Nuove API</span><span class="sxs-lookup"><span data-stu-id="91841-136">New APIs</span></span>
* <span data-ttu-id="91841-137">Open source</span><span class="sxs-lookup"><span data-stu-id="91841-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="91841-138">Percorso di apprendimento consigliato</span><span class="sxs-lookup"><span data-stu-id="91841-138">Recommended learning path</span></span>

<span data-ttu-id="91841-139">Per un'introduzione allo sviluppo di app ASP.NET Core, è consigliabile usare la sequenza di esercitazioni seguente:</span><span class="sxs-lookup"><span data-stu-id="91841-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="91841-140">Seguire un'esercitazione per il tipo di app che si vuole sviluppare o gestire.</span><span class="sxs-lookup"><span data-stu-id="91841-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="91841-141">Tipo di app</span><span class="sxs-lookup"><span data-stu-id="91841-141">App type</span></span>  |<span data-ttu-id="91841-142">Scenario</span><span class="sxs-lookup"><span data-stu-id="91841-142">Scenario</span></span>  |<span data-ttu-id="91841-143">Esercitazione</span><span class="sxs-lookup"><span data-stu-id="91841-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="91841-144">app Web</span><span class="sxs-lookup"><span data-stu-id="91841-144">Web app</span></span>                   | <span data-ttu-id="91841-145">Nuovo sviluppo di interfaccia utente Web sul lato server</span><span class="sxs-lookup"><span data-stu-id="91841-145">New server-side web UI development</span></span> |[<span data-ttu-id="91841-146">Introduzione alle Razor pagine</span><span class="sxs-lookup"><span data-stu-id="91841-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="91841-147">app Web</span><span class="sxs-lookup"><span data-stu-id="91841-147">Web app</span></span>                   | <span data-ttu-id="91841-148">Gestione di un'app MVC</span><span class="sxs-lookup"><span data-stu-id="91841-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="91841-149">Introduzione a MVC</span><span class="sxs-lookup"><span data-stu-id="91841-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="91841-150">app Web</span><span class="sxs-lookup"><span data-stu-id="91841-150">Web app</span></span>                   | <span data-ttu-id="91841-151">Sviluppo di interfaccia utente Web lato client</span><span class="sxs-lookup"><span data-stu-id="91841-151">Client-side web UI development</span></span> |[<span data-ttu-id="91841-152">Introduzione a Blazor</span><span class="sxs-lookup"><span data-stu-id="91841-152">Get started with Blazor</span></span>](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |<span data-ttu-id="91841-153">API Web</span><span class="sxs-lookup"><span data-stu-id="91841-153">Web API</span></span>                   | <span data-ttu-id="91841-154">Servizi HTTP RESTful</span><span class="sxs-lookup"><span data-stu-id="91841-154">RESTful HTTP services</span></span> |<span data-ttu-id="91841-155">[Creare un'API Web](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="91841-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="91841-156">App RPC (Remote Procedure Call)</span><span class="sxs-lookup"><span data-stu-id="91841-156">Remote Procedure Call app</span></span> | <span data-ttu-id="91841-157">Servizi di primo contratto che usano i buffer del protocollo</span><span class="sxs-lookup"><span data-stu-id="91841-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="91841-158">Introduzione all'uso di un servizio gRPC</span><span class="sxs-lookup"><span data-stu-id="91841-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="91841-159">App in tempo reale</span><span class="sxs-lookup"><span data-stu-id="91841-159">Real-time app</span></span>             | <span data-ttu-id="91841-160">Comunicazione bidirezionale tra i server e i client connessi</span><span class="sxs-lookup"><span data-stu-id="91841-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="91841-161">Introduzione a SignalR</span><span class="sxs-lookup"><span data-stu-id="91841-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="91841-162">Seguire un'esercitazione che illustra come eseguire l'accesso ai dati di base.</span><span class="sxs-lookup"><span data-stu-id="91841-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="91841-163">Scenario</span><span class="sxs-lookup"><span data-stu-id="91841-163">Scenario</span></span>  |<span data-ttu-id="91841-164">Esercitazione</span><span class="sxs-lookup"><span data-stu-id="91841-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="91841-165">Nuovo sviluppo</span><span class="sxs-lookup"><span data-stu-id="91841-165">New development</span></span>        |[<span data-ttu-id="91841-166">Razor Pagine con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="91841-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="91841-167">Gestione di un'app MVC</span><span class="sxs-lookup"><span data-stu-id="91841-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="91841-168">MVC con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="91841-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="91841-169">Leggi una panoramica dei [concetti di base](xref:fundamentals/index) ASP.NET Core che si applicano a tutti i tipi di app.</span><span class="sxs-lookup"><span data-stu-id="91841-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="91841-170">Esplorare il sommario per altri argomenti di interesse.</span><span class="sxs-lookup"><span data-stu-id="91841-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="91841-171">&dagger;È disponibile anche un' [esercitazione sull'API Web interattiva](/learn/modules/build-web-api-net-core).</span><span class="sxs-lookup"><span data-stu-id="91841-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="91841-172">Non è necessaria alcuna installazione locale degli strumenti di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="91841-172">No local installation of development tools is required.</span></span> <span data-ttu-id="91841-173">Il codice viene eseguito in un [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/) nel browser e [curl](https://curl.haxx.se/) viene usato per i test.</span><span class="sxs-lookup"><span data-stu-id="91841-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="91841-174">Eseguire la migrazione da .NET Framework</span><span class="sxs-lookup"><span data-stu-id="91841-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="91841-175">Per una guida di riferimento alla migrazione delle app ASP.NET 4. x ai ASP.NET Core, vedere <xref:migration/proper-to-2x/index> .</span><span class="sxs-lookup"><span data-stu-id="91841-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="91841-176">ASP.NET Core è un Framework multipiattaforma, ad alte prestazioni, [Open Source](https://github.com/dotnet/aspnetcore) per la compilazione di app moderne, abilitate per il cloud e connesse a Internet.</span><span class="sxs-lookup"><span data-stu-id="91841-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="91841-177">Con ASP.NET Core, è possibile:</span><span class="sxs-lookup"><span data-stu-id="91841-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="91841-178">Creazione di servizi e app Web, [Internet delle cose](https://www.microsoft.com/internet-of-things/) app e backend per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="91841-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="91841-179">Usare gli strumenti di sviluppo preferiti in Windows, macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="91841-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="91841-180">Distribuire nel cloud o in locale.</span><span class="sxs-lookup"><span data-stu-id="91841-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="91841-181">Eseguire in [.NET Core o .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="91841-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="91841-182">Perché scegliere ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="91841-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="91841-183">Milioni di sviluppatori usano o hanno usato [ASP.NET 4. x](/aspnet/overview) per creare app Web.</span><span class="sxs-lookup"><span data-stu-id="91841-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="91841-184">ASP.NET Core è una riprogettazione di ASP.NET 4.x, con modifiche a livello di architettura che comportano un framework più efficiente e modulare.</span><span class="sxs-lookup"><span data-stu-id="91841-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="91841-185">Compilare API web e interfaccia utente web tramite ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="91841-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="91841-186">ASP.NET Core MVC offre funzionalità per la compilazione di [API Web](xref:tutorials/first-web-api) e [app Web](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="91841-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="91841-187">Il [Model-View-Controller (MVC)](xref:mvc/overview) consente di rendere le API web e le app web testabili.</span><span class="sxs-lookup"><span data-stu-id="91841-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="91841-188">[ Razor Pages](xref:razor-pages/index) è un modello di programmazione basato su pagine che rende la creazione di un'interfaccia utente Web più semplice e produttiva.</span><span class="sxs-lookup"><span data-stu-id="91841-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="91841-189">il [ Razor markup](xref:mvc/views/razor) fornisce una sintassi produttiva per le [ Razor pagine](xref:razor-pages/index) e le [visualizzazioni MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="91841-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="91841-190">Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.</span><span class="sxs-lookup"><span data-stu-id="91841-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="91841-191">Il supporto incorporato per [più formati di dati e per la negoziazione del contenuto](xref:web-api/advanced/formatting) consente alle API web di raggiungere una vasta gamma di client, inclusi i browser e i dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="91841-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="91841-192">L'[associazione di modelli](xref:mvc/models/model-binding) esegue automaticamente il mapping dei dati dalle richieste HTTP ai parametri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="91841-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="91841-193">La [convalida dei modelli](xref:mvc/models/validation) esegue automaticamente la convalida sul lato server e sul lato client.</span><span class="sxs-lookup"><span data-stu-id="91841-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="91841-194">Sviluppo lato client</span><span class="sxs-lookup"><span data-stu-id="91841-194">Client-side development</span></span>

<span data-ttu-id="91841-195">ASP.NET Core si integra facilmente con le librerie e i framework lato client più diffusi, tra cui [Blazor](xref:blazor/index) , [angolare](xref:spa/angular), [reagire](xref:spa/react)e [bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="91841-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="91841-196">Per altre informazioni, vedere <xref:blazor/index> e gli argomenti correlati in *Sviluppo lato client*.</span><span class="sxs-lookup"><span data-stu-id="91841-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="91841-197">ASP.NET Core per .NET Framework</span><span class="sxs-lookup"><span data-stu-id="91841-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="91841-198">ASP.NET Core 2.x può avere come destinazione .NET Core o .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="91841-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="91841-199">Le app ASP.NET Core destinate a .NET Framework non sono multipiattaforma, ma funzionano solo in Windows.</span><span class="sxs-lookup"><span data-stu-id="91841-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="91841-200">ASP.NET Core 2.x è costituito a livello generale da librerie [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="91841-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="91841-201">Le librerie scritte con .NET Standard 2.0 supportano l'esecuzione su [qualsiasi piattaforma .NET che implementa .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="91841-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="91841-202">ASP.NET Core 2.x è supportato nelle versioni di .NET Framework che implementano .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="91841-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="91841-203">È consigliabile .NET Framework versione più recente.</span><span class="sxs-lookup"><span data-stu-id="91841-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="91841-204">.NET Framework 4.6.1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="91841-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="91841-205">L'esecuzione di ASP.NET Core 3.0 e versioni successive sarà consentita solo in .NET Core.</span><span class="sxs-lookup"><span data-stu-id="91841-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="91841-206">Per altri dettagli riguardanti questa modifica, vedere [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Una prima occhiata alle modifiche previste per ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="91841-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="91841-207">Usare .NET Core come destinazione offre diversi vantaggi, che aumentano con ogni versione.</span><span class="sxs-lookup"><span data-stu-id="91841-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="91841-208">Alcuni vantaggi di .NET Core in .NET Framework sono:</span><span class="sxs-lookup"><span data-stu-id="91841-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="91841-209">Funzionamento multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="91841-209">Cross-platform.</span></span> <span data-ttu-id="91841-210">Esecuzione con macOS, Linux e Windows.</span><span class="sxs-lookup"><span data-stu-id="91841-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="91841-211">prestazioni migliorate</span><span class="sxs-lookup"><span data-stu-id="91841-211">Improved performance</span></span>
* [<span data-ttu-id="91841-212">Controllo delle versioni affiancato</span><span class="sxs-lookup"><span data-stu-id="91841-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="91841-213">Nuove API</span><span class="sxs-lookup"><span data-stu-id="91841-213">New APIs</span></span>
* <span data-ttu-id="91841-214">Open source</span><span class="sxs-lookup"><span data-stu-id="91841-214">Open source</span></span>

<span data-ttu-id="91841-215">Per semplificare la divariazione dell'API da .NET Framework a .NET Core, [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) ha reso disponibili migliaia di API solo per Windows disponibili in .NET Core.</span><span class="sxs-lookup"><span data-stu-id="91841-215">To help close the API gap from .NET Framework to .NET Core, the [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="91841-216">Queste API non erano disponibili in .NET Core 1. x.</span><span class="sxs-lookup"><span data-stu-id="91841-216">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="91841-217">Percorso di apprendimento consigliato</span><span class="sxs-lookup"><span data-stu-id="91841-217">Recommended learning path</span></span>

<span data-ttu-id="91841-218">Per un'introduzione allo sviluppo delle app ASP.NET Core, è consigliabile eseguire la sequenza di esercitazioni e articoli seguente:</span><span class="sxs-lookup"><span data-stu-id="91841-218">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="91841-219">Seguire un'esercitazione per il tipo di app che si vuole sviluppare o gestire.</span><span class="sxs-lookup"><span data-stu-id="91841-219">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="91841-220">Tipo di app</span><span class="sxs-lookup"><span data-stu-id="91841-220">App type</span></span>  |<span data-ttu-id="91841-221">Scenario</span><span class="sxs-lookup"><span data-stu-id="91841-221">Scenario</span></span>  |<span data-ttu-id="91841-222">Esercitazione</span><span class="sxs-lookup"><span data-stu-id="91841-222">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="91841-223">app Web</span><span class="sxs-lookup"><span data-stu-id="91841-223">Web app</span></span>                   | <span data-ttu-id="91841-224">Per un nuovo sviluppo</span><span class="sxs-lookup"><span data-stu-id="91841-224">For new development</span></span>        |[<span data-ttu-id="91841-225">Introduzione alle Razor pagine</span><span class="sxs-lookup"><span data-stu-id="91841-225">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="91841-226">app Web</span><span class="sxs-lookup"><span data-stu-id="91841-226">Web app</span></span>                   | <span data-ttu-id="91841-227">Per gestire un'app MVC</span><span class="sxs-lookup"><span data-stu-id="91841-227">For maintaining an MVC app</span></span> |[<span data-ttu-id="91841-228">Introduzione a MVC</span><span class="sxs-lookup"><span data-stu-id="91841-228">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="91841-229">API Web</span><span class="sxs-lookup"><span data-stu-id="91841-229">Web API</span></span>                   |                            |<span data-ttu-id="91841-230">[Creare un'API Web](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="91841-230">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="91841-231">App in tempo reale</span><span class="sxs-lookup"><span data-stu-id="91841-231">Real-time app</span></span>             |                            |[<span data-ttu-id="91841-232">Introduzione a SignalR</span><span class="sxs-lookup"><span data-stu-id="91841-232">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="91841-233">Seguire un'esercitazione che illustra come eseguire l'accesso ai dati di base.</span><span class="sxs-lookup"><span data-stu-id="91841-233">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="91841-234">Scenario</span><span class="sxs-lookup"><span data-stu-id="91841-234">Scenario</span></span>  |<span data-ttu-id="91841-235">Esercitazione</span><span class="sxs-lookup"><span data-stu-id="91841-235">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="91841-236">Per un nuovo sviluppo</span><span class="sxs-lookup"><span data-stu-id="91841-236">For new development</span></span>        |[<span data-ttu-id="91841-237">Razor Pagine con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="91841-237">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="91841-238">Per gestire un'app MVC</span><span class="sxs-lookup"><span data-stu-id="91841-238">For maintaining an MVC app</span></span> |[<span data-ttu-id="91841-239">MVC con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="91841-239">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="91841-240">Leggi una panoramica dei [concetti di base](xref:fundamentals/index) ASP.NET Core che si applicano a tutti i tipi di app.</span><span class="sxs-lookup"><span data-stu-id="91841-240">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="91841-241">Esplorare il Sommario per cercare altri argomenti di interesse.</span><span class="sxs-lookup"><span data-stu-id="91841-241">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="91841-242">&dagger;È anche disponibile un' [esercitazione sull'API Web che segue completamente nel browser](/learn/modules/build-web-api-net-core), non è necessaria alcuna installazione dell'IDE locale.</span><span class="sxs-lookup"><span data-stu-id="91841-242">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="91841-243">Il codice viene eseguito in un'[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) e per il testing viene usato [curl](https://curl.haxx.se/).</span><span class="sxs-lookup"><span data-stu-id="91841-243">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="91841-244">Eseguire la migrazione da .NET Framework</span><span class="sxs-lookup"><span data-stu-id="91841-244">Migrate from .NET Framework</span></span>

<span data-ttu-id="91841-245">Per una guida di riferimento alla migrazione delle app ASP.NET ai ASP.NET Core, vedere <xref:migration/proper-to-2x/index> .</span><span class="sxs-lookup"><span data-stu-id="91841-245">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="91841-246">Come scaricare un esempio</span><span class="sxs-lookup"><span data-stu-id="91841-246">How to download a sample</span></span>

<span data-ttu-id="91841-247">Molti articoli ed esercitazioni includono collegamenti al codice di esempio.</span><span class="sxs-lookup"><span data-stu-id="91841-247">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="91841-248">[Scaricare il file ZIP del repository ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="91841-248">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="91841-249">Decomprimere il file *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="91841-249">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="91841-250">Usare l'URL nel collegamento di esempio per passare alla directory di esempio.</span><span class="sxs-lookup"><span data-stu-id="91841-250">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="91841-251">Direttive per il preprocessore nel codice di esempio</span><span class="sxs-lookup"><span data-stu-id="91841-251">Preprocessor directives in sample code</span></span>

<span data-ttu-id="91841-252">Per illustrare più scenari, le app di esempio usano le `#define` `#if-#else/#elif-#endif` direttive per il preprocessore e per compilare ed eseguire in modo selettivo sezioni diverse del codice di esempio.</span><span class="sxs-lookup"><span data-stu-id="91841-252">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="91841-253">Per gli esempi che usano questo approccio, impostare la `#define` direttiva all'inizio dei file C# per definire il simbolo associato allo scenario che si vuole eseguire.</span><span class="sxs-lookup"><span data-stu-id="91841-253">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="91841-254">Alcuni esempi richiedono la definizione del simbolo nella parte superiore di più file per eseguire uno scenario.</span><span class="sxs-lookup"><span data-stu-id="91841-254">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="91841-255">Ad esempio, l'elenco di simboli `#define` seguente indica che sono disponibili quattro scenari, ovvero uno scenario per simbolo.</span><span class="sxs-lookup"><span data-stu-id="91841-255">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="91841-256">La configurazione di esempio corrente esegue lo scenario `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="91841-256">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="91841-257">Per modificare l'esempio in modo che venga eseguito lo scenario `ExpandDefault`, definire il simbolo `ExpandDefault` e lasciare i simboli rimanenti con commento:</span><span class="sxs-lookup"><span data-stu-id="91841-257">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="91841-258">Per altre informazioni sull'uso delle [direttive del preprocessore C#](/dotnet/csharp/language-reference/preprocessor-directives/) per compilare in modo selettivo le sezioni di codice, vedere [#define (Riferimenti per C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) e [#if (Riferimenti per C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="91841-258">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="91841-259">Aree del codice di esempio</span><span class="sxs-lookup"><span data-stu-id="91841-259">Regions in sample code</span></span>

<span data-ttu-id="91841-260">Alcune app di esempio contengono sezioni di codice racchiuse tra [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) e [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) direttive C#.</span><span class="sxs-lookup"><span data-stu-id="91841-260">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="91841-261">Il sistema di compilazione della documentazione inserisce queste aree negli argomenti della documentazione visualizzabile.</span><span class="sxs-lookup"><span data-stu-id="91841-261">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="91841-262">I nomi delle aree in genere contengono la parola "frammento".</span><span class="sxs-lookup"><span data-stu-id="91841-262">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="91841-263">L'esempio seguente mostra un'area denominata `snippet_WebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="91841-263">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="91841-264">Il file markdown dell'argomento fa riferimento al frammento di codice C# precedente con la riga seguente:</span><span class="sxs-lookup"><span data-stu-id="91841-264">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="91841-265">È possibile ignorare o rimuovere in modo sicuro le `#region` `#endregion` direttive e che racchiudono il codice.</span><span class="sxs-lookup"><span data-stu-id="91841-265">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="91841-266">Non modificare il codice all'interno di queste direttive se si prevede di eseguire gli scenari di esempio descritti nell'argomento.</span><span class="sxs-lookup"><span data-stu-id="91841-266">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="91841-267">È possibile modificare il codice durante la sperimentazione con altri scenari.</span><span class="sxs-lookup"><span data-stu-id="91841-267">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="91841-268">Per altre informazioni, vedere [Contribuire alla documentazione ASP.NET: Frammenti di codice](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="91841-268">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="91841-269">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="91841-269">Next steps</span></span>

<span data-ttu-id="91841-270">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="91841-270">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="91841-271">Nozioni fondamentali su ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91841-271">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="91841-272">[La trasmissione settimanale della community di ASP.NET](https://live.asp.net/) offre una presentazione dei progressi e dei piani del team.</span><span class="sxs-lookup"><span data-stu-id="91841-272">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="91841-273">Vengono segnalati nuovi blog e software di terze parti.</span><span class="sxs-lookup"><span data-stu-id="91841-273">It features new blogs and third-party software.</span></span>
