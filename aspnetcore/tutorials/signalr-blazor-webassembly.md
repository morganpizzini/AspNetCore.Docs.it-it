---
title: Usare ASP.NET Core SignalR con Blazor webassembly
author: guardrex
description: Creare un'app di chat che usa ASP.NET Core SignalR con Blazor webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 720f534426cc0e2b32778e49050c7f7d75ecd60d
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679592"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Usare ASP.NET Core SignalR con Blazor webassembly

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Questa esercitazione illustra le nozioni di base per la creazione di un'app in tempo reale usando SignalR con Blazor webassembly. Si apprenderà come:

> [!div class="checklist"]
> * Creare un Blazor progetto di app ospitata Webassembly
> * Aggiungere la SignalR libreria client
> * Aggiungere un SignalR Hub
> * Aggiungere SignalR Servizi e un endpoint per l' SignalR Hub
> * Aggiungi Razor codice componente per la chat

Al termine di questa esercitazione, si disporrà di un'app di chat funzionante.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16,6 o versione successiva](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con il carico di lavoro **sviluppo di ASP.NET e Web**
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* [Visual Studio per Mac versione 8,6 o successiva](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Creare un Blazor progetto di app webassembly ospitato

Seguire le istruzioni per la scelta degli strumenti:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!NOTE]
> Sono necessari Visual Studio 16,6 o versioni successive e .NET Core SDK 3.1.300 o versioni successive.

1. Creare un nuovo progetto.

1. Selezionare ** Blazor app** e fare clic su **Next (avanti**).

1. Digitare "BlazorSignalRApp" nel campo **nome progetto** . Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto. Selezionare **Crea**.

1. Scegliere il modello ** Blazor app webassembly** .

1. In **Avanzate**selezionare la casella di controllo **ASP.NET Core Hosted** .

1. Selezionare **Crea**.

> [!NOTE]
> Se è stato eseguito l'aggiornamento o l'installazione di una nuova versione di Visual Studio e il Blazor modello webassembly non viene visualizzato nell'interfaccia utente di Visual Studio, reinstallare il modello usando il `dotnet new` comando illustrato in precedenza.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. In una shell dei comandi eseguire il comando seguente:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. In Visual Studio Code aprire la cartella del progetto dell'app.

1. Quando viene visualizzata la finestra di dialogo per aggiungere asset per compilare ed eseguire il debug dell'app, selezionare **Sì**. Visual Studio Code aggiunge automaticamente la cartella *. VSCODE* con i *launch.jsgenerati su* e *tasks.jsnei* file.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Installare la versione più recente di [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) e seguire questa procedura:

1. Selezionare **file**  >  **nuova soluzione** o creare un **nuovo** progetto dalla **finestra Start**.

1. Nella barra laterale selezionare app **Web e console**  >  **App**.

1. Scegliere il modello ** Blazor app webassembly** . Selezionare **Avanti**.

   Verificare le configurazioni seguenti:

   * Il **Framework di destinazione** è impostato su **.NET Core 3,1**.
   * **L'autenticazione** è impostata su **Nessuna autenticazione**.

   Selezionare la casella di controllo **ASP.NET Core Hosted** .

   Selezionare **Avanti**.

1. Nel campo **nome progetto** assegnare un nome all'app `BlazorSignalRApp` . Selezionare **Crea**.

   Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare. Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.

1. Aprire il progetto passando alla cartella del progetto e aprendo il file di soluzione del progetto (*. sln*).

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

In una shell dei comandi eseguire il comando seguente:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Aggiungere la SignalR libreria client

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **BlazorSignalRApp. client** e scegliere **Gestisci pacchetti NuGet**.

1. Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l' **origine del pacchetto** sia impostata su *NuGet.org*.

1. Con **Sfoglia** selezionato digitare " SignalR Microsoft. AspNetCore. Client "nella casella di ricerca.

