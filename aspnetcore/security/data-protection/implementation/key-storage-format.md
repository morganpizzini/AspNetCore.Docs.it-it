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
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="26b06-103">Formato di archiviazione delle chiavi in ASP.NET CoreKey storage format in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="26b06-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="26b06-104">Gli oggetti vengono archiviati inattivi nella rappresentazione XML.</span><span class="sxs-lookup"><span data-stu-id="26b06-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="26b06-105">La directory predefinita per l'archiviazione delle chiavi è:The default directory for key storage is:</span><span class="sxs-lookup"><span data-stu-id="26b06-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="26b06-106">Finestre: "%LOCALAPPDATA% " ASP.NET DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="26b06-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="26b06-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span><span class="sxs-lookup"><span data-stu-id="26b06-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="26b06-108">L'elemento \<chiave></span><span class="sxs-lookup"><span data-stu-id="26b06-108">The \<key> element</span></span>

<span data-ttu-id="26b06-109">Le chiavi esistono come oggetti di primo livello nel repository delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="26b06-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="26b06-110">In base alle chiavi di convenzione, il nome del file è un nome di file **key-'guid''xml**, dove 'guid' è l'ID della chiave.</span><span class="sxs-lookup"><span data-stu-id="26b06-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="26b06-111">Ogni file contiene una singola chiave.</span><span class="sxs-lookup"><span data-stu-id="26b06-111">Each such file contains a single key.</span></span> <span data-ttu-id="26b06-112">Il formato del file è il seguente.</span><span class="sxs-lookup"><span data-stu-id="26b06-112">The format of the file is as follows.</span></span>

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

<span data-ttu-id="26b06-113">L'elemento \<key> contiene gli attributi e gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="26b06-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="26b06-114">ID della chiave. Questo valore viene considerato autorevole; il nome del file è semplicemente una sottigliezza per la leggibilità umana.</span><span class="sxs-lookup"><span data-stu-id="26b06-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="26b06-115">La versione \<dell'elemento chiave>, attualmente fissata a 1.</span><span class="sxs-lookup"><span data-stu-id="26b06-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="26b06-116">Date di creazione, attivazione e scadenza della chiave.</span><span class="sxs-lookup"><span data-stu-id="26b06-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="26b06-117">Elemento \<> descrittore, che contiene informazioni sull'implementazione della crittografia autenticata contenuta in questa chiave.</span><span class="sxs-lookup"><span data-stu-id="26b06-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="26b06-118">Nell'esempio precedente, l'ID della chiave è 80732141-ec8f-4b80-af9c-c4d2d1ff8901, è stato creato e attivato il 19 marzo 2015 e ha una durata di 90 giorni.</span><span class="sxs-lookup"><span data-stu-id="26b06-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="26b06-119">(In alcuni casi la data di attivazione potrebbe essere leggermente precedente alla data di creazione, come in questo esempio.</span><span class="sxs-lookup"><span data-stu-id="26b06-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="26b06-120">Ciò è dovuto a un nit nel funzionamento delle API ed è innocuo nella pratica.)</span><span class="sxs-lookup"><span data-stu-id="26b06-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="26b06-121">Elemento \<del> descrittore</span><span class="sxs-lookup"><span data-stu-id="26b06-121">The \<descriptor> element</span></span>

<span data-ttu-id="26b06-122">Il \<descrittore esterno> elemento contiene un attributo deserializerType, ovvero il nome qualificato dall'assembly di un tipo che implementa IAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="26b06-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="26b06-123">Questo tipo è responsabile \<della lettura del descrittore interno> elemento e dell'analisi delle informazioni contenute all'interno.</span><span class="sxs-lookup"><span data-stu-id="26b06-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="26b06-124">Il formato particolare \<del descrittore> elemento dipende dall'implementazione del crittografia autenticato incapsulata dalla chiave e ogni tipo di deserializzatore prevede un formato leggermente diverso per questo.</span><span class="sxs-lookup"><span data-stu-id="26b06-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="26b06-125">In generale, tuttavia, questo elemento conterrà informazioni algoritmiche (nomi, tipi, OID o simili) e materiale della chiave segreta.</span><span class="sxs-lookup"><span data-stu-id="26b06-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="26b06-126">Nell'esempio precedente, il descrittore specifica che questa chiave esegue il wrapping della crittografia AES-256-CBC e della convalida HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="26b06-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="26b06-127">L'elemento \<> encryptedSecret</span><span class="sxs-lookup"><span data-stu-id="26b06-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="26b06-128">Un \*\* &lt;elemento&gt; encryptedSecret\*\* che contiene la forma crittografata del materiale della chiave segreta può essere presente se [la crittografia dei segreti inattivi è abilitata.](xref:security/data-protection/implementation/key-encryption-at-rest)</span><span class="sxs-lookup"><span data-stu-id="26b06-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="26b06-129">L'attributo `decryptorType` è il nome qualificato dall'assembly di un tipo che implementa [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="26b06-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="26b06-130">Questo tipo è responsabile della lettura dell'elemento \*\* &lt;crittografato&gt; \*\* interno e della decrittografia per recuperare il testo in chiaro originale.</span><span class="sxs-lookup"><span data-stu-id="26b06-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="26b06-131">Come `<descriptor>`per , il `<encryptedSecret>` formato particolare dell'elemento dipende dal meccanismo di crittografia inattivi in uso.</span><span class="sxs-lookup"><span data-stu-id="26b06-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="26b06-132">Nell'esempio precedente, la chiave master viene crittografata utilizzando Windows DPAPI per il commento.</span><span class="sxs-lookup"><span data-stu-id="26b06-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="26b06-133">L'elemento \<> revoca</span><span class="sxs-lookup"><span data-stu-id="26b06-133">The \<revocation> element</span></span>

<span data-ttu-id="26b06-134">Le revoche esistono come oggetti di primo livello nel repository delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="26b06-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="26b06-135">Per impostazione di una convenzione, le reimpostazioni hanno il nome del file **revocazione (timestamp.xml** (per richiamare tutte le chiavi prima di una data specifica) o la **revoca (per** richiamare una chiave specifica).</span><span class="sxs-lookup"><span data-stu-id="26b06-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="26b06-136">Ogni file contiene \<un singolo elemento di revoca>.</span><span class="sxs-lookup"><span data-stu-id="26b06-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="26b06-137">Per le revoche di singole chiavi, il contenuto del file sarà il seguente.</span><span class="sxs-lookup"><span data-stu-id="26b06-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="26b06-138">In questo caso, viene revocata solo la chiave specificata.</span><span class="sxs-lookup"><span data-stu-id="26b06-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="26b06-139">Se l'id della chiave è """, tuttavia, come nell'esempio seguente, tutte le chiavi la cui data di creazione è precedente alla data di revoca specificata vengono revocate.</span><span class="sxs-lookup"><span data-stu-id="26b06-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="26b06-140">Il \<motivo> elemento non viene mai letto dal sistema.</span><span class="sxs-lookup"><span data-stu-id="26b06-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="26b06-141">E 'semplicemente un luogo conveniente per memorizzare un motivo leggibile dall'uomo per la revoca.</span><span class="sxs-lookup"><span data-stu-id="26b06-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
