---
title: Provider di archiviazione chiavi in ASP.NET Core
author: rick-anderson
description: Informazioni sui provider di archiviazione chiavi in ASP.NET Core e su come configurare i percorsi di archiviazione delle chiavi.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: a8d38f17b066a0aa9a38b1bdfea3491f733cf1bc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776877"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="df464-103">Provider di archiviazione chiavi in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df464-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="df464-104">Per impostazione predefinita, il sistema di protezione dei dati [utilizza un meccanismo di individuazione](xref:security/data-protection/configuration/default-settings) per determinare dove devono essere rese permanente le chiavi crittografiche.</span><span class="sxs-lookup"><span data-stu-id="df464-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="df464-105">Lo sviluppatore può eseguire l'override del meccanismo di individuazione predefinito e specificare manualmente il percorso.</span><span class="sxs-lookup"><span data-stu-id="df464-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="df464-106">Se si specifica un percorso di persistenza della chiave esplicito, il sistema di protezione dei dati Annulla la registrazione del meccanismo di crittografia delle chiavi predefinite, in modo che le chiavi non vengano più crittografate a riposo.</span><span class="sxs-lookup"><span data-stu-id="df464-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="df464-107">Si consiglia inoltre di [specificare un meccanismo di crittografia a chiave esplicito](xref:security/data-protection/implementation/key-encryption-at-rest) per le distribuzioni di produzione.</span><span class="sxs-lookup"><span data-stu-id="df464-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="df464-108">File system</span><span class="sxs-lookup"><span data-stu-id="df464-108">File system</span></span>

<span data-ttu-id="df464-109">Per configurare un repository di chiavi basato su file system, chiamare la routine di configurazione [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) , come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="df464-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="df464-110">Fornire un oggetto [DirectoryInfo](/dotnet/api/system.io.directoryinfo) che punta al repository in cui devono essere archiviate le chiavi:</span><span class="sxs-lookup"><span data-stu-id="df464-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="df464-111">Il `DirectoryInfo` può puntare a una directory nel computer locale oppure può puntare a una cartella in una condivisione di rete.</span><span class="sxs-lookup"><span data-stu-id="df464-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="df464-112">Se si fa riferimento a una directory nel computer locale (e lo scenario è che solo le app nel computer locale richiedono l'accesso per usare questo repository), provare a usare [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (in Windows) per crittografare le chiavi inattive.</span><span class="sxs-lookup"><span data-stu-id="df464-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="df464-113">In caso contrario, prendere in considerazione l'uso di un [certificato X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) per crittografare le chiavi inattive.</span><span class="sxs-lookup"><span data-stu-id="df464-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="df464-114">Archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="df464-114">Azure Storage</span></span>

