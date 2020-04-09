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
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="e7750-103">Usare Grunt in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7750-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="e7750-104">Grunt è un task runner JavaScript che automatizza la minimizzazione degli script, la compilazione TypeScript, gli strumenti "lint" di qualità del codice, i preprocessori CSS e praticamente qualsiasi lavoro ripetitivo che deve essere fatto per supportare lo sviluppo client.</span><span class="sxs-lookup"><span data-stu-id="e7750-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="e7750-105">Grunt è completamente supportato in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7750-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="e7750-106">Questo esempio usa un progetto ASP.NET Core vuoto come punto di partenza, per mostrare come automatizzare il processo di compilazione client da zero.</span><span class="sxs-lookup"><span data-stu-id="e7750-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="e7750-107">L'esempio finito pulisce la directory di distribuzione di destinazione, combina i file JavaScript, controlla la qualità del codice, condensa il contenuto del file JavaScript e viene distribuito nella radice dell'applicazione Web.</span><span class="sxs-lookup"><span data-stu-id="e7750-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="e7750-108">Useremo i seguenti pacchetti:</span><span class="sxs-lookup"><span data-stu-id="e7750-108">We will use the following packages:</span></span>

* <span data-ttu-id="e7750-109">**grugnito**: Il pacchetto corridore task Grunt.</span><span class="sxs-lookup"><span data-stu-id="e7750-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="e7750-110">**grunt-contrib-clean**: Un plugin che rimuove file o directory.</span><span class="sxs-lookup"><span data-stu-id="e7750-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="e7750-111">**grunt-contrib-jshint**: Un plugin che esamina la qualità del codice JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e7750-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="e7750-112">**grunt-contrib-concat**: Un plugin che unisce i file in un unico file.</span><span class="sxs-lookup"><span data-stu-id="e7750-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="e7750-113">**grunt-contrib-uglify**: Un plugin che minifica JavaScript per ridurre le dimensioni.</span><span class="sxs-lookup"><span data-stu-id="e7750-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="e7750-114">**grunt-contrib-watch**: Un plugin che guarda l'attività dei file.</span><span class="sxs-lookup"><span data-stu-id="e7750-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="e7750-115">Preparazione dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="e7750-115">Preparing the application</span></span>

<span data-ttu-id="e7750-116">Per iniziare, impostare una nuova applicazione Web vuota e aggiungere file di esempio TypeScript.</span><span class="sxs-lookup"><span data-stu-id="e7750-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="e7750-117">I file TypeScript vengono compilati automaticamente in JavaScript utilizzando le impostazioni predefinite di Visual Studio e saranno la nostra materia prima da elaborare utilizzando Grunt.</span><span class="sxs-lookup"><span data-stu-id="e7750-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="e7750-118">In Visual Studio creare `ASP.NET Web Application`un nuovo file .</span><span class="sxs-lookup"><span data-stu-id="e7750-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="e7750-119">Nella finestra di dialogo **Nuovo progetto ASP.NET,** selezionare il ASP.NET modello **Core Empty** e fare clic sul pulsante OK.</span><span class="sxs-lookup"><span data-stu-id="e7750-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="e7750-120">In Esplora soluzioni esaminare la struttura del progetto.</span><span class="sxs-lookup"><span data-stu-id="e7750-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="e7750-121">La `\src` cartella `wwwroot` include `Dependencies` nodi vuoti e vuoti.</span><span class="sxs-lookup"><span data-stu-id="e7750-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![soluzione web vuota](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="e7750-123">Aggiungere una nuova `TypeScript` cartella denominata alla directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="e7750-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="e7750-124">Prima di aggiungere file, assicurarsi che Visual Studio abbia l'opzione 'compila in caso di salvataggio' per i file TypeScript selezionata.</span><span class="sxs-lookup"><span data-stu-id="e7750-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="e7750-125">Passare a**Opzioni** >  **opzioni** > **Testo Editor** > **Tipodi** > **progetto**:</span><span class="sxs-lookup"><span data-stu-id="e7750-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![opzioni impostazione compilazione automatica dei file TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="e7750-127">Fare clic `TypeScript` con il pulsante destro del mouse sulla directory e scegliere **Aggiungi > nuovo elemento** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="e7750-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="e7750-128">Selezionare l'elemento di **file JavaScript** e denominare il file *Tastes.ts* (notare l'estensione \*.ts).</span><span class="sxs-lookup"><span data-stu-id="e7750-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="e7750-129">Copiare la riga di codice TypeScript riportato di seguito nel file (quando si salva, verrà visualizzato un nuovo file *Tastes.js* con l'origine JavaScript).</span><span class="sxs-lookup"><span data-stu-id="e7750-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="e7750-130">Aggiungere un secondo file alla directory `Food.ts` **TypeScript** e denominarlo .</span><span class="sxs-lookup"><span data-stu-id="e7750-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="e7750-131">Copiare il codice seguente nel file.</span><span class="sxs-lookup"><span data-stu-id="e7750-131">Copy the code below into the file.</span></span>

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

