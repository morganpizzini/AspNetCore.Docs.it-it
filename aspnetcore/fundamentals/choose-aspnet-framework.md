---
title: Scegliere tra ASP.NET 4.x e ASP.NET Core
author: rick-anderson
description: Viene illustrato ASP.NET Core vs. ASP.NET 4. x e come scegliere tra di essi.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
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
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 95ac4784634d38add5e28644d42b0182e15c6de9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060027"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>Scegliere tra ASP.NET 4.x e ASP.NET Core

ASP.NET Core è una riprogettazione di ASP.NET 4.x. Questo articolo elenca le differenze tra i due.

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core è un framework open source, multipiattaforma per la compilazione di moderne app Web basate sul cloud in Windows, Mac OS o Linux.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4.x è un framework consolidato che offre i servizi necessari per la compilazione di app Web di livello aziendale basate su server in Windows.

## <a name="framework-selection"></a>Selezione del framework

La tabella seguente mette a confronto ASP.NET Core e ASP.NET 4.x.

| ASP.NET Core | ASP.NET 4.x |
|---|---|
|Compilare per Windows, Mac OS o Linux|Compilare per Windows|
|[ Razor Pages](xref:razor-pages/index) è l'approccio consigliato per creare un'interfaccia utente Web a partire da ASP.NET Core 2. x. Vedere anche [MVC](xref:mvc/overview), [API Web](xref:tutorials/first-web-api)e [SignalR](xref:signalr/introduction) .|Usare [Web Form](/aspnet/web-forms), [SignalR](/aspnet/signalr) , [MVC](/aspnet/mvc), [API Web](/aspnet/web-api/), [webhook](/aspnet/webhooks/)o [pagine Web](/aspnet/web-pages)|
|Più versioni per computer|Una versione per computer|
|Sviluppare con [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) o [Visual Studio Code](https://code.visualstudio.com/) tramite C# o F#|Sviluppare con [Visual Studio](https://visualstudio.microsoft.com/vs/) con C#, VB o F #|
|Prestazioni più elevate rispetto ad ASP.NET 4.x|Buone prestazioni|
|[Usare il runtime di .NET Core](/dotnet/standard/choosing-core-framework-server)|Usare runtime .NET Framework|

Vedere [ASP.NET Core per .NET Framework](xref:index#target-framework) per informazioni sul supporto per ASP.NET Core 2.x in .NET Framework.

## <a name="aspnet-core-scenarios"></a>Scenari ASP.NET Core

* [Siti Web](xref:tutorials/first-mvc-app/index)
* [API](xref:tutorials/first-web-api)
* [Tempo reale](xref:signalr/introduction)
* [Distribuire un'app ASP.NET Core in Azure](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>Scenari ASP.NET 4.x

* [Siti Web](/aspnet/mvc)
* [API](/aspnet/web-api)
* [Tempo reale](/aspnet/signalr)
* [Creare un'app Web ASP.NET 4.x in Azure](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Introduzione ad ASP.NET](/aspnet/overview)
* [Introduzione ad ASP.NET Core](xref:index)
* <xref:host-and-deploy/azure-apps/index>
