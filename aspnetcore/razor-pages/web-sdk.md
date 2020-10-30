---
title: SDK Web di ASP.NET Core
author: Rick-Anderson
description: Panoramica di Microsoft. NET. Sdk. Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- appsettings.json
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
uid: razor-pages/web-sdk
ms.openlocfilehash: 8cc0a51d3d917300f3add31f5884b4784dd573dd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059754"
---
# <a name="aspnet-core-web-sdk"></a>SDK Web di ASP.NET Core

### <a name="overview"></a>Panoramica

`Microsoft.NET.Sdk.Web` è un [SDK di progetto MSBuild](/visualstudio/msbuild/how-to-use-project-sdk) per la compilazione di app ASP.NET Core. È possibile creare un'app ASP.NET Core senza questo SDK, ma il Web SDK è:

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
| `DisableImplicitFrameworkReferences` | Disabilita il riferimento implicito al `Microsoft.AspNetCore.App` Framework condiviso. |
| `DisableImplicitAspNetCoreAnalyzers` | Disabilita il riferimento implicito agli analizzatori ASP.NET Core. |
| `DisableImplicitComponentsAnalyzers` | Disabilita il riferimento implicito agli Razor analizzatori di componenti durante la compilazione di Blazor applicazioni (Server). |