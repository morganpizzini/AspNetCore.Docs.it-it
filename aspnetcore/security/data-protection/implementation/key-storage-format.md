---
title: Formato di archiviazione delle chiavi in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione del formato di archiviazione della chiave di protezione dei dati ASP.NET Core.
ms.author: riande
ms.date: 04/08/2020
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
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: daf86d3e3357d42ddad74d5e2f06e00e0e24db07
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631992"
---
# <a name="key-storage-format-in-aspnet-core"></a>Formato di archiviazione delle chiavi in ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Gli oggetti vengono archiviati in formato Rest nella rappresentazione XML. La directory predefinita per l'archiviazione delle chiavi è la seguente:

* Windows: *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*
* macOS/Linux: *$Home/.AspNet/DataProtection-Keys*

## <a name="the-key-element"></a>Elemento \<key>

Le chiavi esistono come oggetti di primo livello nel repository di chiavi. Per chiavi convenzione è presente la **chiave FileName-{GUID}. XML**, dove {GUID} è l'ID della chiave. Ogni file di questo tipo contiene una singola chiave. Il formato del file è il seguente.

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

L' \<key> elemento contiene gli attributi e gli elementi figlio seguenti:

* ID della chiave. Questo valore viene considerato autorevole; il nome file è semplicemente un delicatezza per la leggibilità umana.

* Versione dell' \<key> elemento, attualmente fissata su 1.

* Le date di creazione, attivazione e scadenza della chiave.

* \<descriptor>Elemento, che contiene informazioni sull'implementazione di crittografia autenticata contenuta all'interno di questa chiave.

Nell'esempio precedente, l'ID della chiave è {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, è stato creato e attivato il 19 marzo 2015 e ha una durata di 90 giorni. (Talvolta la data di attivazione potrebbe essere leggermente precedente alla data di creazione, come in questo esempio. Ciò è dovuto a un nit nel funzionamento delle API e in pratica.

## <a name="the-descriptor-element"></a>Elemento \<descriptor>

L' \<descriptor> elemento esterno contiene un attributo deserializerType, che è il nome qualificato dall'assembly di un tipo che implementa IAuthenticatedEncryptorDescriptorDeserializer. Questo tipo è responsabile della lettura dell' \<descriptor> elemento interno e dell'analisi delle informazioni contenute all'interno di.

Il formato specifico dell' \<descriptor> elemento dipende dall'implementazione del componente di crittografia autenticata incapsulata dalla chiave e ogni tipo di deserializzatore prevede un formato leggermente diverso per questa operazione. In generale, tuttavia, questo elemento conterrà informazioni algoritmiche (nomi, tipi, OID o simili) e materiale della chiave privata. Nell'esempio precedente, il descrittore specifica che questa chiave esegue il wrapping della convalida AES-256-CBC Encryption + HMACSHA256.

## <a name="the-encryptedsecret-element"></a>Elemento \<encryptedSecret>

È possibile che sia presente un elemento ** &lt; encryptedSecret &gt; ** che contiene il formato crittografato del materiale della chiave privata se la [crittografia dei segreti inattivi è abilitata](xref:security/data-protection/implementation/key-encryption-at-rest). L'attributo `decryptorType` è il nome qualificato dall'assembly di un tipo che implementa [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Questo tipo è responsabile della lettura dell'elemento ** &lt; EncryptedKey &gt; ** interno e della relativa decrittografia per recuperare il testo non crittografato originale.

Come con `<descriptor>` , il particolare formato dell'elemento dipende dal meccanismo di crittografia dei dati inattivi `<encryptedSecret>` in uso. Nell'esempio precedente, la chiave master viene crittografata utilizzando Windows DPAPI per il commento.

## <a name="the-revocation-element"></a>Elemento \<revocation>

Le revoche sono disponibili come oggetti di primo livello nel repository di chiavi. Le revoche di convenzione hanno la revoca del nome file **-{timestamp}. XML** (per revocare tutte le chiavi prima di una data specifica) o la revoca **-{GUID}. XML** (per revocare una chiave specifica). Ogni file contiene un singolo \<revocation> elemento.

Per le revoche di singole chiavi, il contenuto del file sarà come indicato di seguito.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

In questo caso, viene revocata solo la chiave specificata. Se l'ID chiave è "*", tuttavia, come nell'esempio seguente, tutte le chiavi la cui data di creazione è precedente alla data di revoca specificata vengono revocate.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

L' \<reason> elemento non viene mai letto dal sistema. Si tratta semplicemente di una posizione comoda in cui archiviare un motivo leggibile per la revoca.
