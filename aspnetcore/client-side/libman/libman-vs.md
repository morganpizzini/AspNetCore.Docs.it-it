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
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="e6fec-103">Usare LibMan con ASP.NET Core in Visual StudioUse LibMan with ASP.NET Core in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6fec-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="e6fec-104">Di [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="e6fec-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="e6fec-105">Visual Studio include il supporto incorporato per LibMan nei progetti ASP.NET Core, tra cui:Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span><span class="sxs-lookup"><span data-stu-id="e6fec-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="e6fec-106">Supporto per la configurazione e l'esecuzione di operazioni di ripristino LibMan in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="e6fec-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="e6fec-107">Voci di menu per l'attivazione di operazioni di ripristino e pulizia di LibMan.</span><span class="sxs-lookup"><span data-stu-id="e6fec-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="e6fec-108">Finestra di dialogo di ricerca per trovare librerie e aggiungere i file a un progetto.</span><span class="sxs-lookup"><span data-stu-id="e6fec-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="e6fec-109">Modifica del supporto per *libman.json*&mdash;del file manifesto LibMan.</span><span class="sxs-lookup"><span data-stu-id="e6fec-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="e6fec-110">[Visualizzare o scaricare](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) codice di esempio [(come scaricare)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e6fec-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e6fec-111">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="e6fec-111">Prerequisites</span></span>

* <span data-ttu-id="e6fec-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con il carico di lavoro **di sviluppo Web e ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="e6fec-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="e6fec-113">Aggiungere file di libreria</span><span class="sxs-lookup"><span data-stu-id="e6fec-113">Add library files</span></span>

<span data-ttu-id="e6fec-114">I file di libreria possono essere aggiunti a un progetto ASP.NET Core in due modi diversi:</span><span class="sxs-lookup"><span data-stu-id="e6fec-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="e6fec-115">Utilizzare la finestra di dialogo Aggiungi libreria sul lato client</span><span class="sxs-lookup"><span data-stu-id="e6fec-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="e6fec-116">Configurare manualmente le voci del file manifesto LibMan</span><span class="sxs-lookup"><span data-stu-id="e6fec-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="e6fec-117">Utilizzare la finestra di dialogo Aggiungi libreria sul lato client</span><span class="sxs-lookup"><span data-stu-id="e6fec-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="e6fec-118">Per installare una libreria lato client, attenersi alla seguente procedura:</span><span class="sxs-lookup"><span data-stu-id="e6fec-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="e6fec-119">In **Esplora soluzioni**fare clic con il pulsante destro del mouse sulla cartella del progetto in cui devono essere aggiunti i file.</span><span class="sxs-lookup"><span data-stu-id="e6fec-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="e6fec-120">Scegliere **Aggiungi** > **libreria lato client**.</span><span class="sxs-lookup"><span data-stu-id="e6fec-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="e6fec-121">Viene visualizzata la finestra di dialogo **Aggiungi libreria lato client:**</span><span class="sxs-lookup"><span data-stu-id="e6fec-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Finestra di dialogo Aggiungi libreria lato client](_static/add-library-dialog.png)

