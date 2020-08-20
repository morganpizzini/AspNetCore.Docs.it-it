---
title: SignalRPiattaforme supportate ASP.NET Core
author: bradygaster
description: Informazioni sulle piattaforme supportate per ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/supported-platforms
ms.openlocfilehash: 91fd2553803d855b338b1d1b46d55e1d1e4cc21e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635151"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="4743d-103">SignalRPiattaforme supportate ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4743d-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="4743d-104">Requisiti di sistema del server di</span><span class="sxs-lookup"><span data-stu-id="4743d-104">Server system requirements</span></span>

<span data-ttu-id="4743d-105">SignalR per ASP.NET Core supporta qualsiasi piattaforma server supportata da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4743d-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="4743d-106">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="4743d-106">JavaScript client</span></span>

<span data-ttu-id="4743d-107">Il [client JavaScript](xref:signalr/javascript-client) viene eseguito in NodeJS 8 e versioni successive e nei browser seguenti:</span><span class="sxs-lookup"><span data-stu-id="4743d-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="4743d-108">Browser</span><span class="sxs-lookup"><span data-stu-id="4743d-108">Browser</span></span>                         | <span data-ttu-id="4743d-109">Versione</span><span class="sxs-lookup"><span data-stu-id="4743d-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="4743d-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="4743d-110">Microsoft Edge</span></span>                  | <span data-ttu-id="4743d-111">Corrente&dagger;</span><span class="sxs-lookup"><span data-stu-id="4743d-111">Current&dagger;</span></span> |
| <span data-ttu-id="4743d-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="4743d-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="4743d-113">Corrente&dagger;</span><span class="sxs-lookup"><span data-stu-id="4743d-113">Current&dagger;</span></span> |
| <span data-ttu-id="4743d-114">Google Chrome; include Android</span><span class="sxs-lookup"><span data-stu-id="4743d-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="4743d-115">Corrente&dagger;</span><span class="sxs-lookup"><span data-stu-id="4743d-115">Current&dagger;</span></span> |
| <span data-ttu-id="4743d-116">Safari include iOS</span><span class="sxs-lookup"><span data-stu-id="4743d-116">Safari; includes iOS</span></span>            | <span data-ttu-id="4743d-117">Corrente&dagger;</span><span class="sxs-lookup"><span data-stu-id="4743d-117">Current&dagger;</span></span> |
| <span data-ttu-id="4743d-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="4743d-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="4743d-119">11</span><span class="sxs-lookup"><span data-stu-id="4743d-119">11</span></span>              |

<span data-ttu-id="4743d-120">&dagger;*Current* si riferisce alla versione più recente del browser.</span><span class="sxs-lookup"><span data-stu-id="4743d-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="4743d-121">Client .NET</span><span class="sxs-lookup"><span data-stu-id="4743d-121">.NET client</span></span>

<span data-ttu-id="4743d-122">Il [client .NET](xref:signalr/dotnet-client) viene eseguito su qualsiasi piattaforma supportata da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4743d-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="4743d-123">Ad esempio, [gli sviluppatori Novell possono SignalR usare](https://github.com/aspnet/Announcements/issues/305) per compilare app Android usando Novell. Android 8.4.0.1 e versioni successive e le app iOS usando Novell. iOS 11.14.0.4 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="4743d-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="4743d-124">Se il server esegue IIS, il trasporto WebSocket richiede IIS 8,0 o versioni successive in Windows Server 2012 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="4743d-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="4743d-125">Gli altri trasporti sono supportati in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="4743d-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="4743d-126">Client Java</span><span class="sxs-lookup"><span data-stu-id="4743d-126">Java client</span></span>

<span data-ttu-id="4743d-127">Il [client Java](xref:signalr/java-client) supporta Java 8 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="4743d-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="4743d-128">Client non supportati</span><span class="sxs-lookup"><span data-stu-id="4743d-128">Unsupported clients</span></span>

<span data-ttu-id="4743d-129">I client seguenti sono disponibili, ma sono sperimentali o non ufficiali.</span><span class="sxs-lookup"><span data-stu-id="4743d-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="4743d-130">Non sono attualmente supportati e potrebbero non essere mai.</span><span class="sxs-lookup"><span data-stu-id="4743d-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="4743d-131">[Client C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="4743d-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="4743d-132">[Client Swift](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="4743d-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
