---
title: Articoli basati su progetti ASP.NET Core creati con account utente singoli
author: rick-anderson
description: Scopri gli articoli in base ai progetti ASP.NET Core creati con singoli account utente.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 26f53b6452e307bbd0816c1a3604f38b04c6af15
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768650"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Articoli basati su progetti ASP.NET Core creati con account utente singoli

ASP.NET Core identità è inclusa nei modelli di progetto di Visual Studio con l'opzione "singoli account utente".

I modelli di autenticazione sono disponibili in interfaccia della riga di comando di .NET Core `-au Individual`con:

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

Vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore/issues/5833) per l'autenticazione dell'API Web.

<a name="no"></a>

## <a name="no-authentication"></a>Nessuna autenticazione

L'autenticazione viene specificata nella interfaccia della riga di comando di .NET Core con l' `-au` opzione. In Visual Studio la finestra di dialogo **Modifica autenticazione** è disponibile per le nuove applicazioni Web. Il valore predefinito per le nuove app Web in Visual Studio **non è Authentication**.

Progetti creati senza autenticazione:

* Non contenere pagine Web e interfaccia utente per l'accesso e la disconnessione.
* Non contiene codice di autenticazione.

<a name="win"></a>

## <a name="windows-authentication"></a>Autenticazione di Windows

L'autenticazione di Windows viene specificata per le nuove app Web nella interfaccia della riga di comando di .NET Core `-au Windows` con l'opzione. In Visual Studio la finestra di dialogo **Cambia autenticazione** fornisce le opzioni di **autenticazione di Windows** .

Se è selezionata l'autenticazione di Windows, l'app viene configurata per l'uso del [modulo IIS](xref:host-and-deploy/iis/modules)per l'autenticazione di Windows. L'autenticazione di Windows è destinata ai siti Web Intranet.

## <a name="dotnet-new-webapp-authentication-options"></a>opzioni di autenticazione New webapp DotNet

La tabella seguente illustra le opzioni di autenticazione disponibili per le nuove app Web:

| Opzione | Tipo di autenticazione | Collegamento per altre informazioni |
 | ----------------- | ------------ | ---------- |
| nessuno            |  Nessuna autenticazione | | 
| Utenti      |  Autenticazione singola | <xref:security/authentication/identity>
| IndividualB2C   |  Autenticazione Single ospitata nel cloud con Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| SingleOrg       |  Autenticazione organizzativa per un singolo tenant | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| MultiOrg        |  Autenticazione organizzativa per più tenant | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Autenticazione di Windows | [Autenticazione di Windows](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Nuove opzioni di autenticazione WebApp per Visual Studio

La tabella seguente illustra le opzioni di autenticazione disponibili quando si crea una nuova app Web con Visual Studio:

| Opzione | Tipo di autenticazione | Collegamento per altre informazioni |
 | ----------------- | ------------ | ---------- |
| nessuno            |  Nessuna autenticazione | | 
| Account utente singoli/archivia account utente in-app |  Autenticazione singola | <xref:security/authentication/identity> |
| Singoli account utente/connettersi a un archivio utente esistente nel cloud |  Autenticazione Single ospitata nel cloud con Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| Cloud aziendale o dell'Istituto di istruzione/organizzazione singola  |  Autenticazione organizzativa per un singolo tenant | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Cloud aziendale o dell'Istituto di istruzione/organizzazione multipla |  Autenticazione organizzativa per più tenant | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Autenticazione di Windows | [Autenticazione di Windows](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>Risorse aggiuntive

Gli articoli seguenti illustrano come usare il codice generato in ASP.NET Core modelli che usano singoli account utente:

* [Account confirmation and password recovery in ASP.NET Core](xref:security/authentication/accconfirm) (Conferma dell'account e recupero della password in ASP.NET Core)
* [Creare un'app ASP.NET Core con i dati utente protetti dall'autorizzazione](xref:security/authorization/secure-data)