* <span data-ttu-id="e6fec-123">Selezionare il provider di librerie dall'elenco a discesa **Provider.**</span><span class="sxs-lookup"><span data-stu-id="e6fec-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="e6fec-124">CDNJS è il provider predefinito.</span><span class="sxs-lookup"><span data-stu-id="e6fec-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="e6fec-125">Digitare il nome della libreria da recuperare nella casella di testo **Libreria.**</span><span class="sxs-lookup"><span data-stu-id="e6fec-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="e6fec-126">IntelliSense fornisce un elenco di librerie che iniziano con il testo fornito.</span><span class="sxs-lookup"><span data-stu-id="e6fec-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="e6fec-127">Selezionare la libreria dall'elenco IntelliSense.Select the library from the IntelliSense list.</span><span class="sxs-lookup"><span data-stu-id="e6fec-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="e6fec-128">Si noti che il `@` nome della libreria è suffisso con il simbolo e l'ultima versione stabile nota al provider selezionato.</span><span class="sxs-lookup"><span data-stu-id="e6fec-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="e6fec-129">Decidere quali file includere:</span><span class="sxs-lookup"><span data-stu-id="e6fec-129">Decide which files to include:</span></span>
  * <span data-ttu-id="e6fec-130">Selezionare il pulsante di opzione **Includi tutti i file** di libreria per includere tutti i file della libreria.</span><span class="sxs-lookup"><span data-stu-id="e6fec-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="e6fec-131">Selezionare il pulsante di opzione **Scegli file specifici** per includere un sottoinsieme dei file della libreria.</span><span class="sxs-lookup"><span data-stu-id="e6fec-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="e6fec-132">Quando il pulsante di opzione è selezionato, l'albero del selettore di file è abilitato.</span><span class="sxs-lookup"><span data-stu-id="e6fec-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="e6fec-133">Selezionare le caselle a sinistra dei nomi dei file da scaricare.</span><span class="sxs-lookup"><span data-stu-id="e6fec-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="e6fec-134">Specificare la cartella del progetto per l'archiviazione dei file nella casella di testo Percorso di **destinazione.**</span><span class="sxs-lookup"><span data-stu-id="e6fec-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="e6fec-135">Come raccomandazione, archiviare ogni libreria in una cartella separata.</span><span class="sxs-lookup"><span data-stu-id="e6fec-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="e6fec-136">La cartella **Posizione di destinazione** suggerita si basa sulla posizione da cui è stata avviata la finestra di dialogo:</span><span class="sxs-lookup"><span data-stu-id="e6fec-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="e6fec-137">Se lanciato dalla radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="e6fec-137">If launched from the project root:</span></span>
    * <span data-ttu-id="e6fec-138">*wwwroot/lib* viene utilizzato se *wwwroot* esiste.</span><span class="sxs-lookup"><span data-stu-id="e6fec-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="e6fec-139">*lib* viene utilizzato se *wwwroot* non esiste.</span><span class="sxs-lookup"><span data-stu-id="e6fec-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="e6fec-140">Se viene avviato da una cartella di progetto, viene utilizzato il nome della cartella corrispondente.</span><span class="sxs-lookup"><span data-stu-id="e6fec-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="e6fec-141">Il suggerimento della cartella è un suffisso con il nome della libreria.</span><span class="sxs-lookup"><span data-stu-id="e6fec-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="e6fec-142">Nella tabella seguente vengono illustrati i suggerimenti di cartelle durante l'installazione di jQuery in un progetto Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e6fec-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="e6fec-143">Posizione di lancio</span><span class="sxs-lookup"><span data-stu-id="e6fec-143">Launch location</span></span>                           |<span data-ttu-id="e6fec-144">Cartella suggerita</span><span class="sxs-lookup"><span data-stu-id="e6fec-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="e6fec-145">radice del progetto (se *wwwroot* esiste)</span><span class="sxs-lookup"><span data-stu-id="e6fec-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="e6fec-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="e6fec-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="e6fec-147">radice del progetto (se *wwwroot* non esiste)</span><span class="sxs-lookup"><span data-stu-id="e6fec-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="e6fec-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="e6fec-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="e6fec-149">*Cartella Pagine* nel progetto</span><span class="sxs-lookup"><span data-stu-id="e6fec-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="e6fec-150">*Pagine/jquery/*</span><span class="sxs-lookup"><span data-stu-id="e6fec-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="e6fec-151">Fare clic sul pulsante **Installa** per scaricare i file, in base alla configurazione in *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="e6fec-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="e6fec-152">Esaminare il feed **Gestione librerie** della finestra **Output** per i dettagli di installazione.</span><span class="sxs-lookup"><span data-stu-id="e6fec-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="e6fec-153">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e6fec-153">For example:</span></span>

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

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="e6fec-154">Configurare manualmente le voci del file manifesto LibMan</span><span class="sxs-lookup"><span data-stu-id="e6fec-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="e6fec-155">Tutte le operazioni LibMan in Visual Studio sono basate sul contenuto del manifesto LibMan della radice del progetto (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="e6fec-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="e6fec-156">È possibile modificare manualmente *libman.json* per configurare i file di libreria per il progetto.</span><span class="sxs-lookup"><span data-stu-id="e6fec-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="e6fec-157">Visual Studio ripristina tutti i file di libreria dopo il salvataggio di *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="e6fec-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="e6fec-158">Per aprire *libman.json* per la modifica, esistono le seguenti opzioni:</span><span class="sxs-lookup"><span data-stu-id="e6fec-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="e6fec-159">Fare doppio clic sul file *libman.json* in **Esplora soluzioni**.</span><span class="sxs-lookup"><span data-stu-id="e6fec-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="e6fec-160">Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e selezionare **Gestisci librerie lato client**.</span><span class="sxs-lookup"><span data-stu-id="e6fec-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="e6fec-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="e6fec-161">**&#8224;**</span></span>
* <span data-ttu-id="e6fec-162">Selezionare **Gestisci librerie lato client** dal menu Progetto di Visual Studio.Select Manage Client-Side Libraries from the Visual Studio **Project** menu.</span><span class="sxs-lookup"><span data-stu-id="e6fec-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="e6fec-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="e6fec-163">**&#8224;**</span></span>

