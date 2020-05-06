---
title: Intestazioni di contesto nel ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione di ASP.NET Core intestazioni del contesto di protezione dati.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 381cc137d1de87e87f36c3b32a6a551a318ed3cf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776955"
---
# <a name="context-headers-in-aspnet-core"></a>Intestazioni di contesto nel ASP.NET Core

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>Background e teoria

Nel sistema di protezione dei dati, una "chiave" indica un oggetto che può fornire servizi di crittografia autenticati. Ogni chiave è identificata da un ID univoco (GUID), che contiene informazioni algoritmiche e materiali intropici. È necessario che ogni chiave includa un'entropia univoca, ma non può essere applicata dal sistema ed è inoltre necessario tenere conto degli sviluppatori che potrebbero modificare manualmente l'anello della chiave modificando le informazioni algoritmiche di una chiave esistente nell'anello chiave. Per soddisfare i requisiti di sicurezza in base a questi casi, il sistema di protezione dei dati ha un concetto di [agilità crittografica](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), che consente di usare in modo sicuro un singolo valore in più algoritmi di crittografia.

La maggior parte dei sistemi che supportano l'agilità crittografica consente di includere alcune informazioni di identificazione sull'algoritmo all'interno del payload. L'OID dell'algoritmo è in genere un buon candidato per questa operazione. Tuttavia, un problema che si è verificato è che esistono diversi modi per specificare lo stesso algoritmo: "AES" (CNG) e le classi gestite AES, AesManaged, AesCryptoServiceProvider, AesCng e RijndaelManaged (determinati parametri specifici) sono in realtà la stessa cosa ed è necessario mantenere un mapping di tutti questi nell'OID corretto. Se uno sviluppatore vuole fornire un algoritmo personalizzato (o anche un'altra implementazione di AES!), deve indicare il relativo OID. Questo passaggio di registrazione aggiuntivo rende la configurazione del sistema particolarmente penosa.

Tornando indietro, abbiamo deciso di affrontare il problema dalla direzione sbagliata. Un OID indica l'algoritmo, ma in realtà non è rilevante. Se è necessario usare un singolo valore di tipo Tropico in modo sicuro in due algoritmi diversi, non è necessario conoscere gli algoritmi effettivamente. Il modo in cui si è effettivamente interessati è il comportamento. Un algoritmo di crittografia a blocchi simmetrico decente è anche una forte permutazione pseudocasuale (PRP): la correzione degli input (chiave, modalità di concatenamento, IV, testo non crittografato) e l'output del testo crittografato con probabilità eccessiva possono essere distinti da qualsiasi altro algoritmo di crittografia a blocchi simmetrico dato gli stessi input. Analogamente, qualsiasi funzione hash con chiave decente è anche una funzione pseudocasuale forte (PRF) e, dato un set di input fisso, l'output sarà decisamente diverso da qualsiasi altra funzione hash con chiave.

Si usa questo concetto di PRPs e PRFs avanzati per creare un'intestazione di contesto. Questa intestazione del contesto funge essenzialmente da identificazione personale stabile sugli algoritmi utilizzati per qualsiasi operazione e fornisce l'agilità crittografica necessaria per il sistema di protezione dei dati. Questa intestazione è riproducibile e viene usata in un secondo momento come parte del [processo di derivazione della sottochiave](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Esistono due modi diversi per compilare l'intestazione del contesto a seconda delle modalità di funzionamento degli algoritmi sottostanti.

## <a name="cbc-mode-encryption--hmac-authentication"></a>Crittografia in modalità CBC + autenticazione HMAC

<a name="data-protection-implementation-context-headers-cbc-components"></a>

L'intestazione del contesto è costituita dai componenti seguenti:

* [16 bit] Il valore 00 00, ovvero un marcatore che significa "CBC Encryption + HMAC Authentication".

* [bit 32] Lunghezza della chiave (in byte, big endian) dell'algoritmo di crittografia a blocchi simmetrico.

* [bit 32] Dimensioni del blocco (in byte, big endian) dell'algoritmo di crittografia a blocchi simmetrico.

* [bit 32] Lunghezza della chiave (in byte, big endian) dell'algoritmo HMAC. Attualmente la dimensione della chiave corrisponde sempre alle dimensioni del digest.

* [bit 32] Dimensioni del digest (in byte, big endian) dell'algoritmo HMAC.

* EncCBC (K_E, IV, ""), che è l'output dell'algoritmo di crittografia a blocchi simmetrico dato un input di stringa vuoto e dove IV è un vettore all-zero. La costruzione di K_E è descritta di seguito.

* MAC (K_H, ""), ovvero l'output dell'algoritmo HMAC dato un input di stringa vuoto. La costruzione di K_H è descritta di seguito.

Idealmente, è possibile passare tutti gli zero vettori per K_E e K_H. Tuttavia, si desidera evitare la situazione in cui l'algoritmo sottostante controlla l'esistenza di chiavi vulnerabili prima di eseguire qualsiasi operazione (in particolare DES e 3DES), che impedisce l'utilizzo di un modello semplice o ripetibile come un vettore all-zero.

Viene invece usato il KDF NIST SP800-108 in modalità contatore (vedere [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) con una chiave di lunghezza zero, un'etichetta e un contesto e HMACSHA512 come PRF sottostante. Deriva | K_E | + | K_H | byte di output, quindi scomporre il risultato in K_E e K_H. In matematica, questo viene rappresentato come indicato di seguito.

(K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, Key = "", label = "", context = "")

### <a name="example-aes-192-cbc--hmacsha256"></a>Esempio: AES-192-CBC + HMACSHA256

Ad esempio, si consideri il caso in cui l'algoritmo di crittografia a blocchi simmetrico è AES-192-CBC e l'algoritmo di convalida è HMACSHA256. Il sistema genera l'intestazione del contesto attenendosi alla procedura seguente.

Per prima cosa, Let (K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, Key = "", label = "", context = ""), dove | K_E | = 192 bit e | K_H | = 256 bit per gli algoritmi specificati. Questo porta a K_E = 5BB6.. 21DD e K_H = A04A.. 00A9 nell'esempio seguente:

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

Successivamente, COMPUTE Enc_CBC (K_E, IV, "") per AES-192-CBC dato IV = 0 * e K_E come sopra.

risultato: = F474B1872B3B53E4721DE19C0841DB6F

Successivamente, COMPUTE MAC (K_H "") per HMACSHA256 K_H come indicato in precedenza.

risultato: = D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C

Questa operazione produce l'intestazione del contesto completo seguente:

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Questa intestazione del contesto è l'identificazione personale della coppia di algoritmi di crittografia autenticata (la convalida AES-192-CBC Encryption + HMACSHA256). I componenti, come descritto in [precedenza](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) , sono i seguenti:

* marcatore (00 00)

* lunghezza della chiave di crittografia a blocchi (00 00 00 18)

* dimensioni del blocco di crittografia a blocchi (00 00 00 10)

* lunghezza della chiave HMAC (00 00 00 20)

* dimensioni del digest HMAC (00 00 00 20)

* output della crittografia a blocchi (F4 74-DB 6F) e

* output della PRF HMAC (D4 79-end).

> [!NOTE]
> L'intestazione del contesto di autenticazione CBC-Mode Encryption + HMAC viene compilata allo stesso modo, indipendentemente dal fatto che le implementazioni degli algoritmi siano fornite da Windows CNG o dai tipi gestiti SymmetricAlgorithm e KeyedHashAlgorithm. Ciò consente alle applicazioni in esecuzione su sistemi operativi diversi di produrre in modo affidabile la stessa intestazione di contesto anche se le implementazioni degli algoritmi sono diverse tra i sistemi operativi. In pratica, il KeyedHashAlgorithm non deve essere un HMAC appropriato. Può essere qualsiasi tipo di algoritmo hash con chiave.

### <a name="example-3des-192-cbc--hmacsha1"></a>Esempio: 3DES-192-CBC + HMACSHA1

Per prima cosa, Let (K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, Key = "", label = "", context = ""), dove | K_E | = 192 bit e | K_H | = 160 bit per gli algoritmi specificati. Questo porta a K_E = A219.. E2BB e K_H = DC4A.. B464 nell'esempio seguente:

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

Successivamente, COMPUTE Enc_CBC (K_E, IV, "") per 3DES-192-CBC dato IV = 0 * e K_E come sopra.

risultato: = ABB100F81E53E10E

Successivamente, COMPUTE MAC (K_H "") per HMACSHA1 K_H come indicato in precedenza.

risultato: = 76EB189B35CF03461DDF877CD9F4B1B4D63A7555

Questa operazione produce l'intestazione del contesto completo che rappresenta un'identificazione personale della coppia di algoritmi di crittografia autenticata (3DES-192-CBC Encryption + HMACSHA1 Validation), mostrata di seguito:

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

I componenti si suddividono come segue:

* marcatore (00 00)

* lunghezza della chiave di crittografia a blocchi (00 00 00 18)

* dimensioni del blocco di crittografia a blocchi (00 00 00 08)

* lunghezza della chiave HMAC (00 00 00 14)

* dimensioni del digest HMAC (00 00 00 14)

* output della crittografia a blocchi (AB B1-E1 0E) e

* output della PRF HMAC (76 EB-end).

## <a name="galoiscounter-mode-encryption--authentication"></a>Crittografia e autenticazione in modalità contatore di Galois/Counter

L'intestazione del contesto è costituita dai componenti seguenti:

* [16 bit] Il valore 00 01, che è un marcatore che significa "GCM Encryption + Authentication".

* [bit 32] Lunghezza della chiave (in byte, big endian) dell'algoritmo di crittografia a blocchi simmetrico.

* [bit 32] Dimensioni del parametro nonce (in byte, big endian) utilizzate durante le operazioni di crittografia autenticata. Per il nostro sistema, questo problema viene risolto alle dimensioni del parametro nonce = 96 bit.

* [bit 32] Dimensioni del blocco (in byte, big endian) dell'algoritmo di crittografia a blocchi simmetrico. (Per GCM, questo è corretto a dimensione blocco = 128 bit).

* [bit 32] Dimensioni del tag di autenticazione (in byte, big endian) prodotte dalla funzione di crittografia autenticata. Per il nostro sistema, questo problema viene risolto in corrispondenza della dimensione del tag = 128 bit.

* [bit 128] Tag di Enc_GCM (K_E, nonce, ""), che è l'output dell'algoritmo di crittografia a blocchi simmetrico dato un input di stringa vuoto e dove nonce è un vettore all-zero a 96 bit.

K_E viene derivato utilizzando lo stesso meccanismo dello scenario di autenticazione CBC Encryption + HMAC. Tuttavia, poiché non c'è K_H in gioco, abbiamo essenzialmente | K_H | = 0 e l'algoritmo viene compresso nel formato seguente.

K_E = SP800_108_CTR (PRF = HMACSHA512, Key = "", label = "", context = "")

### <a name="example-aes-256-gcm"></a>Esempio: AES-256-GCM

Per prima cosa, Let K_E = SP800_108_CTR (PRF = HMACSHA512, Key = "", label = "", context = ""), dove | K_E | = 256 bit.

K_E: = 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8

Successivamente, calcolare il tag di autenticazione di Enc_GCM (K_E, nonce, "") per AES-256-GCM dati nonce = 096 e K_E come sopra.

risultato: = E7DCCE66DF855A323A6BB7BD7A59BE45

Questa operazione produce l'intestazione del contesto completo seguente:

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

I componenti si suddividono come segue:

* marcatore (00 01)

* lunghezza della chiave di crittografia a blocchi (00 00 00 20)

* dimensioni del parametro nonce (00 00 00 0C)

* dimensioni del blocco di crittografia a blocchi (00 00 00 10)

* dimensioni del tag di autenticazione (00 00 00 10) e

* il tag di autenticazione dall'esecuzione della crittografia a blocchi (E7 DC-end).
