---
title: Esempi di autenticazione per ASP.NET Core
author: rick-anderson
description: Fornisce collegamenti agli esempi di autenticazione nel repository ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
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
uid: security/authentication/samples
ms.openlocfilehash: 71e614eb3d91e8425ec430d775b8a0163dc7258f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017792"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="d0e24-103">Esempi di autenticazione per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0e24-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="d0e24-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d0e24-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d0e24-105">Il [repository ASP.NET Core](https://github.com/dotnet/AspNetCore) contiene gli esempi di autenticazione seguenti nella cartella *AspNetCore/SRC/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="d0e24-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="d0e24-106">Trasformazione delle attestazioni</span><span class="sxs-lookup"><span data-stu-id="d0e24-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="d0e24-107">[Cookieautenticazione](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="d0e24-107">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="d0e24-108">Provider di criteri personalizzati-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="d0e24-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="d0e24-109">Opzioni e schemi di autenticazione dinamici</span><span class="sxs-lookup"><span data-stu-id="d0e24-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="d0e24-110">[Attestazioni esterne](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="d0e24-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="d0e24-111">Selezione tra cookie e un altro schema di autenticazione in base alla richiesta</span><span class="sxs-lookup"><span data-stu-id="d0e24-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="d0e24-112">Limita l'accesso ai file statici</span><span class="sxs-lookup"><span data-stu-id="d0e24-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="d0e24-113">Eseguire gli esempi</span><span class="sxs-lookup"><span data-stu-id="d0e24-113">Run the samples</span></span>

* <span data-ttu-id="d0e24-114">Selezionare un [ramo](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d0e24-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="d0e24-115">Ad esempio: `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="d0e24-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="d0e24-116">Clonare o scaricare il [repository ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d0e24-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="d0e24-117">Verificare di aver installato la versione di [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) corrispondente al clone del repository di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d0e24-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="d0e24-118">Passare a un esempio in *AspNetCore/SRC/Security/Samples* ed eseguire l'esempio con `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="d0e24-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d0e24-119">Il [repository ASP.NET Core](https://github.com/dotnet/AspNetCore) contiene gli esempi di autenticazione seguenti nella cartella *AspNetCore/SRC/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="d0e24-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="d0e24-120">Trasformazione delle attestazioni</span><span class="sxs-lookup"><span data-stu-id="d0e24-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="d0e24-121">[Cookieautenticazione](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="d0e24-121">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="d0e24-122">Provider di criteri personalizzati-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="d0e24-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="d0e24-123">Opzioni e schemi di autenticazione dinamici</span><span class="sxs-lookup"><span data-stu-id="d0e24-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="d0e24-124">[Attestazioni esterne](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="d0e24-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="d0e24-125">Selezione tra cookie e un altro schema di autenticazione in base alla richiesta</span><span class="sxs-lookup"><span data-stu-id="d0e24-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="d0e24-126">Limita l'accesso ai file statici</span><span class="sxs-lookup"><span data-stu-id="d0e24-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="d0e24-127">Eseguire gli esempi</span><span class="sxs-lookup"><span data-stu-id="d0e24-127">Run the samples</span></span>

* <span data-ttu-id="d0e24-128">Selezionare un [ramo](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d0e24-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="d0e24-129">Ad esempio: `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="d0e24-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="d0e24-130">Clonare o scaricare il [repository ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d0e24-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="d0e24-131">Verificare di aver installato la versione di [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) corrispondente al clone del repository di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d0e24-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="d0e24-132">Passare a un esempio in *AspNetCore/SRC/Security/Samples* ed eseguire l'esempio con `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="d0e24-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
