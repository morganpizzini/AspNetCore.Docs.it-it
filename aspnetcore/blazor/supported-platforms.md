---
title: BlazorPiattaforme supportate ASP.NET Core
author: guardrex
description: Informazioni sulle piattaforme supportate per ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013528"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="6468a-103">BlazorPiattaforme supportate ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6468a-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="6468a-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6468a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="6468a-105">Requisiti del browser</span><span class="sxs-lookup"><span data-stu-id="6468a-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="6468a-106">Browser</span><span class="sxs-lookup"><span data-stu-id="6468a-106">Browser</span></span>                          | <span data-ttu-id="6468a-107">Versione</span><span class="sxs-lookup"><span data-stu-id="6468a-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="6468a-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="6468a-108">Microsoft Edge</span></span>                   | <span data-ttu-id="6468a-109">Corrente</span><span class="sxs-lookup"><span data-stu-id="6468a-109">Current</span></span>               |
| <span data-ttu-id="6468a-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="6468a-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="6468a-111">Corrente</span><span class="sxs-lookup"><span data-stu-id="6468a-111">Current</span></span>               |
| <span data-ttu-id="6468a-112">Google Chrome, incluso Android</span><span class="sxs-lookup"><span data-stu-id="6468a-112">Google Chrome, including Android</span></span> | <span data-ttu-id="6468a-113">Corrente</span><span class="sxs-lookup"><span data-stu-id="6468a-113">Current</span></span>               |
| <span data-ttu-id="6468a-114">Safari, incluso iOS</span><span class="sxs-lookup"><span data-stu-id="6468a-114">Safari, including iOS</span></span>            | <span data-ttu-id="6468a-115">Corrente</span><span class="sxs-lookup"><span data-stu-id="6468a-115">Current</span></span>               |
| <span data-ttu-id="6468a-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="6468a-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="6468a-117">Non supportato&dagger;</span><span class="sxs-lookup"><span data-stu-id="6468a-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="6468a-118">&dagger;Microsoft Internet Explorer non supporta [webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="6468a-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="6468a-119">Browser</span><span class="sxs-lookup"><span data-stu-id="6468a-119">Browser</span></span>                          | <span data-ttu-id="6468a-120">Versione</span><span class="sxs-lookup"><span data-stu-id="6468a-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="6468a-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="6468a-121">Microsoft Edge</span></span>                   | <span data-ttu-id="6468a-122">Corrente</span><span class="sxs-lookup"><span data-stu-id="6468a-122">Current</span></span>    |
| <span data-ttu-id="6468a-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="6468a-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="6468a-124">Corrente</span><span class="sxs-lookup"><span data-stu-id="6468a-124">Current</span></span>    |
| <span data-ttu-id="6468a-125">Google Chrome, incluso Android</span><span class="sxs-lookup"><span data-stu-id="6468a-125">Google Chrome, including Android</span></span> | <span data-ttu-id="6468a-126">Corrente</span><span class="sxs-lookup"><span data-stu-id="6468a-126">Current</span></span>    |
| <span data-ttu-id="6468a-127">Safari, incluso iOS</span><span class="sxs-lookup"><span data-stu-id="6468a-127">Safari, including iOS</span></span>            | <span data-ttu-id="6468a-128">Corrente</span><span class="sxs-lookup"><span data-stu-id="6468a-128">Current</span></span>    |
| <span data-ttu-id="6468a-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="6468a-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="6468a-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="6468a-130">11&dagger;</span></span> |

<span data-ttu-id="6468a-131">&dagger;Sono necessari altri riempimenti, ad esempio le promesse possono essere aggiunte tramite un [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span><span class="sxs-lookup"><span data-stu-id="6468a-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6468a-132">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6468a-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
