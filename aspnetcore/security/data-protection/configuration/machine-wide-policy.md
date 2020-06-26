---
title: Supporto dei criteri a livello di computer per la protezione dati in ASP.NET Core
author: rick-anderson
description: Informazioni sul supporto per l'impostazione di criteri a livello di computer predefiniti per tutte le app che utilizzano ASP.NET Core la protezione dei dati.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 1bde6d90a52ab8873777b5320540251170eaade6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404886"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>Supporto dei criteri a livello di computer per la protezione dati in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

Quando è in esecuzione in Windows, il sistema di protezione dei dati ha un supporto limitato per l'impostazione di criteri a livello di computer predefiniti per tutte le app che utilizzano ASP.NET Core la protezione dei dati. L'idea generale è che un amministratore potrebbe voler modificare un'impostazione predefinita, ad esempio gli algoritmi usati o la durata delle chiavi, senza dover aggiornare manualmente ogni app nel computer.

> [!WARNING]
> L'amministratore di sistema può impostare i criteri predefiniti, ma non è possibile applicarli. Lo sviluppatore dell'app può sempre eseguire l'override di qualsiasi valore con una scelta. I criteri predefiniti influiscono solo sulle app in cui lo sviluppatore non ha specificato un valore esplicito per un'impostazione.

## <a name="setting-default-policy"></a>Impostazione dei criteri predefiniti

Per impostare i criteri predefiniti, un amministratore può impostare i valori noti nel registro di sistema nella seguente chiave del registro di sistema:

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

Se ci si trova in un sistema operativo a 64 bit e si vuole influenzare il comportamento delle app a 32 bit, ricordarsi di configurare l'equivalente Wow6432Node della chiave precedente.

Di seguito sono riportati i valori supportati.

| valore              | Tipo   | Descrizione |
| ------------------ | :----: | ----------- |
| EncryptionType     | string | Specifica gli algoritmi da utilizzare per la protezione dei dati. Il valore deve essere CNG-CBC, CNG-GCM o gestito ed è descritto in dettaglio di seguito. |
| DefaultKeyLifetime | DWORD  | Specifica la durata delle chiavi appena generate. Il valore viene specificato in giorni e deve essere >= 7. |
| KeyEscrowSinks     | string | Specifica i tipi utilizzati per il deposito della chiave. Il valore è un elenco delimitato da punti e virgola di sink di deposito chiave, dove ogni elemento dell'elenco è il nome qualificato dall'assembly di un tipo che implementa [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink). |

## <a name="encryption-types"></a>Tipi di crittografia

Se EncryptionType è CNG-CBC, il sistema è configurato per l'utilizzo di una crittografia a blocchi simmetrica in modalità CBC per la riservatezza e HMAC per l'autenticità con i servizi forniti da Windows CNG. per ulteriori informazioni, vedere [specifica di algoritmi CNG personalizzati di Windows](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) . Sono supportati i valori aggiuntivi seguenti, ognuno dei quali corrisponde a una proprietà nel tipo CngCbcAuthenticatedEncryptionSettings.

| valore                       | Tipo   | Descrizione |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | Nome di un algoritmo di crittografia a blocchi simmetrico riconosciuto da CNG. Questo algoritmo viene aperto in modalità CBC. |
| EncryptionAlgorithmProvider | string | Nome dell'implementazione del provider CNG che può produrre l'algoritmo EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | Lunghezza in bit della chiave da derivare per l'algoritmo di crittografia a blocchi simmetrico. |
| HashAlgorithm               | string | Nome di un algoritmo hash riconosciuto da CNG. Questo algoritmo viene aperto in modalità HMAC. |
| HashAlgorithmProvider       | string | Nome dell'implementazione del provider CNG che può produrre l'algoritmo HashAlgorithm. |

Se EncryptionType è CNG-GCM, il sistema è configurato per l'uso di una crittografia a blocchi simmetrico/in modalità contatore simmetrica per la riservatezza e l'autenticità con i servizi forniti da CNG di Windows. per ulteriori informazioni, vedere [specifica di algoritmi CNG personalizzati di Windows](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) . Sono supportati i valori aggiuntivi seguenti, ognuno dei quali corrisponde a una proprietà nel tipo CngGcmAuthenticatedEncryptionSettings.

| valore                       | Tipo   | Descrizione |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | Nome di un algoritmo di crittografia a blocchi simmetrico riconosciuto da CNG. Questo algoritmo viene aperto in modalità Galois/Counter. |
| EncryptionAlgorithmProvider | string | Nome dell'implementazione del provider CNG che può produrre l'algoritmo EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | Lunghezza in bit della chiave da derivare per l'algoritmo di crittografia a blocchi simmetrico. |

Se EncryptionType è gestito, il sistema è configurato per l'uso di un oggetto SymmetricAlgorithm gestito per la riservatezza e KeyedHashAlgorithm per l'autenticità (vedere [specifica di algoritmi gestiti personalizzati](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) per altri dettagli). Sono supportati i valori aggiuntivi seguenti, ognuno dei quali corrisponde a una proprietà nel tipo ManagedAuthenticatedEncryptionSettings.

| valore                      | Tipo   | Descrizione |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | string | Nome qualificato dall'assembly di un tipo che implementa SymmetricAlgorithm. |
| EncryptionAlgorithmKeySize | DWORD  | Lunghezza in bit della chiave da derivare per l'algoritmo di crittografia simmetrica. |
| ValidationAlgorithmType    | string | Nome qualificato dall'assembly di un tipo che implementa KeyedHashAlgorithm. |

Se EncryptionType ha un valore diverso da null o vuoto, il sistema di protezione dei dati genera un'eccezione all'avvio.

> [!WARNING]
> Quando si configura un'impostazione di criteri predefinita che include i nomi dei tipi (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), i tipi devono essere disponibili per l'app. Ciò significa che per le app in esecuzione su CLR desktop, gli assembly che contengono questi tipi devono essere presenti nella global assembly cache (GAC). Per ASP.NET Core app in esecuzione in .NET Core, è necessario installare i pacchetti che contengono questi tipi.
