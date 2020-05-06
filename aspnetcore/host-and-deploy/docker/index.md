---
title: Eseguire l'hosting di ASP.NET Core nei contenitori Docker
author: rick-anderson
description: Collegamenti alle risorse che illustrano come eseguire l'hosting di app ASP.NET Core nei contenitori Docker.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/docker/index
ms.openlocfilehash: 94996f279929f6e3837efb613c99927edbd9143e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774587"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="c4e78-103">Eseguire l'hosting di ASP.NET Core nei contenitori Docker</span><span class="sxs-lookup"><span data-stu-id="c4e78-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="c4e78-104">Per apprendere come eseguire l'hosting di app ASP.NET Core in Docker, leggere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="c4e78-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="c4e78-105">Introduzione a contenitori e Docker</span><span class="sxs-lookup"><span data-stu-id="c4e78-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="c4e78-106">Descrive la containerizzazione, un approccio allo sviluppo del software in cui un'applicazione o un servizio, le relative dipendenze e la configurazione corrispondente sono inclusi in uno stesso pacchetto come immagine del contenitore.</span><span class="sxs-lookup"><span data-stu-id="c4e78-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="c4e78-107">L'immagine può essere testata e quindi distribuita in un host.</span><span class="sxs-lookup"><span data-stu-id="c4e78-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="c4e78-108">Che cos'è Docker</span><span class="sxs-lookup"><span data-stu-id="c4e78-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="c4e78-109">Informazioni su Docker, un progetto open source per automatizzare la distribuzione di app come contenitori portabili e autosufficienti eseguibili nel cloud o in locale.</span><span class="sxs-lookup"><span data-stu-id="c4e78-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="c4e78-110">Terminologia di Docker</span><span class="sxs-lookup"><span data-stu-id="c4e78-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="c4e78-111">Informazioni su termini e definizioni relativi alla tecnologia Docker.</span><span class="sxs-lookup"><span data-stu-id="c4e78-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="c4e78-112">Contenitori, immagini e registri Docker</span><span class="sxs-lookup"><span data-stu-id="c4e78-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="c4e78-113">Informazioni sulle modalità con cui le immagini del contenitore Docker vengono archiviate in un registro immagini per una distribuzione uniforme nei diversi ambienti.</span><span class="sxs-lookup"><span data-stu-id="c4e78-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

<span data-ttu-id="c4e78-114"><xref:host-and-deploy/docker/building-net-docker-images> Informazioni sulle procedure per compilare un'app di ASP.NET Core e inserirla in un contenitore Docker.</span><span class="sxs-lookup"><span data-stu-id="c4e78-114"><xref:host-and-deploy/docker/building-net-docker-images> Learn how to build and dockerize an ASP.NET Core app.</span></span> <span data-ttu-id="c4e78-115">Si analizzano le immagini Docker gestite da Microsoft e si esaminano casi d'uso.</span><span class="sxs-lookup"><span data-stu-id="c4e78-115">Explore Docker images maintained by Microsoft and examine use cases.</span></span>

[<span data-ttu-id="c4e78-116">Strumenti contenitore di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4e78-116">Visual Studio Container Tools</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="c4e78-117">Informazioni su come Visual Studio supporta la compilazione, il debug e l'esecuzione di app ASP.NET Core destinate a .NET Framework o .NET Core in Docker per Windows.</span><span class="sxs-lookup"><span data-stu-id="c4e78-117">Discover how Visual Studio supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="c4e78-118">Sono supportati sia contenitori Windows che contenitori Linux.</span><span class="sxs-lookup"><span data-stu-id="c4e78-118">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="c4e78-119">Eseguire la pubblicazione in Registro Azure Container</span><span class="sxs-lookup"><span data-stu-id="c4e78-119">Publish to Azure Container Registry</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="c4e78-120">Informazioni su come usare l'estensione degli Strumenti contenitore di Visual Studio per distribuire un'app ASP.NET Core in un host Docker in Azure con PowerShell.</span><span class="sxs-lookup"><span data-stu-id="c4e78-120">Find out how to use the Visual Studio Container Tools extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="c4e78-121">Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="c4e78-121">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="c4e78-122">Potrebbero essere necessari interventi di configurazione aggiuntivi per le app ospitate dietro a server proxy e a servizi di bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="c4e78-122">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="c4e78-123">Il passaggio delle richieste attraverso un proxy spesso oscura le informazioni sulla richiesta originale, ad esempio lo schema e l'indirizzo IP del client.</span><span class="sxs-lookup"><span data-stu-id="c4e78-123">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="c4e78-124">Potrebbe essere necessario inoltrare alcune informazioni sulla richiesta manualmente all'app.</span><span class="sxs-lookup"><span data-stu-id="c4e78-124">It might be necessary to forwarded some information about the request manually to the app.</span></span>

<span data-ttu-id="c4e78-125">[GC con contenitori Docker e Small](xref:performance/memory#sc) Viene descritta la selezione GC con piccoli contenitori.</span><span class="sxs-lookup"><span data-stu-id="c4e78-125">[GC using Docker and small containers](xref:performance/memory#sc) Discusses GC selection with small containers.</span></span>