---
title: Gestire i riferimenti Protobuf con dotnet-grpc
author: juntaoluo
description: Scopri come aggiungere, aggiornare, rimuovere ed elencare i riferimenti Protobuf con lo strumento globale dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667335"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="84e2f-103">Gestire i riferimenti Protobuf con dotnet-grpc</span><span class="sxs-lookup"><span data-stu-id="84e2f-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="84e2f-104">A cura di [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="84e2f-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="84e2f-105">`dotnet-grpc`è uno strumento globale .NET Core per la gestione dei riferimenti [Protobuf (*.proto*)](xref:grpc/basics#proto-file) all'interno di un progetto gRPC .NET.</span><span class="sxs-lookup"><span data-stu-id="84e2f-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="84e2f-106">Lo strumento può essere utilizzato per aggiungere, aggiornare, rimuovere ed elencare i riferimenti Protobuf.</span><span class="sxs-lookup"><span data-stu-id="84e2f-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="84e2f-107">Installazione</span><span class="sxs-lookup"><span data-stu-id="84e2f-107">Installation</span></span>

<span data-ttu-id="84e2f-108">Per installare `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="84e2f-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="84e2f-109">Aggiungere riferimenti</span><span class="sxs-lookup"><span data-stu-id="84e2f-109">Add references</span></span>

<span data-ttu-id="84e2f-110">`dotnet-grpc`può essere utilizzato per aggiungere riferimenti `<Protobuf />` Protobuf come elementi al file *con estensione csproj:*</span><span class="sxs-lookup"><span data-stu-id="84e2f-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="84e2f-111">I riferimenti Protobuf vengono utilizzati per generare le risorse client e/o server di C.</span><span class="sxs-lookup"><span data-stu-id="84e2f-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="84e2f-112">Lo `dotnet-grpc` strumento può:</span><span class="sxs-lookup"><span data-stu-id="84e2f-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="84e2f-113">Creare un riferimento Protobuf dai file locali su disco.</span><span class="sxs-lookup"><span data-stu-id="84e2f-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="84e2f-114">Creare un riferimento Protobuf da un file remoto specificato da un URL.</span><span class="sxs-lookup"><span data-stu-id="84e2f-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="84e2f-115">Assicurarsi che le dipendenze corrette del pacchetto gRPC vengano aggiunte al progetto.</span><span class="sxs-lookup"><span data-stu-id="84e2f-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="84e2f-116">Ad esempio, `Grpc.AspNetCore` il pacchetto viene aggiunto a un'app Web.For example, the package is added to a web app.</span><span class="sxs-lookup"><span data-stu-id="84e2f-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="84e2f-117">`Grpc.AspNetCore`contiene librerie server e client gRPC e il supporto degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="84e2f-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="84e2f-118">In alternativa, `Grpc.Net.Client` `Grpc.Tools` i `Google.Protobuf` pacchetti , e , che contengono solo le librerie client gRPC e il supporto degli strumenti, vengono aggiunti a un'app Console.</span><span class="sxs-lookup"><span data-stu-id="84e2f-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="84e2f-119">Aggiungere file</span><span class="sxs-lookup"><span data-stu-id="84e2f-119">Add file</span></span>

<span data-ttu-id="84e2f-120">Il `add-file` comando viene utilizzato per aggiungere file locali su disco come riferimenti Protobuf.</span><span class="sxs-lookup"><span data-stu-id="84e2f-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="84e2f-121">I percorsi dei file forniti:</span><span class="sxs-lookup"><span data-stu-id="84e2f-121">The file paths provided:</span></span>

* <span data-ttu-id="84e2f-122">Può essere relativo alla directory corrente o ai percorsi assoluti.</span><span class="sxs-lookup"><span data-stu-id="84e2f-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="84e2f-123">Può contenere caratteri jolly per [il file globbing](https://wikipedia.org/wiki/Glob_(programming))basato su modelli .</span><span class="sxs-lookup"><span data-stu-id="84e2f-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="84e2f-124">Se alcuni file si trovano `Link` all'esterno della directory del `Protos` progetto, viene aggiunto un elemento per visualizzare il file nella cartella in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="84e2f-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="84e2f-125">Uso</span><span class="sxs-lookup"><span data-stu-id="84e2f-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="84e2f-126">Argomenti</span><span class="sxs-lookup"><span data-stu-id="84e2f-126">Arguments</span></span>

| <span data-ttu-id="84e2f-127">Argomento</span><span class="sxs-lookup"><span data-stu-id="84e2f-127">Argument</span></span> | <span data-ttu-id="84e2f-128">Descrizione</span><span class="sxs-lookup"><span data-stu-id="84e2f-128">Description</span></span> |
|-|-|
| <span data-ttu-id="84e2f-129">files</span><span class="sxs-lookup"><span data-stu-id="84e2f-129">files</span></span> | <span data-ttu-id="84e2f-130">Riferimenti al file protobuf.</span><span class="sxs-lookup"><span data-stu-id="84e2f-130">The protobuf file references.</span></span> <span data-ttu-id="84e2f-131">Questi possono essere un percorso a glob per i file protobuf locali.</span><span class="sxs-lookup"><span data-stu-id="84e2f-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="84e2f-132">Opzioni</span><span class="sxs-lookup"><span data-stu-id="84e2f-132">Options</span></span>

| <span data-ttu-id="84e2f-133">Opzione breve</span><span class="sxs-lookup"><span data-stu-id="84e2f-133">Short option</span></span> | <span data-ttu-id="84e2f-134">Opzione lunga</span><span class="sxs-lookup"><span data-stu-id="84e2f-134">Long option</span></span> | <span data-ttu-id="84e2f-135">Descrizione</span><span class="sxs-lookup"><span data-stu-id="84e2f-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="84e2f-136">-p</span><span class="sxs-lookup"><span data-stu-id="84e2f-136">-p</span></span> | <span data-ttu-id="84e2f-137">--progetto</span><span class="sxs-lookup"><span data-stu-id="84e2f-137">--project</span></span> | <span data-ttu-id="84e2f-138">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="84e2f-138">The path to the project file to operate on.</span></span> <span data-ttu-id="84e2f-139">Se non viene specificato un file, il comando ne cerca uno nella directory corrente.</span><span class="sxs-lookup"><span data-stu-id="84e2f-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="84e2f-140">-S</span><span class="sxs-lookup"><span data-stu-id="84e2f-140">-s</span></span> | <span data-ttu-id="84e2f-141">--servizi</span><span class="sxs-lookup"><span data-stu-id="84e2f-141">--services</span></span> | <span data-ttu-id="84e2f-142">Tipo di servizi gRPC che devono essere generati.</span><span class="sxs-lookup"><span data-stu-id="84e2f-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="84e2f-143">Se `Default` viene `Both` specificato, viene utilizzato `Client` per i progetti Web e viene utilizzato per i progetti non Web.</span><span class="sxs-lookup"><span data-stu-id="84e2f-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="84e2f-144">I valori `Both` `Client`accettati `None` `Server`sono , , `Default`, , .</span><span class="sxs-lookup"><span data-stu-id="84e2f-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="84e2f-145">-i</span><span class="sxs-lookup"><span data-stu-id="84e2f-145">-i</span></span> | <span data-ttu-id="84e2f-146">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="84e2f-146">--additional-import-dirs</span></span> | <span data-ttu-id="84e2f-147">Directory aggiuntive da utilizzare per la risoluzione delle importazioni per i file protobuf.</span><span class="sxs-lookup"><span data-stu-id="84e2f-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="84e2f-148">Si tratta di un elenco di percorsi separati da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="84e2f-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="84e2f-149">--accesso</span><span class="sxs-lookup"><span data-stu-id="84e2f-149">--access</span></span> | <span data-ttu-id="84e2f-150">Modificatore di accesso da utilizzare per le classi generate in C.</span><span class="sxs-lookup"><span data-stu-id="84e2f-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="84e2f-151">Il valore predefinito è `Public`.</span><span class="sxs-lookup"><span data-stu-id="84e2f-151">The default value is `Public`.</span></span> <span data-ttu-id="84e2f-152">I valori accettati sono `Internal` e `Public`.</span><span class="sxs-lookup"><span data-stu-id="84e2f-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="84e2f-153">Aggiungere URL</span><span class="sxs-lookup"><span data-stu-id="84e2f-153">Add URL</span></span>

<span data-ttu-id="84e2f-154">Il `add-url` comando viene utilizzato per aggiungere un file remoto specificato da un URL di origine come riferimento Protobuf.</span><span class="sxs-lookup"><span data-stu-id="84e2f-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="84e2f-155">È necessario specificare un percorso di file per specificare dove scaricare il file remoto.</span><span class="sxs-lookup"><span data-stu-id="84e2f-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="84e2f-156">Il percorso del file può essere relativo alla directory corrente o a un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="84e2f-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="84e2f-157">Se il percorso del file è `Link` esterno alla directory del progetto, `Protos` viene aggiunto un elemento per visualizzare il file nella cartella virtuale in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="84e2f-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="84e2f-158">Uso</span><span class="sxs-lookup"><span data-stu-id="84e2f-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="84e2f-159">Argomenti</span><span class="sxs-lookup"><span data-stu-id="84e2f-159">Arguments</span></span>

| <span data-ttu-id="84e2f-160">Argomento</span><span class="sxs-lookup"><span data-stu-id="84e2f-160">Argument</span></span> | <span data-ttu-id="84e2f-161">Descrizione</span><span class="sxs-lookup"><span data-stu-id="84e2f-161">Description</span></span> |
|-|-|
| <span data-ttu-id="84e2f-162">url</span><span class="sxs-lookup"><span data-stu-id="84e2f-162">url</span></span> | <span data-ttu-id="84e2f-163">URL di un file protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="84e2f-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="84e2f-164">Opzioni</span><span class="sxs-lookup"><span data-stu-id="84e2f-164">Options</span></span>

| <span data-ttu-id="84e2f-165">Opzione breve</span><span class="sxs-lookup"><span data-stu-id="84e2f-165">Short option</span></span> | <span data-ttu-id="84e2f-166">Opzione lunga</span><span class="sxs-lookup"><span data-stu-id="84e2f-166">Long option</span></span> | <span data-ttu-id="84e2f-167">Descrizione</span><span class="sxs-lookup"><span data-stu-id="84e2f-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="84e2f-168">-o</span><span class="sxs-lookup"><span data-stu-id="84e2f-168">-o</span></span> | <span data-ttu-id="84e2f-169">--output</span><span class="sxs-lookup"><span data-stu-id="84e2f-169">--output</span></span> | <span data-ttu-id="84e2f-170">Specifica il percorso di download per il file protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="84e2f-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="84e2f-171">Si tratta di un'opzione obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="84e2f-171">This is a required option.</span></span>
| <span data-ttu-id="84e2f-172">-p</span><span class="sxs-lookup"><span data-stu-id="84e2f-172">-p</span></span> | <span data-ttu-id="84e2f-173">--progetto</span><span class="sxs-lookup"><span data-stu-id="84e2f-173">--project</span></span> | <span data-ttu-id="84e2f-174">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="84e2f-174">The path to the project file to operate on.</span></span> <span data-ttu-id="84e2f-175">Se non viene specificato un file, il comando ne cerca uno nella directory corrente.</span><span class="sxs-lookup"><span data-stu-id="84e2f-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="84e2f-176">-S</span><span class="sxs-lookup"><span data-stu-id="84e2f-176">-s</span></span> | <span data-ttu-id="84e2f-177">--servizi</span><span class="sxs-lookup"><span data-stu-id="84e2f-177">--services</span></span> | <span data-ttu-id="84e2f-178">Tipo di servizi gRPC che devono essere generati.</span><span class="sxs-lookup"><span data-stu-id="84e2f-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="84e2f-179">Se `Default` viene `Both` specificato, viene utilizzato `Client` per i progetti Web e viene utilizzato per i progetti non Web.</span><span class="sxs-lookup"><span data-stu-id="84e2f-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="84e2f-180">I valori `Both` `Client`accettati `None` `Server`sono , , `Default`, , .</span><span class="sxs-lookup"><span data-stu-id="84e2f-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="84e2f-181">-i</span><span class="sxs-lookup"><span data-stu-id="84e2f-181">-i</span></span> | <span data-ttu-id="84e2f-182">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="84e2f-182">--additional-import-dirs</span></span> | <span data-ttu-id="84e2f-183">Directory aggiuntive da utilizzare per la risoluzione delle importazioni per i file protobuf.</span><span class="sxs-lookup"><span data-stu-id="84e2f-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="84e2f-184">Si tratta di un elenco di percorsi separati da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="84e2f-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="84e2f-185">--accesso</span><span class="sxs-lookup"><span data-stu-id="84e2f-185">--access</span></span> | <span data-ttu-id="84e2f-186">Modificatore di accesso da utilizzare per le classi generate in C.</span><span class="sxs-lookup"><span data-stu-id="84e2f-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="84e2f-187">Il valore predefinito è `Public`.</span><span class="sxs-lookup"><span data-stu-id="84e2f-187">Default value is `Public`.</span></span> <span data-ttu-id="84e2f-188">I valori accettati sono `Internal` e `Public`.</span><span class="sxs-lookup"><span data-stu-id="84e2f-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="84e2f-189">Rimuovere</span><span class="sxs-lookup"><span data-stu-id="84e2f-189">Remove</span></span>

<span data-ttu-id="84e2f-190">Il `remove` comando viene utilizzato per rimuovere i riferimenti Protobuf dal file *con estensione csproj.*</span><span class="sxs-lookup"><span data-stu-id="84e2f-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="84e2f-191">Il comando accetta gli argomenti di percorso e gli URL di origine come argomenti.</span><span class="sxs-lookup"><span data-stu-id="84e2f-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="84e2f-192">Lo strumento:</span><span class="sxs-lookup"><span data-stu-id="84e2f-192">The tool:</span></span>

* <span data-ttu-id="84e2f-193">Rimuove solo il riferimento Protobuf.</span><span class="sxs-lookup"><span data-stu-id="84e2f-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="84e2f-194">Non elimina il file *proto,* anche se è stato originariamente scaricato da un URL remoto.</span><span class="sxs-lookup"><span data-stu-id="84e2f-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="84e2f-195">Uso</span><span class="sxs-lookup"><span data-stu-id="84e2f-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="84e2f-196">Argomenti</span><span class="sxs-lookup"><span data-stu-id="84e2f-196">Arguments</span></span>

| <span data-ttu-id="84e2f-197">Argomento</span><span class="sxs-lookup"><span data-stu-id="84e2f-197">Argument</span></span> | <span data-ttu-id="84e2f-198">Descrizione</span><span class="sxs-lookup"><span data-stu-id="84e2f-198">Description</span></span> |
|-|-|
| <span data-ttu-id="84e2f-199">references</span><span class="sxs-lookup"><span data-stu-id="84e2f-199">references</span></span> | <span data-ttu-id="84e2f-200">Gli URL o i percorsi dei file dei riferimenti protobuf da rimuovere.</span><span class="sxs-lookup"><span data-stu-id="84e2f-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="84e2f-201">Opzioni</span><span class="sxs-lookup"><span data-stu-id="84e2f-201">Options</span></span>

| <span data-ttu-id="84e2f-202">Opzione breve</span><span class="sxs-lookup"><span data-stu-id="84e2f-202">Short option</span></span> | <span data-ttu-id="84e2f-203">Opzione lunga</span><span class="sxs-lookup"><span data-stu-id="84e2f-203">Long option</span></span> | <span data-ttu-id="84e2f-204">Descrizione</span><span class="sxs-lookup"><span data-stu-id="84e2f-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="84e2f-205">-p</span><span class="sxs-lookup"><span data-stu-id="84e2f-205">-p</span></span> | <span data-ttu-id="84e2f-206">--progetto</span><span class="sxs-lookup"><span data-stu-id="84e2f-206">--project</span></span> | <span data-ttu-id="84e2f-207">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="84e2f-207">The path to the project file to operate on.</span></span> <span data-ttu-id="84e2f-208">Se non viene specificato un file, il comando ne cerca uno nella directory corrente.</span><span class="sxs-lookup"><span data-stu-id="84e2f-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="84e2f-209">Aggiorna</span><span class="sxs-lookup"><span data-stu-id="84e2f-209">Refresh</span></span>

<span data-ttu-id="84e2f-210">Il `refresh` comando viene utilizzato per aggiornare un riferimento remoto con il contenuto più recente dall'URL di origine.</span><span class="sxs-lookup"><span data-stu-id="84e2f-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="84e2f-211">Sia il percorso del file di download che l'URL di origine possono essere utilizzati per specificare il riferimento da aggiornare.</span><span class="sxs-lookup"><span data-stu-id="84e2f-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="84e2f-212">Nota:</span><span class="sxs-lookup"><span data-stu-id="84e2f-212">Note:</span></span>

* <span data-ttu-id="84e2f-213">Gli iste del contenuto del file vengono confrontati per determinare se il file locale deve essere aggiornato.</span><span class="sxs-lookup"><span data-stu-id="84e2f-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="84e2f-214">Non viene confrontata alcuna informazione timestamp.</span><span class="sxs-lookup"><span data-stu-id="84e2f-214">No timestamp information is compared.</span></span>

<span data-ttu-id="84e2f-215">Lo strumento sostituisce sempre il file locale con il file remoto se è necessario un aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="84e2f-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="84e2f-216">Uso</span><span class="sxs-lookup"><span data-stu-id="84e2f-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="84e2f-217">Argomenti</span><span class="sxs-lookup"><span data-stu-id="84e2f-217">Arguments</span></span>

| <span data-ttu-id="84e2f-218">Argomento</span><span class="sxs-lookup"><span data-stu-id="84e2f-218">Argument</span></span> | <span data-ttu-id="84e2f-219">Descrizione</span><span class="sxs-lookup"><span data-stu-id="84e2f-219">Description</span></span> |
|-|-|
| <span data-ttu-id="84e2f-220">references</span><span class="sxs-lookup"><span data-stu-id="84e2f-220">references</span></span> | <span data-ttu-id="84e2f-221">GLI URL o i percorsi dei file ai riferimenti protobuf remoti che devono essere aggiornati.</span><span class="sxs-lookup"><span data-stu-id="84e2f-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="84e2f-222">Lasciare vuoto questo argomento per aggiornare tutti i riferimenti remoti.</span><span class="sxs-lookup"><span data-stu-id="84e2f-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="84e2f-223">Opzioni</span><span class="sxs-lookup"><span data-stu-id="84e2f-223">Options</span></span>

| <span data-ttu-id="84e2f-224">Opzione breve</span><span class="sxs-lookup"><span data-stu-id="84e2f-224">Short option</span></span> | <span data-ttu-id="84e2f-225">Opzione lunga</span><span class="sxs-lookup"><span data-stu-id="84e2f-225">Long option</span></span> | <span data-ttu-id="84e2f-226">Descrizione</span><span class="sxs-lookup"><span data-stu-id="84e2f-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="84e2f-227">-p</span><span class="sxs-lookup"><span data-stu-id="84e2f-227">-p</span></span> | <span data-ttu-id="84e2f-228">--progetto</span><span class="sxs-lookup"><span data-stu-id="84e2f-228">--project</span></span> | <span data-ttu-id="84e2f-229">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="84e2f-229">The path to the project file to operate on.</span></span> <span data-ttu-id="84e2f-230">Se non viene specificato un file, il comando ne cerca uno nella directory corrente.</span><span class="sxs-lookup"><span data-stu-id="84e2f-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="84e2f-231">--secco-run</span><span class="sxs-lookup"><span data-stu-id="84e2f-231">--dry-run</span></span> | <span data-ttu-id="84e2f-232">Genera un elenco di file che verrebbero aggiornati senza scaricare alcun nuovo contenuto.</span><span class="sxs-lookup"><span data-stu-id="84e2f-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="84e2f-233">Elenco</span><span class="sxs-lookup"><span data-stu-id="84e2f-233">List</span></span>

<span data-ttu-id="84e2f-234">Il `list` comando viene utilizzato per visualizzare tutti i riferimenti Protobuf nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="84e2f-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="84e2f-235">Se tutti i valori di una colonna sono valori predefiniti, la colonna può essere omessa.</span><span class="sxs-lookup"><span data-stu-id="84e2f-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="84e2f-236">Uso</span><span class="sxs-lookup"><span data-stu-id="84e2f-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="84e2f-237">Opzioni</span><span class="sxs-lookup"><span data-stu-id="84e2f-237">Options</span></span>

| <span data-ttu-id="84e2f-238">Opzione breve</span><span class="sxs-lookup"><span data-stu-id="84e2f-238">Short option</span></span> | <span data-ttu-id="84e2f-239">Opzione lunga</span><span class="sxs-lookup"><span data-stu-id="84e2f-239">Long option</span></span> | <span data-ttu-id="84e2f-240">Descrizione</span><span class="sxs-lookup"><span data-stu-id="84e2f-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="84e2f-241">-p</span><span class="sxs-lookup"><span data-stu-id="84e2f-241">-p</span></span> | <span data-ttu-id="84e2f-242">--progetto</span><span class="sxs-lookup"><span data-stu-id="84e2f-242">--project</span></span> | <span data-ttu-id="84e2f-243">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="84e2f-243">The path to the project file to operate on.</span></span> <span data-ttu-id="84e2f-244">Se non viene specificato un file, il comando ne cerca uno nella directory corrente.</span><span class="sxs-lookup"><span data-stu-id="84e2f-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84e2f-245">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="84e2f-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
