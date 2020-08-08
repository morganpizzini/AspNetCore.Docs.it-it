---
title: Helper tag di cache in ASP.NET Core MVC
author: pkellner
description: Informazioni su come usare l'helper tag di cache.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: 7d2ff774b7654993e2cd9b126db252f81a3032d3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018754"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="47ff4-103">Helper tag di cache in ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="47ff4-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="47ff4-104">Di [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="47ff4-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="47ff4-105">L'helper tag di cache consente di migliorare le prestazioni dell'app ASP.NET Core memorizzandone il contenuto nel provider di cache ASP.NET Core interno.</span><span class="sxs-lookup"><span data-stu-id="47ff4-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="47ff4-106">Per una panoramica degli helper per tag, vedere <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="47ff4-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="47ff4-107">Il Razor markup seguente memorizza nella cache la data corrente:</span><span class="sxs-lookup"><span data-stu-id="47ff4-107">The following Razor markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="47ff4-108">La prima richiesta alla pagina contenente l'helper tag consente di visualizzare la data corrente.</span><span class="sxs-lookup"><span data-stu-id="47ff4-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="47ff4-109">Le richieste aggiuntive mostrano il valore memorizzato nella cache fino alla scadenza della cache (per impostazione predefinita 20 minuti) o fino a quando la data memorizzata nella cache viene rimossa dalla cache.</span><span class="sxs-lookup"><span data-stu-id="47ff4-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="47ff4-110">Attributi dell'helper tag di cache</span><span class="sxs-lookup"><span data-stu-id="47ff4-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="47ff4-111">Enabled</span><span class="sxs-lookup"><span data-stu-id="47ff4-111">enabled</span></span>

| <span data-ttu-id="47ff4-112">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="47ff4-112">Attribute Type</span></span>  | <span data-ttu-id="47ff4-113">Esempi</span><span class="sxs-lookup"><span data-stu-id="47ff4-113">Examples</span></span>        | <span data-ttu-id="47ff4-114">Predefinito</span><span class="sxs-lookup"><span data-stu-id="47ff4-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="47ff4-115">Boolean</span><span class="sxs-lookup"><span data-stu-id="47ff4-115">Boolean</span></span>         | <span data-ttu-id="47ff4-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="47ff4-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="47ff4-117">`enabled` determina se il contenuto incluso nell'helper tag di cache viene memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="47ff4-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="47ff4-118">Il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="47ff4-118">The default is `true`.</span></span> <span data-ttu-id="47ff4-119">Se impostato su `false`, l'output sottoposto a rendering **non** viene memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="47ff4-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="47ff4-120">Esempio:</span><span class="sxs-lookup"><span data-stu-id="47ff4-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="47ff4-121">expires-on</span><span class="sxs-lookup"><span data-stu-id="47ff4-121">expires-on</span></span>

| <span data-ttu-id="47ff4-122">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="47ff4-122">Attribute Type</span></span>   | <span data-ttu-id="47ff4-123">Esempio</span><span class="sxs-lookup"><span data-stu-id="47ff4-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="47ff4-124">`expires-on` imposta una data di scadenza assoluta per l'elemento memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="47ff4-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="47ff4-125">Il codice di esempio seguente memorizza nella cache il contenuto dell'helper tag di cache fino alle 17:02 del 29 gennaio 2025:</span><span class="sxs-lookup"><span data-stu-id="47ff4-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="47ff4-126">expires-after</span><span class="sxs-lookup"><span data-stu-id="47ff4-126">expires-after</span></span>

| <span data-ttu-id="47ff4-127">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="47ff4-127">Attribute Type</span></span> | <span data-ttu-id="47ff4-128">Esempio</span><span class="sxs-lookup"><span data-stu-id="47ff4-128">Example</span></span>                      | <span data-ttu-id="47ff4-129">Impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="47ff4-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="47ff4-130">20 minuti</span><span class="sxs-lookup"><span data-stu-id="47ff4-130">20 minutes</span></span> |

