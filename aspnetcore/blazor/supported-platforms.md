---
title: BlazorPiattaforme supportate ASP.NET Core
author: guardrex
description: Informazioni sulle piattaforme supportate per ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 99f14486711c2dd2a634bc51b27a8e3891deee1a
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243226"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="fb347-103">BlazorPiattaforme supportate ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb347-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="fb347-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fb347-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="fb347-105">Requisiti del browser</span><span class="sxs-lookup"><span data-stu-id="fb347-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="fb347-106">Webassembly</span><span class="sxs-lookup"><span data-stu-id="fb347-106"> WebAssembly</span></span>

| <span data-ttu-id="fb347-107">Browser</span><span class="sxs-lookup"><span data-stu-id="fb347-107">Browser</span></span>                          | <span data-ttu-id="fb347-108">Versione</span><span class="sxs-lookup"><span data-stu-id="fb347-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="fb347-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="fb347-109">Microsoft Edge</span></span>                   | <span data-ttu-id="fb347-110">Corrente</span><span class="sxs-lookup"><span data-stu-id="fb347-110">Current</span></span>               |
| <span data-ttu-id="fb347-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="fb347-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="fb347-112">Corrente</span><span class="sxs-lookup"><span data-stu-id="fb347-112">Current</span></span>               |
| <span data-ttu-id="fb347-113">Google Chrome, incluso Android</span><span class="sxs-lookup"><span data-stu-id="fb347-113">Google Chrome, including Android</span></span> | <span data-ttu-id="fb347-114">Corrente</span><span class="sxs-lookup"><span data-stu-id="fb347-114">Current</span></span>               |
| <span data-ttu-id="fb347-115">Safari, incluso iOS</span><span class="sxs-lookup"><span data-stu-id="fb347-115">Safari, including iOS</span></span>            | <span data-ttu-id="fb347-116">Corrente</span><span class="sxs-lookup"><span data-stu-id="fb347-116">Current</span></span>               |
| <span data-ttu-id="fb347-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="fb347-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="fb347-118">Non supportato&dagger;</span><span class="sxs-lookup"><span data-stu-id="fb347-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="fb347-119">&dagger;Microsoft Internet Explorer non supporta [webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="fb347-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="fb347-120">Server</span><span class="sxs-lookup"><span data-stu-id="fb347-120"> Server</span></span>

| <span data-ttu-id="fb347-121">Browser</span><span class="sxs-lookup"><span data-stu-id="fb347-121">Browser</span></span>                          | <span data-ttu-id="fb347-122">Versione</span><span class="sxs-lookup"><span data-stu-id="fb347-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="fb347-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="fb347-123">Microsoft Edge</span></span>                   | <span data-ttu-id="fb347-124">Corrente</span><span class="sxs-lookup"><span data-stu-id="fb347-124">Current</span></span>    |
| <span data-ttu-id="fb347-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="fb347-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="fb347-126">Corrente</span><span class="sxs-lookup"><span data-stu-id="fb347-126">Current</span></span>    |
| <span data-ttu-id="fb347-127">Google Chrome, incluso Android</span><span class="sxs-lookup"><span data-stu-id="fb347-127">Google Chrome, including Android</span></span> | <span data-ttu-id="fb347-128">Corrente</span><span class="sxs-lookup"><span data-stu-id="fb347-128">Current</span></span>    |
| <span data-ttu-id="fb347-129">Safari, incluso iOS</span><span class="sxs-lookup"><span data-stu-id="fb347-129">Safari, including iOS</span></span>            | <span data-ttu-id="fb347-130">Corrente</span><span class="sxs-lookup"><span data-stu-id="fb347-130">Current</span></span>    |
| <span data-ttu-id="fb347-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="fb347-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="fb347-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="fb347-132">11&dagger;</span></span> |

<span data-ttu-id="fb347-133">&dagger;Sono necessari altri riempimenti, ad esempio le promesse possono essere aggiunte tramite un [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span><span class="sxs-lookup"><span data-stu-id="fb347-133">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb347-134">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="fb347-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
