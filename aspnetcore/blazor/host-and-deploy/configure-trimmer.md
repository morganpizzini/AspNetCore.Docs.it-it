---
title: Configurare il trimmer per ASP.NET Core Blazor
author: guardrex
description: Informazioni su come controllare il linker linguaggio intermedio (IL) (trimmer) quando si compila un' Blazor app.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 57d8f069c79b558020253968d736f350bc8a6f03
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721736"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="2075f-103">Configurare il trimmer per ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="2075f-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="2075f-104">Di [il](https://github.com/pranavkm) suo</span><span class="sxs-lookup"><span data-stu-id="2075f-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="2075f-105">Blazor WebAssembly esegue il taglio del [linguaggio intermedio (il)](/dotnet/standard/managed-code#intermediate-language--execution) per ridurre le dimensioni dell'output pubblicato.</span><span class="sxs-lookup"><span data-stu-id="2075f-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="2075f-106">Il taglio di un'app ottimizza le dimensioni, ma può avere effetti negativi.</span><span class="sxs-lookup"><span data-stu-id="2075f-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="2075f-107">Le app che usano la reflection o le funzionalità dinamiche correlate potrebbero interrompersi quando vengono tagliate, perché il trimmer non è a conoscenza del comportamento dinamico e non può determinare in generale quali tipi sono necessari per la reflection in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="2075f-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="2075f-108">Per tagliare tali app, è necessario che il trimmer sia informato sui tipi necessari per la reflection nel codice e nei pacchetti o Framework da cui dipende l'app.</span><span class="sxs-lookup"><span data-stu-id="2075f-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="2075f-109">Per assicurarsi che l'app tagliata funzioni correttamente una volta distribuita, è importante testare spesso l'output pubblicato durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="2075f-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="2075f-110">Il trimming per le app .NET può essere disabilitato impostando la `PublishTrimmed` Proprietà MSBuild su `false` nel file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="2075f-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
<span data-ttu-id="2075f-111">Opzioni aggiuntive per la configurazione del trimmer sono disponibili in [Opzioni di taglio](/dotnet/core/deploying/trimming-options).</span><span class="sxs-lookup"><span data-stu-id="2075f-111">Additional options to configure the trimmer can be found at [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2075f-112">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2075f-112">Additional resources</span></span>

* [<span data-ttu-id="2075f-113">Trimming delle distribuzioni autonome e degli eseguibili</span><span class="sxs-lookup"><span data-stu-id="2075f-113">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>