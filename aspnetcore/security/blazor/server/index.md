---
title: Proteggere le Blazor app server ASP.NET Core
author: guardrex
description: Informazioni su come proteggere Blazor le app Server come ASP.NET Core applicazioni.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: bbd8b6fcd357b8929bf097450854d98fbea2570e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772635"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Proteggere le app del server ASP.NET Core Blazer

Di [Luke Latham](https://github.com/guardrex)

## <a name="blazor-server-project-template"></a>Modello di progetto server Blazer

Il modello di progetto server blazer può essere configurato per l'autenticazione quando viene creato il progetto.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Per creare un nuovo progetto server Blazer con un meccanismo di autenticazione, seguire le istruzioni di Visual Studio disponibili nell' <xref:blazor/get-started> articolo.

Dopo aver scelto il modello **App server Blazor** nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core**, selezionare **Modifica** in **Autenticazione **.

Viene visualizzata una finestra di dialogo che offre lo stesso set di meccanismi di autenticazione disponibili per altri progetti ASP.NET Core:

* **Nessuna autenticazione**
* **Account utente individuali** &ndash; Gli account utente possono essere archiviati:
  * All'interno dell'app usando il sistema di [identità](xref:security/authentication/identity) di ASP.NET Core.
  * Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Account aziendali o dell'Istituto di istruzione**
* **Autenticazione di Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Seguire le indicazioni Visual Studio Code nell' <xref:blazor/get-started> articolo per creare un nuovo progetto server Blazer con un meccanismo di autenticazione:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.

| Meccanismo di autenticazione | Description |
| ------------------------ | ----------- |
| `None` (impostazione predefinita)         | Nessuna autenticazione |
| `Individual`             | Utenti archiviati nell'app con ASP.NET Core identità |
| `IndividualB2C`          | Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Autenticazione organizzativa per un singolo tenant |
| `MultiOrg`               | Autenticazione organizzativa per più tenant |
| `Windows`                | Autenticazione di Windows |

Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:

* Creare una cartella per il progetto.
* Assegnare un nome al progetto.

Per altre informazioni, vedere il comando [dotnet new](/dotnet/core/tools/dotnet-new) nella guida per .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Seguire le indicazioni Visual Studio per Mac nell' <xref:blazor/get-started> articolo.

1. Nel passaggio **configurare la nuova app del server Blazer** selezionare **autenticazione singola (in-app) nell'elenco a** discesa **autenticazione** .

1. L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET Core identità.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

Seguire le indicazioni interfaccia della riga di comando di .NET Core nell' <xref:blazor/get-started> articolo per creare un nuovo progetto server Blazer con un meccanismo di autenticazione:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.

| Meccanismo di autenticazione | Description |
| ------------------------ | ----------- |
| `None` (impostazione predefinita)         | Nessuna autenticazione |
| `Individual`             | Utenti archiviati nell'app con ASP.NET Core identità |
| `IndividualB2C`          | Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Autenticazione organizzativa per un singolo tenant |
| `MultiOrg`               | Autenticazione organizzativa per più tenant |
| `Windows`                | Autenticazione di Windows |

Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:

* Creare una cartella per il progetto.
* Assegnare un nome al progetto.

Per altre informazioni, vedere il comando [dotnet new](/dotnet/core/tools/dotnet-new) nella guida per .NET Core.

---

## <a name="secure-an-existing-app"></a>Proteggere un'app esistente

BlazorLe app Server sono configurate per la sicurezza in modo analogo alle app ASP.NET Core. Per ulteriori informazioni, vedere gli articoli in <xref:security/index>.
