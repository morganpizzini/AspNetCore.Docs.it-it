---
title: Formato di archiviazione delle chiavi in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione del formato di archiviazione della chiave di protezione dei dati ASP.NET Core.
ms.author: riande
ms.date: 04/08/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 4a8503964c98d1828dc9d02640a7621b370e679c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060144"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="790aa-103">Formato di archiviazione delle chiavi in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="790aa-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="790aa-104">Gli oggetti vengono archiviati in formato Rest nella rappresentazione XML.</span><span class="sxs-lookup"><span data-stu-id="790aa-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="790aa-105">La directory predefinita per l'archiviazione delle chiavi è la seguente:</span><span class="sxs-lookup"><span data-stu-id="790aa-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="790aa-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="790aa-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="790aa-107">macOS/Linux: *$Home/.AspNet/DataProtection-Keys*</span><span class="sxs-lookup"><span data-stu-id="790aa-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="790aa-108">Elemento \<key></span><span class="sxs-lookup"><span data-stu-id="790aa-108">The \<key> element</span></span>

<span data-ttu-id="790aa-109">Le chiavi esistono come oggetti di primo livello nel repository di chiavi.</span><span class="sxs-lookup"><span data-stu-id="790aa-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="790aa-110">Per chiavi convenzione è presente la **chiave FileName-{GUID}. XML** , dove {GUID} è l'ID della chiave.</span><span class="sxs-lookup"><span data-stu-id="790aa-110">By convention keys have the filename **key-{guid}.xml** , where {guid} is the id of the key.</span></span> <span data-ttu-id="790aa-111">Ogni file di questo tipo contiene una singola chiave.</span><span class="sxs-lookup"><span data-stu-id="790aa-111">Each such file contains a single key.</span></span> <span data-ttu-id="790aa-112">Il formato del file è il seguente.</span><span class="sxs-lookup"><span data-stu-id="790aa-112">The format of the file is as follows.</span></span>

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

<span data-ttu-id="790aa-113">L' \<key> elemento contiene gli attributi e gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="790aa-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="790aa-114">ID della chiave. Questo valore viene considerato autorevole; il nome file è semplicemente un delicatezza per la leggibilità umana.</span><span class="sxs-lookup"><span data-stu-id="790aa-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="790aa-115">Versione dell' \<key> elemento, attualmente fissata su 1.</span><span class="sxs-lookup"><span data-stu-id="790aa-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="790aa-116">Le date di creazione, attivazione e scadenza della chiave.</span><span class="sxs-lookup"><span data-stu-id="790aa-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="790aa-117">\<descriptor>Elemento, che contiene informazioni sull'implementazione di crittografia autenticata contenuta all'interno di questa chiave.</span><span class="sxs-lookup"><span data-stu-id="790aa-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="790aa-118">Nell'esempio precedente, l'ID della chiave è {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, è stato creato e attivato il 19 marzo 2015 e ha una durata di 90 giorni.</span><span class="sxs-lookup"><span data-stu-id="790aa-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="790aa-119">(Talvolta la data di attivazione potrebbe essere leggermente precedente alla data di creazione, come in questo esempio.</span><span class="sxs-lookup"><span data-stu-id="790aa-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="790aa-120">Ciò è dovuto a un nit nel funzionamento delle API e in pratica.</span><span class="sxs-lookup"><span data-stu-id="790aa-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="790aa-121">Elemento \<descriptor></span><span class="sxs-lookup"><span data-stu-id="790aa-121">The \<descriptor> element</span></span>

<span data-ttu-id="790aa-122">L' \<descriptor> elemento esterno contiene un attributo deserializerType, che è il nome qualificato dall'assembly di un tipo che implementa IAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="790aa-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="790aa-123">Questo tipo è responsabile della lettura dell' \<descriptor> elemento interno e dell'analisi delle informazioni contenute all'interno di.</span><span class="sxs-lookup"><span data-stu-id="790aa-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="790aa-124">Il formato specifico dell' \<descriptor> elemento dipende dall'implementazione del componente di crittografia autenticata incapsulata dalla chiave e ogni tipo di deserializzatore prevede un formato leggermente diverso per questa operazione.</span><span class="sxs-lookup"><span data-stu-id="790aa-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="790aa-125">In generale, tuttavia, questo elemento conterrà informazioni algoritmiche (nomi, tipi, OID o simili) e materiale della chiave privata.</span><span class="sxs-lookup"><span data-stu-id="790aa-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="790aa-126">Nell'esempio precedente, il descrittore specifica che questa chiave esegue il wrapping della convalida AES-256-CBC Encryption + HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="790aa-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="790aa-127">Elemento \<encryptedSecret></span><span class="sxs-lookup"><span data-stu-id="790aa-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="790aa-128">È possibile che sia presente un elemento **&lt; encryptedSecret &gt;** che contiene il formato crittografato del materiale della chiave privata se la [crittografia dei segreti inattivi è abilitata](xref:security/data-protection/implementation/key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="790aa-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="790aa-129">L'attributo `decryptorType` è il nome qualificato dall'assembly di un tipo che implementa [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="790aa-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="790aa-130">Questo tipo è responsabile della lettura dell'elemento **&lt; EncryptedKey &gt;** interno e della relativa decrittografia per recuperare il testo non crittografato originale.</span><span class="sxs-lookup"><span data-stu-id="790aa-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="790aa-131">Come con `<descriptor>` , il particolare formato dell'elemento dipende dal meccanismo di crittografia dei dati inattivi `<encryptedSecret>` in uso.</span><span class="sxs-lookup"><span data-stu-id="790aa-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="790aa-132">Nell'esempio precedente, la chiave master viene crittografata utilizzando Windows DPAPI per il commento.</span><span class="sxs-lookup"><span data-stu-id="790aa-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="790aa-133">Elemento \<revocation></span><span class="sxs-lookup"><span data-stu-id="790aa-133">The \<revocation> element</span></span>

<span data-ttu-id="790aa-134">Le revoche sono disponibili come oggetti di primo livello nel repository di chiavi.</span><span class="sxs-lookup"><span data-stu-id="790aa-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="790aa-135">Le revoche di convenzione hanno la revoca del nome file **-{timestamp}. XML** (per revocare tutte le chiavi prima di una data specifica) o la revoca **-{GUID}. XML** (per revocare una chiave specifica).</span><span class="sxs-lookup"><span data-stu-id="790aa-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="790aa-136">Ogni file contiene un singolo \<revocation> elemento.</span><span class="sxs-lookup"><span data-stu-id="790aa-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="790aa-137">Per le revoche di singole chiavi, il contenuto del file sarà come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="790aa-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="790aa-138">In questo caso, viene revocata solo la chiave specificata.</span><span class="sxs-lookup"><span data-stu-id="790aa-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="790aa-139">Se l'ID chiave è "\*", tuttavia, come nell'esempio seguente, tutte le chiavi la cui data di creazione è precedente alla data di revoca specificata vengono revocate.</span><span class="sxs-lookup"><span data-stu-id="790aa-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="790aa-140">L' \<reason> elemento non viene mai letto dal sistema.</span><span class="sxs-lookup"><span data-stu-id="790aa-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="790aa-141">Si tratta semplicemente di una posizione comoda in cui archiviare un motivo leggibile per la revoca.</span><span class="sxs-lookup"><span data-stu-id="790aa-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
