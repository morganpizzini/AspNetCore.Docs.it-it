---
title: Strumenti e download - DevOps con ASP.NET Core e Azure
author: CamSoper
description: Strumenti e download necessari per DevOps con ASP.NET Core e Azure.Tools and downloads required for DevOps with ASP.NET Core and Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511145"
---
# <a name="tools-and-downloads"></a>Strumenti e download

Azure dispone di diverse interfacce per il provisioning e la gestione delle risorse, ad esempio il [portale](https://portal.azure.com)di Azure , [l'interfaccia della riga di comando di Azure](/cli/azure/), Azure [PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)e Visual Studio. Questa guida adotta un approccio minimalista e usa Azure Cloud Shell quando possibile per ridurre i passaggi necessari. Tuttavia, il portale di Azure deve essere usato per alcune parti.

## <a name="prerequisites"></a>Prerequisiti

Sono necessarie le seguenti sottoscrizioni:

* Azure &mdash; Se non si dispone di un account, [ottenere una versione di valutazione gratuita.](https://azure.microsoft.com/free/)
* Servizi DevOps &mdash; di Azure, la sottoscrizione devOps di Azure e l'organizzazione viene creata nel capitolo 4.Azure DevOps Services your Azure DevOps subscription and organization is created in Chapter 4.
* GitHub &mdash; Se non hai un account, [iscriviti gratuitamente.](https://github.com/join)

Sono necessari i seguenti strumenti:

* [Git](https://git-scm.com/downloads) &mdash; Per questa guida è consigliata una comprensione fondamentale di Git. Esaminare la [documentazione Git](https://git-scm.com/doc), in particolare [git remote](https://git-scm.com/docs/git-remote) e [git push](https://git-scm.com/docs/git-push).
* [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; versione 2.1.300 o successiva è necessario per compilare ed eseguire l'app di esempio. Se Visual Studio viene installato con il carico di lavoro di **sviluppo multipiattaforma .NET Core,** .NET Core SDK è già installato.

    Verificare l'installazione di .NET Core SDK. Aprire una shell dei comandi ed eseguire il comando seguente:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Strumenti consigliati (solo Windows)

* I robusti strumenti di Azure di [Visual Studio](https://visualstudio.microsoft.com)forniscono una GUI per la maggior parte delle funzionalità descritte in questa guida. Qualsiasi edizione di Visual Studio funzionerà, inclusa la versione gratuita di Visual Studio Community Edition. Le esercitazioni vengono scritte per illustrare lo sviluppo, la distribuzione e DevOps con e senza Visual Studio.The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.

  Verificare che in Visual Studio siano installati i carichi di lavoro seguenti:Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:

  * Sviluppo Web e ASP.NET
  * Sviluppo di Azure
  * Sviluppo multipiattaforma .NET Core
