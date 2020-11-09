---
title: SignalRPiattaforme supportate ASP.NET Core
author: bradygaster
description: 'Informazioni sulle piattaforme supportate per ASP.NET Core SignalR .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/supported-platforms
ms.openlocfilehash: ee6e263fb5bef7bfb84587c3b0f04175eb8073cd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051018"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="7a2b7-103">SignalRPiattaforme supportate ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a2b7-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="7a2b7-104">Requisiti di sistema del server di</span><span class="sxs-lookup"><span data-stu-id="7a2b7-104">Server system requirements</span></span>

<span data-ttu-id="7a2b7-105">SignalR per ASP.NET Core supporta qualsiasi piattaforma server supportata da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a2b7-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="7a2b7-106">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="7a2b7-106">JavaScript client</span></span>

<span data-ttu-id="7a2b7-107">Il [client JavaScript](xref:signalr/javascript-client) viene eseguito in NodeJS 8 e versioni successive e nei browser seguenti:</span><span class="sxs-lookup"><span data-stu-id="7a2b7-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="7a2b7-108">Browser</span><span class="sxs-lookup"><span data-stu-id="7a2b7-108">Browser</span></span>                          | <span data-ttu-id="7a2b7-109">Versione</span><span class="sxs-lookup"><span data-stu-id="7a2b7-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="7a2b7-110">Apple Safari, incluso iOS</span><span class="sxs-lookup"><span data-stu-id="7a2b7-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="7a2b7-111">Corrente&dagger;</span><span class="sxs-lookup"><span data-stu-id="7a2b7-111">Current&dagger;</span></span> |
| <span data-ttu-id="7a2b7-112">Google Chrome, incluso Android</span><span class="sxs-lookup"><span data-stu-id="7a2b7-112">Google Chrome, including Android</span></span> | <span data-ttu-id="7a2b7-113">Corrente&dagger;</span><span class="sxs-lookup"><span data-stu-id="7a2b7-113">Current&dagger;</span></span> |
| <span data-ttu-id="7a2b7-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="7a2b7-114">Microsoft Edge</span></span>                   | <span data-ttu-id="7a2b7-115">Corrente&dagger;</span><span class="sxs-lookup"><span data-stu-id="7a2b7-115">Current&dagger;</span></span> |
| <span data-ttu-id="7a2b7-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="7a2b7-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="7a2b7-117">Corrente&dagger;</span><span class="sxs-lookup"><span data-stu-id="7a2b7-117">Current&dagger;</span></span> |

<span data-ttu-id="7a2b7-118">&dagger;*Current* si riferisce alla versione più recente del browser.</span><span class="sxs-lookup"><span data-stu-id="7a2b7-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="7a2b7-119">Client .NET</span><span class="sxs-lookup"><span data-stu-id="7a2b7-119">.NET client</span></span>

<span data-ttu-id="7a2b7-120">Il [client .NET](xref:signalr/dotnet-client) viene eseguito su qualsiasi piattaforma supportata da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a2b7-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="7a2b7-121">Ad esempio, [gli sviluppatori Novell possono SignalR usare](https://github.com/aspnet/Announcements/issues/305) per compilare app Android usando Novell. Android 8.4.0.1 e versioni successive e le app iOS usando Novell. iOS 11.14.0.4 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="7a2b7-121">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="7a2b7-122">Se il server esegue IIS, il trasporto WebSocket richiede IIS 8,0 o versioni successive in Windows Server 2012 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="7a2b7-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="7a2b7-123">Gli altri trasporti sono supportati in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="7a2b7-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="7a2b7-124">Client Java</span><span class="sxs-lookup"><span data-stu-id="7a2b7-124">Java client</span></span>

<span data-ttu-id="7a2b7-125">Il [client Java](xref:signalr/java-client) supporta Java 8 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="7a2b7-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="7a2b7-126">Client non supportati</span><span class="sxs-lookup"><span data-stu-id="7a2b7-126">Unsupported clients</span></span>

<span data-ttu-id="7a2b7-127">I client seguenti sono disponibili, ma sono sperimentali o non ufficiali.</span><span class="sxs-lookup"><span data-stu-id="7a2b7-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="7a2b7-128">Non sono attualmente supportati e potrebbero non essere mai.</span><span class="sxs-lookup"><span data-stu-id="7a2b7-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="7a2b7-129">[Client C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="7a2b7-129">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="7a2b7-130">[Client Swift](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="7a2b7-130">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
