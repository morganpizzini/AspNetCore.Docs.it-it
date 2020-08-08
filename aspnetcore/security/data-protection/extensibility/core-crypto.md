---
title: Estendibilità della crittografia di base in ASP.NET Core
author: rick-anderson
description: Informazioni su IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer e sulla factory di primo livello.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 321e039a4b4692266c6d7ceaff96578db8a74bb5
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021601"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>Estendibilità della crittografia di base in ASP.NET Core

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> I tipi che implementano una delle interfacce seguenti devono essere thread-safe per più chiamanti.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

L'interfaccia **IAuthenticatedEncryptor** è il blocco predefinito di base del sottosistema crittografico. Esiste in genere un IAuthenticatedEncryptor per ogni chiave e l'istanza di IAuthenticatedEncryptor esegue il wrapping di tutte le informazioni sul materiale della chiave crittografica e sugli algoritmi necessari per eseguire operazioni di crittografia.

Come suggerisce il nome, il tipo è responsabile di fornire servizi di crittografia e decrittografia autenticati. Espone le due API seguenti.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Il metodo Encrypt restituisce un BLOB che include il testo non crittografato crittografato e un tag di autenticazione. Il tag di autenticazione deve includere i dati autenticati aggiuntivi (AAD), anche se non è necessario che AAD sia reversibile dal payload finale. Il metodo Decrypt convalida il tag di autenticazione e restituisce il payload decifrato. Tutti gli errori (ad eccezione di ArgumentNullException e simili) devono essere omogeneizzati a CryptographicException.

> [!NOTE]
> L'istanza di IAuthenticatedEncryptor non deve contenere effettivamente il materiale della chiave. Ad esempio, l'implementazione può delegare a un modulo di protezione hardware per tutte le operazioni.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Come creare un IAuthenticatedEncryptor

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

