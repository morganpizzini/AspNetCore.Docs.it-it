---
title: Hosting di immagini ASP.NET Core con Docker su HTTPS
author: rick-anderson
description: Informazioni su come ospitare ASP.NET Core immagini con Docker su HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/docker-https
ms.openlocfilehash: 6a83695ff2a9ac7229d1d5086ed13594626476ee
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407658"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="2d00f-103">Hosting di immagini ASP.NET Core con Docker su HTTPS</span><span class="sxs-lookup"><span data-stu-id="2d00f-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="2d00f-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2d00f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2d00f-105">ASP.NET Core utilizza [https per impostazione predefinita](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="2d00f-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="2d00f-106">[Https](https://en.wikipedia.org/wiki/HTTPS) si basa su [certificati](https://en.wikipedia.org/wiki/Public_key_certificate) per l'attendibilità, l'identità e la crittografia.</span><span class="sxs-lookup"><span data-stu-id="2d00f-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="2d00f-107">Questo documento illustra come eseguire le immagini del contenitore predefinite con HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2d00f-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="2d00f-108">Per gli scenari di sviluppo, vedere [sviluppo di applicazioni ASP.NET Core con Docker su HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) .</span><span class="sxs-lookup"><span data-stu-id="2d00f-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="2d00f-109">Questo esempio richiede [docker 17,06](https://docs.docker.com/release-notes/docker-ce) o versione successiva del [client Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="2d00f-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2d00f-110">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="2d00f-110">Prerequisites</span></span>

<span data-ttu-id="2d00f-111">Per alcune istruzioni di questo documento è necessario [.NET Core 2,2 SDK](https://dotnet.microsoft.com/download) o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="2d00f-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="2d00f-112">Certificati</span><span class="sxs-lookup"><span data-stu-id="2d00f-112">Certificates</span></span>

<span data-ttu-id="2d00f-113">Un certificato di un' [autorità di certificazione](https://wikipedia.org/wiki/Certificate_authority) è necessario per l' [hosting di produzione](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) per un dominio.</span><span class="sxs-lookup"><span data-stu-id="2d00f-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="2d00f-114">[Let's Encrypt](https://letsencrypt.org/)è un'autorità di certificazione che offre certificati gratuiti.</span><span class="sxs-lookup"><span data-stu-id="2d00f-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="2d00f-115">Questo documento usa [certificati di sviluppo autofirmati](https://en.wikipedia.org/wiki/Self-signed_certificate) per l'hosting di immagini predefinite rispetto a `localhost` .</span><span class="sxs-lookup"><span data-stu-id="2d00f-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="2d00f-116">Le istruzioni sono simili all'uso dei certificati di produzione.</span><span class="sxs-lookup"><span data-stu-id="2d00f-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="2d00f-117">Per i certificati di produzione:</span><span class="sxs-lookup"><span data-stu-id="2d00f-117">For production certs:</span></span>

* <span data-ttu-id="2d00f-118">Lo `dotnet dev-certs` strumento non è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="2d00f-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="2d00f-119">Non è necessario archiviare i certificati nel percorso utilizzato nelle istruzioni.</span><span class="sxs-lookup"><span data-stu-id="2d00f-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="2d00f-120">Qualsiasi località dovrebbe funzionare, anche se non è consigliabile archiviare i certificati nella directory del sito.</span><span class="sxs-lookup"><span data-stu-id="2d00f-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="2d00f-121">Le istruzioni contenute nella sezione seguente comportano i certificati di montaggio nei contenitori con l'opzione della riga di comando di Docker `-v` .</span><span class="sxs-lookup"><span data-stu-id="2d00f-121">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="2d00f-122">È possibile aggiungere certificati alle immagini del contenitore con un `COPY` comando in un *Dockerfile*, ma non è consigliabile.</span><span class="sxs-lookup"><span data-stu-id="2d00f-122">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="2d00f-123">La copia di certificati in un'immagine non è consigliata per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="2d00f-123">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="2d00f-124">Risulta difficile usare la stessa immagine per i test con certificati per sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="2d00f-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="2d00f-125">Risulta difficile utilizzare la stessa immagine per l'hosting con certificati di produzione.</span><span class="sxs-lookup"><span data-stu-id="2d00f-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="2d00f-126">Il rischio di divulgazione dei certificati è significativo.</span><span class="sxs-lookup"><span data-stu-id="2d00f-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="2d00f-127">Esecuzione di immagini del contenitore predefinite con HTTPS</span><span class="sxs-lookup"><span data-stu-id="2d00f-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="2d00f-128">Usare le istruzioni seguenti per la configurazione del sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="2d00f-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="2d00f-129">Windows con contenitori Linux</span><span class="sxs-lookup"><span data-stu-id="2d00f-129">Windows using Linux containers</span></span>

<span data-ttu-id="2d00f-130">Generare il certificato e configurare il computer locale:</span><span class="sxs-lookup"><span data-stu-id="2d00f-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="2d00f-131">Nei comandi precedenti sostituire `{ password here }` con una password.</span><span class="sxs-lookup"><span data-stu-id="2d00f-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="2d00f-132">Eseguire l'immagine del contenitore con ASP.NET Core configurato per HTTPS in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2d00f-132">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="2d00f-133">Quando si usa [PowerShell](/powershell/scripting/overview), sostituire `%USERPROFILE%` con `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="2d00f-133">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="2d00f-134">La password deve corrispondere alla password utilizzata per il certificato.</span><span class="sxs-lookup"><span data-stu-id="2d00f-134">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="2d00f-135">macOS o Linux</span><span class="sxs-lookup"><span data-stu-id="2d00f-135">macOS or Linux</span></span>

<span data-ttu-id="2d00f-136">Generare il certificato e configurare il computer locale:</span><span class="sxs-lookup"><span data-stu-id="2d00f-136">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="2d00f-137">`dotnet dev-certs https --trust`è supportato solo in macOS e Windows.</span><span class="sxs-lookup"><span data-stu-id="2d00f-137">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="2d00f-138">È necessario considerare attendibili i certificati in Linux nel modo supportato dalla distribuzione.</span><span class="sxs-lookup"><span data-stu-id="2d00f-138">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="2d00f-139">È probabile che sia necessario considerare attendibile il certificato nel browser.</span><span class="sxs-lookup"><span data-stu-id="2d00f-139">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="2d00f-140">Nei comandi precedenti sostituire `{ password here }` con una password.</span><span class="sxs-lookup"><span data-stu-id="2d00f-140">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="2d00f-141">Eseguire l'immagine del contenitore con ASP.NET Core configurato per HTTPS:</span><span class="sxs-lookup"><span data-stu-id="2d00f-141">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="2d00f-142">La password deve corrispondere alla password utilizzata per il certificato.</span><span class="sxs-lookup"><span data-stu-id="2d00f-142">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="2d00f-143">Windows con i contenitori di Windows</span><span class="sxs-lookup"><span data-stu-id="2d00f-143">Windows using Windows containers</span></span>

<span data-ttu-id="2d00f-144">Generare il certificato e configurare il computer locale:</span><span class="sxs-lookup"><span data-stu-id="2d00f-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="2d00f-145">Nei comandi precedenti sostituire `{ password here }` con una password.</span><span class="sxs-lookup"><span data-stu-id="2d00f-145">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="2d00f-146">Quando si usa [PowerShell](/powershell/scripting/overview), sostituire `%USERPROFILE%` con `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="2d00f-146">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="2d00f-147">Eseguire l'immagine del contenitore con ASP.NET Core configurato per HTTPS:</span><span class="sxs-lookup"><span data-stu-id="2d00f-147">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="2d00f-148">La password deve corrispondere alla password utilizzata per il certificato.</span><span class="sxs-lookup"><span data-stu-id="2d00f-148">The password must match the password used for the certificate.</span></span> <span data-ttu-id="2d00f-149">Quando si usa [PowerShell](/powershell/scripting/overview), sostituire `%USERPROFILE%` con `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="2d00f-149">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
