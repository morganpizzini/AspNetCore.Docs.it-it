---
title: Crittografia chiave inattiva in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione di ASP.NET Core crittografia della chiave di protezione dati inattivi.
ms.author: riande
ms.date: 07/16/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: e68b8e09dbd876c6f0d37242ebaa415994b3b808
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776929"
---
# <a name="key-encryption-at-rest-in-aspnet-core"></a><span data-ttu-id="e1aca-103">Crittografia chiave inattiva in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1aca-103">Key encryption at rest in ASP.NET Core</span></span>

<span data-ttu-id="e1aca-104">Per impostazione predefinita, il sistema di protezione dei dati [utilizza un meccanismo di individuazione](xref:security/data-protection/configuration/default-settings) per determinare come crittografare le chiavi di crittografia inattive.</span><span class="sxs-lookup"><span data-stu-id="e1aca-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="e1aca-105">Lo sviluppatore può eseguire l'override del meccanismo di individuazione e specificare manualmente come crittografare le chiavi inattive.</span><span class="sxs-lookup"><span data-stu-id="e1aca-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="e1aca-106">Se si specifica un [percorso di persistenza della chiave](xref:security/data-protection/implementation/key-storage-providers)esplicito, il sistema di protezione dei dati Annulla la registrazione del meccanismo di crittografia delle chiavi predefinite.</span><span class="sxs-lookup"><span data-stu-id="e1aca-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="e1aca-107">Di conseguenza, le chiavi non sono più crittografate a riposo.</span><span class="sxs-lookup"><span data-stu-id="e1aca-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="e1aca-108">Si consiglia di [specificare un meccanismo di crittografia a chiave esplicito](xref:security/data-protection/implementation/key-encryption-at-rest) per le distribuzioni di produzione.</span><span class="sxs-lookup"><span data-stu-id="e1aca-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="e1aca-109">In questo argomento vengono descritte le opzioni del meccanismo Encryption-at-rest.</span><span class="sxs-lookup"><span data-stu-id="e1aca-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="e1aca-110">Insieme di credenziali chiave di Azure</span><span class="sxs-lookup"><span data-stu-id="e1aca-110">Azure Key Vault</span></span>

