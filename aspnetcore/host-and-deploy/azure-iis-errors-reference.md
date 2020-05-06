---
title: Errori comuni di Servizio app di Azure e IIS con ASP.NET Core
author: rick-anderson
description: Ottenere suggerimenti per la risoluzione degli errori comuni dell'hosting di app ASP.NET Core in Servizio app di Azure e IIS.
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
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 7b3454fbd891ca26d44125810a10eb3b3c2c3933
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775206"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>Errori comuni di Servizio app di Azure e IIS con ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

Questo argomento descrive gli errori comuni e fornisce consigli per la risoluzione di errori specifici quando si ospitano ASP.NET Core app nel servizio app di Azure e in IIS.

Per indicazioni generali sulla risoluzione dei problemi <xref:test/troubleshoot-azure-iis>, vedere.

Raccogliere le seguenti informazioni:

* Comportamento del browser (codice di stato e messaggio di errore)
* Voci del log eventi dell'applicazione
  * Servizio app di Azure &ndash; Vedere <xref:test/troubleshoot-azure-iis>.
  * IIS
    1. Selezionare **Start** nel menu di **Windows**, digitare *Visualizzatore eventi* e premere **INVIO**.
    1. Dopo l'apertura di **Visualizzatore eventi**, espandere **Registri di Windows** > **Applicazione** nella barra laterale.
* Voci del log per debug e stdout di ASP.NET Core
  * Servizio app di Azure &ndash; Vedere <xref:test/troubleshoot-azure-iis>.
  * IIS &ndash; Seguire le istruzioni nelle sezioni [Creazione e reindirizzamento dei log](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) e [Log di diagnostica avanzati](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) dell'argomento Modulo ASP.NET Core.

Confrontare le informazioni sugli errori con gli errori comuni seguenti. Se viene trovata una corrispondenza, seguire le indicazioni per la risoluzione dei problemi.

L'elenco degli errori in questo argomento non è esaustivo. Se si verifica un errore non elencato qui, aprire un nuovo problema tramite il pulsante per l'invio di **feedback per il contenuto** nella parte inferiore di questo argomento con istruzioni dettagliate su come riprodurre l'errore.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>L'aggiornamento del sistema operativo ha rimosso il modulo di ASP.NET Core a 32 bit

**Registro dell'applicazione:** Il modulo DLL**C:\WINDOWS\system32\inetsrv\aspnetcore.dll** non è riuscito a caricare. L'errore è nei dati.

Risoluzione dei problemi:

I file non appartenenti al sistema operativo presenti nella directory **C:\Windows\SysWOW64\inetsrv** non vengono mantenuti durante un aggiornamento del sistema operativo. Se il modulo ASP.NET Core viene installato prima di un aggiornamento del sistema operativo e quindi si esegue qualsiasi pool di app in modalità a 32 bit dopo l'aggiornamento, si verifica questo problema. Dopo un aggiornamento del sistema operativo, ripristinare il Modulo di ASP.NET Core. Vedere [installare il bundle di hosting .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Selezionare **Ripara** quando viene eseguito il programma di installazione.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Estensione del sito mancante, estensioni del sito a 32 bit (x86) e a 64 bit (x64) installate o numero di bit per il processo impostato errato

*Si applica alle app ospitate da Servizi app di Azure.*

* **Browser:** Errore HTTP 500,0-errore di caricamento del gestore in-Process modulo ASP.NET Core

* **Registro applicazioni:** La chiamata di hostfxr per trovare il gestore della richiesta di Inprocess non è riuscita senza trovare dipendenze native. Non è stato possibile trovare il gestore delle richieste In-Process. Output acquisito dalla chiamata a hostfxr: non è possibile trovare una versione di Framework compatibile. Impossibile trovare la versione del framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' specificata. Impossibile avviare l'applicazione '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.

* **Log stdout del modulo ASP.NET Core:** Non è stato possibile trovare una versione di Framework compatibile. Impossibile trovare la versione del framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' specificata.

* **Log di debug del modulo ASP.NET Core:** La chiamata di hostfxr per trovare il gestore della richiesta di Inprocess non è riuscita senza trovare dipendenze native. Questo errore probabilmente significa che l'app non è configurata correttamente. Controllare le versioni di Microsoft.NetCore.App e Microsoft.AspNetCore.App specificate come destinazione dall'applicazione e installate nel computer. Errore HRESULT restituito: 0x8000ffff. Non è stato possibile trovare il gestore delle richieste In-Process. Non è stato possibile trovare qualsiasi versione del framework compatibile. Impossibile trovare la versione del framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' specificata.

Risoluzione dei problemi:

* Se l'app è in esecuzione in un runtime di anteprima, installare l'estensione del sito a 32 bit (x86) **o** a 64 bit (x64) corrispondente al numero di bit dell'app e alla versione del runtime dell'app. **Non installare entrambe le estensioni o più versioni di runtime dell'estensione.**

  * Runtime ASP.NET Core {RUNTIME VERSION} (x86)
  * Runtime ASP.NET Core {RUNTIME VERSION} (x64)

  Riavviare l'app. Attendere alcuni secondi per il riavvio dell'app.

