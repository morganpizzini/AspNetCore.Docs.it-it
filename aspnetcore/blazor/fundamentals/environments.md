---
title: Ambienti di ASP.NET Core Blazor
author: guardrex
description: Informazioni sugli ambienti in Blazor , incluse informazioni su come impostare l'ambiente di un' Blazor WebAssembly app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 8a672b3d2aff4dd2b80465b0f6dac038d299eaa9
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014425"
---
# <a name="aspnet-core-no-locblazor-environments"></a>Ambienti di ASP.NET Core Blazor

> [!NOTE]
> Questo argomento si applica a Blazor WebAssembly . Per indicazioni generali sulla configurazione dell'app ASP.NET Core, vedere <xref:fundamentals/environments> .

Quando si esegue un'app localmente, per impostazione predefinita viene impostato lo sviluppo per l'ambiente. Quando l'app viene pubblicata, per impostazione predefinita viene impostato l'ambiente di produzione.

Un'app ospitata Blazor WebAssembly preleva l'ambiente dal server tramite un middleware che comunica l'ambiente al browser aggiungendo l' `blazor-environment` intestazione. Il valore dell'intestazione è l'ambiente. L'app ospitata Blazor e l'app Server condividono lo stesso ambiente. Per ulteriori informazioni, tra cui come configurare l'ambiente, vedere <xref:fundamentals/environments> .

Per un'app autonoma in esecuzione in locale, il server di sviluppo aggiunge l' `blazor-environment` intestazione per specificare l'ambiente di sviluppo. Per specificare l'ambiente per altri ambienti host, aggiungere l' `blazor-environment` intestazione.

Nell'esempio seguente per IIS aggiungere l'intestazione personalizzata al `web.config` file pubblicato. Il `web.config` file si trova nella `bin/Release/{TARGET FRAMEWORK}/publish` cartella:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> Per usare un `web.config` file personalizzato per IIS che non viene sovrascritto quando l'app viene pubblicata nella `publish` cartella, vedere <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .

Ottenere l'ambiente dell'app in un componente inserendo <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> e leggendo la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> proprietà:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Durante l'avvio, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> espone l'oggetto <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> tramite la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> proprietà, che consente agli sviluppatori di disporre di logica specifica dell'ambiente nel codice:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

I metodi di estensione pratici seguenti consentono di controllare l'ambiente corrente per i nomi di sviluppo, produzione, gestione temporanea e ambiente personalizzato:

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

La <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> proprietà può essere utilizzata durante l'avvio quando il <xref:Microsoft.AspNetCore.Components.NavigationManager> servizio non è disponibile.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/environments>
