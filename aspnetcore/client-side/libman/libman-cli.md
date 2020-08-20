---
title: Usare l'interfaccia della riga di comando di LibMan con ASP.NET Core
author: scottaddie
description: Informazioni su come usare l'interfaccia della riga di comando di LibMan in un progetto ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
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
uid: client-side/libman/libman-cli
ms.openlocfilehash: 8b883269a82a1a6e55bf04bd40bfcbab28ae1fb3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625700"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="3fdd7-103">Usare l'interfaccia della riga di comando di LibMan con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fdd7-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="3fdd7-104">Di [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="3fdd7-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="3fdd7-105">L'interfaccia della riga di comando di [LibMan](xref:client-side/libman/index) è uno strumento multipiattaforma supportato ovunque sia supportato .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3fdd7-106">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="3fdd7-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="3fdd7-107">Installazione</span><span class="sxs-lookup"><span data-stu-id="3fdd7-107">Installation</span></span>

<span data-ttu-id="3fdd7-108">Per installare l'interfaccia della riga di comando di LibMan:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="3fdd7-109">Uno [strumento .NET Core Global](/dotnet/core/tools/global-tools#install-a-global-tool) viene installato dal pacchetto NuGet [Microsoft. Web. librarymanager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) .</span><span class="sxs-lookup"><span data-stu-id="3fdd7-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="3fdd7-110">Per installare l'interfaccia della riga di comando di LibMan da un'origine del pacchetto NuGet specifica:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="3fdd7-111">Nell'esempio precedente, uno strumento globale di .NET Core viene installato dal file *C:\Temp\Microsoft.Web.librarymanager.cli.1.0.94-g606058a278.nupkg* del computer Windows locale.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="3fdd7-112">Uso</span><span class="sxs-lookup"><span data-stu-id="3fdd7-112">Usage</span></span>

<span data-ttu-id="3fdd7-113">Una volta completata l'installazione dell'interfaccia della riga di comando, è possibile usare il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="3fdd7-114">Per visualizzare la versione dell'interfaccia della riga di comando installata:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="3fdd7-115">Per visualizzare i comandi dell'interfaccia della riga di comando disponibili:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="3fdd7-116">Il comando precedente Visualizza un output simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-116">The preceding command displays output similar to the following:</span></span>

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

<span data-ttu-id="3fdd7-117">Le sezioni seguenti descrivono i comandi dell'interfaccia della riga di comando disponibili.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="3fdd7-118">Inizializzare LibMan nel progetto</span><span class="sxs-lookup"><span data-stu-id="3fdd7-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="3fdd7-119">Il `libman init` comando crea un *libman.jsnel* file, se non ne esiste uno.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="3fdd7-120">Il file viene creato con il contenuto del modello di elemento predefinito.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3fdd7-121">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="3fdd7-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3fdd7-122">Opzioni</span><span class="sxs-lookup"><span data-stu-id="3fdd7-122">Options</span></span>

<span data-ttu-id="3fdd7-123">Per il comando `libman init` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="3fdd7-124">Percorso relativo alla cartella corrente.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-124">A path relative to the current folder.</span></span> <span data-ttu-id="3fdd7-125">Se non `destination` è stata definita alcuna proprietà per una libreria in *libman.json*, i file di libreria vengono installati in questo percorso.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="3fdd7-126">Il `<PATH>` valore viene scritto nella `defaultDestination` proprietà di *libman.jssu*.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="3fdd7-127">Provider da utilizzare se non è definito alcun provider per una determinata libreria.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="3fdd7-128">Il `<PROVIDER>` valore viene scritto nella `defaultProvider` proprietà di *libman.jssu*.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="3fdd7-129">Sostituire `<PROVIDER>` con uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3fdd7-130">Esempi</span><span class="sxs-lookup"><span data-stu-id="3fdd7-130">Examples</span></span>

<span data-ttu-id="3fdd7-131">Per creare un *libman.jssu* file in un progetto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="3fdd7-132">Passare alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-132">Navigate to the project root.</span></span>
* <span data-ttu-id="3fdd7-133">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="3fdd7-134">Digitare il nome del provider predefinito oppure premere `Enter` per utilizzare il provider CDNJS predefinito.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="3fdd7-135">I valori validi includono:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![comando Libman init-provider predefinito](_static/libman-init-provider.png)