## <a name="configuring-npm"></a><span data-ttu-id="e7750-132">Configurazione di NPM</span><span class="sxs-lookup"><span data-stu-id="e7750-132">Configuring NPM</span></span>

<span data-ttu-id="e7750-133">Configurare quindi NPM per scaricare le attività grunt e grunt.</span><span class="sxs-lookup"><span data-stu-id="e7750-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="e7750-134">In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi > nuovo elemento** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="e7750-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="e7750-135">Selezionare l'elemento del file di **configurazione NPM,** lasciare il nome predefinito, *package.json*, quindi fare clic sul pulsante **Aggiungi.**</span><span class="sxs-lookup"><span data-stu-id="e7750-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="e7750-136">Nel file *package.json,* `devDependencies` all'interno delle parentesi graffe dell'oggetto, immettere "grunt".</span><span class="sxs-lookup"><span data-stu-id="e7750-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="e7750-137">Selezionare `grunt` dall'elenco Intellisense e premere INVIO.</span><span class="sxs-lookup"><span data-stu-id="e7750-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="e7750-138">Visual Studio citerà il nome del pacchetto grunt e aggiungerà i due punti.</span><span class="sxs-lookup"><span data-stu-id="e7750-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="e7750-139">A destra dei due punti, selezionare l'ultima versione stabile del pacchetto `Ctrl-Space` dall'inizio dell'elenco Intellisense (premere se Intellisense non viene visualizzato).</span><span class="sxs-lookup"><span data-stu-id="e7750-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![intellisense grugnito](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="e7750-141">NPM utilizza [il controllo delle versioni semantico](https://semver.org/) per organizzare le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e7750-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="e7750-142">Il controllo delle versioni semantico, noto anche come \<SemVer, identifica i pacchetti con lo schema di numerazione principale>. \<> minori. \<> patch.</span><span class="sxs-lookup"><span data-stu-id="e7750-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="e7750-143">Intellisense semplifica il controllo delle versioni semantico mostrando solo alcune scelte comuni.</span><span class="sxs-lookup"><span data-stu-id="e7750-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="e7750-144">L'elemento superiore nell'elenco Intellisense (0.4.5 nell'esempio precedente) viene considerato l'ultima versione stabile del pacchetto.</span><span class="sxs-lookup"><span data-stu-id="e7750-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="e7750-145">Il simbolo dell'accento circonflesso (-) corrisponde alla versione principale più recente e la tilde (-) corrisponde alla versione secondaria più recente.</span><span class="sxs-lookup"><span data-stu-id="e7750-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="e7750-146">Vedere il riferimento al parser della [versione semver NPM](https://www.npmjs.com/package/semver) come guida all'espressività completa fornita da SemVer.</span><span class="sxs-lookup"><span data-stu-id="e7750-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="e7750-147">Aggiungere altre dipendenze\* per caricare grunt-contrib- packages for *clean*, *jshint*, *concat*, *uglify*e *watch* come illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="e7750-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="e7750-148">Non è necessario che le versioni corrispondano all'esempio.</span><span class="sxs-lookup"><span data-stu-id="e7750-148">The versions don't need to match the example.</span></span>

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

4. <span data-ttu-id="e7750-149">Salvare il file *package.json.*</span><span class="sxs-lookup"><span data-stu-id="e7750-149">Save the *package.json* file.</span></span>

