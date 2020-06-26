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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401935"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="f901f-103">BlazorPiattaforme supportate ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f901f-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="f901f-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f901f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="f901f-105">Requisiti del browser</span><span class="sxs-lookup"><span data-stu-id="f901f-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="f901f-106">Browser</span><span class="sxs-lookup"><span data-stu-id="f901f-106">Browser</span></span>                          | <span data-ttu-id="f901f-107">Versione</span><span class="sxs-lookup"><span data-stu-id="f901f-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="f901f-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="f901f-108">Microsoft Edge</span></span>                   | <span data-ttu-id="f901f-109">Corrente</span><span class="sxs-lookup"><span data-stu-id="f901f-109">Current</span></span>               |
| <span data-ttu-id="f901f-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="f901f-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="f901f-111">Corrente</span><span class="sxs-lookup"><span data-stu-id="f901f-111">Current</span></span>               |
| <span data-ttu-id="f901f-112">Google Chrome, incluso Android</span><span class="sxs-lookup"><span data-stu-id="f901f-112">Google Chrome, including Android</span></span> | <span data-ttu-id="f901f-113">Corrente</span><span class="sxs-lookup"><span data-stu-id="f901f-113">Current</span></span>               |
| <span data-ttu-id="f901f-114">Safari, incluso iOS</span><span class="sxs-lookup"><span data-stu-id="f901f-114">Safari, including iOS</span></span>            | <span data-ttu-id="f901f-115">Corrente</span><span class="sxs-lookup"><span data-stu-id="f901f-115">Current</span></span>               |
| <span data-ttu-id="f901f-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="f901f-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="f901f-117">Non supportato&dagger;</span><span class="sxs-lookup"><span data-stu-id="f901f-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="f901f-118">&dagger;Microsoft Internet Explorer non supporta [webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="f901f-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="f901f-119">Browser</span><span class="sxs-lookup"><span data-stu-id="f901f-119">Browser</span></span>                          | <span data-ttu-id="f901f-120">Versione</span><span class="sxs-lookup"><span data-stu-id="f901f-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="f901f-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="f901f-121">Microsoft Edge</span></span>                   | <span data-ttu-id="f901f-122">Corrente</span><span class="sxs-lookup"><span data-stu-id="f901f-122">Current</span></span>    |
| <span data-ttu-id="f901f-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="f901f-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="f901f-124">Corrente</span><span class="sxs-lookup"><span data-stu-id="f901f-124">Current</span></span>    |
| <span data-ttu-id="f901f-125">Google Chrome, incluso Android</span><span class="sxs-lookup"><span data-stu-id="f901f-125">Google Chrome, including Android</span></span> | <span data-ttu-id="f901f-126">Corrente</span><span class="sxs-lookup"><span data-stu-id="f901f-126">Current</span></span>    |
| <span data-ttu-id="f901f-127">Safari, incluso iOS</span><span class="sxs-lookup"><span data-stu-id="f901f-127">Safari, including iOS</span></span>            | <span data-ttu-id="f901f-128">Corrente</span><span class="sxs-lookup"><span data-stu-id="f901f-128">Current</span></span>    |
| <span data-ttu-id="f901f-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="f901f-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="f901f-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="f901f-130">11&dagger;</span></span> |

<span data-ttu-id="f901f-131">&dagger;Sono necessari altri riempimenti, ad esempio le promesse possono essere aggiunte tramite un [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span><span class="sxs-lookup"><span data-stu-id="f901f-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f901f-132">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f901f-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
