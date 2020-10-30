---
title: File offline dell'app (app_offline.htm)
author: rick-anderson
description: Informazioni sul funzionamento del file offline dell'app ( `app_offline.htm` ) con il modulo ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 4d71b95680a9b160ebb25116e35096495a2eaf93
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058649"
---
# <a name="app-offline-file-app_offlinehtm"></a>File offline dell'app ( `app_offline.htm` )

Il file offline dell'app ( `app_offline.htm` ) viene usato dal modulo ASP.NET Core per arrestare un'app.

Se viene rilevato un file con il nome `app_offline.htm` nella directory radice di un'app, il modulo ASP.NET Core tenta di arrestare normalmente l'app e di arrestare l'elaborazione delle richieste in ingresso. Se l'app è ancora in esecuzione dopo il numero di secondi definito in `shutdownTimeLimit` , il modulo ASP.NET Core interrompe il processo in esecuzione.

Mentre il `app_offline.htm` file è presente, il modulo ASP.NET Core risponde alle richieste inviando di nuovo il contenuto del `app_offline.htm` file. Quando il `app_offline.htm` file viene rimosso, la richiesta successiva avvia l'app.

Quando si usa il modello di hosting out-of-process, l'app potrebbe non arrestarsi immediatamente se una connessione è aperta. Ad esempio, una connessione WebSocket potrebbe ritardare l'arresto dell'app.

## <a name="locked-deployment-files"></a>File di distribuzione bloccati

I file nella cartella di distribuzione sono bloccati durante l'esecuzione dell'app. I file bloccati non possono essere sovrascritti durante la distribuzione.

`app_offline.htm` è il meccanismo principale per rilasciare i file bloccati. `app_offline.htm` viene usato da Distribuzione Web per arrestare e avviare correttamente l'app.

`app_offline.htm` può essere usato manualmente per avviare e arrestare l'app (richiede PowerShell 5 o versione successiva):

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

Nello script di PowerShell precedente:

* Il segnaposto `{PATH TO APP}` è il percorso dell'app.
* Il `New-Item` comando interrompe il pool di app.
* Il `Remove-Item` comando avvia il pool di app.
* I comandi tra il `New-Item` comando e il `Remove-Item` comando vengono forniti dallo sviluppatore per la distribuzione dell'app.

I file possono essere sbloccati anche arrestando manualmente il pool di applicazioni in Gestione IIS sul server. Non usare il `app_offine.htm` file quando si usa Gestione IIS per arrestare e riavviare il pool di app.
