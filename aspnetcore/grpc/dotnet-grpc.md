---
title: Gestire i riferimenti Protobuf con dotnet-grpc
author: juntaoluo
description: Informazioni sull'aggiunta, l'aggiornamento, la rimozione e l'elenco di riferimenti protobuf con lo strumento globale DotNet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
no-loc:
- appsettings.json
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: f34e1543d9695e138a85db3b79e013cf5fb6d138
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059910"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="439bd-103">Gestire i riferimenti Protobuf con dotnet-grpc</span><span class="sxs-lookup"><span data-stu-id="439bd-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="439bd-104">A cura di [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="439bd-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="439bd-105">`dotnet-grpc` è uno strumento globale .NET Core per la gestione dei riferimenti [protobuf (*. proto*)](xref:grpc/basics#proto-file) in un progetto .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="439bd-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="439bd-106">Lo strumento può essere usato per aggiungere, aggiornare, rimuovere ed elencare i riferimenti a protobuf.</span><span class="sxs-lookup"><span data-stu-id="439bd-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="439bd-107">Installazione</span><span class="sxs-lookup"><span data-stu-id="439bd-107">Installation</span></span>

<span data-ttu-id="439bd-108">Per installare lo `dotnet-grpc` [strumento globale di .NET Core](/dotnet/core/tools/global-tools), eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="439bd-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="439bd-109">Aggiungere riferimenti</span><span class="sxs-lookup"><span data-stu-id="439bd-109">Add references</span></span>

<span data-ttu-id="439bd-110">`dotnet-grpc` può essere usato per aggiungere riferimenti protobuf come `<Protobuf />` elementi al file con *estensione csproj* :</span><span class="sxs-lookup"><span data-stu-id="439bd-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="439bd-111">I riferimenti protobuf vengono usati per generare il client C# e/o le risorse del server.</span><span class="sxs-lookup"><span data-stu-id="439bd-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="439bd-112">Lo `dotnet-grpc` strumento consente di:</span><span class="sxs-lookup"><span data-stu-id="439bd-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="439bd-113">Creare un riferimento protobuf da file locali su disco.</span><span class="sxs-lookup"><span data-stu-id="439bd-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="439bd-114">Creare un riferimento protobuf da un file remoto specificato da un URL.</span><span class="sxs-lookup"><span data-stu-id="439bd-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="439bd-115">Verificare che al progetto siano state aggiunte le dipendenze del pacchetto gRPC corrette.</span><span class="sxs-lookup"><span data-stu-id="439bd-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="439bd-116">Ad esempio, il `Grpc.AspNetCore` pacchetto viene aggiunto a un'app Web.</span><span class="sxs-lookup"><span data-stu-id="439bd-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="439bd-117">`Grpc.AspNetCore` contiene le librerie client e server gRPC e il supporto degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="439bd-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="439bd-118">In alternativa, i `Grpc.Net.Client` `Grpc.Tools` pacchetti e, `Google.Protobuf` che contengono solo le librerie client e il supporto per gli strumenti di gRPC, vengono aggiunti a un'app console.</span><span class="sxs-lookup"><span data-stu-id="439bd-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="439bd-119">Aggiungere file</span><span class="sxs-lookup"><span data-stu-id="439bd-119">Add file</span></span>

<span data-ttu-id="439bd-120">Il `add-file` comando viene usato per aggiungere file locali su disco come riferimenti a protobuf.</span><span class="sxs-lookup"><span data-stu-id="439bd-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="439bd-121">I percorsi dei file forniti:</span><span class="sxs-lookup"><span data-stu-id="439bd-121">The file paths provided:</span></span>

* <span data-ttu-id="439bd-122">Può essere relativo alla directory corrente o ai percorsi assoluti.</span><span class="sxs-lookup"><span data-stu-id="439bd-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="439bd-123">Può contenere caratteri jolly per i file basati su pattern [glob](https://wikipedia.org/wiki/Glob_(programming)).</span><span class="sxs-lookup"><span data-stu-id="439bd-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="439bd-124">Se i file si trovano all'esterno della directory del progetto, `Link` viene aggiunto un elemento per visualizzare il file `Protos` nella cartella in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="439bd-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="439bd-125">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="439bd-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="439bd-126">Argomenti</span><span class="sxs-lookup"><span data-stu-id="439bd-126">Arguments</span></span>

| <span data-ttu-id="439bd-127">Argomento</span><span class="sxs-lookup"><span data-stu-id="439bd-127">Argument</span></span> | <span data-ttu-id="439bd-128">Descrizione</span><span class="sxs-lookup"><span data-stu-id="439bd-128">Description</span></span> |
|-|-|
| <span data-ttu-id="439bd-129">files</span><span class="sxs-lookup"><span data-stu-id="439bd-129">files</span></span> | <span data-ttu-id="439bd-130">Il file protobuf fa riferimento a.</span><span class="sxs-lookup"><span data-stu-id="439bd-130">The protobuf file references.</span></span> <span data-ttu-id="439bd-131">Può trattarsi di un percorso di glob per i file protobuf locali.</span><span class="sxs-lookup"><span data-stu-id="439bd-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="439bd-132">Opzioni</span><span class="sxs-lookup"><span data-stu-id="439bd-132">Options</span></span>

| <span data-ttu-id="439bd-133">Short-opzione</span><span class="sxs-lookup"><span data-stu-id="439bd-133">Short option</span></span> | <span data-ttu-id="439bd-134">Opzione Long</span><span class="sxs-lookup"><span data-stu-id="439bd-134">Long option</span></span> | <span data-ttu-id="439bd-135">Descrizione</span><span class="sxs-lookup"><span data-stu-id="439bd-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="439bd-136">-p</span><span class="sxs-lookup"><span data-stu-id="439bd-136">-p</span></span> | <span data-ttu-id="439bd-137">--progetto</span><span class="sxs-lookup"><span data-stu-id="439bd-137">--project</span></span> | <span data-ttu-id="439bd-138">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="439bd-138">The path to the project file to operate on.</span></span> <span data-ttu-id="439bd-139">Se non si specifica un file, il comando Cerca una directory corrente.</span><span class="sxs-lookup"><span data-stu-id="439bd-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="439bd-140">-S</span><span class="sxs-lookup"><span data-stu-id="439bd-140">-s</span></span> | <span data-ttu-id="439bd-141">--Servizi</span><span class="sxs-lookup"><span data-stu-id="439bd-141">--services</span></span> | <span data-ttu-id="439bd-142">Tipo di servizi gRPC da generare.</span><span class="sxs-lookup"><span data-stu-id="439bd-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="439bd-143">Se `Default` si specifica, `Both` viene utilizzato per i progetti Web e `Client` viene utilizzato per i progetti non Web.</span><span class="sxs-lookup"><span data-stu-id="439bd-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="439bd-144">I valori accettati sono `Both` ,, `Client` `Default` , `None` , `Server` .</span><span class="sxs-lookup"><span data-stu-id="439bd-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="439bd-145">-i</span><span class="sxs-lookup"><span data-stu-id="439bd-145">-i</span></span> | <span data-ttu-id="439bd-146">--Additional-Import-directory</span><span class="sxs-lookup"><span data-stu-id="439bd-146">--additional-import-dirs</span></span> | <span data-ttu-id="439bd-147">Directory aggiuntive da usare quando si risolvono le importazioni per i file protobuf.</span><span class="sxs-lookup"><span data-stu-id="439bd-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="439bd-148">Si tratta di un elenco di percorsi separati da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="439bd-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="439bd-149">--accesso</span><span class="sxs-lookup"><span data-stu-id="439bd-149">--access</span></span> | <span data-ttu-id="439bd-150">Modificatore di accesso da usare per le classi C# generate.</span><span class="sxs-lookup"><span data-stu-id="439bd-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="439bd-151">Il valore predefinito è `Public`.</span><span class="sxs-lookup"><span data-stu-id="439bd-151">The default value is `Public`.</span></span> <span data-ttu-id="439bd-152">I valori accettati sono `Internal` e `Public`.</span><span class="sxs-lookup"><span data-stu-id="439bd-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="439bd-153">Aggiungere URL</span><span class="sxs-lookup"><span data-stu-id="439bd-153">Add URL</span></span>

<span data-ttu-id="439bd-154">Il `add-url` comando viene usato per aggiungere un file remoto specificato da un URL di origine come riferimento protobuf.</span><span class="sxs-lookup"><span data-stu-id="439bd-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="439bd-155">È necessario fornire un percorso di file per specificare dove scaricare il file remoto.</span><span class="sxs-lookup"><span data-stu-id="439bd-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="439bd-156">Il percorso del file può essere relativo alla directory corrente o a un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="439bd-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="439bd-157">Se il percorso del file è esterno alla directory del progetto, `Link` viene aggiunto un elemento per visualizzare il file nella cartella virtuale `Protos` in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="439bd-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="439bd-158">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="439bd-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="439bd-159">Argomenti</span><span class="sxs-lookup"><span data-stu-id="439bd-159">Arguments</span></span>

| <span data-ttu-id="439bd-160">Argomento</span><span class="sxs-lookup"><span data-stu-id="439bd-160">Argument</span></span> | <span data-ttu-id="439bd-161">Descrizione</span><span class="sxs-lookup"><span data-stu-id="439bd-161">Description</span></span> |
|-|-|
| <span data-ttu-id="439bd-162">url</span><span class="sxs-lookup"><span data-stu-id="439bd-162">url</span></span> | <span data-ttu-id="439bd-163">URL di un file protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="439bd-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="439bd-164">Opzioni</span><span class="sxs-lookup"><span data-stu-id="439bd-164">Options</span></span>

| <span data-ttu-id="439bd-165">Short-opzione</span><span class="sxs-lookup"><span data-stu-id="439bd-165">Short option</span></span> | <span data-ttu-id="439bd-166">Opzione Long</span><span class="sxs-lookup"><span data-stu-id="439bd-166">Long option</span></span> | <span data-ttu-id="439bd-167">Descrizione</span><span class="sxs-lookup"><span data-stu-id="439bd-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="439bd-168">-o</span><span class="sxs-lookup"><span data-stu-id="439bd-168">-o</span></span> | <span data-ttu-id="439bd-169">--output</span><span class="sxs-lookup"><span data-stu-id="439bd-169">--output</span></span> | <span data-ttu-id="439bd-170">Specifica il percorso di download per il file protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="439bd-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="439bd-171">Si tratta di un'opzione obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="439bd-171">This is a required option.</span></span>
| <span data-ttu-id="439bd-172">-p</span><span class="sxs-lookup"><span data-stu-id="439bd-172">-p</span></span> | <span data-ttu-id="439bd-173">--progetto</span><span class="sxs-lookup"><span data-stu-id="439bd-173">--project</span></span> | <span data-ttu-id="439bd-174">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="439bd-174">The path to the project file to operate on.</span></span> <span data-ttu-id="439bd-175">Se non si specifica un file, il comando Cerca una directory corrente.</span><span class="sxs-lookup"><span data-stu-id="439bd-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="439bd-176">-S</span><span class="sxs-lookup"><span data-stu-id="439bd-176">-s</span></span> | <span data-ttu-id="439bd-177">--Servizi</span><span class="sxs-lookup"><span data-stu-id="439bd-177">--services</span></span> | <span data-ttu-id="439bd-178">Tipo di servizi gRPC da generare.</span><span class="sxs-lookup"><span data-stu-id="439bd-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="439bd-179">Se `Default` si specifica, `Both` viene utilizzato per i progetti Web e `Client` viene utilizzato per i progetti non Web.</span><span class="sxs-lookup"><span data-stu-id="439bd-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="439bd-180">I valori accettati sono `Both` ,, `Client` `Default` , `None` , `Server` .</span><span class="sxs-lookup"><span data-stu-id="439bd-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="439bd-181">-i</span><span class="sxs-lookup"><span data-stu-id="439bd-181">-i</span></span> | <span data-ttu-id="439bd-182">--Additional-Import-directory</span><span class="sxs-lookup"><span data-stu-id="439bd-182">--additional-import-dirs</span></span> | <span data-ttu-id="439bd-183">Directory aggiuntive da usare quando si risolvono le importazioni per i file protobuf.</span><span class="sxs-lookup"><span data-stu-id="439bd-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="439bd-184">Si tratta di un elenco di percorsi separati da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="439bd-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="439bd-185">--accesso</span><span class="sxs-lookup"><span data-stu-id="439bd-185">--access</span></span> | <span data-ttu-id="439bd-186">Modificatore di accesso da usare per le classi C# generate.</span><span class="sxs-lookup"><span data-stu-id="439bd-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="439bd-187">Il valore predefinito è `Public`.</span><span class="sxs-lookup"><span data-stu-id="439bd-187">Default value is `Public`.</span></span> <span data-ttu-id="439bd-188">I valori accettati sono `Internal` e `Public`.</span><span class="sxs-lookup"><span data-stu-id="439bd-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="439bd-189">Rimuovi</span><span class="sxs-lookup"><span data-stu-id="439bd-189">Remove</span></span>

<span data-ttu-id="439bd-190">Il `remove` comando viene usato per rimuovere i riferimenti protobuf dal file con *estensione csproj* .</span><span class="sxs-lookup"><span data-stu-id="439bd-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="439bd-191">Il comando accetta gli argomenti del percorso e gli URL di origine come argomenti.</span><span class="sxs-lookup"><span data-stu-id="439bd-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="439bd-192">Lo strumento:</span><span class="sxs-lookup"><span data-stu-id="439bd-192">The tool:</span></span>

* <span data-ttu-id="439bd-193">Rimuove solo il riferimento protobuf.</span><span class="sxs-lookup"><span data-stu-id="439bd-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="439bd-194">Non elimina il file *. proto* , neanche se è stato originariamente scaricato da un URL remoto.</span><span class="sxs-lookup"><span data-stu-id="439bd-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="439bd-195">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="439bd-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="439bd-196">Argomenti</span><span class="sxs-lookup"><span data-stu-id="439bd-196">Arguments</span></span>

| <span data-ttu-id="439bd-197">Argomento</span><span class="sxs-lookup"><span data-stu-id="439bd-197">Argument</span></span> | <span data-ttu-id="439bd-198">Descrizione</span><span class="sxs-lookup"><span data-stu-id="439bd-198">Description</span></span> |
|-|-|
| <span data-ttu-id="439bd-199">references</span><span class="sxs-lookup"><span data-stu-id="439bd-199">references</span></span> | <span data-ttu-id="439bd-200">URL o percorsi di file dei riferimenti protobuf da rimuovere.</span><span class="sxs-lookup"><span data-stu-id="439bd-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="439bd-201">Opzioni</span><span class="sxs-lookup"><span data-stu-id="439bd-201">Options</span></span>

| <span data-ttu-id="439bd-202">Short-opzione</span><span class="sxs-lookup"><span data-stu-id="439bd-202">Short option</span></span> | <span data-ttu-id="439bd-203">Opzione Long</span><span class="sxs-lookup"><span data-stu-id="439bd-203">Long option</span></span> | <span data-ttu-id="439bd-204">Descrizione</span><span class="sxs-lookup"><span data-stu-id="439bd-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="439bd-205">-p</span><span class="sxs-lookup"><span data-stu-id="439bd-205">-p</span></span> | <span data-ttu-id="439bd-206">--progetto</span><span class="sxs-lookup"><span data-stu-id="439bd-206">--project</span></span> | <span data-ttu-id="439bd-207">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="439bd-207">The path to the project file to operate on.</span></span> <span data-ttu-id="439bd-208">Se non si specifica un file, il comando Cerca una directory corrente.</span><span class="sxs-lookup"><span data-stu-id="439bd-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="439bd-209">Aggiorna</span><span class="sxs-lookup"><span data-stu-id="439bd-209">Refresh</span></span>

<span data-ttu-id="439bd-210">Il `refresh` comando viene usato per aggiornare un riferimento remoto con il contenuto più recente dall'URL di origine.</span><span class="sxs-lookup"><span data-stu-id="439bd-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="439bd-211">È possibile usare sia il percorso del file di download che l'URL di origine per specificare il riferimento da aggiornare.</span><span class="sxs-lookup"><span data-stu-id="439bd-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="439bd-212">Nota:</span><span class="sxs-lookup"><span data-stu-id="439bd-212">Note:</span></span>

* <span data-ttu-id="439bd-213">Gli hash del contenuto del file vengono confrontati per determinare se è necessario aggiornare il file locale.</span><span class="sxs-lookup"><span data-stu-id="439bd-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="439bd-214">Nessuna informazione sul timestamp viene confrontata.</span><span class="sxs-lookup"><span data-stu-id="439bd-214">No timestamp information is compared.</span></span>

<span data-ttu-id="439bd-215">Se è necessario un aggiornamento, lo strumento sostituisce sempre il file locale con il file remoto.</span><span class="sxs-lookup"><span data-stu-id="439bd-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="439bd-216">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="439bd-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="439bd-217">Argomenti</span><span class="sxs-lookup"><span data-stu-id="439bd-217">Arguments</span></span>

| <span data-ttu-id="439bd-218">Argomento</span><span class="sxs-lookup"><span data-stu-id="439bd-218">Argument</span></span> | <span data-ttu-id="439bd-219">Descrizione</span><span class="sxs-lookup"><span data-stu-id="439bd-219">Description</span></span> |
|-|-|
| <span data-ttu-id="439bd-220">references</span><span class="sxs-lookup"><span data-stu-id="439bd-220">references</span></span> | <span data-ttu-id="439bd-221">Gli URL o i percorsi di file per i riferimenti protobuf remoti che devono essere aggiornati.</span><span class="sxs-lookup"><span data-stu-id="439bd-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="439bd-222">Lasciare vuoto questo argomento per aggiornare tutti i riferimenti remoti.</span><span class="sxs-lookup"><span data-stu-id="439bd-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="439bd-223">Opzioni</span><span class="sxs-lookup"><span data-stu-id="439bd-223">Options</span></span>

| <span data-ttu-id="439bd-224">Short-opzione</span><span class="sxs-lookup"><span data-stu-id="439bd-224">Short option</span></span> | <span data-ttu-id="439bd-225">Opzione Long</span><span class="sxs-lookup"><span data-stu-id="439bd-225">Long option</span></span> | <span data-ttu-id="439bd-226">Descrizione</span><span class="sxs-lookup"><span data-stu-id="439bd-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="439bd-227">-p</span><span class="sxs-lookup"><span data-stu-id="439bd-227">-p</span></span> | <span data-ttu-id="439bd-228">--progetto</span><span class="sxs-lookup"><span data-stu-id="439bd-228">--project</span></span> | <span data-ttu-id="439bd-229">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="439bd-229">The path to the project file to operate on.</span></span> <span data-ttu-id="439bd-230">Se non si specifica un file, il comando Cerca una directory corrente.</span><span class="sxs-lookup"><span data-stu-id="439bd-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="439bd-231">--esecuzione a secco</span><span class="sxs-lookup"><span data-stu-id="439bd-231">--dry-run</span></span> | <span data-ttu-id="439bd-232">Restituisce un elenco di file che verrebbero aggiornati senza scaricare alcun nuovo contenuto.</span><span class="sxs-lookup"><span data-stu-id="439bd-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="439bd-233">Elenco</span><span class="sxs-lookup"><span data-stu-id="439bd-233">List</span></span>

<span data-ttu-id="439bd-234">Il `list` comando viene usato per visualizzare tutti i riferimenti protobuf nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="439bd-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="439bd-235">Se tutti i valori di una colonna sono valori predefiniti, è possibile omettere la colonna.</span><span class="sxs-lookup"><span data-stu-id="439bd-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="439bd-236">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="439bd-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="439bd-237">Opzioni</span><span class="sxs-lookup"><span data-stu-id="439bd-237">Options</span></span>

| <span data-ttu-id="439bd-238">Short-opzione</span><span class="sxs-lookup"><span data-stu-id="439bd-238">Short option</span></span> | <span data-ttu-id="439bd-239">Opzione Long</span><span class="sxs-lookup"><span data-stu-id="439bd-239">Long option</span></span> | <span data-ttu-id="439bd-240">Descrizione</span><span class="sxs-lookup"><span data-stu-id="439bd-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="439bd-241">-p</span><span class="sxs-lookup"><span data-stu-id="439bd-241">-p</span></span> | <span data-ttu-id="439bd-242">--progetto</span><span class="sxs-lookup"><span data-stu-id="439bd-242">--project</span></span> | <span data-ttu-id="439bd-243">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="439bd-243">The path to the project file to operate on.</span></span> <span data-ttu-id="439bd-244">Se non si specifica un file, il comando Cerca una directory corrente.</span><span class="sxs-lookup"><span data-stu-id="439bd-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="439bd-245">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="439bd-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