* Se l'app è in esecuzione in un runtime di anteprima e sono installate entrambe le [estensioni del sito](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) a 32 bit (x86) e a 64 bit (x64), disinstallare l'estensione del sito che non corrisponde al numero di bit dell'app. Dopo aver rimosso l'estensione del sito, riavviare l'app. Attendere alcuni secondi per il riavvio dell'app.

* Se l'app è in esecuzione in un runtime di anteprima e il numero di bit dell'estensione del sito corrisponde a quello dell'app, verificare che la *versione del runtime* dell'estensione del sito di anteprima corrisponda alla versione del runtime dell'app.

* Verificare che la **piattaforma** dell'app in **Impostazioni applicazione** corrisponda al numero di bit dell'app.

Per altre informazioni, vedere <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Un'app x86 viene distribuita, ma il pool di app non è abilitato per le app a 32 bit

* **Browser:** Errore HTTP 500,30-errore di avvio in-Process modulo ASP.NET Core

* **Registro applicazioni:** L'applicazione '/LM/W3SVC/5/ROOT ' con radice fisica ' {PATH}' ha raggiunto un'eccezione gestita imprevista, codice eccezione =' 0xe0434352'. Controllare i log stderr per altre informazioni. Applicazione '/LM/W3SVC/5/ROOT' con radice fisica '{PATH}'. Impossibile caricare clr e applicazione gestita. Chiusura prematura del thread di lavoro CLR

* **Log stdout del modulo ASP.NET Core:** Il file di log viene creato, ma vuoto.

* **Log di debug del modulo ASP.NET Core:** HRESULT restituito non riuscito: 0x8007023e

Questo scenario viene intercettato dall'SDK durante la pubblicazione di un'app autonoma. L'SDK genera un errore se il RID non corrisponde alla piattaforma di destinazione (ad esempio, RID `win10-x64` con `<PlatformTarget>x86</PlatformTarget>` nel file di progetto).

Risoluzione dei problemi:

Per una distribuzione dipendente dal framework x86 (`<PlatformTarget>x86</PlatformTarget>`), abilitare il pool di app IIS per le app a 32 bit. In Gestione IIS aprire **Impostazioni avanzate** per il pool di app e impostare **Attiva applicazioni a 32 bit** su **True**.

## <a name="platform-conflicts-with-rid"></a>La piattaforma è in conflitto con RID

* **Browser:** errore HTTP 502.5 - errore del processo

* **Registro applicazioni:** L'applicazione "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}" con radice fisica "C\{: Path\' } non è riuscita ad avviare il processo con la\{riga di comando" "C: Path} {assembly}. {exe | dll} "', ErrorCode =' 0x80004005: FF.

* **Log stdout del modulo ASP.NET Core:** Eccezione non gestita: System. BadImageFormatException: Impossibile caricare il file o l'assembly ' {ASSEMBLY}. dll '. Tentativo di caricare un programma con un formato non corretto.

Risoluzione dei problemi:

* Confermare che l'app sia eseguita in locale su Kestrel. Un errore del processo può essere causato da un problema interno dell'applicazione. Per altre informazioni, vedere <xref:test/troubleshoot-azure-iis>.

* Se questa eccezione si verifica per una distribuzione di App di Azure durante l'aggiornamento di un'app e la distribuzione di assembly più recenti, eliminare manualmente tutti i file dalla distribuzione precedente. Le assembly incompatibili con il tempo di ritardo possono causare una eccezione `System.BadImageFormatException` quando si distribuisce un'app aggiornata.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Endpoint dell'URI non corretto o sito web arrestato

* **Browser:** ERR_CONNECTION_REFUSED **--o--** non è possibile connettersi

* **Registro dell'applicazione:** Nessuna voce

* **Log stdout del modulo ASP.NET Core:** Il file di log non viene creato.

* **Log di debug del modulo ASP.NET Core:** Il file di log non viene creato.

Risoluzione dei problemi:

* Verificare che sia in uso l'endpoint URI corretto per l'app. Controllare i binding.

* Verificare che il sito Web IIS non sia nello stato *In arresto*.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Le funzionalità del server CoreWebEngine o W3SVC sono disabilitate

**Eccezione del sistema operativo:** per usare il modulo di ASP.NET Core è necessario installare le funzionalità di IIS 7.0 CoreWebEngine e W3SVC.

Risoluzione dei problemi:

