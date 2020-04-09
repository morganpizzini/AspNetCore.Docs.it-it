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
# <a name="tools-and-downloads"></a><span data-ttu-id="f076f-103">Strumenti e download</span><span class="sxs-lookup"><span data-stu-id="f076f-103">Tools and downloads</span></span>

<span data-ttu-id="f076f-104">Azure dispone di diverse interfacce per il provisioning e la gestione delle risorse, ad esempio il [portale](https://portal.azure.com)di Azure , [l'interfaccia della riga di comando di Azure](/cli/azure/), Azure [PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)e Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f076f-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="f076f-105">Questa guida adotta un approccio minimalista e usa Azure Cloud Shell quando possibile per ridurre i passaggi necessari.</span><span class="sxs-lookup"><span data-stu-id="f076f-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="f076f-106">Tuttavia, il portale di Azure deve essere usato per alcune parti.</span><span class="sxs-lookup"><span data-stu-id="f076f-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f076f-107">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="f076f-107">Prerequisites</span></span>

<span data-ttu-id="f076f-108">Sono necessarie le seguenti sottoscrizioni:</span><span class="sxs-lookup"><span data-stu-id="f076f-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="f076f-109">Azure &mdash; Se non si dispone di un account, [ottenere una versione di valutazione gratuita.](https://azure.microsoft.com/free/)</span><span class="sxs-lookup"><span data-stu-id="f076f-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="f076f-110">Servizi DevOps &mdash; di Azure, la sottoscrizione devOps di Azure e l'organizzazione viene creata nel capitolo 4.Azure DevOps Services your Azure DevOps subscription and organization is created in Chapter 4.</span><span class="sxs-lookup"><span data-stu-id="f076f-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="f076f-111">GitHub &mdash; Se non hai un account, [iscriviti gratuitamente.](https://github.com/join)</span><span class="sxs-lookup"><span data-stu-id="f076f-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="f076f-112">Sono necessari i seguenti strumenti:</span><span class="sxs-lookup"><span data-stu-id="f076f-112">The following tools are required:</span></span>

* <span data-ttu-id="f076f-113">[Git](https://git-scm.com/downloads) &mdash; Per questa guida è consigliata una comprensione fondamentale di Git.</span><span class="sxs-lookup"><span data-stu-id="f076f-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="f076f-114">Esaminare la [documentazione Git](https://git-scm.com/doc), in particolare [git remote](https://git-scm.com/docs/git-remote) e [git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="f076f-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="f076f-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; versione 2.1.300 o successiva è necessario per compilare ed eseguire l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="f076f-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="f076f-116">Se Visual Studio viene installato con il carico di lavoro di **sviluppo multipiattaforma .NET Core,** .NET Core SDK è già installato.</span><span class="sxs-lookup"><span data-stu-id="f076f-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="f076f-117">Verificare l'installazione di .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="f076f-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="f076f-118">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="f076f-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="f076f-119">Strumenti consigliati (solo Windows)</span><span class="sxs-lookup"><span data-stu-id="f076f-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="f076f-120">I robusti strumenti di Azure di [Visual Studio](https://visualstudio.microsoft.com)forniscono una GUI per la maggior parte delle funzionalità descritte in questa guida.</span><span class="sxs-lookup"><span data-stu-id="f076f-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="f076f-121">Qualsiasi edizione di Visual Studio funzionerà, inclusa la versione gratuita di Visual Studio Community Edition.</span><span class="sxs-lookup"><span data-stu-id="f076f-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="f076f-122">Le esercitazioni vengono scritte per illustrare lo sviluppo, la distribuzione e DevOps con e senza Visual Studio.The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f076f-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="f076f-123">Verificare che in Visual Studio siano installati i carichi di lavoro seguenti:Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span><span class="sxs-lookup"><span data-stu-id="f076f-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="f076f-124">Sviluppo Web e ASP.NET</span><span class="sxs-lookup"><span data-stu-id="f076f-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="f076f-125">Sviluppo di Azure</span><span class="sxs-lookup"><span data-stu-id="f076f-125">Azure development</span></span>
  * <span data-ttu-id="f076f-126">Sviluppo multipiattaforma .NET Core</span><span class="sxs-lookup"><span data-stu-id="f076f-126">.NET Core cross-platform development</span></span>
