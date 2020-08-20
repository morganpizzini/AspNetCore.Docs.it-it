---
title: SignalRPiattaforme supportate ASP.NET Core
author: bradygaster
description: Informazioni sulle piattaforme supportate per ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
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
uid: signalr/supported-platforms
ms.openlocfilehash: 91fd2553803d855b338b1d1b46d55e1d1e4cc21e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635151"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>SignalRPiattaforme supportate ASP.NET Core

## <a name="server-system-requirements"></a>Requisiti di sistema del server di

SignalR per ASP.NET Core supporta qualsiasi piattaforma server supportata da ASP.NET Core.

## <a name="javascript-client"></a>Client JavaScript

Il [client JavaScript](xref:signalr/javascript-client) viene eseguito in NodeJS 8 e versioni successive e nei browser seguenti:

| Browser                         | Versione         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | Corrente&dagger; |
| Mozilla Firefox                 | Corrente&dagger; |
| Google Chrome; include Android | Corrente&dagger; |
| Safari include iOS            | Corrente&dagger; |
| Microsoft Internet Explorer     | 11              |

&dagger;*Current* si riferisce alla versione più recente del browser.

## <a name="net-client"></a>Client .NET

Il [client .NET](xref:signalr/dotnet-client) viene eseguito su qualsiasi piattaforma supportata da ASP.NET Core. Ad esempio, [gli sviluppatori Novell possono SignalR usare](https://github.com/aspnet/Announcements/issues/305) per compilare app Android usando Novell. Android 8.4.0.1 e versioni successive e le app iOS usando Novell. iOS 11.14.0.4 e versioni successive.

Se il server esegue IIS, il trasporto WebSocket richiede IIS 8,0 o versioni successive in Windows Server 2012 o versioni successive. Gli altri trasporti sono supportati in tutte le piattaforme.

## <a name="java-client"></a>Client Java

Il [client Java](xref:signalr/java-client) supporta Java 8 e versioni successive.

## <a name="unsupported-clients"></a>Client non supportati

I client seguenti sono disponibili, ma sono sperimentali o non ufficiali. Non sono attualmente supportati e potrebbero non essere mai.

* [Client C++](https://github.com/aspnet/SignalR-Client-Cpp)

* [Client Swift](https://github.com/moozzyk/SignalR-Client-Swift)
