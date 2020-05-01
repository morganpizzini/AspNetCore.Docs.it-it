---
title: Inizia a usare ASP.NET CoreBlazor
author: guardrex
description: Inizia Blazor a creare un' Blazor app con gli strumenti che preferisci.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 8ef55b92c45aa07113fd4601a3c7464b42125623
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604766"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Introduzione a ASP.NET Core Blazer

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Per iniziare a usare blazer, seguire le istruzioni per la scelta degli strumenti:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Per creare app del server blazer, installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro **sviluppo di ASP.NET e Web** .

   Per creare app di webassembly con server blazer e blazer, installare l'anteprima più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) con il carico di lavoro **sviluppo di ASP.NET e Web** .

   Per informazioni sui due modelli di hosting di Blazer, *Blazer webassembly* e il *Server Blazer*, vedere <xref:blazor/hosting-models>.

1. Installare il modello di anteprima [Webassembly Blazer](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. Creare un nuovo progetto.

1. Selezionare **app Blazer**. Selezionare **Avanti**.

1. Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito. Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto. Selezionare **Create** (Crea).

1. Per un'esperienza di webassembly blazer (Visual Studio 16,6 Preview 2 o versione successiva), scegliere il modello di **app Webassembly Blazer** . Per un'esperienza del server blazer (Visual Studio 16,4 o versione successiva), scegliere il modello di **app del server Blazer** . Selezionare **Create** (Crea).

1. Premere <kbd>CTRL</kbd>+<kbd>F5</kbd> per eseguire l'app.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Installare [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Facoltativamente, installare il modello di anteprima di [Webassembly Blazer](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

   > [!NOTE]
   > Per usare il modello di assembly webassembly 3,2 Preview 4 Blazer è **necessario** il [.NET Core SDK versione 3.1.201 o successiva](https://dotnet.microsoft.com/download/dotnet-core/3.1) . Verificare la versione di .NET Core SDK installata eseguendo `dotnet --version` in una shell dei comandi.

1. Installare [Visual Studio Code](https://code.visualstudio.com/).

1. Installare la versione più recente [di C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione del [debugger JavaScript (notturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true`.

1. Per un'esperienza del server blazer, eseguire il comando seguente in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Per un'esperienza di webassembly blazer, eseguire il comando seguente in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Per informazioni sui due modelli di hosting blazer, *Server Blazer* e *webassembly Blazer*, vedere <xref:blazor/hosting-models>.

1. Aprire la cartella *WebApplication1* in Visual Studio Code.

1. Le richieste dell'IDE aggiungono risorse per compilare ed eseguire il debug del progetto. Selezionare **Sì**.

1. Con il server blazer, eseguire l'app usando il debugger Visual Studio Code.

   Con l'assembly Web Blazer, avviare l'app usando la configurazione di avvio di **.NET Core (autonomia di Blaze)** e quindi avviare il browser usando l' **assembly Web di debug di .NET Core in Chrome** Launch Configuration (richiede Chrome). Per altre informazioni, vedere <xref:blazor/debug#visual-studio-code>.

1. In un browser passare a `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Il server blazer è supportato in Visual Studio per Mac. Il webassembly Blazer non è supportato in questo momento. Per compilare app webassembly di Blazer in macOS, seguire le istruzioni nella scheda **interfaccia della riga di comando di .NET Core** .

1. Installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Selezionare **file** > **nuova soluzione** o creare un **nuovo progetto**.

1. Nella barra laterale selezionare app **Web e console** > **App**.

1. Selezionare il modello **applicazione server Blazer** . Selezionare **Avanti**.

   Per informazioni sul modello di hosting del server blazer, <xref:blazor/hosting-models>vedere.

1. Verificare che il **Framework di destinazione** sia impostato su **.NET Core 3,1** e selezionare **Avanti**.

1. Nel campo **nome progetto** assegnare un nome all'app `WebApplication1`. Selezionare **Create** (Crea).

1. Selezionare **Esegui** > **esecuzione senza debug** per eseguire l'app *senza il debugger*. Eseguire l'app con **Avvia debug** o il pulsante esegui (&#9654;) per eseguire l'app *con il debugger*.

Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare. Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

1. Installare [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Facoltativamente, installare il modello di anteprima di [Webassembly Blazer](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

   > [!NOTE]
   > Per usare il modello di assembly webassembly 3,2 Preview 4 Blazer è **necessario** il [.NET Core SDK versione 3.1.201 o successiva](https://dotnet.microsoft.com/download/dotnet-core/3.1) . Verificare la versione di .NET Core SDK installata eseguendo `dotnet --version` in una shell dei comandi.

1. Per un'esperienza del server blazer, eseguire i comandi seguenti in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Per un'esperienza di webassembly blazer, eseguire i comandi seguenti in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Per informazioni sui due modelli di hosting blazer, *Server Blazer* e *webassembly Blazer*, vedere <xref:blazor/hosting-models>.

1. In un browser passare a `https://localhost:5001`.

---

Nelle schede della barra laterale sono disponibili più pagine:

* Home
* Contatore
* Recuperare i dati

Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina. Per incrementare un contatore in una pagina Web è in genere necessario scrivere JavaScript Blazor , ma con è possibile usare C#.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Una richiesta di `/counter` nel browser, come specificato dalla `@page` direttiva nella parte superiore, determina il rendering del `Counter` contenuto da parte del componente. I componenti eseguono il rendering in una rappresentazione in memoria della struttura di rendering che può quindi essere utilizzata per aggiornare l'interfaccia utente in modo flessibile ed efficiente.

Ogni volta che viene selezionato il pulsante **Click me** :

* L' `onclick` evento viene generato.
* Viene chiamato il metodo `IncrementCount` .
* `currentCount` Viene incrementato.
* Il rendering del componente viene eseguito nuovamente.

Il runtime confronta il nuovo contenuto con il contenuto precedente e applica solo il contenuto modificato al Document Object Model (DOM).

Aggiungere un componente a un altro componente usando la sintassi HTML. Ad esempio, aggiungere il `Counter` componente alla Home page dell'app aggiungendo un `<Counter />` elemento al `Index` componente.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Eseguire l'app. La Home page presenta il proprio contatore fornito dal `Counter` componente.

I parametri del componente vengono specificati utilizzando attributi o [contenuto figlio](xref:blazor/components#child-content), che consentono di impostare le proprietà per il componente figlio. Per aggiungere un parametro al `Counter` componente, aggiornare il `@code` blocco del componente:

* Aggiungere una proprietà pubblica per `IncrementAmount` con un `[Parameter]` attributo.
* Modificare il metodo `IncrementCount` per usare `IncrementAmount` quando si aumenta il valore di `currentCount`.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Specificare `IncrementAmount` nell' `<Counter>` elemento del `Index` componente usando un attributo.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Eseguire l'app. Il `Index` componente dispone di un contatore specifico che aumenta di dieci ogni volta che viene selezionato il pulsante **Click me** . Il `Counter` componente (*Counter. Razor*) a `/counter` continua a incrementare di uno.

## <a name="next-steps"></a>Passaggi successivi

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/templates>
* <xref:signalr/introduction>
