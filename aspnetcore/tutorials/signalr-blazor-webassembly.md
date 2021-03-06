---
title: Usare ASP.NET Core SignalR con un'app ospitata Blazor WebAssembly
author: guardrex
description: Creare un'app di chat che usa ASP.NET Core SignalR con Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 2f5630eac65b880bdefff2a4baf4f1878e981536
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058389"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a>Usare ASP.NET Core SignalR con un'app ospitata Blazor WebAssembly

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Questa esercitazione illustra le nozioni di base per la creazione di un'app in tempo reale usando SignalR con Blazor WebAssembly . Si apprenderà come:

> [!div class="checklist"]
> * Creare un Blazor WebAssembly progetto di app ospitata
> * Aggiungere la SignalR libreria client
> * Aggiungere un SignalR Hub
> * Aggiungere SignalR Servizi e un endpoint per l' SignalR Hub
> * Aggiungi Razor codice componente per la chat

Al termine di questa esercitazione, si disporrà di un'app di chat funzionante.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prerequisiti

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16,8 o versione successiva](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con il carico di lavoro **sviluppo di ASP.NET e Web**
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* [Visual Studio per Mac versione 8,8 o successiva](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

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

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a>Creare un progetto di app ospitata Blazor WebAssembly

Seguire le istruzioni per la scelta degli strumenti:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Sono necessari Visual Studio 16,8 o versioni successive e .NET Core SDK 5.0.0 o versioni successive.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Sono necessari Visual Studio 16,6 o versioni successive e .NET Core SDK 3.1.300 o versioni successive.

::: moniker-end

1. Creare un nuovo progetto.

1. Selezionare **Blazor app** e fare clic su **Next (avanti**).

1. Digitare `BlazorSignalRApp` nel campo **nome progetto** . Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto. Selezionare **Create** (Crea).

1. Scegliere il modello di **Blazor WebAssembly app** .

1. In **Avanzate** selezionare la casella di controllo **ASP.NET Core Hosted** .

1. Selezionare **Create** (Crea).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. In una shell dei comandi eseguire il comando seguente:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. In Visual Studio Code aprire la cartella del progetto dell'app.

1. Quando viene visualizzata la finestra di dialogo per aggiungere asset per compilare ed eseguire il debug dell'app, selezionare **Sì**. Visual Studio Code aggiunge automaticamente la `.vscode` cartella con `launch.json` `tasks.json` i file e generati.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Installare la versione più recente di [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) e seguire questa procedura:

1. Selezionare **file**  >  **nuova soluzione** o creare un **nuovo** progetto dalla **finestra Start**.

1. Nella barra laterale selezionare app **Web e console**  >  .

1. Scegliere il modello di **Blazor WebAssembly app** . Selezionare **Avanti**.

1. Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**. Selezionare la casella di controllo **ASP.NET Core Hosted** . Selezionare **Avanti**.

1. Nel campo **nome progetto** assegnare un nome all'app `BlazorSignalRApp` . Selezionare **Create** (Crea).

   Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare. Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.

1. Aprire il progetto passando alla cartella del progetto e aprendo il file di soluzione del progetto ( `.sln` ).

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

In una shell dei comandi eseguire il comando seguente:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a>Aggiungere la SignalR libreria client

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul `BlazorSignalRApp.Client` progetto e scegliere **Gestisci pacchetti NuGet**.

1. Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l' **origine del pacchetto** sia impostata su `nuget.org` .

1. Con **Sfoglia** selezionato, digitare `Microsoft.AspNetCore.SignalR.Client` nella casella di ricerca.

1. Nei risultati della ricerca selezionare il [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacchetto e selezionare **Installa**.

1. Se viene visualizzata la finestra di dialogo **Anteprima modifiche** , selezionare **OK**.

1. Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare Accetto se **si accettano le** condizioni di licenza.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Nel **terminale integrato** (**visualizzare** il  >  **terminale** dalla barra degli strumenti) eseguire i comandi seguenti:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Nella barra laterale **soluzione** fare clic con il pulsante destro del mouse sul `BlazorSignalRApp.Client` progetto e scegliere **Gestisci pacchetti NuGet**.

1. Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l'elenco a discesa origine sia impostato su `nuget.org` .

1. Con **Sfoglia** selezionato, digitare `Microsoft.AspNetCore.SignalR.Client` nella casella di ricerca.

1. Nei risultati della ricerca selezionare la casella di controllo accanto al [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacchetto e selezionare **Aggiungi pacchetto**.

1. Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare **Accetto** se si accettano le condizioni di licenza.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

In una shell dei comandi eseguire i comandi seguenti:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Aggiungere il pacchetto System. Text. Encodings. Web

A causa di un problema di risoluzione del pacchetto quando si usa [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 in un'app ASP.NET Core 3,1, il `BlazorSignalRApp.Server` progetto richiede un riferimento al pacchetto per [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) . Il problema sottostante verrà risolto in una versione futura della patch di .NET 5. Per altre informazioni, vedere [System.Text.Json definisce netcoreapp 3.0 senza dipendenze (DotNet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).

Per aggiungere [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) al `BlazorSignalRApp.Server` progetto della soluzione ospitata ASP.NET Core 3,1 Blazor , seguire le istruzioni per la scelta degli strumenti:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul `BlazorSignalRApp.Server` progetto e scegliere **Gestisci pacchetti NuGet**.

1. Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l' **origine del pacchetto** sia impostata su `nuget.org` .

1. Con **Sfoglia** selezionato, digitare `System.Text.Encodings.Web` nella casella di ricerca.

1. Nei risultati della ricerca selezionare il [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacchetto e selezionare **Installa**.

1. Se viene visualizzata la finestra di dialogo **Anteprima modifiche** , selezionare **OK**.

1. Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare Accetto se **si accettano le** condizioni di licenza.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Nel **terminale integrato** (**visualizzare** il > **terminale** dalla barra degli strumenti) eseguire i comandi seguenti:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Nella barra laterale **soluzione** fare clic con il pulsante destro del mouse sul `BlazorSignalRApp.Server` progetto e scegliere **Gestisci pacchetti NuGet**.

1. Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l'elenco a discesa origine sia impostato su `nuget.org` .

1. Con **Sfoglia** selezionato, digitare `System.Text.Encodings.Web` nella casella di ricerca.

1. Nei risultati della ricerca selezionare la casella di controllo accanto al [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacchetto e selezionare **Aggiungi pacchetto**.

1. Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare **Accetto** se si accettano le condizioni di licenza.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

In una shell dei comandi eseguire il comando seguente:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a>Aggiungere un SignalR Hub

Nel `BlazorSignalRApp.Server` progetto creare una `Hubs` cartella (plurale) e aggiungere la classe seguente `ChatHub` ( `Hubs/ChatHub.cs` ):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a>Aggiungere servizi e un endpoint per l' SignalR Hub

1. Nel progetto `BlazorSignalRApp.Server` aprire il file `Startup.cs`.

1. Aggiungere lo spazio dei nomi per la `ChatHub` classe all'inizio del file:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. Aggiungere SignalR e rispondere ai servizi middleware di compressione per `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. In `Startup.Configure`:

   * Usare il middleware della compressione della risposta nella parte superiore della configurazione della pipeline di elaborazione.
   * Tra gli endpoint per i controller e il fallback sul lato client, aggiungere un endpoint per l'hub.

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Aggiungere SignalR e rispondere ai servizi middleware di compressione per `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. In `Startup.Configure`:

   * Usare il middleware della compressione della risposta nella parte superiore della configurazione della pipeline di elaborazione.
   * Tra gli endpoint per i controller e il fallback sul lato client, aggiungere un endpoint per l'hub.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a>Aggiungi Razor codice componente per la chat

1. Nel progetto `BlazorSignalRApp.Client` aprire il file `Pages/Index.razor`.

::: moniker range=">= aspnetcore-5.0"

1. Sostituire il markup con il codice seguente:

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Sostituire il markup con il codice seguente:

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Eseguire l'app

Seguire le istruzioni per gli strumenti:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. In **Esplora soluzioni** selezionare il `BlazorSignalRApp.Server` progetto. Premere <kbd>F5</kbd> per eseguire l'app con il debug o <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app senza debug.

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere browser, immettere un nome e un messaggio e selezionare il pulsante per l'invio del messaggio. Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:

   ![::: NO-LOC (SignalR):::::: NO-LOC (webassembly Blazer)::: app di esempio aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Premere <kbd>F5</kbd> per eseguire l'app con il debug o <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app senza debug.

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere browser, immettere un nome e un messaggio e selezionare il pulsante per l'invio del messaggio. Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:

   ![::: NO-LOC (SignalR):::::: NO-LOC (webassembly Blazer)::: app di esempio aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Nella barra laterale della **soluzione** selezionare il `BlazorSignalRApp.Server` progetto. Premere <kbd>⌘</kbd> + <kbd>↩</kbd> per eseguire l'app con debug o <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> per eseguire l'app senza debug.

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere browser, immettere un nome e un messaggio e selezionare il pulsante per l'invio del messaggio. Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:

   ![::: NO-LOC (SignalR):::::: NO-LOC (webassembly Blazer)::: app di esempio aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

1. In una shell dei comandi eseguire i comandi seguenti:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

1. Scegliere browser, immettere un nome e un messaggio e selezionare il pulsante per l'invio del messaggio. Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:

   ![::: NO-LOC (SignalR):::::: NO-LOC (webassembly Blazer)::: app di esempio aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un Blazor WebAssembly progetto di app ospitata
> * Aggiungere la SignalR libreria client
> * Aggiungere un SignalR Hub
> * Aggiungere SignalR Servizi e un endpoint per l' SignalR Hub
> * Aggiungi Razor codice componente per la chat

Per altre informazioni sulla creazione di Blazor app, vedere la Blazor documentazione:

> [!div class="nextstepaction"]
> <xref:blazor/index>
> [Autenticazione del token di porta con Identity Server, WebSocket ed eventi di Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:signalr/introduction>
* [SignalR negoziazione tra le origini per l'autenticazione](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
