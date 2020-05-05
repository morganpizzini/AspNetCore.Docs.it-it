---
title: SDK Web di ASP.NET Core
author: Rick-Anderson
description: Panoramica di Microsoft. NET. Sdk. Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/web-sdk
ms.openlocfilehash: 2797f0b3003b8ad89093fe1115dee2acc8650c73
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777163"
---
# <a name="aspnet-core-web-sdk"></a>SDK Web di ASP.NET Core

### <a name="overview"></a>Panoramica

`Microsoft.NET.Sdk.Web`è un [SDK di progetto MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) per la compilazione di app ASP.NET Core. È possibile creare un'app ASP.NET Core senza questo SDK, ma il Web SDK è:

* Personalizzata per offrire un'esperienza di prima classe.
* Destinazione consigliata per la maggior parte degli utenti.

Usare Web. SDK in un progetto:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Funzionalità abilitate tramite Web SDK:

* I progetti destinati a .NET Core 3,0 o versioni successive fanno riferimento in modo implicito:

  * [Framework condiviso ASP.NET Core](xref:fundamentals/metapackage-app).
  * [Analizzatori](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) progettati per la compilazione di app ASP.NET Core.
* Web SDK Importa destinazioni MSBuild che consentono di usare i profili di pubblicazione e la pubblicazione tramite WebDeploy.

### <a name="properties"></a>Proprietà

| Proprietà | Descrizione |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Disabilita il `Microsoft.AspNetCore.App` riferimento implicito al Framework condiviso. |
| `DisableImplicitAspNetCoreAnalyzers` | Disabilita il riferimento implicito agli analizzatori ASP.NET Core. |
| `DisableImplicitComponentsAnalyzers` | Disabilita il riferimento implicito agli Razor analizzatori di componenti durante Blazor la compilazione di applicazioni (Server). |
