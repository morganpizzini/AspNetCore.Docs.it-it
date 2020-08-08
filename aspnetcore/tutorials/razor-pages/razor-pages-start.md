---
title: 'Esercitazione: Introduzione alle Razor pagine in ASP.NET Core'
author: rick-anderson
description: Questa serie di esercitazioni illustra come usare le Razor pagine in ASP.NET Core. Informazioni su come creare un modello, generare codice per Razor le pagine, usare Entity Framework Core e SQL Server per l'accesso ai dati, aggiungere funzionalità di ricerca, aggiungere la convalida dell'input e usare le migrazioni per aggiornare il modello.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 5fb841de2fa9a04cf05aaf08f255041ee1952638
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021094"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a>Esercitazione: Introduzione alle Razor pagine in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Questa è la prima esercitazione di una serie che illustra le nozioni di base per la creazione di un' Razor app Web di ASP.NET Core Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

Al termine della serie si otterrà un'app che gestisce un database di film.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

In questa esercitazione:

> [!div class="checklist"]
> * Creare un' Razor app Web di pagine.
> * Eseguire l'app.
> * Esaminare i file di progetto.

Al termine di questa esercitazione, si disporrà di un' Razor app Web di pagine di lavoro che verrà compilata nelle esercitazioni successive.

![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>Creare un' Razor app Web di pagine

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.
* Creare una nuova applicazione Web ASP.NET Core e selezionare **Avanti**.
  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Denominare il progetto ** Razor PagesMovie**. È importante denominare il progetto * Razor PagesMovie* in modo che gli spazi dei nomi corrispondano quando si copia e incolla il codice.
  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)

* Selezionare **ASP.NET Core 3,1** nell'elenco a discesa, **applicazione Web**, quindi selezionare **Crea**.

