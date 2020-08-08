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
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>Sostituire ASP.NET machineKey in ASP.NET Core

<a name="compatibility-replacing-machinekey"></a>

L'implementazione dell' `<machineKey>` elemento in ASP.NET [è sostituibile](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). In questo modo è possibile indirizzare la maggior parte delle chiamate alle routine di crittografia ASP.NET tramite un meccanismo di protezione dei dati sostitutivo, incluso il nuovo sistema di protezione dei dati.

## <a name="package-installation"></a>Installazione del pacchetto

> [!NOTE]
> Il nuovo sistema di protezione dei dati può essere installato solo in un'applicazione ASP.NET esistente destinata a .NET 4.5.1 o versione successiva. L'installazione avrà esito negativo se l'applicazione è destinata a .NET 4,5 o versioni precedenti.

Per installare il nuovo sistema di protezione dei dati in un progetto ASP.NET 4.5.1 + esistente, installare il pacchetto Microsoft.AspNetCore.DataProtection.SystemWeb. Verrà creata un'istanza del sistema di protezione dei dati usando le impostazioni di [configurazione predefinite](xref:security/data-protection/configuration/default-settings) .

Quando si installa il pacchetto, viene inserita una riga in *Web.config* che indica a ASP.NET di utilizzarla per [la maggior parte delle operazioni di crittografia](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), tra cui l'autenticazione basata su form, lo stato di visualizzazione e le chiamate a machineKey. Protect. La riga inserita viene letta come segue.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> È possibile stabilire se il nuovo sistema di protezione dei dati è attivo controllando i campi come `__VIEWSTATE` , che dovrebbe iniziare con "CfDJ8", come nell'esempio riportato di seguito. "CfDJ8" è la rappresentazione Base64 dell'intestazione "09 F0 C9 F0" di Magic che identifica un payload protetto dal sistema di protezione dei dati.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>Configurazione pacchetto

Viene creata un'istanza del sistema di protezione dei dati con una configurazione predefinita di installazione zero. Tuttavia, poiché per impostazione predefinita le chiavi vengono rese persistenti nella file system locale, questa operazione non funzionerà per le applicazioni distribuite in una farm. Per risolvere questo problema, è possibile fornire la configurazione creando un tipo che sottoclassi DataProtectionStartup ed esegue l'override del relativo metodo ConfigureServices.

Di seguito è riportato un esempio di un tipo di avvio per la protezione dei dati personalizzato che consente di configurare le chiavi in modo permanente e il modo in cui vengono crittografate. Esegue inoltre l'override dei criteri di isolamento delle app predefiniti fornendo il nome dell'applicazione.

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
> È anche possibile usare `<machineKey applicationName="my-app" ... />` al posto di una chiamata esplicita a MyApplicationName. Si tratta di un meccanismo utile per evitare di forzare lo sviluppatore a creare un tipo derivato da DataProtectionStartup se si desidera configurare il nome dell'applicazione.

Per abilitare questa configurazione personalizzata, tornare a Web.config e cercare l' `<appSettings>` elemento che l'installazione del pacchetto ha aggiunto al file di configurazione. Il markup sarà simile al seguente:

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

Inserire il valore blank con il nome qualificato dall'assembly del tipo derivato da DataProtectionStartup appena creato. Se il nome dell'applicazione è DataProtectionDemo, avrà un aspetto simile al seguente.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

Il sistema di protezione dei dati appena configurato è ora pronto per l'uso all'interno dell'applicazione.
