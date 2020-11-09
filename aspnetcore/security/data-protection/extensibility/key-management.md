---
title: Estendibilità della gestione delle chiavi in ASP.NET Core
author: rick-anderson
description: Informazioni sulle ASP.NET Core estensibilità di gestione delle chiavi di protezione dati.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 08ec018238d18cd253e793267c5b146df8319485
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060768"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="5acd2-103">Estendibilità della gestione delle chiavi in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5acd2-103">Key management extensibility in ASP.NET Core</span></span>

<span data-ttu-id="5acd2-104">Leggere la sezione relativa alla [gestione delle chiavi](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) prima di leggere questa sezione, in quanto illustra alcuni dei concetti fondamentali alla base di queste API.</span><span class="sxs-lookup"><span data-stu-id="5acd2-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

<span data-ttu-id="5acd2-105">**Avviso** : i tipi che implementano una delle interfacce seguenti devono essere thread-safe per più chiamanti.</span><span class="sxs-lookup"><span data-stu-id="5acd2-105">**Warning** : Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="5acd2-106">Chiave</span><span class="sxs-lookup"><span data-stu-id="5acd2-106">Key</span></span>

<span data-ttu-id="5acd2-107">L' `IKey` interfaccia è la rappresentazione di base di una chiave in cryptosystem.</span><span class="sxs-lookup"><span data-stu-id="5acd2-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="5acd2-108">Il termine chiave viene usato qui in senso astratto, non nel senso letterale del "materiale della chiave crittografica".</span><span class="sxs-lookup"><span data-stu-id="5acd2-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="5acd2-109">Una chiave presenta le proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="5acd2-109">A key has the following properties:</span></span>

* <span data-ttu-id="5acd2-110">Data di attivazione, creazione e scadenza</span><span class="sxs-lookup"><span data-stu-id="5acd2-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="5acd2-111">Stato di revoca</span><span class="sxs-lookup"><span data-stu-id="5acd2-111">Revocation status</span></span>

* <span data-ttu-id="5acd2-112">Identificatore di chiave (GUID)</span><span class="sxs-lookup"><span data-stu-id="5acd2-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="5acd2-113">Espone inoltre `IKey` un `CreateEncryptor` metodo che può essere utilizzato per creare un'istanza di [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) associata a questa chiave.</span><span class="sxs-lookup"><span data-stu-id="5acd2-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="5acd2-114">Espone inoltre `IKey` un `CreateEncryptorInstance` metodo che può essere utilizzato per creare un'istanza di [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) associata a questa chiave.</span><span class="sxs-lookup"><span data-stu-id="5acd2-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="5acd2-115">Non è disponibile alcuna API per recuperare il materiale di crittografia non elaborato da un' `IKey` istanza di.</span><span class="sxs-lookup"><span data-stu-id="5acd2-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="5acd2-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="5acd2-116">IKeyManager</span></span>

<span data-ttu-id="5acd2-117">L' `IKeyManager` interfaccia rappresenta un oggetto responsabile dell'archiviazione della chiave generale, del recupero e della manipolazione.</span><span class="sxs-lookup"><span data-stu-id="5acd2-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="5acd2-118">Espone tre operazioni di alto livello:</span><span class="sxs-lookup"><span data-stu-id="5acd2-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="5acd2-119">Creare una nuova chiave e salvarla in modo permanente nella risorsa di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="5acd2-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="5acd2-120">Ottenere tutte le chiavi dalla risorsa di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="5acd2-120">Get all keys from storage.</span></span>

