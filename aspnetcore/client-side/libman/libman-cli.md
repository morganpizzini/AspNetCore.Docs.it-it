---
title: Usare la RIGA di comando LibMan con ASP.NET CoreUse the LibMan CLI with ASP.NET Core
author: scottaddie
description: Informazioni su come usare l'interfaccia della riga di comando LibMan in un progetto ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664633"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="8914d-103">Usare la RIGA di comando LibMan con ASP.NET CoreUse the LibMan CLI with ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8914d-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="8914d-104">Di [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="8914d-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="8914d-105">La [CLI LibMan](xref:client-side/libman/index) è uno strumento multipiattaforma supportato ovunque .NET Core sia supportato.</span><span class="sxs-lookup"><span data-stu-id="8914d-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8914d-106">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="8914d-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="8914d-107">Installazione</span><span class="sxs-lookup"><span data-stu-id="8914d-107">Installation</span></span>

<span data-ttu-id="8914d-108">Per installare l'interfaccia della riga di comando libMan:</span><span class="sxs-lookup"><span data-stu-id="8914d-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="8914d-109">Uno [strumento globale .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) viene installato dal pacchetto [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="8914d-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="8914d-110">Per installare l'interfaccia della riga di comando LibMan da un'origine del pacchetto NuGet specifica:</span><span class="sxs-lookup"><span data-stu-id="8914d-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="8914d-111">Nell'esempio precedente, uno strumento globale di .NET Core viene installato dal file *C:* .</span><span class="sxs-lookup"><span data-stu-id="8914d-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="8914d-112">Uso</span><span class="sxs-lookup"><span data-stu-id="8914d-112">Usage</span></span>

<span data-ttu-id="8914d-113">Dopo la corretta installazione dell'interfaccia della riga di comando, è possibile utilizzare il seguente comando:</span><span class="sxs-lookup"><span data-stu-id="8914d-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="8914d-114">Per visualizzare la versione dell'interfaccia della riga di comando installata:</span><span class="sxs-lookup"><span data-stu-id="8914d-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="8914d-115">Per visualizzare i comandi dell'interfaccia della riga di comando disponibili:</span><span class="sxs-lookup"><span data-stu-id="8914d-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="8914d-116">Il comando precedente visualizza un output simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="8914d-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="8914d-117">Le sezioni seguenti descrivono i comandi dell'interfaccia della riga di comando disponibili.</span><span class="sxs-lookup"><span data-stu-id="8914d-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="8914d-118">Inizializzare LibMan nel progetto</span><span class="sxs-lookup"><span data-stu-id="8914d-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="8914d-119">Il `libman init` comando crea un file *libman.json* se non ne esiste uno.</span><span class="sxs-lookup"><span data-stu-id="8914d-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="8914d-120">Il file viene creato con il contenuto del modello di elemento predefinito.</span><span class="sxs-lookup"><span data-stu-id="8914d-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8914d-121">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="8914d-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="8914d-122">Opzioni</span><span class="sxs-lookup"><span data-stu-id="8914d-122">Options</span></span>

<span data-ttu-id="8914d-123">Per il comando `libman init` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8914d-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="8914d-124">Percorso relativo alla cartella corrente.</span><span class="sxs-lookup"><span data-stu-id="8914d-124">A path relative to the current folder.</span></span> <span data-ttu-id="8914d-125">I file di libreria vengono `destination` installati in questa posizione se non è definita alcuna proprietà per una libreria in *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8914d-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="8914d-126">Il `<PATH>` valore viene `defaultDestination` scritto nella proprietà di *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8914d-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="8914d-127">Provider da utilizzare se non è definito alcun provider per una determinata libreria.</span><span class="sxs-lookup"><span data-stu-id="8914d-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="8914d-128">Il `<PROVIDER>` valore viene `defaultProvider` scritto nella proprietà di *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8914d-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="8914d-129">Sostituire `<PROVIDER>` con uno dei seguenti valori:</span><span class="sxs-lookup"><span data-stu-id="8914d-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8914d-130">Esempi</span><span class="sxs-lookup"><span data-stu-id="8914d-130">Examples</span></span>