<span data-ttu-id="df464-115">Il pacchetto [Microsoft. AspNetCore. dataprotection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) consente di archiviare le chiavi di protezione dei dati nell'archivio BLOB di Azure.</span><span class="sxs-lookup"><span data-stu-id="df464-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="df464-116">Le chiavi possono essere condivise tra più istanze di un'app Web.</span><span class="sxs-lookup"><span data-stu-id="df464-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="df464-117">Le app possono condividere i cookie di autenticazione o la protezione CSRF su più server.</span><span class="sxs-lookup"><span data-stu-id="df464-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="df464-118">Per configurare il provider di archiviazione BLOB di Azure, chiamare uno degli overload [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .</span><span class="sxs-lookup"><span data-stu-id="df464-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="df464-119">Se l'app Web è in esecuzione come servizio di Azure, i token di autenticazione possono essere creati automaticamente usando [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span><span class="sxs-lookup"><span data-stu-id="df464-119">If the web app is running as an Azure service, authentication tokens can be automatically created using [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span></span>

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

<span data-ttu-id="df464-120">Per [ulteriori informazioni sulla configurazione dell'autenticazione da servizio a servizio](/azure/key-vault/service-to-service-authentication) , vedere.</span><span class="sxs-lookup"><span data-stu-id="df464-120">See [more details about configuring service-to-service authentication.](/azure/key-vault/service-to-service-authentication)</span></span>

## <a name="redis"></a><span data-ttu-id="df464-121">Redis</span><span class="sxs-lookup"><span data-stu-id="df464-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="df464-122">Il pacchetto [Microsoft. AspNetCore. dataprotection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) consente di archiviare le chiavi di protezione dei dati in una cache Redis.</span><span class="sxs-lookup"><span data-stu-id="df464-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="df464-123">Le chiavi possono essere condivise tra più istanze di un'app Web.</span><span class="sxs-lookup"><span data-stu-id="df464-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="df464-124">Le app possono condividere i cookie di autenticazione o la protezione CSRF su più server.</span><span class="sxs-lookup"><span data-stu-id="df464-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="df464-125">Il pacchetto [Microsoft. AspNetCore. dataprotection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) consente di archiviare le chiavi di protezione dei dati in una cache Redis.</span><span class="sxs-lookup"><span data-stu-id="df464-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="df464-126">Le chiavi possono essere condivise tra più istanze di un'app Web.</span><span class="sxs-lookup"><span data-stu-id="df464-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="df464-127">Le app possono condividere i cookie di autenticazione o la protezione CSRF su più server.</span><span class="sxs-lookup"><span data-stu-id="df464-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="df464-128">Per eseguire la configurazione in Redis, chiamare uno degli overload di [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :</span><span class="sxs-lookup"><span data-stu-id="df464-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="df464-129">Per eseguire la configurazione in Redis, chiamare uno degli overload di [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :</span><span class="sxs-lookup"><span data-stu-id="df464-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="df464-130">Per altre informazioni, vedere i seguenti argomenti:</span><span class="sxs-lookup"><span data-stu-id="df464-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="df464-131">StackExchange. Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="df464-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="df464-132">Cache Redis di Azure</span><span class="sxs-lookup"><span data-stu-id="df464-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="df464-133">Esempi di ASP.NET Core DataProtection</span><span class="sxs-lookup"><span data-stu-id="df464-133">ASP.NET Core DataProtection samples</span></span>](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="df464-134">Registro</span><span class="sxs-lookup"><span data-stu-id="df464-134">Registry</span></span>

<span data-ttu-id="df464-135">**Si applica solo alle distribuzioni di Windows.**</span><span class="sxs-lookup"><span data-stu-id="df464-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="df464-136">In alcuni casi l'app potrebbe non avere accesso in scrittura alla file system.</span><span class="sxs-lookup"><span data-stu-id="df464-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="df464-137">Si consideri uno scenario in cui un'app è in esecuzione come account del servizio virtuale, ad esempio l'identità del pool di applicazioni di *w3wp. exe*.</span><span class="sxs-lookup"><span data-stu-id="df464-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="df464-138">In questi casi, l'amministratore può eseguire il provisioning di una chiave del registro di sistema accessibile dall'identità dell'account del servizio.</span><span class="sxs-lookup"><span data-stu-id="df464-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="df464-139">Chiamare il metodo di estensione [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) , come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="df464-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="df464-140">Specificare un [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) che punta alla posizione in cui devono essere archiviate le chiavi crittografiche:</span><span class="sxs-lookup"><span data-stu-id="df464-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="df464-141">Si consiglia di utilizzare [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) per crittografare le chiavi inattive.</span><span class="sxs-lookup"><span data-stu-id="df464-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="df464-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="df464-142">Entity Framework Core</span></span>

<span data-ttu-id="df464-143">Il pacchetto [Microsoft. AspNetCore. dataprotection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) fornisce un meccanismo per archiviare le chiavi di protezione dati in un database usando Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="df464-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="df464-144">Il `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` pacchetto NuGet deve essere aggiunto al file di progetto e non è incluso nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="df464-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="df464-145">Con questo pacchetto, le chiavi possono essere condivise tra più istanze di un'app Web.</span><span class="sxs-lookup"><span data-stu-id="df464-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="df464-146">Per configurare il provider di EF Core, chiamare il metodo [\<PersistKeysToDbContext TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :</span><span class="sxs-lookup"><span data-stu-id="df464-146">To configure the EF Core provider, call the [PersistKeysToDbContext\<TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="df464-147">Il parametro generico, `TContext`, deve ereditare da [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e implementare [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="df464-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="df464-148">Creare la `DataProtectionKeys` tabella.</span><span class="sxs-lookup"><span data-stu-id="df464-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="df464-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df464-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="df464-150">Eseguire i comandi seguenti nella finestra **console di gestione pacchetti** (PMC):</span><span class="sxs-lookup"><span data-stu-id="df464-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[<span data-ttu-id="df464-151">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="df464-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="df464-152">Eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="df464-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="df464-153">`MyKeysContext`è l' `DbContext` oggetto definito nel codice di esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="df464-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="df464-154">Se si usa un `DbContext` con un nome diverso, sostituire con il `DbContext` nome. `MyKeysContext`</span><span class="sxs-lookup"><span data-stu-id="df464-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="df464-155">La `DataProtectionKeys` classe/entità adotta la struttura illustrata nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="df464-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="df464-156">Proprietà/campo</span><span class="sxs-lookup"><span data-stu-id="df464-156">Property/Field</span></span> | <span data-ttu-id="df464-157">Tipo CLR</span><span class="sxs-lookup"><span data-stu-id="df464-157">CLR Type</span></span> | <span data-ttu-id="df464-158">Tipo SQL</span><span class="sxs-lookup"><span data-stu-id="df464-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="df464-159">`int`, PK, not null</span><span class="sxs-lookup"><span data-stu-id="df464-159">`int`, PK, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="df464-160">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="df464-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="df464-161">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="df464-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="df464-162">Repository di chiavi personalizzate</span><span class="sxs-lookup"><span data-stu-id="df464-162">Custom key repository</span></span>

<span data-ttu-id="df464-163">Se i meccanismi predefiniti non sono appropriati, lo sviluppatore può specificare il proprio meccanismo di persistenza della chiave fornendo un [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)personalizzato.</span><span class="sxs-lookup"><span data-stu-id="df464-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