Verificare che il ruolo e le funzionalità appropriati siano abilitati. Vedere [Configurazione di IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Percorso fisico del sito Web non corretto o app mancante

* **Browser:** 403 Non consentito: accesso negato **- OPPURE -** 403.14 Non consentito - Il server Web è configurato per non visualizzare il contenuto di questa directory.

* **Registro dell'applicazione:** Nessuna voce

* **Log stdout del modulo ASP.NET Core:** Il file di log non viene creato.

* **Log di debug del modulo ASP.NET Core:** Il file di log non viene creato.

Risoluzione dei problemi:

Controllare il sito Web IIS **Impostazioni di base** e la cartella dell'app fisica. Verificare che l'app si trovi nella cartella nel sito Web IIS **Percorso fisico**.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Ruolo non corretto, modulo ASP.NET Core non installato o autorizzazioni non corrette

* **Browser:** 500.19 Errore interno al server – non è possibile accedere alla pagina richiesta perché i dati di configurazione per la pagina non sono validi. **--Oppure--** Non è possibile visualizzare questa pagina

* **Registro dell'applicazione:** Nessuna voce

* **Log stdout del modulo ASP.NET Core:** Il file di log non viene creato.

* **Log di debug del modulo ASP.NET Core:** Il file di log non viene creato.

Risoluzione dei problemi:

* Verificare che il ruolo appropriato sia abilitato. Vedere [Configurazione di IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Aprire **Programmi e funzionalità** oppure **App e funzionalità** e verificare che sia installato **Windows Server Hosting**. Se **Windows Server Hosting** non è presente nell'elenco dei programmi installati, scaricare e installare il bundle di hosting .NET Core.

  [Programma di installazione del bundle di hosting .NET Core corrente (download diretto)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Per altre informazioni, vedere [Installare il bundle di hosting .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Verificare che il **modello** > **Identity** di processo del **pool** > di applicazioni sia impostato su **ApplicationPoolIdentity** o che l'identità personalizzata disponga delle autorizzazioni corrette per accedere alla cartella di distribuzione dell'app.

* Se il bundle di hosting ASP.NET Core è stato disinstallato e quindi è stata installata una versione del bundle di hosting precedente, il file *applicationHost.config* non include una sezione per il modulo ASP.NET Core. Aprire *applicationHost.config* in *%windir%/System32/inetsrv/config* e trovare il gruppo di sezioni `<configuration><configSections><sectionGroup name="system.webServer">`. Se la sezione per il modulo ASP.NET Core non è presente nel gruppo di sezioni, aggiungere l'elemento della sezione:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  In alternativa installare la versione più recente del bundle di hosting ASP.NET Core. La versione più recente è compatibile con le app ASP.NET Core supportate.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>ProcessPath non corretto, variabile di percorso mancante, aggregazione di hosting non installata, sistema/IIS non riavviato, VC Redistributable non installato o violazione dell'accesso a dotnet.exe

* **Browser:** Errore HTTP 500,0-errore di caricamento del gestore in-Process modulo ASP.NET Core

* **Registro applicazioni:** L'applicazione "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}" con radice fisica "C\{: Path\' } non è riuscita ad avviare il processo con la riga di comando" "{...}" ', ErrorCode =' 0x80070002:0. Impossibile avviare l'applicazione '{PATH}'. Eseguibile non trovato in '{PATH}'. Impossibile avviare l'applicazione '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.

* **Log stdout del modulo ASP.NET Core:** Il file di log non viene creato.

* **Log di debug del modulo ASP.NET Core:** Log eventi: Impossibile avviare ' applicazione ' {PATH}'. Eseguibile non trovato in '{PATH}'. HRESULT restituito non riuscito: 0x8007023e

Risoluzione dei problemi:

* Confermare che l'app sia eseguita in locale su Kestrel. Un errore del processo può essere causato da un problema interno dell'applicazione. Per altre informazioni, vedere <xref:test/troubleshoot-azure-iis>.

* Verificare che l'attributo *processPath* dell'elemento `<aspNetCore>` in *web.config* sia `dotnet` per una distribuzione dipendente da framework (FDD, Framework-Dependent Deployment) o `.\{ASSEMBLY}.exe` per una [distribuzione autonoma (SCD, Self-Contained Deployment)](/dotnet/core/deploying/#self-contained-deployments-scd).

* Per una distribuzione dipendente dal framework, *dotnet.exe* potrebbe non essere accessibile tramite le impostazioni del percorso. Confermare che *C:\Programmi\dotnet\\* esiste nelle impostazioni PATH di sistema.

* Per una distribuzione dipendente da framework, *dotnet.exe* potrebbe non essere accessibile per l'identità utente del pool di app. Confermare che l'identità dell'utente del pool di app abbia accesso alla directory *C:\Programmi\dotnet*. Verificare che non siano presenti regole di negazione configurate per l'identità dell'utente del pool di app in *C:\Programmi\dotnet* e nelle directory dell'app.

* È possibile che sia stata eseguita una distribuzione FDD e che sia stato installato .NET Core senza riavviare IIS. Riavviare il server o riavviare IIS eseguendo **net stop was /y** seguito da **net start w3svc** da un prompt dei comandi.

* È possibile che sia stata eseguita una distribuzione FDD senza installare il runtime .NET Core nel sistema host. Se il runtime .NET Core non è stato installato, eseguire il **programma di installazione del bundle di hosting .NET Core** nel sistema.

  [Programma di installazione del bundle di hosting .NET Core corrente (download diretto)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Per altre informazioni, vedere [Installare il bundle di hosting .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Se è necessario un runtime specifico, scaricare il runtime dagli [archivi di download .NET](https://dotnet.microsoft.com/download/archives) e installarlo nel sistema. Completare l'installazione riavviando il sistema o riavviando IIS eseguendo **net stop was /y** seguito da **net start w3svc** da un prompt dei comandi.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Argomenti non corretti dell'elemento \<aspNetCore>

* **Browser:** Errore HTTP 500,0-errore di caricamento del gestore in-Process modulo ASP.NET Core

* **Registro applicazioni:** La chiamata di hostfxr per trovare il gestore della richiesta di Inprocess non è riuscita senza trovare dipendenze native. Questo errore probabilmente significa che l'app non è configurata correttamente. Controllare le versioni di Microsoft.NetCore.App e Microsoft.AspNetCore.App specificate come destinazione dall'applicazione e installate nel computer. Non è stato possibile trovare il gestore delle richieste In-Process. Output acquisito dalla chiamata a hostfxr: si intendeva eseguire i comandi di DotNet SDK? Installare dotnet SDK da: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 non è stato possibile avviare l'applicazione '/LM/W3SVC/3/root ', ErrorCode ' 0x8000ffff '.

* **Log stdout del modulo ASP.NET Core:** Si intendeva eseguire i comandi di DotNet SDK? Installare dotnet SDK da: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409

* **Log di debug del modulo ASP.NET Core:** La chiamata di hostfxr per trovare il gestore della richiesta di Inprocess non è riuscita senza trovare dipendenze native. Questo errore probabilmente significa che l'app non è configurata correttamente. Controllare le versioni di Microsoft.NetCore.App e Microsoft.AspNetCore.App specificate come destinazione dall'applicazione e installate nel computer. Restituito HRESULT non riuscito: 0x8000ffff non è in grado di trovare il gestore della richiesta di InProcess. Output acquisito dalla chiamata a hostfxr: si intendeva eseguire i comandi di DotNet SDK? Installare dotnet SDK da: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 HRESULT non riuscito restituito: 0x8000ffff

Risoluzione dei problemi:

* Confermare che l'app sia eseguita in locale su Kestrel. Un errore del processo può essere causato da un problema interno dell'applicazione. Per altre informazioni, vedere <xref:test/troubleshoot-azure-iis>.

* Verificare che l'attributo *arguments* dell'elemento `<aspNetCore>` in *web.config* sia (a) `.\{ASSEMBLY}.dll` per una distribuzione dipendente da framework (FDD) o (b) non disponibile, una stringa vuota (`arguments=""`) o un elenco degli argomenti dell'app (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) per una distribuzione autonoma (SCD).

## <a name="missing-net-core-shared-framework"></a>Framework condiviso di .NET Core mancante

* **Browser:** Errore HTTP 500,0-errore di caricamento del gestore in-Process modulo ASP.NET Core

* **Registro applicazioni:** La chiamata di hostfxr per trovare il gestore della richiesta di Inprocess non è riuscita senza trovare dipendenze native. Questo errore probabilmente significa che l'app non è configurata correttamente. Controllare le versioni di Microsoft.NetCore.App e Microsoft.AspNetCore.App specificate come destinazione dall'applicazione e installate nel computer. Non è stato possibile trovare il gestore delle richieste In-Process. Output acquisito dalla chiamata a hostfxr: non è possibile trovare una versione di Framework compatibile. Impossibile trovare la versione del framework 'Microsoft.AspNetCore.App' specificata '{VERSION}'.

Impossibile avviare l'applicazione '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.

* **Log stdout del modulo ASP.NET Core:** Non è stato possibile trovare una versione di Framework compatibile. Impossibile trovare la versione del framework 'Microsoft.AspNetCore.App' specificata '{VERSION}'.

* **Log di debug del modulo ASP.NET Core:** HRESULT restituito non riuscito: 0x8000ffff

Risoluzione dei problemi:

Per una distribuzione dipendente da framework (FDD), verificare che nel sistema sia installato il runtime corretto.

## <a name="stopped-application-pool"></a>Pool di applicazioni arrestato

* **Browser:** 503 Servizio non disponibile

* **Registro dell'applicazione:** Nessuna voce

* **Log stdout del modulo ASP.NET Core:** Il file di log non viene creato.

* **Log di debug del modulo ASP.NET Core:** Il file di log non viene creato.

Risoluzione dei problemi:

Confermare che il pool di applicazioni non sia nello stato *Arrestato*.

## <a name="sub-application-includes-a-handlers-section"></a>L'app secondaria include una sezione \<handlers>

* **Browser:** errore HTTP 500.19 - errore del server interno

* **Registro dell'applicazione:** Nessuna voce

* **Log stdout del modulo ASP.NET Core:** Il file di log dell'app radice viene creato e Mostra il normale funzionamento. Il file di log dell'app secondaria non viene creato.

* **Log di debug del modulo ASP.NET Core:** Il file di log dell'app radice viene creato e Mostra il normale funzionamento. Il file di log dell'app secondaria non viene creato.

Risoluzione dei problemi:

Verificare che il file *web.config* dell'app secondaria non includa una sezione `<handlers>` o che l'app secondaria non erediti i gestori dell'app padre.

La sezione `<system.webServer>` dell'app padre di *web.config* viene inserita all'interno di un elemento `<location>`. La <xref:System.Configuration.SectionInformation.InheritInChildApplications*> proprietà è impostata su `false` per indicare che le impostazioni specificate all'interno della [ \<posizione>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemento non vengono ereditate dalle app che si trovano in una sottodirectory dell'app padre. Per altre informazioni, vedere <xref:host-and-deploy/aspnet-core-module>.

## <a name="stdout-log-path-incorrect"></a>percorso errato del log stdout

* **Browser:** l'app risponde normalmente.

* **Registro applicazioni:** Non è stato possibile avviare il Reindirizzamento stdout in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Messaggio eccezione: HRESULT 0x80070005 restituito in {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84. Non è possibile arrestare il reindirizzamento di stdout in C:\Programmi\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Messaggio di eccezione: HRESULT 0x80070002 restituito in {PATH}. Non è possibile avviare il reindirizzamento di stdout in {PATH}\aspnetcorev2_inprocess.dll.

* **Log stdout del modulo ASP.NET Core:** Il file di log non viene creato.

* **Log di debug del modulo ASP.NET Core:** Non è stato possibile avviare il Reindirizzamento stdout in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Messaggio eccezione: HRESULT 0x80070005 restituito in {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84. Non è possibile arrestare il reindirizzamento di stdout in C:\Programmi\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Messaggio di eccezione: HRESULT 0x80070002 restituito in {PATH}. Non è possibile avviare il reindirizzamento di stdout in {PATH}\aspnetcorev2_inprocess.dll.

Risoluzione dei problemi:

* Il percorso `stdoutLogFile` specificato nell'elemento `<aspNetCore>` di *web.config* non esiste. Per ulteriori informazioni, vedere [ASP.NET Core Module: creazione e Reindirizzamento dei log](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* L'utente del pool di app non ha accesso in scrittura al percorso del log di stdout.

## <a name="application-configuration-general-issue"></a>Problema generale della configurazione dell'applicazione

* **Browser:** Errore HTTP 500,0-errore di caricamento del gestore in-Process modulo ASP.NET Core **--o--** errore HTTP 500,30-errore di avvio in-Process modulo ASP.NET Core

* **Registro applicazioni:** Variabile

* **Log stdout del modulo ASP.NET Core:** Il file di log viene creato ma vuoto o creato con le voci normali fino a quando il punto dell'app non riesce.

* **Log di debug del modulo ASP.NET Core:** Variabile

Risoluzione dei problemi:

non è stato possibile avviare il processo, molto probabilmente a causa di un problema di configurazione dell'app o di programmazione.

Per altre informazioni, vedere i seguenti argomenti:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Questo argomento descrive gli errori comuni e fornisce consigli per la risoluzione di errori specifici quando si ospitano ASP.NET Core app nel servizio app di Azure e in IIS.

Per indicazioni generali sulla risoluzione dei problemi <xref:test/troubleshoot-azure-iis>, vedere.

Raccogliere le seguenti informazioni:

* Comportamento del browser (codice di stato e messaggio di errore)
* Voci del log eventi dell'applicazione
  * Servizio app di Azure &ndash; Vedere <xref:test/troubleshoot-azure-iis>.
  * IIS
    1. Selezionare **Start** nel menu di **Windows**, digitare *Visualizzatore eventi* e premere **INVIO**.
    1. Dopo l'apertura di **Visualizzatore eventi**, espandere **Registri di Windows** > **Applicazione** nella barra laterale.
* Voci del log per debug e stdout di ASP.NET Core
  * Servizio app di Azure &ndash; Vedere <xref:test/troubleshoot-azure-iis>.
  * IIS &ndash; Seguire le istruzioni nelle sezioni [Creazione e reindirizzamento dei log](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) e [Log di diagnostica avanzati](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) dell'argomento Modulo ASP.NET Core.

Confrontare le informazioni sugli errori con gli errori comuni seguenti. Se viene trovata una corrispondenza, seguire le indicazioni per la risoluzione dei problemi.

L'elenco degli errori in questo argomento non è esaustivo. Se si verifica un errore non elencato qui, aprire un nuovo problema tramite il pulsante per l'invio di **feedback per il contenuto** nella parte inferiore di questo argomento con istruzioni dettagliate su come riprodurre l'errore.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>L'aggiornamento del sistema operativo ha rimosso il modulo di ASP.NET Core a 32 bit

**Registro dell'applicazione:** Il modulo DLL**C:\WINDOWS\system32\inetsrv\aspnetcore.dll** non è riuscito a caricare. L'errore è nei dati.

Risoluzione dei problemi:

I file non appartenenti al sistema operativo presenti nella directory **C:\Windows\SysWOW64\inetsrv** non vengono mantenuti durante un aggiornamento del sistema operativo. Se il modulo ASP.NET Core viene installato prima di un aggiornamento del sistema operativo e quindi si esegue qualsiasi pool di app in modalità a 32 bit dopo l'aggiornamento, si verifica questo problema. Dopo un aggiornamento del sistema operativo, ripristinare il Modulo di ASP.NET Core. Vedere [installare il bundle di hosting .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Selezionare **Ripara** quando viene eseguito il programma di installazione.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Estensione del sito mancante, estensioni del sito a 32 bit (x86) e a 64 bit (x64) installate o numero di bit per il processo impostato errato

*Si applica alle app ospitate da Servizi app di Azure.*

* **Browser:** Errore HTTP 500,0-errore di caricamento del gestore in-Process modulo ASP.NET Core

* **Registro applicazioni:** La chiamata di hostfxr per trovare il gestore della richiesta di Inprocess non è riuscita senza trovare dipendenze native. Non è stato possibile trovare il gestore delle richieste In-Process. Output acquisito dalla chiamata a hostfxr: non è possibile trovare una versione di Framework compatibile. Impossibile trovare la versione del framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' specificata. Impossibile avviare l'applicazione '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.

* **Log stdout del modulo ASP.NET Core:** Non è stato possibile trovare una versione di Framework compatibile. Impossibile trovare la versione del framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' specificata.

Risoluzione dei problemi:

* Se l'app è in esecuzione in un runtime di anteprima, installare l'estensione del sito a 32 bit (x86) **o** a 64 bit (x64) corrispondente al numero di bit dell'app e alla versione del runtime dell'app. **Non installare entrambe le estensioni o più versioni di runtime dell'estensione.**

  * Runtime ASP.NET Core {RUNTIME VERSION} (x86)
  * Runtime ASP.NET Core {RUNTIME VERSION} (x64)

  Riavviare l'app. Attendere alcuni secondi per il riavvio dell'app.

* Se l'app è in esecuzione in un runtime di anteprima e sono installate entrambe le [estensioni del sito](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) a 32 bit (x86) e a 64 bit (x64), disinstallare l'estensione del sito che non corrisponde al numero di bit dell'app. Dopo aver rimosso l'estensione del sito, riavviare l'app. Attendere alcuni secondi per il riavvio dell'app.

* Se l'app è in esecuzione in un runtime di anteprima e il numero di bit dell'estensione del sito corrisponde a quello dell'app, verificare che la *versione del runtime* dell'estensione del sito di anteprima corrisponda alla versione del runtime dell'app.

* Verificare che la **piattaforma** dell'app in **Impostazioni applicazione** corrisponda al numero di bit dell'app.

Per altre informazioni, vedere <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Un'app x86 viene distribuita, ma il pool di app non è abilitato per le app a 32 bit

* **Browser:** Errore HTTP 500,30-errore di avvio in-Process modulo ASP.NET Core

* **Registro applicazioni:** L'applicazione '/LM/W3SVC/5/ROOT ' con radice fisica ' {PATH}' ha raggiunto un'eccezione gestita imprevista, codice eccezione =' 0xe0434352'. Controllare i log stderr per altre informazioni. Applicazione '/LM/W3SVC/5/ROOT' con radice fisica '{PATH}'. Impossibile caricare clr e applicazione gestita. Chiusura prematura del thread di lavoro CLR

* **Log stdout del modulo ASP.NET Core:** Il file di log viene creato, ma vuoto.

Questo scenario viene intercettato dall'SDK durante la pubblicazione di un'app autonoma. L'SDK genera un errore se il RID non corrisponde alla piattaforma di destinazione (ad esempio, RID `win10-x64` con `<PlatformTarget>x86</PlatformTarget>` nel file di progetto).

Risoluzione dei problemi:

Per una distribuzione dipendente dal framework x86 (`<PlatformTarget>x86</PlatformTarget>`), abilitare il pool di app IIS per le app a 32 bit. In Gestione IIS aprire **Impostazioni avanzate** per il pool di app e impostare **Attiva applicazioni a 32 bit** su **True**.

## <a name="platform-conflicts-with-rid"></a>La piattaforma è in conflitto con RID

* **Browser:** errore HTTP 502.5 - errore del processo

* **Registro applicazioni:** L'applicazione "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}" con radice fisica "C\{: Path\' } non è riuscita ad avviare il processo con la\{riga di comando" "C: Path} {assembly}. {exe | dll} "', ErrorCode =' 0x80004005: FF.

* **Log stdout del modulo ASP.NET Core:** Eccezione non gestita: System. BadImageFormatException: Impossibile caricare il file o l'assembly ' {ASSEMBLY}. dll '. Tentativo di caricare un programma con un formato non corretto.

Risoluzione dei problemi:

* Confermare che l'app sia eseguita in locale su Kestrel. Un errore del processo può essere causato da un problema interno dell'applicazione. Per altre informazioni, vedere <xref:test/troubleshoot-azure-iis>.

* Se questa eccezione si verifica per una distribuzione di App di Azure durante l'aggiornamento di un'app e la distribuzione di assembly più recenti, eliminare manualmente tutti i file dalla distribuzione precedente. Le assembly incompatibili con il tempo di ritardo possono causare una eccezione `System.BadImageFormatException` quando si distribuisce un'app aggiornata.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Endpoint dell'URI non corretto o sito web arrestato

* **Browser:** ERR_CONNECTION_REFUSED **--o--** non è possibile connettersi

* **Registro dell'applicazione:** Nessuna voce

* **Log stdout del modulo ASP.NET Core:** Il file di log non viene creato.

Risoluzione dei problemi:

* Verificare che sia in uso l'endpoint URI corretto per l'app. Controllare i binding.

* Verificare che il sito Web IIS non sia nello stato *In arresto*.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Le funzionalità del server CoreWebEngine o W3SVC sono disabilitate

**Eccezione del sistema operativo:** per usare il modulo di ASP.NET Core è necessario installare le funzionalità di IIS 7.0 CoreWebEngine e W3SVC.

Risoluzione dei problemi:

Verificare che il ruolo e le funzionalità appropriati siano abilitati. Vedere [Configurazione di IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Percorso fisico del sito Web non corretto o app mancante

* **Browser:** 403 Non consentito: accesso negato **- OPPURE -** 403.14 Non consentito - Il server Web è configurato per non visualizzare il contenuto di questa directory.

* **Registro dell'applicazione:** Nessuna voce

* **Log stdout del modulo ASP.NET Core:** Il file di log non viene creato.

Risoluzione dei problemi:

Controllare il sito Web IIS **Impostazioni di base** e la cartella dell'app fisica. Verificare che l'app si trovi nella cartella nel sito Web IIS **Percorso fisico**.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Ruolo non corretto, modulo ASP.NET Core non installato o autorizzazioni non corrette

* **Browser:** 500.19 Errore interno al server – non è possibile accedere alla pagina richiesta perché i dati di configurazione per la pagina non sono validi. **--Oppure--** Non è possibile visualizzare questa pagina

* **Registro dell'applicazione:** Nessuna voce

* **Log stdout del modulo ASP.NET Core:** Il file di log non viene creato.

Risoluzione dei problemi:

* Verificare che il ruolo appropriato sia abilitato. Vedere [Configurazione di IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Aprire **Programmi e funzionalità** oppure **App e funzionalità** e verificare che sia installato **Windows Server Hosting**. Se **Windows Server Hosting** non è presente nell'elenco dei programmi installati, scaricare e installare il bundle di hosting .NET Core.

  [Programma di installazione del bundle di hosting .NET Core corrente (download diretto)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Per altre informazioni, vedere [Installare il bundle di hosting .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Verificare che il **modello** > **Identity** di processo del **pool** > di applicazioni sia impostato su **ApplicationPoolIdentity** o che l'identità personalizzata disponga delle autorizzazioni corrette per accedere alla cartella di distribuzione dell'app.

* Se il bundle di hosting ASP.NET Core è stato disinstallato e quindi è stata installata una versione del bundle di hosting precedente, il file *applicationHost.config* non include una sezione per il modulo ASP.NET Core. Aprire *applicationHost.config* in *%windir%/System32/inetsrv/config* e trovare il gruppo di sezioni `<configuration><configSections><sectionGroup name="system.webServer">`. Se la sezione per il modulo ASP.NET Core non è presente nel gruppo di sezioni, aggiungere l'elemento della sezione:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  In alternativa installare la versione più recente del bundle di hosting ASP.NET Core. La versione più recente è compatibile con le app ASP.NET Core supportate.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>ProcessPath non corretto, variabile di percorso mancante, aggregazione di hosting non installata, sistema/IIS non riavviato, VC Redistributable non installato o violazione dell'accesso a dotnet.exe

* **Browser:** errore HTTP 502.5 - errore del processo

* **Registro applicazioni:** L'applicazione "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}" con radice fisica "C\{: Path\' } non è riuscita ad avviare il processo con la riga di comando" "{...}" ', ErrorCode =' 0x80070002:0.

* **Log stdout del modulo ASP.NET Core:** Il file di log viene creato, ma vuoto.

Risoluzione dei problemi:

* Confermare che l'app sia eseguita in locale su Kestrel. Un errore del processo può essere causato da un problema interno dell'applicazione. Per altre informazioni, vedere <xref:test/troubleshoot-azure-iis>.

* Verificare che l'attributo *processPath* dell'elemento `<aspNetCore>` in *web.config* sia `dotnet` per una distribuzione dipendente da framework (FDD, Framework-Dependent Deployment) o `.\{ASSEMBLY}.exe` per una [distribuzione autonoma (SCD, Self-Contained Deployment)](/dotnet/core/deploying/#self-contained-deployments-scd).

* Per una distribuzione dipendente dal framework, *dotnet.exe* potrebbe non essere accessibile tramite le impostazioni del percorso. Confermare che *C:\Programmi\dotnet\\* esiste nelle impostazioni PATH di sistema.

* Per una distribuzione dipendente da framework, *dotnet.exe* potrebbe non essere accessibile per l'identità utente del pool di app. Confermare che l'identità dell'utente del pool di app abbia accesso alla directory *C:\Programmi\dotnet*. Verificare che non siano presenti regole di negazione configurate per l'identità dell'utente del pool di app in *C:\Programmi\dotnet* e nelle directory dell'app.

* È possibile che sia stata eseguita una distribuzione FDD e che sia stato installato .NET Core senza riavviare IIS. Riavviare il server o riavviare IIS eseguendo **net stop was /y** seguito da **net start w3svc** da un prompt dei comandi.

* È possibile che sia stata eseguita una distribuzione FDD senza installare il runtime .NET Core nel sistema host. Se il runtime .NET Core non è stato installato, eseguire il **programma di installazione del bundle di hosting .NET Core** nel sistema.

  [Programma di installazione del bundle di hosting .NET Core corrente (download diretto)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Per altre informazioni, vedere [Installare il bundle di hosting .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Se è necessario un runtime specifico, scaricare il runtime dagli [archivi di download .NET](https://dotnet.microsoft.com/download/archives) e installarlo nel sistema. Completare l'installazione riavviando il sistema o riavviando IIS eseguendo **net stop was /y** seguito da **net start w3svc** da un prompt dei comandi.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Argomenti non corretti dell'elemento \<aspNetCore>

* **Browser:** errore HTTP 502.5 - errore del processo

* **Registro applicazioni:** L'applicazione "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}" con radice fisica "C\{: Path\' } non è riuscita ad avviare il processo con la riga di comando" "DotNet". \{Assembly}. dll ', ErrorCode =' 0x80004005:80008081.

* **Log stdout del modulo ASP.NET Core:** L'applicazione da eseguire non esiste:' assembly percorso\{}. dll '

Risoluzione dei problemi:

* Confermare che l'app sia eseguita in locale su Kestrel. Un errore del processo può essere causato da un problema interno dell'applicazione. Per altre informazioni, vedere <xref:test/troubleshoot-azure-iis>.

* Verificare che l'attributo *arguments* dell'elemento `<aspNetCore>` in *web.config* sia (a) `.\{ASSEMBLY}.dll` per una distribuzione dipendente da framework (FDD) o (b) non disponibile, una stringa vuota (`arguments=""`) o un elenco degli argomenti dell'app (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) per una distribuzione autonoma (SCD).

Risoluzione dei problemi:

Per una distribuzione dipendente da framework (FDD), verificare che nel sistema sia installato il runtime corretto.

## <a name="stopped-application-pool"></a>Pool di applicazioni arrestato

* **Browser:** 503 Servizio non disponibile

* **Registro dell'applicazione:** Nessuna voce

* **Log stdout del modulo ASP.NET Core:** Il file di log non viene creato.

Risoluzione dei problemi:

Confermare che il pool di applicazioni non sia nello stato *Arrestato*.

## <a name="sub-application-includes-a-handlers-section"></a>L'app secondaria include una sezione \<handlers>

* **Browser:** errore HTTP 500.19 - errore del server interno

* **Registro dell'applicazione:** Nessuna voce

* **Log stdout del modulo ASP.NET Core:** Il file di log dell'app radice viene creato e Mostra il normale funzionamento. Il file di log dell'app secondaria non viene creato.

Risoluzione dei problemi:

Verificare che il file *web.config* della sotto-applicazione non includa una sezione `<handlers>`.

## <a name="stdout-log-path-incorrect"></a>percorso errato del log stdout

* **Browser:** l'app risponde normalmente.

* **Registro applicazioni:** Avviso: non è stato possibile \\creare stdoutLogFile? \{Path} \ path_doesnt_exist \ STDOUT_ {ID processo} _ {timestamp}. log, ErrorCode =-2147024893.

* **Log stdout del modulo ASP.NET Core:** Il file di log non viene creato.

Risoluzione dei problemi:

* Il percorso `stdoutLogFile` specificato nell'elemento `<aspNetCore>` di *web.config* non esiste. Per ulteriori informazioni, vedere [ASP.NET Core Module: creazione e Reindirizzamento dei log](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* L'utente del pool di app non ha accesso in scrittura al percorso del log di stdout.

## <a name="application-configuration-general-issue"></a>Problema generale della configurazione dell'applicazione

* **Browser:** errore HTTP 502.5 - errore del processo

* **Registro applicazioni:** L'applicazione "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}" con radice fisica "C\{: Path\' } ha creato il processo con la riga\{di comando\{" "c: Path} assembly}. {exe | dll} "', ma è stato arrestato in modo anomalo o non ha risposto o non è in ascolto sulla porta specificata ' {PORT}', ErrorCode =' {codice errore}'

* **Log stdout del modulo ASP.NET Core:** Il file di log viene creato, ma vuoto.

Risoluzione dei problemi:

non è stato possibile avviare il processo, molto probabilmente a causa di un problema di configurazione dell'app o di programmazione.

Per altre informazioni, vedere i seguenti argomenti:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