<span data-ttu-id="8914d-131">Per creare un file *libman.json* in un progetto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8914d-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="8914d-132">Passare alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="8914d-132">Navigate to the project root.</span></span>
* <span data-ttu-id="8914d-133">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="8914d-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="8914d-134">Digitare il nome del provider `Enter` predefinito o premere per usare il provider CDNJS predefinito.</span><span class="sxs-lookup"><span data-stu-id="8914d-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="8914d-135">I valori validi includono:</span><span class="sxs-lookup"><span data-stu-id="8914d-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init command - provider predefinito](_static/libman-init-provider.png)

<span data-ttu-id="8914d-137">Un file libman.json viene aggiunto alla radice del progetto con il contenuto seguente:A *libman.json* file is added to the project root with the following content:</span><span class="sxs-lookup"><span data-stu-id="8914d-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="8914d-138">Aggiungere file di libreria</span><span class="sxs-lookup"><span data-stu-id="8914d-138">Add library files</span></span>

<span data-ttu-id="8914d-139">Il `libman install` comando scarica e installa i file di libreria nel progetto.</span><span class="sxs-lookup"><span data-stu-id="8914d-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="8914d-140">Se non ne esiste uno, viene aggiunto un file *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="8914d-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="8914d-141">Il file *libman.json* viene modificato per archiviare i dettagli di configurazione per i file di libreria.</span><span class="sxs-lookup"><span data-stu-id="8914d-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8914d-142">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="8914d-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="8914d-143">Argomenti</span><span class="sxs-lookup"><span data-stu-id="8914d-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="8914d-144">Nome della libreria da installare.</span><span class="sxs-lookup"><span data-stu-id="8914d-144">The name of the library to install.</span></span> <span data-ttu-id="8914d-145">Questo nome può includere la notazione `@1.2.0`del numero di versione (ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="8914d-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="8914d-146">Opzioni</span><span class="sxs-lookup"><span data-stu-id="8914d-146">Options</span></span>

<span data-ttu-id="8914d-147">Per il comando `libman install` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8914d-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="8914d-148">Percorso in cui installare la libreria.</span><span class="sxs-lookup"><span data-stu-id="8914d-148">The location to install the library.</span></span> <span data-ttu-id="8914d-149">Se non specificato, viene utilizzato il percorso predefinito.</span><span class="sxs-lookup"><span data-stu-id="8914d-149">If not specified, the default location is used.</span></span> <span data-ttu-id="8914d-150">Se `defaultDestination` in *libman.json*non viene specificata alcuna proprietà , questa opzione è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="8914d-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="8914d-151">Specificare il nome del file da installare dalla libreria.</span><span class="sxs-lookup"><span data-stu-id="8914d-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="8914d-152">Se non specificato, vengono installati tutti i file della raccolta.</span><span class="sxs-lookup"><span data-stu-id="8914d-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="8914d-153">Specificare `--files` un'opzione per ogni file da installare.</span><span class="sxs-lookup"><span data-stu-id="8914d-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="8914d-154">Sono supportati anche i percorsi relativi.</span><span class="sxs-lookup"><span data-stu-id="8914d-154">Relative paths are supported too.</span></span> <span data-ttu-id="8914d-155">Ad esempio: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="8914d-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="8914d-156">Nome del provider da utilizzare per l'acquisizione della libreria.</span><span class="sxs-lookup"><span data-stu-id="8914d-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="8914d-157">Sostituire `<PROVIDER>` con uno dei seguenti valori:</span><span class="sxs-lookup"><span data-stu-id="8914d-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="8914d-158">Se non specificato, viene utilizzata la `defaultProvider` proprietà in *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="8914d-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="8914d-159">Se `defaultProvider` in *libman.json*non viene specificata alcuna proprietà , questa opzione è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="8914d-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8914d-160">Esempi</span><span class="sxs-lookup"><span data-stu-id="8914d-160">Examples</span></span>

<span data-ttu-id="8914d-161">Si consideri il seguente file *libman.json:*</span><span class="sxs-lookup"><span data-stu-id="8914d-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="8914d-162">Per installare il file jQuery versione 3.2.1 *jquery.min.js* nella cartella *wwwroot/scripts/jquery* utilizzando il provider CDNJS:</span><span class="sxs-lookup"><span data-stu-id="8914d-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="8914d-163">Il file *libman.json* è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="8914d-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="8914d-164">Per installare i file *calendar.js* e *calendar.css* da \*C:\\\\temp contosoCalendar\\ \* utilizzando il provider del file system:</span><span class="sxs-lookup"><span data-stu-id="8914d-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="8914d-165">Per due motivi viene visualizzato il seguente messaggio:</span><span class="sxs-lookup"><span data-stu-id="8914d-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="8914d-166">Il file *libman.json* non `defaultDestination` contiene una proprietà.</span><span class="sxs-lookup"><span data-stu-id="8914d-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="8914d-167">Il `libman install` comando non contiene `-d|--destination` l'opzione.</span><span class="sxs-lookup"><span data-stu-id="8914d-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman comando di installazione - destinazione](_static/libman-install-destination.png)

<span data-ttu-id="8914d-169">Dopo aver accettato la destinazione predefinita, il file *libman.json* è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="8914d-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="8914d-170">Ripristinare i file di libreria</span><span class="sxs-lookup"><span data-stu-id="8914d-170">Restore library files</span></span>

<span data-ttu-id="8914d-171">Il `libman restore` comando installa i file di libreria definiti in *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8914d-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="8914d-172">Sono applicabili le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="8914d-172">The following rules apply:</span></span>

* <span data-ttu-id="8914d-173">Se nella radice del progetto non è presente alcun file *libman.json,* viene restituito un errore.</span><span class="sxs-lookup"><span data-stu-id="8914d-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="8914d-174">Se una libreria specifica un `defaultProvider` provider, la proprietà in *libman.json* viene ignorata.</span><span class="sxs-lookup"><span data-stu-id="8914d-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="8914d-175">Se una libreria specifica una `defaultDestination` destinazione, la proprietà in *libman.json* viene ignorata.</span><span class="sxs-lookup"><span data-stu-id="8914d-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8914d-176">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="8914d-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="8914d-177">Opzioni</span><span class="sxs-lookup"><span data-stu-id="8914d-177">Options</span></span>

<span data-ttu-id="8914d-178">Per il comando `libman restore` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8914d-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8914d-179">Esempi</span><span class="sxs-lookup"><span data-stu-id="8914d-179">Examples</span></span>

<span data-ttu-id="8914d-180">Per ripristinare i file di libreria definiti in *libman.json*:</span><span class="sxs-lookup"><span data-stu-id="8914d-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="8914d-181">Eliminare i file di libreria</span><span class="sxs-lookup"><span data-stu-id="8914d-181">Delete library files</span></span>

<span data-ttu-id="8914d-182">Il `libman clean` comando elimina i file di libreria precedentemente ripristinati tramite LibMan.</span><span class="sxs-lookup"><span data-stu-id="8914d-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="8914d-183">Le cartelle che diventano vuote dopo questa operazione vengono eliminate.</span><span class="sxs-lookup"><span data-stu-id="8914d-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="8914d-184">Le configurazioni associate dei `libraries` file di libreria nella proprietà di *libman.json* non vengono rimosse.</span><span class="sxs-lookup"><span data-stu-id="8914d-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8914d-185">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="8914d-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="8914d-186">Opzioni</span><span class="sxs-lookup"><span data-stu-id="8914d-186">Options</span></span>

<span data-ttu-id="8914d-187">Per il comando `libman clean` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8914d-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8914d-188">Esempi</span><span class="sxs-lookup"><span data-stu-id="8914d-188">Examples</span></span>

<span data-ttu-id="8914d-189">Per eliminare i file di libreria installati tramite LibMan:</span><span class="sxs-lookup"><span data-stu-id="8914d-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="8914d-190">Disinstallare i file di libreria</span><span class="sxs-lookup"><span data-stu-id="8914d-190">Uninstall library files</span></span>

<span data-ttu-id="8914d-191">Il `libman uninstall` comando:</span><span class="sxs-lookup"><span data-stu-id="8914d-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="8914d-192">Elimina tutti i file associati alla libreria specificata dalla destinazione in *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8914d-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="8914d-193">Rimuove la configurazione di libreria associata da *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="8914d-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="8914d-194">Si verifica un errore quando:</span><span class="sxs-lookup"><span data-stu-id="8914d-194">An error occurs when:</span></span>

* <span data-ttu-id="8914d-195">Nella radice del progetto non è presente alcun file *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="8914d-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="8914d-196">La libreria specificata non esiste.</span><span class="sxs-lookup"><span data-stu-id="8914d-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="8914d-197">Se è installata più di una libreria con lo stesso nome, viene richiesto di sceglierne una.</span><span class="sxs-lookup"><span data-stu-id="8914d-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8914d-198">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="8914d-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="8914d-199">Argomenti</span><span class="sxs-lookup"><span data-stu-id="8914d-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="8914d-200">Nome della libreria da disinstallare.</span><span class="sxs-lookup"><span data-stu-id="8914d-200">The name of the library to uninstall.</span></span> <span data-ttu-id="8914d-201">Questo nome può includere la notazione `@1.2.0`del numero di versione (ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="8914d-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="8914d-202">Opzioni</span><span class="sxs-lookup"><span data-stu-id="8914d-202">Options</span></span>

<span data-ttu-id="8914d-203">Per il comando `libman uninstall` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8914d-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8914d-204">Esempi</span><span class="sxs-lookup"><span data-stu-id="8914d-204">Examples</span></span>

<span data-ttu-id="8914d-205">Si consideri il seguente file *libman.json:*</span><span class="sxs-lookup"><span data-stu-id="8914d-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="8914d-206">Per disinstallare jQuery, uno dei seguenti comandi ha esito positivo:</span><span class="sxs-lookup"><span data-stu-id="8914d-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="8914d-207">Per disinstallare i file `filesystem` Lodash installati tramite il provider:</span><span class="sxs-lookup"><span data-stu-id="8914d-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="8914d-208">Aggiornare la versione della libreria</span><span class="sxs-lookup"><span data-stu-id="8914d-208">Update library version</span></span>

<span data-ttu-id="8914d-209">Il `libman update` comando aggiorna una libreria installata tramite LibMan alla versione specificata.</span><span class="sxs-lookup"><span data-stu-id="8914d-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="8914d-210">Si verifica un errore quando:</span><span class="sxs-lookup"><span data-stu-id="8914d-210">An error occurs when:</span></span>

* <span data-ttu-id="8914d-211">Nella radice del progetto non è presente alcun file *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="8914d-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="8914d-212">La libreria specificata non esiste.</span><span class="sxs-lookup"><span data-stu-id="8914d-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="8914d-213">Se è installata più di una libreria con lo stesso nome, viene richiesto di sceglierne una.</span><span class="sxs-lookup"><span data-stu-id="8914d-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8914d-214">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="8914d-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="8914d-215">Argomenti</span><span class="sxs-lookup"><span data-stu-id="8914d-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="8914d-216">Nome della libreria da aggiornare.</span><span class="sxs-lookup"><span data-stu-id="8914d-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="8914d-217">Opzioni</span><span class="sxs-lookup"><span data-stu-id="8914d-217">Options</span></span>

<span data-ttu-id="8914d-218">Per il comando `libman update` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8914d-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="8914d-219">Ottenere l'ultima versione non definitiva della libreria.</span><span class="sxs-lookup"><span data-stu-id="8914d-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="8914d-220">Ottenere una versione specifica della libreria.</span><span class="sxs-lookup"><span data-stu-id="8914d-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8914d-221">Esempi</span><span class="sxs-lookup"><span data-stu-id="8914d-221">Examples</span></span>

* <span data-ttu-id="8914d-222">Per aggiornare jQuery alla versione più recente:</span><span class="sxs-lookup"><span data-stu-id="8914d-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="8914d-223">Per aggiornare jQuery alla versione 3.3.1:</span><span class="sxs-lookup"><span data-stu-id="8914d-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="8914d-224">Per aggiornare jQuery all'ultima versione non definitiva:</span><span class="sxs-lookup"><span data-stu-id="8914d-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="8914d-225">Gestire la cache della libreria</span><span class="sxs-lookup"><span data-stu-id="8914d-225">Manage library cache</span></span>

<span data-ttu-id="8914d-226">Il `libman cache` comando gestisce la cache della libreria LibMan.</span><span class="sxs-lookup"><span data-stu-id="8914d-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="8914d-227">Il `filesystem` provider non utilizza la cache della libreria.</span><span class="sxs-lookup"><span data-stu-id="8914d-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="8914d-228">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="8914d-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="8914d-229">Argomenti</span><span class="sxs-lookup"><span data-stu-id="8914d-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="8914d-230">Utilizzato solo `clean` con il comando.</span><span class="sxs-lookup"><span data-stu-id="8914d-230">Only used with the `clean` command.</span></span> <span data-ttu-id="8914d-231">Specifica la cache del provider da pulire.</span><span class="sxs-lookup"><span data-stu-id="8914d-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="8914d-232">I valori validi includono:</span><span class="sxs-lookup"><span data-stu-id="8914d-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="8914d-233">Opzioni</span><span class="sxs-lookup"><span data-stu-id="8914d-233">Options</span></span>

<span data-ttu-id="8914d-234">Per il comando `libman cache` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8914d-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="8914d-235">Elencare i nomi dei file memorizzati nella cache.</span><span class="sxs-lookup"><span data-stu-id="8914d-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="8914d-236">Elencare i nomi delle librerie memorizzate nella cache.</span><span class="sxs-lookup"><span data-stu-id="8914d-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="8914d-237">Esempi</span><span class="sxs-lookup"><span data-stu-id="8914d-237">Examples</span></span>

* <span data-ttu-id="8914d-238">Per visualizzare i nomi delle librerie memorizzate nella cache per ogni provider, utilizzare uno dei comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8914d-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="8914d-239">Viene visualizzato output simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="8914d-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="8914d-240">Per visualizzare i nomi dei file di libreria memorizzati nella cache per provider:</span><span class="sxs-lookup"><span data-stu-id="8914d-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="8914d-241">Viene visualizzato output simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="8914d-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="8914d-242">Si noti che l'output precedente mostra che le versioni di jQuery 3.2.1 e 3.3.1 vengono memorizzate nella cache del provider CDNJS.</span><span class="sxs-lookup"><span data-stu-id="8914d-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="8914d-243">Per svuotare la cache della libreria per il provider CDNJS:</span><span class="sxs-lookup"><span data-stu-id="8914d-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="8914d-244">Dopo aver svuotato la `libman cache list` cache del provider CDNJS, il comando visualizza quanto segue:</span><span class="sxs-lookup"><span data-stu-id="8914d-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="8914d-245">Per svuotare la cache per tutti i provider supportati:</span><span class="sxs-lookup"><span data-stu-id="8914d-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="8914d-246">Dopo aver svuotato tutte `libman cache list` le cache del provider, il comando visualizza quanto segue:</span><span class="sxs-lookup"><span data-stu-id="8914d-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="8914d-247">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8914d-247">Additional resources</span></span>

* [<span data-ttu-id="8914d-248">Installare uno strumento globale</span><span class="sxs-lookup"><span data-stu-id="8914d-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="8914d-249">Repository GitHub di LibMan</span><span class="sxs-lookup"><span data-stu-id="8914d-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
