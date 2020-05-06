---
title: Helper tag di script in ASP.NET Core
author: rick-anderson
ms.author: riande
description: Individuare gli attributi degli helper tag di script ASP.NET Core e il ruolo di ciascun attributo per estendere il comportamento del tag di script HTML.
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 4162221ef3401427c44fb54dee9c36b78b39564f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774665"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="90f38-103">Helper tag di script in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="90f38-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="90f38-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="90f38-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="90f38-105">L' [Helper tag script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) genera un collegamento a un file di script primario o di fallback.</span><span class="sxs-lookup"><span data-stu-id="90f38-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="90f38-106">In genere il file di script primario si trova in una rete per la [distribuzione di contenuti](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="90f38-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="90f38-107">L'helper tag script consente di specificare una rete CDN per il file di script e un fallback quando la rete CDN non è disponibile.</span><span class="sxs-lookup"><span data-stu-id="90f38-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="90f38-108">L'helper tag script offre il vantaggio in merito alle prestazioni di una rete CDN con l'affidabilità dell'hosting locale.</span><span class="sxs-lookup"><span data-stu-id="90f38-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="90f38-109">Il markup Razor seguente mostra un `script` elemento con un fallback:</span><span class="sxs-lookup"><span data-stu-id="90f38-109">The following Razor markup shows a `script` element with a fallback:</span></span>

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

<span data-ttu-id="90f38-110">Non usare l' `<script>` attributo [rinvia](https://developer.mozilla.org/docs/Web/HTML/Element/script) dell'elemento per rinviare il caricamento dello script della rete CDN.</span><span class="sxs-lookup"><span data-stu-id="90f38-110">Don't use the `<script>` element's [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) attribute to defer loading the CDN script.</span></span> <span data-ttu-id="90f38-111">L'helper tag script esegue il rendering di JavaScript che esegue immediatamente l'espressione [ASP-fallback-test](#asp-fallback-test) .</span><span class="sxs-lookup"><span data-stu-id="90f38-111">The Script Tag Helper renders JavaScript that immediately executes the [asp-fallback-test](#asp-fallback-test) expression.</span></span> <span data-ttu-id="90f38-112">L'espressione ha esito negativo se viene posticipato il caricamento dello script della rete CDN.</span><span class="sxs-lookup"><span data-stu-id="90f38-112">The expression fails if loading the CDN script is deferred.</span></span>

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="90f38-113">Attributi di helper tag di script di uso comune</span><span class="sxs-lookup"><span data-stu-id="90f38-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="90f38-114">Vedere [script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) per tutti gli attributi, le proprietà e i metodi dell'helper tag di script.</span><span class="sxs-lookup"><span data-stu-id="90f38-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="90f38-115">ASP-fallback-test</span><span class="sxs-lookup"><span data-stu-id="90f38-115">asp-fallback-test</span></span>

<span data-ttu-id="90f38-116">Metodo di script definito nello script principale da usare per il test di fallback.</span><span class="sxs-lookup"><span data-stu-id="90f38-116">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="90f38-117">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span><span class="sxs-lookup"><span data-stu-id="90f38-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="90f38-118">ASP-fallback-src</span><span class="sxs-lookup"><span data-stu-id="90f38-118">asp-fallback-src</span></span>

<span data-ttu-id="90f38-119">L'URL di un tag di script di cui eseguire il fallback nel caso in cui si verifica l'errore primario.</span><span class="sxs-lookup"><span data-stu-id="90f38-119">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="90f38-120">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span><span class="sxs-lookup"><span data-stu-id="90f38-120">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90f38-121">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="90f38-121">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
