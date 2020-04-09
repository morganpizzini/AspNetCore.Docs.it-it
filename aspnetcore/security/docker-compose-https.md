---
title: Hosting ASP.NET Core image in container using docker compose with HTTPS
author: ravipal
description: Scopri come ospitare ASP.NET immagini di base con Docker Compose su HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Let's Encrypt
uid: security/docker-compose-https
ms.openlocfilehash: 616ccf906e98534ffda08c0c2b6d0a171f063cc1
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381824"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Hosting di immagini ASP.NET Core con Docker Compose su HTTPS


ASP.NET Core utilizza [HTTPS per impostazione predefinita](/aspnet/core/security/enforcing-ssl). [HTTPS](https://en.wikipedia.org/wiki/HTTPS) si basa su [certificati](https://en.wikipedia.org/wiki/Public_key_certificate) per l'attendibilità, l'identità e la crittografia.

Questo documento spiega come eseguire immagini contenitore predefinite con HTTPS.

Vedere [Sviluppo di applicazioni di ASP.NET base con Docker su HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) per gli scenari di sviluppo.

Questo esempio richiede [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) o versione successiva del [client Docker](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Prerequisiti

Per alcune delle istruzioni contenute in questo documento è necessario [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) o versione successiva.

## <a name="certificates"></a>Certificati

Un certificato di [un'autorità di certificazione](https://wikipedia.org/wiki/Certificate_authority) è necessario per [l'hosting di produzione](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) per un dominio. [Let's Encrypt](https://letsencrypt.org/)è un'autorità di certificazione che offre certificati gratuiti.

Questo documento utilizza certificati di [sviluppo autofirmati](https://wikipedia.org/wiki/Self-signed_certificate) per l'hosting di immagini predefinite tramite `localhost`. Le istruzioni sono simili all'utilizzo dei certificati di produzione.

Per i certificati di produzione:

* Lo `dotnet dev-certs` strumento non è necessario.
* Non è necessario archiviare i certificati nel percorso utilizzato nelle istruzioni. Archiviare i certificati in qualsiasi posizione esterna alla directory del sito.

Le istruzioni contenute nella sezione seguente montano i certificati in contenitori utilizzando la `volumes` proprietà in *docker-compose.yml.* È possibile aggiungere certificati alle `COPY` immagini del contenitore con un comando in un *Dockerfile*, ma non è consigliabile. La copia dei certificati in un'immagine non è consigliata per i motivi seguenti:Copying certificates into an image isn't recommended for the following reasons:

* Rende difficile utilizzare la stessa immagine per il test con i certificati dello sviluppatore.
* Rende difficile utilizzare la stessa immagine per l'hosting con i certificati di produzione.
* C'è un rischio significativo di divulgazione del certificato.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Avvio di un contenitore con supporto https tramite docker composeStarting a container with https support using docker compose

Utilizzare le istruzioni seguenti per la configurazione del sistema operativo.

### <a name="windows-using-linux-containers"></a>Windows con contenitori Linux

Generare il certificato e configurare il computer locale:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Nei comandi precedenti sostituire `{ password here }` con una password.

Creare un file _docker-compose.debug.yml_ con il seguente contenuto:

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
La password specificata nel file docker compose deve corrispondere alla password utilizzata per il certificato.

Avviare il contenitore con ASP.NET Core configurato per HTTPS:Start the container with ASP.NET Core configured for HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS o Linux

Generare il certificato e configurare il computer locale:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`è supportato solo su macOS e Windows. È necessario considerare attendibili i certificati su Linux nel modo supportato dalla distribuzione. È probabile che sia necessario considerare attendibile il certificato nel browser.

Nei comandi precedenti sostituire `{ password here }` con una password.

Creare un file _docker-compose.debug.yml_ con il seguente contenuto:

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
La password specificata nel file docker compose deve corrispondere alla password utilizzata per il certificato.

Avviare il contenitore con ASP.NET Core configurato per HTTPS:Start the container with ASP.NET Core configured for HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Finestre con contenitori Windows

Generare il certificato e configurare il computer locale:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Nei comandi precedenti sostituire `{ password here }` con una password.

Creare un file _docker-compose.debug.yml_ con il seguente contenuto:

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
La password specificata nel file docker compose deve corrispondere alla password utilizzata per il certificato.

Avviare il contenitore con ASP.NET Core configurato per HTTPS:Start the container with ASP.NET Core configured for HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
