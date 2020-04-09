---
title: Formato di archiviazione delle chiavi in ASP.NET CoreKey storage format in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione del formato di archiviazione della chiave di ASP.NET Core Data Protection.
ms.author: riande
ms.date: 04/08/2020
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 3072c673791b589027a910b80eaba52052eb9311
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976937"
---
# <a name="key-storage-format-in-aspnet-core"></a>Formato di archiviazione delle chiavi in ASP.NET CoreKey storage format in ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Gli oggetti vengono archiviati inattivi nella rappresentazione XML. La directory predefinita per l'archiviazione delle chiavi è:The default directory for key storage is:

* Finestre: "%LOCALAPPDATA% " ASP.NET DataProtection-Keys\*
* macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*

## <a name="the-key-element"></a>L'elemento \<chiave>

Le chiavi esistono come oggetti di primo livello nel repository delle chiavi. In base alle chiavi di convenzione, il nome del file è un nome di file **key-'guid''xml**, dove 'guid' è l'ID della chiave. Ogni file contiene una singola chiave. Il formato del file è il seguente.

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

L'elemento \<key> contiene gli attributi e gli elementi figlio seguenti:

* ID della chiave. Questo valore viene considerato autorevole; il nome del file è semplicemente una sottigliezza per la leggibilità umana.

* La versione \<dell'elemento chiave>, attualmente fissata a 1.

* Date di creazione, attivazione e scadenza della chiave.

* Elemento \<> descrittore, che contiene informazioni sull'implementazione della crittografia autenticata contenuta in questa chiave.

Nell'esempio precedente, l'ID della chiave è 80732141-ec8f-4b80-af9c-c4d2d1ff8901, è stato creato e attivato il 19 marzo 2015 e ha una durata di 90 giorni. (In alcuni casi la data di attivazione potrebbe essere leggermente precedente alla data di creazione, come in questo esempio. Ciò è dovuto a un nit nel funzionamento delle API ed è innocuo nella pratica.)

## <a name="the-descriptor-element"></a>Elemento \<del> descrittore

Il \<descrittore esterno> elemento contiene un attributo deserializerType, ovvero il nome qualificato dall'assembly di un tipo che implementa IAuthenticatedEncryptorDescriptorDeserializer. Questo tipo è responsabile \<della lettura del descrittore interno> elemento e dell'analisi delle informazioni contenute all'interno.

Il formato particolare \<del descrittore> elemento dipende dall'implementazione del crittografia autenticato incapsulata dalla chiave e ogni tipo di deserializzatore prevede un formato leggermente diverso per questo. In generale, tuttavia, questo elemento conterrà informazioni algoritmiche (nomi, tipi, OID o simili) e materiale della chiave segreta. Nell'esempio precedente, il descrittore specifica che questa chiave esegue il wrapping della crittografia AES-256-CBC e della convalida HMACSHA256.

## <a name="the-encryptedsecret-element"></a>L'elemento \<> encryptedSecret

Un ** &lt;elemento&gt; encryptedSecret** che contiene la forma crittografata del materiale della chiave segreta può essere presente se [la crittografia dei segreti inattivi è abilitata.](xref:security/data-protection/implementation/key-encryption-at-rest) L'attributo `decryptorType` è il nome qualificato dall'assembly di un tipo che implementa [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Questo tipo è responsabile della lettura dell'elemento ** &lt;crittografato&gt; ** interno e della decrittografia per recuperare il testo in chiaro originale.

Come `<descriptor>`per , il `<encryptedSecret>` formato particolare dell'elemento dipende dal meccanismo di crittografia inattivi in uso. Nell'esempio precedente, la chiave master viene crittografata utilizzando Windows DPAPI per il commento.

## <a name="the-revocation-element"></a>L'elemento \<> revoca

Le revoche esistono come oggetti di primo livello nel repository delle chiavi. Per impostazione di una convenzione, le reimpostazioni hanno il nome del file **revocazione (timestamp.xml** (per richiamare tutte le chiavi prima di una data specifica) o la **revoca (per** richiamare una chiave specifica). Ogni file contiene \<un singolo elemento di revoca>.

Per le revoche di singole chiavi, il contenuto del file sarà il seguente.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

In questo caso, viene revocata solo la chiave specificata. Se l'id della chiave è """, tuttavia, come nell'esempio seguente, tutte le chiavi la cui data di creazione è precedente alla data di revoca specificata vengono revocate.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

Il \<motivo> elemento non viene mai letto dal sistema. E 'semplicemente un luogo conveniente per memorizzare un motivo leggibile dall'uomo per la revoca.
