---
title: Rimuovere la protezione dai payload le cui chiavi sono state revocate in ASP.NET Core
author: rick-anderson
description: Informazioni su come rimuovere la protezione dei dati, protetti con chiavi da revocare, in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- appsettings.json
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
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 5f13b53182f720ac8b58d90701561d381981308a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051096"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>Rimuovere la protezione dai payload le cui chiavi sono state revocate in ASP.NET Core

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

Le API di protezione dei dati ASP.NET Core non sono destinate principalmente alla persistenza illimitata dei payload riservati. Altre tecnologie come [DPAPI di Windows](/windows/win32/seccng/cng-dpapi) e [Rights Management di Azure](/rights-management/) sono più adatte allo scenario di archiviazione indefinita e hanno funzionalità di gestione delle chiavi corrispondenti. Detto questo, non c'è nulla che impedisce agli sviluppatori di usare le API di protezione dei dati ASP.NET Core per la protezione a lungo termine dei dati riservati. Le chiavi non vengono mai rimosse dall'anello chiave, quindi è `IDataProtector.Unprotect` possibile recuperare sempre i payload esistenti purché le chiavi siano disponibili e valide.

Tuttavia, si verifica un problema quando lo sviluppatore tenta di rimuovere la protezione dei dati protetti con una chiave revocata, `IDataProtector.Unprotect` in quanto genererà un'eccezione in questo caso. Questa operazione può essere corretta per i payload temporanei o temporanei, ad esempio i token di autenticazione, in quanto questi tipi di payload possono essere facilmente ricreati dal sistema e, nel peggiore dei casi, potrebbe essere necessario che il visitatore del sito acceda nuovamente. Tuttavia, per i payload salvati in modo permanente, la `Unprotect` generazione di throw potrebbe causare una perdita di dati inaccettabile.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

Per supportare lo scenario di consentire la protezione dei payload anche in presenza di chiavi revocate, il sistema di protezione dei dati contiene un `IPersistedDataProtector` tipo. Per ottenere un'istanza di `IPersistedDataProtector` , è sufficiente ottenere un'istanza di `IDataProtector` in modo normale e provare `IDataProtector` a eseguire il cast di a `IPersistedDataProtector` .

> [!NOTE]
> Non `IDataProtector` è possibile eseguire il cast di tutte le istanze a `IPersistedDataProtector` . Gli sviluppatori devono usare l'operatore C# As o un tipo simile per evitare eccezioni di runtime causate da cast non validi e devono essere preparati a gestire il caso di errore in modo appropriato.

`IPersistedDataProtector` espone la seguente superficie API:

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Questa API accetta il payload protetto (sotto forma di matrice di byte) e restituisce il payload non protetto. Non è presente alcun overload basato su stringa. I due parametri out sono i seguenti.

* `requiresMigration`: verrà impostato su true se la chiave usata per proteggere il payload non è più la chiave predefinita attiva, ad esempio la chiave usata per proteggere questo payload è obsoleta ed è stata eseguita un'operazione di sequenza della chiave. Il chiamante potrebbe voler prendere in considerazione la riprotezione del payload a seconda delle esigenze aziendali.

* `wasRevoked`: verrà impostato su true se la chiave usata per proteggere il payload è stata revocata.

>[!WARNING]
> Prestare molta attenzione quando `ignoreRevocationErrors: true` si passa al `DangerousUnprotect` metodo. Se dopo la chiamata a questo metodo il `wasRevoked` valore è true, la chiave usata per proteggere il payload è stata revocata e l'autenticità del payload deve essere considerata sospetta. In questo caso, è possibile continuare a utilizzare il payload non protetto solo se si dispone di una garanzia separata che è autentica, ad esempio se si tratta di un database protetto anziché inviato da un client Web non attendibile.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