<span data-ttu-id="3fdd7-137">Alla radice del progetto viene aggiunto un *libman.jsnel* file con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="3fdd7-138">Aggiungi file di libreria</span><span class="sxs-lookup"><span data-stu-id="3fdd7-138">Add library files</span></span>

<span data-ttu-id="3fdd7-139">Il `libman install` comando Scarica e installa i file di libreria nel progetto.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="3fdd7-140">Se non ne esiste uno, viene aggiunto un *libman.jssul* file.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="3fdd7-141">Il *libman.jsnel* file viene modificato in modo da archiviare i dettagli di configurazione per i file di libreria.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3fdd7-142">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="3fdd7-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3fdd7-143">Argomenti</span><span class="sxs-lookup"><span data-stu-id="3fdd7-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3fdd7-144">Nome della libreria da installare.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-144">The name of the library to install.</span></span> <span data-ttu-id="3fdd7-145">Questo nome può includere la notazione dei numeri di versione (ad esempio, `@1.2.0` ).</span><span class="sxs-lookup"><span data-stu-id="3fdd7-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="3fdd7-146">Opzioni</span><span class="sxs-lookup"><span data-stu-id="3fdd7-146">Options</span></span>

<span data-ttu-id="3fdd7-147">Per il comando `libman install` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="3fdd7-148">Percorso in cui installare la libreria.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-148">The location to install the library.</span></span> <span data-ttu-id="3fdd7-149">Se non specificato, viene utilizzato il percorso predefinito.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-149">If not specified, the default location is used.</span></span> <span data-ttu-id="3fdd7-150">Se non `defaultDestination` è specificata alcuna proprietà in *libman.json*, questa opzione è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="3fdd7-151">Specificare il nome del file da installare dalla libreria.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="3fdd7-152">Se non specificato, vengono installati tutti i file dalla libreria.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="3fdd7-153">Specificare un' `--files` opzione per ogni file da installare.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="3fdd7-154">Sono supportati anche i percorsi relativi.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-154">Relative paths are supported too.</span></span> <span data-ttu-id="3fdd7-155">Ad esempio: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="3fdd7-156">Nome del provider da utilizzare per l'acquisizione della libreria.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="3fdd7-157">Sostituire `<PROVIDER>` con uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="3fdd7-158">Se non è specificato, `defaultProvider` viene utilizzata la proprietà in *libman.js* .</span><span class="sxs-lookup"><span data-stu-id="3fdd7-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="3fdd7-159">Se non `defaultProvider` è specificata alcuna proprietà in *libman.json*, questa opzione è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3fdd7-160">Esempi</span><span class="sxs-lookup"><span data-stu-id="3fdd7-160">Examples</span></span>

<span data-ttu-id="3fdd7-161">Si considerino i *libman.jsseguenti nel* file:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="3fdd7-162">Per installare il file di *jquery.min.js* jQuery versione 3.2.1 nella cartella *wwwroot/scripts/jQuery* usando il provider CDNJS:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="3fdd7-163">Il *libman.jsnel* file è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-163">The *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

<span data-ttu-id="3fdd7-164">Per installare i file *calendar.js* e *Calendar. CSS* da \*C: \\ temp \\ contosoCalendar \\ \* usando il provider file System:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="3fdd7-165">La richiesta seguente viene visualizzata per due motivi:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="3fdd7-166">Il *libman.jsnel* file non contiene una `defaultDestination` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="3fdd7-167">Il `libman install` comando non contiene l' `-d|--destination` opzione.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![comando di installazione di Libman-destinazione](_static/libman-install-destination.png)

<span data-ttu-id="3fdd7-169">Dopo aver accettato la destinazione predefinita, il *libman.jsnel* file è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a><span data-ttu-id="3fdd7-170">Ripristinare i file di libreria</span><span class="sxs-lookup"><span data-stu-id="3fdd7-170">Restore library files</span></span>

<span data-ttu-id="3fdd7-171">Il `libman restore` comando installa i file di libreria definiti in *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="3fdd7-172">Sono applicabili le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-172">The following rules apply:</span></span>

