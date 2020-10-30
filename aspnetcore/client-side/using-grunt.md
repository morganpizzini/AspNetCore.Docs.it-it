---
title: USA Grugnio in ASP.NET Core
author: rick-anderson
description: USA Grugnio in ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: client-side/using-grunt
ms.openlocfilehash: 374c23f440dcf301b3a1e1e9e6684dd050f218c6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054554"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="5a1e6-103">USA Grugnio in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a1e6-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="5a1e6-104">Grugnito è un task Runner JavaScript che consente di automatizzare lo script minification, la compilazione di TypeScript, gli strumenti di qualità del codice "lanugine", i preprocessori CSS e solo le faccende ripetitive che richiedono per supportare lo sviluppo di client.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="5a1e6-105">Grugnito è completamente supportato in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="5a1e6-106">Questo esempio usa un progetto di ASP.NET Core vuoto come punto di partenza, per illustrare come automatizzare il processo di compilazione client da zero.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="5a1e6-107">L'esempio completato pulisce la directory di distribuzione di destinazione, combina i file JavaScript, controlla la qualità del codice, condensa il contenuto del file JavaScript e lo distribuisce nella radice dell'applicazione Web.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="5a1e6-108">Si useranno i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="5a1e6-108">We will use the following packages:</span></span>

* <span data-ttu-id="5a1e6-109">**grugnito** : il pacchetto di attività Runner.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-109">**grunt** : The Grunt task runner package.</span></span>

* <span data-ttu-id="5a1e6-110">**grugnito-contrib-clean** : un plug-in che rimuove file o directory.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-110">**grunt-contrib-clean** : A plugin that removes files or directories.</span></span>

* <span data-ttu-id="5a1e6-111">**grugnito-contrib-jshint** : un plug-in che esamina la qualità del codice JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-111">**grunt-contrib-jshint** : A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="5a1e6-112">**grugnito-contrib-Concat** : un plug-in che unisce i file in un singolo file.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-112">**grunt-contrib-concat** : A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="5a1e6-113">**grugnito-contrib-uglify** : un plug-in che minimizza JavaScript per ridurre le dimensioni.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-113">**grunt-contrib-uglify** : A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="5a1e6-114">**grugnito-contrib-Watch** : un plug-in che controlla l'attività del file.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-114">**grunt-contrib-watch** : A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="5a1e6-115">Preparazione dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="5a1e6-115">Preparing the application</span></span>

<span data-ttu-id="5a1e6-116">Per iniziare, impostare una nuova applicazione Web vuota e aggiungere i file di esempio TypeScript.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="5a1e6-117">I file TypeScript vengono compilati automaticamente in JavaScript usando le impostazioni predefinite di Visual Studio e saranno il materiale grezzo per l'elaborazione con grugni.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="5a1e6-118">In Visual Studio creare un nuovo `ASP.NET Web Application` .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="5a1e6-119">Nella finestra di dialogo **nuovo progetto ASP.NET** selezionare il modello ASP.NET Core **vuoto** e fare clic sul pulsante OK.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="5a1e6-120">Nella Esplora soluzioni esaminare la struttura del progetto.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="5a1e6-121">La `\src` cartella include `wwwroot` nodi vuoti e `Dependencies` .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![soluzione Web vuota](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="5a1e6-123">Aggiungere una nuova cartella denominata `TypeScript` alla directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="5a1e6-124">Prima di aggiungere i file, assicurarsi che in Visual Studio sia selezionata l'opzione ' compila al salvataggio ' per i file TypeScript.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="5a1e6-125">Passare a **strumenti**  >  **Opzioni**  >  **editor di testo**  >  **typescript**  >  **progetto** :</span><span class="sxs-lookup"><span data-stu-id="5a1e6-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project** :</span></span>

    ![opzioni di impostazione della compilazione automatica dei file TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="5a1e6-127">Fare clic con il pulsante destro del mouse sulla `TypeScript` Directory e scegliere **Aggiungi > nuovo elemento** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="5a1e6-128">Selezionare l'elemento di **file JavaScript** e denominare il file *gusti. TS* (si noti l' \* estensione TS).</span><span class="sxs-lookup"><span data-stu-id="5a1e6-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="5a1e6-129">Copiare la riga di codice TypeScript seguente nel file (quando si salva, viene visualizzato un nuovo file di *Tastes.js* con l'origine JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5a1e6-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="5a1e6-130">Aggiungere un secondo file alla directory **typescript** e denominarlo `Food.ts` .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="5a1e6-131">Copiare il codice seguente nel file.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-131">Copy the code below into the file.</span></span>

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