<span data-ttu-id="e7750-150">Verranno scaricati i pacchetti per ogni `devDependencies` elemento, insieme a tutti i file necessari per ogni pacchetto.</span><span class="sxs-lookup"><span data-stu-id="e7750-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="e7750-151">È possibile trovare i file del pacchetto nella directory *node_modules* attivando il pulsante **Mostra tutti i file** in Esplora **soluzioni**.</span><span class="sxs-lookup"><span data-stu-id="e7750-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![node_modules grugniti](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="e7750-153">Se necessario, è possibile ripristinare manualmente le dipendenze in `Dependencies\NPM` **Esplora soluzioni** facendo clic con il pulsante destro del mouse e selezionando l'opzione di menu Ripristina **pacchetti.**</span><span class="sxs-lookup"><span data-stu-id="e7750-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![ripristinare i pacchetti](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="e7750-155">Configurazione di Grunt</span><span class="sxs-lookup"><span data-stu-id="e7750-155">Configuring Grunt</span></span>

<span data-ttu-id="e7750-156">Grunt viene configurato utilizzando un manifesto denominato *Gruntfile.js* che definisce, carica e registra le attività che possono essere eseguite manualmente o configurate per l'esecuzione automatica in base agli eventi in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7750-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="e7750-157">Fare clic con il pulsante destro del mouse sul progetto e **scegliere Aggiungi** > **nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="e7750-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="e7750-158">Selezionare il modello di elemento **File JavaScript,** modificare il nome in *Gruntfile.js*e fare clic sul pulsante **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="e7750-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="e7750-159">Aggiungere il codice seguente a *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="e7750-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="e7750-160">La `initConfig` funzione imposta le opzioni per ogni pacchetto e il resto del modulo carica e registra le attività.</span><span class="sxs-lookup"><span data-stu-id="e7750-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="e7750-161">All'interno della `initConfig` funzione, `clean` aggiungere le opzioni per l'attività come illustrato nell'esempio *Gruntfile.js* riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="e7750-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="e7750-162">L'attività `clean` accetta una matrice di stringhe di directory.</span><span class="sxs-lookup"><span data-stu-id="e7750-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="e7750-163">Questa attività rimuove i file da *wwwroot/lib* e rimuove l'intera directory */temp.*</span><span class="sxs-lookup"><span data-stu-id="e7750-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="e7750-164">Sotto `initConfig` la funzione, aggiungere `grunt.loadNpmTasks`una chiamata a .</span><span class="sxs-lookup"><span data-stu-id="e7750-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="e7750-165">In questo modo l'attività eseguebile da Visual Studio.This will make the task runnable from Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7750-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="e7750-166">Salvare *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="e7750-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="e7750-167">Il file dovrebbe essere simile alla schermata qui sotto.</span><span class="sxs-lookup"><span data-stu-id="e7750-167">The file should look something like the screenshot below.</span></span>

    ![gruntfile iniziale](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="e7750-169">Fare clic con il pulsante destro del mouse su *Gruntfile.js* e selezionare **Task Runner Explorer** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="e7750-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="e7750-170">Si aprirà la finestra **Task Runner Explorer.**</span><span class="sxs-lookup"><span data-stu-id="e7750-170">The **Task Runner Explorer** window will open.</span></span>

    ![task runner explorer menu](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="e7750-172">Verificare `clean` che viene visualizzato in **Attività** in **Task Runner Explorer**.</span><span class="sxs-lookup"><span data-stu-id="e7750-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![elenco delle attività di esploratori runner](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="e7750-174">Fare clic con il pulsante destro del mouse sull'attività pulita e scegliere **Esegui** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="e7750-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="e7750-175">Una finestra di comando visualizza lo stato di avanzamento dell'attività.</span><span class="sxs-lookup"><span data-stu-id="e7750-175">A command window displays progress of the task.</span></span>

    ![task runner explorer eseguire attività pulita](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="e7750-177">Non ci sono file o directory da pulire ancora.</span><span class="sxs-lookup"><span data-stu-id="e7750-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="e7750-178">Se lo si desidera, è possibile crearli manualmente in Esplora soluzioni e quindi eseguire l'attività pulita come test.</span><span class="sxs-lookup"><span data-stu-id="e7750-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="e7750-179">Nella `initConfig` funzione aggiungere una `concat` voce per l'utilizzo del codice seguente.</span><span class="sxs-lookup"><span data-stu-id="e7750-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="e7750-180">La `src` matrice di proprietà elenca i file da combinare, nell'ordine in cui devono essere combinati.</span><span class="sxs-lookup"><span data-stu-id="e7750-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="e7750-181">La `dest` proprietà assegna il percorso al file combinato prodotto.</span><span class="sxs-lookup"><span data-stu-id="e7750-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="e7750-182">La `all` proprietà nel codice precedente è il nome di una destinazione.</span><span class="sxs-lookup"><span data-stu-id="e7750-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="e7750-183">Le destinazioni vengono usate in alcune attività Grunt per consentire più ambienti di compilazione.</span><span class="sxs-lookup"><span data-stu-id="e7750-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="e7750-184">È possibile visualizzare le destinazioni predefinite usando IntelliSense o assegnarne di proprie.</span><span class="sxs-lookup"><span data-stu-id="e7750-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="e7750-185">Aggiungere `jshint` l'attività utilizzando il codice riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="e7750-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="e7750-186">L'utilità `code-quality` jshint viene eseguita su tutti i file JavaScript presenti nella directory *temp.*</span><span class="sxs-lookup"><span data-stu-id="e7750-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="e7750-187">L'opzione "-W069" è un errore generato da jshint quando JavaScript utilizza la sintassi `Tastes["Sweet"]` delle `Tastes.Sweet`parentesi quadre per assegnare una proprietà anziché la notazione del punto, ovvero anziché .</span><span class="sxs-lookup"><span data-stu-id="e7750-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="e7750-188">L'opzione disattiva l'avviso per consentire al resto del processo di continuare.</span><span class="sxs-lookup"><span data-stu-id="e7750-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="e7750-189">Aggiungere `uglify` l'attività utilizzando il codice riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="e7750-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="e7750-190">L'attività minifica il file *combined.js* che si trova nella directory temp e crea il file dei risultati in wwwroot/lib seguendo il nome \* \<\>\* del file della convenzione di denominazione standard .min.js .</span><span class="sxs-lookup"><span data-stu-id="e7750-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="e7750-191">Sotto la `grunt.loadNpmTasks` chiamata `grunt-contrib-clean`a tale caricamento , includere la stessa chiamata per jshint, concat e uglify utilizzando il codice riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="e7750-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="e7750-192">Salvare *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="e7750-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="e7750-193">Il file dovrebbe essere simile all'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="e7750-193">The file should look something like the example below.</span></span>

    ![esempio di file grugnito completo](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="e7750-195">Si noti che l'elenco `concat` `jshint` Attività `uglify` di **Task Runner Explorer** include `clean`, e attività.</span><span class="sxs-lookup"><span data-stu-id="e7750-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="e7750-196">Eseguire ogni attività in ordine e osservare i risultati in **Esplora soluzioni**.</span><span class="sxs-lookup"><span data-stu-id="e7750-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="e7750-197">Ogni attività deve essere eseguita senza errori.</span><span class="sxs-lookup"><span data-stu-id="e7750-197">Each task should run without errors.</span></span>

    ![task runner explorer eseguire ogni attività](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="e7750-199">L'attività concat crea un nuovo file *combined.js* e lo inserisce nella directory temp.</span><span class="sxs-lookup"><span data-stu-id="e7750-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="e7750-200">L'attività `jshint` viene eseguita semplicemente e non produce output.</span><span class="sxs-lookup"><span data-stu-id="e7750-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="e7750-201">L'attività `uglify` crea un nuovo file *combined.min.js* e lo inserisce in *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="e7750-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="e7750-202">Al termine, la soluzione dovrebbe essere simile alla schermata seguente:On completion, the solution should look something like the screenshot below:</span><span class="sxs-lookup"><span data-stu-id="e7750-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![Esplora soluzioni dopo tutte le attività](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="e7750-204">Per ulteriori informazioni sulle opzioni per [https://www.npmjs.com/](https://www.npmjs.com/) ogni pacchetto, visitare e cercare il nome del pacchetto nella casella di ricerca nella pagina principale.</span><span class="sxs-lookup"><span data-stu-id="e7750-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="e7750-205">Ad esempio, è possibile cercare il pacchetto grunt-contrib-clean per ottenere un collegamento alla documentazione che spiega tutti i relativi parametri.</span><span class="sxs-lookup"><span data-stu-id="e7750-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="e7750-206">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="e7750-206">All together now</span></span>

<span data-ttu-id="e7750-207">Utilizzare il metodo `registerTask()` Grunt per eseguire una serie di attività in una determinata sequenza.</span><span class="sxs-lookup"><span data-stu-id="e7750-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="e7750-208">Ad esempio, per eseguire i passaggi di esempio precedenti nell'ordine clean -> concat -> > > possibile aggiungere il codice riportato di seguito al modulo.</span><span class="sxs-lookup"><span data-stu-id="e7750-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="e7750-209">Il codice deve essere aggiunto allo stesso livello delle chiamate loadNpmTasks(), all'esterno di initConfig.</span><span class="sxs-lookup"><span data-stu-id="e7750-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="e7750-210">La nuova attività viene visualizzata in Task Runner Explorer in Alias Tasks.</span><span class="sxs-lookup"><span data-stu-id="e7750-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="e7750-211">È possibile fare clic con il pulsante destro del mouse ed eseguirlo come in altre attività.</span><span class="sxs-lookup"><span data-stu-id="e7750-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="e7750-212">`all` L'attività `clean`verrà `concat` `jshint` eseguita in ordine e , e `uglify`, .</span><span class="sxs-lookup"><span data-stu-id="e7750-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![alias compiti grunt](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="e7750-214">Controllo per la ricerca di modifiche</span><span class="sxs-lookup"><span data-stu-id="e7750-214">Watching for changes</span></span>

<span data-ttu-id="e7750-215">Un'attività `watch` tiene d'occhio file e directory.</span><span class="sxs-lookup"><span data-stu-id="e7750-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="e7750-216">L'orologio attiva automaticamente le attività se rileva le modifiche.</span><span class="sxs-lookup"><span data-stu-id="e7750-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="e7750-217">Aggiungere il codice seguente a initConfig \*per controllare le modifiche apportate ai file con estensione js nella directory TypeScript.</span><span class="sxs-lookup"><span data-stu-id="e7750-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="e7750-218">Se un file JavaScript `watch` viene `all` modificato, eseguirà l'attività.</span><span class="sxs-lookup"><span data-stu-id="e7750-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="e7750-219">Aggiungere una `loadNpmTasks()` chiamata a `watch` per visualizzare l'attività in Task Runner Explorer.</span><span class="sxs-lookup"><span data-stu-id="e7750-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="e7750-220">Fare clic con il pulsante destro del mouse sull'attività di controllo in Task Runner Explorer e scegliere Esegui dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="e7750-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="e7750-221">Nella finestra di comando che mostra l'attività di espressioni di controllo in esecuzione verrà visualizzato un "In attesa..." Messaggio.</span><span class="sxs-lookup"><span data-stu-id="e7750-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="e7750-222">Aprire uno dei file TypeScript, aggiungere uno spazio e quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="e7750-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="e7750-223">Ciò attiverà l'attività di espressioni di controllo e attiverà l'esecuzione delle altre attività nell'ordine.</span><span class="sxs-lookup"><span data-stu-id="e7750-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="e7750-224">La schermata seguente mostra un'esecuzione di esempio.</span><span class="sxs-lookup"><span data-stu-id="e7750-224">The screenshot below shows a sample run.</span></span>

![output delle attività in esecuzione](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="e7750-226">Associazione agli eventi di Visual StudioBinding to Visual Studio events</span><span class="sxs-lookup"><span data-stu-id="e7750-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="e7750-227">A meno che non si desideri avviare manualmente le attività ogni volta che si lavora in Visual Studio, associare le attività agli eventi **Prima della compilazione**, **Dopo la compilazione**, **Clean**e **Project Open** .</span><span class="sxs-lookup"><span data-stu-id="e7750-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="e7750-228">Associare in modo che venga eseguito ogni volta che viene aperto Visual Studio.Bind `watch` so that it runs every time Visual Studio opens.</span><span class="sxs-lookup"><span data-stu-id="e7750-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="e7750-229">In Task Runner Explorer, fare clic con il pulsante destro del mouse sull'attività di controllo e selezionare **Binding apertura** > **progetto** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="e7750-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![associare un'attività all'apertura del progetto](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="e7750-231">Scaricare e ricaricare il progetto.</span><span class="sxs-lookup"><span data-stu-id="e7750-231">Unload and reload the project.</span></span> <span data-ttu-id="e7750-232">Quando il progetto viene caricato nuovamente, l'attività di espressioni di controllo viene avviata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="e7750-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="e7750-233">Summary</span><span class="sxs-lookup"><span data-stu-id="e7750-233">Summary</span></span>

<span data-ttu-id="e7750-234">Grunt è un potente corridore di attività che può essere utilizzato per automatizzare la maggior parte delle attività di creazione del client.</span><span class="sxs-lookup"><span data-stu-id="e7750-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="e7750-235">Grunt sfrutta NPM per distribuire i propri pacchetti e l'integrazione degli strumenti con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7750-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="e7750-236">Task Runner Explorer di Visual Studio rileva le modifiche ai file di configurazione e fornisce una comoda interfaccia per eseguire attività, visualizzare attività in esecuzione e associare attività agli eventi di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7750-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
