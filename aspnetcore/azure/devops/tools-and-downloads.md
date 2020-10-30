---
title: Strumenti e download-DevOps con ASP.NET Core e Azure
author: CamSoper
description: Strumenti e download necessari per DevOps con ASP.NET Core e Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 8c7f10a6b6f8504efaba6054533aba034982820c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056569"
---
# <a name="tools-and-downloads"></a>Strumenti e download

Azure offre diverse interfacce per il provisioning e la gestione delle risorse, ad esempio [portale di Azure](https://portal.azure.com), l'interfaccia della riga di comando di [Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure cloud Shell](https://shell.azure.com/bash)e Visual Studio. Questa guida accetta un approccio minimalista e usa il Azure Cloud Shell laddove possibile per ridurre i passaggi necessari. Tuttavia, è necessario utilizzare il portale di Azure per alcune parti.

## <a name="prerequisites"></a>Prerequisiti

Sono necessarie le sottoscrizioni seguenti:

* Azure &mdash; se non si ha un account, [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/dotnet/).
* Azure DevOps Services &mdash; la sottoscrizione e l'organizzazione di Azure DevOps vengono create nel capitolo 4.
* GitHub &mdash; se non si ha un account, [iscriversi gratuitamente](https://github.com/join).

Sono necessari gli strumenti seguenti:

* [Git](https://git-scm.com/downloads) &mdash; Per questa guida è consigliata una conoscenza di base di git. Esaminare la [documentazione di git](https://git-scm.com/doc), in particolare [git Remote](https://git-scm.com/docs/git-remote) e [git push](https://git-scm.com/docs/git-push).
* [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Per compilare ed eseguire l'app di esempio, è necessaria la versione 2.1.300 o successiva. Se Visual Studio viene installato con il carico di lavoro **sviluppo multipiattaforma .NET Core** , il .NET Core SDK è già installato.

    Verificare l'installazione del .NET Core SDK. Aprire una shell dei comandi ed eseguire il comando seguente:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Strumenti consigliati (solo Windows)

* I robusti strumenti di Azure di [Visual Studio](https://visualstudio.microsoft.com)forniscono un'interfaccia utente grafica per la maggior parte delle funzionalità descritte in questa guida. Qualsiasi edizione di Visual Studio funzionerà, inclusa la versione gratuita di Visual Studio Community Edition. Le esercitazioni vengono scritte per illustrare lo sviluppo, la distribuzione e la DevOps con e senza Visual Studio.

  Verificare che in Visual Studio siano installati i [carichi di lavoro](/visualstudio/install/modify-visual-studio) seguenti:

  * Sviluppo Web e ASP.NET
  * Sviluppo di Azure
  * Sviluppo multipiattaforma .NET Core
