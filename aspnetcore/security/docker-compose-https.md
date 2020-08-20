---
title: Hosting di ASP.NET Core immagine nel contenitore con Docker compose con HTTPS
author: ravipal
description: Informazioni su come ospitare ASP.NET Core immagini con Docker Compose su HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
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
uid: security/docker-compose-https
ms.openlocfilehash: 75a205c1eb21394ed36c00359f0dc4ca7e6d09e0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631641"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="cb125-103">Hosting di immagini ASP.NET Core con Docker Compose su HTTPS</span><span class="sxs-lookup"><span data-stu-id="cb125-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="cb125-104">ASP.NET Core utilizza [https per impostazione predefinita](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="cb125-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="cb125-105">[Https](https://en.wikipedia.org/wiki/HTTPS) si basa su [certificati](https://en.wikipedia.org/wiki/Public_key_certificate) per l'attendibilità, l'identità e la crittografia.</span><span class="sxs-lookup"><span data-stu-id="cb125-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="cb125-106">Questo documento illustra come eseguire le immagini del contenitore predefinite con HTTPS.</span><span class="sxs-lookup"><span data-stu-id="cb125-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="cb125-107">Per gli scenari di sviluppo, vedere [sviluppo di applicazioni ASP.NET Core con Docker su HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) .</span><span class="sxs-lookup"><span data-stu-id="cb125-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="cb125-108">Questo esempio richiede [docker 17,06](https://docs.docker.com/release-notes/docker-ce) o versione successiva del [client Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="cb125-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cb125-109">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="cb125-109">Prerequisites</span></span>

<span data-ttu-id="cb125-110">Per alcune istruzioni di questo documento è necessario [.NET Core 2,2 SDK](https://dotnet.microsoft.com/download) o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="cb125-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="cb125-111">Certificati</span><span class="sxs-lookup"><span data-stu-id="cb125-111">Certificates</span></span>

<span data-ttu-id="cb125-112">Un certificato di un' [autorità di certificazione](https://wikipedia.org/wiki/Certificate_authority) è necessario per l' [hosting di produzione](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) per un dominio.</span><span class="sxs-lookup"><span data-stu-id="cb125-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="cb125-113">[Let's Encrypt](https://letsencrypt.org/) è un'autorità di certificazione che offre certificati gratuiti.</span><span class="sxs-lookup"><span data-stu-id="cb125-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="cb125-114">Questo documento usa [certificati di sviluppo autofirmati](https://wikipedia.org/wiki/Self-signed_certificate) per l'hosting di immagini predefinite rispetto a `localhost` .</span><span class="sxs-lookup"><span data-stu-id="cb125-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="cb125-115">Le istruzioni sono simili all'uso dei certificati di produzione.</span><span class="sxs-lookup"><span data-stu-id="cb125-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="cb125-116">Per i certificati di produzione:</span><span class="sxs-lookup"><span data-stu-id="cb125-116">For production certificates:</span></span>

* <span data-ttu-id="cb125-117">Lo `dotnet dev-certs` strumento non è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="cb125-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="cb125-118">I certificati non devono essere archiviati nel percorso utilizzato nelle istruzioni.</span><span class="sxs-lookup"><span data-stu-id="cb125-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="cb125-119">Archiviare i certificati in qualsiasi posizione all'esterno della directory del sito.</span><span class="sxs-lookup"><span data-stu-id="cb125-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="cb125-120">Le istruzioni contenute nella sezione seguente comportano i certificati di montaggio nei contenitori usando la `volumes` Proprietà in *Docker-compose. yml.*</span><span class="sxs-lookup"><span data-stu-id="cb125-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="cb125-121">È possibile aggiungere certificati alle immagini del contenitore con un `COPY` comando in un *Dockerfile*, ma non è consigliabile.</span><span class="sxs-lookup"><span data-stu-id="cb125-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="cb125-122">La copia di certificati in un'immagine non è consigliata per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="cb125-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="cb125-123">Risulta difficile usare la stessa immagine per i test con certificati per sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="cb125-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="cb125-124">Rende difficile usare la stessa immagine per l'hosting con certificati di produzione.</span><span class="sxs-lookup"><span data-stu-id="cb125-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="cb125-125">Il rischio di divulgazione dei certificati è significativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="cb125-126">Avvio di un contenitore con supporto HTTPS con Docker compose</span><span class="sxs-lookup"><span data-stu-id="cb125-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="cb125-127">Usare le istruzioni seguenti per la configurazione del sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="cb125-128">Windows con contenitori Linux</span><span class="sxs-lookup"><span data-stu-id="cb125-128">Windows using Linux containers</span></span>

<span data-ttu-id="cb125-129">Generare il certificato e configurare il computer locale:</span><span class="sxs-lookup"><span data-stu-id="cb125-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="cb125-130">Nei comandi precedenti sostituire `{ password here }` con una password.</span><span class="sxs-lookup"><span data-stu-id="cb125-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="cb125-131">Creare un file _Docker-compose. debug. yml_ con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="cb125-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="cb125-132">La password specificata nel file Docker compose deve corrispondere alla password usata per il certificato.</span><span class="sxs-lookup"><span data-stu-id="cb125-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="cb125-133">Avviare il contenitore con ASP.NET Core configurato per HTTPS:</span><span class="sxs-lookup"><span data-stu-id="cb125-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="cb125-134">macOS o Linux</span><span class="sxs-lookup"><span data-stu-id="cb125-134">macOS or Linux</span></span>

<span data-ttu-id="cb125-135">Generare il certificato e configurare il computer locale:</span><span class="sxs-lookup"><span data-stu-id="cb125-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="cb125-136">`dotnet dev-certs https --trust` è supportato solo in macOS e Windows.</span><span class="sxs-lookup"><span data-stu-id="cb125-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="cb125-137">È necessario considerare attendibili i certificati in Linux nel modo supportato dalla distribuzione.</span><span class="sxs-lookup"><span data-stu-id="cb125-137">You need to trust certificates on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="cb125-138">È probabile che sia necessario considerare attendibile il certificato nel browser.</span><span class="sxs-lookup"><span data-stu-id="cb125-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="cb125-139">Nei comandi precedenti sostituire `{ password here }` con una password.</span><span class="sxs-lookup"><span data-stu-id="cb125-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="cb125-140">Creare un file _Docker-compose. debug. yml_ con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="cb125-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="cb125-141">La password specificata nel file Docker compose deve corrispondere alla password usata per il certificato.</span><span class="sxs-lookup"><span data-stu-id="cb125-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="cb125-142">Avviare il contenitore con ASP.NET Core configurato per HTTPS:</span><span class="sxs-lookup"><span data-stu-id="cb125-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="cb125-143">Windows con i contenitori di Windows</span><span class="sxs-lookup"><span data-stu-id="cb125-143">Windows using Windows containers</span></span>

<span data-ttu-id="cb125-144">Generare il certificato e configurare il computer locale:</span><span class="sxs-lookup"><span data-stu-id="cb125-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="cb125-145">Nei comandi precedenti sostituire `{ password here }` con una password.</span><span class="sxs-lookup"><span data-stu-id="cb125-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="cb125-146">Creare un file _Docker-compose. debug. yml_ con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="cb125-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="cb125-147">La password specificata nel file Docker compose deve corrispondere alla password usata per il certificato.</span><span class="sxs-lookup"><span data-stu-id="cb125-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="cb125-148">Avviare il contenitore con ASP.NET Core configurato per HTTPS:</span><span class="sxs-lookup"><span data-stu-id="cb125-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
