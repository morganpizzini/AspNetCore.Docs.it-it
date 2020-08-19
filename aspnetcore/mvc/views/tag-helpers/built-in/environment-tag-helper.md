---
title: Helper tag di ambiente in ASP.NET Core
author: pkellner
description: Helper tag di ambiente ASP.NET Core definito con tutte le proprietà
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: de37bf704ac377a15ef600b96ba78056c51ea7be
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633864"
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="19096-103">Helper tag di ambiente in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="19096-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="19096-104">Di [Peter Kellner](https://peterkellner.net) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="19096-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="19096-105">L'helper tag di ambiente esegue in modo condizionale il rendering del contenuto racchiuso in base all' [ambiente host](xref:fundamentals/environments)corrente.</span><span class="sxs-lookup"><span data-stu-id="19096-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="19096-106">L'unico attributo dell'helper tag di ambiente, `names`, è un elenco delimitato da virgole di nomi di ambiente.</span><span class="sxs-lookup"><span data-stu-id="19096-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="19096-107">Se nessuno dei nomi di ambiente specificato corrisponde all'ambiente corrente, viene eseguito il rendering del contenuto incluso.</span><span class="sxs-lookup"><span data-stu-id="19096-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="19096-108">Per una panoramica degli helper per tag, vedere <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="19096-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="19096-109">Attributi dell'helper tag di ambiente</span><span class="sxs-lookup"><span data-stu-id="19096-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="19096-110">nomi</span><span class="sxs-lookup"><span data-stu-id="19096-110">names</span></span>

<span data-ttu-id="19096-111">`names` accetta un singolo nome di ambiente host o un elenco delimitato da virgole di nomi di ambiente, che attiva il rendering del contenuto.</span><span class="sxs-lookup"><span data-stu-id="19096-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="19096-112">I valori di ambiente vengono confrontati con il valore corrente restituito da [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="19096-112">Environment values are compared to the current value returned by [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="19096-113">Il confronto non applica la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="19096-113">The comparison ignores case.</span></span>

<span data-ttu-id="19096-114">L'esempio seguente usa un helper tag di ambiente.</span><span class="sxs-lookup"><span data-stu-id="19096-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="19096-115">Il rendering del contenuto viene eseguito se l'ambiente host è Staging o Production:</span><span class="sxs-lookup"><span data-stu-id="19096-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="19096-116">Attributi include ed exclude</span><span class="sxs-lookup"><span data-stu-id="19096-116">include and exclude attributes</span></span>

<span data-ttu-id="19096-117">`include`& `exclude` gli attributi controllano il rendering del contenuto racchiuso in base ai nomi degli ambienti host inclusi o esclusi.</span><span class="sxs-lookup"><span data-stu-id="19096-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="19096-118">include</span><span class="sxs-lookup"><span data-stu-id="19096-118">include</span></span>

<span data-ttu-id="19096-119">La proprietà `include` ha un comportamento simile all'attributo `names`.</span><span class="sxs-lookup"><span data-stu-id="19096-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="19096-120">Un ambiente elencato nel valore dell'attributo `include` deve corrispondere all'ambiente host dell'app ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) per il rendering del contenuto del tag `<environment>`.</span><span class="sxs-lookup"><span data-stu-id="19096-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="19096-121">escludere</span><span class="sxs-lookup"><span data-stu-id="19096-121">exclude</span></span>

<span data-ttu-id="19096-122">Al contrario dell'attributo `include`, il rendering del contenuto del tag `<environment>` viene eseguito quando l'ambiente host non corrisponde a un ambiente elencato nel valore dell'attributo `exclude`.</span><span class="sxs-lookup"><span data-stu-id="19096-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="19096-123">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="19096-123">Additional resources</span></span>

* <xref:fundamentals/environments>
