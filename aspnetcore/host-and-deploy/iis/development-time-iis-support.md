---
title: Supporto di IIS in fase di sviluppo in Visual Studio per ASP.NET Core
author: rick-anderson
description: Informazioni sul supporto del debug di app ASP.NET Core durante l'esecuzione con IIS in Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 06c215156cdfa9bc1ae6ac2eb21a3f739a309bed
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106780"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Supporto di IIS in fase di sviluppo in Visual Studio per ASP.NET Core

Di [Sourabh Shirhatti](https://twitter.com/sshirhatti)

::: moniker range=">= aspnetcore-3.0"

Questo articolo descrive il supporto del debug di app ASP.NET Core in [Visual Studio](https://visualstudio.microsoft.com) durante l'esecuzione con IIS in Windows Server. Questo argomento illustra nel dettaglio l'abilitazione di questo scenario e la configurazione di un progetto.

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio per Windows](https://visualstudio.microsoft.com/downloads/)
* **ASP.NET e carico di lavoro di sviluppo Web**
* Carico di lavoro di **sviluppo multipiattaforma .NET Core**
* Certificato di sicurezza X.509 (per il supporto di HTTPS)

## <a name="enable-iis"></a>Abilitare IIS

1. In Windows passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità** > **Attiva o disattiva funzionalità di Windows** (sul lato sinistro dello schermo).
1. Selezionare la casella di controllo **Internet Information Services**. Selezionare **OK**.

L'installazione di IIS potrebbe richiedere un riavvio del sistema.

## <a name="configure-iis"></a>Configura IIS

IIS deve disporre di un sito Web configurato con gli elementi seguenti:

* **Nome host**: in genere, il **sito Web predefinito** viene utilizzato con il **nome host** `localhost` . Tuttavia, è appropriato qualsiasi sito Web IIS valido con un nome host univoco.
* **Binding del sito**
  * Per le app che richiedono HTTPS, creare un binding alla porta 443 con un certificato. Viene in genere usato il **certificato di sviluppo di IIS Express**, ma può essere usato qualsiasi certificato valido.
  * Per le app che usano HTTP, verificare l'esistenza di un binding alla porta 80 o creare un binding alla porta 80 per un nuovo sito.
  * Usare un singolo binding per HTTP o HTTPS. **Non è supportato il binding a porte HTTP e HTTPS in contemporanea.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Abilitare il supporto di IIS in fase di sviluppo in Visual Studio

1. Avviare il programma di installazione di Visual Studio.
1. Selezionare **Modifica** per l'installazione di Visual Studio che si intende usare per il supporto di IIS in fase di sviluppo.
1. Per il carico di lavoro **Sviluppo ASP.NET e Web**, individuare e installare il componente **Supporto IIS in fase di sviluppo**.

   Il componente è elencato nella sezione **Facoltativo** in **Supporto IIS in fase di sviluppo** nel pannello **Dettagli di installazione** a destra dei carichi di lavoro. Il componente installa il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), un modulo IIS nativo necessario per l'esecuzione delle applicazioni ASP.NET Core con IIS.

## <a name="configure-the-project"></a>Configurare il progetto

### <a name="https-redirection"></a>Reindirizzamento HTTPS

Per un nuovo progetto che richiede HTTPS, selezionare la casella di controllo **Configura per HTTPS** nella finestra **Crea una nuova applicazione Web ASP.NET Core**. Selezionando questa casella di controllo viene aggiunto il [middleware di reindirizzamento HTTPS e HSTS](xref:security/enforcing-ssl) all'app quando viene creata.

Per un progetto esistente che richiede HTTPS, usare il middleware di reindirizzamento HTTPS e HSTS in `Startup.Configure`. Per altre informazioni, vedere <xref:security/enforcing-ssl>.

Per un progetto che usa HTTP, il [middleware di reindirizzamento HTTPS e HSTS](xref:security/enforcing-ssl) non viene aggiunto all'app. Non è richiesta alcuna configurazione dell'app.

### <a name="iis-launch-profile"></a>Profilo di avvio IIS

Creare un nuovo profilo di avvio per aggiungere il supporto IIS in fase di sviluppo:

1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**. Selezionare **Proprietà**. Aprire la scheda **debug** .
1. Per **Profilo** selezionare il pulsante **Nuovo**. Denominare il profilo "IIS" nella finestra popup. Selezionare **OK** per creare il profilo.
1. Per l'impostazione **Avvio** selezionare **IIS** dall'elenco.
1. Selezionare la casella di controllo **Avvia browser** e specificare l'URL dell'endpoint.

   Quando l'app richiede HTTPS, usare un endpoint HTTPS (`https://`). Per il protocollo HTTP, usare un endpoint HTTP (`http://`).

   Specificare lo stesso nome host e la stessa porta usati nella [configurazione di IIS specificata in precedenza](#configure-iis), in genere `localhost`.

   Specificare il nome dell'app alla fine dell'URL.

   Ad esempio, `https://localhost/WebApplication1` (HTTPS) o `http://localhost/WebApplication1` (HTTP) sono URL di endpoint validi.
1. Nella sezione **Variabili di ambiente** selezionare il pulsante **Aggiungi**. Specificare una variabile di ambiente con **Nome**`ASPNETCORE_ENVIRONMENT` e **Valore**`Development`.
1. Nell'area **Impostazioni server Web** impostare **URL app** sullo stesso valore usato per l'URL dell'endpoint **Avvia browser**.
1. Per l'impostazione **Modello di hosting** in Visual Studio 2019 o versione successiva, selezionare **Predefinito** per usare il modello di hosting usato dal progetto. Se il progetto imposta la proprietà `<AspNetCoreHostingModel>` nel file di progetto, viene usato il valore della proprietà (`InProcess` o `OutOfProcess`). Se la proprietà non è presente, viene usato il modello di hosting predefinito dell'app, ovvero in-process. Se l'app richiede l'impostazione esplicita di un modello di hosting diverso dal modello di hosting normale dell'app, impostare **Modello di hosting** su `In Process` o `Out Of Process` in base alle esigenze.
1. Salvare il profilo.

Se non si usa Visual Studio, aggiungere manualmente un profilo di avvio al file [launchSettings.json](https://json.schemastore.org/launchsettings) nella cartella *Proprietà*. L'esempio seguente configura il profilo per usare il protocollo HTTPS:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Verificare che gli endpoint `applicationUrl` e `launchUrl` corrispondano e usare lo stesso protocollo della configurazione del binding IIS, ovvero HTTP o HTTPS.

## <a name="run-the-project"></a>Eseguire il progetto

Eseguire Visual Studio come amministratore:

* Verificare che l'elenco di riepilogo a discesa della configurazione della build sia impostato su **Debug**.
* Impostare il [pulsante Avvia debug](/visualstudio/debugger/debugger-feature-tour) sul profilo **IIS** e selezionare il pulsante per avviare l'app.

Visual Studio potrebbe richiedere un riavvio, se non si è in modalità amministratore. In tal caso riavviare Visual Studio.

Se viene usato un certificato di sviluppo non attendibile, il browser potrebbe richiedere di creare un'eccezione per il certificato non attendibile.

> [!NOTE]
> Il debug della configurazione della build Versione con [Just My Code](/visualstudio/debugger/just-my-code) e le ottimizzazioni del compilatore genera un'esperienza con funzionalità ridotta. Ad esempio, i punti di interruzione non vengono raggiunti.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8) (Introduzione a Gestione IIS in IIS)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Questo articolo descrive il supporto del debug di app ASP.NET Core in [Visual Studio](https://visualstudio.microsoft.com) durante l'esecuzione con IIS in Windows Server. Questo argomento illustra nel dettaglio l'abilitazione di questo scenario e la configurazione di un progetto.

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio per Windows](https://visualstudio.microsoft.com/downloads/)
* **ASP.NET e carico di lavoro di sviluppo Web**
* Carico di lavoro di **sviluppo multipiattaforma .NET Core**
* Certificato di sicurezza X.509 (per il supporto di HTTPS)

## <a name="enable-iis"></a>Abilitare IIS

1. In Windows passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità** > **Attiva o disattiva funzionalità di Windows** (sul lato sinistro dello schermo).
1. Selezionare la casella di controllo **Internet Information Services**. Selezionare **OK**.

L'installazione di IIS potrebbe richiedere un riavvio del sistema.

## <a name="configure-iis"></a>Configura IIS

IIS deve disporre di un sito Web configurato con gli elementi seguenti:

* **Nome host**: in genere, il **sito Web predefinito** viene utilizzato con il **nome host** `localhost` . Tuttavia, è appropriato qualsiasi sito Web IIS valido con un nome host univoco.
* **Binding del sito**
  * Per le app che richiedono HTTPS, creare un binding alla porta 443 con un certificato. Viene in genere usato il **certificato di sviluppo di IIS Express**, ma può essere usato qualsiasi certificato valido.
  * Per le app che usano HTTP, verificare l'esistenza di un binding alla porta 80 o creare un binding alla porta 80 per un nuovo sito.
  * Usare un singolo binding per HTTP o HTTPS. **Non è supportato il binding a porte HTTP e HTTPS in contemporanea.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Abilitare il supporto di IIS in fase di sviluppo in Visual Studio

1. Avviare il programma di installazione di Visual Studio.
1. Selezionare **Modifica** per l'installazione di Visual Studio che si intende usare per il supporto di IIS in fase di sviluppo.
1. Per il carico di lavoro **Sviluppo ASP.NET e Web**, individuare e installare il componente **Supporto IIS in fase di sviluppo**.

   Il componente è elencato nella sezione **Facoltativo** in **Supporto IIS in fase di sviluppo** nel pannello **Dettagli di installazione** a destra dei carichi di lavoro. Il componente installa il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), un modulo IIS nativo necessario per l'esecuzione delle applicazioni ASP.NET Core con IIS.

## <a name="configure-the-project"></a>Configurare il progetto

### <a name="https-redirection"></a>Reindirizzamento HTTPS

Per un nuovo progetto che richiede HTTPS, selezionare la casella di controllo **Configura per HTTPS** nella finestra **Crea una nuova applicazione Web ASP.NET Core**. Selezionando questa casella di controllo viene aggiunto il [middleware di reindirizzamento HTTPS e HSTS](xref:security/enforcing-ssl) all'app quando viene creata.

Per un progetto esistente che richiede HTTPS, usare il middleware di reindirizzamento HTTPS e HSTS in `Startup.Configure`. Per altre informazioni, vedere <xref:security/enforcing-ssl>.

Per un progetto che usa HTTP, il [middleware di reindirizzamento HTTPS e HSTS](xref:security/enforcing-ssl) non viene aggiunto all'app. Non è richiesta alcuna configurazione dell'app.

### <a name="iis-launch-profile"></a>Profilo di avvio IIS

Creare un nuovo profilo di avvio per aggiungere il supporto IIS in fase di sviluppo:

1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**. Selezionare **Proprietà**. Aprire la scheda **debug** .
1. Per **Profilo** selezionare il pulsante **Nuovo**. Denominare il profilo "IIS" nella finestra popup. Selezionare **OK** per creare il profilo.
1. Per l'impostazione **Avvio** selezionare **IIS** dall'elenco.
1. Selezionare la casella di controllo **Avvia browser** e specificare l'URL dell'endpoint.

   Quando l'app richiede HTTPS, usare un endpoint HTTPS (`https://`). Per il protocollo HTTP, usare un endpoint HTTP (`http://`).

   Specificare lo stesso nome host e la stessa porta usati nella [configurazione di IIS specificata in precedenza](#configure-iis), in genere `localhost`.

   Specificare il nome dell'app alla fine dell'URL.

   Ad esempio, `https://localhost/WebApplication1` (HTTPS) o `http://localhost/WebApplication1` (HTTP) sono URL di endpoint validi.
1. Nella sezione **Variabili di ambiente** selezionare il pulsante **Aggiungi**. Specificare una variabile di ambiente con **Nome**`ASPNETCORE_ENVIRONMENT` e **Valore**`Development`.
1. Nell'area **Impostazioni server Web** impostare **URL app** sullo stesso valore usato per l'URL dell'endpoint **Avvia browser**.
1. Per l'impostazione **Modello di hosting** in Visual Studio 2019 o versione successiva, selezionare **Predefinito** per usare il modello di hosting usato dal progetto. Se il progetto imposta la proprietà `<AspNetCoreHostingModel>` nel file di progetto, viene usato il valore della proprietà (`InProcess` o `OutOfProcess`). Se la proprietà non è presente, viene usato il modello di hosting predefinito dell'app, ovvero out-of-process. Se l'app richiede l'impostazione esplicita di un modello di hosting diverso dal modello di hosting normale dell'app, impostare **Modello di hosting** su `In Process` o `Out Of Process` in base alle esigenze.
1. Salvare il profilo.

Se non si usa Visual Studio, aggiungere manualmente un profilo di avvio al file [launchSettings.json](https://json.schemastore.org/launchsettings) nella cartella *Proprietà*. L'esempio seguente configura il profilo per usare il protocollo HTTPS:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Verificare che gli endpoint `applicationUrl` e `launchUrl` corrispondano e usare lo stesso protocollo della configurazione del binding IIS, ovvero HTTP o HTTPS.

## <a name="run-the-project"></a>Eseguire il progetto

Eseguire Visual Studio come amministratore:

* Verificare che l'elenco di riepilogo a discesa della configurazione della build sia impostato su **Debug**.
* Impostare il [pulsante Avvia debug](/visualstudio/debugger/debugger-feature-tour) sul profilo **IIS** e selezionare il pulsante per avviare l'app.

Visual Studio potrebbe richiedere un riavvio, se non si è in modalità amministratore. In tal caso riavviare Visual Studio.

Se viene usato un certificato di sviluppo non attendibile, il browser potrebbe richiedere di creare un'eccezione per il certificato non attendibile.

> [!NOTE]
> Il debug della configurazione della build Versione con [Just My Code](/visualstudio/debugger/just-my-code) e le ottimizzazioni del compilatore genera un'esperienza con funzionalità ridotta. Ad esempio, i punti di interruzione non vengono raggiunti.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8) (Introduzione a Gestione IIS in IIS)
* <xref:security/enforcing-ssl>

::: moniker-end
