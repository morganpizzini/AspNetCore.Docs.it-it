---
title: USA Grugnio in ASP.NET Core
author: rick-anderson
description: USA Grugnio in ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
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
uid: client-side/using-grunt
ms.openlocfilehash: 019f31c1a6fa3a33783f78f2fee71f710642ce6d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013203"
---
# <a name="use-grunt-in-aspnet-core"></a>USA Grugnio in ASP.NET Core

Grugnito è un task Runner JavaScript che consente di automatizzare lo script minification, la compilazione di TypeScript, gli strumenti di qualità del codice "lanugine", i preprocessori CSS e solo le faccende ripetitive che richiedono per supportare lo sviluppo di client. Grugnito è completamente supportato in Visual Studio.

Questo esempio usa un progetto di ASP.NET Core vuoto come punto di partenza, per illustrare come automatizzare il processo di compilazione client da zero.

L'esempio completato pulisce la directory di distribuzione di destinazione, combina i file JavaScript, controlla la qualità del codice, condensa il contenuto del file JavaScript e lo distribuisce nella radice dell'applicazione Web. Si useranno i pacchetti seguenti:

* **grugnito**: il pacchetto di attività Runner.

* **grugnito-contrib-clean**: un plug-in che rimuove file o directory.

* **grugnito-contrib-jshint**: un plug-in che esamina la qualità del codice JavaScript.

* **grugnito-contrib-Concat**: un plug-in che unisce i file in un singolo file.

* **grugnito-contrib-uglify**: un plug-in che minimizza JavaScript per ridurre le dimensioni.

* **grugnito-contrib-Watch**: un plug-in che controlla l'attività del file.

## <a name="preparing-the-application"></a>Preparazione dell'applicazione

Per iniziare, impostare una nuova applicazione Web vuota e aggiungere i file di esempio TypeScript. I file TypeScript vengono compilati automaticamente in JavaScript usando le impostazioni predefinite di Visual Studio e saranno il materiale grezzo per l'elaborazione con grugni.

1. In Visual Studio creare un nuovo `ASP.NET Web Application` .

2. Nella finestra di dialogo **nuovo progetto ASP.NET** selezionare il modello ASP.NET Core **vuoto** e fare clic sul pulsante OK.

3. Nella Esplora soluzioni esaminare la struttura del progetto. La `\src` cartella include `wwwroot` nodi vuoti e `Dependencies` .

    ![soluzione Web vuota](using-grunt/_static/grunt-solution-explorer.png)

4. Aggiungere una nuova cartella denominata `TypeScript` alla directory del progetto.

5. Prima di aggiungere i file, assicurarsi che in Visual Studio sia selezionata l'opzione ' compila al salvataggio ' per i file TypeScript. Passare a **strumenti**  >  **Opzioni**  >  **editor di testo**  >  **typescript**  >  **progetto**:

    ![opzioni di impostazione della compilazione automatica dei file TypeScript](using-grunt/_static/typescript-options.png)

