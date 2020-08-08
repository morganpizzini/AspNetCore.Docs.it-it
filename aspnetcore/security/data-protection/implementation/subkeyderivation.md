---
title: Derivazione di sottochiavi e crittografia autenticata in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione della derivazione della sottochiave di ASP.NET Core Data Protection e della crittografia autenticata.
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
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: ef9c100df69f9f7a1b51819ebb5721cb4f875ffd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019690"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>Derivazione di sottochiavi e crittografia autenticata in ASP.NET Core

<a name="data-protection-implementation-subkey-derivation"></a>

La maggior parte delle chiavi nell'anello chiave conterrà una forma di entropia e avrà informazioni algoritmiche che indicano la crittografia in modalità CBC e la convalida HMAC o la crittografia e la convalida GCM. In questi casi, si fa riferimento all'entropia incorporata come materiale per la chiave master (o KM) per questa chiave ed è stata eseguita una funzione di derivazione della chiave per derivare le chiavi che verranno usate per le operazioni di crittografia effettive.

> [!NOTE]
> Le chiavi sono astratte e un'implementazione personalizzata potrebbe non comportarsi come indicato di seguito. Se la chiave fornisce una propria implementazione di `IAuthenticatedEncryptor` anziché usare una delle factory predefinite, il meccanismo descritto in questa sezione non è più applicabile.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>Derivazione di sottochiavi e dati autenticati aggiuntivi

L' `IAuthenticatedEncryptor` interfaccia funge da interfaccia di base per tutte le operazioni di crittografia autenticata. Il `Encrypt` metodo accetta due buffer: testo normale e additionalAuthenticatedData (AAD). Il contenuto del testo non crittografato passa alla chiamata a `IDataProtector.Protect` , ma AAD viene generato dal sistema ed è costituito da tre componenti:

1. Intestazione magica a 32 bit 09 F0 C9 F0 che identifica questa versione del sistema di protezione dei dati.

2. ID della chiave a 128 bit.

3. Stringa a lunghezza variabile formata dalla catena di scopi che ha creato l'oggetto `IDataProtector` che sta eseguendo questa operazione.

Poiché AAD è univoco per la tupla di tutti e tre i componenti, è possibile usarlo per derivare nuove chiavi da KM anziché usare il KM stesso in tutte le operazioni crittografiche. Per ogni chiamata a `IAuthenticatedEncryptor.Encrypt` , si verifica il seguente processo di derivazione della chiave:

`( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)`

Qui viene chiamato il NIST SP800-108 KDF in modalità contatore (vedere [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) con i parametri seguenti:

* Chiave di derivazione della chiave (KDK) =`K_M`

* PRF = HMACSHA512

* Label = additionalAuthenticatedData

* context = contextHeader | | Modificatore di tasto

L'intestazione del contesto è di lunghezza variabile ed essenzialmente funge da identificazione digitale degli algoritmi per i quali si derivano `K_E` e `K_H` . Il modificatore di chiave è una stringa a 128 bit generata in modo casuale per ogni chiamata a `Encrypt` e serve a garantire con probabilità travolgente che ke e KH sono univoci per questa operazione di crittografia di autenticazione specifica, anche se tutti gli altri input per KDF sono costanti.

Per la crittografia in modalità CBC e le operazioni di convalida HMAC, `| K_E |` è la lunghezza della chiave di crittografia a blocchi simmetrica e `| K_H |` è la dimensione del digest della routine HMAC. Per la crittografia GCM e le operazioni di convalida, `| K_H | = 0` .

## <a name="cbc-mode-encryption--hmac-validation"></a>Crittografia in modalità CBC + convalida HMAC

Una volta `K_E` generato tramite il meccanismo precedente, viene generato un vettore di inizializzazione casuale ed eseguito l'algoritmo di crittografia a blocchi simmetrico per la crittografia del testo non crittografato. Il vettore di inizializzazione e il testo crittografato vengono quindi eseguiti attraverso la routine HMAC inizializzata con la chiave `K_H` per produrre il Mac. Questo processo e il valore restituito sono rappresentati graficamente di seguito.

![Processo in modalità CBC e restituzione](subkeyderivation/_static/cbcprocess.png)

`output:= keyModifier || iv || E_cbc (K_E,iv,data) || HMAC(K_H, iv || E_cbc (K_E,iv,data))`

> [!NOTE]
> L' `IDataProtector.Protect` implementazione [antepone l'intestazione magica e l'ID chiave](xref:security/data-protection/implementation/authenticated-encryption-details) a output prima di restituirlo al chiamante. Poiché l'intestazione magica e l'ID chiave sono implicitamente parte di [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad)e perché il modificatore di chiave viene alimentato come input per KDF, ciò significa che ogni singolo byte del payload finale restituito viene autenticato dal Mac.

## <a name="galoiscounter-mode-encryption--validation"></a>Crittografia e convalida della modalità di Galois/Counter

Una volta che `K_E` viene generato tramite il meccanismo precedente, viene generato un nonce casuale a 96 bit ed eseguito l'algoritmo di crittografia a blocchi simmetrico per crittografare il testo normale e produrre il tag di autenticazione a 128 bit.

![Processo in modalità GCM e restituzione](subkeyderivation/_static/galoisprocess.png)

`output := keyModifier || nonce || E_gcm (K_E,nonce,data) || authTag`

> [!NOTE]
> Anche se GCM supporta in modo nativo il concetto di AAD, stiamo ancora inviando AAD solo alla KDF originale, scegliendo di passare una stringa vuota a GCM per il parametro AAD. Il motivo è due volte. Per prima cosa, [per supportare l'agilità](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) non si vuole mai usare `K_M` direttamente come chiave di crittografia. Inoltre, GCM impone requisiti di univocità molto rigidi per gli input. La probabilità che la routine di crittografia GCM venga mai richiamata su due o più set distinti di dati di input con la stessa coppia (chiave, nonce) non deve superare 2 ^ 32. Se si corregge, `K_E` non è possibile eseguire più di 2 ^ 32 operazioni di crittografia prima di eseguire afoul del limite di 2 ^-32. Questo può sembrare un numero molto elevato di operazioni, ma un server Web con traffico elevato può superare le richieste 4 miliardi in pochi giorni, entro la durata normale di queste chiavi. Per mantenere la conformità al limite di probabilità 2 ^-32, continuiamo a usare un modificatore di chiave a 128 bit e un parametro nonce a 96 bit, che estende radicalmente il numero di operazioni utilizzabili per qualsiasi dato `K_M` . Per semplicità di progettazione, viene condiviso il percorso del codice KDF tra le operazioni CBC e GCM e, poiché AAD è già considerato nel KDF, non è necessario inviarlo alla routine GCM.
