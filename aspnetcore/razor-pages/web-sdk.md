---
title: SDK di ASP.NET Core
author: Rick-Anderson
description: Panoramica di Microsoft.NET.Sdk.Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661056"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="7eced-103">SDK di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7eced-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="7eced-104">Panoramica</span><span class="sxs-lookup"><span data-stu-id="7eced-104">Overview</span></span>

<span data-ttu-id="7eced-105">`Microsoft.NET.Sdk.Web`è un SDK di [progetto MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) per la compilazione di app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7eced-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="7eced-106">È possibile compilare un'app ASP.NET Core senza questo SDK, tuttavia, il Web SDK è:</span><span class="sxs-lookup"><span data-stu-id="7eced-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="7eced-107">Su misura per fornire un'esperienza di prima classe.</span><span class="sxs-lookup"><span data-stu-id="7eced-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="7eced-108">La destinazione consigliata per la maggior parte degli utenti.</span><span class="sxs-lookup"><span data-stu-id="7eced-108">The recommended target for most users.</span></span>

<span data-ttu-id="7eced-109">Utilizzare il file Web.SDK in un progetto:</span><span class="sxs-lookup"><span data-stu-id="7eced-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="7eced-110">Funzionalità abilitate tramite Web SDK:</span><span class="sxs-lookup"><span data-stu-id="7eced-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="7eced-111">I progetti destinati a .NET Core 3.0 o versioni successive fanno riferimento in modo implicito ai progetti:Projects targeting .NET Core 3.0 or later implicitly reference:</span><span class="sxs-lookup"><span data-stu-id="7eced-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="7eced-112">Framework [condiviso ASP.NET Core](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7eced-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="7eced-113">[Analizzatori](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) progettati per la creazione di app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7eced-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="7eced-114">Web SDK importa le destinazioni MSBuild che consentono l'utilizzo di profili di pubblicazione e pubblicazione tramite WebDeploy.</span><span class="sxs-lookup"><span data-stu-id="7eced-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="7eced-115">Proprietà</span><span class="sxs-lookup"><span data-stu-id="7eced-115">Properties</span></span>

| <span data-ttu-id="7eced-116">Proprietà</span><span class="sxs-lookup"><span data-stu-id="7eced-116">Property</span></span> | <span data-ttu-id="7eced-117">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7eced-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="7eced-118">Disabilita il riferimento `Microsoft.AspNetCore.App` implicito al framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="7eced-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="7eced-119">Disabilita il riferimento implicito agli analizzatori di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7eced-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="7eced-120">Disabilita il riferimento implicito agli analizzatori di Razor Components durante la creazione Blazor di applicazioni (server).</span><span class="sxs-lookup"><span data-stu-id="7eced-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