<span data-ttu-id="e6fec-164">**&#8224;** Se il file *libman.json* non esiste già nella radice del progetto, verrà creato con il contenuto del modello di elemento predefinito.</span><span class="sxs-lookup"><span data-stu-id="e6fec-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="e6fec-165">Visual Studio offre supporto avanzato per la modifica JSON, ad esempio la colorazione, la formattazione, IntelliSense e la convalida dello schema.</span><span class="sxs-lookup"><span data-stu-id="e6fec-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="e6fec-166">Lo schema JSON del manifesto LibMan si trova in [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span><span class="sxs-lookup"><span data-stu-id="e6fec-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="e6fec-167">Con il file manifesto seguente, LibMan recupera i `libraries` file in base alla configurazione definita nella proprietà.</span><span class="sxs-lookup"><span data-stu-id="e6fec-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="e6fec-168">Una spiegazione dei valori `libraries` letterali oggetto definiti all'interno di quanto segue:</span><span class="sxs-lookup"><span data-stu-id="e6fec-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="e6fec-169">Un sottoinsieme di [jQuery](https://jquery.com/) versione 3.3.1 viene recuperato dal provider CDNJS.</span><span class="sxs-lookup"><span data-stu-id="e6fec-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="e6fec-170">Il sottoinsieme è `files` &mdash;definito nella proprietà*jquery.min.js*, *jquery.js*e *jquery.min.map*.</span><span class="sxs-lookup"><span data-stu-id="e6fec-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="e6fec-171">I file vengono inseriti nella cartella *wwwroot/lib/jquery* del progetto.</span><span class="sxs-lookup"><span data-stu-id="e6fec-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="e6fec-172">La totalità di [Bootstrap](https://getbootstrap.com/) versione 4.1.3 viene recuperata e inserita in una cartella *wwwroot/lib/bootstrap.*</span><span class="sxs-lookup"><span data-stu-id="e6fec-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="e6fec-173">La proprietà del `provider` valore letterale dell'oggetto esegue l'override del valore della `defaultProvider` proprietà.</span><span class="sxs-lookup"><span data-stu-id="e6fec-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="e6fec-174">LibMan recupera i file Bootstrap dal provider unpkg.</span><span class="sxs-lookup"><span data-stu-id="e6fec-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="e6fec-175">Un sottoinsieme di [Lodash](https://lodash.com/) è stato approvato da un organo direttivo all'interno dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="e6fec-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="e6fec-176">I file *lodash.js* e *lodash.min.js* vengono recuperati dal file system locale in *C:\\temp\\lodash\\*.</span><span class="sxs-lookup"><span data-stu-id="e6fec-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="e6fec-177">I file vengono copiati nella cartella *wwwroot/lib/lodash* del progetto.</span><span class="sxs-lookup"><span data-stu-id="e6fec-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="e6fec-178">LibMan supporta solo una versione di ogni libreria da ogni provider.</span><span class="sxs-lookup"><span data-stu-id="e6fec-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="e6fec-179">Il file *libman.json* non supera la convalida dello schema se contiene due librerie con lo stesso nome di libreria per un determinato provider.</span><span class="sxs-lookup"><span data-stu-id="e6fec-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="e6fec-180">Ripristinare i file di libreria</span><span class="sxs-lookup"><span data-stu-id="e6fec-180">Restore library files</span></span>

