---
title: Estendibilità della gestione delle chiavi in ASP.NET Core
author: rick-anderson
description: Informazioni sulle ASP.NET Core estensibilità di gestione delle chiavi di protezione dati.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: db718b8d4c305b75ad52054efde6b2d03f6825ed
ms.sourcegitcommit: 4cce99cbd44372fd4575e8da8c0f4345949f4d9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89153532"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>Estendibilità della gestione delle chiavi in ASP.NET Core

Leggere la sezione relativa alla [gestione delle chiavi](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) prima di leggere questa sezione, in quanto illustra alcuni dei concetti fondamentali alla base di queste API.

**Avviso**: i tipi che implementano una delle interfacce seguenti devono essere thread-safe per più chiamanti.

## <a name="key"></a>Chiave

L' `IKey` interfaccia è la rappresentazione di base di una chiave in cryptosystem. Il termine chiave viene usato qui in senso astratto, non nel senso letterale del "materiale della chiave crittografica". Una chiave presenta le proprietà seguenti:

* Data di attivazione, creazione e scadenza

* Stato di revoca

* Identificatore di chiave (GUID)

::: moniker range=">= aspnetcore-2.0"

Espone inoltre `IKey` un `CreateEncryptor` metodo che può essere utilizzato per creare un'istanza di [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) associata a questa chiave.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Espone inoltre `IKey` un `CreateEncryptorInstance` metodo che può essere utilizzato per creare un'istanza di [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) associata a questa chiave.

::: moniker-end

> [!NOTE]
> Non è disponibile alcuna API per recuperare il materiale di crittografia non elaborato da un' `IKey` istanza di.

## <a name="ikeymanager"></a>IKeyManager

L' `IKeyManager` interfaccia rappresenta un oggetto responsabile dell'archiviazione della chiave generale, del recupero e della manipolazione. Espone tre operazioni di alto livello:

* Creare una nuova chiave e salvarla in modo permanente nella risorsa di archiviazione.

* Ottenere tutte le chiavi dalla risorsa di archiviazione.

* Revocare una o più chiavi e salvare in modo permanente le informazioni di revoca nello spazio di archiviazione.

>[!WARNING]
> La scrittura di un oggetto `IKeyManager` è un'attività molto avanzata e la maggior parte degli sviluppatori non dovrebbe tentarla. Al contrario, la maggior parte degli sviluppatori dovrebbe sfruttare le funzionalità offerte dalla classe [XmlKeyManager](#xmlkeymanager) .

## <a name="xmlkeymanager"></a>XmlKeyManager

Il `XmlKeyManager` tipo è l'implementazione concreta predefinita di `IKeyManager` . Offre diverse funzionalità utili, tra cui la chiave di deposito e la crittografia delle chiavi inattive. Le chiavi in questo sistema sono rappresentate come elementi XML (in particolare, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` dipende da diversi altri componenti in corso di completamento delle attività:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, che determina gli algoritmi usati dalle nuove chiavi.

* `IXmlRepository`, che controlla dove le chiavi vengono salvate in modo permanente nell'archiviazione.

* `IXmlEncryptor` [facoltativo], che consente di crittografare le chiavi inattive.

* `IKeyEscrowSink` [facoltativo], che fornisce i servizi di deposito chiavi.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, che controlla dove le chiavi vengono salvate in modo permanente nell'archiviazione.

* `IXmlEncryptor` [facoltativo], che consente di crittografare le chiavi inattive.

* `IKeyEscrowSink` [facoltativo], che fornisce i servizi di deposito chiavi.

::: moniker-end

Di seguito sono riportati i diagrammi di alto livello che indicano il modo in cui questi componenti sono collegati tra loro `XmlKeyManager` .

::: moniker range=">= aspnetcore-2.0"

![Creazione di chiavi](key-management/_static/keycreation2.png)

*Creazione della chiave/CreateNewKey*

