---
title: Associazione di modelli in ASP.NET Core
author: rick-anderson
description: Informazioni su come funziona l'associazione di modelli in ASP.NET Core e su come personalizzarne il comportamento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- appsettings.json
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
uid: mvc/models/model-binding
ms.openlocfilehash: 4de34a75da932b41190caa8434ac5be8cc0710fd
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981934"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="3caac-103">Associazione di modelli in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3caac-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3caac-104">Questo articolo illustra cos'è l'associazione di modelli, come funziona e come personalizzarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="3caac-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="3caac-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3caac-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="3caac-106">Che cos'è l'associazione di modelli</span><span class="sxs-lookup"><span data-stu-id="3caac-106">What is Model binding</span></span>

<span data-ttu-id="3caac-107">I controller e le Razor pagine funzionano con dati provenienti da richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="3caac-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="3caac-108">Ad esempio, i dati di route possono fornire una chiave del record e i campi modulo inviati possono fornire valori per le proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="3caac-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="3caac-109">La scrittura di codice per recuperare tutti i valori e convertirli da stringhe in tipi .NET sarebbe noiosa e soggetta a errori.</span><span class="sxs-lookup"><span data-stu-id="3caac-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="3caac-110">L'associazione di modelli consente di automatizzare questo processo.</span><span class="sxs-lookup"><span data-stu-id="3caac-110">Model binding automates this process.</span></span> <span data-ttu-id="3caac-111">Il sistema di associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="3caac-111">The model binding system:</span></span>

* <span data-ttu-id="3caac-112">Recupera i dati da diverse origini, ad esempio i dati di route, i campi modulo e le stringhe di query.</span><span class="sxs-lookup"><span data-stu-id="3caac-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="3caac-113">Fornisce i dati ai controller e alle Razor pagine nei parametri del metodo e nelle proprietà pubbliche.</span><span class="sxs-lookup"><span data-stu-id="3caac-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="3caac-114">Converte dati stringa in tipi .NET.</span><span class="sxs-lookup"><span data-stu-id="3caac-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="3caac-115">Aggiorna le proprietà dei tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="3caac-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="3caac-116">Esempio</span><span class="sxs-lookup"><span data-stu-id="3caac-116">Example</span></span>

<span data-ttu-id="3caac-117">Si supponga di avere il metodo di azione seguente:</span><span class="sxs-lookup"><span data-stu-id="3caac-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="3caac-118">E l'app riceve una richiesta con questo URL:</span><span class="sxs-lookup"><span data-stu-id="3caac-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="3caac-119">L'associazione di modelli esegue i passaggi seguenti dopo che il sistema di routing seleziona il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="3caac-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="3caac-120">Trova il primo parametro di `GetByID`, un intero denominato `id`.</span><span class="sxs-lookup"><span data-stu-id="3caac-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="3caac-121">Esamina le origini disponibili nella richiesta HTTP e trova `id` = "2" nei dati di route.</span><span class="sxs-lookup"><span data-stu-id="3caac-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="3caac-122">Converte la stringa "2" nell'intero 2.</span><span class="sxs-lookup"><span data-stu-id="3caac-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="3caac-123">Trova il parametro successivo di `GetByID`, un valore booleano denominato `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="3caac-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="3caac-124">Esamina le origini e trova "DogsOnly=true" nella stringa di query.</span><span class="sxs-lookup"><span data-stu-id="3caac-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="3caac-125">Per la corrispondenza dei nomi non viene applicata la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="3caac-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="3caac-126">Converte la stringa "true" nel valore booleano `true`.</span><span class="sxs-lookup"><span data-stu-id="3caac-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="3caac-127">Il framework chiama quindi il metodo `GetById`, passando 2 per il parametro `id` e `true` per il parametro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="3caac-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="3caac-128">Nell'esempio precedente le destinazioni dell'associazione di modelli sono parametri di metodo che sono tipi semplici.</span><span class="sxs-lookup"><span data-stu-id="3caac-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="3caac-129">Le destinazioni possono essere anche le proprietà di un tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="3caac-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="3caac-130">Dopo l'associazione di ogni proprietà, viene eseguita la [convalida dei modelli](xref:mvc/models/validation) per la proprietà.</span><span class="sxs-lookup"><span data-stu-id="3caac-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="3caac-131">Il record dei dati associati al modello e di eventuali errori di associazione o convalida viene archiviato in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oppure [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="3caac-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="3caac-132">Per scoprire se questo processo ha esito positivo, l'app controlla il flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="3caac-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="3caac-133">Server di destinazione</span><span class="sxs-lookup"><span data-stu-id="3caac-133">Targets</span></span>

<span data-ttu-id="3caac-134">L'associazione di modelli cerca di trovare i valori per i tipi di destinazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3caac-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="3caac-135">Parametri del metodo di azione del controller a cui viene indirizzata una richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="3caac-136">Parametri del Razor metodo del gestore pagine a cui viene indirizzata una richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="3caac-137">Proprietà pubbliche di un controller o una classe `PageModel`, se specificate dagli attributi.</span><span class="sxs-lookup"><span data-stu-id="3caac-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="3caac-138">Attributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="3caac-138">[BindProperty] attribute</span></span>

<span data-ttu-id="3caac-139">Può essere applicato a una proprietà pubblica di un controller o una classe `PageModel` per fare in modo che l'associazione di modelli usi tale proprietà come destinazione:</span><span class="sxs-lookup"><span data-stu-id="3caac-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="3caac-140">Attributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="3caac-140">[BindProperties] attribute</span></span>

<span data-ttu-id="3caac-141">Disponibile in ASP.NET Core 2.1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="3caac-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="3caac-142">Può essere applicato a un controller o una classe `PageModel` per indicare all'associazione del modello di usare tutte le proprietà pubbliche della classe come destinazione:</span><span class="sxs-lookup"><span data-stu-id="3caac-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="3caac-143">Associazione di modelli per le richieste HTTP GET</span><span class="sxs-lookup"><span data-stu-id="3caac-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="3caac-144">Per impostazione predefinita, le proprietà non vengono associate per le richieste HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3caac-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="3caac-145">In genere, per una richiesta GET è sufficiente un parametro ID record.</span><span class="sxs-lookup"><span data-stu-id="3caac-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="3caac-146">L'ID record viene usato per cercare l'elemento nel database.</span><span class="sxs-lookup"><span data-stu-id="3caac-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="3caac-147">Pertanto, non è necessario associare una proprietà che contiene un'istanza del modello.</span><span class="sxs-lookup"><span data-stu-id="3caac-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="3caac-148">Negli scenari in cui si vogliono associare le proprietà ai dati dalle richieste GET, impostare la proprietà `SupportsGet` su `true`:</span><span class="sxs-lookup"><span data-stu-id="3caac-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="3caac-149">Origini</span><span class="sxs-lookup"><span data-stu-id="3caac-149">Sources</span></span>

