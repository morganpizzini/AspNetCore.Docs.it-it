---
title: Inizia a usare ASP.NET Core SignalR
author: bradygaster
description: In questa esercitazione si creerà un'app di chat che usa ASP.NET Core SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
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
uid: tutorials/signalr
ms.openlocfilehash: 1c77648f809562389667da452bdbf3f25f67c558
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689318"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a>Esercitazione: Introduzione a ASP.NET Core SignalR

::: moniker range=">= aspnetcore-3.0"

Questa esercitazione illustra le nozioni di base per la creazione di un'app in tempo reale usando SignalR . Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto Web.
> * Aggiungere la SignalR libreria client.
> * Creare un SignalR Hub.
> * Configurare il progetto da usare SignalR .
> * Aggiungere codice che invia messaggi da qualsiasi client a tutti i client connessi.

Al termine, si disporrà di un'app di chat funzionante:

![App::: NO-LOC (SignalR)::: Sample](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a>Creare un progetto di app Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

  * Nel menu selezionare **File > Nuovo progetto**.
  * Nella finestra di dialogo **Crea un nuovo progetto** selezionare **Applicazione Web ASP.NET Core**, quindi selezionare **Avanti**.
  * Nella finestra di dialogo **Configura il nuovo progetto** denominare il progetto *SignalR chat*, quindi selezionare **Crea**.
  * Nella finestra di dialogo **Crea una nuova applicazione web ASP.NET Core** selezionare **.net Core** e **ASP.NET Core 3,1**.
  * Selezionare **applicazione Web** per creare un progetto che utilizza Razor pagine, quindi selezionare **Crea**.

  ![Finestra di dialogo Nuovo progetto di Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

  * Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal) alla cartella in cui verrà creata la nuova cartella del progetto.
  * Eseguire i comandi seguenti:

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   cd SignalRChat
   code -r .
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

  * Nel menu selezionare **File > Nuova soluzione**.
  * Selezionare **.NET Core > App > Applicazione Web** (Non selezionare **Applicazione Web (Model-View-Controller)**), quindi selezionare **Avanti**.
  * Verificare che il **Framework di destinazione** sia impostato su **.NET Core 3,1**, quindi selezionare **Avanti**.
  * Denominare il progetto *SignalR chat*, quindi selezionare **Crea**.

---

## <a name="add-the-no-locsignalr-client-library"></a>Aggiungere la SignalR libreria client

La SignalR libreria server è inclusa nel Framework condiviso ASP.NET Core 3,1. La libreria client JavaScript non viene inclusa automaticamente nel progetto. Per questa esercitazione, usare Gestione librerie (LibMan) per ottenere la libreria client da *unpkg*. unpkg è una rete per la distribuzione di contenuti (CDN) che può fornire qualsiasi elemento disponibile in NPM, il Node.js gestione pacchetti.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

  * In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **Client-Side Library** (Libreria lato client).
  * Nella finestra di dialogo **Add Client-Side Library** (Aggiungi libreria lato client) selezionare **unpkg** in **Provider**.
  * Per la **Library**, immettere `@microsoft/signalr@latest`.
  * Selezionare **Choose specific files** (Scegli file specifici), espandere la cartella *dist/browser* e selezionare *signalr.js* e *signalr.min.js*.
  * Impostare **percorso di destinazione** su *wwwroot/JS/SignalR/* e selezionare **Installa**.

  ![Finestra di dialogo Add Client-Side Library (Aggiungi libreria lato client) - selezione della libreria](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  LibMan crea una cartella *wwwroot/JS/SignalR* e ne copia i file selezionati.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

  * Eseguire il comando seguente nel terminale integrato per installare LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

  * Eseguire il comando seguente per ottenere la SignalR libreria client usando LibMan. Potrebbe essere necessario attendere alcuni secondi prima di visualizzare l'output.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  I parametri specificano le opzioni seguenti:
  * Usare il provider unpkg.
  * Copiare i file nella destinazione *wwwroot/JS/SignalR* .
  * Copiare solo i file specificati.

  L'output ha un aspetto simile all'esempio seguente:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

  * Nel **Terminale** eseguire il comando seguente per installare LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

  * Passare alla cartella del progetto (quella che contiene il file *SignalR chat. csproj* ).

  * Eseguire il comando seguente per ottenere la SignalR libreria client usando LibMan.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  I parametri specificano le opzioni seguenti:
  * Usare il provider unpkg.
  * Copiare i file nella destinazione *wwwroot/JS/SignalR* .
  * Copiare solo i file specificati.

  L'output ha un aspetto simile all'esempio seguente:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a>Creare un SignalR Hub

Un *hub* è una classe usata come pipeline di alto livello che gestisce le comunicazioni client-server.

  * Nella SignalR cartella del progetto chat creare una cartella *Hub* .
  * Nella cartella *Hubs* creare un file *ChatHub.cs* contenente il codice seguente:

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  La classe `ChatHub` eredita dalla classe SignalR `Hub`. La classe `Hub` gestisce connessioni, gruppi e messaggistica.

  Il metodo `SendMessage` può essere chiamato da un client connesso per inviare un messaggio a tutti i client. Il codice client JavaScript che chiama il metodo è illustrato più avanti nell'esercitazione. SignalR il codice è asincrono per garantire la massima scalabilità.

## <a name="configure-no-locsignalr"></a>ConfigurareSignalR

Il SignalR Server deve essere configurato per il passaggio delle SignalR richieste a SignalR .

* Aggiungere il codice evidenziato seguente al file *Startup.cs*.

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  Queste modifiche aggiungono SignalR ai sistemi di routing e inserimento delle dipendenze ASP.NET Core.

## <a name="add-no-locsignalr-client-code"></a>Aggiungi SignalR codice client

* Sostituire il codice in *Pages\Index.cshtml* con il codice seguente:

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  Il codice precedente:

  * Crea le caselle di testo per il nome e il messaggio di testo, nonché un pulsante di invio.
  * Crea un elenco con `id="messagesList"` per visualizzare i messaggi ricevuti dall' SignalR Hub.
  * Include i riferimenti agli script SignalR e il codice dell'applicazione *chat.js* creato nel passaggio successivo.

* Nella cartella *wwwroot/js* creare un file *chat.js* con il codice seguente:

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  Il codice precedente:

  * Crea e avvia una connessione.
  * Aggiunge al pulsante di invio un gestore che invia messaggi all'hub.
  * Aggiunge all'oggetto connessione un gestore che riceve i messaggi dall'hub e li aggiunge all'elenco.

## <a name="run-the-app"></a>Eseguire l'app

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Premere **CTRL+F5** per eseguire l'app senza debug.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Eseguire il comando seguente nel terminale integrato:

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

  * Nel menu selezionare **Esegui > Avvia senza eseguire debug**.

---

  * Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.
  * Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia messaggio**.
  Nome e messaggio vengono visualizzati immediatamente in entrambe le pagine.

  ![App::: NO-LOC (SignalR)::: Sample](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * Se l'app non funziona, aprire gli strumenti di sviluppo (F12) del browser e passare alla console. È possibile che vengano visualizzati errori correlati al codice HTML e JavaScript. Ad esempio, si supponga di aver inserito *signalr.js* in una cartella diversa da quella indicata nelle istruzioni. In questo caso il riferimento a tale file non funzionerà e verrà visualizzato un errore 404 nella console.
>   ![Errore di signalr.js non trovato](signalr/_static/3.x/f12-console.png)
> * Se si riceve l'errore ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, eseguire questi comandi per aggiornare il certificato di sviluppo:
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Questa esercitazione illustra le nozioni di base per la creazione di un'app in tempo reale usando SignalR . Si apprenderà come: 

> [!div class="checklist"]  
> * Creare un progetto Web.   
> * Aggiungere la SignalR libreria client.   
> * Creare un SignalR Hub. 
> * Configurare il progetto da usare SignalR . 
> * Aggiungere codice che invia messaggi da qualsiasi client a tutti i client connessi.  
Alla fine, si avrà un'app di chat funzionante:::: ![ No-loc (SignalR)::: Sample App](signalr/_static/2.x/signalr-get-started-finished.png)   

## <a name="prerequisites"></a>Prerequisiti    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>Creare un progetto Web 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* Nel menu selezionare **File > Nuovo progetto**. 

* Nella finestra di dialogo **Nuovo progetto** selezionare **Installate > Visual C# > Web > Applicazione Web ASP.NET Core**. Denominare il progetto *SignalR chat*.   

  ![Finestra di dialogo Nuovo progetto di Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* Selezionare **applicazione Web** per creare un progetto che utilizza Razor pagine.   

* Selezionare un framework di destinazione di **.NET Core**, selezionare **ASP.NET Core 2.2** e fare clic su **OK**.    

  ![Finestra di dialogo Nuovo progetto di Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal) alla cartella in cui verrà creata la nuova cartella del progetto.  

* Eseguire i comandi seguenti:   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)   

* Nel menu selezionare **File > Nuova soluzione**.    

* Selezionare **.NET Core > App > App Web ASP.NET Core** (non selezionare **App Web ASP.NET Core (MVC)**).  

* Selezionare **Avanti**.  

* Denominare il progetto *SignalR chat*, quindi selezionare **Crea**. 

--- 

## <a name="add-the-no-locsignalr-client-library"></a>Aggiungere la SignalR libreria client 

La SignalR libreria del server è inclusa nel `Microsoft.AspNetCore.App` metapacchetto. La libreria client JavaScript non viene inclusa automaticamente nel progetto. Per questa esercitazione, usare Gestione librerie (LibMan) per ottenere la libreria client da *unpkg*. unpkg è una rete per la distribuzione di contenuti (CDN) che può fornire qualsiasi elemento disponibile in NPM, il Node.js gestione pacchetti.   

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **Client-Side Library** (Libreria lato client).  

* Nella finestra di dialogo **Add Client-Side Library** (Aggiungi libreria lato client) selezionare **unpkg** in **Provider**. 

* In **Libreria** immettere `@microsoft/signalr@3`e selezionare la versione più recente che non sia di anteprima.  

  ![Finestra di dialogo Add Client-Side Library (Aggiungi libreria lato client) - selezione della libreria](signalr/_static/2.x/libman1.png)   

* Selezionare **Choose specific files** (Scegli file specifici), espandere la cartella *dist/browser* e selezionare *signalr.js* e *signalr.min.js*. 

* Impostare **Percorso di destinazione** su *wwwroot/lib/signalr/* e selezionare **Installa**.    

  ![Finestra di dialogo Add Client-Side Library (Aggiungi libreria lato client) - selezione di file e destinazione](signalr/_static/2.x/libman2.png) 

  LibMan crea una cartella *wwwroot/lib/signalr* e copia i file selezionati in tale cartella.    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Eseguire il comando seguente nel terminale integrato per installare LibMan.  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Eseguire il comando seguente per ottenere la SignalR libreria client usando LibMan. Potrebbe essere necessario attendere alcuni secondi prima di visualizzare l'output. 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  I parametri specificano le opzioni seguenti: 
  * Usare il provider unpkg. 
  * Copiare i file nella destinazione *wwwroot/lib/signalr*.    
  * Copiare solo i file specificati.  

  L'output ha un aspetto simile all'esempio seguente:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)   

* Nel **Terminale** eseguire il comando seguente per installare LibMan. 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Passare alla cartella del progetto (quella che contiene il file *SignalR chat. csproj* ).   

* Eseguire il comando seguente per ottenere la SignalR libreria client usando LibMan.    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  I parametri specificano le opzioni seguenti: 
  * Usare il provider unpkg. 
  * Copiare i file nella destinazione *wwwroot/lib/signalr*.    
  * Copiare solo i file specificati.  

  L'output ha un aspetto simile all'esempio seguente:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a>Creare un SignalR Hub   

Un *hub* è una classe usata come pipeline di alto livello che gestisce le comunicazioni client-server.   

* Nella SignalR cartella del progetto chat creare una cartella *Hub* .  

* Nella cartella *Hubs* creare un file *ChatHub.cs* contenente il codice seguente: 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  La classe `ChatHub` eredita dalla classe SignalR `Hub`. La classe `Hub` gestisce connessioni, gruppi e messaggistica.  

  Il metodo `SendMessage` può essere chiamato da un client connesso per inviare un messaggio a tutti i client. Il codice client JavaScript che chiama il metodo è illustrato più avanti nell'esercitazione. SignalR il codice è asincrono per garantire la massima scalabilità.    

## <a name="configure-no-locsignalr"></a>ConfigurareSignalR  

Il SignalR Server deve essere configurato per il passaggio delle SignalR richieste a SignalR .    

* Aggiungere il codice evidenziato seguente al file *Startup.cs*.  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  Queste modifiche vengono aggiunte SignalR al sistema di inserimento delle dipendenze ASP.NET Core e alla pipeline middleware.  

## <a name="add-no-locsignalr-client-code"></a>Aggiungi SignalR codice client    

* Sostituire il codice in *Pages\Index.cshtml* con il codice seguente:  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  Il codice precedente:   

  * Crea le caselle di testo per il nome e il messaggio di testo, nonché un pulsante di invio.  
  * Crea un elenco con `id="messagesList"` per visualizzare i messaggi ricevuti dall' SignalR Hub.   
  * Include i riferimenti agli script SignalR e il codice dell'applicazione *chat.js* creato nel passaggio successivo.    

* Nella cartella *wwwroot/js* creare un file *chat.js* con il codice seguente:  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  Il codice precedente:   

  * Crea e avvia una connessione.    
  * Aggiunge al pulsante di invio un gestore che invia messaggi all'hub. 
  * Aggiunge all'oggetto connessione un gestore che riceve i messaggi dall'hub e li aggiunge all'elenco.  

## <a name="run-the-app"></a>Eseguire l'app  

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

* Premere **CTRL+F5** per eseguire l'app senza debug.   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Eseguire il comando seguente nel terminale integrato:    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Nel menu selezionare **Esegui > Avvia senza eseguire debug**.

---

* Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.

* Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia messaggio**.  

  Nome e messaggio vengono visualizzati immediatamente in entrambe le pagine.   

  ![App::: NO-LOC (SignalR)::: Sample](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> Se l'app non funziona, aprire gli strumenti di sviluppo (F12) del browser e passare alla console. È possibile che vengano visualizzati errori correlati al codice HTML e JavaScript. Ad esempio, si supponga di aver inserito *signalr.js* in una cartella diversa da quella indicata nelle istruzioni. In questo caso il riferimento a tale file non funzionerà e verrà visualizzato un errore 404 nella console.   
> ![Errore di signalr.js non trovato](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>Risorse aggiuntive 
* [Versione di YouTube di questa esercitazione](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
