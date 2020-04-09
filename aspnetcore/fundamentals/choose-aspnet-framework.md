---
title: Scegliere tra ASP.NET 4.x e ASP.NET Core
author: rick-anderson
description: Illustra ASP.NET Core rispetto ASP.NET 4.x e come scegliere tra di essi.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: a7280b59578ee1d96edeeccf9c9df0b0e4eb4eb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665242"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="99799-103">Scegliere tra ASP.NET 4.x e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99799-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="99799-104">ASP.NET Core è una riprogettazione di ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="99799-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="99799-105">Questo articolo elenca le differenze tra i due.</span><span class="sxs-lookup"><span data-stu-id="99799-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="99799-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99799-106">ASP.NET Core</span></span>

<span data-ttu-id="99799-107">ASP.NET Core è un framework open source, multipiattaforma per la compilazione di moderne app Web basate sul cloud in Windows, Mac OS o Linux.</span><span class="sxs-lookup"><span data-stu-id="99799-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="99799-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="99799-108">ASP.NET 4.x</span></span>

<span data-ttu-id="99799-109">ASP.NET 4.x è un framework consolidato che offre i servizi necessari per la compilazione di app Web di livello aziendale basate su server in Windows.</span><span class="sxs-lookup"><span data-stu-id="99799-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="99799-110">Selezione del framework</span><span class="sxs-lookup"><span data-stu-id="99799-110">Framework selection</span></span>

<span data-ttu-id="99799-111">La tabella seguente mette a confronto ASP.NET Core e ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="99799-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="99799-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99799-112">ASP.NET Core</span></span> | <span data-ttu-id="99799-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="99799-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="99799-114">Compilare per Windows, Mac OS o Linux</span><span class="sxs-lookup"><span data-stu-id="99799-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="99799-115">Compilare per Windows</span><span class="sxs-lookup"><span data-stu-id="99799-115">Build for Windows</span></span>|
|<span data-ttu-id="99799-116">[Razor Pages](xref:razor-pages/index) è l'approccio consigliato per la creazione di un'interfaccia utente Web da ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="99799-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="99799-117">Vedere anche [MVC](xref:mvc/overview), [SignalR](xref:signalr/introduction)API [Web](xref:tutorials/first-web-api)e .</span><span class="sxs-lookup"><span data-stu-id="99799-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="99799-118">Utilizzare [Web](/aspnet/web-forms) [SignalR](/aspnet/signalr)Form , , [, MVC](/aspnet/mvc), [API Web](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/)o [pagine Web](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="99799-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="99799-119">Più versioni per computer</span><span class="sxs-lookup"><span data-stu-id="99799-119">Multiple versions per machine</span></span>|<span data-ttu-id="99799-120">Una versione per computer</span><span class="sxs-lookup"><span data-stu-id="99799-120">One version per machine</span></span>|
|<span data-ttu-id="99799-121">Sviluppare con [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) o [Visual Studio Code](https://code.visualstudio.com/) tramite C# o F#</span><span class="sxs-lookup"><span data-stu-id="99799-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="99799-122">Sviluppare con [Visual Studio](https://visualstudio.microsoft.com/vs/) usando C , VB o F #</span><span class="sxs-lookup"><span data-stu-id="99799-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="99799-123">Prestazioni più elevate rispetto ad ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="99799-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="99799-124">Buone prestazioni</span><span class="sxs-lookup"><span data-stu-id="99799-124">Good performance</span></span>|
|[<span data-ttu-id="99799-125">Usare il runtime di .NET Core</span><span class="sxs-lookup"><span data-stu-id="99799-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="99799-126">Usare runtime .NET Framework</span><span class="sxs-lookup"><span data-stu-id="99799-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="99799-127">Vedere [ASP.NET Core per .NET Framework](xref:index#target-framework) per informazioni sul supporto per ASP.NET Core 2.x in .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="99799-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="99799-128">Scenari ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99799-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="99799-129">Siti Web</span><span class="sxs-lookup"><span data-stu-id="99799-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="99799-130">API</span><span class="sxs-lookup"><span data-stu-id="99799-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="99799-131">Tempo reale</span><span class="sxs-lookup"><span data-stu-id="99799-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="99799-132">Distribuire un'app ASP.NET Core in Azure</span><span class="sxs-lookup"><span data-stu-id="99799-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="99799-133">Scenari ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="99799-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="99799-134">Siti Web</span><span class="sxs-lookup"><span data-stu-id="99799-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="99799-135">API</span><span class="sxs-lookup"><span data-stu-id="99799-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="99799-136">Tempo reale</span><span class="sxs-lookup"><span data-stu-id="99799-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="99799-137">Creare un'app Web ASP.NET 4.x in Azure</span><span class="sxs-lookup"><span data-stu-id="99799-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="99799-138">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="99799-138">Additional resources</span></span>

* [<span data-ttu-id="99799-139">Introduzione ad ASP.NET</span><span class="sxs-lookup"><span data-stu-id="99799-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="99799-140">Introduzione ad ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99799-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