Nell'implementazione di `CreateNewKey` , il `AlgorithmConfiguration` componente viene utilizzato per creare un oggetto univoco `IAuthenticatedEncryptorDescriptor` , che viene quindi SERIALIZZATO in formato XML. Se è presente un sink di deposito della chiave, il codice XML non elaborato (non crittografato) viene fornito al sink per l'archiviazione a lungo termine. Il codice XML non crittografato viene quindi eseguito tramite un `IXmlEncryptor` (se necessario) per generare il documento XML crittografato. Questo documento crittografato viene salvato in modo permanente nell'archiviazione a lungo termine tramite `IXmlRepository` . (Se non `IXmlEncryptor` è configurato alcun, il documento non crittografato viene salvato in modo permanente in `IXmlRepository` ).

![Recupero della chiave](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Creazione di chiavi](key-management/_static/keycreation1.png)

*Creazione della chiave/CreateNewKey*

Nell'implementazione di `CreateNewKey` , il `IAuthenticatedEncryptorConfiguration` componente viene utilizzato per creare un oggetto univoco `IAuthenticatedEncryptorDescriptor` , che viene quindi SERIALIZZATO in formato XML. Se è presente un sink di deposito della chiave, il codice XML non elaborato (non crittografato) viene fornito al sink per l'archiviazione a lungo termine. Il codice XML non crittografato viene quindi eseguito tramite un `IXmlEncryptor` (se necessario) per generare il documento XML crittografato. Questo documento crittografato viene salvato in modo permanente nell'archiviazione a lungo termine tramite `IXmlRepository` . (Se non `IXmlEncryptor` è configurato alcun, il documento non crittografato viene salvato in modo permanente in `IXmlRepository` ).

![Recupero della chiave](key-management/_static/keyretrieval1.png)

::: moniker-end

*Recupero chiavi/GetAllKeys*

Nell'implementazione di `GetAllKeys` , i documenti XML che rappresentano le chiavi e le revoche vengono letti dall'oggetto sottostante `IXmlRepository` . Se questi documenti sono crittografati, verranno automaticamente decrittografati dal sistema. `XmlKeyManager` Crea le `IAuthenticatedEncryptorDescriptorDeserializer` istanze appropriate per deserializzare i documenti di nuovo in `IAuthenticatedEncryptorDescriptor` istanze di, che vengono quindi sottoposte a incapsulamento in singole `IKey` istanze. Questa raccolta di `IKey` istanze viene restituita al chiamante.

