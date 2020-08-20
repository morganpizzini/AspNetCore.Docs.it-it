---
title: Dettagli della crittografia autenticata in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione di ASP.NET Core crittografia autenticata per la protezione dei dati.
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: ed75ab235a95a88bbe60615526137b4c2bb719ef
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630848"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>Dettagli della crittografia autenticata in ASP.NET Core

<a name="data-protection-implementation-authenticated-encryption-details"></a>

Le chiamate a IDataProtector. Protect sono operazioni di crittografia autenticate. Il metodo Protect offre la riservatezza e l'autenticità ed è associato alla catena di scopi usata per derivare questa particolare istanza di IDataProtector dalla relativa IDataProtectionProvider radice.

IDataProtector. Protect accetta un parametro di testo non crittografato byte [] e produce un payload protetto byte [], il cui formato è descritto di seguito. (Esiste anche un overload del metodo di estensione che accetta un parametro di testo non crittografato della stringa e restituisce un payload protetto da stringa. Se questa API viene usata, il formato del payload protetto avrà ancora la struttura seguente, ma verrà [codificato base64url](https://tools.ietf.org/html/rfc4648#section-5).

## <a name="protected-payload-format"></a>Formato payload protetto

Il formato del payload protetto è costituito da tre componenti principali:

* Intestazione magica a 32 bit che identifica la versione del sistema di protezione dei dati.

* ID chiave a 128 bit che identifica la chiave utilizzata per proteggere questo payload specifico.

* Il resto del payload protetto è [specifico del codificatore incapsulato da questa chiave](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Nell'esempio seguente la chiave rappresenta un HMACSHA256 di crittografia AES-256-CBC + e il payload viene suddiviso ulteriormente come segue:
  * Modificatore di chiave a 128 bit.
  * Vettore di inizializzazione a 128 bit.
  * 48 byte di output AES-256-CBC.
  * Tag di autenticazione HMACSHA256.

Di seguito è illustrato un payload protetto di esempio.

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

Dal formato del payload sopra i primi 32 bit oppure 4 byte sono l'intestazione magica che identifica la versione (09 F0 C9 F0)

I successivi 128 bit o 16 byte è l'identificatore di chiave (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)

Il resto contiene il payload ed è specifico del formato utilizzato.

> [!WARNING]
> Tutti i payload protetti a una determinata chiave inizieranno con la stessa intestazione di 20 byte (valore magico, ID chiave). Gli amministratori possono utilizzare questo fatto a scopo di diagnostica per approssimarsi quando è stato generato un payload. Il payload precedente, ad esempio, corrisponde alla chiave {0c819c80-6619-4019-9536-53f8aaffee57}. Se dopo aver verificato il repository della chiave si è verificato che la data di attivazione della chiave specifica è 2015-01-01 e che la data di scadenza è 2015-03-01, è ragionevole presupporre che il payload (se non alterato) sia stato generato all'interno di tale finestra, assegnare o assumere un piccolo fattore di Fudge su entrambi i lati.