* <span data-ttu-id="5acd2-121">Revocare una o più chiavi e salvare in modo permanente le informazioni di revoca nello spazio di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="5acd2-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="5acd2-122">La scrittura di un oggetto `IKeyManager` è un'attività molto avanzata e la maggior parte degli sviluppatori non dovrebbe tentarla.</span><span class="sxs-lookup"><span data-stu-id="5acd2-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="5acd2-123">Al contrario, la maggior parte degli sviluppatori dovrebbe sfruttare le funzionalità offerte dalla classe [XmlKeyManager](#xmlkeymanager) .</span><span class="sxs-lookup"><span data-stu-id="5acd2-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="5acd2-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="5acd2-124">XmlKeyManager</span></span>

<span data-ttu-id="5acd2-125">Il `XmlKeyManager` tipo è l'implementazione concreta predefinita di `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="5acd2-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="5acd2-126">Offre diverse funzionalità utili, tra cui la chiave di deposito e la crittografia delle chiavi inattive.</span><span class="sxs-lookup"><span data-stu-id="5acd2-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="5acd2-127">Le chiavi in questo sistema sono rappresentate come elementi XML (in particolare, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="5acd2-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="5acd2-128">`XmlKeyManager` dipende da diversi altri componenti in corso di completamento delle attività:</span><span class="sxs-lookup"><span data-stu-id="5acd2-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="5acd2-129">`AlgorithmConfiguration`, che determina gli algoritmi usati dalle nuove chiavi.</span><span class="sxs-lookup"><span data-stu-id="5acd2-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="5acd2-130">`IXmlRepository`, che controlla dove le chiavi vengono salvate in modo permanente nell'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="5acd2-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="5acd2-131">`IXmlEncryptor` [facoltativo], che consente di crittografare le chiavi inattive.</span><span class="sxs-lookup"><span data-stu-id="5acd2-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="5acd2-132">`IKeyEscrowSink` [facoltativo], che fornisce i servizi di deposito chiavi.</span><span class="sxs-lookup"><span data-stu-id="5acd2-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="5acd2-133">`IXmlRepository`, che controlla dove le chiavi vengono salvate in modo permanente nell'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="5acd2-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="5acd2-134">`IXmlEncryptor` [facoltativo], che consente di crittografare le chiavi inattive.</span><span class="sxs-lookup"><span data-stu-id="5acd2-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="5acd2-135">`IKeyEscrowSink` [facoltativo], che fornisce i servizi di deposito chiavi.</span><span class="sxs-lookup"><span data-stu-id="5acd2-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="5acd2-136">Di seguito sono riportati i diagrammi di alto livello che indicano il modo in cui questi componenti sono collegati tra loro `XmlKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="5acd2-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Creazione di chiavi](key-management/_static/keycreation2.png)

<span data-ttu-id="5acd2-138">*Creazione della chiave/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="5acd2-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="5acd2-139">Nell'implementazione di `CreateNewKey` , il `AlgorithmConfiguration` componente viene utilizzato per creare un oggetto univoco `IAuthenticatedEncryptorDescriptor` , che viene quindi SERIALIZZATO in formato XML.</span><span class="sxs-lookup"><span data-stu-id="5acd2-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="5acd2-140">Se è presente un sink di deposito della chiave, il codice XML non elaborato (non crittografato) viene fornito al sink per l'archiviazione a lungo termine.</span><span class="sxs-lookup"><span data-stu-id="5acd2-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="5acd2-141">Il codice XML non crittografato viene quindi eseguito tramite un `IXmlEncryptor` (se necessario) per generare il documento XML crittografato.</span><span class="sxs-lookup"><span data-stu-id="5acd2-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="5acd2-142">Questo documento crittografato viene salvato in modo permanente nell'archiviazione a lungo termine tramite `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="5acd2-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="5acd2-143">(Se non `IXmlEncryptor` è configurato alcun, il documento non crittografato viene salvato in modo permanente in `IXmlRepository` ).</span><span class="sxs-lookup"><span data-stu-id="5acd2-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Recupero della chiave](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Creazione di chiavi](key-management/_static/keycreation1.png)