![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  Viene creato il progetto iniziale seguente:

  ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Passare alla directory (`cd`) che conterrà il progetto.

* Eseguire i comandi seguenti:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Il `dotnet new` comando crea un nuovo Razor progetto di pagine nella cartella * Razor PagesMovie* .
  * Il `code` comando apre la cartella * Razor PagesMovie* nell'istanza corrente di Visual Studio Code.

* Dopo che l'icona di OmniSharp Flame della barra di stato è verde, una finestra di dialogo richiede che le **risorse necessarie per la compilazione e il debug siano mancanti da' Razor PagesMovie '. Aggiungerli?** Selezionare **Sì**.

  Una directory *.vscode* che contiene i file *launch.json* e *tasks.json* viene aggiunta alla directory radice del progetto.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Selezionare **File** > **Nuova soluzione**.

  ![Nuova soluzione macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* In Visual Studio per Mac precedente alla versione 8,6 selezionare **.NET Core**  >  **App**  >  **applicazione Web**app .NET Core  >  **Avanti**. Nella versione 8,6 o successive selezionare applicazione Web **e**  >  **app**  >  **Web dell'** app console  >  **successiva**.

  ![selezione del modello di app Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* Nella finestra di dialogo **Configura nuova applicazione Web** :

  * Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.
  * Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 3. x più recente.

  Selezionare **Avanti**.

* Assegnare al progetto il nome ** Razor PagesMovie**e quindi selezionare **Crea**.

  ![macOS nome del progetto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Eseguire l'app

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Esaminare i file di progetto

Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.

### <a name="pages-folder"></a>Cartella Pages

Contiene Razor pagine e file di supporto. Ogni Razor pagina è una coppia di file:

* Un file con *estensione cshtml* che contiene markup HTML con codice C# che utilizza la Razor sintassi.
* Un file con estensione *cshtml.cs* contenente il codice C# per la gestione degli eventi della pagina.

I nomi dei file di supporto iniziano con un carattere di sottolineatura. Ad esempio, il file *_Layout. cshtml* configura gli elementi dell'interfaccia utente comuni a tutte le pagine. Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina. Per altre informazioni, vedere <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Cartella wwwroot

Contiene i file statici, ad esempio i file HTML, i file JavaScript e i file CSS. Per altre informazioni, vedere <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contiene i dati di configurazione, ad esempio le stringhe di connessione. Per altre informazioni, vedere <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contiene il punto di ingresso per il programma. Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Contiene codice che consente di configurare il comportamento dell'app. Per altre informazioni, vedere <xref:fundamentals/startup>.

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva della serie:

> [!div class="step-by-step"]
> [Aggiunta di un modello](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Questa è la prima esercitazione di una serie. [La serie](xref:tutorials/razor-pages/index) illustra le nozioni di base per la creazione di un' Razor app Web di ASP.NET Core Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

Al termine della serie si otterrà un'app che gestisce un database di film.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

In questa esercitazione:

> [!div class="checklist"]
> * Creare un' Razor app Web di pagine.
> * Eseguire l'app.
> * Esaminare i file di progetto.

Al termine di questa esercitazione, si disporrà di un' Razor app Web di pagine di lavoro che verrà compilata nelle esercitazioni successive.

![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>Creare un' Razor app Web di pagine

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.

* Creare una nuova applicazione Web ASP.NET Core e selezionare **Avanti**.

  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Denominare il progetto ** Razor PagesMovie**. È importante denominare il progetto * Razor PagesMovie* in modo che gli spazi dei nomi corrispondano quando si copia e incolla il codice.

  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)

* Selezionare** ASP.NET Core 2.2** nell'elenco a discesa **Applicazione Web** e quindi selezionare **Crea**.

![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  Viene creato il progetto iniziale seguente:

  ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Passare alla directory (`cd`) che conterrà il progetto.

* Eseguire i comandi seguenti:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Il `dotnet new` comando crea un nuovo Razor progetto di pagine nella cartella * Razor PagesMovie* .
  * Il `code` comando apre la cartella * Razor PagesMovie* nell'istanza corrente di Visual Studio Code.

* Dopo che l'icona di OmniSharp Flame della barra di stato è verde, una finestra di dialogo richiede che le **risorse necessarie per la compilazione e il debug siano mancanti da' Razor PagesMovie '. Aggiungerli?** Selezionare **Sì**.

  Una directory *.vscode* che contiene i file *launch.json* e *tasks.json* viene aggiunta alla directory radice del progetto.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Selezionare **File** > **Nuova soluzione**.

![Nuova soluzione macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* In Visual Studio per Mac precedente alla versione 8,6 selezionare **.NET Core**  >  **App**  >  **applicazione Web**app .NET Core  >  **Avanti**. Nella versione 8,6 o successive selezionare applicazione Web **e**  >  **app**  >  **Web dell'** app console  >  **successiva**.

* Nella finestra di dialogo **Configura nuova applicazione Web** :

  * Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.
  * Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 2. x più recente.

  Selezionare **Avanti**.

* Assegnare al progetto il nome ** Razor PagesMovie**e quindi selezionare **Crea**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Eseguire l'app

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Premere CTRL+F5 per l'esecuzione senza il debugger.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app. La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Localhost viene usato solo per le richieste web del computer locale. Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.

* Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.

  Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2.png)

  La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Premere **CTRL+F5** per l'esecuzione senza il debugger.

  Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`. La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Localhost viene usato solo per le richieste web del computer locale.

* Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.

  Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2.png)

  La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Premere **Cmd-Opt-F5** per l'esecuzione senza il debugger.

  Visual Studio avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`.

* Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.

  Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Esaminare i file di progetto

Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.

### <a name="pages-folder"></a>Cartella Pages

Contiene Razor pagine e file di supporto. Ogni Razor pagina è una coppia di file:

* Un file con *estensione cshtml* che contiene markup HTML con codice C# che utilizza la Razor sintassi.
* Un file con estensione *cshtml.cs* contenente il codice C# per la gestione degli eventi della pagina.

I nomi dei file di supporto iniziano con un carattere di sottolineatura. Ad esempio, il file *_Layout. cshtml* configura gli elementi dell'interfaccia utente comuni a tutte le pagine. Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina. Per altre informazioni, vedere <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Cartella wwwroot

Contiene i file statici, ad esempio i file HTML, i file JavaScript e i file CSS. Per altre informazioni, vedere <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contiene i dati di configurazione, ad esempio le stringhe di connessione. Per altre informazioni, vedere <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contiene il punto di ingresso per il programma. Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Contiene il codice che configura il comportamento dell'app, ad esempio se richiede il consenso per cookie s. Per altre informazioni, vedere <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Versione di YouTube di questa esercitazione](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva della serie:

> [!div class="step-by-step"]
> [Aggiunta di un modello](xref:tutorials/razor-pages/model)

::: moniker-end
