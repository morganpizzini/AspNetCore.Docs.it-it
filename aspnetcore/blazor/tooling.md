---
title: Strumenti per ASP.NET CoreBlazor
author: guardrex
description: Informazioni sugli strumenti disponibili per la compilazione di Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems-set-one
ms.openlocfilehash: 538257597ec5c6c705d8280a817c409debe22224
ms.sourcegitcommit: c6467f86c09b1f608b09d37d33c8c43de7ae8bc7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2020
ms.locfileid: "85946808"
---
# <a name="tooling-for-aspnet-core-blazor"></a>Strumenti per ASP.NET CoreBlazor

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

::: zone pivot="os-windows"

1. Installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro **sviluppo di ASP.NET e Web** .

1. Creare un nuovo progetto.

1. Selezionare ** Blazor app**. Selezionare **Avanti**.

1. Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito. Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto. Selezionare **Crea**.

1. Per un' Blazor WebAssembly esperienza, scegliere il modello ** Blazor WebAssembly app** . Per un' Blazor Server esperienza, scegliere il modello ** Blazor Server app** . Selezionare **Crea**.

   Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* e *Blazor Server* , vedere <xref:blazor/hosting-models> .

1. Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.

::: zone-end

::: zone pivot="os-linux"

1. Installare la versione più recente di [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Se in precedenza è stato installato l'SDK, è possibile determinare la versione installata eseguendo il comando seguente in una shell dei comandi:

   ```dotnetcli
   dotnet --version
   ```

1. Installare la versione più recente di [Visual Studio Code](https://code.visualstudio.com/).

1. Installare la versione più recente [di C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione del [debugger JavaScript (notturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true` .

   Per impostare `debug.javascript.usePreview` `true` l'utilizzo dell'interfaccia utente di vs Code **File**, aprire  >  **Preferences**  >  **Impostazioni** preferenze file e cercare `debug javascript use preview` . Selezionare la casella di controllo **Usa il nuovo debugger JavaScript in anteprima per Node.js e Chrome**.

1. Per un' Blazor WebAssembly esperienza, eseguire il comando seguente in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Per un' Blazor Server esperienza, eseguire il comando seguente in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* e *Blazor Server* , vedere <xref:blazor/hosting-models> .

1. Aprire la cartella `WebApplication1` in Visual Studio Code.

1. Le richieste dell'IDE aggiungono risorse per compilare ed eseguire il debug del progetto. Selezionare **Sì**.

1. Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.

::: zone-end

::: zone pivot="os-macos"

1. Installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Selezionare **file**  >  **nuova soluzione** o creare un **nuovo** progetto dalla **finestra Start**.

1. Nella barra laterale selezionare app **Web e console**  >  **App**.

   Per un' Blazor WebAssembly esperienza, scegliere il modello ** Blazor WebAssembly app** . Per un' Blazor Server esperienza, scegliere il modello ** Blazor Server app** . Selezionare **Avanti**.

   Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* e *Blazor Server* , vedere <xref:blazor/hosting-models> .

1. Verificare le configurazioni seguenti:

   * Il **Framework di destinazione** è impostato su **.NET Core 3,1**.
   * **L'autenticazione** è impostata su **Nessuna autenticazione**.
   
   Selezionare **Avanti**.

1. Nel campo **nome progetto** assegnare un nome all'app `WebApplication1` . Selezionare **Crea**.

1. Selezionare **Esegui**  >  **Avvia senza eseguire debug** per eseguire l'app *senza il debugger*. Eseguire l'app con **Esegui**  >  **debug Avvia debug** o il pulsante Esegui (&#9654;) per eseguire l'app *con il debugger*.

Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare. Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.

::: zone-end