<span data-ttu-id="e6fec-181">Per ripristinare i file di libreria dall'interno di Visual Studio, deve essere presente un file *libman.json* valido nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="e6fec-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="e6fec-182">I file ripristinati vengono inseriti nel progetto nella posizione specificata per ogni libreria.</span><span class="sxs-lookup"><span data-stu-id="e6fec-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="e6fec-183">I file di libreria possono essere ripristinati in un progetto ASP.NET Core in due modi:</span><span class="sxs-lookup"><span data-stu-id="e6fec-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="e6fec-184">Ripristinare i file durante la compilazioneRestore files during build</span><span class="sxs-lookup"><span data-stu-id="e6fec-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="e6fec-185">Ripristinare i file manualmente</span><span class="sxs-lookup"><span data-stu-id="e6fec-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="e6fec-186">Ripristinare i file durante la compilazioneRestore files during build</span><span class="sxs-lookup"><span data-stu-id="e6fec-186">Restore files during build</span></span>

<span data-ttu-id="e6fec-187">LibMan può ripristinare i file di libreria definiti come parte del processo di compilazione.</span><span class="sxs-lookup"><span data-stu-id="e6fec-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="e6fec-188">Per impostazione predefinita, il comportamento di ripristino alla *compilazione* è disabilitato.</span><span class="sxs-lookup"><span data-stu-id="e6fec-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="e6fec-189">Per abilitare e testare il comportamento di ripristino in fase di compilazione:</span><span class="sxs-lookup"><span data-stu-id="e6fec-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="e6fec-190">Fare clic con il pulsante destro del mouse su *libman.json* in **Esplora soluzioni** e scegliere **Abilita ripristina librerie lato client durante** la compilazione dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="e6fec-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="e6fec-191">Fare clic sul pulsante **Sì** quando viene richiesto di installare un pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="e6fec-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="e6fec-192">Il pacchetto [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet viene aggiunto al progetto:</span><span class="sxs-lookup"><span data-stu-id="e6fec-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="e6fec-193">Compilare il progetto per verificare che si verifichi il ripristino del file LibMan.</span><span class="sxs-lookup"><span data-stu-id="e6fec-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="e6fec-194">Il `Microsoft.Web.LibraryManager.Build` pacchetto inserisce una destinazione MSBuild che esegue LibMan durante l'operazione di compilazione del progetto.</span><span class="sxs-lookup"><span data-stu-id="e6fec-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="e6fec-195">Esaminare il feed **Di compilazione** della finestra **Output** per un log attività LibMan:</span><span class="sxs-lookup"><span data-stu-id="e6fec-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

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

<span data-ttu-id="e6fec-196">Quando il comportamento restore-on-build è abilitato, il menu di scelta rapida *libman.json* visualizza un'opzione **Disattiva ripristina librerie lato client durante** la compilazione.</span><span class="sxs-lookup"><span data-stu-id="e6fec-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="e6fec-197">Selezionando questa opzione `Microsoft.Web.LibraryManager.Build` si rimuove il riferimento al pacchetto dal file di progetto.</span><span class="sxs-lookup"><span data-stu-id="e6fec-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="e6fec-198">Di conseguenza, le librerie lato client non vengono più ripristinate in ogni compilazione.</span><span class="sxs-lookup"><span data-stu-id="e6fec-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="e6fec-199">Indipendentemente dall'impostazione di ripristino alla compilazione, è possibile eseguire manualmente il ripristino in qualsiasi momento dal menu di scelta rapida *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="e6fec-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="e6fec-200">Per ulteriori informazioni, consultate [Ripristinare i file manualmente.](#restore-files-manually)</span><span class="sxs-lookup"><span data-stu-id="e6fec-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="e6fec-201">Ripristinare i file manualmente</span><span class="sxs-lookup"><span data-stu-id="e6fec-201">Restore files manually</span></span>

<span data-ttu-id="e6fec-202">Per ripristinare manualmente i file di libreria:</span><span class="sxs-lookup"><span data-stu-id="e6fec-202">To manually restore library files:</span></span>

