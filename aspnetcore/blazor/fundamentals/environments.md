---
title: 'Ambienti di ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: "Informazioni sugli ambienti in :::no-loc(Blazor)::: , incluse informazioni su come impostare l'ambiente di un' :::no-loc(Blazor WebAssembly)::: app."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 61d46e0bd83d8bd82bf7faaf9d8f2fecbacc2ffa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056036"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="66d16-103">Ambienti di ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="66d16-103">ASP.NET Core :::no-loc(Blazor)::: environments</span></span>

> [!NOTE]
> <span data-ttu-id="66d16-104">Questo argomento si applica a :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="66d16-104">This topic applies to :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="66d16-105">Per indicazioni generali sulla configurazione dell'app ASP.NET Core, vedere <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="66d16-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="66d16-106">Quando si esegue un'app localmente, per impostazione predefinita viene impostato lo sviluppo per l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="66d16-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="66d16-107">Quando l'app viene pubblicata, per impostazione predefinita viene impostato l'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="66d16-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="66d16-108">Un'app ospitata :::no-loc(Blazor WebAssembly)::: preleva l'ambiente dal server tramite un middleware che comunica l'ambiente al browser aggiungendo l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="66d16-108">A hosted :::no-loc(Blazor WebAssembly)::: app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="66d16-109">Il valore dell'intestazione è l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="66d16-109">The value of the header is the environment.</span></span> <span data-ttu-id="66d16-110">L'app ospitata :::no-loc(Blazor)::: e l'app Server condividono lo stesso ambiente.</span><span class="sxs-lookup"><span data-stu-id="66d16-110">The hosted :::no-loc(Blazor)::: app and the server app share the same environment.</span></span> <span data-ttu-id="66d16-111">Per ulteriori informazioni, tra cui come configurare l'ambiente, vedere <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="66d16-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="66d16-112">Per un'app autonoma in esecuzione in locale, il server di sviluppo aggiunge l' `blazor-environment` intestazione per specificare l'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="66d16-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="66d16-113">Per specificare l'ambiente per altri ambienti host, aggiungere l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="66d16-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="66d16-114">Nell'esempio seguente per IIS aggiungere l'intestazione personalizzata al `web.config` file pubblicato.</span><span class="sxs-lookup"><span data-stu-id="66d16-114">In the following example for IIS, add the custom header to the published `web.config` file.</span></span> <span data-ttu-id="66d16-115">Il `web.config` file si trova nella `bin/Release/{TARGET FRAMEWORK}/publish` cartella:</span><span class="sxs-lookup"><span data-stu-id="66d16-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder:</span></span>

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
> <span data-ttu-id="66d16-116">Per usare un `web.config` file personalizzato per IIS che non viene sovrascritto quando l'app viene pubblicata nella `publish` cartella, vedere <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="66d16-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="66d16-117">Ottenere l'ambiente dell'app in un componente inserendo <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> e leggendo la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> proprietà:</span><span class="sxs-lookup"><span data-stu-id="66d16-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="66d16-118">Durante l'avvio, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> espone l'oggetto <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> tramite la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> proprietà, che consente agli sviluppatori di disporre di logica specifica dell'ambiente nel codice:</span><span class="sxs-lookup"><span data-stu-id="66d16-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="66d16-119">I metodi di estensione pratici seguenti consentono di controllare l'ambiente corrente per i nomi di sviluppo, produzione, gestione temporanea e ambiente personalizzato:</span><span class="sxs-lookup"><span data-stu-id="66d16-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="66d16-120">La <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> proprietà può essere utilizzata durante l'avvio quando il <xref:Microsoft.AspNetCore.Components.NavigationManager> servizio non è disponibile.</span><span class="sxs-lookup"><span data-stu-id="66d16-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="66d16-121">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="66d16-121">Additional resources</span></span>

* <xref:fundamentals/environments>
