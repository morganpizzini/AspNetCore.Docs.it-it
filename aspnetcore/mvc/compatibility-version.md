---
title: Versione di compatibilità per ASP.NET Core MVC
author: rick-anderson
description: Informazioni su come la classe Startup in ASP.NET Core configura i servizi e la pipeline delle richieste dell'app.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/compatibility-version
ms.openlocfilehash: 45eca0bedc2e4e5c74936ae5d1bf525774467b2a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774210"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="17da1-103">Versione di compatibilità per ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="17da1-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="17da1-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="17da1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="17da1-105">Il <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> metodo è un no-op per le app ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="17da1-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="17da1-106">Ovvero, la chiamata `SetCompatibilityVersion` a con qualsiasi valore <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> di non ha alcun effetto sull'applicazione.</span><span class="sxs-lookup"><span data-stu-id="17da1-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="17da1-107">La versione secondaria successiva di ASP.NET Core può fornire un nuovo `CompatibilityVersion` valore.</span><span class="sxs-lookup"><span data-stu-id="17da1-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="17da1-108">`CompatibilityVersion`i `Version_2_0` valori `Version_2_2` tramite sono `[Obsolete(...)]`contrassegnati.</span><span class="sxs-lookup"><span data-stu-id="17da1-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="17da1-109">Vedere la pagina relativa [alle modifiche delle API in antifalsificazione, CORS, diagnostica, MVC e routing](https://github.com/aspnet/Announcements/issues/387).</span><span class="sxs-lookup"><span data-stu-id="17da1-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="17da1-110">Questo elenco include modifiche di rilievo per le opzioni di compatibilità.</span><span class="sxs-lookup"><span data-stu-id="17da1-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="17da1-111">Per vedere come `SetCompatibilityVersion` funziona con le app ASP.NET Core 2. x, selezionare la [versione ASP.NET Core 2,2 di questo articolo](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="17da1-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="17da1-112">Il <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> metodo consente a un'app ASP.NET Core 2. x di acconsentire o rifiutare esplicitamente le modifiche del comportamento potenzialmente indesiderate introdotte in ASP.NET Core MVC 2,1 o 2,2.</span><span class="sxs-lookup"><span data-stu-id="17da1-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="17da1-113">Queste modifiche potenzialmente importanti del comportamento riguardano in genere il funzionamento del sottosistema MVC e la modalità con cui il **codice dell'utente** viene chiamato dal runtime.</span><span class="sxs-lookup"><span data-stu-id="17da1-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="17da1-114">Se si acconsente esplicitamente, si ottiene il comportamento più recente e il comportamento a lungo termine di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="17da1-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="17da1-115">Il codice seguente imposta la modalità di compatibilità su ASP.NET Core 2.2:</span><span class="sxs-lookup"><span data-stu-id="17da1-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="17da1-116">È consigliabile testare l'app usando la versione più recente (`CompatibilityVersion.Latest`).</span><span class="sxs-lookup"><span data-stu-id="17da1-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="17da1-117">Microsoft prevede che la maggior parte delle app non riscontrerà modifiche importanti del comportamento quando viene usata la versione più recente.</span><span class="sxs-lookup"><span data-stu-id="17da1-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="17da1-118">Le app che `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` chiamano sono protette da potenziali modifiche del comportamento introdotte nelle versioni ASP.NET Core 2.1/2.2 MVC.</span><span class="sxs-lookup"><span data-stu-id="17da1-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="17da1-119">La protezione:</span><span class="sxs-lookup"><span data-stu-id="17da1-119">This protection:</span></span>

* <span data-ttu-id="17da1-120">Non è valida per tutte le modifiche 2.1 e successive, ma è destinata alle modifiche potenzialmente importanti del comportamento del runtime ASP.NET Core nel sottosistema MVC.</span><span class="sxs-lookup"><span data-stu-id="17da1-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="17da1-121">Non si estende a ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="17da1-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="17da1-122">La compatibilità predefinita per le app ASP.NET Core 2,1 e 2,2 che **non** chiamano `SetCompatibilityVersion` è la compatibilità 2,0.</span><span class="sxs-lookup"><span data-stu-id="17da1-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="17da1-123">In altri termini il fatto di non chiamare `SetCompatibilityVersion` equivale a chiamare `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span><span class="sxs-lookup"><span data-stu-id="17da1-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="17da1-124">Il codice seguente imposta la modalità di compatibilità su ASP.NET Core 2.2, con l'eccezione dei comportamenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="17da1-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="17da1-125">Per le app che riscontrano modifiche potenzialmente importanti del comportamento, l'uso delle opzioni di compatibilità appropriate:</span><span class="sxs-lookup"><span data-stu-id="17da1-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="17da1-126">Consente di usare la versione più recente e di rifiutare esplicitamente modifiche importanti del comportamento specifiche.</span><span class="sxs-lookup"><span data-stu-id="17da1-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="17da1-127">Garantisce il tempo necessario per l'aggiornamento dell'app, che in tal modo potrà funzionare con le modifiche più recenti.</span><span class="sxs-lookup"><span data-stu-id="17da1-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="17da1-128">La documentazione di <xref:Microsoft.AspNetCore.Mvc.MvcOptions> offre una spiegazione chiara delle modifiche e dei motivi per cui queste rappresentano un miglioramento per la maggior parte degli utenti.</span><span class="sxs-lookup"><span data-stu-id="17da1-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="17da1-129">Con ASP.NET Core 3,0, i comportamenti precedenti supportati dalle opzioni di compatibilità sono stati rimossi.</span><span class="sxs-lookup"><span data-stu-id="17da1-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="17da1-130">Queste modifiche positive andranno a vantaggio della maggior parte degli utenti.</span><span class="sxs-lookup"><span data-stu-id="17da1-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="17da1-131">Introducendo queste modifiche nei 2,1 e 2,2, la maggior parte delle app può trarre vantaggio, mentre altre hanno tempo per l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="17da1-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end