6. Fare clic con il pulsante destro del mouse sulla `TypeScript` Directory e scegliere **Aggiungi > nuovo elemento** dal menu di scelta rapida. Selezionare l'elemento di **file JavaScript** e denominare il file *gusti. TS* (si noti l' \* estensione TS). Copiare la riga di codice TypeScript seguente nel file (quando si salva, viene visualizzato un nuovo file di *Tastes.js* con l'origine JavaScript).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Aggiungere un secondo file alla directory **typescript** e denominarlo `Food.ts` . Copiare il codice seguente nel file.

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

Successivamente, configurare NPM per scaricare le attività grugnite e grugnito.

1. Nel Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **aggiungi > nuovo elemento** dal menu di scelta rapida. Selezionare l'elemento del **file di configurazione NPM** , lasciare il nome predefinito *package.jsin*e fare clic sul pulsante **Aggiungi** .

2. Nella *package.jssu* file, all'interno delle `devDependencies` parentesi graffe dell'oggetto, immettere "grugnito". Selezionare `grunt` dall'elenco IntelliSense e premere il tasto INVIO. In Visual Studio viene citato il nome del pacchetto grugnito e vengono aggiunti i due punti. A destra dei due punti, selezionare la versione stabile più recente del pacchetto dalla parte superiore dell'elenco IntelliSense (premere `Ctrl-Space` Se IntelliSense non viene visualizzato).

    ![IntelliSense grugnito](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM usa il [controllo delle versioni semantico](https://semver.org/) per organizzare le dipendenze. Il controllo delle versioni semantico, noto anche come SemVer, identifica i pacchetti con lo schema di numerazione \<major> . \<minor> . \<patch> . IntelliSense semplifica la versione semantica visualizzando solo alcune opzioni comuni. Il primo elemento dell'elenco IntelliSense (0.4.5 nell'esempio precedente) viene considerato la versione stabile più recente del pacchetto. Il simbolo del cursore (^) corrisponde alla versione principale più recente e la tilde (~) corrisponde alla versione secondaria più recente. Vedere la Guida di [riferimento al parser della versione NPM semver](https://www.npmjs.com/package/semver) come guida alla completa espressività fornita da semver.

3. Aggiungere altre dipendenze per caricare i pacchetti grugnito-contrib \* per *Clean*, *jshint*, *Concat*, *uglify*e *Watch* come illustrato nell'esempio riportato di seguito. Non è necessario che le versioni corrispondano all'esempio.

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

4. Salvare il file *package.json*.

I pacchetti per ogni `devDependencies` elemento vengono scaricati insieme a tutti i file necessari per ogni pacchetto. È possibile trovare i file del pacchetto nella directory *node_modules* abilitando il pulsante **Mostra tutti i file** in **Esplora soluzioni**.

![node_modules grugnito](using-grunt/_static/node-modules.png)

> [!NOTE]
> Se necessario, è possibile ripristinare manualmente le dipendenze in **Esplora soluzioni** facendo clic con il pulsante destro del mouse su `Dependencies\NPM` e selezionando l'opzione di menu **Ripristina pacchetti** .

![ripristinare i pacchetti](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Configurazione di grugnito

Il grugnito viene configurato usando un manifesto denominato *Gruntfile.js* che definisce, carica e registra le attività che possono essere eseguite manualmente o configurate per essere eseguite automaticamente in base agli eventi in Visual Studio.

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi**  >  **nuovo elemento**. Selezionare il modello di elemento **file JavaScript** , modificare il nome in *Gruntfile.js*, quindi fare clic sul pulsante **Aggiungi** .

1. Aggiungere il codice seguente per *Gruntfile.js*. La `initConfig` funzione imposta le opzioni per ogni pacchetto e il resto del modulo carica e registra le attività.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. All'interno della `initConfig` funzione aggiungere le opzioni per l' `clean` attività, come illustrato nell'esempio *Gruntfile.js* riportato di seguito. L' `clean` attività accetta una matrice di stringhe di directory. Questa attività rimuove i file da *wwwroot/lib* e rimuove l'intera directory */Temp* .

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Sotto la `initConfig` funzione, aggiungere una chiamata a `grunt.loadNpmTasks` . Questo renderà l'attività eseguibile da Visual Studio.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. Salva *Gruntfile.js*. Il file dovrebbe avere un aspetto simile a quello riportato nella schermata seguente.

    ![gruntfile iniziale](using-grunt/_static/gruntfile-js-initial.png)

1. Fare clic con il pulsante destro del mouse su *Gruntfile.js* e scegliere **Esplora esecuzioni attività** dal menu di scelta rapida. Si aprirà la finestra di **esplorazione di Task Runner** .

    ![menu Esplora attività](using-grunt/_static/task-runner-explorer-menu.png)

1. Verificare che venga `clean` visualizzato sotto **attività** in **Esplora attività**.

    ![Elenco attività di Esplora attività](using-grunt/_static/task-runner-explorer-tasks.png)

1. Fare clic con il pulsante destro del mouse sull'attività Pulisci e scegliere **Esegui** dal menu di scelta rapida. In una finestra di comando viene visualizzato lo stato dell'attività.

    ![attività di esecuzione pulita di Esplora attività](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Non sono ancora presenti file o directory da pulire. Se lo si desidera, è possibile crearli manualmente nella Esplora soluzioni e quindi eseguire l'attività pulita come test.

1. Nella `initConfig` funzione aggiungere una voce per `concat` usando il codice seguente.

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
    > La `all` proprietà nel codice precedente è il nome di una destinazione. Le destinazioni vengono usate in alcune attività per consentire più ambienti di compilazione. È possibile visualizzare le destinazioni predefinite usando IntelliSense o assegnarne di personalizzate.

1. Aggiungere l' `jshint` attività usando il codice seguente.

    L' `code-quality` utilità jshint viene eseguita su tutti i file JavaScript trovati nella directory *Temp* .

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > L'opzione "-W069" è un errore prodotto da jshint quando JavaScript usa la sintassi di parentesi quadre per assegnare una proprietà anziché la notazione del punto, ovvero `Tastes["Sweet"]` anziché `Tastes.Sweet` . L'opzione disattiva l'avviso per consentire al resto del processo di continuare.

1. Aggiungere l' `uglify` attività usando il codice seguente.

    L'attività minimizza il file di *combined.js* trovato nella directory Temp e crea il file dei risultati in wwwroot/lib seguendo la convenzione di denominazione standard * \<file name\>.min.js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. Sotto la chiamata a `grunt.loadNpmTasks` che carica `grunt-contrib-clean` , includere la stessa chiamata per jshint, Concat e uglify usando il codice riportato di seguito.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. Salva *Gruntfile.js*. Il file dovrebbe avere un aspetto simile all'esempio seguente.

    ![esempio di file grugnito completo](using-grunt/_static/gruntfile-js-complete.png)

1. Si noti che nell'elenco attività di **Esplora attività** sono incluse le `clean` `concat` attività, `jshint` e `uglify` . Eseguire ogni attività nell'ordine e osservare i risultati in **Esplora soluzioni**. Ogni attività deve essere eseguita senza errori.

    ![esecuzione di ogni attività in Esplora esecuzioni](using-grunt/_static/task-runner-explorer-run-each-task.png)

    L'attività Concat crea un nuovo file di *combined.js* e lo inserisce nella directory Temp. L' `jshint` attività viene semplicemente eseguita e non produce output. L' `uglify` attività crea un nuovo file di *combined.min.js* e lo inserisce in *wwwroot/lib*. Al termine, la soluzione dovrebbe avere un aspetto simile alla schermata seguente:

    ![Esplora soluzioni dopo tutte le attività](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Per ulteriori informazioni sulle opzioni per ogni pacchetto, visitare [https://www.npmjs.com/](https://www.npmjs.com/) e cercare il nome del pacchetto nella casella Cerca della pagina principale. Ad esempio, è possibile cercare il pacchetto grugnito-contrib-clean per ottenere un collegamento alla documentazione in cui vengono illustrati tutti i relativi parametri.

### <a name="all-together-now"></a>Riepilogo

Usare il metodo grugnito `registerTask()` per eseguire una serie di attività in una sequenza specifica. Ad esempio, per eseguire i passaggi di esempio precedenti nell'ordine Pulisci-> Concat-> jshint-> uglify, aggiungere il codice seguente al modulo. Il codice deve essere aggiunto allo stesso livello delle chiamate a loadNpmTasks (), all'esterno di initConfig.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

La nuova attività viene visualizzata in Esplora attività in attività alias. È possibile fare clic con il pulsante destro del mouse ed eseguirlo esattamente come per le altre attività. L' `all` attività eseguirà `clean` , `concat` `jshint` e `uglify` , nell'ordine.

![attività con alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Controllo per la ricerca di modifiche

Un' `watch` attività consente di tenere sotto controllo i file e le directory. Il controllo attiva automaticamente le attività in caso di rilevamento delle modifiche. Aggiungere il codice seguente a initConfig per controllare le modifiche apportate ai \* file con estensione js nella directory typescript. Se viene modificato un file JavaScript, `watch` eseguirà l' `all` attività.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Aggiungere una chiamata a `loadNpmTasks()` per visualizzare l' `watch` attività nella finestra di esplorazione di Task Runner.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Fare clic con il pulsante destro del mouse sull'attività Watch in Esplora attività e scegliere Esegui dal menu di scelta rapida. La finestra di comando che mostra l'attività Watch in esecuzione visualizzerà "Waiting..." Messaggio. Aprire uno dei file TypeScript, aggiungere uno spazio e quindi salvare il file. In questo modo l'attività Watch viene attivata e vengono attivate le altre attività da eseguire nell'ordine. La schermata seguente mostra un'esecuzione di esempio.

![output attività in esecuzione](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Associazione agli eventi di Visual Studio

A meno che non si desideri avviare manualmente le attività ogni volta che si lavora in Visual Studio, associare le attività a **prima della compilazione**, **dopo la compilazione**, la **pulizia**e il progetto degli eventi **aperti** .

`watch`Eseguire l'associazione in modo che venga eseguita ogni volta che si apre Visual Studio. In Task Runner Explorer fare clic con il pulsante destro del mouse sull'attività Watch e scegliere **bindings**  >  **progetto Open** dal menu di scelta rapida.

![associa un'attività all'apertura del progetto](using-grunt/_static/bindings-project-open.png)

Scaricare e ricaricare il progetto. Quando il progetto viene caricato nuovamente, l'attività Watch viene avviata automaticamente.

## <a name="summary"></a>Riepilogo

Grugnito è un potente strumento di esecuzione delle attività che può essere usato per automatizzare la maggior parte delle attività di compilazione client. Grugni si avvale di NPM per distribuire i pacchetti e include funzionalità di integrazione degli strumenti con Visual Studio. Esplora attività di Visual Studio rileva le modifiche apportate ai file di configurazione e offre una comoda interfaccia per eseguire attività, visualizzare attività in esecuzione e associare attività agli eventi di Visual Studio.
