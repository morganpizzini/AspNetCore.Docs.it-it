---
title: Gestire i riferimenti Protobuf con dotnet-grpc
author: juntaoluo
description: Informazioni sull'aggiunta, l'aggiornamento, la rimozione e l'elenco di riferimenti protobuf con lo strumento globale DotNet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/dotnet-grpc
ms.openlocfilehash: 0990013947be2cee5045deac92efc3c6bcf12e03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768835"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="c7dfc-103">Gestire i riferimenti Protobuf con dotnet-grpc</span><span class="sxs-lookup"><span data-stu-id="c7dfc-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="c7dfc-104">A cura di [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="c7dfc-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="c7dfc-105">`dotnet-grpc`è uno strumento globale .NET Core per la gestione dei riferimenti [protobuf (*. proto*)](xref:grpc/basics#proto-file) in un progetto .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="c7dfc-106">Lo strumento può essere usato per aggiungere, aggiornare, rimuovere ed elencare i riferimenti a protobuf.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="c7dfc-107">Installazione</span><span class="sxs-lookup"><span data-stu-id="c7dfc-107">Installation</span></span>

<span data-ttu-id="c7dfc-108">Per installare lo `dotnet-grpc` [strumento globale di .NET Core](/dotnet/core/tools/global-tools), eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c7dfc-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="c7dfc-109">Aggiungere riferimenti</span><span class="sxs-lookup"><span data-stu-id="c7dfc-109">Add references</span></span>

<span data-ttu-id="c7dfc-110">`dotnet-grpc`può essere usato per aggiungere riferimenti protobuf come `<Protobuf />` elementi al file con *estensione csproj* :</span><span class="sxs-lookup"><span data-stu-id="c7dfc-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="c7dfc-111">I riferimenti protobuf vengono usati per generare il client C# e/o le risorse del server.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="c7dfc-112">Lo `dotnet-grpc` strumento consente di:</span><span class="sxs-lookup"><span data-stu-id="c7dfc-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="c7dfc-113">Creare un riferimento protobuf da file locali su disco.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="c7dfc-114">Creare un riferimento protobuf da un file remoto specificato da un URL.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="c7dfc-115">Verificare che al progetto siano state aggiunte le dipendenze del pacchetto gRPC corrette.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="c7dfc-116">Ad esempio, il `Grpc.AspNetCore` pacchetto viene aggiunto a un'app Web.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="c7dfc-117">`Grpc.AspNetCore`contiene le librerie client e server gRPC e il supporto degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="c7dfc-118">In alternativa, i `Grpc.Net.Client`pacchetti `Grpc.Tools` e `Google.Protobuf` , che contengono solo le librerie client e il supporto per gli strumenti di gRPC, vengono aggiunti a un'app console.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="c7dfc-119">Aggiungere file</span><span class="sxs-lookup"><span data-stu-id="c7dfc-119">Add file</span></span>

<span data-ttu-id="c7dfc-120">Il `add-file` comando viene usato per aggiungere file locali su disco come riferimenti a protobuf.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="c7dfc-121">I percorsi dei file forniti:</span><span class="sxs-lookup"><span data-stu-id="c7dfc-121">The file paths provided:</span></span>

* <span data-ttu-id="c7dfc-122">Può essere relativo alla directory corrente o ai percorsi assoluti.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="c7dfc-123">Può contenere caratteri jolly per i file basati su pattern [glob](https://wikipedia.org/wiki/Glob_(programming)).</span><span class="sxs-lookup"><span data-stu-id="c7dfc-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="c7dfc-124">Se i file si trovano all'esterno della directory del `Link` progetto, viene aggiunto un elemento per visualizzare il file `Protos` nella cartella in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="c7dfc-125">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="c7dfc-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="c7dfc-126">Arguments</span><span class="sxs-lookup"><span data-stu-id="c7dfc-126">Arguments</span></span>

| <span data-ttu-id="c7dfc-127">Argomento</span><span class="sxs-lookup"><span data-stu-id="c7dfc-127">Argument</span></span> | <span data-ttu-id="c7dfc-128">Descrizione</span><span class="sxs-lookup"><span data-stu-id="c7dfc-128">Description</span></span> |
|-|-|
| <span data-ttu-id="c7dfc-129">files</span><span class="sxs-lookup"><span data-stu-id="c7dfc-129">files</span></span> | <span data-ttu-id="c7dfc-130">Il file protobuf fa riferimento a.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-130">The protobuf file references.</span></span> <span data-ttu-id="c7dfc-131">Può trattarsi di un percorso di glob per i file protobuf locali.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="c7dfc-132">Opzioni</span><span class="sxs-lookup"><span data-stu-id="c7dfc-132">Options</span></span>

| <span data-ttu-id="c7dfc-133">Short-opzione</span><span class="sxs-lookup"><span data-stu-id="c7dfc-133">Short option</span></span> | <span data-ttu-id="c7dfc-134">Opzione Long</span><span class="sxs-lookup"><span data-stu-id="c7dfc-134">Long option</span></span> | <span data-ttu-id="c7dfc-135">Description</span><span class="sxs-lookup"><span data-stu-id="c7dfc-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="c7dfc-136">-p</span><span class="sxs-lookup"><span data-stu-id="c7dfc-136">-p</span></span> | <span data-ttu-id="c7dfc-137">--progetto</span><span class="sxs-lookup"><span data-stu-id="c7dfc-137">--project</span></span> | <span data-ttu-id="c7dfc-138">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-138">The path to the project file to operate on.</span></span> <span data-ttu-id="c7dfc-139">Se non si specifica un file, il comando Cerca una directory corrente.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="c7dfc-140">-S</span><span class="sxs-lookup"><span data-stu-id="c7dfc-140">-s</span></span> | <span data-ttu-id="c7dfc-141">--Servizi</span><span class="sxs-lookup"><span data-stu-id="c7dfc-141">--services</span></span> | <span data-ttu-id="c7dfc-142">Tipo di servizi gRPC da generare.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="c7dfc-143">Se `Default` si specifica, `Both` viene utilizzato per i progetti Web `Client` e viene utilizzato per i progetti non Web.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="c7dfc-144">I `Both`valori accettati `Client`sono `Default`, `None`, `Server`,,.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="c7dfc-145">-i</span><span class="sxs-lookup"><span data-stu-id="c7dfc-145">-i</span></span> | <span data-ttu-id="c7dfc-146">--Additional-Import-directory</span><span class="sxs-lookup"><span data-stu-id="c7dfc-146">--additional-import-dirs</span></span> | <span data-ttu-id="c7dfc-147">Directory aggiuntive da usare quando si risolvono le importazioni per i file protobuf.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="c7dfc-148">Si tratta di un elenco di percorsi separati da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="c7dfc-149">--accesso</span><span class="sxs-lookup"><span data-stu-id="c7dfc-149">--access</span></span> | <span data-ttu-id="c7dfc-150">Modificatore di accesso da usare per le classi C# generate.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="c7dfc-151">Il valore predefinito è `Public`.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-151">The default value is `Public`.</span></span> <span data-ttu-id="c7dfc-152">I valori accettati sono `Internal` e `Public`.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="c7dfc-153">Aggiungere URL</span><span class="sxs-lookup"><span data-stu-id="c7dfc-153">Add URL</span></span>

<span data-ttu-id="c7dfc-154">Il `add-url` comando viene usato per aggiungere un file remoto specificato da un URL di origine come riferimento protobuf.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="c7dfc-155">È necessario fornire un percorso di file per specificare dove scaricare il file remoto.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="c7dfc-156">Il percorso del file può essere relativo alla directory corrente o a un percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="c7dfc-157">Se il percorso del file è esterno alla directory del progetto `Link` , viene aggiunto un elemento per visualizzare il file nella cartella `Protos` virtuale in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="c7dfc-158">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="c7dfc-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="c7dfc-159">Arguments</span><span class="sxs-lookup"><span data-stu-id="c7dfc-159">Arguments</span></span>

| <span data-ttu-id="c7dfc-160">Argomento</span><span class="sxs-lookup"><span data-stu-id="c7dfc-160">Argument</span></span> | <span data-ttu-id="c7dfc-161">Descrizione</span><span class="sxs-lookup"><span data-stu-id="c7dfc-161">Description</span></span> |
|-|-|
| <span data-ttu-id="c7dfc-162">url</span><span class="sxs-lookup"><span data-stu-id="c7dfc-162">url</span></span> | <span data-ttu-id="c7dfc-163">URL di un file protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="c7dfc-164">Opzioni</span><span class="sxs-lookup"><span data-stu-id="c7dfc-164">Options</span></span>

| <span data-ttu-id="c7dfc-165">Short-opzione</span><span class="sxs-lookup"><span data-stu-id="c7dfc-165">Short option</span></span> | <span data-ttu-id="c7dfc-166">Opzione Long</span><span class="sxs-lookup"><span data-stu-id="c7dfc-166">Long option</span></span> | <span data-ttu-id="c7dfc-167">Description</span><span class="sxs-lookup"><span data-stu-id="c7dfc-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="c7dfc-168">-o</span><span class="sxs-lookup"><span data-stu-id="c7dfc-168">-o</span></span> | <span data-ttu-id="c7dfc-169">--output</span><span class="sxs-lookup"><span data-stu-id="c7dfc-169">--output</span></span> | <span data-ttu-id="c7dfc-170">Specifica il percorso di download per il file protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="c7dfc-171">Si tratta di un'opzione obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-171">This is a required option.</span></span>
| <span data-ttu-id="c7dfc-172">-p</span><span class="sxs-lookup"><span data-stu-id="c7dfc-172">-p</span></span> | <span data-ttu-id="c7dfc-173">--progetto</span><span class="sxs-lookup"><span data-stu-id="c7dfc-173">--project</span></span> | <span data-ttu-id="c7dfc-174">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-174">The path to the project file to operate on.</span></span> <span data-ttu-id="c7dfc-175">Se non si specifica un file, il comando Cerca una directory corrente.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="c7dfc-176">-S</span><span class="sxs-lookup"><span data-stu-id="c7dfc-176">-s</span></span> | <span data-ttu-id="c7dfc-177">--Servizi</span><span class="sxs-lookup"><span data-stu-id="c7dfc-177">--services</span></span> | <span data-ttu-id="c7dfc-178">Tipo di servizi gRPC da generare.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="c7dfc-179">Se `Default` si specifica, `Both` viene utilizzato per i progetti Web `Client` e viene utilizzato per i progetti non Web.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="c7dfc-180">I `Both`valori accettati `Client`sono `Default`, `None`, `Server`,,.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="c7dfc-181">-i</span><span class="sxs-lookup"><span data-stu-id="c7dfc-181">-i</span></span> | <span data-ttu-id="c7dfc-182">--Additional-Import-directory</span><span class="sxs-lookup"><span data-stu-id="c7dfc-182">--additional-import-dirs</span></span> | <span data-ttu-id="c7dfc-183">Directory aggiuntive da usare quando si risolvono le importazioni per i file protobuf.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="c7dfc-184">Si tratta di un elenco di percorsi separati da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="c7dfc-185">--accesso</span><span class="sxs-lookup"><span data-stu-id="c7dfc-185">--access</span></span> | <span data-ttu-id="c7dfc-186">Modificatore di accesso da usare per le classi C# generate.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="c7dfc-187">Il valore predefinito è `Public`.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-187">Default value is `Public`.</span></span> <span data-ttu-id="c7dfc-188">I valori accettati sono `Internal` e `Public`.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="c7dfc-189">Remove</span><span class="sxs-lookup"><span data-stu-id="c7dfc-189">Remove</span></span>

<span data-ttu-id="c7dfc-190">Il `remove` comando viene usato per rimuovere i riferimenti protobuf dal file con *estensione csproj* .</span><span class="sxs-lookup"><span data-stu-id="c7dfc-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="c7dfc-191">Il comando accetta gli argomenti del percorso e gli URL di origine come argomenti.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="c7dfc-192">Lo strumento:</span><span class="sxs-lookup"><span data-stu-id="c7dfc-192">The tool:</span></span>

* <span data-ttu-id="c7dfc-193">Rimuove solo il riferimento protobuf.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="c7dfc-194">Non elimina il file *. proto* , neanche se è stato originariamente scaricato da un URL remoto.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="c7dfc-195">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="c7dfc-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="c7dfc-196">Arguments</span><span class="sxs-lookup"><span data-stu-id="c7dfc-196">Arguments</span></span>

| <span data-ttu-id="c7dfc-197">Argomento</span><span class="sxs-lookup"><span data-stu-id="c7dfc-197">Argument</span></span> | <span data-ttu-id="c7dfc-198">Descrizione</span><span class="sxs-lookup"><span data-stu-id="c7dfc-198">Description</span></span> |
|-|-|
| <span data-ttu-id="c7dfc-199">references</span><span class="sxs-lookup"><span data-stu-id="c7dfc-199">references</span></span> | <span data-ttu-id="c7dfc-200">URL o percorsi di file dei riferimenti protobuf da rimuovere.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="c7dfc-201">Opzioni</span><span class="sxs-lookup"><span data-stu-id="c7dfc-201">Options</span></span>

| <span data-ttu-id="c7dfc-202">Short-opzione</span><span class="sxs-lookup"><span data-stu-id="c7dfc-202">Short option</span></span> | <span data-ttu-id="c7dfc-203">Opzione Long</span><span class="sxs-lookup"><span data-stu-id="c7dfc-203">Long option</span></span> | <span data-ttu-id="c7dfc-204">Description</span><span class="sxs-lookup"><span data-stu-id="c7dfc-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="c7dfc-205">-p</span><span class="sxs-lookup"><span data-stu-id="c7dfc-205">-p</span></span> | <span data-ttu-id="c7dfc-206">--progetto</span><span class="sxs-lookup"><span data-stu-id="c7dfc-206">--project</span></span> | <span data-ttu-id="c7dfc-207">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-207">The path to the project file to operate on.</span></span> <span data-ttu-id="c7dfc-208">Se non si specifica un file, il comando Cerca una directory corrente.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="c7dfc-209">Aggiorna</span><span class="sxs-lookup"><span data-stu-id="c7dfc-209">Refresh</span></span>

<span data-ttu-id="c7dfc-210">Il `refresh` comando viene usato per aggiornare un riferimento remoto con il contenuto più recente dall'URL di origine.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="c7dfc-211">È possibile usare sia il percorso del file di download che l'URL di origine per specificare il riferimento da aggiornare.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="c7dfc-212">Nota:</span><span class="sxs-lookup"><span data-stu-id="c7dfc-212">Note:</span></span>

* <span data-ttu-id="c7dfc-213">Gli hash del contenuto del file vengono confrontati per determinare se è necessario aggiornare il file locale.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="c7dfc-214">Nessuna informazione sul timestamp viene confrontata.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-214">No timestamp information is compared.</span></span>

<span data-ttu-id="c7dfc-215">Se è necessario un aggiornamento, lo strumento sostituisce sempre il file locale con il file remoto.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="c7dfc-216">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="c7dfc-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="c7dfc-217">Arguments</span><span class="sxs-lookup"><span data-stu-id="c7dfc-217">Arguments</span></span>

| <span data-ttu-id="c7dfc-218">Argomento</span><span class="sxs-lookup"><span data-stu-id="c7dfc-218">Argument</span></span> | <span data-ttu-id="c7dfc-219">Descrizione</span><span class="sxs-lookup"><span data-stu-id="c7dfc-219">Description</span></span> |
|-|-|
| <span data-ttu-id="c7dfc-220">references</span><span class="sxs-lookup"><span data-stu-id="c7dfc-220">references</span></span> | <span data-ttu-id="c7dfc-221">Gli URL o i percorsi di file per i riferimenti protobuf remoti che devono essere aggiornati.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="c7dfc-222">Lasciare vuoto questo argomento per aggiornare tutti i riferimenti remoti.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="c7dfc-223">Opzioni</span><span class="sxs-lookup"><span data-stu-id="c7dfc-223">Options</span></span>

| <span data-ttu-id="c7dfc-224">Short-opzione</span><span class="sxs-lookup"><span data-stu-id="c7dfc-224">Short option</span></span> | <span data-ttu-id="c7dfc-225">Opzione Long</span><span class="sxs-lookup"><span data-stu-id="c7dfc-225">Long option</span></span> | <span data-ttu-id="c7dfc-226">Description</span><span class="sxs-lookup"><span data-stu-id="c7dfc-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="c7dfc-227">-p</span><span class="sxs-lookup"><span data-stu-id="c7dfc-227">-p</span></span> | <span data-ttu-id="c7dfc-228">--progetto</span><span class="sxs-lookup"><span data-stu-id="c7dfc-228">--project</span></span> | <span data-ttu-id="c7dfc-229">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-229">The path to the project file to operate on.</span></span> <span data-ttu-id="c7dfc-230">Se non si specifica un file, il comando Cerca una directory corrente.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="c7dfc-231">--esecuzione a secco</span><span class="sxs-lookup"><span data-stu-id="c7dfc-231">--dry-run</span></span> | <span data-ttu-id="c7dfc-232">Restituisce un elenco di file che verrebbero aggiornati senza scaricare alcun nuovo contenuto.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="c7dfc-233">Elenco</span><span class="sxs-lookup"><span data-stu-id="c7dfc-233">List</span></span>

<span data-ttu-id="c7dfc-234">Il `list` comando viene usato per visualizzare tutti i riferimenti protobuf nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="c7dfc-235">Se tutti i valori di una colonna sono valori predefiniti, è possibile omettere la colonna.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="c7dfc-236">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="c7dfc-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="c7dfc-237">Opzioni</span><span class="sxs-lookup"><span data-stu-id="c7dfc-237">Options</span></span>

| <span data-ttu-id="c7dfc-238">Short-opzione</span><span class="sxs-lookup"><span data-stu-id="c7dfc-238">Short option</span></span> | <span data-ttu-id="c7dfc-239">Opzione Long</span><span class="sxs-lookup"><span data-stu-id="c7dfc-239">Long option</span></span> | <span data-ttu-id="c7dfc-240">Description</span><span class="sxs-lookup"><span data-stu-id="c7dfc-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="c7dfc-241">-p</span><span class="sxs-lookup"><span data-stu-id="c7dfc-241">-p</span></span> | <span data-ttu-id="c7dfc-242">--progetto</span><span class="sxs-lookup"><span data-stu-id="c7dfc-242">--project</span></span> | <span data-ttu-id="c7dfc-243">Percorso del file di progetto su cui operare.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-243">The path to the project file to operate on.</span></span> <span data-ttu-id="c7dfc-244">Se non si specifica un file, il comando Cerca una directory corrente.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c7dfc-245">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c7dfc-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