<span data-ttu-id="e1aca-111">Per archiviare le chiavi in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configurare il sistema con [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) nella `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="e1aca-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="e1aca-112">Per ulteriori informazioni, vedere [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span><span class="sxs-lookup"><span data-stu-id="e1aca-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="e1aca-113">DPAPI di Windows</span><span class="sxs-lookup"><span data-stu-id="e1aca-113">Windows DPAPI</span></span>

<span data-ttu-id="e1aca-114">**Si applica solo alle distribuzioni di Windows.**</span><span class="sxs-lookup"><span data-stu-id="e1aca-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="e1aca-115">Quando si usa DPAPI di Windows, il materiale della chiave viene crittografato con [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) prima di essere salvato in modo permanente nell'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="e1aca-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="e1aca-116">DPAPI è un meccanismo di crittografia appropriato per i dati che non vengono mai letti all'esterno del computer corrente (anche se è possibile eseguire il backup di queste chiavi fino a Active Directory; vedere [DPAPI e profili mobili](https://support.microsoft.com/kb/309408/#6)).</span><span class="sxs-lookup"><span data-stu-id="e1aca-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="e1aca-117">Per configurare la crittografia key-at-rest DPAPI, chiamare uno dei metodi di estensione [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :</span><span class="sxs-lookup"><span data-stu-id="e1aca-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="e1aca-118">Se `ProtectKeysWithDpapi` viene chiamato senza parametri, solo l'account utente di Windows corrente può decifrare l'anello di chiave permanente.</span><span class="sxs-lookup"><span data-stu-id="e1aca-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="e1aca-119">Facoltativamente, è possibile specificare che qualsiasi account utente nel computer (non solo l'account utente corrente) sia in grado di decifrare l'anello chiave:</span><span class="sxs-lookup"><span data-stu-id="e1aca-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="e1aca-120">Certificato X.509</span><span class="sxs-lookup"><span data-stu-id="e1aca-120">X.509 certificate</span></span>

<span data-ttu-id="e1aca-121">Se l'app viene distribuita tra più computer, potrebbe essere utile distribuire un certificato X. 509 condiviso tra le macchine e configurare le app ospitate in modo da usare il certificato per la crittografia delle chiavi inattive:</span><span class="sxs-lookup"><span data-stu-id="e1aca-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="e1aca-122">A causa delle limitazioni di .NET Framework, sono supportati solo i certificati con chiavi private CAPI.</span><span class="sxs-lookup"><span data-stu-id="e1aca-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="e1aca-123">Per le possibili soluzioni alternative a queste limitazioni, vedere il contenuto seguente.</span><span class="sxs-lookup"><span data-stu-id="e1aca-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="e1aca-124">DPAPI di Windows-NG</span><span class="sxs-lookup"><span data-stu-id="e1aca-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="e1aca-125">**Questo meccanismo è disponibile solo in Windows 8/Windows Server 2012 o versioni successive.**</span><span class="sxs-lookup"><span data-stu-id="e1aca-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="e1aca-126">A partire da Windows 8, il sistema operativo Windows supporta DPAPI-NG (detto anche DPAPI di CNG).</span><span class="sxs-lookup"><span data-stu-id="e1aca-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="e1aca-127">Per ulteriori informazioni, vedere [informazioni su CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span><span class="sxs-lookup"><span data-stu-id="e1aca-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="e1aca-128">L'entità è codificata come regola del descrittore di protezione.</span><span class="sxs-lookup"><span data-stu-id="e1aca-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="e1aca-129">Nell'esempio seguente viene chiamato [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), solo l'utente aggiunto al dominio con il SID specificato può decrittografare l'anello chiave:</span><span class="sxs-lookup"><span data-stu-id="e1aca-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="e1aca-130">Esiste anche un overload senza parametri di `ProtectKeysWithDpapiNG`.</span><span class="sxs-lookup"><span data-stu-id="e1aca-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="e1aca-131">Utilizzare questo metodo pratico per specificare la regola "SID = {CURRENT_ACCOUNT_SID}", dove *CURRENT_ACCOUNT_SID* è il SID dell'account utente di Windows corrente:</span><span class="sxs-lookup"><span data-stu-id="e1aca-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="e1aca-132">In questo scenario, il controller di dominio di Active Directory è responsabile della distribuzione delle chiavi di crittografia utilizzate dalle operazioni DPAPI-NG.</span><span class="sxs-lookup"><span data-stu-id="e1aca-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="e1aca-133">L'utente di destinazione può decifrare il payload crittografato da qualsiasi computer aggiunto a un dominio (purché il processo sia in esecuzione con la relativa identità).</span><span class="sxs-lookup"><span data-stu-id="e1aca-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="e1aca-134">Crittografia basata su certificato con Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="e1aca-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="e1aca-135">Se l'app è in esecuzione in Windows 8.1/Windows Server 2012 R2 o versione successiva, è possibile usare Windows DPAPI-NG per eseguire la crittografia basata sui certificati.</span><span class="sxs-lookup"><span data-stu-id="e1aca-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="e1aca-136">Usare la stringa di descrizione della regola "CERTIFICAte = HashId: identificazione personale", dove *Identificazione* personale è l'identificazione personale SHA1 con codifica esadecimale del certificato:</span><span class="sxs-lookup"><span data-stu-id="e1aca-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="e1aca-137">Per decifrare le chiavi, è necessario che tutte le app che puntano a questo repository siano in esecuzione in Windows 8.1/Windows Server 2012 R2 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="e1aca-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="e1aca-138">Crittografia chiave personalizzata</span><span class="sxs-lookup"><span data-stu-id="e1aca-138">Custom key encryption</span></span>

<span data-ttu-id="e1aca-139">Se i meccanismi integrati non sono appropriati, lo sviluppatore può specificare il proprio meccanismo di crittografia delle chiavi fornendo un [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)personalizzato.</span><span class="sxs-lookup"><span data-stu-id="e1aca-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
