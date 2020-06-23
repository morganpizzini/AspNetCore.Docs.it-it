---
title: Inizia a usare ASP.NET CoreBlazor
author: guardrex
description: Inizia a Blazor creare un' Blazor app con gli strumenti che preferisci.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 63fee0b6a3152640a5483c2a682eec7d04742145
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243603"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Inizia a usare ASP.NET CoreBlazor

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Per iniziare Blazor , seguire le istruzioni per la scelta degli strumenti:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro **sviluppo di ASP.NET e Web** .

1. Creare un nuovo progetto.

1. Selezionare ** Blazor app**. Selezionare **Avanti**.

1. Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito. Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto. Selezionare **Crea**.

1. Per un' Blazor esperienza di webassembly, scegliere il modello ** Blazor app webassembly** . Per un' Blazor esperienza server, scegliere il modello ** Blazor applicazione server** . Selezionare **Crea**.

   Per informazioni sui due Blazor modelli di hosting, * Blazor webassembly* e * Blazor Server*, vedere <xref:blazor/hosting-models> .

1. Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Installare la versione più recente di [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Se in precedenza è stato installato l'SDK, è possibile determinare la versione installata eseguendo il comando seguente in una shell dei comandi:

   ```dotnetcli
   dotnet --version
   ```

1. Installare la versione più recente di [Visual Studio Code](https://code.visualstudio.com/).

1. Installare la versione più recente [di C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione del [debugger JavaScript (notturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true` .

  Per impostare `debug.javascript.usePreview` `true` l'utilizzo dell'interfaccia utente di vs Code **File**, aprire  >  **Preferences**  >  **Impostazioni** preferenze file e cercare `debug javascript use preview` . Selezionare la casella di controllo **Usa il nuovo debugger JavaScript in anteprima per Node.js e Chrome**.

1. Per un' Blazor esperienza di webassembly, eseguire il comando seguente in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Per un' Blazor esperienza server, eseguire il comando seguente in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Per informazioni sui due Blazor modelli di hosting, * Blazor webassembly* e * Blazor Server*, vedere <xref:blazor/hosting-models> .

1. Aprire la cartella `WebApplication1` in Visual Studio Code.

1. Le richieste dell'IDE aggiungono risorse per compilare ed eseguire il debug del progetto. Selezionare **Sì**.

1. Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Selezionare **file**  >  **nuova soluzione** o creare un **nuovo** progetto dalla **finestra Start**.

1. Nella barra laterale selezionare app **Web e console**  >  **App**.

   Per un' Blazor esperienza di webassembly, scegliere il modello ** Blazor app webassembly** . Per un' Blazor esperienza server, scegliere il modello ** Blazor applicazione server** . Selezionare **Avanti**.

   Per informazioni sui due Blazor modelli di hosting, * Blazor webassembly* e * Blazor Server*, vedere <xref:blazor/hosting-models> .

1. Verificare le configurazioni seguenti:

   * Il **Framework di destinazione** è impostato su **.NET Core 3,1**.
   * **L'autenticazione** è impostata su **Nessuna autenticazione**.
   
   Selezionare **Avanti**.

1. Nel campo **nome progetto** assegnare un nome all'app `WebApplication1` . Selezionare **Crea**.

1. Selezionare **Esegui**  >  **Avvia senza eseguire debug** per eseguire l'app *senza il debugger*. Eseguire l'app con **Esegui**  >  **debug Avvia debug** o il pulsante Esegui (&#9654;) per eseguire l'app *con il debugger*.

Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare. Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

1. Installare la versione più recente di [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Se in precedenza è stato installato l'SDK, è possibile determinare la versione installata eseguendo il comando seguente in una shell dei comandi:

   ```dotnetcli
   dotnet --version
   ```

1. Per un' Blazor esperienza di webassembly, eseguire i comandi seguenti in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Per un' Blazor esperienza server, eseguire i comandi seguenti in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Per informazioni sui due Blazor modelli di hosting, * Blazor webassembly* e * Blazor Server*, vedere <xref:blazor/hosting-models> .

1. In un browser passare a `https://localhost:5001`.

---

Nelle schede della barra laterale sono disponibili più pagine:

* Home page
* Contatore
* Recuperare i dati

Nella pagina contatore selezionare il pulsante per incrementare il contatore senza un aggiornamento della pagina. Per incrementare un contatore in una pagina Web è in genere necessario scrivere JavaScript, ma con Blazor è possibile usare C#.

`Pages/Counter.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Una richiesta di `/counter` nel browser, come specificato dalla direttiva nella `@page` parte superiore, determina il rendering del contenuto da parte del `Counter` componente. I componenti eseguono il rendering in una rappresentazione in memoria della struttura di rendering che può quindi essere utilizzata per aggiornare l'interfaccia utente in modo flessibile ed efficiente.

Ogni volta che si seleziona il pulsante:

* L' `onclick` evento viene generato.
* Viene chiamato il metodo `IncrementCount` .
* `currentCount`Viene incrementato.
* Il rendering del componente viene eseguito nuovamente.

Il runtime confronta il nuovo contenuto con il contenuto precedente e applica solo il contenuto modificato al Document Object Model (DOM).

Aggiungere un componente a un altro componente usando la sintassi HTML. Ad esempio, aggiungere il `Counter` componente alla Home page dell'app aggiungendo un `<Counter />` elemento al `Index` componente.

`Pages/Index.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Eseguire l'app. La Home page presenta il proprio contatore fornito dal `Counter` componente.

I parametri del componente vengono specificati utilizzando attributi o [contenuto figlio](xref:blazor/components/index#child-content), che consentono di impostare le proprietà per il componente figlio. Per aggiungere un parametro al `Counter` componente, aggiornare il blocco del componente `@code` :

* Aggiungere una proprietà pubblica per `IncrementAmount` con un [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo.
* Modificare il metodo `IncrementCount` per usare `IncrementAmount` quando si aumenta il valore di `currentCount`.

`Pages/Counter.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Specificare `IncrementAmount` nell'elemento del `Index` componente `<Counter>` usando un attributo.

`Pages/Index.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Eseguire l'app. Il `Index` componente ha il proprio contatore che aumenta di dieci ogni volta che viene selezionato il pulsante. Il `Counter` componente ( `Pages/Counter.razor` ) in `/counter` continua a incrementare di uno.

## <a name="next-steps"></a>Passaggi successivi

Procedura dettagliata per la compilazione di un' Blazor app:

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/templates>
* <xref:signalr/introduction>
