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
# <a name="aspnet-core-web-sdk"></a>SDK di ASP.NET Core

### <a name="overview"></a>Panoramica

`Microsoft.NET.Sdk.Web`è un SDK di [progetto MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) per la compilazione di app ASP.NET Core. È possibile compilare un'app ASP.NET Core senza questo SDK, tuttavia, il Web SDK è:

* Su misura per fornire un'esperienza di prima classe.
* La destinazione consigliata per la maggior parte degli utenti.

Utilizzare il file Web.SDK in un progetto:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Funzionalità abilitate tramite Web SDK:

* I progetti destinati a .NET Core 3.0 o versioni successive fanno riferimento in modo implicito ai progetti:Projects targeting .NET Core 3.0 or later implicitly reference:

  * Framework [condiviso ASP.NET Core](xref:fundamentals/metapackage-app).
  * [Analizzatori](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) progettati per la creazione di app ASP.NET Core.
* Web SDK importa le destinazioni MSBuild che consentono l'utilizzo di profili di pubblicazione e pubblicazione tramite WebDeploy.

### <a name="properties"></a>Proprietà

| Proprietà | Descrizione |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Disabilita il riferimento `Microsoft.AspNetCore.App` implicito al framework condiviso. |
| `DisableImplicitAspNetCoreAnalyzers` | Disabilita il riferimento implicito agli analizzatori di ASP.NET Core. |
| `DisableImplicitComponentsAnalyzers` | Disabilita il riferimento implicito agli analizzatori di Razor Components durante la creazione Blazor di applicazioni (server). |