Altre informazioni sui particolari elementi XML sono reperibili nel [documento formato archiviazione chiavi](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

L' `IXmlRepository` interfaccia rappresenta un tipo che può salvare in modo permanente XML e recuperare XML da un archivio di backup. Espone due API:

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

`IXmlRepository`Non è necessario che le implementazioni di analizzino il codice XML passato. Devono considerare i documenti XML come opachi e consentire ai livelli più elevati di preoccuparsi di generare e analizzare i documenti.

Sono disponibili quattro tipi concreti incorporati che implementano `IXmlRepository` :

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Per ulteriori informazioni, vedere il documento relativo ai [provider di archiviazione chiavi](xref:security/data-protection/implementation/key-storage-providers) .

La registrazione di un oggetto personalizzato `IXmlRepository` è appropriata quando si usa un archivio di backup diverso (ad esempio, archiviazione tabelle di Azure).

Per modificare il repository predefinito a livello di applicazione, registrare un' `IXmlRepository` istanza personalizzata:

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

## <a name="ixmlencryptor"></a>IXmlEncryptor

L' `IXmlEncryptor` interfaccia rappresenta un tipo in grado di crittografare un elemento XML non crittografato. Espone una singola API:

* Encrypt (XElement plaintextElement): EncryptedXmlInfo

Se un oggetto serializzato `IAuthenticatedEncryptorDescriptor` contiene tutti gli elementi contrassegnati come "requires Encryption", `XmlKeyManager` eseguirà tali elementi tramite `IXmlEncryptor` il `Encrypt` Metodo configurato e renderà permanente l'elemento crittografato anziché l'elemento del testo non crittografato in `IXmlRepository` . L'output del `Encrypt` metodo è un `EncryptedXmlInfo` oggetto. Questo oggetto è un wrapper che contiene sia la crittografia risultante sia `XElement` il tipo che rappresenta un oggetto `IXmlDecryptor` che può essere utilizzato per decrittografare l'elemento corrispondente.

Sono disponibili quattro tipi concreti incorporati che implementano `IXmlEncryptor` :

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Per ulteriori informazioni, vedere il [documento chiave di crittografia](xref:security/data-protection/implementation/key-encryption-at-rest) dei dati inattivi.

Per modificare il meccanismo predefinito a livello di applicazione per la crittografia delle chiavi, registrare un'istanza personalizzata `IXmlEncryptor` :

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

## <a name="ixmldecryptor"></a>IXmlDecryptor

L' `IXmlDecryptor` interfaccia rappresenta un tipo che sa come decrittografare un oggetto `XElement` che è stato codificato tramite un oggetto `IXmlEncryptor` . Espone una singola API:

* Decrypt (XElement encryptedElement): XElement

Il `Decrypt` metodo annulla la crittografia eseguita da `IXmlEncryptor.Encrypt` . In genere, ogni `IXmlEncryptor` implementazione concreta avrà un'implementazione concreta corrispondente `IXmlDecryptor` .

I tipi che implementano `IXmlDecryptor` devono avere uno dei due costruttori pubblici seguenti:

* . ctor (IServiceProvider)
* . ctor ()

> [!NOTE]
> Il `IServiceProvider` passato al costruttore può essere null.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

L' `IKeyEscrowSink` interfaccia rappresenta un tipo in grado di eseguire il deposito di informazioni riservate. Si ricordi che i descrittori serializzati potrebbero contenere informazioni riservate, ad esempio il materiale crittografico, ed è ciò che ha portato all'introduzione del tipo [IXmlEncryptor](#ixmlencryptor) . Tuttavia, gli incidenti si verificano e gli anelli chiave possono essere eliminati o danneggiati.

L'interfaccia Escrow fornisce un tratteggio di escape di emergenza, che consente l'accesso al codice XML serializzato non elaborato prima che venga trasformato da qualsiasi [IXmlEncryptor](#ixmlencryptor)configurato. L'interfaccia espone una singola API:

* Store (GUID keyId, elemento XElement)

Spetta all' `IKeyEscrowSink` implementazione gestire l'elemento fornito in modo sicuro coerente con i criteri di business. Una possibile implementazione può essere per il sink del deposito a garanzia per crittografare l'elemento XML usando un certificato X. 509 aziendale noto in cui è stata depositata la chiave privata del certificato. il `CertificateXmlEncryptor` tipo può essere utile per questo tipo di supporto. L' `IKeyEscrowSink` implementazione di è inoltre responsabile di salvare in modo permanente l'elemento specificato.

Per impostazione predefinita, non è abilitato alcun meccanismo di deposito, sebbene gli amministratori del server possano [configurarlo a livello globale](xref:security/data-protection/configuration/machine-wide-policy). Può anche essere configurato a livello di codice tramite il `IDataProtectionBuilder.AddKeyEscrowSink` metodo, come illustrato nell'esempio riportato di seguito. Gli `AddKeyEscrowSink` Overload del metodo eseguono il mirroring degli `IServiceCollection.AddSingleton` `IServiceCollection.AddInstance` Overload e, poiché le `IKeyEscrowSink` istanze di sono destinate a essere singleton. Se `IKeyEscrowSink` vengono registrate più istanze, ciascuna di esse verrà chiamata durante la generazione della chiave, quindi le chiavi possono essere estratta simultaneamente a più meccanismi.

Non è disponibile alcuna API per leggere il materiale da un' `IKeyEscrowSink` istanza. Questo comportamento è coerente con la teoria progettuale del meccanismo di deposito, ovvero è concepito per rendere il materiale della chiave accessibile a un'autorità attendibile e, dal momento che l'applicazione non è un'autorità attendibile, non dovrebbe avere accesso al proprio materiale con deposito.

Il codice di esempio seguente illustra la creazione e la registrazione di un oggetto in cui vengono depositate le `IKeyEscrowSink` chiavi in modo che solo i membri di "CONTOSODomain Admins" possano ripristinarli.

> [!NOTE]
> Per eseguire questo esempio, è necessario essere in un computer Windows 8/Windows Server 2012 aggiunto al dominio e il controller di dominio deve essere Windows Server 2012 o versione successiva.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