## <a name="configuring-npm"></a><span data-ttu-id="5a1e6-132">Configurazione di NPM</span><span class="sxs-lookup"><span data-stu-id="5a1e6-132">Configuring NPM</span></span>

<span data-ttu-id="5a1e6-133">Successivamente, configurare NPM per scaricare le attività grugnite e grugnito.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="5a1e6-134">Nel Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **aggiungi > nuovo elemento** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="5a1e6-135">Selezionare l'elemento del **file di configurazione NPM** , lasciare il nome predefinito *package.jsin* e fare clic sul pulsante **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-135">Select the **NPM configuration file** item, leave the default name, *package.json* , and click the **Add** button.</span></span>

2. <span data-ttu-id="5a1e6-136">Nella *package.jssu* file, all'interno delle `devDependencies` parentesi graffe dell'oggetto, immettere "grugnito".</span><span class="sxs-lookup"><span data-stu-id="5a1e6-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="5a1e6-137">Selezionare `grunt` dall'elenco IntelliSense e premere il tasto INVIO.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="5a1e6-138">In Visual Studio viene citato il nome del pacchetto grugnito e vengono aggiunti i due punti.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="5a1e6-139">A destra dei due punti, selezionare la versione stabile più recente del pacchetto dalla parte superiore dell'elenco IntelliSense (premere `Ctrl-Space` Se IntelliSense non viene visualizzato).</span><span class="sxs-lookup"><span data-stu-id="5a1e6-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![IntelliSense grugnito](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="5a1e6-141">NPM usa il [controllo delle versioni semantico](https://semver.org/) per organizzare le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="5a1e6-142">Il controllo delle versioni semantico, noto anche come SemVer, identifica i pacchetti con lo schema di numerazione \<major> . \<minor> . \<patch> . IntelliSense semplifica la versione semantica visualizzando solo alcune opzioni comuni.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>. Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="5a1e6-143">Il primo elemento dell'elenco IntelliSense (0.4.5 nell'esempio precedente) viene considerato la versione stabile più recente del pacchetto.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-143">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="5a1e6-144">Il simbolo del cursore (^) corrisponde alla versione principale più recente e la tilde (~) corrisponde alla versione secondaria più recente.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-144">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="5a1e6-145">Vedere la Guida di [riferimento al parser della versione NPM semver](https://www.npmjs.com/package/semver) come guida alla completa espressività fornita da semver.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-145">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="5a1e6-146">Aggiungere altre dipendenze per caricare i pacchetti grugnito-contrib \* per *Clean* , *jshint* , *Concat* , *uglify* e *Watch* come illustrato nell'esempio riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-146">Add more dependencies to load grunt-contrib-\* packages for *clean* , *jshint* , *concat* , *uglify* , and *watch* as shown in the example below.</span></span> <span data-ttu-id="5a1e6-147">Non è necessario che le versioni corrispondano all'esempio.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-147">The versions don't need to match the example.</span></span>

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

4. <span data-ttu-id="5a1e6-148">Salvare il file *package.json* .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-148">Save the *package.json* file.</span></span>

<span data-ttu-id="5a1e6-149">I pacchetti per ogni `devDependencies` elemento vengono scaricati insieme a tutti i file necessari per ogni pacchetto.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-149">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="5a1e6-150">È possibile trovare i file del pacchetto nella directory *node_modules* abilitando il pulsante **Mostra tutti i file** in **Esplora soluzioni** .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-150">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer** .</span></span>

![node_modules grugnito](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="5a1e6-152">Se necessario, è possibile ripristinare manualmente le dipendenze in **Esplora soluzioni** facendo clic con il pulsante destro del mouse su `Dependencies\NPM` e selezionando l'opzione di menu **Ripristina pacchetti** .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-152">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![ripristinare i pacchetti](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="5a1e6-154">Configurazione di grugnito</span><span class="sxs-lookup"><span data-stu-id="5a1e6-154">Configuring Grunt</span></span>

<span data-ttu-id="5a1e6-155">Il grugnito viene configurato usando un manifesto denominato *Gruntfile.js* che definisce, carica e registra le attività che possono essere eseguite manualmente o configurate per essere eseguite automaticamente in base agli eventi in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-155">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="5a1e6-156">Fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi**  >  **nuovo elemento** .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-156">Right-click the project and select **Add** > **New Item** .</span></span> <span data-ttu-id="5a1e6-157">Selezionare il modello di elemento **file JavaScript** , modificare il nome in *Gruntfile.js* , quindi fare clic sul pulsante **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-157">Select the **JavaScript File** item template, change the name to *Gruntfile.js* , and click the **Add** button.</span></span>

1. <span data-ttu-id="5a1e6-158">Aggiungere il codice seguente per *Gruntfile.js* .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-158">Add the following code to *Gruntfile.js* .</span></span> <span data-ttu-id="5a1e6-159">La `initConfig` funzione imposta le opzioni per ogni pacchetto e il resto del modulo carica e registra le attività.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-159">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="5a1e6-160">All'interno della `initConfig` funzione aggiungere le opzioni per l' `clean` attività, come illustrato nell'esempio *Gruntfile.js* riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-160">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="5a1e6-161">L' `clean` attività accetta una matrice di stringhe di directory.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-161">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="5a1e6-162">Questa attività rimuove i file da *wwwroot/lib* e rimuove l'intera directory */Temp* .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-162">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="5a1e6-163">Sotto la `initConfig` funzione, aggiungere una chiamata a `grunt.loadNpmTasks` .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-163">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="5a1e6-164">Questo renderà l'attività eseguibile da Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-164">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="5a1e6-165">Salva *Gruntfile.js* .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-165">Save *Gruntfile.js* .</span></span> <span data-ttu-id="5a1e6-166">Il file dovrebbe avere un aspetto simile a quello riportato nella schermata seguente.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-166">The file should look something like the screenshot below.</span></span>

    ![gruntfile iniziale](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="5a1e6-168">Fare clic con il pulsante destro del mouse su *Gruntfile.js* e scegliere **Esplora esecuzioni attività** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-168">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="5a1e6-169">Si aprirà la finestra di **esplorazione di Task Runner** .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-169">The **Task Runner Explorer** window will open.</span></span>

    ![menu Esplora attività](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="5a1e6-171">Verificare che venga `clean` visualizzato sotto **attività** in **Esplora attività** .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-171">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer** .</span></span>

    ![Elenco attività di Esplora attività](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="5a1e6-173">Fare clic con il pulsante destro del mouse sull'attività Pulisci e scegliere **Esegui** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-173">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="5a1e6-174">In una finestra di comando viene visualizzato lo stato dell'attività.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-174">A command window displays progress of the task.</span></span>

    ![attività di esecuzione pulita di Esplora attività](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="5a1e6-176">Non sono ancora presenti file o directory da pulire.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-176">There are no files or directories to clean yet.</span></span> <span data-ttu-id="5a1e6-177">Se lo si desidera, è possibile crearli manualmente nella Esplora soluzioni e quindi eseguire l'attività pulita come test.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-177">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="5a1e6-178">Nella `initConfig` funzione aggiungere una voce per `concat` usando il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-178">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="5a1e6-179">La `src` matrice di proprietà elenca i file da combinare, nell'ordine in cui devono essere combinati.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-179">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="5a1e6-180">La `dest` proprietà assegna il percorso al file combinato prodotto.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-180">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="5a1e6-181">La `all` proprietà nel codice precedente è il nome di una destinazione.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-181">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="5a1e6-182">Le destinazioni vengono usate in alcune attività per consentire più ambienti di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-182">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="5a1e6-183">È possibile visualizzare le destinazioni predefinite usando IntelliSense o assegnarne di personalizzate.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-183">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="5a1e6-184">Aggiungere l' `jshint` attività usando il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-184">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="5a1e6-185">L' `code-quality` utilità jshint viene eseguita su tutti i file JavaScript trovati nella directory *Temp* .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-185">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="5a1e6-186">L'opzione "-W069" è un errore prodotto da jshint quando JavaScript usa la sintassi di parentesi quadre per assegnare una proprietà anziché la notazione del punto, ovvero `Tastes["Sweet"]` anziché `Tastes.Sweet` .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-186">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="5a1e6-187">L'opzione disattiva l'avviso per consentire al resto del processo di continuare.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-187">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="5a1e6-188">Aggiungere l' `uglify` attività usando il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-188">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="5a1e6-189">L'attività minimizza il file di *combined.js* trovato nella directory Temp e crea il file dei risultati in wwwroot/lib seguendo la convenzione di denominazione standard *\<file name\>.min.js* .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-189">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js* .</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="5a1e6-190">Sotto la chiamata a `grunt.loadNpmTasks` che carica `grunt-contrib-clean` , includere la stessa chiamata per jshint, Concat e uglify usando il codice riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-190">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="5a1e6-191">Salva *Gruntfile.js* .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-191">Save *Gruntfile.js* .</span></span> <span data-ttu-id="5a1e6-192">Il file dovrebbe avere un aspetto simile all'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-192">The file should look something like the example below.</span></span>

    ![esempio di file grugnito completo](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="5a1e6-194">Si noti che nell'elenco attività di **Esplora attività** sono incluse le `clean` `concat` attività, `jshint` e `uglify` .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-194">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="5a1e6-195">Eseguire ogni attività nell'ordine e osservare i risultati in **Esplora soluzioni** .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-195">Run each task in order and observe the results in **Solution Explorer** .</span></span> <span data-ttu-id="5a1e6-196">Ogni attività deve essere eseguita senza errori.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-196">Each task should run without errors.</span></span>

    ![esecuzione di ogni attività in Esplora esecuzioni](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="5a1e6-198">L'attività Concat crea un nuovo file di *combined.js* e lo inserisce nella directory Temp.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-198">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="5a1e6-199">L' `jshint` attività viene semplicemente eseguita e non produce output.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-199">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="5a1e6-200">L' `uglify` attività crea un nuovo file di *combined.min.js* e lo inserisce in *wwwroot/lib* .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-200">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib* .</span></span> <span data-ttu-id="5a1e6-201">Al termine, la soluzione dovrebbe avere un aspetto simile alla schermata seguente:</span><span class="sxs-lookup"><span data-stu-id="5a1e6-201">On completion, the solution should look something like the screenshot below:</span></span>

    ![Esplora soluzioni dopo tutte le attività](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="5a1e6-203">Per ulteriori informazioni sulle opzioni per ogni pacchetto, visitare [https://www.npmjs.com/](https://www.npmjs.com/) e cercare il nome del pacchetto nella casella Cerca della pagina principale.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-203">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="5a1e6-204">Ad esempio, è possibile cercare il pacchetto grugnito-contrib-clean per ottenere un collegamento alla documentazione in cui vengono illustrati tutti i relativi parametri.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-204">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="5a1e6-205">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="5a1e6-205">All together now</span></span>

<span data-ttu-id="5a1e6-206">Usare il metodo grugnito `registerTask()` per eseguire una serie di attività in una sequenza specifica.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-206">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="5a1e6-207">Ad esempio, per eseguire i passaggi di esempio precedenti nell'ordine Pulisci-> Concat-> jshint-> uglify, aggiungere il codice seguente al modulo.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-207">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="5a1e6-208">Il codice deve essere aggiunto allo stesso livello delle chiamate a loadNpmTasks (), all'esterno di initConfig.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-208">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="5a1e6-209">La nuova attività viene visualizzata in Esplora attività in attività alias.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-209">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="5a1e6-210">È possibile fare clic con il pulsante destro del mouse ed eseguirlo esattamente come per le altre attività.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-210">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="5a1e6-211">L' `all` attività eseguirà `clean` , `concat` `jshint` e `uglify` , nell'ordine.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-211">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![attività con alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="5a1e6-213">Controllo per la ricerca di modifiche</span><span class="sxs-lookup"><span data-stu-id="5a1e6-213">Watching for changes</span></span>

<span data-ttu-id="5a1e6-214">Un' `watch` attività consente di tenere sotto controllo i file e le directory.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-214">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="5a1e6-215">Il controllo attiva automaticamente le attività in caso di rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-215">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="5a1e6-216">Aggiungere il codice seguente a initConfig per controllare le modifiche apportate ai \* file con estensione js nella directory typescript.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-216">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="5a1e6-217">Se viene modificato un file JavaScript, `watch` eseguirà l' `all` attività.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-217">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="5a1e6-218">Aggiungere una chiamata a `loadNpmTasks()` per visualizzare l' `watch` attività nella finestra di esplorazione di Task Runner.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-218">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="5a1e6-219">Fare clic con il pulsante destro del mouse sull'attività Watch in Esplora attività e scegliere Esegui dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-219">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="5a1e6-220">La finestra di comando che mostra l'attività Watch in esecuzione visualizzerà "Waiting..." Messaggio.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-220">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="5a1e6-221">Aprire uno dei file TypeScript, aggiungere uno spazio e quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-221">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="5a1e6-222">In questo modo l'attività Watch viene attivata e vengono attivate le altre attività da eseguire nell'ordine.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-222">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="5a1e6-223">La schermata seguente mostra un'esecuzione di esempio.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-223">The screenshot below shows a sample run.</span></span>

![output attività in esecuzione](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="5a1e6-225">Associazione agli eventi di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a1e6-225">Binding to Visual Studio events</span></span>

<span data-ttu-id="5a1e6-226">A meno che non si desideri avviare manualmente le attività ogni volta che si lavora in Visual Studio, associare le attività a **prima della compilazione** , **dopo la compilazione** , la **pulizia** e il progetto degli eventi **aperti** .</span><span class="sxs-lookup"><span data-stu-id="5a1e6-226">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build** , **After Build** , **Clean** , and **Project Open** events.</span></span>

<span data-ttu-id="5a1e6-227">`watch`Eseguire l'associazione in modo che venga eseguita ogni volta che si apre Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-227">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="5a1e6-228">In Task Runner Explorer fare clic con il pulsante destro del mouse sull'attività Watch e scegliere **bindings**  >  **progetto Open** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-228">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![associa un'attività all'apertura del progetto](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="5a1e6-230">Scaricare e ricaricare il progetto.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-230">Unload and reload the project.</span></span> <span data-ttu-id="5a1e6-231">Quando il progetto viene caricato nuovamente, l'attività Watch viene avviata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-231">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="5a1e6-232">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="5a1e6-232">Summary</span></span>

<span data-ttu-id="5a1e6-233">Grugnito è un potente strumento di esecuzione delle attività che può essere usato per automatizzare la maggior parte delle attività di compilazione client.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-233">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="5a1e6-234">Grugni si avvale di NPM per distribuire i pacchetti e include funzionalità di integrazione degli strumenti con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-234">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="5a1e6-235">Esplora attività di Visual Studio rileva le modifiche apportate ai file di configurazione e offre una comoda interfaccia per eseguire attività, visualizzare attività in esecuzione e associare attività agli eventi di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5a1e6-235">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
