---
title: Esempi di autenticazione per ASP.NET Core
author: rick-anderson
description: Fornisce collegamenti agli esempi di autenticazione nel repository ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 71e614eb3d91e8425ec430d775b8a0163dc7258f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017792"
---
# <a name="authentication-samples-for-aspnet-core"></a>Esempi di autenticazione per ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Il [repository ASP.NET Core](https://github.com/dotnet/AspNetCore) contiene gli esempi di autenticazione seguenti nella cartella *AspNetCore/SRC/Security/Samples* :

* [Trasformazione delle attestazioni](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Cookieautenticazione](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Provider di criteri personalizzati-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Opzioni e schemi di autenticazione dinamici](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Attestazioni esterne](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [Selezione tra cookie e un altro schema di autenticazione in base alla richiesta](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Limita l'accesso ai file statici](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Eseguire gli esempi

* Selezionare un [ramo](https://github.com/dotnet/AspNetCore). Ad esempio: `release/3.1`
* Clonare o scaricare il [repository ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Verificare di aver installato la versione di [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) corrispondente al clone del repository di ASP.NET Core.
* Passare a un esempio in *AspNetCore/SRC/Security/Samples* ed eseguire l'esempio con `dotnet run` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Il [repository ASP.NET Core](https://github.com/dotnet/AspNetCore) contiene gli esempi di autenticazione seguenti nella cartella *AspNetCore/SRC/Security/Samples* :

* [Trasformazione delle attestazioni](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* [Cookieautenticazione](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)
* [Provider di criteri personalizzati-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [Opzioni e schemi di autenticazione dinamici](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* [Attestazioni esterne](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)
* [Selezione tra cookie e un altro schema di autenticazione in base alla richiesta](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [Limita l'accesso ai file statici](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Eseguire gli esempi

* Selezionare un [ramo](https://github.com/dotnet/AspNetCore). Ad esempio: `release/2.1`
* Clonare o scaricare il [repository ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Verificare di aver installato la versione di [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) corrispondente al clone del repository di ASP.NET Core.
* Passare a un esempio in *AspNetCore/SRC/Security/Samples* ed eseguire l'esempio con `dotnet run` .

::: moniker-end