<span data-ttu-id="5acd2-146">*Creazione della chiave/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="5acd2-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="5acd2-147">Nell'implementazione di `CreateNewKey` , il `IAuthenticatedEncryptorConfiguration` componente viene utilizzato per creare un oggetto univoco `IAuthenticatedEncryptorDescriptor` , che viene quindi SERIALIZZATO in formato XML.</span><span class="sxs-lookup"><span data-stu-id="5acd2-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="5acd2-148">Se è presente un sink di deposito della chiave, il codice XML non elaborato (non crittografato) viene fornito al sink per l'archiviazione a lungo termine.</span><span class="sxs-lookup"><span data-stu-id="5acd2-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="5acd2-149">Il codice XML non crittografato viene quindi eseguito tramite un `IXmlEncryptor` (se necessario) per generare il documento XML crittografato.</span><span class="sxs-lookup"><span data-stu-id="5acd2-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="5acd2-150">Questo documento crittografato viene salvato in modo permanente nell'archiviazione a lungo termine tramite `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="5acd2-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="5acd2-151">(Se non `IXmlEncryptor` è configurato alcun, il documento non crittografato viene salvato in modo permanente in `IXmlRepository` ).</span><span class="sxs-lookup"><span data-stu-id="5acd2-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Recupero della chiave](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="5acd2-153">*Recupero chiavi/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="5acd2-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="5acd2-154">Nell'implementazione di `GetAllKeys` , i documenti XML che rappresentano le chiavi e le revoche vengono letti dall'oggetto sottostante `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="5acd2-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="5acd2-155">Se questi documenti sono crittografati, verranno automaticamente decrittografati dal sistema.</span><span class="sxs-lookup"><span data-stu-id="5acd2-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="5acd2-156">`XmlKeyManager` Crea le `IAuthenticatedEncryptorDescriptorDeserializer` istanze appropriate per deserializzare i documenti di nuovo in `IAuthenticatedEncryptorDescriptor` istanze di, che vengono quindi sottoposte a incapsulamento in singole `IKey` istanze.</span><span class="sxs-lookup"><span data-stu-id="5acd2-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="5acd2-157">Questa raccolta di `IKey` istanze viene restituita al chiamante.</span><span class="sxs-lookup"><span data-stu-id="5acd2-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="5acd2-158">Altre informazioni sui particolari elementi XML sono reperibili nel [documento formato archiviazione chiavi](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="5acd2-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="5acd2-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5acd2-159">IXmlRepository</span></span>

<span data-ttu-id="5acd2-160">L' `IXmlRepository` interfaccia rappresenta un tipo che può salvare in modo permanente XML e recuperare XML da un archivio di backup.</span><span class="sxs-lookup"><span data-stu-id="5acd2-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="5acd2-161">Espone due API:</span><span class="sxs-lookup"><span data-stu-id="5acd2-161">It exposes two APIs:</span></span>

* <span data-ttu-id="5acd2-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="5acd2-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="5acd2-163">`IXmlRepository`Non è necessario che le implementazioni di analizzino il codice XML passato.</span><span class="sxs-lookup"><span data-stu-id="5acd2-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="5acd2-164">Devono considerare i documenti XML come opachi e consentire ai livelli più elevati di preoccuparsi di generare e analizzare i documenti.</span><span class="sxs-lookup"><span data-stu-id="5acd2-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="5acd2-165">Sono disponibili quattro tipi concreti incorporati che implementano `IXmlRepository` :</span><span class="sxs-lookup"><span data-stu-id="5acd2-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="5acd2-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5acd2-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="5acd2-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5acd2-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="5acd2-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5acd2-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="5acd2-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5acd2-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="5acd2-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5acd2-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="5acd2-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5acd2-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="5acd2-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5acd2-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="5acd2-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5acd2-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="5acd2-174">Per ulteriori informazioni, vedere il documento relativo ai [provider di archiviazione chiavi](xref:security/data-protection/implementation/key-storage-providers) .</span><span class="sxs-lookup"><span data-stu-id="5acd2-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="5acd2-175">La registrazione di un oggetto personalizzato `IXmlRepository` è appropriata quando si usa un archivio di backup diverso (ad esempio, archiviazione tabelle di Azure).</span><span class="sxs-lookup"><span data-stu-id="5acd2-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="5acd2-176">Per modificare il repository predefinito a livello di applicazione, registrare un' `IXmlRepository` istanza personalizzata:</span><span class="sxs-lookup"><span data-stu-id="5acd2-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="5acd2-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="5acd2-177">IXmlEncryptor</span></span>

<span data-ttu-id="5acd2-178">L' `IXmlEncryptor` interfaccia rappresenta un tipo in grado di crittografare un elemento XML non crittografato.</span><span class="sxs-lookup"><span data-stu-id="5acd2-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="5acd2-179">Espone una singola API:</span><span class="sxs-lookup"><span data-stu-id="5acd2-179">It exposes a single API:</span></span>

* <span data-ttu-id="5acd2-180">Encrypt (XElement plaintextElement): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="5acd2-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="5acd2-181">Se un oggetto serializzato `IAuthenticatedEncryptorDescriptor` contiene tutti gli elementi contrassegnati come "requires Encryption", `XmlKeyManager` eseguirà tali elementi tramite `IXmlEncryptor` il `Encrypt` Metodo configurato e renderà permanente l'elemento crittografato anziché l'elemento del testo non crittografato in `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="5acd2-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="5acd2-182">L'output del `Encrypt` metodo è un `EncryptedXmlInfo` oggetto.</span><span class="sxs-lookup"><span data-stu-id="5acd2-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="5acd2-183">Questo oggetto è un wrapper che contiene sia la crittografia risultante sia `XElement` il tipo che rappresenta un oggetto `IXmlDecryptor` che può essere utilizzato per decrittografare l'elemento corrispondente.</span><span class="sxs-lookup"><span data-stu-id="5acd2-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="5acd2-184">Sono disponibili quattro tipi concreti incorporati che implementano `IXmlEncryptor` :</span><span class="sxs-lookup"><span data-stu-id="5acd2-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="5acd2-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="5acd2-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="5acd2-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="5acd2-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="5acd2-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="5acd2-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="5acd2-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="5acd2-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="5acd2-189">Per ulteriori informazioni, vedere il [documento chiave di crittografia](xref:security/data-protection/implementation/key-encryption-at-rest) dei dati inattivi.</span><span class="sxs-lookup"><span data-stu-id="5acd2-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="5acd2-190">Per modificare il meccanismo predefinito a livello di applicazione per la crittografia delle chiavi, registrare un'istanza personalizzata `IXmlEncryptor` :</span><span class="sxs-lookup"><span data-stu-id="5acd2-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="5acd2-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="5acd2-191">IXmlDecryptor</span></span>

<span data-ttu-id="5acd2-192">L' `IXmlDecryptor` interfaccia rappresenta un tipo che sa come decrittografare un oggetto `XElement` che è stato codificato tramite un oggetto `IXmlEncryptor` .</span><span class="sxs-lookup"><span data-stu-id="5acd2-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="5acd2-193">Espone una singola API:</span><span class="sxs-lookup"><span data-stu-id="5acd2-193">It exposes a single API:</span></span>

* <span data-ttu-id="5acd2-194">Decrypt (XElement encryptedElement): XElement</span><span class="sxs-lookup"><span data-stu-id="5acd2-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="5acd2-195">Il `Decrypt` metodo annulla la crittografia eseguita da `IXmlEncryptor.Encrypt` .</span><span class="sxs-lookup"><span data-stu-id="5acd2-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="5acd2-196">In genere, ogni `IXmlEncryptor` implementazione concreta avrà un'implementazione concreta corrispondente `IXmlDecryptor` .</span><span class="sxs-lookup"><span data-stu-id="5acd2-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="5acd2-197">I tipi che implementano `IXmlDecryptor` devono avere uno dei due costruttori pubblici seguenti:</span><span class="sxs-lookup"><span data-stu-id="5acd2-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="5acd2-198">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="5acd2-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="5acd2-199">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="5acd2-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="5acd2-200">Il `IServiceProvider` passato al costruttore può essere null.</span><span class="sxs-lookup"><span data-stu-id="5acd2-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="5acd2-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="5acd2-201">IKeyEscrowSink</span></span>

<span data-ttu-id="5acd2-202">L' `IKeyEscrowSink` interfaccia rappresenta un tipo in grado di eseguire il deposito di informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="5acd2-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="5acd2-203">Si ricordi che i descrittori serializzati potrebbero contenere informazioni riservate, ad esempio il materiale crittografico, ed è ciò che ha portato all'introduzione del tipo [IXmlEncryptor](#ixmlencryptor) .</span><span class="sxs-lookup"><span data-stu-id="5acd2-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="5acd2-204">Tuttavia, gli incidenti si verificano e gli anelli chiave possono essere eliminati o danneggiati.</span><span class="sxs-lookup"><span data-stu-id="5acd2-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="5acd2-205">L'interfaccia Escrow fornisce un tratteggio di escape di emergenza, che consente l'accesso al codice XML serializzato non elaborato prima che venga trasformato da qualsiasi [IXmlEncryptor](#ixmlencryptor)configurato.</span><span class="sxs-lookup"><span data-stu-id="5acd2-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="5acd2-206">L'interfaccia espone una singola API:</span><span class="sxs-lookup"><span data-stu-id="5acd2-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="5acd2-207">Store (GUID keyId, elemento XElement)</span><span class="sxs-lookup"><span data-stu-id="5acd2-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="5acd2-208">Spetta all' `IKeyEscrowSink` implementazione gestire l'elemento fornito in modo sicuro coerente con i criteri di business.</span><span class="sxs-lookup"><span data-stu-id="5acd2-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="5acd2-209">Una possibile implementazione può essere per il sink del deposito a garanzia per crittografare l'elemento XML usando un certificato X. 509 aziendale noto in cui è stata depositata la chiave privata del certificato. il `CertificateXmlEncryptor` tipo può essere utile per questo tipo di supporto.</span><span class="sxs-lookup"><span data-stu-id="5acd2-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="5acd2-210">L' `IKeyEscrowSink` implementazione di è inoltre responsabile di salvare in modo permanente l'elemento specificato.</span><span class="sxs-lookup"><span data-stu-id="5acd2-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="5acd2-211">Per impostazione predefinita, non è abilitato alcun meccanismo di deposito, sebbene gli amministratori del server possano [configurarlo a livello globale](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="5acd2-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="5acd2-212">Può anche essere configurato a livello di codice tramite il `IDataProtectionBuilder.AddKeyEscrowSink` metodo, come illustrato nell'esempio riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="5acd2-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="5acd2-213">Gli `AddKeyEscrowSink` Overload del metodo eseguono il mirroring degli `IServiceCollection.AddSingleton` `IServiceCollection.AddInstance` Overload e, poiché le `IKeyEscrowSink` istanze di sono destinate a essere singleton.</span><span class="sxs-lookup"><span data-stu-id="5acd2-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="5acd2-214">Se `IKeyEscrowSink` vengono registrate più istanze, ciascuna di esse verrà chiamata durante la generazione della chiave, quindi le chiavi possono essere estratta simultaneamente a più meccanismi.</span><span class="sxs-lookup"><span data-stu-id="5acd2-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="5acd2-215">Non è disponibile alcuna API per leggere il materiale da un' `IKeyEscrowSink` istanza.</span><span class="sxs-lookup"><span data-stu-id="5acd2-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="5acd2-216">Questo comportamento è coerente con la teoria progettuale del meccanismo di deposito, ovvero è concepito per rendere il materiale della chiave accessibile a un'autorità attendibile e, dal momento che l'applicazione non è un'autorità attendibile, non dovrebbe avere accesso al proprio materiale con deposito.</span><span class="sxs-lookup"><span data-stu-id="5acd2-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="5acd2-217">Il codice di esempio seguente illustra la creazione e la registrazione di un oggetto in cui vengono depositate le `IKeyEscrowSink` chiavi in modo che solo i membri di "CONTOSODomain Admins" possano ripristinarli.</span><span class="sxs-lookup"><span data-stu-id="5acd2-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="5acd2-218">Per eseguire questo esempio, è necessario essere in un computer Windows 8/Windows Server 2012 aggiunto al dominio e il controller di dominio deve essere Windows Server 2012 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="5acd2-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
