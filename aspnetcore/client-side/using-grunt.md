---
title: Usare Grunt in ASP.NET Core
author: rick-anderson
description: Usare Grunt in ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657591"
---
# <a name="use-grunt-in-aspnet-core"></a>Usare Grunt in ASP.NET Core

Grunt è un task runner JavaScript che automatizza la minimizzazione degli script, la compilazione TypeScript, gli strumenti "lint" di qualità del codice, i preprocessori CSS e praticamente qualsiasi lavoro ripetitivo che deve essere fatto per supportare lo sviluppo client. Grunt è completamente supportato in Visual Studio.

Questo esempio usa un progetto ASP.NET Core vuoto come punto di partenza, per mostrare come automatizzare il processo di compilazione client da zero.

L'esempio finito pulisce la directory di distribuzione di destinazione, combina i file JavaScript, controlla la qualità del codice, condensa il contenuto del file JavaScript e viene distribuito nella radice dell'applicazione Web. Useremo i seguenti pacchetti:

* **grugnito**: Il pacchetto corridore task Grunt.

* **grunt-contrib-clean**: Un plugin che rimuove file o directory.

* **grunt-contrib-jshint**: Un plugin che esamina la qualità del codice JavaScript.

* **grunt-contrib-concat**: Un plugin che unisce i file in un unico file.

* **grunt-contrib-uglify**: Un plugin che minifica JavaScript per ridurre le dimensioni.

* **grunt-contrib-watch**: Un plugin che guarda l'attività dei file.

## <a name="preparing-the-application"></a>Preparazione dell'applicazione

Per iniziare, impostare una nuova applicazione Web vuota e aggiungere file di esempio TypeScript. I file TypeScript vengono compilati automaticamente in JavaScript utilizzando le impostazioni predefinite di Visual Studio e saranno la nostra materia prima da elaborare utilizzando Grunt.

1. In Visual Studio creare `ASP.NET Web Application`un nuovo file .

2. Nella finestra di dialogo **Nuovo progetto ASP.NET,** selezionare il ASP.NET modello **Core Empty** e fare clic sul pulsante OK.

3. In Esplora soluzioni esaminare la struttura del progetto. La `\src` cartella `wwwroot` include `Dependencies` nodi vuoti e vuoti.

    ![soluzione web vuota](using-grunt/_static/grunt-solution-explorer.png)

4. Aggiungere una nuova `TypeScript` cartella denominata alla directory del progetto.

5. Prima di aggiungere file, assicurarsi che Visual Studio abbia l'opzione 'compila in caso di salvataggio' per i file TypeScript selezionata. Passare a**Opzioni** >  **opzioni** > **Testo Editor** > **Tipodi** > **progetto**:

    ![opzioni impostazione compilazione automatica dei file TypeScript](using-grunt/_static/typescript-options.png)

