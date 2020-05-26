---
<span data-ttu-id="e7b02-101">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-102">'Blazor'</span></span>
- <span data-ttu-id="e7b02-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-103">'Identity'</span></span>
- <span data-ttu-id="e7b02-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-105">'Razor'</span></span>
- <span data-ttu-id="e7b02-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="e7b02-107">JSON Patch nell'API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7b02-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="e7b02-108">Di [Tom Dykstra](https://github.com/tdykstra) e [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="e7b02-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7b02-109">Questo articolo descrive come gestire le richieste JSON Patch in un'API Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e7b02-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="e7b02-110">Installazione del pacchetto</span><span class="sxs-lookup"><span data-stu-id="e7b02-110">Package installation</span></span>

<span data-ttu-id="e7b02-111">Per abilitare il supporto della patch JSON nell'app, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="e7b02-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="e7b02-112">Installare il [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="e7b02-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="e7b02-113">Aggiornare il metodo del progetto `Startup.ConfigureServices` in modo da chiamare <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="e7b02-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="e7b02-114">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e7b02-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="e7b02-115">`AddNewtonsoftJson`è compatibile con i metodi di registrazione del servizio MVC:</span><span class="sxs-lookup"><span data-stu-id="e7b02-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="e7b02-116">Patch JSON, AddNewtonsoftJson e System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="e7b02-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="e7b02-117">`AddNewtonsoftJson`sostituisce i `System.Text.Json` formattatori di input e output basati su usati per formattare **tutto** il contenuto JSON.</span><span class="sxs-lookup"><span data-stu-id="e7b02-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="e7b02-118">Per aggiungere il supporto per la patch JSON usando `Newtonsoft.Json` , lasciando invariati gli altri formattatori, aggiornare il metodo del progetto `Startup.ConfigureServices` come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="e7b02-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="e7b02-119">Il codice precedente richiede il `Microsoft.AspNetCore.Mvc.NewtonsoftJson` pacchetto e le `using` istruzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="e7b02-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="e7b02-120">Metodo di richiesta HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="e7b02-120">PATCH HTTP request method</span></span>

