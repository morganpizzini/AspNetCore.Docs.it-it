---
title: Client IP safelist for ASP.NET Core
author: damienbod
description: Informazioni su come scrivere filtri middleware o action per convalidare gli indirizzi IP remoti in base a un elenco di indirizzi IP approvati.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471784"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="6ce39-103">Client IP safelist for ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ce39-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="6ce39-104">Di [Damien Bowden](https://twitter.com/damien_bod) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="6ce39-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="6ce39-105">Questo articolo illustra tre modi per implementare un elenco indirizzi IP attendibili (noto anche come elenco Consenti) in un'app ASP.NET Core.This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span><span class="sxs-lookup"><span data-stu-id="6ce39-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="6ce39-106">Un'app di esempio di accompagnamento illustra tutti e tre gli approcci.</span><span class="sxs-lookup"><span data-stu-id="6ce39-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="6ce39-107">È possibile usare:</span><span class="sxs-lookup"><span data-stu-id="6ce39-107">You can use:</span></span>

* <span data-ttu-id="6ce39-108">Middleware per controllare l'indirizzo IP remoto di ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="6ce39-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="6ce39-109">Filtri di azione MVC per controllare l'indirizzo IP remoto delle richieste per controller o metodi di azione specifici.</span><span class="sxs-lookup"><span data-stu-id="6ce39-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="6ce39-110">Filtri Razor Pages per controllare l'indirizzo IP remoto delle richieste per le pagine Razor.</span><span class="sxs-lookup"><span data-stu-id="6ce39-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="6ce39-111">In ogni caso, una stringa contenente gli indirizzi IP dei client approvati viene archiviata in un'impostazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="6ce39-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="6ce39-112">Il middleware o il filtro:</span><span class="sxs-lookup"><span data-stu-id="6ce39-112">The middleware or filter:</span></span>

* <span data-ttu-id="6ce39-113">Analizza la stringa in una matrice.</span><span class="sxs-lookup"><span data-stu-id="6ce39-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="6ce39-114">Controlla se l'indirizzo IP remoto esiste nell'array.</span><span class="sxs-lookup"><span data-stu-id="6ce39-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="6ce39-115">L'accesso è consentito se l'array contiene l'indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="6ce39-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="6ce39-116">In caso contrario, viene restituito un codice di stato HTTP 403 Forbidden.</span><span class="sxs-lookup"><span data-stu-id="6ce39-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="6ce39-117">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ce39-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="6ce39-118">Elenco indirizzi IP attendibili</span><span class="sxs-lookup"><span data-stu-id="6ce39-118">IP address safelist</span></span>