<span data-ttu-id="47ff4-131">`expires-after` imposta il tempo di memorizzazione del contenuto nella cache a partire dalla prima richiesta.</span><span class="sxs-lookup"><span data-stu-id="47ff4-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="47ff4-132">Esempio:</span><span class="sxs-lookup"><span data-stu-id="47ff4-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="47ff4-133">Il Razor motore di visualizzazione imposta il `expires-after` valore predefinito su venti minuti.</span><span class="sxs-lookup"><span data-stu-id="47ff4-133">The Razor View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="47ff4-134">expires-sliding</span><span class="sxs-lookup"><span data-stu-id="47ff4-134">expires-sliding</span></span>

| <span data-ttu-id="47ff4-135">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="47ff4-135">Attribute Type</span></span> | <span data-ttu-id="47ff4-136">Esempio</span><span class="sxs-lookup"><span data-stu-id="47ff4-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="47ff4-137">Imposta il tempo trascorso il quale un elemento memorizzato nella cache viene rimosso se non è stato usato.</span><span class="sxs-lookup"><span data-stu-id="47ff4-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="47ff4-138">Esempio:</span><span class="sxs-lookup"><span data-stu-id="47ff4-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="47ff4-139">vary-by-header</span><span class="sxs-lookup"><span data-stu-id="47ff4-139">vary-by-header</span></span>