6. Fare clic `TypeScript` con il pulsante destro del mouse sulla directory e scegliere **Aggiungi > nuovo elemento** dal menu di scelta rapida. Selezionare l'elemento di **file JavaScript** e denominare il file *Tastes.ts* (notare l'estensione \*.ts). Copiare la riga di codice TypeScript riportato di seguito nel file (quando si salva, verrà visualizzato un nuovo file *Tastes.js* con l'origine JavaScript).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Aggiungere un secondo file alla directory `Food.ts` **TypeScript** e denominarlo . Copiare il codice seguente nel file.

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a>Configurazione di NPM

Configurare quindi NPM per scaricare le attività grunt e grunt.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi > nuovo elemento** dal menu di scelta rapida. Selezionare l'elemento del file di **configurazione NPM,** lasciare il nome predefinito, *package.json*, quindi fare clic sul pulsante **Aggiungi.**

2. Nel file *package.json,* `devDependencies` all'interno delle parentesi graffe dell'oggetto, immettere "grunt". Selezionare `grunt` dall'elenco Intellisense e premere INVIO. Visual Studio citerà il nome del pacchetto grunt e aggiungerà i due punti. A destra dei due punti, selezionare l'ultima versione stabile del pacchetto `Ctrl-Space` dall'inizio dell'elenco Intellisense (premere se Intellisense non viene visualizzato).

    ![intellisense grugnito](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM utilizza [il controllo delle versioni semantico](https://semver.org/) per organizzare le dipendenze. Il controllo delle versioni semantico, noto anche come \<SemVer, identifica i pacchetti con lo schema di numerazione principale>. \<> minori. \<> patch. Intellisense semplifica il controllo delle versioni semantico mostrando solo alcune scelte comuni. L'elemento superiore nell'elenco Intellisense (0.4.5 nell'esempio precedente) viene considerato l'ultima versione stabile del pacchetto. Il simbolo dell'accento circonflesso (-) corrisponde alla versione principale più recente e la tilde (-) corrisponde alla versione secondaria più recente. Vedere il riferimento al parser della [versione semver NPM](https://www.npmjs.com/package/semver) come guida all'espressività completa fornita da SemVer.

3. Aggiungere altre dipendenze\* per caricare grunt-contrib- packages for *clean*, *jshint*, *concat*, *uglify*e *watch* come illustrato nell'esempio seguente. Non è necessario che le versioni corrispondano all'esempio.

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. Salvare il file *package.json.*

Verranno scaricati i pacchetti per ogni `devDependencies` elemento, insieme a tutti i file necessari per ogni pacchetto. È possibile trovare i file del pacchetto nella directory *node_modules* attivando il pulsante **Mostra tutti i file** in Esplora **soluzioni**.

![node_modules grugniti](using-grunt/_static/node-modules.png)

> [!NOTE]
> Se necessario, è possibile ripristinare manualmente le dipendenze in `Dependencies\NPM` **Esplora soluzioni** facendo clic con il pulsante destro del mouse e selezionando l'opzione di menu Ripristina **pacchetti.**

![ripristinare i pacchetti](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Configurazione di Grunt

Grunt viene configurato utilizzando un manifesto denominato *Gruntfile.js* che definisce, carica e registra le attività che possono essere eseguite manualmente o configurate per l'esecuzione automatica in base agli eventi in Visual Studio.

1. Fare clic con il pulsante destro del mouse sul progetto e **scegliere Aggiungi** > **nuovo elemento**. Selezionare il modello di elemento **File JavaScript,** modificare il nome in *Gruntfile.js*e fare clic sul pulsante **Aggiungi** .

1. Aggiungere il codice seguente a *Gruntfile.js*. La `initConfig` funzione imposta le opzioni per ogni pacchetto e il resto del modulo carica e registra le attività.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. All'interno della `initConfig` funzione, `clean` aggiungere le opzioni per l'attività come illustrato nell'esempio *Gruntfile.js* riportato di seguito. L'attività `clean` accetta una matrice di stringhe di directory. Questa attività rimuove i file da *wwwroot/lib* e rimuove l'intera directory */temp.*

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Sotto `initConfig` la funzione, aggiungere `grunt.loadNpmTasks`una chiamata a . In questo modo l'attività eseguebile da Visual Studio.This will make the task runnable from Visual Studio.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. Salvare *Gruntfile.js*. Il file dovrebbe essere simile alla schermata qui sotto.

    ![gruntfile iniziale](using-grunt/_static/gruntfile-js-initial.png)

1. Fare clic con il pulsante destro del mouse su *Gruntfile.js* e selezionare **Task Runner Explorer** dal menu di scelta rapida. Si aprirà la finestra **Task Runner Explorer.**

    ![task runner explorer menu](using-grunt/_static/task-runner-explorer-menu.png)

1. Verificare `clean` che viene visualizzato in **Attività** in **Task Runner Explorer**.

    ![elenco delle attività di esploratori runner](using-grunt/_static/task-runner-explorer-tasks.png)

1. Fare clic con il pulsante destro del mouse sull'attività pulita e scegliere **Esegui** dal menu di scelta rapida. Una finestra di comando visualizza lo stato di avanzamento dell'attività.

    ![task runner explorer eseguire attività pulita](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Non ci sono file o directory da pulire ancora. Se lo si desidera, è possibile crearli manualmente in Esplora soluzioni e quindi eseguire l'attività pulita come test.

1. Nella `initConfig` funzione aggiungere una `concat` voce per l'utilizzo del codice seguente.

    La `src` matrice di proprietà elenca i file da combinare, nell'ordine in cui devono essere combinati. La `dest` proprietà assegna il percorso al file combinato prodotto.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > La `all` proprietà nel codice precedente è il nome di una destinazione. Le destinazioni vengono usate in alcune attività Grunt per consentire più ambienti di compilazione. È possibile visualizzare le destinazioni predefinite usando IntelliSense o assegnarne di proprie.

1. Aggiungere `jshint` l'attività utilizzando il codice riportato di seguito.

    L'utilità `code-quality` jshint viene eseguita su tutti i file JavaScript presenti nella directory *temp.*

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > L'opzione "-W069" è un errore generato da jshint quando JavaScript utilizza la sintassi `Tastes["Sweet"]` delle `Tastes.Sweet`parentesi quadre per assegnare una proprietà anziché la notazione del punto, ovvero anziché . L'opzione disattiva l'avviso per consentire al resto del processo di continuare.

1. Aggiungere `uglify` l'attività utilizzando il codice riportato di seguito.

    L'attività minifica il file *combined.js* che si trova nella directory temp e crea il file dei risultati in wwwroot/lib seguendo il nome * \<\>* del file della convenzione di denominazione standard .min.js .

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. Sotto la `grunt.loadNpmTasks` chiamata `grunt-contrib-clean`a tale caricamento , includere la stessa chiamata per jshint, concat e uglify utilizzando il codice riportato di seguito.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. Salvare *Gruntfile.js*. Il file dovrebbe essere simile all'esempio seguente.

    ![esempio di file grugnito completo](using-grunt/_static/gruntfile-js-complete.png)

1. Si noti che l'elenco `concat` `jshint` Attività `uglify` di **Task Runner Explorer** include `clean`, e attività. Eseguire ogni attività in ordine e osservare i risultati in **Esplora soluzioni**. Ogni attività deve essere eseguita senza errori.

    ![task runner explorer eseguire ogni attività](using-grunt/_static/task-runner-explorer-run-each-task.png)

    L'attività concat crea un nuovo file *combined.js* e lo inserisce nella directory temp. L'attività `jshint` viene eseguita semplicemente e non produce output. L'attività `uglify` crea un nuovo file *combined.min.js* e lo inserisce in *wwwroot/lib*. Al termine, la soluzione dovrebbe essere simile alla schermata seguente:On completion, the solution should look something like the screenshot below:

    ![Esplora soluzioni dopo tutte le attività](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Per ulteriori informazioni sulle opzioni per [https://www.npmjs.com/](https://www.npmjs.com/) ogni pacchetto, visitare e cercare il nome del pacchetto nella casella di ricerca nella pagina principale. Ad esempio, è possibile cercare il pacchetto grunt-contrib-clean per ottenere un collegamento alla documentazione che spiega tutti i relativi parametri.

### <a name="all-together-now"></a>Riepilogo

Utilizzare il metodo `registerTask()` Grunt per eseguire una serie di attività in una determinata sequenza. Ad esempio, per eseguire i passaggi di esempio precedenti nell'ordine clean -> concat -> > > possibile aggiungere il codice riportato di seguito al modulo. Il codice deve essere aggiunto allo stesso livello delle chiamate loadNpmTasks(), all'esterno di initConfig.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

La nuova attività viene visualizzata in Task Runner Explorer in Alias Tasks. È possibile fare clic con il pulsante destro del mouse ed eseguirlo come in altre attività. `all` L'attività `clean`verrà `concat` `jshint` eseguita in ordine e , e `uglify`, .

![alias compiti grunt](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Controllo per la ricerca di modifiche

Un'attività `watch` tiene d'occhio file e directory. L'orologio attiva automaticamente le attività se rileva le modifiche. Aggiungere il codice seguente a initConfig \*per controllare le modifiche apportate ai file con estensione js nella directory TypeScript. Se un file JavaScript `watch` viene `all` modificato, eseguirà l'attività.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Aggiungere una `loadNpmTasks()` chiamata a `watch` per visualizzare l'attività in Task Runner Explorer.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Fare clic con il pulsante destro del mouse sull'attività di controllo in Task Runner Explorer e scegliere Esegui dal menu di scelta rapida. Nella finestra di comando che mostra l'attività di espressioni di controllo in esecuzione verrà visualizzato un "In attesa..." Messaggio. Aprire uno dei file TypeScript, aggiungere uno spazio e quindi salvare il file. Ciò attiverà l'attività di espressioni di controllo e attiverà l'esecuzione delle altre attività nell'ordine. La schermata seguente mostra un'esecuzione di esempio.

![output delle attività in esecuzione](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Associazione agli eventi di Visual StudioBinding to Visual Studio events

A meno che non si desideri avviare manualmente le attività ogni volta che si lavora in Visual Studio, associare le attività agli eventi **Prima della compilazione**, **Dopo la compilazione**, **Clean**e **Project Open** .

Associare in modo che venga eseguito ogni volta che viene aperto Visual Studio.Bind `watch` so that it runs every time Visual Studio opens. In Task Runner Explorer, fare clic con il pulsante destro del mouse sull'attività di controllo e selezionare **Binding apertura** > **progetto** dal menu di scelta rapida.

![associare un'attività all'apertura del progetto](using-grunt/_static/bindings-project-open.png)

Scaricare e ricaricare il progetto. Quando il progetto viene caricato nuovamente, l'attività di espressioni di controllo viene avviata automaticamente.

## <a name="summary"></a>Summary

Grunt è un potente corridore di attività che può essere utilizzato per automatizzare la maggior parte delle attività di creazione del client. Grunt sfrutta NPM per distribuire i propri pacchetti e l'integrazione degli strumenti con Visual Studio. Task Runner Explorer di Visual Studio rileva le modifiche ai file di configurazione e fornisce una comoda interfaccia per eseguire attività, visualizzare attività in esecuzione e associare attività agli eventi di Visual Studio.
