---
title: Usare LibMan con ASP.NET Core in Visual StudioUse LibMan with ASP.NET Core in Visual Studio
author: scottaddie
description: Informazioni su come usare LibMan in un progetto ASP.NET Core con Visual Studio.Learn how to use LibMan in an ASP.NET Core project with Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658312"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Usare LibMan con ASP.NET Core in Visual StudioUse LibMan with ASP.NET Core in Visual Studio

Di [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio include il supporto incorporato per LibMan nei progetti ASP.NET Core, tra cui:Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:

* Supporto per la configurazione e l'esecuzione di operazioni di ripristino LibMan in fase di compilazione.
* Voci di menu per l'attivazione di operazioni di ripristino e pulizia di LibMan.
* Finestra di dialogo di ricerca per trovare librerie e aggiungere i file a un progetto.
* Modifica del supporto per *libman.json*&mdash;del file manifesto LibMan.

[Visualizzare o scaricare](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) codice di esempio [(come scaricare)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con il carico di lavoro **di sviluppo Web e ASP.NET**

## <a name="add-library-files"></a>Aggiungere file di libreria

I file di libreria possono essere aggiunti a un progetto ASP.NET Core in due modi diversi:

1. [Utilizzare la finestra di dialogo Aggiungi libreria sul lato client](#use-the-add-client-side-library-dialog)
1. [Configurare manualmente le voci del file manifesto LibMan](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Utilizzare la finestra di dialogo Aggiungi libreria sul lato client

Per installare una libreria lato client, attenersi alla seguente procedura:

* In **Esplora soluzioni**fare clic con il pulsante destro del mouse sulla cartella del progetto in cui devono essere aggiunti i file. Scegliere **Aggiungi** > **libreria lato client**. Viene visualizzata la finestra di dialogo **Aggiungi libreria lato client:**

  ![Finestra di dialogo Aggiungi libreria lato client](_static/add-library-dialog.png)

* Selezionare il provider di librerie dall'elenco a discesa **Provider.** CDNJS è il provider predefinito.
* Digitare il nome della libreria da recuperare nella casella di testo **Libreria.** IntelliSense fornisce un elenco di librerie che iniziano con il testo fornito.
* Selezionare la libreria dall'elenco IntelliSense.Select the library from the IntelliSense list. Si noti che il `@` nome della libreria è suffisso con il simbolo e l'ultima versione stabile nota al provider selezionato.
* Decidere quali file includere:
  * Selezionare il pulsante di opzione **Includi tutti i file** di libreria per includere tutti i file della libreria.
  * Selezionare il pulsante di opzione **Scegli file specifici** per includere un sottoinsieme dei file della libreria. Quando il pulsante di opzione è selezionato, l'albero del selettore di file è abilitato. Selezionare le caselle a sinistra dei nomi dei file da scaricare.
* Specificare la cartella del progetto per l'archiviazione dei file nella casella di testo Percorso di **destinazione.** Come raccomandazione, archiviare ogni libreria in una cartella separata.

  La cartella **Posizione di destinazione** suggerita si basa sulla posizione da cui è stata avviata la finestra di dialogo:

  * Se lanciato dalla radice del progetto:
    * *wwwroot/lib* viene utilizzato se *wwwroot* esiste.
    * *lib* viene utilizzato se *wwwroot* non esiste.
  * Se viene avviato da una cartella di progetto, viene utilizzato il nome della cartella corrispondente.

  Il suggerimento della cartella è un suffisso con il nome della libreria. Nella tabella seguente vengono illustrati i suggerimenti di cartelle durante l'installazione di jQuery in un progetto Razor Pages.
  
  |Posizione di lancio                           |Cartella suggerita      |
  |------------------------------------------|----------------------|
  |radice del progetto (se *wwwroot* esiste)        |*wwwroot/lib/jquery/* |
  |radice del progetto (se *wwwroot* non esiste) |*lib/jquery/*         |
  |*Cartella Pagine* nel progetto                 |*Pagine/jquery/*       |

* Fare clic sul pulsante **Installa** per scaricare i file, in base alla configurazione in *libman.json*.
* Esaminare il feed **Gestione librerie** della finestra **Output** per i dettagli di installazione. Ad esempio:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>Configurare manualmente le voci del file manifesto LibMan

Tutte le operazioni LibMan in Visual Studio sono basate sul contenuto del manifesto LibMan della radice del progetto (*libman.json*). È possibile modificare manualmente *libman.json* per configurare i file di libreria per il progetto. Visual Studio ripristina tutti i file di libreria dopo il salvataggio di *libman.json.*

Per aprire *libman.json* per la modifica, esistono le seguenti opzioni:

* Fare doppio clic sul file *libman.json* in **Esplora soluzioni**.
* Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e selezionare **Gestisci librerie lato client**. **&#8224;**
* Selezionare **Gestisci librerie lato client** dal menu Progetto di Visual Studio.Select Manage Client-Side Libraries from the Visual Studio **Project** menu. **&#8224;**

**&#8224;** Se il file *libman.json* non esiste già nella radice del progetto, verrà creato con il contenuto del modello di elemento predefinito.

Visual Studio offre supporto avanzato per la modifica JSON, ad esempio la colorazione, la formattazione, IntelliSense e la convalida dello schema. Lo schema JSON del manifesto LibMan si trova in [https://json.schemastore.org/libman](https://json.schemastore.org/libman).

Con il file manifesto seguente, LibMan recupera i `libraries` file in base alla configurazione definita nella proprietà. Una spiegazione dei valori `libraries` letterali oggetto definiti all'interno di quanto segue:

* Un sottoinsieme di [jQuery](https://jquery.com/) versione 3.3.1 viene recuperato dal provider CDNJS. Il sottoinsieme è `files` &mdash;definito nella proprietà*jquery.min.js*, *jquery.js*e *jquery.min.map*. I file vengono inseriti nella cartella *wwwroot/lib/jquery* del progetto.
* La totalità di [Bootstrap](https://getbootstrap.com/) versione 4.1.3 viene recuperata e inserita in una cartella *wwwroot/lib/bootstrap.* La proprietà del `provider` valore letterale dell'oggetto esegue l'override del valore della `defaultProvider` proprietà. LibMan recupera i file Bootstrap dal provider unpkg.
* Un sottoinsieme di [Lodash](https://lodash.com/) è stato approvato da un organo direttivo all'interno dell'organizzazione. I file *lodash.js* e *lodash.min.js* vengono recuperati dal file system locale in *C:\\temp\\lodash\\*. I file vengono copiati nella cartella *wwwroot/lib/lodash* del progetto.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan supporta solo una versione di ogni libreria da ogni provider. Il file *libman.json* non supera la convalida dello schema se contiene due librerie con lo stesso nome di libreria per un determinato provider.

## <a name="restore-library-files"></a>Ripristinare i file di libreria

Per ripristinare i file di libreria dall'interno di Visual Studio, deve essere presente un file *libman.json* valido nella radice del progetto. I file ripristinati vengono inseriti nel progetto nella posizione specificata per ogni libreria.

I file di libreria possono essere ripristinati in un progetto ASP.NET Core in due modi:

1. [Ripristinare i file durante la compilazioneRestore files during build](#restore-files-during-build)
1. [Ripristinare i file manualmente](#restore-files-manually)

### <a name="restore-files-during-build"></a>Ripristinare i file durante la compilazioneRestore files during build

LibMan può ripristinare i file di libreria definiti come parte del processo di compilazione. Per impostazione predefinita, il comportamento di ripristino alla *compilazione* è disabilitato.

Per abilitare e testare il comportamento di ripristino in fase di compilazione:

* Fare clic con il pulsante destro del mouse su *libman.json* in **Esplora soluzioni** e scegliere **Abilita ripristina librerie lato client durante** la compilazione dal menu di scelta rapida.
* Fare clic sul pulsante **Sì** quando viene richiesto di installare un pacchetto NuGet. Il pacchetto [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet viene aggiunto al progetto:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Compilare il progetto per verificare che si verifichi il ripristino del file LibMan. Il `Microsoft.Web.LibraryManager.Build` pacchetto inserisce una destinazione MSBuild che esegue LibMan durante l'operazione di compilazione del progetto.
* Esaminare il feed **Di compilazione** della finestra **Output** per un log attività LibMan:

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

Quando il comportamento restore-on-build è abilitato, il menu di scelta rapida *libman.json* visualizza un'opzione **Disattiva ripristina librerie lato client durante** la compilazione. Selezionando questa opzione `Microsoft.Web.LibraryManager.Build` si rimuove il riferimento al pacchetto dal file di progetto. Di conseguenza, le librerie lato client non vengono più ripristinate in ogni compilazione.

Indipendentemente dall'impostazione di ripristino alla compilazione, è possibile eseguire manualmente il ripristino in qualsiasi momento dal menu di scelta rapida *libman.json.* Per ulteriori informazioni, consultate [Ripristinare i file manualmente.](#restore-files-manually)

### <a name="restore-files-manually"></a>Ripristinare i file manualmente

Per ripristinare manualmente i file di libreria:

* Per tutti i progetti nella soluzione:
  * Fare clic con il pulsante destro del mouse sul nome della soluzione in **Esplora soluzioni**.
  * Selezionare l'opzione **Ripristina librerie lato client.**
* Per un progetto specifico:
  * Fare clic con il pulsante destro del mouse sul file *libman.json* in **Esplora soluzioni**.
  * Selezionare l'opzione **Ripristina librerie lato client.**

Durante l'esecuzione dell'operazione di ripristino:

* L'icona Centro stato attività (TSC) sulla barra di stato di Visual Studio verrà animata e verrà *letta avviata l'operazione di ripristino*. Facendo clic sull'icona si apre una descrizione comando che elenca le attività in background note.
* I messaggi verranno inviati alla barra di stato e al feed **di Gestione librerie** della finestra **Output.** Ad esempio:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>Eliminare i file di libreria

Per eseguire l'operazione *di pulizia,* che elimina i file di libreria precedentemente ripristinati in Visual Studio:

* Fare clic con il pulsante destro del mouse sul file *libman.json* in **Esplora soluzioni**.
* Selezionare l'opzione **Pulisci librerie lato client.**

Per impedire la rimozione involontaria di file non di libreria, l'operazione di pulizia non elimina intere directory. Rimuove solo i file inclusi nel ripristino precedente.

Durante l'esecuzione dell'operazione di pulizia:

* L'icona TSC sulla barra di stato di Visual Studio verrà animata e verrà letta *L'operazione Librerie client avviata*. Facendo clic sull'icona si apre una descrizione comando che elenca le attività in background note.
* I messaggi vengono inviati alla barra di stato e al feed **Gestione librerie** della finestra **Output.** Ad esempio:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

L'operazione di pulizia elimina solo i file dal progetto. I file di libreria rimangono nella cache per un recupero più rapido delle operazioni di ripristino future. Per gestire i file di libreria archiviati nella cache del computer locale, utilizzare [l'interfaccia della riga di comando LibMan](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Disinstallare i file di libreria

Per disinstallare i file di libreria:

* Aprire *libman.json*.
* Posizionare il carattere `libraries` di inserimento all'interno del valore letterale dell'oggetto corrispondente.
* Fare clic sull'icona della lampadina visualizzata nel margine sinistro e selezionare **Disinstalla \<library_name>\<library_version>**:

  ![Opzione di menu di scelta rapida Disinstalla libreria](_static/uninstall-menu-option.png)

In alternativa, è possibile modificare e salvare manualmente il manifesto LibMan (*libman.json*). [L'operazione di ripristino](#restore-library-files) viene eseguita quando il file viene salvato. I file di libreria non più definiti in *libman.json* vengono rimossi dal progetto.

## <a name="update-library-version"></a>Aggiornare la versione della libreria

Per verificare la disponibilità di una versione aggiornata della libreria:

* Aprire *libman.json*.
* Posizionare il carattere `libraries` di inserimento all'interno del valore letterale dell'oggetto corrispondente.
* Fare clic sull'icona della lampadina visualizzata nel margine sinistro. Passare il mouse su **Controlla aggiornamenti**.

LibMan verifica la presenza di una versione della libreria più recente della versione installata. Possono verificarsi i seguenti risultati:

* Se è già installata la versione più recente, viene visualizzato il messaggio **Nessun aggiornamento trovato.**
* Se non è già installata, viene visualizzata l'ultima versione stabile.

  ![Opzione del menu di scelta rapida Controlla aggiornamenti](_static/update-menu-option.png)

* Se è disponibile una versione non definitiva rispetto alla versione installata, viene visualizzata la versione non definitiva.

Per eseguire il downgrade a una versione precedente della libreria, modificare manualmente il file *libman.json.* Quando il file viene salvato, [l'operazione](#restore-library-files)di ripristino LibMan :

* Rimuove i file ridondanti dalla versione precedente.
* Aggiunge file nuovi e aggiornati dalla nuova versione.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:client-side/libman/libman-cli>
* [Repository GitHub di LibMan](https://github.com/aspnet/LibraryManager)