* <span data-ttu-id="3fdd7-173">Se nella radice del progetto non è presente alcun *libman.js* nel file, viene restituito un errore.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="3fdd7-174">Se una libreria specifica un provider, la `defaultProvider` Proprietà in *libman.js* in viene ignorata.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="3fdd7-175">Se una libreria specifica una destinazione, la `defaultDestination` Proprietà in *libman.js* in viene ignorata.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3fdd7-176">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="3fdd7-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3fdd7-177">Opzioni</span><span class="sxs-lookup"><span data-stu-id="3fdd7-177">Options</span></span>

<span data-ttu-id="3fdd7-178">Per il comando `libman restore` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3fdd7-179">Esempi</span><span class="sxs-lookup"><span data-stu-id="3fdd7-179">Examples</span></span>

<span data-ttu-id="3fdd7-180">Per ripristinare i file di libreria definiti in *libman.jsin*:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="3fdd7-181">Elimina file di libreria</span><span class="sxs-lookup"><span data-stu-id="3fdd7-181">Delete library files</span></span>

<span data-ttu-id="3fdd7-182">Il `libman clean` comando Elimina i file di libreria ripristinati in precedenza tramite LibMan.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="3fdd7-183">Cartelle che diventano vuote dopo l'eliminazione di questa operazione.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="3fdd7-184">Le configurazioni associate dei file di libreria nella `libraries` proprietà di *libman.js* non vengono rimosse.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3fdd7-185">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="3fdd7-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3fdd7-186">Opzioni</span><span class="sxs-lookup"><span data-stu-id="3fdd7-186">Options</span></span>

<span data-ttu-id="3fdd7-187">Per il comando `libman clean` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3fdd7-188">Esempi</span><span class="sxs-lookup"><span data-stu-id="3fdd7-188">Examples</span></span>

<span data-ttu-id="3fdd7-189">Per eliminare i file di libreria installati tramite LibMan:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="3fdd7-190">Disinstalla file di libreria</span><span class="sxs-lookup"><span data-stu-id="3fdd7-190">Uninstall library files</span></span>

<span data-ttu-id="3fdd7-191">Il `libman uninstall` comando:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="3fdd7-192">Elimina tutti i file associati alla libreria specificata dalla destinazione nel *libman.js*.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="3fdd7-193">Rimuove la configurazione della libreria associata dal *libman.js*.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="3fdd7-194">Si verifica un errore quando:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-194">An error occurs when:</span></span>

* <span data-ttu-id="3fdd7-195">Non esiste alcun *libman.js* nel file nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="3fdd7-196">La libreria specificata non esiste.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="3fdd7-197">Se è installata più di una libreria con lo stesso nome, viene richiesto di sceglierne una.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3fdd7-198">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="3fdd7-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3fdd7-199">Argomenti</span><span class="sxs-lookup"><span data-stu-id="3fdd7-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3fdd7-200">Nome della libreria da disinstallare.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-200">The name of the library to uninstall.</span></span> <span data-ttu-id="3fdd7-201">Questo nome può includere la notazione dei numeri di versione (ad esempio, `@1.2.0` ).</span><span class="sxs-lookup"><span data-stu-id="3fdd7-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="3fdd7-202">Opzioni</span><span class="sxs-lookup"><span data-stu-id="3fdd7-202">Options</span></span>

<span data-ttu-id="3fdd7-203">Per il comando `libman uninstall` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3fdd7-204">Esempi</span><span class="sxs-lookup"><span data-stu-id="3fdd7-204">Examples</span></span>

<span data-ttu-id="3fdd7-205">Si considerino i *libman.jsseguenti nel* file:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="3fdd7-206">Per disinstallare jQuery, uno dei seguenti comandi ha esito positivo:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="3fdd7-207">Per disinstallare i file Lodash installati tramite il `filesystem` provider:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="3fdd7-208">Aggiornare la versione della libreria</span><span class="sxs-lookup"><span data-stu-id="3fdd7-208">Update library version</span></span>