L'interfaccia **IAuthenticatedEncryptorFactory** rappresenta un tipo che sa come creare un'istanza di [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . La relativa API è la seguente.

* CreateEncryptorInstance (chiave IKey): IAuthenticatedEncryptor

Per ogni istanza di IKey specificata, tutti i crittografi autenticati creati dal relativo metodo CreateEncryptorInstance devono essere considerati equivalenti, come nell'esempio di codice riportato di seguito.

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

L'interfaccia **IAuthenticatedEncryptorDescriptor** rappresenta un tipo che sa come creare un'istanza di [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . La relativa API è la seguente.

* CreateEncryptorInstance() : IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

Analogamente a IAuthenticatedEncryptor, si presuppone che un'istanza di IAuthenticatedEncryptorDescriptor esegua il wrapping di una chiave specifica. Ciò significa che per ogni istanza di IAuthenticatedEncryptorDescriptor specificata, tutti i crittografi autenticati creati dal relativo metodo CreateEncryptorInstance devono essere considerati equivalenti, come nell'esempio di codice riportato di seguito.

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (solo ASP.NET Core 2. x)

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

L'interfaccia **IAuthenticatedEncryptorDescriptor** rappresenta un tipo che sa come esportarsi in XML. La relativa API è la seguente.

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>Serializzazione XML

La differenza principale tra IAuthenticatedEncryptor e IAuthenticatedEncryptorDescriptor è che il descrittore sa come creare il codificatore e fornire gli argomenti validi. Si consideri un IAuthenticatedEncryptor la cui implementazione si basa su SymmetricAlgorithm e KeyedHashAlgorithm. Il processo di crittografia prevede l'utilizzo di questi tipi, ma non necessariamente sa da dove provengono questi tipi, quindi non può scrivere una descrizione corretta di come ricrearsi se l'applicazione viene riavviata. Il descrittore funge da livello superiore. Poiché il descrittore sa come creare l'istanza di crittografia (ad esempio, sa come creare gli algoritmi necessari), può serializzare tali informazioni in formato XML in modo che l'istanza di crittografia possa essere ricreata dopo la reimpostazione dell'applicazione.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

Il descrittore può essere serializzato tramite la routine ExportToXml. Questa routine restituisce un XmlSerializedDescriptorInfo che contiene due proprietà: la rappresentazione XElement del descrittore e il tipo che rappresenta un [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) che può essere utilizzato per riportare questo descrittore in base all'oggetto XElement corrispondente.

Il descrittore serializzato può contenere informazioni riservate, ad esempio il materiale della chiave crittografica. Il sistema di protezione dei dati offre supporto incorporato per la crittografia delle informazioni prima che questo venga salvato in modo permanente nell'archiviazione. Per trarre vantaggio da questo, il descrittore deve contrassegnare l'elemento che contiene informazioni riservate con il nome di attributo "requiresEncryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> "), il valore "true".

>[!TIP]
> È disponibile un'API helper per l'impostazione di questo attributo. Chiamare il metodo di estensione XElement. MarkAsRequiresEncryption () che si trova nello spazio dei nomi Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.

Possono inoltre essere presenti casi in cui il descrittore serializzato non contiene informazioni riservate. Considerare di nuovo il caso di una chiave crittografica archiviata in un modulo di protezione hardware. Il descrittore non può scrivere il materiale della chiave durante la serializzazione, perché il modulo di protezione hardware non espone il materiale in formato testo normale. Al contrario, il descrittore potrebbe scrivere la versione della chiave di cui è stato eseguito il wrapper (se il modulo HSM consente l'esportazione in questo modo) o l'identificatore univoco per la chiave del modulo di protezione hardware.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

L'interfaccia **IAuthenticatedEncryptorDescriptorDeserializer** rappresenta un tipo che sa come deserializzare un'istanza di IAuthenticatedEncryptorDescriptor da un XElement. Espone un solo metodo:

* ImportFromXml (elemento XElement): IAuthenticatedEncryptorDescriptor

Il metodo ImportFromXml accetta il XElement restituito da [IAuthenticatedEncryptorDescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) e crea un equivalente del IAuthenticatedEncryptorDescriptor originale.

I tipi che implementano IAuthenticatedEncryptorDescriptorDeserializer devono avere uno dei due costruttori pubblici seguenti:

* . ctor (IServiceProvider)

* . ctor ()

> [!NOTE]
> L'oggetto IServiceProvider passato al costruttore può essere null.

## <a name="the-top-level-factory"></a>Factory di primo livello

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

La classe **AlgorithmConfiguration** rappresenta un tipo che sa come creare istanze di [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Espone una singola API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Si pensi a AlgorithmConfiguration come factory di primo livello. La configurazione funge da modello. Esegue il wrapping delle informazioni algoritmiche (ad esempio, questa configurazione produce descrittori con una chiave master AES-128-GCM), ma non è ancora associata a una chiave specifica.

Quando viene chiamato CreateNewDescriptor, il materiale della chiave aggiornata viene creato solo per questa chiamata e viene prodotto un nuovo IAuthenticatedEncryptorDescriptor che esegue il wrapping del materiale della chiave e le informazioni algoritmiche necessarie per utilizzare il materiale. Il materiale della chiave può essere creato nel software (e conservato in memoria), che può essere creato e mantenuto all'interno di un modulo di protezione hardware e così via. Il punto fondamentale è che le due chiamate a CreateNewDescriptor non devono mai creare istanze IAuthenticatedEncryptorDescriptor equivalenti.

Il tipo AlgorithmConfiguration funge da punto di ingresso per le routine di creazione delle chiavi, ad esempio il [rollup automatico della chiave](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Per modificare l'implementazione per tutte le chiavi future, impostare la proprietà AuthenticatedEncryptorConfiguration in KeyManagementOptions.

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

L'interfaccia **IAuthenticatedEncryptorConfiguration** rappresenta un tipo che sa come creare istanze di [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Espone una singola API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Si pensi a IAuthenticatedEncryptorConfiguration come factory di primo livello. La configurazione funge da modello. Esegue il wrapping delle informazioni algoritmiche (ad esempio, questa configurazione produce descrittori con una chiave master AES-128-GCM), ma non è ancora associata a una chiave specifica.

Quando viene chiamato CreateNewDescriptor, il materiale della chiave aggiornata viene creato solo per questa chiamata e viene prodotto un nuovo IAuthenticatedEncryptorDescriptor che esegue il wrapping del materiale della chiave e le informazioni algoritmiche necessarie per utilizzare il materiale. Il materiale della chiave può essere creato nel software (e conservato in memoria), che può essere creato e mantenuto all'interno di un modulo di protezione hardware e così via. Il punto fondamentale è che le due chiamate a CreateNewDescriptor non devono mai creare istanze IAuthenticatedEncryptorDescriptor equivalenti.

Il tipo IAuthenticatedEncryptorConfiguration funge da punto di ingresso per le routine di creazione delle chiavi, ad esempio il [rollup automatico della chiave](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Per modificare l'implementazione per tutte le chiavi future, registrare un IAuthenticatedEncryptorConfiguration singleton nel contenitore dei servizi.

---