<span data-ttu-id="e7b02-121">I metodi PUT e [PATCH](https://tools.ietf.org/html/rfc5789) vengono usati per aggiornare una risorsa esistente.</span><span class="sxs-lookup"><span data-stu-id="e7b02-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="e7b02-122">La differenza tra i due metodi è che PUT sostituisce l'intera risorsa, mentre PATCH specifica solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="e7b02-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="e7b02-123">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="e7b02-123">JSON Patch</span></span>

<span data-ttu-id="e7b02-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) è un formato per specificare gli aggiornamenti da applicare a una risorsa.</span><span class="sxs-lookup"><span data-stu-id="e7b02-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="e7b02-125">Un documento JSON Patch è una matrice di *operazioni*.</span><span class="sxs-lookup"><span data-stu-id="e7b02-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="e7b02-126">Ogni operazione identifica un tipo specifico di modifica.</span><span class="sxs-lookup"><span data-stu-id="e7b02-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="e7b02-127">Esempi di tali modifiche includono l'aggiunta di un elemento di matrice o la sostituzione di un valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="e7b02-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="e7b02-128">Ad esempio, i documenti JSON seguenti rappresentano una risorsa, un documento di patch JSON per la risorsa e il risultato dell'applicazione delle operazioni patch.</span><span class="sxs-lookup"><span data-stu-id="e7b02-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="e7b02-129">Esempio di risorsa</span><span class="sxs-lookup"><span data-stu-id="e7b02-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="e7b02-130">Esempio di patch JSON</span><span class="sxs-lookup"><span data-stu-id="e7b02-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="e7b02-131">Nel codice JSON precedente:</span><span class="sxs-lookup"><span data-stu-id="e7b02-131">In the preceding JSON:</span></span>

* <span data-ttu-id="e7b02-132">La proprietà `op` indica il tipo di operazione.</span><span class="sxs-lookup"><span data-stu-id="e7b02-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="e7b02-133">La proprietà `path` indica l'elemento da aggiornare.</span><span class="sxs-lookup"><span data-stu-id="e7b02-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="e7b02-134">La proprietà `value` fornisce il nuovo valore.</span><span class="sxs-lookup"><span data-stu-id="e7b02-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="e7b02-135">Risorsa dopo la patch</span><span class="sxs-lookup"><span data-stu-id="e7b02-135">Resource after patch</span></span>

<span data-ttu-id="e7b02-136">Ecco la risorsa dopo aver applicato il documento JSON Patch precedente:</span><span class="sxs-lookup"><span data-stu-id="e7b02-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="e7b02-137">Le modifiche apportate applicando un documento Patch JSON a una risorsa sono atomiche.</span><span class="sxs-lookup"><span data-stu-id="e7b02-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="e7b02-138">Se un'operazione nell'elenco ha esito negativo, non viene applicata alcuna operazione nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="e7b02-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="e7b02-139">Sintassi di path</span><span class="sxs-lookup"><span data-stu-id="e7b02-139">Path syntax</span></span>

<span data-ttu-id="e7b02-140">La proprietà [path](https://tools.ietf.org/html/rfc6901) di un oggetto operazione include barre tra livelli.</span><span class="sxs-lookup"><span data-stu-id="e7b02-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="e7b02-141">Ad esempio: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="e7b02-142">Vengono usati indici in base zero per specificare gli elementi della matrice.</span><span class="sxs-lookup"><span data-stu-id="e7b02-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="e7b02-143">Il primo elemento della matrice `addresses` è in corrispondenza di `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="e7b02-144">Fino alla `add` fine di una matrice, usare un trattino ( `-` ) invece di un numero di indice: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="e7b02-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="e7b02-145">Gestione operativa</span><span class="sxs-lookup"><span data-stu-id="e7b02-145">Operations</span></span>

<span data-ttu-id="e7b02-146">La tabella seguente mostra le operazioni supportate in base a quanto definito nella [specifica JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="e7b02-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="e7b02-147">Operazione</span><span class="sxs-lookup"><span data-stu-id="e7b02-147">Operation</span></span>  | <span data-ttu-id="e7b02-148">Note</span><span class="sxs-lookup"><span data-stu-id="e7b02-148">Notes</span></span> |
|---
<span data-ttu-id="e7b02-149">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-150">'Blazor'</span></span>
- <span data-ttu-id="e7b02-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-151">'Identity'</span></span>
- <span data-ttu-id="e7b02-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-153">'Razor'</span></span>
- <span data-ttu-id="e7b02-154">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-155">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-156">'Blazor'</span></span>
- <span data-ttu-id="e7b02-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-157">'Identity'</span></span>
- <span data-ttu-id="e7b02-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-159">'Razor'</span></span>
- <span data-ttu-id="e7b02-160">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-161">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-162">'Blazor'</span></span>
- <span data-ttu-id="e7b02-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-163">'Identity'</span></span>
- <span data-ttu-id="e7b02-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-165">'Razor'</span></span>
- <span data-ttu-id="e7b02-166">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-166">'SignalR' uid:</span></span> 

<span data-ttu-id="e7b02-167">------|---
title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-168">'Blazor'</span></span>
- <span data-ttu-id="e7b02-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-169">'Identity'</span></span>
- <span data-ttu-id="e7b02-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-171">'Razor'</span></span>
- <span data-ttu-id="e7b02-172">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-173">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-174">'Blazor'</span></span>
- <span data-ttu-id="e7b02-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-175">'Identity'</span></span>
- <span data-ttu-id="e7b02-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-177">'Razor'</span></span>
- <span data-ttu-id="e7b02-178">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-179">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-180">'Blazor'</span></span>
- <span data-ttu-id="e7b02-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-181">'Identity'</span></span>
- <span data-ttu-id="e7b02-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-183">'Razor'</span></span>
- <span data-ttu-id="e7b02-184">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-185">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-186">'Blazor'</span></span>
- <span data-ttu-id="e7b02-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-187">'Identity'</span></span>
- <span data-ttu-id="e7b02-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-189">'Razor'</span></span>
- <span data-ttu-id="e7b02-190">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-191">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-192">'Blazor'</span></span>
- <span data-ttu-id="e7b02-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-193">'Identity'</span></span>
- <span data-ttu-id="e7b02-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-195">'Razor'</span></span>
- <span data-ttu-id="e7b02-196">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-197">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-198">'Blazor'</span></span>
- <span data-ttu-id="e7b02-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-199">'Identity'</span></span>
- <span data-ttu-id="e7b02-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-201">'Razor'</span></span>
- <span data-ttu-id="e7b02-202">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-203">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-204">'Blazor'</span></span>
- <span data-ttu-id="e7b02-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-205">'Identity'</span></span>
- <span data-ttu-id="e7b02-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-207">'Razor'</span></span>
- <span data-ttu-id="e7b02-208">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-209">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-210">'Blazor'</span></span>
- <span data-ttu-id="e7b02-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-211">'Identity'</span></span>
- <span data-ttu-id="e7b02-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-213">'Razor'</span></span>
- <span data-ttu-id="e7b02-214">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-215">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-216">'Blazor'</span></span>
- <span data-ttu-id="e7b02-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-217">'Identity'</span></span>
- <span data-ttu-id="e7b02-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-219">'Razor'</span></span>
- <span data-ttu-id="e7b02-220">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-221">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-222">'Blazor'</span></span>
- <span data-ttu-id="e7b02-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-223">'Identity'</span></span>
- <span data-ttu-id="e7b02-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-225">'Razor'</span></span>
- <span data-ttu-id="e7b02-226">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-227">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-228">'Blazor'</span></span>
- <span data-ttu-id="e7b02-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-229">'Identity'</span></span>
- <span data-ttu-id="e7b02-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-231">'Razor'</span></span>
- <span data-ttu-id="e7b02-232">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-233">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-234">'Blazor'</span></span>
- <span data-ttu-id="e7b02-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-235">'Identity'</span></span>
- <span data-ttu-id="e7b02-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-237">'Razor'</span></span>
- <span data-ttu-id="e7b02-238">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-239">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-240">'Blazor'</span></span>
- <span data-ttu-id="e7b02-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-241">'Identity'</span></span>
- <span data-ttu-id="e7b02-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-243">'Razor'</span></span>
- <span data-ttu-id="e7b02-244">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-245">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-246">'Blazor'</span></span>
- <span data-ttu-id="e7b02-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-247">'Identity'</span></span>
- <span data-ttu-id="e7b02-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-249">'Razor'</span></span>
- <span data-ttu-id="e7b02-250">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-250">'SignalR' uid:</span></span> 

<span data-ttu-id="e7b02-251">----------------| | `add`     | Aggiungere una proprietà o un elemento di matrice.</span><span class="sxs-lookup"><span data-stu-id="e7b02-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="e7b02-252">Per la proprietà esistente: impostare Value. | | `remove`  | Rimuovere una proprietà o un elemento di matrice.</span><span class="sxs-lookup"><span data-stu-id="e7b02-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="e7b02-253">| | `replace` | Uguale `remove` a seguito dallo `add` stesso percorso.</span><span class="sxs-lookup"><span data-stu-id="e7b02-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="e7b02-254">| | `move`    | Uguale a `remove` dall'origine seguito da `add` alla destinazione usando il valore dell'origine.</span><span class="sxs-lookup"><span data-stu-id="e7b02-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="e7b02-255">| | `copy`    | Uguale `add` a per la destinazione usando il valore dell'origine.</span><span class="sxs-lookup"><span data-stu-id="e7b02-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="e7b02-256">| | `test`    | Restituisce il codice di stato di esito positivo se value at `path` = fornito `value` . |</span><span class="sxs-lookup"><span data-stu-id="e7b02-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="e7b02-257">Patch JSON in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7b02-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="e7b02-258">L'implementazione ASP.NET Core di JSON Patch viene fornita nel pacchetto NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="e7b02-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="e7b02-259">Codice del metodo di azione</span><span class="sxs-lookup"><span data-stu-id="e7b02-259">Action method code</span></span>

<span data-ttu-id="e7b02-260">In un controller API un metodo di azione per JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="e7b02-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="e7b02-261">Viene annotato con l'attributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="e7b02-262">Accetta un oggetto `JsonPatchDocument<T>` , in genere con `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="e7b02-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="e7b02-263">Chiama `ApplyTo` nel documento di patch per applicare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="e7b02-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="e7b02-264">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="e7b02-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="e7b02-265">Questo codice dall'app di esempio funziona con il `Customer` modello seguente:</span><span class="sxs-lookup"><span data-stu-id="e7b02-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="e7b02-266">Il metodo di azione di esempio:</span><span class="sxs-lookup"><span data-stu-id="e7b02-266">The sample action method:</span></span>

* <span data-ttu-id="e7b02-267">Costruisce un oggetto `Customer`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="e7b02-268">Applica la patch.</span><span class="sxs-lookup"><span data-stu-id="e7b02-268">Applies the patch.</span></span>
* <span data-ttu-id="e7b02-269">Restituisce il risultato nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="e7b02-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="e7b02-270">In un'app reale il codice recupererebbe i dati da un archivio, ad esempio un database, e aggiornerebbe il database dopo aver applicato la patch.</span><span class="sxs-lookup"><span data-stu-id="e7b02-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="e7b02-271">Stato del modello</span><span class="sxs-lookup"><span data-stu-id="e7b02-271">Model state</span></span>

<span data-ttu-id="e7b02-272">L'esempio di metodo di azione precedente chiama un overload di `ApplyTo` che accetta lo stato del modello come uno dei propri parametri.</span><span class="sxs-lookup"><span data-stu-id="e7b02-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="e7b02-273">Con questa opzione, è possibile ottenere messaggi di errore nelle risposte.</span><span class="sxs-lookup"><span data-stu-id="e7b02-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="e7b02-274">L'esempio seguente mostra il corpo di una risposta 400 Richiesta non valida per un'operazione `test`:</span><span class="sxs-lookup"><span data-stu-id="e7b02-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="e7b02-275">Oggetti dinamici</span><span class="sxs-lookup"><span data-stu-id="e7b02-275">Dynamic objects</span></span>

<span data-ttu-id="e7b02-276">Nell'esempio di metodo di azione seguente viene illustrato come applicare una patch a un oggetto dinamico:</span><span class="sxs-lookup"><span data-stu-id="e7b02-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="e7b02-277">Operazione add</span><span class="sxs-lookup"><span data-stu-id="e7b02-277">The add operation</span></span>

* <span data-ttu-id="e7b02-278">Se `path` punta a un elemento della matrice: inserisce un nuovo elemento prima di quello specificato da `path`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="e7b02-279">Se `path` punta a una proprietà: imposta il valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="e7b02-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="e7b02-280">Se `path` punta a una posizione che non esiste:</span><span class="sxs-lookup"><span data-stu-id="e7b02-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="e7b02-281">Se la risorsa cui applicare la patch è un oggetto dinamico: aggiunge una proprietà.</span><span class="sxs-lookup"><span data-stu-id="e7b02-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="e7b02-282">Se la risorsa cui applicare la patch è un oggetto statico: la richiesta non riesce.</span><span class="sxs-lookup"><span data-stu-id="e7b02-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="e7b02-283">Il documento di patch di esempio seguente imposta il valore di `CustomerName` e aggiunge un oggetto `Order` alla fine della matrice `Orders`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="e7b02-284">Operazione remove</span><span class="sxs-lookup"><span data-stu-id="e7b02-284">The remove operation</span></span>

* <span data-ttu-id="e7b02-285">Se `path` punta a un elemento della matrice: rimuove l'elemento.</span><span class="sxs-lookup"><span data-stu-id="e7b02-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="e7b02-286">Se `path` punta a una proprietà:</span><span class="sxs-lookup"><span data-stu-id="e7b02-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="e7b02-287">Se la risorsa cui applicare la patch è un oggetto dinamico: rimuove la proprietà.</span><span class="sxs-lookup"><span data-stu-id="e7b02-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="e7b02-288">Se la risorsa cui applicare la patch è un oggetto statico:</span><span class="sxs-lookup"><span data-stu-id="e7b02-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="e7b02-289">Se la proprietà ammette valori Null: la imposta su Null.</span><span class="sxs-lookup"><span data-stu-id="e7b02-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="e7b02-290">Se la proprietà non ammette valori Null: la imposta su `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="e7b02-291">Il documento di patch di esempio seguente imposta `CustomerName` su null ed Elimina `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="e7b02-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="e7b02-292">Operazione replace</span><span class="sxs-lookup"><span data-stu-id="e7b02-292">The replace operation</span></span>

<span data-ttu-id="e7b02-293">Questa operazione equivale a livello funzionale all'operazione `remove` seguita da un'operazione `add`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="e7b02-294">Nel documento della patch di esempio seguente viene impostato il valore di `CustomerName` e viene sostituito `Orders[0]` con un nuovo `Order` oggetto:</span><span class="sxs-lookup"><span data-stu-id="e7b02-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="e7b02-295">Operazione move</span><span class="sxs-lookup"><span data-stu-id="e7b02-295">The move operation</span></span>

* <span data-ttu-id="e7b02-296">Se `path` punta a un elemento della matrice: copia l'elemento `from` nella posizione dell'elemento `path`, quindi esegue un'operazione `remove` sull'elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="e7b02-297">Se `path` punta a una proprietà: copia il valore della proprietà `from` nella proprietà `path`, quindi esegue un'operazione `remove` sulla proprietà `from`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="e7b02-298">Se `path` punta a una proprietà che non esiste:</span><span class="sxs-lookup"><span data-stu-id="e7b02-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="e7b02-299">Se la risorsa cui applicare la patch è un oggetto statico: la richiesta non riesce.</span><span class="sxs-lookup"><span data-stu-id="e7b02-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="e7b02-300">Se la risorsa cui applicare la patch è un oggetto dinamico: copia la proprietà `from` nella posizione indicata da `path`, quindi esegue un'operazione `remove` sulla proprietà `from`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="e7b02-301">Il documento di patch di esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="e7b02-301">The following sample patch document:</span></span>

* <span data-ttu-id="e7b02-302">Copia il valore di `Orders[0].OrderName` in `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="e7b02-303">Imposta `Orders[0].OrderName` su Null.</span><span class="sxs-lookup"><span data-stu-id="e7b02-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="e7b02-304">Sposta `Orders[1]` prima di `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="e7b02-305">Operazione copy</span><span class="sxs-lookup"><span data-stu-id="e7b02-305">The copy operation</span></span>

<span data-ttu-id="e7b02-306">Questa operazione equivale a livello funzionale all'operazione `move` senza il passaggio `remove` finale.</span><span class="sxs-lookup"><span data-stu-id="e7b02-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="e7b02-307">Il documento di patch di esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="e7b02-307">The following sample patch document:</span></span>

* <span data-ttu-id="e7b02-308">Copia il valore di `Orders[0].OrderName` in `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="e7b02-309">Inserisce una copia di `Orders[1]` prima di `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="e7b02-310">Operazione test</span><span class="sxs-lookup"><span data-stu-id="e7b02-310">The test operation</span></span>

<span data-ttu-id="e7b02-311">Se il valore nella posizione indicata da `path` è diverso dal valore fornito in `value`, la richiesta non riesce.</span><span class="sxs-lookup"><span data-stu-id="e7b02-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="e7b02-312">In questo caso, l'intera richiesta PATCH non riesce anche se tutte le altre operazioni nel documento di patch potrebbero riuscire.</span><span class="sxs-lookup"><span data-stu-id="e7b02-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="e7b02-313">L'operazione `test` viene usata in genere per impedire un aggiornamento in caso di conflitto di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="e7b02-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="e7b02-314">Il documento di patch di esempio seguente non ha alcun effetto se il valore iniziale di `CustomerName` è "John", perché il test non riesce:</span><span class="sxs-lookup"><span data-stu-id="e7b02-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="e7b02-315">Ottenere il codice</span><span class="sxs-lookup"><span data-stu-id="e7b02-315">Get the code</span></span>

<span data-ttu-id="e7b02-316">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="e7b02-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="e7b02-317">([Come scaricare](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e7b02-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="e7b02-318">Per testare l'esempio, eseguire l'app e inviare richieste HTTP con le impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="e7b02-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="e7b02-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="e7b02-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="e7b02-320">Metodo HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="e7b02-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="e7b02-321">Intestazione: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="e7b02-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="e7b02-322">Body: copiare e incollare uno degli esempi di documenti patch JSON dalla cartella del progetto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="e7b02-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7b02-323">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e7b02-323">Additional resources</span></span>

* [<span data-ttu-id="e7b02-324">Specifica del metodo PATCH IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="e7b02-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="e7b02-325">Specifica JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="e7b02-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="e7b02-326">Specifica del formato di percorso JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="e7b02-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="e7b02-327">[Documentazione di JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="e7b02-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="e7b02-328">Include collegamenti a risorse per la creazione di documenti JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="e7b02-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="e7b02-329">Codice sorgente JSON Patch in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7b02-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e7b02-330">Questo articolo descrive come gestire le richieste JSON Patch in un'API Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e7b02-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="e7b02-331">Metodo di richiesta HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="e7b02-331">PATCH HTTP request method</span></span>

<span data-ttu-id="e7b02-332">I metodi PUT e [PATCH](https://tools.ietf.org/html/rfc5789) vengono usati per aggiornare una risorsa esistente.</span><span class="sxs-lookup"><span data-stu-id="e7b02-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="e7b02-333">La differenza tra i due metodi è che PUT sostituisce l'intera risorsa, mentre PATCH specifica solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="e7b02-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="e7b02-334">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="e7b02-334">JSON Patch</span></span>

<span data-ttu-id="e7b02-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) è un formato per specificare gli aggiornamenti da applicare a una risorsa.</span><span class="sxs-lookup"><span data-stu-id="e7b02-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="e7b02-336">Un documento JSON Patch è una matrice di *operazioni*.</span><span class="sxs-lookup"><span data-stu-id="e7b02-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="e7b02-337">Ogni operazione identifica un determinato tipo di modifica, ad esempio l'aggiunta di un elemento della matrice o la sostituzione di un valore di proprietà.</span><span class="sxs-lookup"><span data-stu-id="e7b02-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="e7b02-338">Ad esempio, i documenti JSON seguenti rappresentano una risorsa, un documento di patch JSON per la risorsa e il risultato dell'applicazione delle operazioni patch.</span><span class="sxs-lookup"><span data-stu-id="e7b02-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="e7b02-339">Esempio di risorsa</span><span class="sxs-lookup"><span data-stu-id="e7b02-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="e7b02-340">Esempio di patch JSON</span><span class="sxs-lookup"><span data-stu-id="e7b02-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="e7b02-341">Nel codice JSON precedente:</span><span class="sxs-lookup"><span data-stu-id="e7b02-341">In the preceding JSON:</span></span>

* <span data-ttu-id="e7b02-342">La proprietà `op` indica il tipo di operazione.</span><span class="sxs-lookup"><span data-stu-id="e7b02-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="e7b02-343">La proprietà `path` indica l'elemento da aggiornare.</span><span class="sxs-lookup"><span data-stu-id="e7b02-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="e7b02-344">La proprietà `value` fornisce il nuovo valore.</span><span class="sxs-lookup"><span data-stu-id="e7b02-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="e7b02-345">Risorsa dopo la patch</span><span class="sxs-lookup"><span data-stu-id="e7b02-345">Resource after patch</span></span>

<span data-ttu-id="e7b02-346">Ecco la risorsa dopo aver applicato il documento JSON Patch precedente:</span><span class="sxs-lookup"><span data-stu-id="e7b02-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="e7b02-347">Le modifiche apportate tramite l'applicazione di un documento JSON Patch a una risorsa sono atomiche: se qualsiasi operazione nell'elenco non riesce, non viene applicata alcuna operazione contenuta nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="e7b02-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="e7b02-348">Sintassi di path</span><span class="sxs-lookup"><span data-stu-id="e7b02-348">Path syntax</span></span>

<span data-ttu-id="e7b02-349">La proprietà [path](https://tools.ietf.org/html/rfc6901) di un oggetto operazione include barre tra livelli.</span><span class="sxs-lookup"><span data-stu-id="e7b02-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="e7b02-350">Ad esempio: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="e7b02-351">Vengono usati indici in base zero per specificare gli elementi della matrice.</span><span class="sxs-lookup"><span data-stu-id="e7b02-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="e7b02-352">Il primo elemento della matrice `addresses` è in corrispondenza di `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="e7b02-353">Per aggiungere (`add`) un elemento alla fine della matrice, usare un trattino (-) invece di un numero di indice: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="e7b02-354">Gestione operativa</span><span class="sxs-lookup"><span data-stu-id="e7b02-354">Operations</span></span>

<span data-ttu-id="e7b02-355">La tabella seguente mostra le operazioni supportate in base a quanto definito nella [specifica JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="e7b02-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="e7b02-356">Operazione</span><span class="sxs-lookup"><span data-stu-id="e7b02-356">Operation</span></span>  | <span data-ttu-id="e7b02-357">Note</span><span class="sxs-lookup"><span data-stu-id="e7b02-357">Notes</span></span> |
|---
<span data-ttu-id="e7b02-358">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-359">'Blazor'</span></span>
- <span data-ttu-id="e7b02-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-360">'Identity'</span></span>
- <span data-ttu-id="e7b02-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-362">'Razor'</span></span>
- <span data-ttu-id="e7b02-363">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-364">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-365">'Blazor'</span></span>
- <span data-ttu-id="e7b02-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-366">'Identity'</span></span>
- <span data-ttu-id="e7b02-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-368">'Razor'</span></span>
- <span data-ttu-id="e7b02-369">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-370">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-371">'Blazor'</span></span>
- <span data-ttu-id="e7b02-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-372">'Identity'</span></span>
- <span data-ttu-id="e7b02-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-374">'Razor'</span></span>
- <span data-ttu-id="e7b02-375">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-375">'SignalR' uid:</span></span> 

<span data-ttu-id="e7b02-376">------|---
title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-377">'Blazor'</span></span>
- <span data-ttu-id="e7b02-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-378">'Identity'</span></span>
- <span data-ttu-id="e7b02-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-380">'Razor'</span></span>
- <span data-ttu-id="e7b02-381">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-382">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-383">'Blazor'</span></span>
- <span data-ttu-id="e7b02-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-384">'Identity'</span></span>
- <span data-ttu-id="e7b02-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-386">'Razor'</span></span>
- <span data-ttu-id="e7b02-387">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-388">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-389">'Blazor'</span></span>
- <span data-ttu-id="e7b02-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-390">'Identity'</span></span>
- <span data-ttu-id="e7b02-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-392">'Razor'</span></span>
- <span data-ttu-id="e7b02-393">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-394">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-395">'Blazor'</span></span>
- <span data-ttu-id="e7b02-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-396">'Identity'</span></span>
- <span data-ttu-id="e7b02-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-398">'Razor'</span></span>
- <span data-ttu-id="e7b02-399">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-400">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-401">'Blazor'</span></span>
- <span data-ttu-id="e7b02-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-402">'Identity'</span></span>
- <span data-ttu-id="e7b02-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-404">'Razor'</span></span>
- <span data-ttu-id="e7b02-405">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-406">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-407">'Blazor'</span></span>
- <span data-ttu-id="e7b02-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-408">'Identity'</span></span>
- <span data-ttu-id="e7b02-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-410">'Razor'</span></span>
- <span data-ttu-id="e7b02-411">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-412">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-413">'Blazor'</span></span>
- <span data-ttu-id="e7b02-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-414">'Identity'</span></span>
- <span data-ttu-id="e7b02-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-416">'Razor'</span></span>
- <span data-ttu-id="e7b02-417">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-418">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-419">'Blazor'</span></span>
- <span data-ttu-id="e7b02-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-420">'Identity'</span></span>
- <span data-ttu-id="e7b02-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-422">'Razor'</span></span>
- <span data-ttu-id="e7b02-423">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-424">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-425">'Blazor'</span></span>
- <span data-ttu-id="e7b02-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-426">'Identity'</span></span>
- <span data-ttu-id="e7b02-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-428">'Razor'</span></span>
- <span data-ttu-id="e7b02-429">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-430">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-431">'Blazor'</span></span>
- <span data-ttu-id="e7b02-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-432">'Identity'</span></span>
- <span data-ttu-id="e7b02-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-434">'Razor'</span></span>
- <span data-ttu-id="e7b02-435">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-436">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-437">'Blazor'</span></span>
- <span data-ttu-id="e7b02-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-438">'Identity'</span></span>
- <span data-ttu-id="e7b02-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-440">'Razor'</span></span>
- <span data-ttu-id="e7b02-441">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-442">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-443">'Blazor'</span></span>
- <span data-ttu-id="e7b02-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-444">'Identity'</span></span>
- <span data-ttu-id="e7b02-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-446">'Razor'</span></span>
- <span data-ttu-id="e7b02-447">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-448">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-449">'Blazor'</span></span>
- <span data-ttu-id="e7b02-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-450">'Identity'</span></span>
- <span data-ttu-id="e7b02-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-452">'Razor'</span></span>
- <span data-ttu-id="e7b02-453">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7b02-454">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7b02-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7b02-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-455">'Blazor'</span></span>
- <span data-ttu-id="e7b02-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7b02-456">'Identity'</span></span>
- <span data-ttu-id="e7b02-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7b02-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7b02-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7b02-458">'Razor'</span></span>
- <span data-ttu-id="e7b02-459">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e7b02-459">'SignalR' uid:</span></span> 

<span data-ttu-id="e7b02-460">----------------| | `add`     | Aggiungere una proprietà o un elemento di matrice.</span><span class="sxs-lookup"><span data-stu-id="e7b02-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="e7b02-461">Per la proprietà esistente: impostare Value. | | `remove`  | Rimuovere una proprietà o un elemento di matrice.</span><span class="sxs-lookup"><span data-stu-id="e7b02-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="e7b02-462">| | `replace` | Uguale `remove` a seguito dallo `add` stesso percorso.</span><span class="sxs-lookup"><span data-stu-id="e7b02-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="e7b02-463">| | `move`    | Uguale a `remove` dall'origine seguito da `add` alla destinazione usando il valore dell'origine.</span><span class="sxs-lookup"><span data-stu-id="e7b02-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="e7b02-464">| | `copy`    | Uguale `add` a per la destinazione usando il valore dell'origine.</span><span class="sxs-lookup"><span data-stu-id="e7b02-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="e7b02-465">| | `test`    | Restituisce il codice di stato di esito positivo se value at `path` = fornito `value` . |</span><span class="sxs-lookup"><span data-stu-id="e7b02-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="e7b02-466">JSON Patch in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7b02-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="e7b02-467">L'implementazione ASP.NET Core di JSON Patch viene fornita nel pacchetto NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="e7b02-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="e7b02-468">Il pacchetto è incluso nel metapacchetto [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e7b02-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="e7b02-469">Codice del metodo di azione</span><span class="sxs-lookup"><span data-stu-id="e7b02-469">Action method code</span></span>

<span data-ttu-id="e7b02-470">In un controller API un metodo di azione per JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="e7b02-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="e7b02-471">Viene annotato con l'attributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="e7b02-472">Accetta un oggetto `JsonPatchDocument<T>` , in genere con `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="e7b02-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="e7b02-473">Chiama `ApplyTo` nel documento di patch per applicare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="e7b02-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="e7b02-474">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="e7b02-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="e7b02-475">Questo codice dell'app di esempio funziona con il modello `Customer` seguente.</span><span class="sxs-lookup"><span data-stu-id="e7b02-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="e7b02-476">Il metodo di azione di esempio:</span><span class="sxs-lookup"><span data-stu-id="e7b02-476">The sample action method:</span></span>

* <span data-ttu-id="e7b02-477">Costruisce un oggetto `Customer`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="e7b02-478">Applica la patch.</span><span class="sxs-lookup"><span data-stu-id="e7b02-478">Applies the patch.</span></span>
* <span data-ttu-id="e7b02-479">Restituisce il risultato nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="e7b02-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="e7b02-480">In un'app reale il codice recupererebbe i dati da un archivio, ad esempio un database, e aggiornerebbe il database dopo aver applicato la patch.</span><span class="sxs-lookup"><span data-stu-id="e7b02-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="e7b02-481">Stato del modello</span><span class="sxs-lookup"><span data-stu-id="e7b02-481">Model state</span></span>

<span data-ttu-id="e7b02-482">L'esempio di metodo di azione precedente chiama un overload di `ApplyTo` che accetta lo stato del modello come uno dei propri parametri.</span><span class="sxs-lookup"><span data-stu-id="e7b02-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="e7b02-483">Con questa opzione, è possibile ottenere messaggi di errore nelle risposte.</span><span class="sxs-lookup"><span data-stu-id="e7b02-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="e7b02-484">L'esempio seguente mostra il corpo di una risposta 400 Richiesta non valida per un'operazione `test`:</span><span class="sxs-lookup"><span data-stu-id="e7b02-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="e7b02-485">Oggetti dinamici</span><span class="sxs-lookup"><span data-stu-id="e7b02-485">Dynamic objects</span></span>

<span data-ttu-id="e7b02-486">L'esempio di metodo di azione seguente mostra come applicare una patch a un oggetto dinamico.</span><span class="sxs-lookup"><span data-stu-id="e7b02-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="e7b02-487">Operazione add</span><span class="sxs-lookup"><span data-stu-id="e7b02-487">The add operation</span></span>

* <span data-ttu-id="e7b02-488">Se `path` punta a un elemento della matrice: inserisce un nuovo elemento prima di quello specificato da `path`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="e7b02-489">Se `path` punta a una proprietà: imposta il valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="e7b02-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="e7b02-490">Se `path` punta a una posizione che non esiste:</span><span class="sxs-lookup"><span data-stu-id="e7b02-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="e7b02-491">Se la risorsa cui applicare la patch è un oggetto dinamico: aggiunge una proprietà.</span><span class="sxs-lookup"><span data-stu-id="e7b02-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="e7b02-492">Se la risorsa cui applicare la patch è un oggetto statico: la richiesta non riesce.</span><span class="sxs-lookup"><span data-stu-id="e7b02-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="e7b02-493">Il documento di patch di esempio seguente imposta il valore di `CustomerName` e aggiunge un oggetto `Order` alla fine della matrice `Orders`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="e7b02-494">Operazione remove</span><span class="sxs-lookup"><span data-stu-id="e7b02-494">The remove operation</span></span>

* <span data-ttu-id="e7b02-495">Se `path` punta a un elemento della matrice: rimuove l'elemento.</span><span class="sxs-lookup"><span data-stu-id="e7b02-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="e7b02-496">Se `path` punta a una proprietà:</span><span class="sxs-lookup"><span data-stu-id="e7b02-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="e7b02-497">Se la risorsa cui applicare la patch è un oggetto dinamico: rimuove la proprietà.</span><span class="sxs-lookup"><span data-stu-id="e7b02-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="e7b02-498">Se la risorsa cui applicare la patch è un oggetto statico:</span><span class="sxs-lookup"><span data-stu-id="e7b02-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="e7b02-499">Se la proprietà ammette valori Null: la imposta su Null.</span><span class="sxs-lookup"><span data-stu-id="e7b02-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="e7b02-500">Se la proprietà non ammette valori Null: la imposta su `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="e7b02-501">Il documento di patch di esempio seguente imposta `CustomerName` su Null ed elimina `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="e7b02-502">Operazione replace</span><span class="sxs-lookup"><span data-stu-id="e7b02-502">The replace operation</span></span>

<span data-ttu-id="e7b02-503">Questa operazione equivale a livello funzionale all'operazione `remove` seguita da un'operazione `add`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="e7b02-504">Il documento di patch di esempio seguente imposta il valore di `CustomerName` e sostituisce `Orders[0]` con un nuovo oggetto `Order`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="e7b02-505">Operazione move</span><span class="sxs-lookup"><span data-stu-id="e7b02-505">The move operation</span></span>

* <span data-ttu-id="e7b02-506">Se `path` punta a un elemento della matrice: copia l'elemento `from` nella posizione dell'elemento `path`, quindi esegue un'operazione `remove` sull'elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="e7b02-507">Se `path` punta a una proprietà: copia il valore della proprietà `from` nella proprietà `path`, quindi esegue un'operazione `remove` sulla proprietà `from`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="e7b02-508">Se `path` punta a una proprietà che non esiste:</span><span class="sxs-lookup"><span data-stu-id="e7b02-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="e7b02-509">Se la risorsa cui applicare la patch è un oggetto statico: la richiesta non riesce.</span><span class="sxs-lookup"><span data-stu-id="e7b02-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="e7b02-510">Se la risorsa cui applicare la patch è un oggetto dinamico: copia la proprietà `from` nella posizione indicata da `path`, quindi esegue un'operazione `remove` sulla proprietà `from`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="e7b02-511">Il documento di patch di esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="e7b02-511">The following sample patch document:</span></span>

* <span data-ttu-id="e7b02-512">Copia il valore di `Orders[0].OrderName` in `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="e7b02-513">Imposta `Orders[0].OrderName` su Null.</span><span class="sxs-lookup"><span data-stu-id="e7b02-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="e7b02-514">Sposta `Orders[1]` prima di `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="e7b02-515">Operazione copy</span><span class="sxs-lookup"><span data-stu-id="e7b02-515">The copy operation</span></span>

<span data-ttu-id="e7b02-516">Questa operazione equivale a livello funzionale all'operazione `move` senza il passaggio `remove` finale.</span><span class="sxs-lookup"><span data-stu-id="e7b02-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="e7b02-517">Il documento di patch di esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="e7b02-517">The following sample patch document:</span></span>

* <span data-ttu-id="e7b02-518">Copia il valore di `Orders[0].OrderName` in `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="e7b02-519">Inserisce una copia di `Orders[1]` prima di `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e7b02-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="e7b02-520">Operazione test</span><span class="sxs-lookup"><span data-stu-id="e7b02-520">The test operation</span></span>

<span data-ttu-id="e7b02-521">Se il valore nella posizione indicata da `path` è diverso dal valore fornito in `value`, la richiesta non riesce.</span><span class="sxs-lookup"><span data-stu-id="e7b02-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="e7b02-522">In questo caso, l'intera richiesta PATCH non riesce anche se tutte le altre operazioni nel documento di patch potrebbero riuscire.</span><span class="sxs-lookup"><span data-stu-id="e7b02-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="e7b02-523">L'operazione `test` viene usata in genere per impedire un aggiornamento in caso di conflitto di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="e7b02-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="e7b02-524">Il documento di patch di esempio seguente non ha alcun effetto se il valore iniziale di `CustomerName` è "John", perché il test non riesce:</span><span class="sxs-lookup"><span data-stu-id="e7b02-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="e7b02-525">Ottenere il codice</span><span class="sxs-lookup"><span data-stu-id="e7b02-525">Get the code</span></span>

<span data-ttu-id="e7b02-526">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="e7b02-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="e7b02-527">([Come scaricare](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e7b02-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="e7b02-528">Per testare l'esempio, eseguire l'app e inviare richieste HTTP con le impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="e7b02-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="e7b02-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="e7b02-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="e7b02-530">Metodo HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="e7b02-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="e7b02-531">Intestazione: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="e7b02-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="e7b02-532">Body: copiare e incollare uno degli esempi di documenti patch JSON dalla cartella del progetto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="e7b02-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7b02-533">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e7b02-533">Additional resources</span></span>

* [<span data-ttu-id="e7b02-534">Specifica del metodo PATCH IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="e7b02-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="e7b02-535">Specifica JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="e7b02-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="e7b02-536">Specifica del formato di percorso JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="e7b02-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="e7b02-537">[Documentazione di JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="e7b02-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="e7b02-538">Include collegamenti a risorse per la creazione di documenti JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="e7b02-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="e7b02-539">Codice sorgente JSON Patch in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7b02-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