<span data-ttu-id="3fdd7-209">Il `libman update` comando aggiorna una libreria installata tramite LibMan alla versione specificata.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="3fdd7-210">Si verifica un errore quando:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-210">An error occurs when:</span></span>

* <span data-ttu-id="3fdd7-211">Non esiste alcun *libman.js* nel file nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="3fdd7-212">La libreria specificata non esiste.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="3fdd7-213">Se è installata più di una libreria con lo stesso nome, viene richiesto di sceglierne una.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3fdd7-214">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="3fdd7-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3fdd7-215">Argomenti</span><span class="sxs-lookup"><span data-stu-id="3fdd7-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3fdd7-216">Nome della libreria da aggiornare.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="3fdd7-217">Opzioni</span><span class="sxs-lookup"><span data-stu-id="3fdd7-217">Options</span></span>

<span data-ttu-id="3fdd7-218">Per il comando `libman update` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="3fdd7-219">Ottenere la versione provvisoria più recente della libreria.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="3fdd7-220">Ottenere una versione specifica della libreria.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3fdd7-221">Esempi</span><span class="sxs-lookup"><span data-stu-id="3fdd7-221">Examples</span></span>

* <span data-ttu-id="3fdd7-222">Per aggiornare jQuery alla versione più recente:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="3fdd7-223">Per aggiornare jQuery alla versione 3.3.1:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="3fdd7-224">Per aggiornare jQuery all'ultima versione provvisoria:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="3fdd7-225">Gestisci cache di libreria</span><span class="sxs-lookup"><span data-stu-id="3fdd7-225">Manage library cache</span></span>

<span data-ttu-id="3fdd7-226">Il `libman cache` comando gestisce la cache della libreria LibMan.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="3fdd7-227">Il `filesystem` provider non utilizza la cache della libreria.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3fdd7-228">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="3fdd7-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3fdd7-229">Argomenti</span><span class="sxs-lookup"><span data-stu-id="3fdd7-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="3fdd7-230">Utilizzato solo con il `clean` comando.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-230">Only used with the `clean` command.</span></span> <span data-ttu-id="3fdd7-231">Specifica la cache del provider da pulire.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="3fdd7-232">I valori validi includono:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="3fdd7-233">Opzioni</span><span class="sxs-lookup"><span data-stu-id="3fdd7-233">Options</span></span>

<span data-ttu-id="3fdd7-234">Per il comando `libman cache` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="3fdd7-235">Elenca i nomi dei file memorizzati nella cache.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="3fdd7-236">Elenca i nomi delle librerie memorizzate nella cache.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3fdd7-237">Esempi</span><span class="sxs-lookup"><span data-stu-id="3fdd7-237">Examples</span></span>

* <span data-ttu-id="3fdd7-238">Per visualizzare i nomi delle librerie memorizzate nella cache per ogni provider, usare uno dei comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="3fdd7-239">Viene visualizzato output simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-239">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* <span data-ttu-id="3fdd7-240">Per visualizzare i nomi dei file di libreria memorizzati nella cache per ogni provider:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="3fdd7-241">Viene visualizzato output simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-241">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  <span data-ttu-id="3fdd7-242">Si noti che l'output precedente mostra che le versioni di jQuery 3.2.1 e 3.3.1 sono memorizzate nella cache del provider CDNJS.</span><span class="sxs-lookup"><span data-stu-id="3fdd7-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="3fdd7-243">Per svuotare la cache di libreria per il provider CDNJS:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="3fdd7-244">Dopo aver svuotato la cache del provider CDNJS, il `libman cache list` comando Visualizza quanto segue:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* <span data-ttu-id="3fdd7-245">Per svuotare la cache per tutti i provider supportati:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="3fdd7-246">Dopo aver svuotato tutte le cache del provider, il `libman cache list` comando Visualizza quanto segue:</span><span class="sxs-lookup"><span data-stu-id="3fdd7-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="3fdd7-247">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3fdd7-247">Additional resources</span></span>

* [<span data-ttu-id="3fdd7-248">Installare uno strumento globale</span><span class="sxs-lookup"><span data-stu-id="3fdd7-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="3fdd7-249">Repository GitHub di LibMan</span><span class="sxs-lookup"><span data-stu-id="3fdd7-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
