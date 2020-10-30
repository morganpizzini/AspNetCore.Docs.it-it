---
title: File offline dell'app (app_offline.htm)
author: rick-anderson
description: Informazioni sul funzionamento del file offline dell'app ( `app_offline.htm` ) con il modulo ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 4d71b95680a9b160ebb25116e35096495a2eaf93
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058649"
---
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="b0479-103">File offline dell'app ( `app_offline.htm` )</span><span class="sxs-lookup"><span data-stu-id="b0479-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="b0479-104">Il file offline dell'app ( `app_offline.htm` ) viene usato dal modulo ASP.NET Core per arrestare un'app.</span><span class="sxs-lookup"><span data-stu-id="b0479-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="b0479-105">Se viene rilevato un file con il nome `app_offline.htm` nella directory radice di un'app, il modulo ASP.NET Core tenta di arrestare normalmente l'app e di arrestare l'elaborazione delle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="b0479-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="b0479-106">Se l'app è ancora in esecuzione dopo il numero di secondi definito in `shutdownTimeLimit` , il modulo ASP.NET Core interrompe il processo in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b0479-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="b0479-107">Mentre il `app_offline.htm` file è presente, il modulo ASP.NET Core risponde alle richieste inviando di nuovo il contenuto del `app_offline.htm` file.</span><span class="sxs-lookup"><span data-stu-id="b0479-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="b0479-108">Quando il `app_offline.htm` file viene rimosso, la richiesta successiva avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="b0479-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="b0479-109">Quando si usa il modello di hosting out-of-process, l'app potrebbe non arrestarsi immediatamente se una connessione è aperta.</span><span class="sxs-lookup"><span data-stu-id="b0479-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="b0479-110">Ad esempio, una connessione WebSocket potrebbe ritardare l'arresto dell'app.</span><span class="sxs-lookup"><span data-stu-id="b0479-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="b0479-111">File di distribuzione bloccati</span><span class="sxs-lookup"><span data-stu-id="b0479-111">Locked deployment files</span></span>

<span data-ttu-id="b0479-112">I file nella cartella di distribuzione sono bloccati durante l'esecuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="b0479-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="b0479-113">I file bloccati non possono essere sovrascritti durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="b0479-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="b0479-114">`app_offline.htm` è il meccanismo principale per rilasciare i file bloccati.</span><span class="sxs-lookup"><span data-stu-id="b0479-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="b0479-115">`app_offline.htm` viene usato da Distribuzione Web per arrestare e avviare correttamente l'app.</span><span class="sxs-lookup"><span data-stu-id="b0479-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="b0479-116">`app_offline.htm` può essere usato manualmente per avviare e arrestare l'app (richiede PowerShell 5 o versione successiva):</span><span class="sxs-lookup"><span data-stu-id="b0479-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

<span data-ttu-id="b0479-117">Nello script di PowerShell precedente:</span><span class="sxs-lookup"><span data-stu-id="b0479-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="b0479-118">Il segnaposto `{PATH TO APP}` è il percorso dell'app.</span><span class="sxs-lookup"><span data-stu-id="b0479-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="b0479-119">Il `New-Item` comando interrompe il pool di app.</span><span class="sxs-lookup"><span data-stu-id="b0479-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="b0479-120">Il `Remove-Item` comando avvia il pool di app.</span><span class="sxs-lookup"><span data-stu-id="b0479-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="b0479-121">I comandi tra il `New-Item` comando e il `Remove-Item` comando vengono forniti dallo sviluppatore per la distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="b0479-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="b0479-122">I file possono essere sbloccati anche arrestando manualmente il pool di applicazioni in Gestione IIS sul server.</span><span class="sxs-lookup"><span data-stu-id="b0479-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="b0479-123">Non usare il `app_offine.htm` file quando si usa Gestione IIS per arrestare e riavviare il pool di app.</span><span class="sxs-lookup"><span data-stu-id="b0479-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>