<span data-ttu-id="6ce39-119">Nell'app di esempio, l'elenco indirizzi IP attendibili è:In the sample app, the IP address safelist is:</span><span class="sxs-lookup"><span data-stu-id="6ce39-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="6ce39-120">Definito dalla `AdminSafeList` proprietà nel file *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="6ce39-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="6ce39-121">Stringa delimitata da punti e virgola che può contenere indirizzi [IPv4 (Internet Protocol versione 4)](https://wikipedia.org/wiki/IPv4) e [IPv6 (Internet Protocol versione 6).](https://wikipedia.org/wiki/IPv6)</span><span class="sxs-lookup"><span data-stu-id="6ce39-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="6ce39-122">Nell'esempio precedente sono consentiti gli `127.0.0.1` `192.168.1.5` indirizzi IPv4 di e `::1` e l'indirizzo di loopback IPv6 di (formato compresso per `0:0:0:0:0:0:0:1`).</span><span class="sxs-lookup"><span data-stu-id="6ce39-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="6ce39-123">Middleware</span><span class="sxs-lookup"><span data-stu-id="6ce39-123">Middleware</span></span>

<span data-ttu-id="6ce39-124">Il `Startup.Configure` metodo aggiunge `AdminSafeListMiddleware` il tipo di middleware personalizzato alla pipeline delle richieste dell'app.</span><span class="sxs-lookup"><span data-stu-id="6ce39-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="6ce39-125">L'elenco indirizzi attendibili viene recuperato con il provider di configurazione .NET Core e viene passato come parametro del costruttore.</span><span class="sxs-lookup"><span data-stu-id="6ce39-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="6ce39-126">Il middleware analizza la stringa in una matrice e cerca l'indirizzo IP remoto nella matrice.</span><span class="sxs-lookup"><span data-stu-id="6ce39-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="6ce39-127">Se l'indirizzo IP remoto non viene trovato, il middleware restituisce HTTP 403 non consentito.</span><span class="sxs-lookup"><span data-stu-id="6ce39-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="6ce39-128">Questo processo di convalida viene ignorato per le richieste HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="6ce39-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="6ce39-129">Filtro azione</span><span class="sxs-lookup"><span data-stu-id="6ce39-129">Action filter</span></span>

<span data-ttu-id="6ce39-130">Se si desidera un controllo di accesso basato su elenchi indirizzi attendibili per controller MVC o metodi di azione specifici, utilizzare un filtro azione.</span><span class="sxs-lookup"><span data-stu-id="6ce39-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="6ce39-131">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="6ce39-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="6ce39-132">In `Startup.ConfigureServices`, aggiungere il filtro azione all'insieme di filtri MVC.</span><span class="sxs-lookup"><span data-stu-id="6ce39-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="6ce39-133">Nell'esempio seguente `ClientIpCheckActionFilter` viene aggiunto un filtro azione.</span><span class="sxs-lookup"><span data-stu-id="6ce39-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="6ce39-134">Un elenco indirizzi attendibili e un'istanza del logger di console vengono passati come parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="6ce39-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="6ce39-135">Il filtro azione può quindi essere applicato a un controller o a un metodo di azione con l'attributo [[ServiceFilter]:The](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) action filter can then be applied to a controller or action method with the [ServiceFilter] attribute:</span><span class="sxs-lookup"><span data-stu-id="6ce39-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="6ce39-136">Nell'app di esempio, il filtro azione `Get` viene applicato al metodo di azione del controller.</span><span class="sxs-lookup"><span data-stu-id="6ce39-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="6ce39-137">Quando si testa l'app inviando:</span><span class="sxs-lookup"><span data-stu-id="6ce39-137">When you test the app by sending:</span></span>

* <span data-ttu-id="6ce39-138">Una richiesta HTTP `[ServiceFilter]` GET, l'attributo convalida l'indirizzo IP del client.</span><span class="sxs-lookup"><span data-stu-id="6ce39-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="6ce39-139">Se l'accesso `Get` al metodo di azione è consentito, una variazione dell'output della console seguente viene prodotta dal filtro azione e dal metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="6ce39-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="6ce39-140">Un verbo di richiesta `AdminSafeListMiddleware` HTTP diverso da GET, il middleware convalida l'indirizzo IP del client.</span><span class="sxs-lookup"><span data-stu-id="6ce39-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="6ce39-141">Filtro Pagine Razor</span><span class="sxs-lookup"><span data-stu-id="6ce39-141">Razor Pages filter</span></span>

<span data-ttu-id="6ce39-142">Se vuoi il controllo dell'accesso basato su elenchi indirizzi attendibili per un'app Razor Pages, usa un filtro Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6ce39-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="6ce39-143">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="6ce39-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="6ce39-144">In `Startup.ConfigureServices`, abilitare il filtro Pagine Razor aggiungendolo all'insieme di filtri MVC.</span><span class="sxs-lookup"><span data-stu-id="6ce39-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="6ce39-145">Nell'esempio seguente `ClientIpCheckPageFilter` viene aggiunto un filtro Razor Pages.In the following example, a Razor Pages filter is added.</span><span class="sxs-lookup"><span data-stu-id="6ce39-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="6ce39-146">Un elenco indirizzi attendibili e un'istanza del logger di console vengono passati come parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="6ce39-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="6ce39-147">Quando viene richiesta la pagina *Razor indice* dell'app di esempio, il filtro Razor Pages convalida l'indirizzo IP del client.</span><span class="sxs-lookup"><span data-stu-id="6ce39-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="6ce39-148">Il filtro produce una variazione del seguente output della console:</span><span class="sxs-lookup"><span data-stu-id="6ce39-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="6ce39-149">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6ce39-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="6ce39-150">Filtri azione</span><span class="sxs-lookup"><span data-stu-id="6ce39-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
