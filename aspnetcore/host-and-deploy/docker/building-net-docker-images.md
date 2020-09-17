---
title: Immagini Docker per ASP.NET Core
author: rick-anderson
description: Informazioni su come usare le immagini Docker per .NET Core pubblicate dal registro Docker. Eseguire il pull di immagini e compilare immagini personalizzate.
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 791cc14feac30ad8a571d155624ab821dfd77981
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722858"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="ebada-104">Immagini Docker per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebada-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="ebada-105">Questa esercitazione mostra come eseguire un'app ASP.NET Core in contenitori Docker.</span><span class="sxs-lookup"><span data-stu-id="ebada-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="ebada-106">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="ebada-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="ebada-107">Informazioni sulle immagini Docker per Microsoft .NET Core</span><span class="sxs-lookup"><span data-stu-id="ebada-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="ebada-108">Download di un'app di esempio ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebada-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="ebada-109">Eseguire l'app di esempio in locale</span><span class="sxs-lookup"><span data-stu-id="ebada-109">Run the sample app locally</span></span>
> * <span data-ttu-id="ebada-110">Esecuzione dell'app di esempio in contenitori Linux</span><span class="sxs-lookup"><span data-stu-id="ebada-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="ebada-111">Esecuzione dell'app di esempio in contenitori Windows</span><span class="sxs-lookup"><span data-stu-id="ebada-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="ebada-112">Compilazione e distribuzione manuali</span><span class="sxs-lookup"><span data-stu-id="ebada-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="ebada-113">Immagini Docker per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebada-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="ebada-114">Per questa esercitazione si scaricherà un'app di esempio ASP.NET Core, che verrà eseguita in contenitori Docker.</span><span class="sxs-lookup"><span data-stu-id="ebada-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="ebada-115">L'esempio usa contenitori sia Linux che Windows.</span><span class="sxs-lookup"><span data-stu-id="ebada-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="ebada-116">Il documento Dockerfile di esempio usa la [funzionalità di compilazione in più fasi di Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) per la compilazione e l'esecuzione in contenitori diversi.</span><span class="sxs-lookup"><span data-stu-id="ebada-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="ebada-117">I contenitori di compilazione ed esecuzione vengono creati da immagini fornite nell'hub Docker da Microsoft:</span><span class="sxs-lookup"><span data-stu-id="ebada-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="ebada-118">L'esempio usa questa immagine per la compilazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="ebada-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="ebada-119">L'immagine contiene .NET Core SDK, che include strumenti da riga di comando (interfaccia della riga di comando).</span><span class="sxs-lookup"><span data-stu-id="ebada-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="ebada-120">L'immagine è ottimizzata per lo sviluppo, il debug e il testing unità locali.</span><span class="sxs-lookup"><span data-stu-id="ebada-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="ebada-121">Gli strumenti installati per lo sviluppo e la compilazione rendono questa immagine relativamente grande.</span><span class="sxs-lookup"><span data-stu-id="ebada-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="ebada-122">L'esempio usa questa immagine per l'esecuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="ebada-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="ebada-123">L'immagine contiene il runtime e le librerie ASP.NET Core ed è ottimizzata per l'esecuzione di app nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="ebada-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="ebada-124">Progettata per offrire velocità di distribuzione e avvio dell'app, l'immagine è relativamente piccola, in modo da ottimizzare le prestazioni di rete dal registro Docker all'host Docker.</span><span class="sxs-lookup"><span data-stu-id="ebada-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="ebada-125">Solo i file binari e i contenuti necessari per eseguire un'app vengono copiati nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="ebada-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="ebada-126">I contenuti sono pronti per l'esecuzione, fornendo i tempi più rapidi da `Docker run` all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="ebada-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="ebada-127">La compilazione dinamica del codice non è necessaria nel modello di Docker.</span><span class="sxs-lookup"><span data-stu-id="ebada-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ebada-128">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="ebada-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="ebada-129">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="ebada-129">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="ebada-130">.NET Core SDK 3.0</span><span class="sxs-lookup"><span data-stu-id="ebada-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="ebada-131">Client di Docker 18.03 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="ebada-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="ebada-132">Distribuzioni di Linux</span><span class="sxs-lookup"><span data-stu-id="ebada-132">Linux distributions</span></span>
    * [<span data-ttu-id="ebada-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="ebada-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="ebada-134">Debian</span><span class="sxs-lookup"><span data-stu-id="ebada-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="ebada-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="ebada-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="ebada-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="ebada-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="ebada-137">macOS</span><span class="sxs-lookup"><span data-stu-id="ebada-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="ebada-138">Windows</span><span class="sxs-lookup"><span data-stu-id="ebada-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="ebada-139">Git</span><span class="sxs-lookup"><span data-stu-id="ebada-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="ebada-140">Scaricare l'app di esempio</span><span class="sxs-lookup"><span data-stu-id="ebada-140">Download the sample app</span></span>

* <span data-ttu-id="ebada-141">Scaricare l'esempio clonando il [repository di Docker per .NET Core](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="ebada-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="ebada-142">Eseguire l'app in locale</span><span class="sxs-lookup"><span data-stu-id="ebada-142">Run the app locally</span></span>

* <span data-ttu-id="ebada-143">Passare alla cartella del progetto in *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="ebada-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="ebada-144">Eseguire il comando seguente per compilare ed eseguire l'app in locale:</span><span class="sxs-lookup"><span data-stu-id="ebada-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="ebada-145">Passare a `http://localhost:5000` in un browser per testare l'app.</span><span class="sxs-lookup"><span data-stu-id="ebada-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="ebada-146">Premere CTRL+C al prompt dei comandi per arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="ebada-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="ebada-147">Eseguire l'app in un contenitore Linux</span><span class="sxs-lookup"><span data-stu-id="ebada-147">Run in a Linux container</span></span>

* <span data-ttu-id="ebada-148">Nel client di Docker passare ai contenitori Linux.</span><span class="sxs-lookup"><span data-stu-id="ebada-148">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="ebada-149">Passare alla cartella di Dockerfile in *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="ebada-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="ebada-150">Eseguire i comandi seguenti per compilare ed eseguire l'esempio in Docker:</span><span class="sxs-lookup"><span data-stu-id="ebada-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="ebada-151">Gli argomenti del comando `build`:</span><span class="sxs-lookup"><span data-stu-id="ebada-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="ebada-152">Denominano l'immagine aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="ebada-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="ebada-153">Cercano il documento Dockerfile nella cartella corrente (il punto alla fine).</span><span class="sxs-lookup"><span data-stu-id="ebada-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="ebada-154">Gli argomenti del comando run:</span><span class="sxs-lookup"><span data-stu-id="ebada-154">The run command arguments:</span></span>
  * <span data-ttu-id="ebada-155">Allocano uno pseudo TTY e lo tengono aperto anche se non è collegato</span><span class="sxs-lookup"><span data-stu-id="ebada-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="ebada-156">(stesso effetto di `--interactive --tty`).</span><span class="sxs-lookup"><span data-stu-id="ebada-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="ebada-157">Rimuovono automaticamente il contenitore quando viene chiuso.</span><span class="sxs-lookup"><span data-stu-id="ebada-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="ebada-158">Eseguono il mapping della porta 5000 nel computer locale alla porta 80 nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="ebada-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="ebada-159">Denominano il contenitore aspnetcore_sample.</span><span class="sxs-lookup"><span data-stu-id="ebada-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="ebada-160">Specificano l'immagine aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="ebada-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="ebada-161">Passare a `http://localhost:5000` in un browser per testare l'app.</span><span class="sxs-lookup"><span data-stu-id="ebada-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="ebada-162">Eseguire l'app in un contenitore Windows</span><span class="sxs-lookup"><span data-stu-id="ebada-162">Run in a Windows container</span></span>

* <span data-ttu-id="ebada-163">Nel client di Docker passare ai contenitori Windows.</span><span class="sxs-lookup"><span data-stu-id="ebada-163">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="ebada-164">Passare alla cartella di Dockerfile in `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="ebada-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="ebada-165">Eseguire i comandi seguenti per compilare ed eseguire l'esempio in Docker:</span><span class="sxs-lookup"><span data-stu-id="ebada-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="ebada-166">Per i contenitori Windows, è necessario l'indirizzo IP del contenitore (il passaggio a `http://localhost:5000` non funziona):</span><span class="sxs-lookup"><span data-stu-id="ebada-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="ebada-167">Aprire un altro prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="ebada-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="ebada-168">Eseguire `docker ps` per visualizzare i contenitori in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ebada-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="ebada-169">Verificare che sia presente il contenitore "aspnetcore_sample".</span><span class="sxs-lookup"><span data-stu-id="ebada-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="ebada-170">Eseguire `docker exec aspnetcore_sample ipconfig` per visualizzare l'indirizzo IP del contenitore.</span><span class="sxs-lookup"><span data-stu-id="ebada-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="ebada-171">L'output del comando è simile a questo esempio:</span><span class="sxs-lookup"><span data-stu-id="ebada-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="ebada-172">Copiare l'indirizzo IPv4 del contenitore, ad esempio 172.29.245.43, e incollarlo nella barra degli indirizzi del browser per testare l'app.</span><span class="sxs-lookup"><span data-stu-id="ebada-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="ebada-173">Compilazione e distribuzione manuali</span><span class="sxs-lookup"><span data-stu-id="ebada-173">Build and deploy manually</span></span>

<span data-ttu-id="ebada-174">In alcuni scenari è necessario distribuire un'app in un contenitore copiandovi i file dell'applicazione necessari in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ebada-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="ebada-175">Questa sezione mostra come eseguire manualmente la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="ebada-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="ebada-176">Passare alla cartella del progetto in *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="ebada-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="ebada-177">Eseguire il comando [dotnet publish](/dotnet/core/tools/dotnet-publish):</span><span class="sxs-lookup"><span data-stu-id="ebada-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="ebada-178">Gli argomenti del comando:</span><span class="sxs-lookup"><span data-stu-id="ebada-178">The command arguments:</span></span>
  * <span data-ttu-id="ebada-179">Compilano l'applicazione in modalità versione (quella predefinita è la modalità debug).</span><span class="sxs-lookup"><span data-stu-id="ebada-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="ebada-180">Creano i file nella cartella *published*.</span><span class="sxs-lookup"><span data-stu-id="ebada-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="ebada-181">Eseguire l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="ebada-181">Run the application.</span></span>

  * <span data-ttu-id="ebada-182">Windows:</span><span class="sxs-lookup"><span data-stu-id="ebada-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="ebada-183">Linux:</span><span class="sxs-lookup"><span data-stu-id="ebada-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="ebada-184">Passare a `http://localhost:5000`per visualizzare la home page.</span><span class="sxs-lookup"><span data-stu-id="ebada-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="ebada-185">Per usare l'applicazione pubblicata manualmente in un contenitore Docker, creare un nuovo documento Dockerfile e usare il comando `docker build .` per compilare il contenitore.</span><span class="sxs-lookup"><span data-stu-id="ebada-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="ebada-186">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="ebada-186">The Dockerfile</span></span>

<span data-ttu-id="ebada-187">Ecco il *Dockerfile* usato dal `docker build` comando che è stato eseguito in precedenza.</span><span class="sxs-lookup"><span data-stu-id="ebada-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="ebada-188">Usa `dotnet publish` allo stesso modo in cui è stato usato in questa sezione per la compilazione e la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="ebada-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="ebada-189">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="ebada-189">The Dockerfile</span></span>

<span data-ttu-id="ebada-190">Ecco il *Dockerfile* usato dal `docker build` comando che è stato eseguito in precedenza.</span><span class="sxs-lookup"><span data-stu-id="ebada-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="ebada-191">Usa `dotnet publish` allo stesso modo in cui è stato usato in questa sezione per la compilazione e la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="ebada-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="ebada-192">Come indicato nel Dockerfile precedente, i `*.csproj` file vengono copiati e ripristinati come *livelli*distinti.</span><span class="sxs-lookup"><span data-stu-id="ebada-192">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="ebada-193">Quando il `docker build` comando compila un'immagine, usa una cache predefinita.</span><span class="sxs-lookup"><span data-stu-id="ebada-193">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="ebada-194">Se i `*.csproj` file non sono stati modificati dopo l'ultima esecuzione del `docker build` comando, `dotnet restore` non è necessario eseguire di nuovo il comando.</span><span class="sxs-lookup"><span data-stu-id="ebada-194">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="ebada-195">Viene invece riutilizzata la cache predefinita per il `dotnet restore` livello corrispondente.</span><span class="sxs-lookup"><span data-stu-id="ebada-195">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="ebada-196">Per ulteriori informazioni, vedere [procedure consigliate per la scrittura di dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="ebada-196">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ebada-197">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ebada-197">Additional resources</span></span>

* [<span data-ttu-id="ebada-198">Comando build di Docker</span><span class="sxs-lookup"><span data-stu-id="ebada-198">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="ebada-199">Comando run di Docker</span><span class="sxs-lookup"><span data-stu-id="ebada-199">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="ebada-200">[Esempio di Docker per ASP.NET Core](https://github.com/dotnet/dotnet-docker) (usato in questa esercitazione)</span><span class="sxs-lookup"><span data-stu-id="ebada-200">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="ebada-201">Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="ebada-201">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* <span data-ttu-id="ebada-202">[Working with Visual Studio Docker Tools](./visual-studio-tools-for-docker.md) (Uso degli strumenti Docker per Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="ebada-202">[Working with Visual Studio Docker Tools](./visual-studio-tools-for-docker.md)</span></span>
* [<span data-ttu-id="ebada-203">Debug con Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ebada-203">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="ebada-204">GC con contenitori Docker e Small</span><span class="sxs-lookup"><span data-stu-id="ebada-204">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="ebada-205">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="ebada-205">Next steps</span></span>

<span data-ttu-id="ebada-206">Il repository Git che contiene l'app di esempio include anche la documentazione.</span><span class="sxs-lookup"><span data-stu-id="ebada-206">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="ebada-207">Per una panoramica delle risorse disponibili nel repository, vedere il [file LEGGIMI](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="ebada-207">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="ebada-208">In particolare, vedere le informazioni su come implementare HTTPS:</span><span class="sxs-lookup"><span data-stu-id="ebada-208">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="ebada-209">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) (Sviluppo di applicazioni ASP.NET Core con Docker su HTTPS)</span><span class="sxs-lookup"><span data-stu-id="ebada-209">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)</span></span>