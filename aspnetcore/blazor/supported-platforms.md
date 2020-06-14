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
ms.openlocfilehash: c10132c87c93346af89c548363e786967609f3da
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/12/2020
ms.locfileid: "83607979"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>BlazorPiattaforme supportate ASP.NET Core

Di [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Requisiti del browser

### <a name="blazor-webassembly"></a>BlazorWebassembly

| Browser                          | Versione               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Corrente               |
| Mozilla Firefox                  | Corrente               |
| Google Chrome, incluso Android | Corrente               |
| Safari, incluso iOS            | Corrente               |
| Microsoft Internet Explorer      | Non supportato&dagger; |

&dagger;Microsoft Internet Explorer non supporta [webassembly](https://webassembly.org).

### <a name="blazor-server"></a>BlazorServer

| Browser                          | Versione    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Corrente    |
| Mozilla Firefox                  | Corrente    |
| Google Chrome, incluso Android | Corrente    |
| Safari, incluso iOS            | Corrente    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Sono necessari altri riempimenti, ad esempio le promesse possono essere aggiunte tramite un bundle [polyfill.io](https://polyfill.io/v3/) .

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/hosting-models>
