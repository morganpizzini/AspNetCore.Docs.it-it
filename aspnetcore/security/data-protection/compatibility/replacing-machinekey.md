---
title: Sostituire ASP.NET machineKey in ASP.NET Core
author: rick-anderson
description: Scopri come sostituire machineKey in ASP.NET per consentire l'uso di un nuovo e più sicuro sistema di protezione dei dati.
ms.author: riande
ms.date: 04/06/2019
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
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 6b98244f9f288101a971257c9dab50d8895b8881
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018208"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="af6ec-103">Sostituire ASP.NET machineKey in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af6ec-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="af6ec-104">L'implementazione dell' `<machineKey>` elemento in ASP.NET [è sostituibile](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span><span class="sxs-lookup"><span data-stu-id="af6ec-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="af6ec-105">In questo modo è possibile indirizzare la maggior parte delle chiamate alle routine di crittografia ASP.NET tramite un meccanismo di protezione dei dati sostitutivo, incluso il nuovo sistema di protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="af6ec-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="af6ec-106">Installazione del pacchetto</span><span class="sxs-lookup"><span data-stu-id="af6ec-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="af6ec-107">Il nuovo sistema di protezione dei dati può essere installato solo in un'applicazione ASP.NET esistente destinata a .NET 4.5.1 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="af6ec-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="af6ec-108">L'installazione avrà esito negativo se l'applicazione è destinata a .NET 4,5 o versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="af6ec-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="af6ec-109">Per installare il nuovo sistema di protezione dei dati in un progetto ASP.NET 4.5.1 + esistente, installare il pacchetto Microsoft.AspNetCore.DataProtection.SystemWeb.</span><span class="sxs-lookup"><span data-stu-id="af6ec-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="af6ec-110">Verrà creata un'istanza del sistema di protezione dei dati usando le impostazioni di [configurazione predefinite](xref:security/data-protection/configuration/default-settings) .</span><span class="sxs-lookup"><span data-stu-id="af6ec-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="af6ec-111">Quando si installa il pacchetto, viene inserita una riga in *Web.config* che indica a ASP.NET di utilizzarla per [la maggior parte delle operazioni di crittografia](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), tra cui l'autenticazione basata su form, lo stato di visualizzazione e le chiamate a machineKey. Protect.</span><span class="sxs-lookup"><span data-stu-id="af6ec-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="af6ec-112">La riga inserita viene letta come segue.</span><span class="sxs-lookup"><span data-stu-id="af6ec-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="af6ec-113">È possibile stabilire se il nuovo sistema di protezione dei dati è attivo controllando i campi come `__VIEWSTATE` , che dovrebbe iniziare con "CfDJ8", come nell'esempio riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="af6ec-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="af6ec-114">"CfDJ8" è la rappresentazione Base64 dell'intestazione "09 F0 C9 F0" di Magic che identifica un payload protetto dal sistema di protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="af6ec-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="af6ec-115">Configurazione pacchetto</span><span class="sxs-lookup"><span data-stu-id="af6ec-115">Package configuration</span></span>

<span data-ttu-id="af6ec-116">Viene creata un'istanza del sistema di protezione dei dati con una configurazione predefinita di installazione zero.</span><span class="sxs-lookup"><span data-stu-id="af6ec-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="af6ec-117">Tuttavia, poiché per impostazione predefinita le chiavi vengono rese persistenti nella file system locale, questa operazione non funzionerà per le applicazioni distribuite in una farm.</span><span class="sxs-lookup"><span data-stu-id="af6ec-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="af6ec-118">Per risolvere questo problema, è possibile fornire la configurazione creando un tipo che sottoclassi DataProtectionStartup ed esegue l'override del relativo metodo ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="af6ec-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="af6ec-119">Di seguito è riportato un esempio di un tipo di avvio per la protezione dei dati personalizzato che consente di configurare le chiavi in modo permanente e il modo in cui vengono crittografate.</span><span class="sxs-lookup"><span data-stu-id="af6ec-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="af6ec-120">Esegue inoltre l'override dei criteri di isolamento delle app predefiniti fornendo il nome dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="af6ec-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="af6ec-121">È anche possibile usare `<machineKey applicationName="my-app" ... />` al posto di una chiamata esplicita a MyApplicationName.</span><span class="sxs-lookup"><span data-stu-id="af6ec-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="af6ec-122">Si tratta di un meccanismo utile per evitare di forzare lo sviluppatore a creare un tipo derivato da DataProtectionStartup se si desidera configurare il nome dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="af6ec-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="af6ec-123">Per abilitare questa configurazione personalizzata, tornare a Web.config e cercare l' `<appSettings>` elemento che l'installazione del pacchetto ha aggiunto al file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="af6ec-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="af6ec-124">Il markup sarà simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="af6ec-124">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="af6ec-125">Inserire il valore blank con il nome qualificato dall'assembly del tipo derivato da DataProtectionStartup appena creato.</span><span class="sxs-lookup"><span data-stu-id="af6ec-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="af6ec-126">Se il nome dell'applicazione è DataProtectionDemo, avrà un aspetto simile al seguente.</span><span class="sxs-lookup"><span data-stu-id="af6ec-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="af6ec-127">Il sistema di protezione dei dati appena configurato è ora pronto per l'uso all'interno dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="af6ec-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
