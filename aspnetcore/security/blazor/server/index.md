---
title: Proteggere le Blazor app Server ASP.NET Core
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
ms.openlocfilehash: 5ba7bbde49bfc232795d375a1ec644825a0dee1e
ms.sourcegitcommit: 67eadd7bf28eae0b8786d85e90a7df811ffe5904
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454636"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Proteggere le Blazor app Server ASP.NET Core

Di [Luke Latham](https://github.com/guardrex)

## <a name="blazor-server-project-template"></a>BlazorModello di progetto server

Il Blazor modello di progetto server può essere configurato per l'autenticazione quando viene creato il progetto.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<xref:blazor/get-started>Per creare un nuovo Blazor progetto server con un meccanismo di autenticazione, seguire le istruzioni di Visual Studio disponibili nell'articolo.

Dopo aver scelto il modello ** Blazor app Server** nella finestra di dialogo **Crea una nuova applicazione Web di ASP.NET Core** , selezionare **Cambia** in **autenticazione**.

Viene visualizzata una finestra di dialogo che offre lo stesso set di meccanismi di autenticazione disponibili per altri progetti ASP.NET Core:

* **Nessuna autenticazione**
* **Account utente singoli**: è possibile archiviare gli account utente:
  * All'interno dell'app usando il [Identity](xref:security/authentication/identity) sistema di ASP.NET Core.
  * Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Account aziendali o dell'Istituto di istruzione**
* **Autenticazione di Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Seguire le indicazioni Visual Studio Code nell' <xref:blazor/get-started> articolo per creare un nuovo Blazor progetto server con un meccanismo di autenticazione:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.

| Meccanismo di autenticazione | Descrizione |
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

Per altre informazioni, vedere il comando [dotnet new](/dotnet/core/tools/dotnet-new) nella guida per .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Seguire le indicazioni Visual Studio per Mac nell' <xref:blazor/get-started> articolo.

1. Nel passaggio **Configura l' Blazor app del nuovo server** selezionare **autenticazione singola (in-app) nell'elenco a** discesa **autenticazione** .

1. L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET Core Identity .

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

Seguire le indicazioni interfaccia della riga di comando di .NET Core nell' <xref:blazor/get-started> articolo per creare un nuovo Blazor progetto server con un meccanismo di autenticazione:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.

| Meccanismo di autenticazione | Descrizione |
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

Per altre informazioni, vedere il comando [dotnet new](/dotnet/core/tools/dotnet-new) nella guida per .NET Core.

---

## <a name="secure-an-existing-app"></a>Proteggere un'app esistente

BlazorLe app Server sono configurate per la sicurezza in modo analogo alle app ASP.NET Core. Per ulteriori informazioni, vedere gli articoli in <xref:security/index> .

## <a name="scaffold-identity"></a>ScaffoldIdentity

Impalcatura Identity in un Blazor progetto server:

* [Senza autorizzazione esistente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [Con autorizzazione](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).
