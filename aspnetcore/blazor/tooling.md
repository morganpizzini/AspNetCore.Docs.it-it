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
ms.openlocfilehash: 3ff610225c798624b7ead7d365924ae3d193829d
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944951"
---
<!-- zone_pivot_groups: operating-systems -->

# <a name="tooling-for-aspnet-core-blazor"></a>Strumenti per ASP.NET CoreBlazor

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Selezionare gli strumenti per la piattaforma:

* Windows: selezionare la scheda **Visual Studio** .
* Linux: selezionare la scheda **Visual Studio Code** .
* macOS: selezionare la scheda **Visual Studio per Mac** .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro **sviluppo di ASP.NET e Web** .

1. Creare un nuovo progetto.

1. Selezionare ** Blazor app**. Selezionare **Avanti**.

1. Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito. Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto. Selezionare **Crea**.

1. Per un' Blazor WebAssembly esperienza, scegliere il modello ** Blazor WebAssembly app** . Per un' Blazor Server esperienza, scegliere il modello ** Blazor Server app** . Selezionare **Crea**.

   Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* e *Blazor Server* , vedere <xref:blazor/hosting-models> .

1. Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

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

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

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

---

<!--

::: zone pivot="os-windows"

1. Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.

1. Create a new project.

1. Select **Blazor App**. Select **Next**.

1. Provide a project name in the **Project name** field or accept the default project name. Confirm the **Location** entry is correct or provide a location for the project. Select **Create**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Create**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-linux"

1. Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:

   ```dotnetcli
   dotnet --version
   ```

1. Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).

1. Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.

   To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`. Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.

1. For a Blazor WebAssembly experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   For a Blazor Server experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Open the `WebApplication1` folder in Visual Studio Code.

1. The IDE requests that you add assets to build and debug the project. Select **Yes**.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-macos"

1. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

   For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

If a prompt appears to trust the development certificate, trust the certificate and continue. The user and keychain passwords are required to trust the certificate.

::: zone-end

-->