| <span data-ttu-id="47ff4-140">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="47ff4-140">Attribute Type</span></span> | <span data-ttu-id="47ff4-141">Esempi</span><span class="sxs-lookup"><span data-stu-id="47ff4-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="47ff4-142">string</span><span class="sxs-lookup"><span data-stu-id="47ff4-142">String</span></span>         | <span data-ttu-id="47ff4-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="47ff4-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="47ff4-144">`vary-by-header` accetta un elenco delimitato da virgole di valori di intestazione che attivano un aggiornamento della cache quando vengono modificati.</span><span class="sxs-lookup"><span data-stu-id="47ff4-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="47ff4-145">L'esempio seguente esegue il monitoraggio del valore dell'intestazione `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="47ff4-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="47ff4-146">Il codice dell'esempio memorizza nella cache il contenuto di ogni singolo elemento `User-Agent` presentato al server Web:</span><span class="sxs-lookup"><span data-stu-id="47ff4-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="47ff4-147">vary-by-query</span><span class="sxs-lookup"><span data-stu-id="47ff4-147">vary-by-query</span></span>

| <span data-ttu-id="47ff4-148">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="47ff4-148">Attribute Type</span></span> | <span data-ttu-id="47ff4-149">Esempi</span><span class="sxs-lookup"><span data-stu-id="47ff4-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="47ff4-150">string</span><span class="sxs-lookup"><span data-stu-id="47ff4-150">String</span></span>         | <span data-ttu-id="47ff4-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="47ff4-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="47ff4-152">`vary-by-query` accetta un elenco delimitato da virgole di <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in una stringa di query (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) che attiva un aggiornamento della cache quando cambia il valore di qualsiasi chiave inclusa nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="47ff4-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="47ff4-153">L'esempio seguente esegue il monitoraggio dei valori di `Make` e `Model`.</span><span class="sxs-lookup"><span data-stu-id="47ff4-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="47ff4-154">L'esempio memorizza nella cache il contenuto di ogni singolo valore `Make` e `Model` presentato al server Web:</span><span class="sxs-lookup"><span data-stu-id="47ff4-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="47ff4-155">vary-by-route</span><span class="sxs-lookup"><span data-stu-id="47ff4-155">vary-by-route</span></span>

| <span data-ttu-id="47ff4-156">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="47ff4-156">Attribute Type</span></span> | <span data-ttu-id="47ff4-157">Esempi</span><span class="sxs-lookup"><span data-stu-id="47ff4-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="47ff4-158">string</span><span class="sxs-lookup"><span data-stu-id="47ff4-158">String</span></span>         | <span data-ttu-id="47ff4-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="47ff4-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="47ff4-160">`vary-by-route` accetta un elenco delimitato da virgole di nomi di parametri di route che attivano un aggiornamento della cache quando cambia il valore del parametro dei dati di route.</span><span class="sxs-lookup"><span data-stu-id="47ff4-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="47ff4-161">Esempio:</span><span class="sxs-lookup"><span data-stu-id="47ff4-161">Example:</span></span>

<span data-ttu-id="47ff4-162">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="47ff4-162">*Startup.cs*:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="47ff4-163">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="47ff4-163">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-no-loccookie"></a><span data-ttu-id="47ff4-164">variazione percookie</span><span class="sxs-lookup"><span data-stu-id="47ff4-164">vary-by-cookie</span></span>

| <span data-ttu-id="47ff4-165">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="47ff4-165">Attribute Type</span></span> | <span data-ttu-id="47ff4-166">Esempi</span><span class="sxs-lookup"><span data-stu-id="47ff4-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="47ff4-167">string</span><span class="sxs-lookup"><span data-stu-id="47ff4-167">String</span></span>         | <span data-ttu-id="47ff4-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="47ff4-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span></span> |

<span data-ttu-id="47ff4-169">`vary-by-cookie`accetta un elenco delimitato da virgole di cookie nomi che attivano un aggiornamento della cache quando cookie cambiano i valori.</span><span class="sxs-lookup"><span data-stu-id="47ff4-169">`vary-by-cookie` accepts a comma-delimited list of cookie names that trigger a cache refresh when the cookie values change.</span></span>

<span data-ttu-id="47ff4-170">Nell'esempio seguente viene monitorato l'oggetto cookie associato a ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="47ff4-170">The following example monitors the cookie associated with ASP.NET Core Identity.</span></span> <span data-ttu-id="47ff4-171">Quando un utente viene autenticato, una modifica in Identity cookie attiva un aggiornamento della cache:</span><span class="sxs-lookup"><span data-stu-id="47ff4-171">When a user is authenticated, a change in the Identity cookie triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="47ff4-172">vary-by-user</span><span class="sxs-lookup"><span data-stu-id="47ff4-172">vary-by-user</span></span>

| <span data-ttu-id="47ff4-173">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="47ff4-173">Attribute Type</span></span>  | <span data-ttu-id="47ff4-174">Esempi</span><span class="sxs-lookup"><span data-stu-id="47ff4-174">Examples</span></span>        | <span data-ttu-id="47ff4-175">Predefinito</span><span class="sxs-lookup"><span data-stu-id="47ff4-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="47ff4-176">Boolean</span><span class="sxs-lookup"><span data-stu-id="47ff4-176">Boolean</span></span>         | <span data-ttu-id="47ff4-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="47ff4-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="47ff4-178">`vary-by-user` specifica se la cache deve essere o meno reimpostata quando cambia l'utente connesso (o l'entità di contesto connessa).</span><span class="sxs-lookup"><span data-stu-id="47ff4-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="47ff4-179">L'utente corrente è noto anche come entità del contesto della richiesta e può essere visualizzato in una Razor vista facendo riferimento a `@User.Identity.Name` .</span><span class="sxs-lookup"><span data-stu-id="47ff4-179">The current user is also known as the Request Context Principal and can be viewed in a Razor view by referencing `@User.Identity.Name`.</span></span>

<span data-ttu-id="47ff4-180">L'esempio seguente esegue il monitoraggio dell'utente connesso corrente per attivare un aggiornamento della cache:</span><span class="sxs-lookup"><span data-stu-id="47ff4-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="47ff4-181">Questo attributo consente di mantenere il contenuto nella cache durante un ciclo di accesso e disconnessione.</span><span class="sxs-lookup"><span data-stu-id="47ff4-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="47ff4-182">Quando il valore è impostato su `true`, un ciclo di autenticazione invalida la cache per l'utente autenticato.</span><span class="sxs-lookup"><span data-stu-id="47ff4-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="47ff4-183">La cache viene invalidata perché cookie viene generato un nuovo valore univoco quando un utente viene autenticato.</span><span class="sxs-lookup"><span data-stu-id="47ff4-183">The cache is invalidated because a new unique cookie value is generated when a user is authenticated.</span></span> <span data-ttu-id="47ff4-184">La cache viene mantenuta per lo stato anonimo se non cookie è presente o è cookie scaduto.</span><span class="sxs-lookup"><span data-stu-id="47ff4-184">Cache is maintained for the anonymous state when no cookie is present or the cookie has expired.</span></span> <span data-ttu-id="47ff4-185">Se l'utente **non** viene autenticato, la cache viene mantenuta.</span><span class="sxs-lookup"><span data-stu-id="47ff4-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="47ff4-186">vary-by</span><span class="sxs-lookup"><span data-stu-id="47ff4-186">vary-by</span></span>

| <span data-ttu-id="47ff4-187">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="47ff4-187">Attribute Type</span></span> | <span data-ttu-id="47ff4-188">Esempio</span><span class="sxs-lookup"><span data-stu-id="47ff4-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="47ff4-189">string</span><span class="sxs-lookup"><span data-stu-id="47ff4-189">String</span></span>         | `@Model` |

<span data-ttu-id="47ff4-190">`vary-by` consente di personalizzare quali dati vengono memorizzati nella cache.</span><span class="sxs-lookup"><span data-stu-id="47ff4-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="47ff4-191">Quando l'oggetto al quale fa riferimento il valore stringa dell'attributo cambia, il contenuto dell'helper tag di cache viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="47ff4-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="47ff4-192">Spesso a questo attributo viene assegnata una concatenazione stringa di valori del modello.</span><span class="sxs-lookup"><span data-stu-id="47ff4-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="47ff4-193">In effetti, da ciò risulta uno scenario in cui un aggiornamento di uno qualsiasi dei valori concatenati invalida la cache.</span><span class="sxs-lookup"><span data-stu-id="47ff4-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="47ff4-194">L'esempio seguente presuppone che il metodo del controller che esegue il rendering della visualizzazione effettui la somma dei valori interi dei due parametri di route, `myParam1` e `myParam2`, e restituisca la somma come proprietà singola del modello.</span><span class="sxs-lookup"><span data-stu-id="47ff4-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="47ff4-195">Quando questa somma cambia, il contenuto dell'helper tag di cache viene sottoposto a rendering e memorizzato di nuovo nella cache.</span><span class="sxs-lookup"><span data-stu-id="47ff4-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="47ff4-196">Azione:</span><span class="sxs-lookup"><span data-stu-id="47ff4-196">Action:</span></span>

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

<span data-ttu-id="47ff4-197">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="47ff4-197">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="47ff4-198">priority</span><span class="sxs-lookup"><span data-stu-id="47ff4-198">priority</span></span>

| <span data-ttu-id="47ff4-199">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="47ff4-199">Attribute Type</span></span>      | <span data-ttu-id="47ff4-200">Esempi</span><span class="sxs-lookup"><span data-stu-id="47ff4-200">Examples</span></span>                               | <span data-ttu-id="47ff4-201">Predefinito</span><span class="sxs-lookup"><span data-stu-id="47ff4-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="47ff4-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="47ff4-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="47ff4-203">`priority` offre indicazioni per la rimozione dalla cache al provider di cache predefinito.</span><span class="sxs-lookup"><span data-stu-id="47ff4-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="47ff4-204">In condizioni di utilizzo elevato della memoria, il server Web rimuove per prime le voci della cache `Low`.</span><span class="sxs-lookup"><span data-stu-id="47ff4-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="47ff4-205">Esempio:</span><span class="sxs-lookup"><span data-stu-id="47ff4-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="47ff4-206">L'attributo `priority` non garantisce un livello specifico di mantenimento nella cache.</span><span class="sxs-lookup"><span data-stu-id="47ff4-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="47ff4-207">`CacheItemPriority` è un semplice suggerimento.</span><span class="sxs-lookup"><span data-stu-id="47ff4-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="47ff4-208">L'impostazione di questo attributo su `NeverRemove` non garantisce che gli elementi memorizzati nella cache vengano sempre mantenuti.</span><span class="sxs-lookup"><span data-stu-id="47ff4-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="47ff4-209">Per altre informazioni, vedere gli argomenti nella sezione [Risorse aggiuntive](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="47ff4-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="47ff4-210">L'helper tag di cache dipende dal [servizio cache in memoria](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="47ff4-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="47ff4-211">L'helper tag di cache aggiunge il servizio se non è stato aggiunto.</span><span class="sxs-lookup"><span data-stu-id="47ff4-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="47ff4-212">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="47ff4-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