1. Nei risultati della ricerca selezionare [Microsoft. AspNetCore. SignalR Pacchetto client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) e selezionare **Installa**.

1. Se viene visualizzata la finestra di dialogo **Anteprima modifiche** , selezionare **OK**.

1. Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare Accetto se **si accettano le** condizioni di licenza.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Nel **terminale integrato** (**visualizzare**il  >  **terminale** dalla barra degli strumenti) eseguire i comandi seguenti:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Nella barra laterale **soluzione** fare clic con il pulsante destro del mouse sul progetto **BlazorSignalRApp. client** e scegliere **Gestisci pacchetti NuGet**.

1. Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l'elenco a discesa origine sia impostato su *NuGet.org*.

1. Con **Sfoglia** selezionato digitare " SignalR Microsoft. AspNetCore. Client "nella casella di ricerca.

1. Nei risultati della ricerca selezionare la casella di controllo accanto a [Microsoft. AspNetCore. SignalR Pacchetto client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) e selezionare **Aggiungi pacchetto**.

1. Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare **Accetto** se si accettano le condizioni di licenza.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

In una shell dei comandi eseguire i comandi seguenti:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Aggiungere un SignalR Hub

Nel progetto **BlazorSignalRApp. Server** creare una cartella *Hub* (plurale) e aggiungere la classe seguente `ChatHub` (*Hub/ChatHub. cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Aggiungere servizi e un endpoint per l' SignalR Hub

1. Nel progetto **BlazorSignalRApp. Server** aprire il file *Startup.cs* .

1. Aggiungere lo spazio dei nomi per la `ChatHub` classe all'inizio del file:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Aggiungere SignalR e rispondere ai servizi middleware di compressione per `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. In `Startup.Configure`:

   * Usare il middleware della compressione della risposta nella parte superiore della configurazione della pipeline di elaborazione.
   * Tra gli endpoint per i controller e il fallback sul lato client, aggiungere un endpoint per l'hub.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a>Aggiungi Razor codice componente per la chat

1. Nel progetto **BlazorSignalRApp. client** aprire il file *pages/index. Razor* .

1. Sostituire il markup con il codice seguente:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Eseguire l'app

1. Seguire le istruzioni per gli strumenti:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. In **Esplora soluzioni**selezionare il progetto **BlazorSignalRApp. Server** . Premere <kbd>F5</kbd> per eseguire l'app con il debug o <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app senza debug.

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**. Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:

   ![SignalRBlazorApp di esempio webassembly aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Quando VS Code offre la creazione di un profilo di avvio per l'app Server (*con estensione VSCODE/launch.js*), la `program` voce appare simile alla seguente per puntare all'assembly dell'app ( `{APPLICATION NAME}.Server.dll` ):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Premere <kbd>F5</kbd> per eseguire l'app con il debug o <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app senza debug.

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**. Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:

   ![SignalRBlazorApp di esempio webassembly aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Nella barra laterale della **soluzione** selezionare il progetto **BlazorSignalRApp. Server** . Premere <kbd>⌘</kbd> + <kbd>↩</kbd> per eseguire l'app con debug o <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> per eseguire l'app senza debug.

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**. Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:

   ![SignalRBlazorApp di esempio webassembly aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

1. In una shell dei comandi eseguire i comandi seguenti:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**. Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:

   ![SignalRBlazorApp di esempio webassembly aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un Blazor progetto di app ospitata Webassembly
> * Aggiungere la SignalR libreria client
> * Aggiungere un SignalR Hub
> * Aggiungere SignalR Servizi e un endpoint per l' SignalR Hub
> * Aggiungi Razor codice componente per la chat

Per altre informazioni sulla creazione di Blazor app, vedere la Blazor documentazione:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:signalr/introduction>
* [SignalRnegoziazione tra le origini per l'autenticazione](xref:blazor/hosting-model-configuration#signalr-cross-origin-negotiation-for-authentication)
