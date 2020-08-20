---
title: Helper tag di collegamento in ASP.NET Core
author: rick-anderson
ms.author: riande
description: Individuare gli attributi dell'helper tag di collegamento ASP.NET Core e il ruolo di ciascun attributo per estendere il comportamento del tag di collegamento HTML.
ms.custom: mvc
ms.date: 09/24/2019
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
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 09507294b90f08bbaf134f611aad0b91504ccffb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635073"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="81bef-103">Helper tag di collegamento in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81bef-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="81bef-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="81bef-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="81bef-105">L' [Helper tag di collegamento](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) genera un collegamento a un file CSS primario o di fallback.</span><span class="sxs-lookup"><span data-stu-id="81bef-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="81bef-106">In genere il file CSS primario si trova in una rete per la [distribuzione di contenuti](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="81bef-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="81bef-107">L'helper tag di collegamento consente di specificare una rete CDN per il file CSS e un fallback quando la rete CDN non è disponibile.</span><span class="sxs-lookup"><span data-stu-id="81bef-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="81bef-108">L'helper tag di collegamento offre il vantaggio delle prestazioni di una rete CDN con l'affidabilità dell'hosting locale.</span><span class="sxs-lookup"><span data-stu-id="81bef-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="81bef-109">Il Razor markup seguente mostra l' `head` elemento di un file di layout creato con il modello di app web di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="81bef-109">The following Razor markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="81bef-110">Di seguito viene eseguito il rendering del codice HTML dal codice precedente (in un ambiente non di sviluppo):</span><span class="sxs-lookup"><span data-stu-id="81bef-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="81bef-111">Nel codice precedente, l'helper tag di collegamento ha generato l' `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` elemento e il seguente codice JavaScript usato per verificare che il file *bootstrap. min. CSS* richiesto sia disponibile nella rete CDN.</span><span class="sxs-lookup"><span data-stu-id="81bef-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="81bef-112">In questo caso, il file CSS era disponibile, in modo che l'helper Tag generasse l' `<link />` elemento con il file CSS della rete CDN.</span><span class="sxs-lookup"><span data-stu-id="81bef-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="81bef-113">Attributi di helper tag di collegamento comunemente utilizzati</span><span class="sxs-lookup"><span data-stu-id="81bef-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="81bef-114">Vedere [Helper tag di collegamento](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  per tutti gli attributi, le proprietà e i metodi dell'helper tag di collegamento.</span><span class="sxs-lookup"><span data-stu-id="81bef-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="81bef-115">href</span><span class="sxs-lookup"><span data-stu-id="81bef-115">href</span></span>

<span data-ttu-id="81bef-116">Indirizzo preferenziale della risorsa collegata.</span><span class="sxs-lookup"><span data-stu-id="81bef-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="81bef-117">All'indirizzo viene passato un pensiero al codice HTML generato in tutti i casi.</span><span class="sxs-lookup"><span data-stu-id="81bef-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="81bef-118">ASP-fallback-href</span><span class="sxs-lookup"><span data-stu-id="81bef-118">asp-fallback-href</span></span>

<span data-ttu-id="81bef-119">URL di un foglio di stile CSS di cui eseguire il fallback in caso di errore dell'URL primario.</span><span class="sxs-lookup"><span data-stu-id="81bef-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="81bef-120">ASP-fallback-test-Class</span><span class="sxs-lookup"><span data-stu-id="81bef-120">asp-fallback-test-class</span></span>

<span data-ttu-id="81bef-121">Nome della classe definito nel foglio di stile da utilizzare per il test di fallback.</span><span class="sxs-lookup"><span data-stu-id="81bef-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="81bef-122">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="81bef-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="81bef-123">ASP-fallback-test-proprietà</span><span class="sxs-lookup"><span data-stu-id="81bef-123">asp-fallback-test-property</span></span>

<span data-ttu-id="81bef-124">Nome della proprietà CSS da utilizzare per il test di fallback.</span><span class="sxs-lookup"><span data-stu-id="81bef-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="81bef-125">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="81bef-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="81bef-126">ASP-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="81bef-126">asp-fallback-test-value</span></span>

<span data-ttu-id="81bef-127">Valore della proprietà CSS da utilizzare per il test di fallback.</span><span class="sxs-lookup"><span data-stu-id="81bef-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="81bef-128">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="81bef-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81bef-129">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="81bef-129">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
