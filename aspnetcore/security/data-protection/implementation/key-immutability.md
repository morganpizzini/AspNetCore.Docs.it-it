---
title: Immutabilità chiave e impostazioni chiave in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione delle API di ASP.NET Core chiave di immutabilità della chiave di protezione dati.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 51d7538a98ac2847f018ff1907bb5333bd132f32
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022394"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Immutabilità chiave e impostazioni chiave in ASP.NET Core

Quando un oggetto viene salvato in modo permanente nell'archivio di backup, la relativa rappresentazione è sempre fissa. È possibile aggiungere nuovi dati all'archivio di backup, ma i dati esistenti non possono mai essere mutati. Lo scopo principale di questo comportamento è impedire il danneggiamento dei dati.

Una conseguenza di questo comportamento è che una volta che una chiave viene scritta nell'archivio di backup, non è modificabile. La creazione, l'attivazione e le date di scadenza non possono mai essere modificate, sebbene possano essere revocate tramite `IKeyManager` . Inoltre, le informazioni algoritmiche sottostanti, il materiale della chiave master e la crittografia delle proprietà dei dati inattivi non sono modificabili.

Se lo sviluppatore modifica un'impostazione che influisce sulla persistenza della chiave, queste modifiche non saranno effettive fino alla successiva generazione di una chiave, tramite una chiamata esplicita a `IKeyManager.CreateNewKey` o tramite il comportamento di [generazione automatica della chiave](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) del sistema di protezione dati. Le impostazioni che influiscono sulla persistenza della chiave sono le seguenti:

* [Durata della chiave predefinita](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [Meccanismo di crittografia dei tasti inattivi](xref:security/data-protection/implementation/key-encryption-at-rest)

* [Informazioni algoritmiche contenute all'interno della chiave](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Se queste impostazioni sono necessarie per avviare prima del tempo di esecuzione della chiave automatica successiva, provare a effettuare una chiamata esplicita a `IKeyManager.CreateNewKey` per forzare la creazione di una nuova chiave. Ricordarsi di specificare una data di attivazione esplicita ({Now + 2 Days} è una regola empirica adatta per consentire la propagazione della modifica) e la data di scadenza nella chiamata.

>[!TIP]
> Tutte le applicazioni che toccano il repository devono specificare le stesse impostazioni con i `IDataProtectionBuilder` metodi di estensione. In caso contrario, le proprietà della chiave permanente dipendono dalla specifica applicazione che ha richiamato le routine di generazione delle chiavi.
