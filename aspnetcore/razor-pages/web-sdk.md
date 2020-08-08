---
title: SDK Web di ASP.NET Core
author: Rick-Anderson
description: Panoramica di Microsoft. NET. Sdk. Web.
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: a717d9dc0e326e62ec920d2a838e8b18be3db013
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020691"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="8da46-103">SDK Web di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8da46-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="8da46-104">Panoramica</span><span class="sxs-lookup"><span data-stu-id="8da46-104">Overview</span></span>

<span data-ttu-id="8da46-105">`Microsoft.NET.Sdk.Web`è un [SDK di progetto MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) per la compilazione di app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8da46-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="8da46-106">È possibile creare un'app ASP.NET Core senza questo SDK, ma il Web SDK è:</span><span class="sxs-lookup"><span data-stu-id="8da46-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="8da46-107">Personalizzata per offrire un'esperienza di prima classe.</span><span class="sxs-lookup"><span data-stu-id="8da46-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="8da46-108">Destinazione consigliata per la maggior parte degli utenti.</span><span class="sxs-lookup"><span data-stu-id="8da46-108">The recommended target for most users.</span></span>

<span data-ttu-id="8da46-109">Usare Web. SDK in un progetto:</span><span class="sxs-lookup"><span data-stu-id="8da46-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="8da46-110">Funzionalità abilitate tramite Web SDK:</span><span class="sxs-lookup"><span data-stu-id="8da46-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="8da46-111">I progetti destinati a .NET Core 3,0 o versioni successive fanno riferimento in modo implicito:</span><span class="sxs-lookup"><span data-stu-id="8da46-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="8da46-112">[Framework condiviso ASP.NET Core](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8da46-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="8da46-113">[Analizzatori](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) progettati per la compilazione di app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8da46-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="8da46-114">Web SDK Importa destinazioni MSBuild che consentono di usare i profili di pubblicazione e la pubblicazione tramite WebDeploy.</span><span class="sxs-lookup"><span data-stu-id="8da46-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="8da46-115">Proprietà</span><span class="sxs-lookup"><span data-stu-id="8da46-115">Properties</span></span>

| <span data-ttu-id="8da46-116">Proprietà</span><span class="sxs-lookup"><span data-stu-id="8da46-116">Property</span></span> | <span data-ttu-id="8da46-117">Descrizione</span><span class="sxs-lookup"><span data-stu-id="8da46-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="8da46-118">Disabilita il riferimento implicito al `Microsoft.AspNetCore.App` Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="8da46-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="8da46-119">Disabilita il riferimento implicito agli analizzatori ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8da46-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="8da46-120">Disabilita il riferimento implicito agli Razor analizzatori di componenti durante la compilazione di Blazor applicazioni (Server).</span><span class="sxs-lookup"><span data-stu-id="8da46-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