* <span data-ttu-id="e6fec-203">Per tutti i progetti nella soluzione:</span><span class="sxs-lookup"><span data-stu-id="e6fec-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="e6fec-204">Fare clic con il pulsante destro del mouse sul nome della soluzione in **Esplora soluzioni**.</span><span class="sxs-lookup"><span data-stu-id="e6fec-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="e6fec-205">Selezionare l'opzione **Ripristina librerie lato client.**</span><span class="sxs-lookup"><span data-stu-id="e6fec-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="e6fec-206">Per un progetto specifico:</span><span class="sxs-lookup"><span data-stu-id="e6fec-206">For a specific project:</span></span>
  * <span data-ttu-id="e6fec-207">Fare clic con il pulsante destro del mouse sul file *libman.json* in **Esplora soluzioni**.</span><span class="sxs-lookup"><span data-stu-id="e6fec-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="e6fec-208">Selezionare l'opzione **Ripristina librerie lato client.**</span><span class="sxs-lookup"><span data-stu-id="e6fec-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="e6fec-209">Durante l'esecuzione dell'operazione di ripristino:</span><span class="sxs-lookup"><span data-stu-id="e6fec-209">While the restore operation is running:</span></span>

* <span data-ttu-id="e6fec-210">L'icona Centro stato attività (TSC) sulla barra di stato di Visual Studio verrà animata e verrà *letta avviata l'operazione di ripristino*.</span><span class="sxs-lookup"><span data-stu-id="e6fec-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="e6fec-211">Facendo clic sull'icona si apre una descrizione comando che elenca le attività in background note.</span><span class="sxs-lookup"><span data-stu-id="e6fec-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="e6fec-212">I messaggi verranno inviati alla barra di stato e al feed **di Gestione librerie** della finestra **Output.**</span><span class="sxs-lookup"><span data-stu-id="e6fec-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="e6fec-213">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e6fec-213">For example:</span></span>

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

## <a name="delete-library-files"></a><span data-ttu-id="e6fec-214">Eliminare i file di libreria</span><span class="sxs-lookup"><span data-stu-id="e6fec-214">Delete library files</span></span>

<span data-ttu-id="e6fec-215">Per eseguire l'operazione *di pulizia,* che elimina i file di libreria precedentemente ripristinati in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e6fec-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="e6fec-216">Fare clic con il pulsante destro del mouse sul file *libman.json* in **Esplora soluzioni**.</span><span class="sxs-lookup"><span data-stu-id="e6fec-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="e6fec-217">Selezionare l'opzione **Pulisci librerie lato client.**</span><span class="sxs-lookup"><span data-stu-id="e6fec-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="e6fec-218">Per impedire la rimozione involontaria di file non di libreria, l'operazione di pulizia non elimina intere directory.</span><span class="sxs-lookup"><span data-stu-id="e6fec-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="e6fec-219">Rimuove solo i file inclusi nel ripristino precedente.</span><span class="sxs-lookup"><span data-stu-id="e6fec-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="e6fec-220">Durante l'esecuzione dell'operazione di pulizia:</span><span class="sxs-lookup"><span data-stu-id="e6fec-220">While the clean operation is running:</span></span>

* <span data-ttu-id="e6fec-221">L'icona TSC sulla barra di stato di Visual Studio verrà animata e verrà letta *L'operazione Librerie client avviata*.</span><span class="sxs-lookup"><span data-stu-id="e6fec-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="e6fec-222">Facendo clic sull'icona si apre una descrizione comando che elenca le attività in background note.</span><span class="sxs-lookup"><span data-stu-id="e6fec-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="e6fec-223">I messaggi vengono inviati alla barra di stato e al feed **Gestione librerie** della finestra **Output.**</span><span class="sxs-lookup"><span data-stu-id="e6fec-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="e6fec-224">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e6fec-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="e6fec-225">L'operazione di pulizia elimina solo i file dal progetto.</span><span class="sxs-lookup"><span data-stu-id="e6fec-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="e6fec-226">I file di libreria rimangono nella cache per un recupero più rapido delle operazioni di ripristino future.</span><span class="sxs-lookup"><span data-stu-id="e6fec-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="e6fec-227">Per gestire i file di libreria archiviati nella cache del computer locale, utilizzare [l'interfaccia della riga di comando LibMan](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="e6fec-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="e6fec-228">Disinstallare i file di libreria</span><span class="sxs-lookup"><span data-stu-id="e6fec-228">Uninstall library files</span></span>

