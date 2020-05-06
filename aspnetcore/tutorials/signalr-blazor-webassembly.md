---
title: Usare ASP.NET Core SignalR con Blazor webassembly
author: guardrex
description: Creare un'app di chat che usa SignalR ASP.NET Core Blazor con webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 1579b92dbc9db08bfdc5572e5d4245bd18d50590
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773788"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Usare ASP.NET Core SignalR con il webassembly Blazer

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Questa esercitazione illustra le nozioni di base per la creazione di un'app in tempo reale usando SignalR con il webassembly blazer. Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto di app ospitata webassembly Blazer
> * Aggiungere la libreria client di SignalR
> * Aggiungere un hub SignalR
> * Aggiungere i servizi SignalR e un endpoint per l'hub SignalR
> * Aggiungere il codice del componente Razor per la chat

Al termine di questa esercitazione, si disporrà di un'app di chat funzionante.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Creare un progetto di app webassembly in hosting Blazer

Quando non si usa Visual Studio versione 16,6 Preview 2 o versioni successive, installare il modello [Webassembly Blazer](xref:blazor/hosting-models#blazor-webassembly) . Il pacchetto [Microsoft. AspNetCore. Components. webassembly. templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) include una versione di anteprima mentre Webassembly blazer è in anteprima. In una shell dei comandi eseguire il comando seguente:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
```

Seguire le istruzioni per la scelta degli strumenti:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Creare un nuovo progetto.

1. Selezionare **app Blazer** e fare clic su **Avanti**.

1. Digitare "BlazorSignalRApp" nel campo **nome progetto** . Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto. Selezionare **Crea**.

1. Scegliere il modello **app Webassembly Blazer** .

1. In **Avanzate**selezionare la casella di controllo **ASP.NET Core Hosted** .

1. Selezionare **Crea**.

> [!NOTE]
> Se è stato eseguito l'aggiornamento o l'installazione di una nuova versione di Visual Studio e il modello webassembly Blazer non viene visualizzato nell'interfaccia utente di `dotnet new` Visual Studio, reinstallare il modello usando il comando illustrato in precedenza.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. In una shell dei comandi eseguire il comando seguente:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. In Visual Studio Code aprire la cartella del progetto dell'app.

1. Quando viene visualizzata la finestra di dialogo per aggiungere asset per compilare ed eseguire il debug dell'app, selezionare **Sì**. Visual Studio Code aggiunge automaticamente la cartella *. VSCODE* con i file *Launch. JSON* e *Tasks. JSON* generati.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. In una shell dei comandi eseguire il comando seguente:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. In Visual Studio per Mac aprire il progetto passando alla cartella del progetto e aprendo il file di soluzione del progetto (*. sln*).

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

In una shell dei comandi eseguire il comando seguente:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Aggiungere la libreria client di SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **BlazorSignalRApp. client** e scegliere **Gestisci pacchetti NuGet**.

1. Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l' **origine del pacchetto** sia impostata su *NuGet.org*.

1. Con **Sfoglia** selezionato, digitare "Microsoft. AspNetCore. SignalR. client" nella casella di ricerca.

1. Nei risultati della ricerca selezionare il `Microsoft.AspNetCore.SignalR.Client` pacchetto e selezionare **Installa**.

1. Se viene visualizzata la finestra di dialogo **Anteprima modifiche** , selezionare **OK**.

1. Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare Accetto se **si accettano le** condizioni di licenza.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Nel **terminale integrato** (**visualizzare** > il**terminale** dalla barra degli strumenti) eseguire i comandi seguenti:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Nella barra laterale **soluzione** fare clic con il pulsante destro del mouse sul progetto **BlazorSignalRApp. client** e scegliere **Gestisci pacchetti NuGet**.

1. Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l'elenco a discesa origine sia impostato su *NuGet.org*.

1. Con **Sfoglia** selezionato, digitare "Microsoft. AspNetCore. SignalR. client" nella casella di ricerca.

1. Nei risultati della ricerca selezionare la casella di controllo accanto al `Microsoft.AspNetCore.SignalR.Client` pacchetto e selezionare **Aggiungi pacchetto**.

1. Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare **Accetto** se si accettano le condizioni di licenza.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

In una shell dei comandi eseguire i comandi seguenti:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Aggiungere un hub SignalR

Nel progetto **BlazorSignalRApp. Server** creare una cartella *Hub* (plurale) e aggiungere la classe seguente `ChatHub` (*Hub/ChatHub. cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Aggiungere servizi e un endpoint per l'hub SignalR

1. Nel progetto **BlazorSignalRApp. Server** aprire il file *Startup.cs* .

1. Aggiungere lo spazio dei nomi `ChatHub` per la classe all'inizio del file:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Aggiungere i servizi del middleware SignalR e della `Startup.ConfigureServices`compressione della risposta a:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. In `Startup.Configure` tra gli endpoint per i controller e il fallback lato client aggiungere un endpoint per l'hub:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Aggiungere il codice del componente Razor per la chat

1. Nel progetto **BlazorSignalRApp. client** aprire il file *pages/index. Razor* .

1. Sostituire il markup con il codice seguente:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Eseguire l'app

1. Seguire le istruzioni per gli strumenti:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. In **Esplora soluzioni**selezionare il progetto **BlazorSignalRApp. Server** . Premere <kbd>F5</kbd> per eseguire l'app con il debug o <kbd>CTRL</kbd>+<kbd>F5</kbd> per eseguire l'app senza debug.

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**. Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:

   ![App di esempio webassembly di SignalR Blazer aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Virgolette: *Star Trek VI: il paese* &copy;non individuato 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Quando VS Code offre la creazione di un profilo di avvio per l'app Server (*. VSCODE/Launch. JSON*) `program` , la voce appare simile alla seguente per puntare all'assembly dell'app (`{APPLICATION NAME}.Server.dll`):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Premere <kbd>F5</kbd> per eseguire l'app con il debug o <kbd>CTRL</kbd>+<kbd>F5</kbd> per eseguire l'app senza debug.

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**. Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:

   ![App di esempio webassembly di SignalR Blazer aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Virgolette: *Star Trek VI: il paese* &copy;non individuato 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Nella barra laterale della **soluzione** selezionare il progetto **BlazorSignalRApp. Server** . Premere <kbd>⌘</kbd>+<kbd>↩</kbd>* * per eseguire l'app con debug o <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> per eseguire l'app senza debug.

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**. Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:

   ![App di esempio webassembly di SignalR Blazer aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Virgolette: *Star Trek VI: il paese* &copy;non individuato 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

1. In una shell dei comandi eseguire i comandi seguenti:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**. Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:

   ![App di esempio webassembly di SignalR Blazer aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Virgolette: *Star Trek VI: il paese* &copy;non individuato 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un Blazor progetto di app ospitata webassembly
> * Aggiungere la SignalR libreria client
> * Aggiungere un SignalR Hub
> * Aggiungere SignalR servizi e un endpoint per l' SignalR Hub
> * Aggiungi Razor codice componente per la chat

Per altre informazioni sulla creazione Blazor di app, vedere Blazor la documentazione:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:signalr/introduction>