<span data-ttu-id="3caac-150">Per impostazione predefinita, l'associazione di modelli ottiene i dati sotto forma di coppie chiave-valore dalle origini seguenti in una richiesta HTTP:</span><span class="sxs-lookup"><span data-stu-id="3caac-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="3caac-151">Campi modulo</span><span class="sxs-lookup"><span data-stu-id="3caac-151">Form fields</span></span>
1. <span data-ttu-id="3caac-152">Il corpo della richiesta (per i controller [ con l'attributo [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="3caac-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="3caac-153">Indirizzare i dati</span><span class="sxs-lookup"><span data-stu-id="3caac-153">Route data</span></span>
1. <span data-ttu-id="3caac-154">Parametri della stringa di query</span><span class="sxs-lookup"><span data-stu-id="3caac-154">Query string parameters</span></span>
1. <span data-ttu-id="3caac-155">File caricati</span><span class="sxs-lookup"><span data-stu-id="3caac-155">Uploaded files</span></span>

<span data-ttu-id="3caac-156">Per ogni parametro o proprietà di destinazione, le origini vengono analizzate nell'ordine indicato nell'elenco precedente.</span><span class="sxs-lookup"><span data-stu-id="3caac-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="3caac-157">Esistono tuttavia alcune eccezioni:</span><span class="sxs-lookup"><span data-stu-id="3caac-157">There are a few exceptions:</span></span>

* <span data-ttu-id="3caac-158">I dati di route e i valori delle stringhe di query vengono usati solo per i tipi semplici.</span><span class="sxs-lookup"><span data-stu-id="3caac-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="3caac-159">I file caricati vengono associati solo ai tipi di destinazione che implementano `IFormFile` o `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="3caac-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="3caac-160">Se l'origine predefinita non è corretta, usare uno degli attributi seguenti per specificare l'origine:</span><span class="sxs-lookup"><span data-stu-id="3caac-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="3caac-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Ottiene i valori dalla stringa di query.</span><span class="sxs-lookup"><span data-stu-id="3caac-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="3caac-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Ottiene i valori dai dati della route.</span><span class="sxs-lookup"><span data-stu-id="3caac-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="3caac-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Ottiene i valori dai campi del modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="3caac-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="3caac-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Ottiene i valori dal corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="3caac-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Ottiene i valori dalle intestazioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="3caac-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="3caac-166">Questi attributi:</span><span class="sxs-lookup"><span data-stu-id="3caac-166">These attributes:</span></span>

* <span data-ttu-id="3caac-167">Vengono aggiunti singolarmente alle proprietà del modello (non alla classe del modello), come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3caac-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="3caac-168">Accettano facoltativamente un valore di nome di modello nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="3caac-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="3caac-169">Questa opzione è disponibile nel caso in cui il nome della proprietà non corrisponda al valore nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="3caac-170">Il valore nella richiesta, ad esempio, potrebbe essere un'intestazione con un segno meno nel nome, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3caac-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="3caac-171">Attributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="3caac-171">[FromBody] attribute</span></span>

<span data-ttu-id="3caac-172">Applicare l' `[FromBody]` attributo a un parametro per popolarne le proprietà dal corpo di una richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="3caac-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="3caac-173">Il runtime di ASP.NET Core delega la responsabilità della lettura del corpo a un formattatore di input.</span><span class="sxs-lookup"><span data-stu-id="3caac-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="3caac-174">I formattatori di input sono descritti [più avanti in questo articolo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3caac-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="3caac-175">Quando `[FromBody]` viene applicato a un parametro di tipo complesso, tutti gli attributi di origine di associazione applicati alle relative proprietà vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="3caac-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="3caac-176">Ad esempio, l' `Create` azione seguente specifica che il relativo `pet` parametro viene popolato dal corpo:</span><span class="sxs-lookup"><span data-stu-id="3caac-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="3caac-177">La `Pet` classe specifica che la `Breed` proprietà viene popolata da un parametro della stringa di query:</span><span class="sxs-lookup"><span data-stu-id="3caac-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="3caac-178">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="3caac-178">In the preceding example:</span></span>

* <span data-ttu-id="3caac-179">L' `[FromQuery]` attributo viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="3caac-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="3caac-180">La `Breed` proprietà non viene popolata da un parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="3caac-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="3caac-181">I formattatori di input leggono solo il corpo e non comprendono gli attributi di origine del binding.</span><span class="sxs-lookup"><span data-stu-id="3caac-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="3caac-182">Se nel corpo viene trovato un valore appropriato, tale valore viene usato per popolare la `Breed` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="3caac-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="3caac-183">Non applicare `[FromBody]` a più di un parametro per ogni metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="3caac-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="3caac-184">Una volta che il flusso di richiesta viene letto da un formattatore di input, non è più disponibile per la lettura per l'associazione di altri `[FromBody]` parametri.</span><span class="sxs-lookup"><span data-stu-id="3caac-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="3caac-185">Origini aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3caac-185">Additional sources</span></span>

<span data-ttu-id="3caac-186">I dati di origine vengono forniti al sistema di associazione di modelli dai *provider di valori*.</span><span class="sxs-lookup"><span data-stu-id="3caac-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="3caac-187">È possibile scrivere e registrare i provider di valori personalizzati che recuperano i dati per l'associazione di modelli da altre origini.</span><span class="sxs-lookup"><span data-stu-id="3caac-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="3caac-188">Ad esempio, è possibile che si desiderino i dati cookie dello stato della sessione o.</span><span class="sxs-lookup"><span data-stu-id="3caac-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="3caac-189">Per ottenere dati da una nuova origine:</span><span class="sxs-lookup"><span data-stu-id="3caac-189">To get data from a new source:</span></span>

* <span data-ttu-id="3caac-190">Creare una classe che implementi `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="3caac-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="3caac-191">Creare una classe che implementi `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="3caac-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="3caac-192">Registrare la classe factory in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3caac-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="3caac-193">L'app di esempio include un [provider di valori](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) e un esempio di [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) che ottiene i valori da cookie s.</span><span class="sxs-lookup"><span data-stu-id="3caac-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="3caac-194">Ecco il codice di registrazione in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3caac-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="3caac-195">Il codice illustrato posiziona il provider di valori personalizzati dopo tutti i provider di valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="3caac-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="3caac-196">Per renderlo il primo nell'elenco, chiamare `Insert(0, new CookieValueProviderFactory())` invece di `Add`.</span><span class="sxs-lookup"><span data-stu-id="3caac-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="3caac-197">Nessuna origine per una proprietà del modello</span><span class="sxs-lookup"><span data-stu-id="3caac-197">No source for a model property</span></span>

<span data-ttu-id="3caac-198">Per impostazione predefinita, non viene creato un errore di stato del modello se non viene trovato alcun valore per una proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="3caac-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="3caac-199">La proprietà viene impostata su Null o su un valore predefinito:</span><span class="sxs-lookup"><span data-stu-id="3caac-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="3caac-200">I tipi semplici nullable vengono impostati su `null`.</span><span class="sxs-lookup"><span data-stu-id="3caac-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="3caac-201">I tipi valore non nullable vengono impostati su `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="3caac-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="3caac-202">Ad esempio, un parametro `int id` viene impostato su 0.</span><span class="sxs-lookup"><span data-stu-id="3caac-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="3caac-203">Per i tipi complessi, l'associazione di modelli crea un'istanza usando il costruttore predefinito, senza impostare proprietà.</span><span class="sxs-lookup"><span data-stu-id="3caac-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="3caac-204">Le matrici vengono impostate su `Array.Empty<T>()`, ad eccezione delle matrici `byte[]` che vengono impostate su `null`.</span><span class="sxs-lookup"><span data-stu-id="3caac-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="3caac-205">Se lo stato del modello deve essere invalidato quando non viene trovato alcun elemento nei campi del modulo per una proprietà del modello, usare l' [`[BindRequired]`](#bindrequired-attribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="3caac-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="3caac-206">Si noti che questo comportamento `[BindRequired]` si applica all'associazione di modelli dai dati di moduli inviati e non ai dati JSON o XML nel corpo di una richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="3caac-207">I dati del corpo della richiesta vengono gestiti dai [formattatori di input](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3caac-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="3caac-208">Errori di conversione dei tipi</span><span class="sxs-lookup"><span data-stu-id="3caac-208">Type conversion errors</span></span>

<span data-ttu-id="3caac-209">Se viene trovata un'origine ma non può essere convertita nel tipo di destinazione, lo stato del modello viene contrassegnato come non valido.</span><span class="sxs-lookup"><span data-stu-id="3caac-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="3caac-210">Il parametro o la proprietà di destinazione viene impostato su Null o su un valore predefinito, come indicato nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="3caac-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="3caac-211">In un controller API con l'attributo `[ApiController]`, uno stato del modello non valido genera una risposta HTTP 400 automatica.</span><span class="sxs-lookup"><span data-stu-id="3caac-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="3caac-212">In una Razor pagina, visualizzare nuovamente la pagina con un messaggio di errore:</span><span class="sxs-lookup"><span data-stu-id="3caac-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="3caac-213">La convalida sul lato client rileva la maggior parte dei dati non validi che altrimenti verrebbero inviati a un Razor modulo di pagine.</span><span class="sxs-lookup"><span data-stu-id="3caac-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="3caac-214">Questa convalida rende difficile attivare il codice sopra evidenziato.</span><span class="sxs-lookup"><span data-stu-id="3caac-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="3caac-215">L'app di esempio include un pulsante **Submit with Invalid Date** (Invia con data non valida) che inserisce dati non validi nel campo **Hire Date** (Data assunzione) e invia il modulo.</span><span class="sxs-lookup"><span data-stu-id="3caac-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="3caac-216">Questo pulsante illustra il funzionamento del codice per visualizzare di nuovo la pagina quando si verificano errori di conversione dei dati.</span><span class="sxs-lookup"><span data-stu-id="3caac-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="3caac-217">Quando la pagina viene nuovamente visualizzata dal codice precedente, l'input non valido non viene visualizzato nel campo modulo.</span><span class="sxs-lookup"><span data-stu-id="3caac-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="3caac-218">Questo avviene perché la proprietà del modello è stata impostata su Null o su un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="3caac-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="3caac-219">L'input non valido viene visualizzato in un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="3caac-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="3caac-220">Ma se si vogliono visualizzare di nuovo i dati non validi nel campo modulo, è consigliabile impostare la proprietà del modello come stringa ed eseguire manualmente la conversione dei dati.</span><span class="sxs-lookup"><span data-stu-id="3caac-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="3caac-221">La stessa strategia è consigliata se si vuole evitare che gli errori di conversione del tipo causino errori di stato del modello.</span><span class="sxs-lookup"><span data-stu-id="3caac-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="3caac-222">In tal caso, impostare la proprietà del modello come stringa.</span><span class="sxs-lookup"><span data-stu-id="3caac-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="3caac-223">Tipi semplici</span><span class="sxs-lookup"><span data-stu-id="3caac-223">Simple types</span></span>

<span data-ttu-id="3caac-224">I tipi semplici in cui lo strumento di associazione di modelli può convertire le stringhe di origine includono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="3caac-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="3caac-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="3caac-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="3caac-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="3caac-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="3caac-227">Char</span><span class="sxs-lookup"><span data-stu-id="3caac-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="3caac-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="3caac-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="3caac-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="3caac-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="3caac-230">Decimale</span><span class="sxs-lookup"><span data-stu-id="3caac-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="3caac-231">Double</span><span class="sxs-lookup"><span data-stu-id="3caac-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="3caac-232">Enum</span><span class="sxs-lookup"><span data-stu-id="3caac-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="3caac-233">GUID</span><span class="sxs-lookup"><span data-stu-id="3caac-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="3caac-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="3caac-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="3caac-235">Singolo</span><span class="sxs-lookup"><span data-stu-id="3caac-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="3caac-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="3caac-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="3caac-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="3caac-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="3caac-238">Uri</span><span class="sxs-lookup"><span data-stu-id="3caac-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="3caac-239">Version</span><span class="sxs-lookup"><span data-stu-id="3caac-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="3caac-240">Tipi complessi</span><span class="sxs-lookup"><span data-stu-id="3caac-240">Complex types</span></span>

<span data-ttu-id="3caac-241">Un tipo pubblico deve avere un costruttore predefinito pubblico e proprietà scrivibili pubbliche da associare.</span><span class="sxs-lookup"><span data-stu-id="3caac-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="3caac-242">Quando si verifica l'associazione di modelli, vengono create istanze della classe usando il costruttore predefinito pubblico.</span><span class="sxs-lookup"><span data-stu-id="3caac-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="3caac-243">Per ogni proprietà del tipo complesso, l'associazione di modelli cerca il modello di nome *prefisso.nome_proprietà* nelle origini.</span><span class="sxs-lookup"><span data-stu-id="3caac-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="3caac-244">Se non viene trovato, cerca semplicemente *nome_proprietà* senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="3caac-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="3caac-245">Per l'associazione a un parametro, il prefisso è il nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="3caac-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="3caac-246">Per l'associazione a una proprietà pubblica `PageModel`, il prefisso è il nome della proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="3caac-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="3caac-247">Alcuni attributi hanno una proprietà `Prefix` che consente di eseguire l'override dell'utilizzo predefinito del nome di un parametro o una proprietà.</span><span class="sxs-lookup"><span data-stu-id="3caac-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="3caac-248">Ad esempio, si supponga che il tipo complesso sia la classe `Instructor` seguente:</span><span class="sxs-lookup"><span data-stu-id="3caac-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="3caac-249">Prefisso = nome del parametro</span><span class="sxs-lookup"><span data-stu-id="3caac-249">Prefix = parameter name</span></span>

<span data-ttu-id="3caac-250">Se il modello da associare è un parametro denominato `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="3caac-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="3caac-251">L'associazione di modelli cerca prima di tutto la chiave `instructorToUpdate.ID` nelle origini.</span><span class="sxs-lookup"><span data-stu-id="3caac-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="3caac-252">Se non viene trovata, cerca `ID` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="3caac-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="3caac-253">Prefisso = nome della proprietà</span><span class="sxs-lookup"><span data-stu-id="3caac-253">Prefix = property name</span></span>

<span data-ttu-id="3caac-254">Se il modello da associare è una proprietà denominata `Instructor` del controller o della classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="3caac-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="3caac-255">L'associazione di modelli cerca prima di tutto la chiave `Instructor.ID` nelle origini.</span><span class="sxs-lookup"><span data-stu-id="3caac-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3caac-256">Se non viene trovata, cerca `ID` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="3caac-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="3caac-257">Prefisso personalizzato</span><span class="sxs-lookup"><span data-stu-id="3caac-257">Custom prefix</span></span>

<span data-ttu-id="3caac-258">Se il modello da associare è un parametro denominato `instructorToUpdate` e un attributo `Bind` specifica `Instructor` come prefisso:</span><span class="sxs-lookup"><span data-stu-id="3caac-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="3caac-259">L'associazione di modelli cerca prima di tutto la chiave `Instructor.ID` nelle origini.</span><span class="sxs-lookup"><span data-stu-id="3caac-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3caac-260">Se non viene trovata, cerca `ID` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="3caac-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="3caac-261">Attributi per le destinazioni di tipo complesso</span><span class="sxs-lookup"><span data-stu-id="3caac-261">Attributes for complex type targets</span></span>

<span data-ttu-id="3caac-262">Sono disponibili vari attributi predefiniti per controllare l'associazione di modelli di tipi complessi:</span><span class="sxs-lookup"><span data-stu-id="3caac-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="3caac-263">Questi attributi influiscono sul modello di associazione quando i dati di modulo inviati sono l'origine dei valori.</span><span class="sxs-lookup"><span data-stu-id="3caac-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="3caac-264">**Non hanno** effetti sui formattatori di input, che elaborano i corpi di richiesta JSON e XML.</span><span class="sxs-lookup"><span data-stu-id="3caac-264">They do \***not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="3caac-265">I formattatori di input sono descritti [più avanti in questo articolo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3caac-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="3caac-266">Attributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="3caac-266">[Bind] attribute</span></span>

<span data-ttu-id="3caac-267">Può essere applicato a una classe o a un parametro di metodo.</span><span class="sxs-lookup"><span data-stu-id="3caac-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="3caac-268">Specifica quali proprietà di un modello devono essere incluse nell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="3caac-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="3caac-269">`[Bind]` non _*_influisce sui_*_ formattatori di input.</span><span class="sxs-lookup"><span data-stu-id="3caac-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="3caac-270">Nell'esempio seguente vengono associate solo le proprietà specificate del modello `Instructor` quando viene chiamato qualsiasi gestore o metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="3caac-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="3caac-271">Nell'esempio seguente vengono associate solo le proprietà specificate del modello `Instructor` quando viene chiamato il metodo `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="3caac-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="3caac-272">L' `[Bind]` attributo può essere utilizzato per la protezione dall'overposting negli scenari _create \*.</span><span class="sxs-lookup"><span data-stu-id="3caac-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="3caac-273">Non funziona bene negli scenari di modifica perché le proprietà escluse vengono impostate su Null o su un valore predefinito anziché rimanere inalterate.</span><span class="sxs-lookup"><span data-stu-id="3caac-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="3caac-274">Per difendersi dall'overposting, sono consigliati i modelli di visualizzazione anziché l'attributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="3caac-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="3caac-275">Per altre informazioni, vedere [Nota sulla sicurezza relativa all'overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="3caac-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="3caac-276">Attributo [ModelBinder]</span><span class="sxs-lookup"><span data-stu-id="3caac-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="3caac-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> può essere applicato a tipi, proprietà o parametri.</span><span class="sxs-lookup"><span data-stu-id="3caac-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="3caac-278">Consente di specificare il tipo di strumento di associazione di modelli utilizzato per associare l'istanza o il tipo specifico.</span><span class="sxs-lookup"><span data-stu-id="3caac-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="3caac-279">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3caac-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="3caac-280">L' `[ModelBinder]` attributo può essere usato anche per modificare il nome di una proprietà o di un parametro quando è associato a un modello:</span><span class="sxs-lookup"><span data-stu-id="3caac-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="3caac-281">Attributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="3caac-281">[BindRequired] attribute</span></span>

<span data-ttu-id="3caac-282">Può essere applicato solo alle proprietà del modello e non ai parametri di metodo.</span><span class="sxs-lookup"><span data-stu-id="3caac-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3caac-283">Con questo attributo l'associazione di modelli aggiunge un errore di stato del modello se non è possibile eseguire l'associazione per una proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="3caac-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="3caac-284">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="3caac-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="3caac-285">Vedere anche la discussione relativa all'attributo `[Required]` in [Convalida del modello](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="3caac-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="3caac-286">Attributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="3caac-286">[BindNever] attribute</span></span>

<span data-ttu-id="3caac-287">Può essere applicato solo alle proprietà del modello e non ai parametri di metodo.</span><span class="sxs-lookup"><span data-stu-id="3caac-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3caac-288">Impedisce all'associazione di modelli di impostare una proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="3caac-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="3caac-289">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="3caac-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="3caac-290">Raccolte</span><span class="sxs-lookup"><span data-stu-id="3caac-290">Collections</span></span>

<span data-ttu-id="3caac-291">Per le destinazioni che sono raccolte di tipi semplici, l'associazione di modelli cerca le corrispondenze per *nome_parametro* oppure *nome_proprietà*.</span><span class="sxs-lookup"><span data-stu-id="3caac-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="3caac-292">Se non viene trovata alcuna corrispondenza, viene cercato uno dei formati supportati senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="3caac-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3caac-293">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3caac-293">For example:</span></span>

* <span data-ttu-id="3caac-294">Si supponga che il parametro da associare sia una matrice denominata `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3caac-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="3caac-295">I dati di modulo o di stringhe di query possono essere in uno dei formati seguenti:</span><span class="sxs-lookup"><span data-stu-id="3caac-295">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="3caac-296">Il formato seguente è supportato solo nei dati di modulo:</span><span class="sxs-lookup"><span data-stu-id="3caac-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="3caac-297">Per tutti i formati di esempio precedenti, l'associazione di modelli passa una matrice di due elementi al parametro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3caac-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3caac-298">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="3caac-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="3caac-299">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="3caac-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="3caac-300">Per i formati di dati che usano numeri con indice (... [0]... [1]...) è necessario assicurarsi che la numerazione sia progressiva a partire da zero.</span><span class="sxs-lookup"><span data-stu-id="3caac-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="3caac-301">Se sono presenti eventuali interruzioni nella numerazione con indice, tutti gli elementi dopo l'interruzione vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="3caac-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="3caac-302">Ad esempio, se gli indici sono 0 e 2 anziché 0 e 1, il secondo elemento viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="3caac-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="3caac-303">Dizionari</span><span class="sxs-lookup"><span data-stu-id="3caac-303">Dictionaries</span></span>

<span data-ttu-id="3caac-304">Per le destinazioni `Dictionary`, l'associazione di modelli cerca le corrispondenze per *nome_parametro* oppure *nome_proprietà*.</span><span class="sxs-lookup"><span data-stu-id="3caac-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="3caac-305">Se non viene trovata alcuna corrispondenza, viene cercato uno dei formati supportati senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="3caac-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3caac-306">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3caac-306">For example:</span></span>

* <span data-ttu-id="3caac-307">Si supponga che il parametro di destinazione sia un elemento `Dictionary<int, string>` denominato `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3caac-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="3caac-308">I dati di modulo o di stringhe di query inviati possono essere simili a uno degli esempi seguenti:</span><span class="sxs-lookup"><span data-stu-id="3caac-308">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="3caac-309">Per tutti i formati di esempio precedenti, l'associazione di modelli passa un dizionario di due elementi al parametro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3caac-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3caac-310">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="3caac-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="3caac-311">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="3caac-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="3caac-312">Binding del costruttore e tipi di record</span><span class="sxs-lookup"><span data-stu-id="3caac-312">Constructor binding and record types</span></span>

<span data-ttu-id="3caac-313">Per l'associazione di modelli è necessario che i tipi complessi dispongano di un costruttore senza parametri.</span><span class="sxs-lookup"><span data-stu-id="3caac-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="3caac-314">Entrambi `System.Text.Json` `Newtonsoft.Json` i formattatori di input basati su e supportano la deserializzazione delle classi che non dispongono di un costruttore senza parametri.</span><span class="sxs-lookup"><span data-stu-id="3caac-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="3caac-315">In C# 9 sono stati introdotti i tipi di record, un ottimo modo per rappresentare sinteticamente i dati in rete.</span><span class="sxs-lookup"><span data-stu-id="3caac-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="3caac-316">ASP.NET Core aggiunge il supporto per l'associazione di modelli e la convalida dei tipi di record con un unico costruttore:</span><span class="sxs-lookup"><span data-stu-id="3caac-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="3caac-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="3caac-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="3caac-318">Quando si convalidano i tipi di record, il runtime cerca i metadati di convalida in modo specifico nei parametri anziché nelle proprietà.</span><span class="sxs-lookup"><span data-stu-id="3caac-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="3caac-319">Comportamento di globalizzazione dell'associazione di modelli dati della route e stringhe di query</span><span class="sxs-lookup"><span data-stu-id="3caac-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="3caac-320">Il provider del valore della route ASP.NET Core e il provider del valore della stringa di query:</span><span class="sxs-lookup"><span data-stu-id="3caac-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="3caac-321">Considera i valori come impostazioni cultura invarianti.</span><span class="sxs-lookup"><span data-stu-id="3caac-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="3caac-322">Si prevede che gli URL siano impostazioni cultura invarianti.</span><span class="sxs-lookup"><span data-stu-id="3caac-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="3caac-323">Al contrario, i valori provenienti dai dati del form subiscono una conversione con distinzione delle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="3caac-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="3caac-324">Questa operazione è progettata in modo che gli URL siano condivisibili tra le impostazioni locali.</span><span class="sxs-lookup"><span data-stu-id="3caac-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="3caac-325">Per rendere il provider del valore di route ASP.NET Core e il provider di valori della stringa di query sottoposti a una conversione dipendente dalle impostazioni cultura:</span><span class="sxs-lookup"><span data-stu-id="3caac-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="3caac-326">Ereditano da <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="3caac-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="3caac-327">Copiare il codice da [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) o [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="3caac-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="3caac-328">Sostituire il [valore delle impostazioni cultura](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passato al costruttore del provider di valori con [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="3caac-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="3caac-329">Sostituire la factory del provider di valori predefinito in opzioni MVC con la nuova:</span><span class="sxs-lookup"><span data-stu-id="3caac-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="3caac-330">Tipi di dati speciali</span><span class="sxs-lookup"><span data-stu-id="3caac-330">Special data types</span></span>

<span data-ttu-id="3caac-331">Esistono alcuni tipi di dati speciali che l'associazione di modelli può gestire.</span><span class="sxs-lookup"><span data-stu-id="3caac-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="3caac-332">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="3caac-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="3caac-333">Un file caricato incluso nella richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="3caac-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="3caac-334">È anche supportato `IEnumerable<IFormFile>` per più file.</span><span class="sxs-lookup"><span data-stu-id="3caac-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="3caac-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="3caac-335">CancellationToken</span></span>

<span data-ttu-id="3caac-336">Le azioni possono facoltativamente associare un `CancellationToken` come parametro.</span><span class="sxs-lookup"><span data-stu-id="3caac-336">Actions can optionally bind a `CancellationToken` as a parameter.</span></span> <span data-ttu-id="3caac-337">Questa operazione associa i <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> segnali quando la connessione sottostante la richiesta HTTP viene interrotta.</span><span class="sxs-lookup"><span data-stu-id="3caac-337">This binds <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> that signals when the connection underlying the HTTP request is aborted.</span></span> <span data-ttu-id="3caac-338">Le azioni possono usare questo parametro per annullare le operazioni asincrone a esecuzione prolungata eseguite come parte delle azioni del controller.</span><span class="sxs-lookup"><span data-stu-id="3caac-338">Actions can use this parameter to cancel long running async operations that are executed as part of the controller actions.</span></span>

### <a name="formcollection"></a><span data-ttu-id="3caac-339">FormCollection</span><span class="sxs-lookup"><span data-stu-id="3caac-339">FormCollection</span></span>

<span data-ttu-id="3caac-340">Usato per recuperare tutti i valori dai dati di modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="3caac-340">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="3caac-341">Formattatori di input</span><span class="sxs-lookup"><span data-stu-id="3caac-341">Input formatters</span></span>

<span data-ttu-id="3caac-342">I dati nel corpo della richiesta possono essere in formato JSON, XML o in altri formati.</span><span class="sxs-lookup"><span data-stu-id="3caac-342">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="3caac-343">Per analizzare questi dati, l'associazione di modelli usa un *formattatore di input* configurato in modo da gestire un particolare tipo di contenuto.</span><span class="sxs-lookup"><span data-stu-id="3caac-343">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="3caac-344">Per impostazione predefinita, ASP.NET Core include i formattatori di input basati su JSON per la gestione dei dati JSON.</span><span class="sxs-lookup"><span data-stu-id="3caac-344">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="3caac-345">È possibile aggiungere altri formattatori per altri tipi di contenuto.</span><span class="sxs-lookup"><span data-stu-id="3caac-345">You can add other formatters for other content types.</span></span>

<span data-ttu-id="3caac-346">ASP.NET Core consente di selezionare i formattatori di input in base all'attributo [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="3caac-346">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="3caac-347">Se non è presente alcun attributo, viene usata l'[intestazione Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="3caac-347">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="3caac-348">Per usare i formattatori di input XML predefiniti:</span><span class="sxs-lookup"><span data-stu-id="3caac-348">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="3caac-349">Installare il pacchetto NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="3caac-349">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="3caac-350">In `Startup.ConfigureServices` chiamare <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> o <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="3caac-350">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="3caac-351">Applicare l'attributo `Consumes` alle classi controller o ai metodi di azione che devono aspettarsi XML nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-351">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="3caac-352">Per altre informazioni, vedere [Introduzione alla serializzazione XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="3caac-352">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="3caac-353">Personalizzare l'associazione di modelli con formattatori di input</span><span class="sxs-lookup"><span data-stu-id="3caac-353">Customize model binding with input formatters</span></span>

<span data-ttu-id="3caac-354">Un formattatore di input assume la piena responsabilità per la lettura dei dati dal corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-354">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="3caac-355">Per personalizzare questo processo, configurare le API usate dal formattatore di input.</span><span class="sxs-lookup"><span data-stu-id="3caac-355">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="3caac-356">In questa sezione viene descritto come personalizzare il `System.Text.Json` formattatore di input basato su per comprendere un tipo personalizzato denominato `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="3caac-356">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="3caac-357">Si consideri il modello seguente, che contiene una `ObjectId` proprietà personalizzata denominata `Id` :</span><span class="sxs-lookup"><span data-stu-id="3caac-357">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="3caac-358">Per personalizzare il processo di associazione di modelli quando si usa `System.Text.Json` , creare una classe derivata da <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="3caac-358">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="3caac-359">Per usare un convertitore personalizzato, applicare l' <xref:System.Text.Json.Serialization.JsonConverterAttribute> attributo al tipo.</span><span class="sxs-lookup"><span data-stu-id="3caac-359">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="3caac-360">Nell'esempio seguente il `ObjectId` tipo viene configurato con `ObjectIdConverter` come convertitore personalizzato:</span><span class="sxs-lookup"><span data-stu-id="3caac-360">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="3caac-361">Per ulteriori informazioni, vedere [come scrivere convertitori personalizzati](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="3caac-361">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="3caac-362">Escludere i tipi specificati dall'associazione di modelli</span><span class="sxs-lookup"><span data-stu-id="3caac-362">Exclude specified types from model binding</span></span>

<span data-ttu-id="3caac-363">Il comportamento dell'associazione di modelli e del sistema di convalida è determinato da [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="3caac-363">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="3caac-364">È possibile personalizzare `ModelMetadata` mediante l'aggiunta di un provider di dettagli a [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="3caac-364">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="3caac-365">Sono disponibili provider di dettagli predefiniti per la disabilitazione dell'associazione di modelli o la convalida per i tipi specificati.</span><span class="sxs-lookup"><span data-stu-id="3caac-365">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="3caac-366">Per disabilitare l'associazione di modelli per tutti i modelli di un tipo specificato, aggiungere un <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3caac-366">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3caac-367">Ad esempio, per disabilitare l'associazione di modelli per tutti i modelli di tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="3caac-367">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="3caac-368">Per disabilitare la convalida per le proprietà di un tipo specificato, aggiungere un <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3caac-368">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3caac-369">Ad esempio, per disabilitare la convalida per le proprietà di tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="3caac-369">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="3caac-370">Strumenti di associazione di modelli personalizzati</span><span class="sxs-lookup"><span data-stu-id="3caac-370">Custom model binders</span></span>

<span data-ttu-id="3caac-371">È possibile estendere l'associazione di modelli scrivendo uno strumento di associazione di modelli personalizzato e usando l'attributo `[ModelBinder]` per selezionarlo per una determinata destinazione.</span><span class="sxs-lookup"><span data-stu-id="3caac-371">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="3caac-372">Altre informazioni sull'[associazione di modelli personalizzata](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="3caac-372">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="3caac-373">Associazione di modelli manuale</span><span class="sxs-lookup"><span data-stu-id="3caac-373">Manual model binding</span></span> 

<span data-ttu-id="3caac-374">L'associazione di modelli può essere richiamata manualmente usando il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3caac-374">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="3caac-375">Il metodo è definito in entrambe le classi `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="3caac-375">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="3caac-376">Gli overload del metodo consentono di specificare il prefisso e il provider di valori da usare.</span><span class="sxs-lookup"><span data-stu-id="3caac-376">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="3caac-377">Il metodo restituisce `false` se l'associazione di modelli non riesce.</span><span class="sxs-lookup"><span data-stu-id="3caac-377">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="3caac-378">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="3caac-378">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="3caac-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  USA i provider di valori per ottenere i dati dal corpo del form, dalla stringa di query e dai dati della route.</span><span class="sxs-lookup"><span data-stu-id="3caac-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="3caac-380">`TryUpdateModelAsync` è in genere:</span><span class="sxs-lookup"><span data-stu-id="3caac-380">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="3caac-381">Usato con le Razor pagine e le app MVC che usano i controller e le visualizzazioni per impedire l'overposting.</span><span class="sxs-lookup"><span data-stu-id="3caac-381">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="3caac-382">Non utilizzato con un'API Web, a meno che non venga utilizzato da dati del modulo, stringhe di query e dati di route.</span><span class="sxs-lookup"><span data-stu-id="3caac-382">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="3caac-383">Gli endpoint dell'API Web che utilizzano JSON utilizzano [formattatori di input](#input-formatters) per deserializzare il corpo della richiesta in un oggetto.</span><span class="sxs-lookup"><span data-stu-id="3caac-383">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="3caac-384">Per ulteriori informazioni, vedere [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="3caac-384">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="3caac-385">Attributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="3caac-385">[FromServices] attribute</span></span>

<span data-ttu-id="3caac-386">Il nome di questo attributo segue il modello degli attributi di associazione di modelli che specificano un'origine dati.</span><span class="sxs-lookup"><span data-stu-id="3caac-386">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="3caac-387">Non si tratta però dell'associazione di dati da un provider di valori.</span><span class="sxs-lookup"><span data-stu-id="3caac-387">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="3caac-388">Ottiene un'istanza di un tipo dal contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3caac-388">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3caac-389">Lo scopo è fornire un'alternativa all'inserimento del costruttore per quando è necessario un servizio solo se viene chiamato un metodo specifico.</span><span class="sxs-lookup"><span data-stu-id="3caac-389">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3caac-390">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3caac-390">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3caac-391">Questo articolo illustra cos'è l'associazione di modelli, come funziona e come personalizzarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="3caac-391">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="3caac-392">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3caac-392">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="3caac-393">Che cos'è l'associazione di modelli</span><span class="sxs-lookup"><span data-stu-id="3caac-393">What is Model binding</span></span>

<span data-ttu-id="3caac-394">I controller e le Razor pagine funzionano con dati provenienti da richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="3caac-394">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="3caac-395">Ad esempio, i dati di route possono fornire una chiave del record e i campi modulo inviati possono fornire valori per le proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="3caac-395">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="3caac-396">La scrittura di codice per recuperare tutti i valori e convertirli da stringhe in tipi .NET sarebbe noiosa e soggetta a errori.</span><span class="sxs-lookup"><span data-stu-id="3caac-396">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="3caac-397">L'associazione di modelli consente di automatizzare questo processo.</span><span class="sxs-lookup"><span data-stu-id="3caac-397">Model binding automates this process.</span></span> <span data-ttu-id="3caac-398">Il sistema di associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="3caac-398">The model binding system:</span></span>

* <span data-ttu-id="3caac-399">Recupera i dati da diverse origini, ad esempio i dati di route, i campi modulo e le stringhe di query.</span><span class="sxs-lookup"><span data-stu-id="3caac-399">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="3caac-400">Fornisce i dati ai controller e alle Razor pagine nei parametri del metodo e nelle proprietà pubbliche.</span><span class="sxs-lookup"><span data-stu-id="3caac-400">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="3caac-401">Converte dati stringa in tipi .NET.</span><span class="sxs-lookup"><span data-stu-id="3caac-401">Converts string data to .NET types.</span></span>
* <span data-ttu-id="3caac-402">Aggiorna le proprietà dei tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="3caac-402">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="3caac-403">Esempio</span><span class="sxs-lookup"><span data-stu-id="3caac-403">Example</span></span>

<span data-ttu-id="3caac-404">Si supponga di avere il metodo di azione seguente:</span><span class="sxs-lookup"><span data-stu-id="3caac-404">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="3caac-405">E l'app riceve una richiesta con questo URL:</span><span class="sxs-lookup"><span data-stu-id="3caac-405">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="3caac-406">L'associazione di modelli esegue i passaggi seguenti dopo che il sistema di routing seleziona il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="3caac-406">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="3caac-407">Trova il primo parametro di `GetByID`, un intero denominato `id`.</span><span class="sxs-lookup"><span data-stu-id="3caac-407">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="3caac-408">Esamina le origini disponibili nella richiesta HTTP e trova `id` = "2" nei dati di route.</span><span class="sxs-lookup"><span data-stu-id="3caac-408">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="3caac-409">Converte la stringa "2" nell'intero 2.</span><span class="sxs-lookup"><span data-stu-id="3caac-409">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="3caac-410">Trova il parametro successivo di `GetByID`, un valore booleano denominato `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="3caac-410">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="3caac-411">Esamina le origini e trova "DogsOnly=true" nella stringa di query.</span><span class="sxs-lookup"><span data-stu-id="3caac-411">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="3caac-412">Per la corrispondenza dei nomi non viene applicata la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="3caac-412">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="3caac-413">Converte la stringa "true" nel valore booleano `true`.</span><span class="sxs-lookup"><span data-stu-id="3caac-413">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="3caac-414">Il framework chiama quindi il metodo `GetById`, passando 2 per il parametro `id` e `true` per il parametro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="3caac-414">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="3caac-415">Nell'esempio precedente le destinazioni dell'associazione di modelli sono parametri di metodo che sono tipi semplici.</span><span class="sxs-lookup"><span data-stu-id="3caac-415">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="3caac-416">Le destinazioni possono essere anche le proprietà di un tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="3caac-416">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="3caac-417">Dopo l'associazione di ogni proprietà, viene eseguita la [convalida dei modelli](xref:mvc/models/validation) per la proprietà.</span><span class="sxs-lookup"><span data-stu-id="3caac-417">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="3caac-418">Il record dei dati associati al modello e di eventuali errori di associazione o convalida viene archiviato in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oppure [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="3caac-418">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="3caac-419">Per scoprire se questo processo ha esito positivo, l'app controlla il flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="3caac-419">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="3caac-420">Server di destinazione</span><span class="sxs-lookup"><span data-stu-id="3caac-420">Targets</span></span>

<span data-ttu-id="3caac-421">L'associazione di modelli cerca di trovare i valori per i tipi di destinazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3caac-421">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="3caac-422">Parametri del metodo di azione del controller a cui viene indirizzata una richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-422">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="3caac-423">Parametri del Razor metodo del gestore pagine a cui viene indirizzata una richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-423">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="3caac-424">Proprietà pubbliche di un controller o una classe `PageModel`, se specificate dagli attributi.</span><span class="sxs-lookup"><span data-stu-id="3caac-424">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="3caac-425">Attributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="3caac-425">[BindProperty] attribute</span></span>

<span data-ttu-id="3caac-426">Può essere applicato a una proprietà pubblica di un controller o una classe `PageModel` per fare in modo che l'associazione di modelli usi tale proprietà come destinazione:</span><span class="sxs-lookup"><span data-stu-id="3caac-426">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="3caac-427">Attributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="3caac-427">[BindProperties] attribute</span></span>

<span data-ttu-id="3caac-428">Disponibile in ASP.NET Core 2.1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="3caac-428">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="3caac-429">Può essere applicato a un controller o una classe `PageModel` per indicare all'associazione del modello di usare tutte le proprietà pubbliche della classe come destinazione:</span><span class="sxs-lookup"><span data-stu-id="3caac-429">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="3caac-430">Associazione di modelli per le richieste HTTP GET</span><span class="sxs-lookup"><span data-stu-id="3caac-430">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="3caac-431">Per impostazione predefinita, le proprietà non vengono associate per le richieste HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3caac-431">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="3caac-432">In genere, per una richiesta GET è sufficiente un parametro ID record.</span><span class="sxs-lookup"><span data-stu-id="3caac-432">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="3caac-433">L'ID record viene usato per cercare l'elemento nel database.</span><span class="sxs-lookup"><span data-stu-id="3caac-433">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="3caac-434">Pertanto, non è necessario associare una proprietà che contiene un'istanza del modello.</span><span class="sxs-lookup"><span data-stu-id="3caac-434">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="3caac-435">Negli scenari in cui si vogliono associare le proprietà ai dati dalle richieste GET, impostare la proprietà `SupportsGet` su `true`:</span><span class="sxs-lookup"><span data-stu-id="3caac-435">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="3caac-436">Origini</span><span class="sxs-lookup"><span data-stu-id="3caac-436">Sources</span></span>

<span data-ttu-id="3caac-437">Per impostazione predefinita, l'associazione di modelli ottiene i dati sotto forma di coppie chiave-valore dalle origini seguenti in una richiesta HTTP:</span><span class="sxs-lookup"><span data-stu-id="3caac-437">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="3caac-438">Campi modulo</span><span class="sxs-lookup"><span data-stu-id="3caac-438">Form fields</span></span>
1. <span data-ttu-id="3caac-439">Il corpo della richiesta (per i controller [ con l'attributo [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="3caac-439">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="3caac-440">Indirizzare i dati</span><span class="sxs-lookup"><span data-stu-id="3caac-440">Route data</span></span>
1. <span data-ttu-id="3caac-441">Parametri della stringa di query</span><span class="sxs-lookup"><span data-stu-id="3caac-441">Query string parameters</span></span>
1. <span data-ttu-id="3caac-442">File caricati</span><span class="sxs-lookup"><span data-stu-id="3caac-442">Uploaded files</span></span>

<span data-ttu-id="3caac-443">Per ogni parametro o proprietà di destinazione, le origini vengono analizzate nell'ordine indicato nell'elenco precedente.</span><span class="sxs-lookup"><span data-stu-id="3caac-443">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="3caac-444">Esistono tuttavia alcune eccezioni:</span><span class="sxs-lookup"><span data-stu-id="3caac-444">There are a few exceptions:</span></span>

* <span data-ttu-id="3caac-445">I dati di route e i valori delle stringhe di query vengono usati solo per i tipi semplici.</span><span class="sxs-lookup"><span data-stu-id="3caac-445">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="3caac-446">I file caricati vengono associati solo ai tipi di destinazione che implementano `IFormFile` o `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="3caac-446">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="3caac-447">Se l'origine predefinita non è corretta, usare uno degli attributi seguenti per specificare l'origine:</span><span class="sxs-lookup"><span data-stu-id="3caac-447">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="3caac-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Ottiene i valori dalla stringa di query.</span><span class="sxs-lookup"><span data-stu-id="3caac-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="3caac-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Ottiene i valori dai dati della route.</span><span class="sxs-lookup"><span data-stu-id="3caac-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="3caac-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Ottiene i valori dai campi del modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="3caac-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="3caac-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Ottiene i valori dal corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="3caac-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Ottiene i valori dalle intestazioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="3caac-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="3caac-453">Questi attributi:</span><span class="sxs-lookup"><span data-stu-id="3caac-453">These attributes:</span></span>

* <span data-ttu-id="3caac-454">Vengono aggiunti singolarmente alle proprietà del modello (non alla classe del modello), come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3caac-454">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="3caac-455">Accettano facoltativamente un valore di nome di modello nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="3caac-455">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="3caac-456">Questa opzione è disponibile nel caso in cui il nome della proprietà non corrisponda al valore nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-456">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="3caac-457">Il valore nella richiesta, ad esempio, potrebbe essere un'intestazione con un segno meno nel nome, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3caac-457">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="3caac-458">Attributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="3caac-458">[FromBody] attribute</span></span>

<span data-ttu-id="3caac-459">Applicare l' `[FromBody]` attributo a un parametro per popolarne le proprietà dal corpo di una richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="3caac-459">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="3caac-460">Il runtime di ASP.NET Core delega la responsabilità della lettura del corpo a un formattatore di input.</span><span class="sxs-lookup"><span data-stu-id="3caac-460">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="3caac-461">I formattatori di input sono descritti [più avanti in questo articolo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3caac-461">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="3caac-462">Quando `[FromBody]` viene applicato a un parametro di tipo complesso, tutti gli attributi di origine di associazione applicati alle relative proprietà vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="3caac-462">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="3caac-463">Ad esempio, l' `Create` azione seguente specifica che il relativo `pet` parametro viene popolato dal corpo:</span><span class="sxs-lookup"><span data-stu-id="3caac-463">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="3caac-464">La `Pet` classe specifica che la `Breed` proprietà viene popolata da un parametro della stringa di query:</span><span class="sxs-lookup"><span data-stu-id="3caac-464">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="3caac-465">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="3caac-465">In the preceding example:</span></span>

* <span data-ttu-id="3caac-466">L' `[FromQuery]` attributo viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="3caac-466">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="3caac-467">La `Breed` proprietà non viene popolata da un parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="3caac-467">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="3caac-468">I formattatori di input leggono solo il corpo e non comprendono gli attributi di origine del binding.</span><span class="sxs-lookup"><span data-stu-id="3caac-468">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="3caac-469">Se nel corpo viene trovato un valore appropriato, tale valore viene usato per popolare la `Breed` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="3caac-469">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="3caac-470">Non applicare `[FromBody]` a più di un parametro per ogni metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="3caac-470">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="3caac-471">Una volta che il flusso di richiesta viene letto da un formattatore di input, non è più disponibile per la lettura per l'associazione di altri `[FromBody]` parametri.</span><span class="sxs-lookup"><span data-stu-id="3caac-471">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="3caac-472">Origini aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3caac-472">Additional sources</span></span>

<span data-ttu-id="3caac-473">I dati di origine vengono forniti al sistema di associazione di modelli dai *provider di valori*.</span><span class="sxs-lookup"><span data-stu-id="3caac-473">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="3caac-474">È possibile scrivere e registrare i provider di valori personalizzati che recuperano i dati per l'associazione di modelli da altre origini.</span><span class="sxs-lookup"><span data-stu-id="3caac-474">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="3caac-475">Ad esempio, è possibile che si desiderino i dati cookie dello stato della sessione o.</span><span class="sxs-lookup"><span data-stu-id="3caac-475">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="3caac-476">Per ottenere dati da una nuova origine:</span><span class="sxs-lookup"><span data-stu-id="3caac-476">To get data from a new source:</span></span>

* <span data-ttu-id="3caac-477">Creare una classe che implementi `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="3caac-477">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="3caac-478">Creare una classe che implementi `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="3caac-478">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="3caac-479">Registrare la classe factory in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3caac-479">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="3caac-480">L'app di esempio include un [provider di valori](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) e un esempio di [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) che ottiene i valori da cookie s.</span><span class="sxs-lookup"><span data-stu-id="3caac-480">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="3caac-481">Ecco il codice di registrazione in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3caac-481">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="3caac-482">Il codice illustrato posiziona il provider di valori personalizzati dopo tutti i provider di valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="3caac-482">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="3caac-483">Per renderlo il primo nell'elenco, chiamare `Insert(0, new CookieValueProviderFactory())` invece di `Add`.</span><span class="sxs-lookup"><span data-stu-id="3caac-483">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="3caac-484">Nessuna origine per una proprietà del modello</span><span class="sxs-lookup"><span data-stu-id="3caac-484">No source for a model property</span></span>

<span data-ttu-id="3caac-485">Per impostazione predefinita, non viene creato un errore di stato del modello se non viene trovato alcun valore per una proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="3caac-485">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="3caac-486">La proprietà viene impostata su Null o su un valore predefinito:</span><span class="sxs-lookup"><span data-stu-id="3caac-486">The property is set to null or a default value:</span></span>

* <span data-ttu-id="3caac-487">I tipi semplici nullable vengono impostati su `null`.</span><span class="sxs-lookup"><span data-stu-id="3caac-487">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="3caac-488">I tipi valore non nullable vengono impostati su `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="3caac-488">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="3caac-489">Ad esempio, un parametro `int id` viene impostato su 0.</span><span class="sxs-lookup"><span data-stu-id="3caac-489">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="3caac-490">Per i tipi complessi, l'associazione di modelli crea un'istanza usando il costruttore predefinito, senza impostare proprietà.</span><span class="sxs-lookup"><span data-stu-id="3caac-490">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="3caac-491">Le matrici vengono impostate su `Array.Empty<T>()`, ad eccezione delle matrici `byte[]` che vengono impostate su `null`.</span><span class="sxs-lookup"><span data-stu-id="3caac-491">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="3caac-492">Se lo stato del modello deve essere invalidato quando non viene trovato alcun elemento nei campi del modulo per una proprietà del modello, usare l' [`[BindRequired]`](#bindrequired-attribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="3caac-492">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="3caac-493">Si noti che questo comportamento `[BindRequired]` si applica all'associazione di modelli dai dati di moduli inviati e non ai dati JSON o XML nel corpo di una richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-493">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="3caac-494">I dati del corpo della richiesta vengono gestiti dai [formattatori di input](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3caac-494">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="3caac-495">Errori di conversione dei tipi</span><span class="sxs-lookup"><span data-stu-id="3caac-495">Type conversion errors</span></span>

<span data-ttu-id="3caac-496">Se viene trovata un'origine ma non può essere convertita nel tipo di destinazione, lo stato del modello viene contrassegnato come non valido.</span><span class="sxs-lookup"><span data-stu-id="3caac-496">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="3caac-497">Il parametro o la proprietà di destinazione viene impostato su Null o su un valore predefinito, come indicato nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="3caac-497">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="3caac-498">In un controller API con l'attributo `[ApiController]`, uno stato del modello non valido genera una risposta HTTP 400 automatica.</span><span class="sxs-lookup"><span data-stu-id="3caac-498">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="3caac-499">In una Razor pagina, visualizzare nuovamente la pagina con un messaggio di errore:</span><span class="sxs-lookup"><span data-stu-id="3caac-499">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="3caac-500">La convalida sul lato client rileva la maggior parte dei dati non validi che altrimenti verrebbero inviati a un Razor modulo di pagine.</span><span class="sxs-lookup"><span data-stu-id="3caac-500">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="3caac-501">Questa convalida rende difficile attivare il codice sopra evidenziato.</span><span class="sxs-lookup"><span data-stu-id="3caac-501">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="3caac-502">L'app di esempio include un pulsante **Submit with Invalid Date** (Invia con data non valida) che inserisce dati non validi nel campo **Hire Date** (Data assunzione) e invia il modulo.</span><span class="sxs-lookup"><span data-stu-id="3caac-502">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="3caac-503">Questo pulsante illustra il funzionamento del codice per visualizzare di nuovo la pagina quando si verificano errori di conversione dei dati.</span><span class="sxs-lookup"><span data-stu-id="3caac-503">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="3caac-504">Quando la pagina viene nuovamente visualizzata dal codice precedente, l'input non valido non viene visualizzato nel campo modulo.</span><span class="sxs-lookup"><span data-stu-id="3caac-504">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="3caac-505">Questo avviene perché la proprietà del modello è stata impostata su Null o su un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="3caac-505">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="3caac-506">L'input non valido viene visualizzato in un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="3caac-506">The invalid input does appear in an error message.</span></span> <span data-ttu-id="3caac-507">Ma se si vogliono visualizzare di nuovo i dati non validi nel campo modulo, è consigliabile impostare la proprietà del modello come stringa ed eseguire manualmente la conversione dei dati.</span><span class="sxs-lookup"><span data-stu-id="3caac-507">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="3caac-508">La stessa strategia è consigliata se si vuole evitare che gli errori di conversione del tipo causino errori di stato del modello.</span><span class="sxs-lookup"><span data-stu-id="3caac-508">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="3caac-509">In tal caso, impostare la proprietà del modello come stringa.</span><span class="sxs-lookup"><span data-stu-id="3caac-509">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="3caac-510">Tipi semplici</span><span class="sxs-lookup"><span data-stu-id="3caac-510">Simple types</span></span>

<span data-ttu-id="3caac-511">I tipi semplici in cui lo strumento di associazione di modelli può convertire le stringhe di origine includono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="3caac-511">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="3caac-512">Boolean</span><span class="sxs-lookup"><span data-stu-id="3caac-512">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="3caac-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="3caac-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="3caac-514">Char</span><span class="sxs-lookup"><span data-stu-id="3caac-514">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="3caac-515">DateTime</span><span class="sxs-lookup"><span data-stu-id="3caac-515">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="3caac-516">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="3caac-516">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="3caac-517">Decimale</span><span class="sxs-lookup"><span data-stu-id="3caac-517">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="3caac-518">Double</span><span class="sxs-lookup"><span data-stu-id="3caac-518">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="3caac-519">Enum</span><span class="sxs-lookup"><span data-stu-id="3caac-519">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="3caac-520">GUID</span><span class="sxs-lookup"><span data-stu-id="3caac-520">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="3caac-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="3caac-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="3caac-522">Singolo</span><span class="sxs-lookup"><span data-stu-id="3caac-522">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="3caac-523">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="3caac-523">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="3caac-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="3caac-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="3caac-525">Uri</span><span class="sxs-lookup"><span data-stu-id="3caac-525">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="3caac-526">Version</span><span class="sxs-lookup"><span data-stu-id="3caac-526">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="3caac-527">Tipi complessi</span><span class="sxs-lookup"><span data-stu-id="3caac-527">Complex types</span></span>

<span data-ttu-id="3caac-528">Un tipo pubblico deve avere un costruttore predefinito pubblico e proprietà scrivibili pubbliche da associare.</span><span class="sxs-lookup"><span data-stu-id="3caac-528">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="3caac-529">Quando si verifica l'associazione di modelli, vengono create istanze della classe usando il costruttore predefinito pubblico.</span><span class="sxs-lookup"><span data-stu-id="3caac-529">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="3caac-530">Per ogni proprietà del tipo complesso, l'associazione di modelli cerca il modello di nome *prefisso.nome_proprietà* nelle origini.</span><span class="sxs-lookup"><span data-stu-id="3caac-530">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="3caac-531">Se non viene trovato, cerca semplicemente *nome_proprietà* senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="3caac-531">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="3caac-532">Per l'associazione a un parametro, il prefisso è il nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="3caac-532">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="3caac-533">Per l'associazione a una proprietà pubblica `PageModel`, il prefisso è il nome della proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="3caac-533">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="3caac-534">Alcuni attributi hanno una proprietà `Prefix` che consente di eseguire l'override dell'utilizzo predefinito del nome di un parametro o una proprietà.</span><span class="sxs-lookup"><span data-stu-id="3caac-534">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="3caac-535">Ad esempio, si supponga che il tipo complesso sia la classe `Instructor` seguente:</span><span class="sxs-lookup"><span data-stu-id="3caac-535">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="3caac-536">Prefisso = nome del parametro</span><span class="sxs-lookup"><span data-stu-id="3caac-536">Prefix = parameter name</span></span>

<span data-ttu-id="3caac-537">Se il modello da associare è un parametro denominato `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="3caac-537">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="3caac-538">L'associazione di modelli cerca prima di tutto la chiave `instructorToUpdate.ID` nelle origini.</span><span class="sxs-lookup"><span data-stu-id="3caac-538">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="3caac-539">Se non viene trovata, cerca `ID` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="3caac-539">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="3caac-540">Prefisso = nome della proprietà</span><span class="sxs-lookup"><span data-stu-id="3caac-540">Prefix = property name</span></span>

<span data-ttu-id="3caac-541">Se il modello da associare è una proprietà denominata `Instructor` del controller o della classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="3caac-541">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="3caac-542">L'associazione di modelli cerca prima di tutto la chiave `Instructor.ID` nelle origini.</span><span class="sxs-lookup"><span data-stu-id="3caac-542">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3caac-543">Se non viene trovata, cerca `ID` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="3caac-543">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="3caac-544">Prefisso personalizzato</span><span class="sxs-lookup"><span data-stu-id="3caac-544">Custom prefix</span></span>

<span data-ttu-id="3caac-545">Se il modello da associare è un parametro denominato `instructorToUpdate` e un attributo `Bind` specifica `Instructor` come prefisso:</span><span class="sxs-lookup"><span data-stu-id="3caac-545">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="3caac-546">L'associazione di modelli cerca prima di tutto la chiave `Instructor.ID` nelle origini.</span><span class="sxs-lookup"><span data-stu-id="3caac-546">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3caac-547">Se non viene trovata, cerca `ID` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="3caac-547">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="3caac-548">Attributi per le destinazioni di tipo complesso</span><span class="sxs-lookup"><span data-stu-id="3caac-548">Attributes for complex type targets</span></span>

<span data-ttu-id="3caac-549">Sono disponibili vari attributi predefiniti per controllare l'associazione di modelli di tipi complessi:</span><span class="sxs-lookup"><span data-stu-id="3caac-549">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="3caac-550">Questi attributi influiscono sul modello di associazione quando i dati di modulo inviati sono l'origine dei valori.</span><span class="sxs-lookup"><span data-stu-id="3caac-550">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="3caac-551">Non influiscono sui formattatori di input, che elaborano corpi di richieste JSON e XML inviati.</span><span class="sxs-lookup"><span data-stu-id="3caac-551">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="3caac-552">I formattatori di input sono descritti [più avanti in questo articolo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3caac-552">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="3caac-553">Vedere anche la discussione relativa all'attributo `[Required]` in [Convalida del modello](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="3caac-553">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="3caac-554">Attributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="3caac-554">[BindRequired] attribute</span></span>

<span data-ttu-id="3caac-555">Può essere applicato solo alle proprietà del modello e non ai parametri di metodo.</span><span class="sxs-lookup"><span data-stu-id="3caac-555">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3caac-556">Con questo attributo l'associazione di modelli aggiunge un errore di stato del modello se non è possibile eseguire l'associazione per una proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="3caac-556">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="3caac-557">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="3caac-557">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="3caac-558">Attributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="3caac-558">[BindNever] attribute</span></span>

<span data-ttu-id="3caac-559">Può essere applicato solo alle proprietà del modello e non ai parametri di metodo.</span><span class="sxs-lookup"><span data-stu-id="3caac-559">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3caac-560">Impedisce all'associazione di modelli di impostare una proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="3caac-560">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="3caac-561">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="3caac-561">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="3caac-562">Attributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="3caac-562">[Bind] attribute</span></span>

<span data-ttu-id="3caac-563">Può essere applicato a una classe o a un parametro di metodo.</span><span class="sxs-lookup"><span data-stu-id="3caac-563">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="3caac-564">Specifica quali proprietà di un modello devono essere incluse nell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="3caac-564">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="3caac-565">Nell'esempio seguente vengono associate solo le proprietà specificate del modello `Instructor` quando viene chiamato qualsiasi gestore o metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="3caac-565">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="3caac-566">Nell'esempio seguente vengono associate solo le proprietà specificate del modello `Instructor` quando viene chiamato il metodo `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="3caac-566">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="3caac-567">L'attributo `[Bind]` può essere usato per evitare l'overposting negli scenari di *creazione*.</span><span class="sxs-lookup"><span data-stu-id="3caac-567">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="3caac-568">Non funziona bene negli scenari di modifica perché le proprietà escluse vengono impostate su Null o su un valore predefinito anziché rimanere inalterate.</span><span class="sxs-lookup"><span data-stu-id="3caac-568">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="3caac-569">Per difendersi dall'overposting, sono consigliati i modelli di visualizzazione anziché l'attributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="3caac-569">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="3caac-570">Per altre informazioni, vedere [Nota sulla sicurezza relativa all'overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="3caac-570">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="3caac-571">Raccolte</span><span class="sxs-lookup"><span data-stu-id="3caac-571">Collections</span></span>

<span data-ttu-id="3caac-572">Per le destinazioni che sono raccolte di tipi semplici, l'associazione di modelli cerca le corrispondenze per *nome_parametro* oppure *nome_proprietà*.</span><span class="sxs-lookup"><span data-stu-id="3caac-572">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="3caac-573">Se non viene trovata alcuna corrispondenza, viene cercato uno dei formati supportati senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="3caac-573">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3caac-574">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3caac-574">For example:</span></span>

* <span data-ttu-id="3caac-575">Si supponga che il parametro da associare sia una matrice denominata `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3caac-575">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="3caac-576">I dati di modulo o di stringhe di query possono essere in uno dei formati seguenti:</span><span class="sxs-lookup"><span data-stu-id="3caac-576">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="3caac-577">Il formato seguente è supportato solo nei dati di modulo:</span><span class="sxs-lookup"><span data-stu-id="3caac-577">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="3caac-578">Per tutti i formati di esempio precedenti, l'associazione di modelli passa una matrice di due elementi al parametro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3caac-578">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3caac-579">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="3caac-579">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="3caac-580">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="3caac-580">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="3caac-581">Per i formati di dati che usano numeri con indice (... [0]... [1]...) è necessario assicurarsi che la numerazione sia progressiva a partire da zero.</span><span class="sxs-lookup"><span data-stu-id="3caac-581">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="3caac-582">Se sono presenti eventuali interruzioni nella numerazione con indice, tutti gli elementi dopo l'interruzione vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="3caac-582">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="3caac-583">Ad esempio, se gli indici sono 0 e 2 anziché 0 e 1, il secondo elemento viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="3caac-583">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="3caac-584">Dizionari</span><span class="sxs-lookup"><span data-stu-id="3caac-584">Dictionaries</span></span>

<span data-ttu-id="3caac-585">Per le destinazioni `Dictionary`, l'associazione di modelli cerca le corrispondenze per *nome_parametro* oppure *nome_proprietà*.</span><span class="sxs-lookup"><span data-stu-id="3caac-585">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="3caac-586">Se non viene trovata alcuna corrispondenza, viene cercato uno dei formati supportati senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="3caac-586">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3caac-587">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3caac-587">For example:</span></span>

* <span data-ttu-id="3caac-588">Si supponga che il parametro di destinazione sia un elemento `Dictionary<int, string>` denominato `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3caac-588">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="3caac-589">I dati di modulo o di stringhe di query inviati possono essere simili a uno degli esempi seguenti:</span><span class="sxs-lookup"><span data-stu-id="3caac-589">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="3caac-590">Per tutti i formati di esempio precedenti, l'associazione di modelli passa un dizionario di due elementi al parametro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3caac-590">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3caac-591">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="3caac-591">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="3caac-592">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="3caac-592">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="3caac-593">Comportamento di globalizzazione dell'associazione di modelli dati della route e stringhe di query</span><span class="sxs-lookup"><span data-stu-id="3caac-593">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="3caac-594">Il provider del valore della route ASP.NET Core e il provider del valore della stringa di query:</span><span class="sxs-lookup"><span data-stu-id="3caac-594">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="3caac-595">Considera i valori come impostazioni cultura invarianti.</span><span class="sxs-lookup"><span data-stu-id="3caac-595">Treat values as invariant culture.</span></span>
* <span data-ttu-id="3caac-596">Si prevede che gli URL siano impostazioni cultura invarianti.</span><span class="sxs-lookup"><span data-stu-id="3caac-596">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="3caac-597">Al contrario, i valori provenienti dai dati del form subiscono una conversione con distinzione delle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="3caac-597">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="3caac-598">Questa operazione è progettata in modo che gli URL siano condivisibili tra le impostazioni locali.</span><span class="sxs-lookup"><span data-stu-id="3caac-598">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="3caac-599">Per rendere il provider del valore di route ASP.NET Core e il provider di valori della stringa di query sottoposti a una conversione dipendente dalle impostazioni cultura:</span><span class="sxs-lookup"><span data-stu-id="3caac-599">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="3caac-600">Ereditano da <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="3caac-600">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="3caac-601">Copiare il codice da [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) o [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="3caac-601">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="3caac-602">Sostituire il [valore delle impostazioni cultura](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passato al costruttore del provider di valori con [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="3caac-602">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="3caac-603">Sostituire la factory del provider di valori predefinito in opzioni MVC con la nuova:</span><span class="sxs-lookup"><span data-stu-id="3caac-603">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="3caac-604">Tipi di dati speciali</span><span class="sxs-lookup"><span data-stu-id="3caac-604">Special data types</span></span>

<span data-ttu-id="3caac-605">Esistono alcuni tipi di dati speciali che l'associazione di modelli può gestire.</span><span class="sxs-lookup"><span data-stu-id="3caac-605">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="3caac-606">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="3caac-606">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="3caac-607">Un file caricato incluso nella richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="3caac-607">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="3caac-608">È anche supportato `IEnumerable<IFormFile>` per più file.</span><span class="sxs-lookup"><span data-stu-id="3caac-608">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="3caac-609">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="3caac-609">CancellationToken</span></span>

<span data-ttu-id="3caac-610">usato per annullare l'attività nei controller asincroni.</span><span class="sxs-lookup"><span data-stu-id="3caac-610">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="3caac-611">FormCollection</span><span class="sxs-lookup"><span data-stu-id="3caac-611">FormCollection</span></span>

<span data-ttu-id="3caac-612">Usato per recuperare tutti i valori dai dati di modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="3caac-612">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="3caac-613">Formattatori di input</span><span class="sxs-lookup"><span data-stu-id="3caac-613">Input formatters</span></span>

<span data-ttu-id="3caac-614">I dati nel corpo della richiesta possono essere in formato JSON, XML o in altri formati.</span><span class="sxs-lookup"><span data-stu-id="3caac-614">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="3caac-615">Per analizzare questi dati, l'associazione di modelli usa un *formattatore di input* configurato in modo da gestire un particolare tipo di contenuto.</span><span class="sxs-lookup"><span data-stu-id="3caac-615">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="3caac-616">Per impostazione predefinita, ASP.NET Core include i formattatori di input basati su JSON per la gestione dei dati JSON.</span><span class="sxs-lookup"><span data-stu-id="3caac-616">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="3caac-617">È possibile aggiungere altri formattatori per altri tipi di contenuto.</span><span class="sxs-lookup"><span data-stu-id="3caac-617">You can add other formatters for other content types.</span></span>

<span data-ttu-id="3caac-618">ASP.NET Core consente di selezionare i formattatori di input in base all'attributo [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="3caac-618">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="3caac-619">Se non è presente alcun attributo, viene usata l'[intestazione Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="3caac-619">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="3caac-620">Per usare i formattatori di input XML predefiniti:</span><span class="sxs-lookup"><span data-stu-id="3caac-620">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="3caac-621">Installare il pacchetto NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="3caac-621">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="3caac-622">In `Startup.ConfigureServices` chiamare <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> o <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="3caac-622">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="3caac-623">Applicare l'attributo `Consumes` alle classi controller o ai metodi di azione che devono aspettarsi XML nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="3caac-623">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="3caac-624">Per altre informazioni, vedere [Introduzione alla serializzazione XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="3caac-624">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="3caac-625">Escludere i tipi specificati dall'associazione di modelli</span><span class="sxs-lookup"><span data-stu-id="3caac-625">Exclude specified types from model binding</span></span>

<span data-ttu-id="3caac-626">Il comportamento dell'associazione di modelli e del sistema di convalida è determinato da [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="3caac-626">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="3caac-627">È possibile personalizzare `ModelMetadata` mediante l'aggiunta di un provider di dettagli a [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="3caac-627">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="3caac-628">Sono disponibili provider di dettagli predefiniti per la disabilitazione dell'associazione di modelli o la convalida per i tipi specificati.</span><span class="sxs-lookup"><span data-stu-id="3caac-628">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="3caac-629">Per disabilitare l'associazione di modelli per tutti i modelli di un tipo specificato, aggiungere un <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3caac-629">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3caac-630">Ad esempio, per disabilitare l'associazione di modelli per tutti i modelli di tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="3caac-630">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="3caac-631">Per disabilitare la convalida per le proprietà di un tipo specificato, aggiungere un <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3caac-631">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3caac-632">Ad esempio, per disabilitare la convalida per le proprietà di tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="3caac-632">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="3caac-633">Strumenti di associazione di modelli personalizzati</span><span class="sxs-lookup"><span data-stu-id="3caac-633">Custom model binders</span></span>

<span data-ttu-id="3caac-634">È possibile estendere l'associazione di modelli scrivendo uno strumento di associazione di modelli personalizzato e usando l'attributo `[ModelBinder]` per selezionarlo per una determinata destinazione.</span><span class="sxs-lookup"><span data-stu-id="3caac-634">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="3caac-635">Altre informazioni sull'[associazione di modelli personalizzata](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="3caac-635">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="3caac-636">Associazione di modelli manuale</span><span class="sxs-lookup"><span data-stu-id="3caac-636">Manual model binding</span></span>

<span data-ttu-id="3caac-637">L'associazione di modelli può essere richiamata manualmente usando il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3caac-637">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="3caac-638">Il metodo è definito in entrambe le classi `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="3caac-638">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="3caac-639">Gli overload del metodo consentono di specificare il prefisso e il provider di valori da usare.</span><span class="sxs-lookup"><span data-stu-id="3caac-639">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="3caac-640">Il metodo restituisce `false` se l'associazione di modelli non riesce.</span><span class="sxs-lookup"><span data-stu-id="3caac-640">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="3caac-641">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="3caac-641">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="3caac-642">Attributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="3caac-642">[FromServices] attribute</span></span>

<span data-ttu-id="3caac-643">Il nome di questo attributo segue il modello degli attributi di associazione di modelli che specificano un'origine dati.</span><span class="sxs-lookup"><span data-stu-id="3caac-643">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="3caac-644">Non si tratta però dell'associazione di dati da un provider di valori.</span><span class="sxs-lookup"><span data-stu-id="3caac-644">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="3caac-645">Ottiene un'istanza di un tipo dal contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3caac-645">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3caac-646">Lo scopo è fornire un'alternativa all'inserimento del costruttore per quando è necessario un servizio solo se viene chiamato un metodo specifico.</span><span class="sxs-lookup"><span data-stu-id="3caac-646">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3caac-647">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3caac-647">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