<span data-ttu-id="e6fec-229">Per disinstallare i file di libreria:</span><span class="sxs-lookup"><span data-stu-id="e6fec-229">To uninstall library files:</span></span>

* <span data-ttu-id="e6fec-230">Aprire *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="e6fec-230">Open *libman.json*.</span></span>
* <span data-ttu-id="e6fec-231">Posizionare il carattere `libraries` di inserimento all'interno del valore letterale dell'oggetto corrispondente.</span><span class="sxs-lookup"><span data-stu-id="e6fec-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="e6fec-232">Fare clic sull'icona della lampadina visualizzata nel margine sinistro e selezionare **Disinstalla \<library_name>\<library_version>**:</span><span class="sxs-lookup"><span data-stu-id="e6fec-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Opzione di menu di scelta rapida Disinstalla libreria](_static/uninstall-menu-option.png)

<span data-ttu-id="e6fec-234">In alternativa, è possibile modificare e salvare manualmente il manifesto LibMan (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="e6fec-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="e6fec-235">[L'operazione di ripristino](#restore-library-files) viene eseguita quando il file viene salvato.</span><span class="sxs-lookup"><span data-stu-id="e6fec-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="e6fec-236">I file di libreria non più definiti in *libman.json* vengono rimossi dal progetto.</span><span class="sxs-lookup"><span data-stu-id="e6fec-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="e6fec-237">Aggiornare la versione della libreria</span><span class="sxs-lookup"><span data-stu-id="e6fec-237">Update library version</span></span>

<span data-ttu-id="e6fec-238">Per verificare la disponibilità di una versione aggiornata della libreria:</span><span class="sxs-lookup"><span data-stu-id="e6fec-238">To check for an updated library version:</span></span>

* <span data-ttu-id="e6fec-239">Aprire *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="e6fec-239">Open *libman.json*.</span></span>
* <span data-ttu-id="e6fec-240">Posizionare il carattere `libraries` di inserimento all'interno del valore letterale dell'oggetto corrispondente.</span><span class="sxs-lookup"><span data-stu-id="e6fec-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="e6fec-241">Fare clic sull'icona della lampadina visualizzata nel margine sinistro.</span><span class="sxs-lookup"><span data-stu-id="e6fec-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="e6fec-242">Passare il mouse su **Controlla aggiornamenti**.</span><span class="sxs-lookup"><span data-stu-id="e6fec-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="e6fec-243">LibMan verifica la presenza di una versione della libreria più recente della versione installata.</span><span class="sxs-lookup"><span data-stu-id="e6fec-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="e6fec-244">Possono verificarsi i seguenti risultati:</span><span class="sxs-lookup"><span data-stu-id="e6fec-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="e6fec-245">Se è già installata la versione più recente, viene visualizzato il messaggio **Nessun aggiornamento trovato.**</span><span class="sxs-lookup"><span data-stu-id="e6fec-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="e6fec-246">Se non è già installata, viene visualizzata l'ultima versione stabile.</span><span class="sxs-lookup"><span data-stu-id="e6fec-246">The latest stable version is displayed if not already installed.</span></span>

  ![Opzione del menu di scelta rapida Controlla aggiornamenti](_static/update-menu-option.png)

* <span data-ttu-id="e6fec-248">Se è disponibile una versione non definitiva rispetto alla versione installata, viene visualizzata la versione non definitiva.</span><span class="sxs-lookup"><span data-stu-id="e6fec-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="e6fec-249">Per eseguire il downgrade a una versione precedente della libreria, modificare manualmente il file *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="e6fec-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="e6fec-250">Quando il file viene salvato, [l'operazione](#restore-library-files)di ripristino LibMan :</span><span class="sxs-lookup"><span data-stu-id="e6fec-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="e6fec-251">Rimuove i file ridondanti dalla versione precedente.</span><span class="sxs-lookup"><span data-stu-id="e6fec-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="e6fec-252">Aggiunge file nuovi e aggiornati dalla nuova versione.</span><span class="sxs-lookup"><span data-stu-id="e6fec-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6fec-253">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e6fec-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="e6fec-254">Repository GitHub di LibMan</span><span class="sxs-lookup"><span data-stu-id="e6fec-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
