---
title: Proteggere le Blazor Server app ASP.NET Core
author: guardrex
description: Informazioni su come proteggere Blazor Server le app come ASP.NET Core applicazioni.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: 69a24fc955a0f2fb524ec817eb50372052f538a1
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944256"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Proteggere le Blazor Server app ASP.NET Core

Di [Luke Latham](https://github.com/guardrex)

Blazor Serverle app sono configurate per la sicurezza in modo analogo alle app ASP.NET Core. Per ulteriori informazioni, vedere gli articoli in <xref:security/index> . Gli argomenti di questa panoramica si applicano in modo specifico a Blazor Server . 

## <a name="blazor-server-project-template"></a>Blazor Servermodello di progetto

Il Blazor Server modello di progetto può essere configurato per l'autenticazione quando viene creato il progetto.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Seguire le istruzioni di Visual Studio in <xref:blazor/tooling> per creare un nuovo Blazor Server progetto con un meccanismo di autenticazione.

Dopo aver scelto il modello ** Blazor Server app** nella finestra di dialogo **Crea un nuovo ASP.NET Core applicazione Web** , selezionare **Cambia** in **autenticazione**.

Viene visualizzata una finestra di dialogo che offre lo stesso set di meccanismi di autenticazione disponibili per altri progetti ASP.NET Core:

* **Nessuna autenticazione**
* **Account utente singoli**: è possibile archiviare gli account utente:
  * All'interno dell'app usando il [Identity](xref:security/authentication/identity) sistema di ASP.NET Core.
  * Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Account aziendali o dell'Istituto di istruzione**
* **Autenticazione di Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Seguire le indicazioni Visual Studio Code in <xref:blazor/tooling> per creare un nuovo Blazor Server progetto con un meccanismo di autenticazione:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.

| Meccanismo di autenticazione | Description |
| ------------------------ | ----------- |
| `None` (impostazione predefinita)         | Nessuna autenticazione |
| `Individual`             | Utenti archiviati nell'app con ASP.NET CoreIdentity |
| `IndividualB2C`          | Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Autenticazione organizzativa per un singolo tenant |
| `MultiOrg`               | Autenticazione organizzativa per più tenant |
| `Windows`                | Autenticazione di Windows |

Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:

* Creare una cartella per il progetto.
* Assegnare un nome al progetto.

Per ulteriori informazioni, vedere il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando nella Guida di .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Seguire le indicazioni Visual Studio per Mac in <xref:blazor/tooling> .

1. Nel passaggio **configurare la nuova Blazor Server app** selezionare **autenticazione singola (in-app) nell'elenco a** discesa **autenticazione** .

1. L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET Core Identity .

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

Creare un nuovo Blazor Server progetto con un meccanismo di autenticazione usando il comando seguente in una shell dei comandi:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.

| Meccanismo di autenticazione | Description |
| ------------------------ | ----------- |
| `None` (impostazione predefinita)         | Nessuna autenticazione |
| `Individual`             | Utenti archiviati nell'app con ASP.NET CoreIdentity |
| `IndividualB2C`          | Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Autenticazione organizzativa per un singolo tenant |
| `MultiOrg`               | Autenticazione organizzativa per più tenant |
| `Windows`                | Autenticazione di Windows |

Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:

* Creare una cartella per il progetto.
* Assegnare un nome al progetto.

Per ulteriori informazioni, vedere il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando nella Guida di .NET Core.

---

## <a name="scaffold-identity"></a>ScaffoldIdentity

Impalcatura Identity in un Blazor Server progetto:

* [Senza autorizzazione esistente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [Con autorizzazione](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).
