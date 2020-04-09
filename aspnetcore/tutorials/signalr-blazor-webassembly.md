---
title: Utilizzo di SignalR Blazor ASP.NET Core con WebAssembly
author: guardrex
description: Creare un'app di SignalR chat Blazor che usa ASP.NET Core con WebAssembly.Create a chat app that uses ASP.NET Core with WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: c4843dc282e1978b39738e206ecc79ded87fcff9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306576"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Utilizzo di ASP.NET Core SignalR con Blazor WebAssembly

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Questa esercitazione illustra le nozioni di base per la creazione di un'app in tempo reale usando SignalR con Blazor WebAssembly.This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly. Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto di app ospitata Blazor WebAssemblyCreate a Blazor WebAssembly Hosted app project
> * Aggiungere la libreria client di SignalR
> * Aggiungere un hub SignalRAdd a SignalR hub
> * Aggiungere servizi SignalR e un endpoint per l'hub SignalRAdd SignalR services and an endpoint for the SignalR hub
> * Aggiungere il codice del componente Razor per la chat

Alla fine di questo tutorial, avrai un'app di chat funzionante.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))

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

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Creare un progetto di app Blazor WebAssembly ospitato

Quando non si usa Visual Studio versione 16.6 Preview 2 o versione successiva, installare il modello [Blazor WebAssembly.](xref:blazor/hosting-models#blazor-webassembly) Il pacchetto [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) ha una versione di anteprima mentre Blazor WebAssembly è in anteprima. In una shell dei comandi, eseguire il comando seguente:In a command shell, execute the following command:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
```

Seguire le istruzioni per la scelta degli utensili:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Creare un nuovo progetto.

1. Selezionare **Blazor App** e **quindi Avanti**.

1. Digitare "BlazorSignalRApp" nel campo **Nome progetto.** Verificare che la voce **Posizione** sia corretta o specificare una posizione per il progetto. Selezionare **Create** (Crea).

1. Scegliere il modello **Blazor WebAssembly App.**

1. In **Avanzate**selezionare la casella di controllo **ASP.NET Core ospitato.**

1. Selezionare **Create** (Crea).

> [!NOTE]
> Se è stata aggiornata o installata una nuova versione di Visual Studio e il modello WebAssembly `dotnet new` Blazor non viene visualizzato nell'interfaccia utente di VS, reinstallare il modello utilizzando il comando illustrato in precedenza.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. In una shell dei comandi, eseguire il comando seguente:In a command shell, execute the following command:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. In Visual Studio Code apri la cartella del progetto dell'app.

1. Quando viene visualizzata la finestra di dialogo per aggiungere risorse per compilare ed eseguire il debug dell'app, selezionare **Sì**. Visual Studio Code aggiunge automaticamente la cartella *.vscode* con i file *launch.json* e *tasks.json* generati.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. In una shell dei comandi, eseguire il comando seguente:In a command shell, execute the following command:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. In Visual Studio per Mac aprire il progetto passando alla cartella del progetto e aprendo il file di soluzione del progetto (*.sln*).

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

In una shell dei comandi, eseguire il comando seguente:In a command shell, execute the following command:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Aggiungere la libreria client di SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **BlazorSignalRApp.Client** e **scegliere Gestisci pacchetti NuGet**.

1. Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l'origine **del pacchetto** sia impostata su *nuget.org*.

1. Con **l'opzione Sfoglia** selezionata, digitare "Microsoft.AspNetCore.SignalR.Client" nella casella di ricerca.

1. Nei risultati della ricerca `Microsoft.AspNetCore.SignalR.Client` selezionare il pacchetto e scegliere **Installa**.

1. Se viene visualizzata la finestra di dialogo **Anteprima modifiche,** selezionare **OK**.

1. Se viene visualizzata la finestra di dialogo **Accettazione licenza,** selezionare **Accetto** se si accettano le condizioni di licenza.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Nel **Terminale integrato** **(Visualizza** > **terminale** dalla barra degli strumenti), eseguire i seguenti comandi:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Nella barra **laterale Soluzione** fare clic con il pulsante destro del mouse sul progetto **BlazorSignalRApp.Client** e scegliere **Gestisci pacchetti NuGet**.

1. Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l'elenco a discesa Origine sia impostato su *nuget.org*.

1. Con **l'opzione Sfoglia** selezionata, digitare "Microsoft.AspNetCore.SignalR.Client" nella casella di ricerca.

1. Nei risultati della ricerca selezionare la `Microsoft.AspNetCore.SignalR.Client` casella di controllo accanto al pacchetto e scegliere **Aggiungi pacchetto**.

1. Se viene visualizzata la finestra di dialogo **Accettazione licenza,** selezionare **Accetta** se si accettano le condizioni di licenza.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

In una shell dei comandi, eseguire i seguenti comandi:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Aggiungere un hub SignalRAdd a SignalR hub

Nel progetto **BlazorSignalRApp.Server** creare una cartella *Hubs* (plural) e aggiungere la classe seguente `ChatHub` (*Hubs/ChatHub.cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a>Aggiungere servizi SignalR e un endpoint per l'hub SignalRAdd SignalR services and an endpoint for the SignalR hub

1. Nel progetto **BlazorSignalRApp.Server** aprire il file *Startup.cs.*

1. Aggiungere lo spazio `ChatHub` dei nomi per la classe all'inizio del file:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Aggiungere i servizi `Startup.ConfigureServices`SignalR a :

   ```csharp
   services.AddSignalR();
   ```

1. Tra `Startup.Configure` gli endpoint per la route del controller predefinito e il fallback lato client, aggiungere un endpoint per l'hub:In between the endpoints for the default controller route and the client-side fallback, add an endpoint for the hub:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Aggiungere il codice del componente Razor per la chat

1. Nel progetto **BlazorSignalRApp.Client** aprire il file *Pages/Index.razor.*

1. Sostituire il markup con il codice seguente:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Eseguire l'app

1. Seguire le istruzioni per gli strumenti:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. In **Esplora soluzioni**selezionare il progetto **BlazorSignalRApp.Server.** Premete **Ctrl e F5** per eseguire l'app senza eseguire il debug.

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**. Il nome e il messaggio vengono visualizzati istantaneamente su entrambe le pagine:

   ![SignalR Blazor WebAssembly app di esempio aperto in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citazioni: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Selezionare **Esegui debug** > **senza eseguire debug** dalla barra degli strumenti.

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**. Il nome e il messaggio vengono visualizzati istantaneamente su entrambe le pagine:

   ![SignalR Blazor WebAssembly app di esempio aperto in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citazioni: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Nella barra **laterale Soluzione** selezionare il progetto **BlazorSignalRApp.Server.** Scegliere **Esegui** > **senza eseguire debug**dal menu .

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**. Il nome e il messaggio vengono visualizzati istantaneamente su entrambe le pagine:

   ![SignalR Blazor WebAssembly app di esempio aperto in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citazioni: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

1. In una shell dei comandi, eseguire i seguenti comandi:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**. Il nome e il messaggio vengono visualizzati istantaneamente su entrambe le pagine:

   ![SignalR Blazor WebAssembly app di esempio aperto in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citazioni: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare Blazor un progetto di app ospitata WebAssemblyCreate a WebAssembly Hosted app project
> * Aggiungere SignalR la libreria client
> * Aggiungere SignalR un hub
> * Aggiungere SignalR servizi e un SignalR endpoint per l'hubAdd services and an endpoint for the hub
> * Aggiungere il codice del componente Razor per la chat

Per altre informazioni Blazor sulla creazione Blazor di app, vedere la documentazione:To learn more about building apps, see the documentation:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:signalr/introduction>
