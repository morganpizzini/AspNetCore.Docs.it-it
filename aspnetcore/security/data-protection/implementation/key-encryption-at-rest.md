---
title: Crittografia chiave inattiva in Windows e Azure con ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione di ASP.NET Core crittografia della chiave di protezione dati inattivi.
ms.author: riande
ms.date: 07/16/2018
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
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 6e767c5a34f8bf4c512147e7966f7e2c363c57c5
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018416"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a>Crittografia chiave inattiva in Windows e Azure con ASP.NET Core

Per impostazione predefinita, il sistema di protezione dei dati [utilizza un meccanismo di individuazione](xref:security/data-protection/configuration/default-settings) per determinare come crittografare le chiavi di crittografia inattive. Lo sviluppatore può eseguire l'override del meccanismo di individuazione e specificare manualmente come crittografare le chiavi inattive.

> [!WARNING]
> Se si specifica un [percorso di persistenza della chiave](xref:security/data-protection/implementation/key-storage-providers)esplicito, il sistema di protezione dei dati Annulla la registrazione del meccanismo di crittografia delle chiavi predefinite. Di conseguenza, le chiavi non sono più crittografate a riposo. Si consiglia di [specificare un meccanismo di crittografia a chiave esplicito](xref:security/data-protection/implementation/key-encryption-at-rest) per le distribuzioni di produzione. In questo argomento vengono descritte le opzioni del meccanismo Encryption-at-rest.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Insieme di credenziali chiave di Azure

Per archiviare le chiavi in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configurare il sistema con [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) nella `Startup` classe:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Per ulteriori informazioni, vedere [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>DPAPI di Windows

**Si applica solo alle distribuzioni di Windows.**

Quando si usa DPAPI di Windows, il materiale della chiave viene crittografato con [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) prima di essere salvato in modo permanente nell'archiviazione. DPAPI è un meccanismo di crittografia appropriato per i dati che non vengono mai letti all'esterno del computer corrente (anche se è possibile eseguire il backup di queste chiavi fino a Active Directory; vedere [DPAPI e profili mobili](https://support.microsoft.com/kb/309408/#6)). Per configurare la crittografia key-at-rest DPAPI, chiamare uno dei metodi di estensione [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

Se `ProtectKeysWithDpapi` viene chiamato senza parametri, solo l'account utente di Windows corrente può decifrare l'anello di chiave permanente. Facoltativamente, è possibile specificare che qualsiasi account utente nel computer (non solo l'account utente corrente) sia in grado di decifrare l'anello chiave:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>Certificato X.509

Se l'app viene distribuita tra più computer, potrebbe essere utile distribuire un certificato X. 509 condiviso tra le macchine e configurare le app ospitate in modo da usare il certificato per la crittografia delle chiavi inattive:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

A causa delle limitazioni di .NET Framework, sono supportati solo i certificati con chiavi private CAPI. Per le possibili soluzioni alternative a queste limitazioni, vedere il contenuto seguente.

::: moniker-end

## <a name="windows-dpapi-ng"></a>DPAPI di Windows-NG

**Questo meccanismo è disponibile solo in Windows 8/Windows Server 2012 o versioni successive.**

A partire da Windows 8, il sistema operativo Windows supporta DPAPI-NG (detto anche DPAPI di CNG). Per ulteriori informazioni, vedere [informazioni su CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).

L'entità è codificata come regola del descrittore di protezione. Nell'esempio seguente viene chiamato [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), solo l'utente aggiunto al dominio con il SID specificato può decrittografare l'anello chiave:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Esiste anche un overload senza parametri di `ProtectKeysWithDpapiNG` . Utilizzare questo metodo pratico per specificare la regola "SID = {CURRENT_ACCOUNT_SID}", dove *CURRENT_ACCOUNT_SID* è il SID dell'account utente di Windows corrente:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

In questo scenario, il controller di dominio di Active Directory è responsabile della distribuzione delle chiavi di crittografia utilizzate dalle operazioni DPAPI-NG. L'utente di destinazione può decifrare il payload crittografato da qualsiasi computer aggiunto a un dominio (purché il processo sia in esecuzione con la relativa identità).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Crittografia basata su certificato con Windows DPAPI-NG

Se l'app è in esecuzione in Windows 8.1/Windows Server 2012 R2 o versione successiva, è possibile usare Windows DPAPI-NG per eseguire la crittografia basata sui certificati. Usare la stringa di descrizione della regola "CERTIFICAte = HashId: identificazione personale", dove *Identificazione* personale è l'identificazione personale SHA1 con codifica esadecimale del certificato:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Per decifrare le chiavi, è necessario che tutte le app che puntano a questo repository siano in esecuzione in Windows 8.1/Windows Server 2012 R2 o versione successiva.

## <a name="custom-key-encryption"></a>Crittografia chiave personalizzata

Se i meccanismi integrati non sono appropriati, lo sviluppatore può specificare il proprio meccanismo di crittografia delle chiavi fornendo un [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)personalizzato.
