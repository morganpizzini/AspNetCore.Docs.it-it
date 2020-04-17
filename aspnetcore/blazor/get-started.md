---
title: Introduzione a ASP.NET CoreBlazor
author: guardrex
description: Inizia a Blazor creare Blazor un'app con gli strumenti di tua scelta.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 7fe4fbb082f08d4f71684c836a826d8b6dd888f6
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488728"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Introduzione a ASP.NET Core Blazor

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Per iniziare a utilizzare Blazor, segui le indicazioni per la tua scelta di utensili:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Per creare app del server Blazor, installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro di **sviluppo Web e di ASP.NET.**

   Per creare app Blazor Server e Blazor WebAssembly, installare l'anteprima più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) con il carico di lavoro **di ASP.NET e sviluppo Web.**

   Per informazioni sui due modelli di hosting Blazor, *Blazor WebAssembly* e *Blazor Server*, vedere <xref:blazor/hosting-models>.

1. Installare il modello di anteprima [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

1. Creare un nuovo progetto.

1. Selezionare **Blazor App**. Selezionare **Avanti**.

1. Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito. Verificare che la voce **Posizione** sia corretta o specificare una posizione per il progetto. Selezionare **Create** (Crea).

1. Per un'esperienza Blazor WebAssembly (Visual Studio 16.6 Preview 2 o versione successiva), scegliere il modello **Blazor WebAssembly App.** Per un'esperienza del server Blazor (Visual Studio 16.4 o versione successiva), scegliere il modello **Blazor Server App.** Selezionare **Create** (Crea).

1. Premere <kbd>CTRL</kbd>+<kbd>F5</kbd> per eseguire l'app.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Installare [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Facoltativamente, installare il modello di anteprima [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

   > [!NOTE]
   > Per utilizzare il modello WebAssembly [3.1.201 o versione successiva di .NET Core SDK versione 3.1.201 o successiva,](https://dotnet.microsoft.com/download/dotnet-core/3.1) è **necessario** . Verificare la versione di .NET Core SDK installata eseguendolo `dotnet --version` in una shell dei comandi.

1. Installare [Visual Studio Code](https://code.visualstudio.com/).

1. Installare l'estensione più recente per [l'estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) di codice di `debug.javascript.usePreview` Visual `true`Studio e l'estensione [Debugger JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con impostato su .

1. Per un'esperienza di Blazor Server, eseguire il comando seguente in una shell dei comandi:For a Blazor Server experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Per un'esperienza WebAssembly Blazor, eseguire il comando seguente in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Per informazioni sui due modelli di hosting Blazor, *Blazor* Server <xref:blazor/hosting-models>e *Blazor WebAssembly,* vedere .

1. Aprire la cartella *WebApplication1* in Visual Studio Code.

1. L'IDE richiede di aggiungere risorse per compilare ed eseguire il debug del progetto. Selezionare **Sì**.

1. Con Blazor Server, esegui l'app usando il debugger del codice di Visual Studio.

   Con Blazor WebAssembly, avviare l'app utilizzando la configurazione di avvio **di .NET Core Launch (Blazor Standalone)** e quindi avviare il browser utilizzando **.NET Core Debug Blazor Web Assembly nella** configurazione di avvio di Chrome (richiede Chrome). Per altre informazioni, vedere <xref:blazor/debug#visual-studio-code>.

1. In un browser passare a `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Blazor Server è supportato in Visual Studio per Mac. Blazor WebAssembly non è supportato in questo momento. Per compilare app Blazor WebAssembly in macOS, seguire le indicazioni nella scheda dell'interfaccia della riga di **comando di .NET Core.**

1. Installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Selezionare **File** > **nuova soluzione** o creare un nuovo **progetto**.

1. Nella barra laterale seleziona**App** **.NET Core** > .

1. Selezionare il modello **Blazor Server App.** Selezionare **Create** (Crea).

   Per informazioni sul modello di hosting Blazor Server, vedere <xref:blazor/hosting-models>.

1. Impostare Framework di **destinazione** su **.NET Core 3.1** e selezionare **Avanti**.

1. Nel campo **Nome progetto** assegnare un nome all'app. `WebApplication1` Selezionare **Create** (Crea).

1. Selezionare **Esegui** > **senza debug** per eseguire l'app senza il *debugger.* Eseguire l'app con **Avvia debug** per eseguire l'app *con il debugger.*

Se viene visualizzato un prompt per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

1. Installare [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Facoltativamente, installare il modello di anteprima [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

   > [!NOTE]
   > Per utilizzare il modello WebAssembly [3.1.201 o versione successiva di .NET Core SDK versione 3.1.201 o successiva,](https://dotnet.microsoft.com/download/dotnet-core/3.1) è **necessario** . Verificare la versione di .NET Core SDK installata eseguendolo `dotnet --version` in una shell dei comandi.

1. Per un'esperienza di Blazor Server, eseguire i seguenti comandi in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Per un'esperienza WebAssembly Blazor, eseguire i seguenti comandi in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Per informazioni sui due modelli di hosting Blazor, *Blazor* Server <xref:blazor/hosting-models>e *Blazor WebAssembly,* vedere .

1. In un browser passare a `https://localhost:5001`.

---

Più pagine sono disponibili dalle schede nella barra laterale:

* Home
* Contatore
* Recuperare i dati

Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina. L'incremento di un contatore in una Blazor pagina Web richiede in genere la scrittura di JavaScript, ma con l'uso di C.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Una richiesta `/counter` per nel browser, `@page` come specificato dalla direttiva nella parte superiore, fa sì che il componente per eseguire il rendering del `Counter` contenuto. I componenti eseguono il rendering in una rappresentazione in memoria dell'albero di rendering che può quindi essere utilizzata per aggiornare l'interfaccia utente in modo flessibile ed efficiente.

Ogni volta che viene selezionato il pulsante **Click me:**

* L'evento `onclick` viene generato.
* Viene chiamato il metodo `IncrementCount` .
* L'oggetto `currentCount` viene incrementato.
* Il rendering del componente viene eseguito nuovamente.

Il runtime confronta il nuovo contenuto con il contenuto precedente e applica il contenuto modificato solo al modello DOM (Document Object Model).

Aggiungere un componente a un altro componente utilizzando la sintassi HTML. Ad esempio, `Counter` aggiungi il componente alla home page `<Counter />` dell'app aggiungendo un elemento al `Index` componente.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Eseguire l'app. La homepage ha un proprio `Counter` contatore fornito dal componente.

I parametri del componente vengono specificati utilizzando gli attributi o il [contenuto figlio](xref:blazor/components#child-content), che consentono di impostare le proprietà del componente figlio. Per aggiungere un `Counter` parametro al componente, `@code` aggiornare il blocco del componente:

* Aggiungere una proprietà `IncrementAmount` pubblica `[Parameter]` per con un attributo.
* Modificare il metodo `IncrementCount` per usare `IncrementAmount` quando si aumenta il valore di `currentCount`.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Specificare `IncrementAmount` l'elemento `Index` `<Counter>` nell'elemento del componente utilizzando un attributo.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Eseguire l'app. Il `Index` componente ha un proprio contatore che viene incrementato di dieci ogni volta che viene selezionato il pulsante **Click me.** Il `Counter` componente (*Counter.razor*) `/counter` continua ad aumentare di uno.

## <a name="next-steps"></a>Passaggi successivi

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/templates>
* <xref:signalr/introduction>
