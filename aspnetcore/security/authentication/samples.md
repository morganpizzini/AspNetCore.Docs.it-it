---
title: Esempi di autenticazione per ASP.NET Core
author: rick-anderson
description: Fornisce collegamenti agli esempi di autenticazione nel repository ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 3e5e487adafc09d38400ea58936c5c2e8385e84f
ms.sourcegitcommit: 5a36758cca2861aeb10840093e46d273a6e6e91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87303599"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="60f11-103">Esempi di autenticazione per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="60f11-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="60f11-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="60f11-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="60f11-105">Il [repository ASP.NET Core](https://github.com/dotnet/AspNetCore) contiene gli esempi di autenticazione seguenti nella cartella *AspNetCore/SRC/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="60f11-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="60f11-106">Trasformazione delle attestazioni</span><span class="sxs-lookup"><span data-stu-id="60f11-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="60f11-107">Autenticazione cookie</span><span class="sxs-lookup"><span data-stu-id="60f11-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="60f11-108">Provider di criteri personalizzati-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="60f11-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="60f11-109">Opzioni e schemi di autenticazione dinamici</span><span class="sxs-lookup"><span data-stu-id="60f11-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="60f11-110">[Attestazioni esterne](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="60f11-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="60f11-111">Selezione tra cookie e un altro schema di autenticazione in base alla richiesta</span><span class="sxs-lookup"><span data-stu-id="60f11-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="60f11-112">Limita l'accesso ai file statici</span><span class="sxs-lookup"><span data-stu-id="60f11-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="60f11-113">Eseguire gli esempi</span><span class="sxs-lookup"><span data-stu-id="60f11-113">Run the samples</span></span>

* <span data-ttu-id="60f11-114">Selezionare un [ramo](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="60f11-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="60f11-115">Ad esempio, usare `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="60f11-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="60f11-116">Clonare o scaricare il [repository ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="60f11-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="60f11-117">Verificare di aver installato la versione di [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) corrispondente al clone del repository di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60f11-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="60f11-118">Passare a un esempio in *AspNetCore/SRC/Security/Samples* ed eseguire l'esempio con `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="60f11-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="60f11-119">Il [repository ASP.NET Core](https://github.com/dotnet/AspNetCore) contiene gli esempi di autenticazione seguenti nella cartella *AspNetCore/SRC/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="60f11-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="60f11-120">Trasformazione delle attestazioni</span><span class="sxs-lookup"><span data-stu-id="60f11-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="60f11-121">Autenticazione cookie</span><span class="sxs-lookup"><span data-stu-id="60f11-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)
* [<span data-ttu-id="60f11-122">Provider di criteri personalizzati-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="60f11-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="60f11-123">Opzioni e schemi di autenticazione dinamici</span><span class="sxs-lookup"><span data-stu-id="60f11-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="60f11-124">[Attestazioni esterne](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="60f11-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="60f11-125">Selezione tra cookie e un altro schema di autenticazione in base alla richiesta</span><span class="sxs-lookup"><span data-stu-id="60f11-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="60f11-126">Limita l'accesso ai file statici</span><span class="sxs-lookup"><span data-stu-id="60f11-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="60f11-127">Eseguire gli esempi</span><span class="sxs-lookup"><span data-stu-id="60f11-127">Run the samples</span></span>

* <span data-ttu-id="60f11-128">Selezionare un [ramo](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="60f11-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="60f11-129">Ad esempio, usare `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="60f11-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="60f11-130">Clonare o scaricare il [repository ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="60f11-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="60f11-131">Verificare di aver installato la versione di [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) corrispondente al clone del repository di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60f11-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="60f11-132">Passare a un esempio in *AspNetCore/SRC/Security/Samples* ed eseguire l'esempio con `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="60f11-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
