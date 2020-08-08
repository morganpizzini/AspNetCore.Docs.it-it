---
title: Associazione di modelli in ASP.NET Core
author: rick-anderson
description: Informazioni su come funziona l'associazione di modelli in ASP.NET Core e su come personalizzarne il comportamento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: 6ec531a04a220f75f5793cb2c7b5232908dbd883
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019157"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="999e8-103">Associazione di modelli in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="999e8-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="999e8-104">Questo articolo illustra cos'è l'associazione di modelli, come funziona e come personalizzarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="999e8-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="999e8-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="999e8-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="999e8-106">Che cos'è l'associazione di modelli</span><span class="sxs-lookup"><span data-stu-id="999e8-106">What is Model binding</span></span>

<span data-ttu-id="999e8-107">I controller e le Razor pagine funzionano con dati provenienti da richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="999e8-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="999e8-108">Ad esempio, i dati di route possono fornire una chiave del record e i campi modulo inviati possono fornire valori per le proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="999e8-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="999e8-109">La scrittura di codice per recuperare tutti i valori e convertirli da stringhe in tipi .NET sarebbe noiosa e soggetta a errori.</span><span class="sxs-lookup"><span data-stu-id="999e8-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="999e8-110">L'associazione di modelli consente di automatizzare questo processo.</span><span class="sxs-lookup"><span data-stu-id="999e8-110">Model binding automates this process.</span></span> <span data-ttu-id="999e8-111">Il sistema di associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="999e8-111">The model binding system:</span></span>

* <span data-ttu-id="999e8-112">Recupera i dati da diverse origini, ad esempio i dati di route, i campi modulo e le stringhe di query.</span><span class="sxs-lookup"><span data-stu-id="999e8-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="999e8-113">Fornisce i dati ai controller e alle Razor pagine nei parametri del metodo e nelle proprietà pubbliche.</span><span class="sxs-lookup"><span data-stu-id="999e8-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="999e8-114">Converte dati stringa in tipi .NET.</span><span class="sxs-lookup"><span data-stu-id="999e8-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="999e8-115">Aggiorna le proprietà dei tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="999e8-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="999e8-116">Esempio</span><span class="sxs-lookup"><span data-stu-id="999e8-116">Example</span></span>

<span data-ttu-id="999e8-117">Si supponga di avere il metodo di azione seguente:</span><span class="sxs-lookup"><span data-stu-id="999e8-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="999e8-118">E l'app riceve una richiesta con questo URL:</span><span class="sxs-lookup"><span data-stu-id="999e8-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="999e8-119">L'associazione di modelli esegue i passaggi seguenti dopo che il sistema di routing seleziona il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="999e8-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="999e8-120">Trova il primo parametro di `GetByID`, un intero denominato `id`.</span><span class="sxs-lookup"><span data-stu-id="999e8-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="999e8-121">Esamina le origini disponibili nella richiesta HTTP e trova `id` = "2" nei dati di route.</span><span class="sxs-lookup"><span data-stu-id="999e8-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="999e8-122">Converte la stringa "2" nell'intero 2.</span><span class="sxs-lookup"><span data-stu-id="999e8-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="999e8-123">Trova il parametro successivo di `GetByID`, un valore booleano denominato `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="999e8-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="999e8-124">Esamina le origini e trova "DogsOnly=true" nella stringa di query.</span><span class="sxs-lookup"><span data-stu-id="999e8-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="999e8-125">Per la corrispondenza dei nomi non viene applicata la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="999e8-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="999e8-126">Converte la stringa "true" nel valore booleano `true`.</span><span class="sxs-lookup"><span data-stu-id="999e8-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="999e8-127">Il framework chiama quindi il metodo `GetById`, passando 2 per il parametro `id` e `true` per il parametro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="999e8-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="999e8-128">Nell'esempio precedente le destinazioni dell'associazione di modelli sono parametri di metodo che sono tipi semplici.</span><span class="sxs-lookup"><span data-stu-id="999e8-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="999e8-129">Le destinazioni possono essere anche le proprietà di un tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="999e8-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="999e8-130">Dopo l'associazione di ogni proprietà, viene eseguita la [convalida dei modelli](xref:mvc/models/validation) per la proprietà.</span><span class="sxs-lookup"><span data-stu-id="999e8-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="999e8-131">Il record dei dati associati al modello e di eventuali errori di associazione o convalida viene archiviato in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oppure [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="999e8-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="999e8-132">Per scoprire se questo processo ha esito positivo, l'app controlla il flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="999e8-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="999e8-133">Destinazioni</span><span class="sxs-lookup"><span data-stu-id="999e8-133">Targets</span></span>

<span data-ttu-id="999e8-134">L'associazione di modelli cerca di trovare i valori per i tipi di destinazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="999e8-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="999e8-135">Parametri del metodo di azione del controller a cui viene indirizzata una richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="999e8-136">Parametri del Razor metodo del gestore pagine a cui viene indirizzata una richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="999e8-137">Proprietà pubbliche di un controller o una classe `PageModel`, se specificate dagli attributi.</span><span class="sxs-lookup"><span data-stu-id="999e8-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="999e8-138">Attributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="999e8-138">[BindProperty] attribute</span></span>

<span data-ttu-id="999e8-139">Può essere applicato a una proprietà pubblica di un controller o una classe `PageModel` per fare in modo che l'associazione di modelli usi tale proprietà come destinazione:</span><span class="sxs-lookup"><span data-stu-id="999e8-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="999e8-140">Attributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="999e8-140">[BindProperties] attribute</span></span>

<span data-ttu-id="999e8-141">Disponibile in ASP.NET Core 2.1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="999e8-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="999e8-142">Può essere applicato a un controller o una classe `PageModel` per indicare all'associazione del modello di usare tutte le proprietà pubbliche della classe come destinazione:</span><span class="sxs-lookup"><span data-stu-id="999e8-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="999e8-143">Associazione di modelli per le richieste HTTP GET</span><span class="sxs-lookup"><span data-stu-id="999e8-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="999e8-144">Per impostazione predefinita, le proprietà non vengono associate per le richieste HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="999e8-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="999e8-145">In genere, per una richiesta GET è sufficiente un parametro ID record.</span><span class="sxs-lookup"><span data-stu-id="999e8-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="999e8-146">L'ID record viene usato per cercare l'elemento nel database.</span><span class="sxs-lookup"><span data-stu-id="999e8-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="999e8-147">Pertanto, non è necessario associare una proprietà che contiene un'istanza del modello.</span><span class="sxs-lookup"><span data-stu-id="999e8-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="999e8-148">Negli scenari in cui si vogliono associare le proprietà ai dati dalle richieste GET, impostare la proprietà `SupportsGet` su `true`:</span><span class="sxs-lookup"><span data-stu-id="999e8-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="999e8-149">Origini</span><span class="sxs-lookup"><span data-stu-id="999e8-149">Sources</span></span>

<span data-ttu-id="999e8-150">Per impostazione predefinita, l'associazione di modelli ottiene i dati sotto forma di coppie chiave-valore dalle origini seguenti in una richiesta HTTP:</span><span class="sxs-lookup"><span data-stu-id="999e8-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="999e8-151">Campi modulo</span><span class="sxs-lookup"><span data-stu-id="999e8-151">Form fields</span></span>
1. <span data-ttu-id="999e8-152">Il corpo della richiesta (per i controller [ con l'attributo [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="999e8-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="999e8-153">Indirizzare i dati</span><span class="sxs-lookup"><span data-stu-id="999e8-153">Route data</span></span>
1. <span data-ttu-id="999e8-154">Parametri della stringa di query</span><span class="sxs-lookup"><span data-stu-id="999e8-154">Query string parameters</span></span>
1. <span data-ttu-id="999e8-155">File caricati</span><span class="sxs-lookup"><span data-stu-id="999e8-155">Uploaded files</span></span>

<span data-ttu-id="999e8-156">Per ogni parametro o proprietà di destinazione, le origini vengono analizzate nell'ordine indicato nell'elenco precedente.</span><span class="sxs-lookup"><span data-stu-id="999e8-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="999e8-157">Esistono tuttavia alcune eccezioni:</span><span class="sxs-lookup"><span data-stu-id="999e8-157">There are a few exceptions:</span></span>

* <span data-ttu-id="999e8-158">I dati di route e i valori delle stringhe di query vengono usati solo per i tipi semplici.</span><span class="sxs-lookup"><span data-stu-id="999e8-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="999e8-159">I file caricati vengono associati solo ai tipi di destinazione che implementano `IFormFile` o `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="999e8-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="999e8-160">Se l'origine predefinita non è corretta, usare uno degli attributi seguenti per specificare l'origine:</span><span class="sxs-lookup"><span data-stu-id="999e8-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="999e8-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)-Ottiene i valori dalla stringa di query.</span><span class="sxs-lookup"><span data-stu-id="999e8-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="999e8-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)-Ottiene i valori dai dati della route.</span><span class="sxs-lookup"><span data-stu-id="999e8-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="999e8-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Ottiene i valori dai campi del modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="999e8-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="999e8-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)-Ottiene i valori dal corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="999e8-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)-Ottiene i valori dalle intestazioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="999e8-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="999e8-166">Questi attributi:</span><span class="sxs-lookup"><span data-stu-id="999e8-166">These attributes:</span></span>

* <span data-ttu-id="999e8-167">Vengono aggiunti singolarmente alle proprietà del modello (non alla classe del modello), come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="999e8-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="999e8-168">Accettano facoltativamente un valore di nome di modello nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="999e8-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="999e8-169">Questa opzione è disponibile nel caso in cui il nome della proprietà non corrisponda al valore nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="999e8-170">Il valore nella richiesta, ad esempio, potrebbe essere un'intestazione con un segno meno nel nome, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="999e8-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="999e8-171">Attributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="999e8-171">[FromBody] attribute</span></span>

<span data-ttu-id="999e8-172">Applicare l' `[FromBody]` attributo a un parametro per popolarne le proprietà dal corpo di una richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="999e8-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="999e8-173">Il runtime di ASP.NET Core delega la responsabilità della lettura del corpo a un formattatore di input.</span><span class="sxs-lookup"><span data-stu-id="999e8-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="999e8-174">I formattatori di input sono descritti [più avanti in questo articolo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="999e8-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="999e8-175">Quando `[FromBody]` viene applicato a un parametro di tipo complesso, tutti gli attributi di origine di associazione applicati alle relative proprietà vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="999e8-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="999e8-176">Ad esempio, l' `Create` azione seguente specifica che il relativo `pet` parametro viene popolato dal corpo:</span><span class="sxs-lookup"><span data-stu-id="999e8-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="999e8-177">La `Pet` classe specifica che la `Breed` proprietà viene popolata da un parametro della stringa di query:</span><span class="sxs-lookup"><span data-stu-id="999e8-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="999e8-178">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="999e8-178">In the preceding example:</span></span>

* <span data-ttu-id="999e8-179">L' `[FromQuery]` attributo viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="999e8-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="999e8-180">La `Breed` proprietà non viene popolata da un parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="999e8-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="999e8-181">I formattatori di input leggono solo il corpo e non comprendono gli attributi di origine del binding.</span><span class="sxs-lookup"><span data-stu-id="999e8-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="999e8-182">Se nel corpo viene trovato un valore appropriato, tale valore viene usato per popolare la `Breed` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="999e8-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="999e8-183">Non applicare `[FromBody]` a più di un parametro per ogni metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="999e8-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="999e8-184">Una volta che il flusso di richiesta viene letto da un formattatore di input, non è più disponibile per la lettura per l'associazione di altri `[FromBody]` parametri.</span><span class="sxs-lookup"><span data-stu-id="999e8-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="999e8-185">Origini aggiuntive</span><span class="sxs-lookup"><span data-stu-id="999e8-185">Additional sources</span></span>

<span data-ttu-id="999e8-186">I dati di origine vengono forniti al sistema di associazione di modelli dai *provider di valori*.</span><span class="sxs-lookup"><span data-stu-id="999e8-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="999e8-187">È possibile scrivere e registrare i provider di valori personalizzati che recuperano i dati per l'associazione di modelli da altre origini.</span><span class="sxs-lookup"><span data-stu-id="999e8-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="999e8-188">Ad esempio, è possibile che si desiderino i dati cookie dello stato della sessione o.</span><span class="sxs-lookup"><span data-stu-id="999e8-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="999e8-189">Per ottenere dati da una nuova origine:</span><span class="sxs-lookup"><span data-stu-id="999e8-189">To get data from a new source:</span></span>

* <span data-ttu-id="999e8-190">Creare una classe che implementi `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="999e8-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="999e8-191">Creare una classe che implementi `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="999e8-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="999e8-192">Registrare la classe factory in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="999e8-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="999e8-193">L'app di esempio include un [provider di valori](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) e un esempio di [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) che ottiene i valori da cookie s.</span><span class="sxs-lookup"><span data-stu-id="999e8-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="999e8-194">Ecco il codice di registrazione in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="999e8-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="999e8-195">Il codice illustrato posiziona il provider di valori personalizzati dopo tutti i provider di valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="999e8-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="999e8-196">Per renderlo il primo nell'elenco, chiamare `Insert(0, new CookieValueProviderFactory())` invece di `Add`.</span><span class="sxs-lookup"><span data-stu-id="999e8-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="999e8-197">Nessuna origine per una proprietà del modello</span><span class="sxs-lookup"><span data-stu-id="999e8-197">No source for a model property</span></span>

<span data-ttu-id="999e8-198">Per impostazione predefinita, non viene creato un errore di stato del modello se non viene trovato alcun valore per una proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="999e8-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="999e8-199">La proprietà viene impostata su Null o su un valore predefinito:</span><span class="sxs-lookup"><span data-stu-id="999e8-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="999e8-200">I tipi semplici nullable vengono impostati su `null`.</span><span class="sxs-lookup"><span data-stu-id="999e8-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="999e8-201">I tipi valore non nullable vengono impostati su `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="999e8-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="999e8-202">Ad esempio, un parametro `int id` viene impostato su 0.</span><span class="sxs-lookup"><span data-stu-id="999e8-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="999e8-203">Per i tipi complessi, l'associazione di modelli crea un'istanza usando il costruttore predefinito, senza impostare proprietà.</span><span class="sxs-lookup"><span data-stu-id="999e8-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="999e8-204">Le matrici vengono impostate su `Array.Empty<T>()`, ad eccezione delle matrici `byte[]` che vengono impostate su `null`.</span><span class="sxs-lookup"><span data-stu-id="999e8-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="999e8-205">Se lo stato del modello deve essere invalidato quando non viene trovato alcun elemento nei campi del modulo per una proprietà del modello, usare l' [`[BindRequired]`](#bindrequired-attribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="999e8-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="999e8-206">Si noti che questo comportamento `[BindRequired]` si applica all'associazione di modelli dai dati di moduli inviati e non ai dati JSON o XML nel corpo di una richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="999e8-207">I dati del corpo della richiesta vengono gestiti dai [formattatori di input](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="999e8-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="999e8-208">Errori di conversione dei tipi</span><span class="sxs-lookup"><span data-stu-id="999e8-208">Type conversion errors</span></span>

<span data-ttu-id="999e8-209">Se viene trovata un'origine ma non può essere convertita nel tipo di destinazione, lo stato del modello viene contrassegnato come non valido.</span><span class="sxs-lookup"><span data-stu-id="999e8-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="999e8-210">Il parametro o la proprietà di destinazione viene impostato su Null o su un valore predefinito, come indicato nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="999e8-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="999e8-211">In un controller API con l'attributo `[ApiController]`, uno stato del modello non valido genera una risposta HTTP 400 automatica.</span><span class="sxs-lookup"><span data-stu-id="999e8-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="999e8-212">In una Razor pagina, visualizzare nuovamente la pagina con un messaggio di errore:</span><span class="sxs-lookup"><span data-stu-id="999e8-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="999e8-213">La convalida sul lato client rileva la maggior parte dei dati non validi che altrimenti verrebbero inviati a un Razor modulo di pagine.</span><span class="sxs-lookup"><span data-stu-id="999e8-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="999e8-214">Questa convalida rende difficile attivare il codice sopra evidenziato.</span><span class="sxs-lookup"><span data-stu-id="999e8-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="999e8-215">L'app di esempio include un pulsante **Submit with Invalid Date** (Invia con data non valida) che inserisce dati non validi nel campo **Hire Date** (Data assunzione) e invia il modulo.</span><span class="sxs-lookup"><span data-stu-id="999e8-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="999e8-216">Questo pulsante illustra il funzionamento del codice per visualizzare di nuovo la pagina quando si verificano errori di conversione dei dati.</span><span class="sxs-lookup"><span data-stu-id="999e8-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="999e8-217">Quando la pagina viene nuovamente visualizzata dal codice precedente, l'input non valido non viene visualizzato nel campo modulo.</span><span class="sxs-lookup"><span data-stu-id="999e8-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="999e8-218">Questo avviene perché la proprietà del modello è stata impostata su Null o su un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="999e8-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="999e8-219">L'input non valido viene visualizzato in un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="999e8-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="999e8-220">Ma se si vogliono visualizzare di nuovo i dati non validi nel campo modulo, è consigliabile impostare la proprietà del modello come stringa ed eseguire manualmente la conversione dei dati.</span><span class="sxs-lookup"><span data-stu-id="999e8-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="999e8-221">La stessa strategia è consigliata se si vuole evitare che gli errori di conversione del tipo causino errori di stato del modello.</span><span class="sxs-lookup"><span data-stu-id="999e8-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="999e8-222">In tal caso, impostare la proprietà del modello come stringa.</span><span class="sxs-lookup"><span data-stu-id="999e8-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="999e8-223">Tipi semplici</span><span class="sxs-lookup"><span data-stu-id="999e8-223">Simple types</span></span>

<span data-ttu-id="999e8-224">I tipi semplici in cui lo strumento di associazione di modelli può convertire le stringhe di origine includono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="999e8-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="999e8-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="999e8-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="999e8-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="999e8-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="999e8-227">Char</span><span class="sxs-lookup"><span data-stu-id="999e8-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="999e8-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="999e8-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="999e8-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="999e8-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="999e8-230">Decimale</span><span class="sxs-lookup"><span data-stu-id="999e8-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="999e8-231">Doppio</span><span class="sxs-lookup"><span data-stu-id="999e8-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="999e8-232">Enumerazione</span><span class="sxs-lookup"><span data-stu-id="999e8-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="999e8-233">GUID</span><span class="sxs-lookup"><span data-stu-id="999e8-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="999e8-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="999e8-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="999e8-235">Singolo</span><span class="sxs-lookup"><span data-stu-id="999e8-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="999e8-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="999e8-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="999e8-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="999e8-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="999e8-238">Uri</span><span class="sxs-lookup"><span data-stu-id="999e8-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="999e8-239">Version</span><span class="sxs-lookup"><span data-stu-id="999e8-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="999e8-240">Tipi complessi</span><span class="sxs-lookup"><span data-stu-id="999e8-240">Complex types</span></span>

<span data-ttu-id="999e8-241">Un tipo pubblico deve avere un costruttore predefinito pubblico e proprietà scrivibili pubbliche da associare.</span><span class="sxs-lookup"><span data-stu-id="999e8-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="999e8-242">Quando si verifica l'associazione di modelli, vengono create istanze della classe usando il costruttore predefinito pubblico.</span><span class="sxs-lookup"><span data-stu-id="999e8-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="999e8-243">Per ogni proprietà del tipo complesso, l'associazione di modelli cerca il modello di nome *prefisso.nome_proprietà* nelle origini.</span><span class="sxs-lookup"><span data-stu-id="999e8-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="999e8-244">Se non viene trovato, cerca semplicemente *nome_proprietà* senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="999e8-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="999e8-245">Per l'associazione a un parametro, il prefisso è il nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="999e8-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="999e8-246">Per l'associazione a una proprietà pubblica `PageModel`, il prefisso è il nome della proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="999e8-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="999e8-247">Alcuni attributi hanno una proprietà `Prefix` che consente di eseguire l'override dell'utilizzo predefinito del nome di un parametro o una proprietà.</span><span class="sxs-lookup"><span data-stu-id="999e8-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="999e8-248">Ad esempio, si supponga che il tipo complesso sia la classe `Instructor` seguente:</span><span class="sxs-lookup"><span data-stu-id="999e8-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="999e8-249">Prefisso = nome del parametro</span><span class="sxs-lookup"><span data-stu-id="999e8-249">Prefix = parameter name</span></span>

<span data-ttu-id="999e8-250">Se il modello da associare è un parametro denominato `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="999e8-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="999e8-251">L'associazione di modelli cerca prima di tutto la chiave `instructorToUpdate.ID` nelle origini.</span><span class="sxs-lookup"><span data-stu-id="999e8-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="999e8-252">Se non viene trovata, cerca `ID` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="999e8-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="999e8-253">Prefisso = nome della proprietà</span><span class="sxs-lookup"><span data-stu-id="999e8-253">Prefix = property name</span></span>

<span data-ttu-id="999e8-254">Se il modello da associare è una proprietà denominata `Instructor` del controller o della classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="999e8-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="999e8-255">L'associazione di modelli cerca prima di tutto la chiave `Instructor.ID` nelle origini.</span><span class="sxs-lookup"><span data-stu-id="999e8-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="999e8-256">Se non viene trovata, cerca `ID` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="999e8-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="999e8-257">Prefisso personalizzato</span><span class="sxs-lookup"><span data-stu-id="999e8-257">Custom prefix</span></span>

<span data-ttu-id="999e8-258">Se il modello da associare è un parametro denominato `instructorToUpdate` e un attributo `Bind` specifica `Instructor` come prefisso:</span><span class="sxs-lookup"><span data-stu-id="999e8-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="999e8-259">L'associazione di modelli cerca prima di tutto la chiave `Instructor.ID` nelle origini.</span><span class="sxs-lookup"><span data-stu-id="999e8-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="999e8-260">Se non viene trovata, cerca `ID` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="999e8-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="999e8-261">Attributi per le destinazioni di tipo complesso</span><span class="sxs-lookup"><span data-stu-id="999e8-261">Attributes for complex type targets</span></span>

<span data-ttu-id="999e8-262">Sono disponibili vari attributi predefiniti per controllare l'associazione di modelli di tipi complessi:</span><span class="sxs-lookup"><span data-stu-id="999e8-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="999e8-263">Questi attributi influiscono sul modello di associazione quando i dati di modulo inviati sono l'origine dei valori.</span><span class="sxs-lookup"><span data-stu-id="999e8-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="999e8-264">Non influiscono sui formattatori di input, che elaborano corpi di richieste JSON e XML inviati.</span><span class="sxs-lookup"><span data-stu-id="999e8-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="999e8-265">I formattatori di input sono descritti [più avanti in questo articolo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="999e8-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="999e8-266">Vedere anche la discussione relativa all'attributo `[Required]` in [Convalida del modello](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="999e8-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="999e8-267">Attributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="999e8-267">[BindRequired] attribute</span></span>

<span data-ttu-id="999e8-268">Può essere applicato solo alle proprietà del modello e non ai parametri di metodo.</span><span class="sxs-lookup"><span data-stu-id="999e8-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="999e8-269">Con questo attributo l'associazione di modelli aggiunge un errore di stato del modello se non è possibile eseguire l'associazione per una proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="999e8-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="999e8-270">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="999e8-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="999e8-271">Attributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="999e8-271">[BindNever] attribute</span></span>

<span data-ttu-id="999e8-272">Può essere applicato solo alle proprietà del modello e non ai parametri di metodo.</span><span class="sxs-lookup"><span data-stu-id="999e8-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="999e8-273">Impedisce all'associazione di modelli di impostare una proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="999e8-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="999e8-274">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="999e8-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="999e8-275">Attributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="999e8-275">[Bind] attribute</span></span>

<span data-ttu-id="999e8-276">Può essere applicato a una classe o a un parametro di metodo.</span><span class="sxs-lookup"><span data-stu-id="999e8-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="999e8-277">Specifica quali proprietà di un modello devono essere incluse nell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="999e8-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="999e8-278">Nell'esempio seguente vengono associate solo le proprietà specificate del modello `Instructor` quando viene chiamato qualsiasi gestore o metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="999e8-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="999e8-279">Nell'esempio seguente vengono associate solo le proprietà specificate del modello `Instructor` quando viene chiamato il metodo `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="999e8-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="999e8-280">L'attributo `[Bind]` può essere usato per evitare l'overposting negli scenari di *creazione*.</span><span class="sxs-lookup"><span data-stu-id="999e8-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="999e8-281">Non funziona bene negli scenari di modifica perché le proprietà escluse vengono impostate su Null o su un valore predefinito anziché rimanere inalterate.</span><span class="sxs-lookup"><span data-stu-id="999e8-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="999e8-282">Per difendersi dall'overposting, sono consigliati i modelli di visualizzazione anziché l'attributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="999e8-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="999e8-283">Per altre informazioni, vedere [Nota sulla sicurezza relativa all'overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="999e8-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="999e8-284">Raccolte</span><span class="sxs-lookup"><span data-stu-id="999e8-284">Collections</span></span>

<span data-ttu-id="999e8-285">Per le destinazioni che sono raccolte di tipi semplici, l'associazione di modelli cerca le corrispondenze per *nome_parametro* oppure *nome_proprietà*.</span><span class="sxs-lookup"><span data-stu-id="999e8-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="999e8-286">Se non viene trovata alcuna corrispondenza, viene cercato uno dei formati supportati senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="999e8-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="999e8-287">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="999e8-287">For example:</span></span>

* <span data-ttu-id="999e8-288">Si supponga che il parametro da associare sia una matrice denominata `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="999e8-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="999e8-289">I dati di modulo o di stringhe di query possono essere in uno dei formati seguenti:</span><span class="sxs-lookup"><span data-stu-id="999e8-289">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="999e8-290">Il formato seguente è supportato solo nei dati di modulo:</span><span class="sxs-lookup"><span data-stu-id="999e8-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="999e8-291">Per tutti i formati di esempio precedenti, l'associazione di modelli passa una matrice di due elementi al parametro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="999e8-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="999e8-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="999e8-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="999e8-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="999e8-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="999e8-294">Per i formati di dati che usano numeri con indice (... [0]... [1]...) è necessario assicurarsi che la numerazione sia progressiva a partire da zero.</span><span class="sxs-lookup"><span data-stu-id="999e8-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="999e8-295">Se sono presenti eventuali interruzioni nella numerazione con indice, tutti gli elementi dopo l'interruzione vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="999e8-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="999e8-296">Ad esempio, se gli indici sono 0 e 2 anziché 0 e 1, il secondo elemento viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="999e8-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="999e8-297">Dizionari</span><span class="sxs-lookup"><span data-stu-id="999e8-297">Dictionaries</span></span>

<span data-ttu-id="999e8-298">Per le destinazioni `Dictionary`, l'associazione di modelli cerca le corrispondenze per *nome_parametro* oppure *nome_proprietà*.</span><span class="sxs-lookup"><span data-stu-id="999e8-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="999e8-299">Se non viene trovata alcuna corrispondenza, viene cercato uno dei formati supportati senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="999e8-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="999e8-300">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="999e8-300">For example:</span></span>

* <span data-ttu-id="999e8-301">Si supponga che il parametro di destinazione sia un elemento `Dictionary<int, string>` denominato `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="999e8-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="999e8-302">I dati di modulo o di stringhe di query inviati possono essere simili a uno degli esempi seguenti:</span><span class="sxs-lookup"><span data-stu-id="999e8-302">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="999e8-303">Per tutti i formati di esempio precedenti, l'associazione di modelli passa un dizionario di due elementi al parametro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="999e8-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="999e8-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="999e8-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="999e8-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="999e8-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="999e8-306">Comportamento di globalizzazione dell'associazione di modelli dati della route e stringhe di query</span><span class="sxs-lookup"><span data-stu-id="999e8-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="999e8-307">Il provider del valore della route ASP.NET Core e il provider del valore della stringa di query:</span><span class="sxs-lookup"><span data-stu-id="999e8-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="999e8-308">Considera i valori come impostazioni cultura invarianti.</span><span class="sxs-lookup"><span data-stu-id="999e8-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="999e8-309">Si prevede che gli URL siano impostazioni cultura invarianti.</span><span class="sxs-lookup"><span data-stu-id="999e8-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="999e8-310">Al contrario, i valori provenienti dai dati del form subiscono una conversione con distinzione delle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="999e8-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="999e8-311">Questa operazione è progettata in modo che gli URL siano condivisibili tra le impostazioni locali.</span><span class="sxs-lookup"><span data-stu-id="999e8-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="999e8-312">Per rendere il provider del valore di route ASP.NET Core e il provider di valori della stringa di query sottoposti a una conversione dipendente dalle impostazioni cultura:</span><span class="sxs-lookup"><span data-stu-id="999e8-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="999e8-313">Ereditano da <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="999e8-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="999e8-314">Copiare il codice da [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) o [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="999e8-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="999e8-315">Sostituire il [valore delle impostazioni cultura](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passato al costruttore del provider di valori con [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="999e8-315">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="999e8-316">Sostituire la factory del provider di valori predefinito in opzioni MVC con la nuova:</span><span class="sxs-lookup"><span data-stu-id="999e8-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="999e8-317">Tipi di dati speciali</span><span class="sxs-lookup"><span data-stu-id="999e8-317">Special data types</span></span>

<span data-ttu-id="999e8-318">Esistono alcuni tipi di dati speciali che l'associazione di modelli può gestire.</span><span class="sxs-lookup"><span data-stu-id="999e8-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="999e8-319">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="999e8-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="999e8-320">Un file caricato incluso nella richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="999e8-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="999e8-321">È anche supportato `IEnumerable<IFormFile>` per più file.</span><span class="sxs-lookup"><span data-stu-id="999e8-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="999e8-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="999e8-322">CancellationToken</span></span>

<span data-ttu-id="999e8-323">usato per annullare l'attività nei controller asincroni.</span><span class="sxs-lookup"><span data-stu-id="999e8-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="999e8-324">FormCollection</span><span class="sxs-lookup"><span data-stu-id="999e8-324">FormCollection</span></span>

<span data-ttu-id="999e8-325">Usato per recuperare tutti i valori dai dati di modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="999e8-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="999e8-326">Formattatori di input</span><span class="sxs-lookup"><span data-stu-id="999e8-326">Input formatters</span></span>

<span data-ttu-id="999e8-327">I dati nel corpo della richiesta possono essere in formato JSON, XML o in altri formati.</span><span class="sxs-lookup"><span data-stu-id="999e8-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="999e8-328">Per analizzare questi dati, l'associazione di modelli usa un *formattatore di input* configurato in modo da gestire un particolare tipo di contenuto.</span><span class="sxs-lookup"><span data-stu-id="999e8-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="999e8-329">Per impostazione predefinita, ASP.NET Core include i formattatori di input basati su JSON per la gestione dei dati JSON.</span><span class="sxs-lookup"><span data-stu-id="999e8-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="999e8-330">È possibile aggiungere altri formattatori per altri tipi di contenuto.</span><span class="sxs-lookup"><span data-stu-id="999e8-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="999e8-331">ASP.NET Core consente di selezionare i formattatori di input in base all'attributo [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="999e8-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="999e8-332">Se non è presente alcun attributo, viene usata l'[intestazione Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="999e8-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="999e8-333">Per usare i formattatori di input XML predefiniti:</span><span class="sxs-lookup"><span data-stu-id="999e8-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="999e8-334">Installare il pacchetto NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="999e8-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="999e8-335">In `Startup.ConfigureServices` chiamare <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> o <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="999e8-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="999e8-336">Applicare l'attributo `Consumes` alle classi controller o ai metodi di azione che devono aspettarsi XML nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="999e8-337">Per altre informazioni, vedere [Introduzione alla serializzazione XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="999e8-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="999e8-338">Personalizzare l'associazione di modelli con formattatori di input</span><span class="sxs-lookup"><span data-stu-id="999e8-338">Customize model binding with input formatters</span></span>

<span data-ttu-id="999e8-339">Un formattatore di input assume la piena responsabilità per la lettura dei dati dal corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-339">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="999e8-340">Per personalizzare questo processo, configurare le API usate dal formattatore di input.</span><span class="sxs-lookup"><span data-stu-id="999e8-340">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="999e8-341">In questa sezione viene descritto come personalizzare il `System.Text.Json` formattatore di input basato su per comprendere un tipo personalizzato denominato `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="999e8-341">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="999e8-342">Si consideri il modello seguente, che contiene una `ObjectId` proprietà personalizzata denominata `Id` :</span><span class="sxs-lookup"><span data-stu-id="999e8-342">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="999e8-343">Per personalizzare il processo di associazione di modelli quando si usa `System.Text.Json` , creare una classe derivata da <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="999e8-343">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="999e8-344">Per usare un convertitore personalizzato, applicare l' <xref:System.Text.Json.Serialization.JsonConverterAttribute> attributo al tipo.</span><span class="sxs-lookup"><span data-stu-id="999e8-344">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="999e8-345">Nell'esempio seguente il `ObjectId` tipo viene configurato con `ObjectIdConverter` come convertitore personalizzato:</span><span class="sxs-lookup"><span data-stu-id="999e8-345">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="999e8-346">Per ulteriori informazioni, vedere [come scrivere convertitori personalizzati](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="999e8-346">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="999e8-347">Escludere i tipi specificati dall'associazione di modelli</span><span class="sxs-lookup"><span data-stu-id="999e8-347">Exclude specified types from model binding</span></span>

<span data-ttu-id="999e8-348">Il comportamento dell'associazione di modelli e del sistema di convalida è determinato da [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="999e8-348">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="999e8-349">È possibile personalizzare `ModelMetadata` mediante l'aggiunta di un provider di dettagli a [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="999e8-349">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="999e8-350">Sono disponibili provider di dettagli predefiniti per la disabilitazione dell'associazione di modelli o la convalida per i tipi specificati.</span><span class="sxs-lookup"><span data-stu-id="999e8-350">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="999e8-351">Per disabilitare l'associazione di modelli per tutti i modelli di un tipo specificato, aggiungere un <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="999e8-351">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="999e8-352">Ad esempio, per disabilitare l'associazione di modelli per tutti i modelli di tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="999e8-352">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="999e8-353">Per disabilitare la convalida per le proprietà di un tipo specificato, aggiungere un <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="999e8-353">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="999e8-354">Ad esempio, per disabilitare la convalida per le proprietà di tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="999e8-354">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="999e8-355">Strumenti di associazione di modelli personalizzati</span><span class="sxs-lookup"><span data-stu-id="999e8-355">Custom model binders</span></span>

<span data-ttu-id="999e8-356">È possibile estendere l'associazione di modelli scrivendo uno strumento di associazione di modelli personalizzato e usando l'attributo `[ModelBinder]` per selezionarlo per una determinata destinazione.</span><span class="sxs-lookup"><span data-stu-id="999e8-356">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="999e8-357">Altre informazioni sull'[associazione di modelli personalizzata](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="999e8-357">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="999e8-358">Associazione di modelli manuale</span><span class="sxs-lookup"><span data-stu-id="999e8-358">Manual model binding</span></span> 

<span data-ttu-id="999e8-359">L'associazione di modelli può essere richiamata manualmente usando il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="999e8-359">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="999e8-360">Il metodo è definito in entrambe le classi `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="999e8-360">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="999e8-361">Gli overload del metodo consentono di specificare il prefisso e il provider di valori da usare.</span><span class="sxs-lookup"><span data-stu-id="999e8-361">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="999e8-362">Il metodo restituisce `false` se l'associazione di modelli non riesce.</span><span class="sxs-lookup"><span data-stu-id="999e8-362">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="999e8-363">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="999e8-363">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="999e8-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>USA i provider di valori per ottenere i dati dal corpo del form, dalla stringa di query e dai dati della route.</span><span class="sxs-lookup"><span data-stu-id="999e8-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="999e8-365">`TryUpdateModelAsync`è in genere:</span><span class="sxs-lookup"><span data-stu-id="999e8-365">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="999e8-366">Usato con le Razor pagine e le app MVC che usano i controller e le visualizzazioni per impedire l'overposting.</span><span class="sxs-lookup"><span data-stu-id="999e8-366">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="999e8-367">Non utilizzato con un'API Web, a meno che non venga utilizzato da dati del modulo, stringhe di query e dati di route.</span><span class="sxs-lookup"><span data-stu-id="999e8-367">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="999e8-368">Gli endpoint dell'API Web che utilizzano JSON utilizzano [formattatori di input](#input-formatters) per deserializzare il corpo della richiesta in un oggetto.</span><span class="sxs-lookup"><span data-stu-id="999e8-368">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="999e8-369">Per ulteriori informazioni, vedere [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="999e8-369">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="999e8-370">Attributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="999e8-370">[FromServices] attribute</span></span>

<span data-ttu-id="999e8-371">Il nome di questo attributo segue il modello degli attributi di associazione di modelli che specificano un'origine dati.</span><span class="sxs-lookup"><span data-stu-id="999e8-371">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="999e8-372">Non si tratta però dell'associazione di dati da un provider di valori.</span><span class="sxs-lookup"><span data-stu-id="999e8-372">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="999e8-373">Ottiene un'istanza di un tipo dal contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="999e8-373">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="999e8-374">Lo scopo è fornire un'alternativa all'inserimento del costruttore per quando è necessario un servizio solo se viene chiamato un metodo specifico.</span><span class="sxs-lookup"><span data-stu-id="999e8-374">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="999e8-375">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="999e8-375">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="999e8-376">Questo articolo illustra cos'è l'associazione di modelli, come funziona e come personalizzarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="999e8-376">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="999e8-377">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="999e8-377">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="999e8-378">Che cos'è l'associazione di modelli</span><span class="sxs-lookup"><span data-stu-id="999e8-378">What is Model binding</span></span>

<span data-ttu-id="999e8-379">I controller e le Razor pagine funzionano con dati provenienti da richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="999e8-379">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="999e8-380">Ad esempio, i dati di route possono fornire una chiave del record e i campi modulo inviati possono fornire valori per le proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="999e8-380">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="999e8-381">La scrittura di codice per recuperare tutti i valori e convertirli da stringhe in tipi .NET sarebbe noiosa e soggetta a errori.</span><span class="sxs-lookup"><span data-stu-id="999e8-381">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="999e8-382">L'associazione di modelli consente di automatizzare questo processo.</span><span class="sxs-lookup"><span data-stu-id="999e8-382">Model binding automates this process.</span></span> <span data-ttu-id="999e8-383">Il sistema di associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="999e8-383">The model binding system:</span></span>

* <span data-ttu-id="999e8-384">Recupera i dati da diverse origini, ad esempio i dati di route, i campi modulo e le stringhe di query.</span><span class="sxs-lookup"><span data-stu-id="999e8-384">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="999e8-385">Fornisce i dati ai controller e alle Razor pagine nei parametri del metodo e nelle proprietà pubbliche.</span><span class="sxs-lookup"><span data-stu-id="999e8-385">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="999e8-386">Converte dati stringa in tipi .NET.</span><span class="sxs-lookup"><span data-stu-id="999e8-386">Converts string data to .NET types.</span></span>
* <span data-ttu-id="999e8-387">Aggiorna le proprietà dei tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="999e8-387">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="999e8-388">Esempio</span><span class="sxs-lookup"><span data-stu-id="999e8-388">Example</span></span>

<span data-ttu-id="999e8-389">Si supponga di avere il metodo di azione seguente:</span><span class="sxs-lookup"><span data-stu-id="999e8-389">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="999e8-390">E l'app riceve una richiesta con questo URL:</span><span class="sxs-lookup"><span data-stu-id="999e8-390">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="999e8-391">L'associazione di modelli esegue i passaggi seguenti dopo che il sistema di routing seleziona il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="999e8-391">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="999e8-392">Trova il primo parametro di `GetByID`, un intero denominato `id`.</span><span class="sxs-lookup"><span data-stu-id="999e8-392">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="999e8-393">Esamina le origini disponibili nella richiesta HTTP e trova `id` = "2" nei dati di route.</span><span class="sxs-lookup"><span data-stu-id="999e8-393">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="999e8-394">Converte la stringa "2" nell'intero 2.</span><span class="sxs-lookup"><span data-stu-id="999e8-394">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="999e8-395">Trova il parametro successivo di `GetByID`, un valore booleano denominato `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="999e8-395">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="999e8-396">Esamina le origini e trova "DogsOnly=true" nella stringa di query.</span><span class="sxs-lookup"><span data-stu-id="999e8-396">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="999e8-397">Per la corrispondenza dei nomi non viene applicata la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="999e8-397">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="999e8-398">Converte la stringa "true" nel valore booleano `true`.</span><span class="sxs-lookup"><span data-stu-id="999e8-398">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="999e8-399">Il framework chiama quindi il metodo `GetById`, passando 2 per il parametro `id` e `true` per il parametro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="999e8-399">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="999e8-400">Nell'esempio precedente le destinazioni dell'associazione di modelli sono parametri di metodo che sono tipi semplici.</span><span class="sxs-lookup"><span data-stu-id="999e8-400">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="999e8-401">Le destinazioni possono essere anche le proprietà di un tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="999e8-401">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="999e8-402">Dopo l'associazione di ogni proprietà, viene eseguita la [convalida dei modelli](xref:mvc/models/validation) per la proprietà.</span><span class="sxs-lookup"><span data-stu-id="999e8-402">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="999e8-403">Il record dei dati associati al modello e di eventuali errori di associazione o convalida viene archiviato in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oppure [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="999e8-403">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="999e8-404">Per scoprire se questo processo ha esito positivo, l'app controlla il flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="999e8-404">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="999e8-405">Destinazioni</span><span class="sxs-lookup"><span data-stu-id="999e8-405">Targets</span></span>

<span data-ttu-id="999e8-406">L'associazione di modelli cerca di trovare i valori per i tipi di destinazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="999e8-406">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="999e8-407">Parametri del metodo di azione del controller a cui viene indirizzata una richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-407">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="999e8-408">Parametri del Razor metodo del gestore pagine a cui viene indirizzata una richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-408">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="999e8-409">Proprietà pubbliche di un controller o una classe `PageModel`, se specificate dagli attributi.</span><span class="sxs-lookup"><span data-stu-id="999e8-409">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="999e8-410">Attributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="999e8-410">[BindProperty] attribute</span></span>

<span data-ttu-id="999e8-411">Può essere applicato a una proprietà pubblica di un controller o una classe `PageModel` per fare in modo che l'associazione di modelli usi tale proprietà come destinazione:</span><span class="sxs-lookup"><span data-stu-id="999e8-411">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="999e8-412">Attributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="999e8-412">[BindProperties] attribute</span></span>

<span data-ttu-id="999e8-413">Disponibile in ASP.NET Core 2.1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="999e8-413">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="999e8-414">Può essere applicato a un controller o una classe `PageModel` per indicare all'associazione del modello di usare tutte le proprietà pubbliche della classe come destinazione:</span><span class="sxs-lookup"><span data-stu-id="999e8-414">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="999e8-415">Associazione di modelli per le richieste HTTP GET</span><span class="sxs-lookup"><span data-stu-id="999e8-415">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="999e8-416">Per impostazione predefinita, le proprietà non vengono associate per le richieste HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="999e8-416">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="999e8-417">In genere, per una richiesta GET è sufficiente un parametro ID record.</span><span class="sxs-lookup"><span data-stu-id="999e8-417">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="999e8-418">L'ID record viene usato per cercare l'elemento nel database.</span><span class="sxs-lookup"><span data-stu-id="999e8-418">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="999e8-419">Pertanto, non è necessario associare una proprietà che contiene un'istanza del modello.</span><span class="sxs-lookup"><span data-stu-id="999e8-419">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="999e8-420">Negli scenari in cui si vogliono associare le proprietà ai dati dalle richieste GET, impostare la proprietà `SupportsGet` su `true`:</span><span class="sxs-lookup"><span data-stu-id="999e8-420">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="999e8-421">Origini</span><span class="sxs-lookup"><span data-stu-id="999e8-421">Sources</span></span>

<span data-ttu-id="999e8-422">Per impostazione predefinita, l'associazione di modelli ottiene i dati sotto forma di coppie chiave-valore dalle origini seguenti in una richiesta HTTP:</span><span class="sxs-lookup"><span data-stu-id="999e8-422">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="999e8-423">Campi modulo</span><span class="sxs-lookup"><span data-stu-id="999e8-423">Form fields</span></span>
1. <span data-ttu-id="999e8-424">Il corpo della richiesta (per i controller [ con l'attributo [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="999e8-424">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="999e8-425">Indirizzare i dati</span><span class="sxs-lookup"><span data-stu-id="999e8-425">Route data</span></span>
1. <span data-ttu-id="999e8-426">Parametri della stringa di query</span><span class="sxs-lookup"><span data-stu-id="999e8-426">Query string parameters</span></span>
1. <span data-ttu-id="999e8-427">File caricati</span><span class="sxs-lookup"><span data-stu-id="999e8-427">Uploaded files</span></span>

<span data-ttu-id="999e8-428">Per ogni parametro o proprietà di destinazione, le origini vengono analizzate nell'ordine indicato nell'elenco precedente.</span><span class="sxs-lookup"><span data-stu-id="999e8-428">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="999e8-429">Esistono tuttavia alcune eccezioni:</span><span class="sxs-lookup"><span data-stu-id="999e8-429">There are a few exceptions:</span></span>

* <span data-ttu-id="999e8-430">I dati di route e i valori delle stringhe di query vengono usati solo per i tipi semplici.</span><span class="sxs-lookup"><span data-stu-id="999e8-430">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="999e8-431">I file caricati vengono associati solo ai tipi di destinazione che implementano `IFormFile` o `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="999e8-431">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="999e8-432">Se l'origine predefinita non è corretta, usare uno degli attributi seguenti per specificare l'origine:</span><span class="sxs-lookup"><span data-stu-id="999e8-432">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="999e8-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)-Ottiene i valori dalla stringa di query.</span><span class="sxs-lookup"><span data-stu-id="999e8-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="999e8-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)-Ottiene i valori dai dati della route.</span><span class="sxs-lookup"><span data-stu-id="999e8-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="999e8-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Ottiene i valori dai campi del modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="999e8-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="999e8-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)-Ottiene i valori dal corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="999e8-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)-Ottiene i valori dalle intestazioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="999e8-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="999e8-438">Questi attributi:</span><span class="sxs-lookup"><span data-stu-id="999e8-438">These attributes:</span></span>

* <span data-ttu-id="999e8-439">Vengono aggiunti singolarmente alle proprietà del modello (non alla classe del modello), come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="999e8-439">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="999e8-440">Accettano facoltativamente un valore di nome di modello nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="999e8-440">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="999e8-441">Questa opzione è disponibile nel caso in cui il nome della proprietà non corrisponda al valore nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-441">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="999e8-442">Il valore nella richiesta, ad esempio, potrebbe essere un'intestazione con un segno meno nel nome, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="999e8-442">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="999e8-443">Attributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="999e8-443">[FromBody] attribute</span></span>

<span data-ttu-id="999e8-444">Applicare l' `[FromBody]` attributo a un parametro per popolarne le proprietà dal corpo di una richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="999e8-444">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="999e8-445">Il runtime di ASP.NET Core delega la responsabilità della lettura del corpo a un formattatore di input.</span><span class="sxs-lookup"><span data-stu-id="999e8-445">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="999e8-446">I formattatori di input sono descritti [più avanti in questo articolo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="999e8-446">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="999e8-447">Quando `[FromBody]` viene applicato a un parametro di tipo complesso, tutti gli attributi di origine di associazione applicati alle relative proprietà vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="999e8-447">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="999e8-448">Ad esempio, l' `Create` azione seguente specifica che il relativo `pet` parametro viene popolato dal corpo:</span><span class="sxs-lookup"><span data-stu-id="999e8-448">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="999e8-449">La `Pet` classe specifica che la `Breed` proprietà viene popolata da un parametro della stringa di query:</span><span class="sxs-lookup"><span data-stu-id="999e8-449">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="999e8-450">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="999e8-450">In the preceding example:</span></span>

* <span data-ttu-id="999e8-451">L' `[FromQuery]` attributo viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="999e8-451">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="999e8-452">La `Breed` proprietà non viene popolata da un parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="999e8-452">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="999e8-453">I formattatori di input leggono solo il corpo e non comprendono gli attributi di origine del binding.</span><span class="sxs-lookup"><span data-stu-id="999e8-453">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="999e8-454">Se nel corpo viene trovato un valore appropriato, tale valore viene usato per popolare la `Breed` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="999e8-454">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="999e8-455">Non applicare `[FromBody]` a più di un parametro per ogni metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="999e8-455">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="999e8-456">Una volta che il flusso di richiesta viene letto da un formattatore di input, non è più disponibile per la lettura per l'associazione di altri `[FromBody]` parametri.</span><span class="sxs-lookup"><span data-stu-id="999e8-456">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="999e8-457">Origini aggiuntive</span><span class="sxs-lookup"><span data-stu-id="999e8-457">Additional sources</span></span>

<span data-ttu-id="999e8-458">I dati di origine vengono forniti al sistema di associazione di modelli dai *provider di valori*.</span><span class="sxs-lookup"><span data-stu-id="999e8-458">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="999e8-459">È possibile scrivere e registrare i provider di valori personalizzati che recuperano i dati per l'associazione di modelli da altre origini.</span><span class="sxs-lookup"><span data-stu-id="999e8-459">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="999e8-460">Ad esempio, è possibile che si desiderino i dati cookie dello stato della sessione o.</span><span class="sxs-lookup"><span data-stu-id="999e8-460">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="999e8-461">Per ottenere dati da una nuova origine:</span><span class="sxs-lookup"><span data-stu-id="999e8-461">To get data from a new source:</span></span>

* <span data-ttu-id="999e8-462">Creare una classe che implementi `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="999e8-462">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="999e8-463">Creare una classe che implementi `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="999e8-463">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="999e8-464">Registrare la classe factory in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="999e8-464">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="999e8-465">L'app di esempio include un [provider di valori](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) e un esempio di [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) che ottiene i valori da cookie s.</span><span class="sxs-lookup"><span data-stu-id="999e8-465">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="999e8-466">Ecco il codice di registrazione in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="999e8-466">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="999e8-467">Il codice illustrato posiziona il provider di valori personalizzati dopo tutti i provider di valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="999e8-467">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="999e8-468">Per renderlo il primo nell'elenco, chiamare `Insert(0, new CookieValueProviderFactory())` invece di `Add`.</span><span class="sxs-lookup"><span data-stu-id="999e8-468">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="999e8-469">Nessuna origine per una proprietà del modello</span><span class="sxs-lookup"><span data-stu-id="999e8-469">No source for a model property</span></span>

<span data-ttu-id="999e8-470">Per impostazione predefinita, non viene creato un errore di stato del modello se non viene trovato alcun valore per una proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="999e8-470">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="999e8-471">La proprietà viene impostata su Null o su un valore predefinito:</span><span class="sxs-lookup"><span data-stu-id="999e8-471">The property is set to null or a default value:</span></span>

* <span data-ttu-id="999e8-472">I tipi semplici nullable vengono impostati su `null`.</span><span class="sxs-lookup"><span data-stu-id="999e8-472">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="999e8-473">I tipi valore non nullable vengono impostati su `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="999e8-473">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="999e8-474">Ad esempio, un parametro `int id` viene impostato su 0.</span><span class="sxs-lookup"><span data-stu-id="999e8-474">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="999e8-475">Per i tipi complessi, l'associazione di modelli crea un'istanza usando il costruttore predefinito, senza impostare proprietà.</span><span class="sxs-lookup"><span data-stu-id="999e8-475">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="999e8-476">Le matrici vengono impostate su `Array.Empty<T>()`, ad eccezione delle matrici `byte[]` che vengono impostate su `null`.</span><span class="sxs-lookup"><span data-stu-id="999e8-476">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="999e8-477">Se lo stato del modello deve essere invalidato quando non viene trovato alcun elemento nei campi del modulo per una proprietà del modello, usare l' [`[BindRequired]`](#bindrequired-attribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="999e8-477">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="999e8-478">Si noti che questo comportamento `[BindRequired]` si applica all'associazione di modelli dai dati di moduli inviati e non ai dati JSON o XML nel corpo di una richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-478">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="999e8-479">I dati del corpo della richiesta vengono gestiti dai [formattatori di input](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="999e8-479">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="999e8-480">Errori di conversione dei tipi</span><span class="sxs-lookup"><span data-stu-id="999e8-480">Type conversion errors</span></span>

<span data-ttu-id="999e8-481">Se viene trovata un'origine ma non può essere convertita nel tipo di destinazione, lo stato del modello viene contrassegnato come non valido.</span><span class="sxs-lookup"><span data-stu-id="999e8-481">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="999e8-482">Il parametro o la proprietà di destinazione viene impostato su Null o su un valore predefinito, come indicato nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="999e8-482">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="999e8-483">In un controller API con l'attributo `[ApiController]`, uno stato del modello non valido genera una risposta HTTP 400 automatica.</span><span class="sxs-lookup"><span data-stu-id="999e8-483">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="999e8-484">In una Razor pagina, visualizzare nuovamente la pagina con un messaggio di errore:</span><span class="sxs-lookup"><span data-stu-id="999e8-484">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="999e8-485">La convalida sul lato client rileva la maggior parte dei dati non validi che altrimenti verrebbero inviati a un Razor modulo di pagine.</span><span class="sxs-lookup"><span data-stu-id="999e8-485">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="999e8-486">Questa convalida rende difficile attivare il codice sopra evidenziato.</span><span class="sxs-lookup"><span data-stu-id="999e8-486">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="999e8-487">L'app di esempio include un pulsante **Submit with Invalid Date** (Invia con data non valida) che inserisce dati non validi nel campo **Hire Date** (Data assunzione) e invia il modulo.</span><span class="sxs-lookup"><span data-stu-id="999e8-487">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="999e8-488">Questo pulsante illustra il funzionamento del codice per visualizzare di nuovo la pagina quando si verificano errori di conversione dei dati.</span><span class="sxs-lookup"><span data-stu-id="999e8-488">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="999e8-489">Quando la pagina viene nuovamente visualizzata dal codice precedente, l'input non valido non viene visualizzato nel campo modulo.</span><span class="sxs-lookup"><span data-stu-id="999e8-489">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="999e8-490">Questo avviene perché la proprietà del modello è stata impostata su Null o su un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="999e8-490">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="999e8-491">L'input non valido viene visualizzato in un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="999e8-491">The invalid input does appear in an error message.</span></span> <span data-ttu-id="999e8-492">Ma se si vogliono visualizzare di nuovo i dati non validi nel campo modulo, è consigliabile impostare la proprietà del modello come stringa ed eseguire manualmente la conversione dei dati.</span><span class="sxs-lookup"><span data-stu-id="999e8-492">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="999e8-493">La stessa strategia è consigliata se si vuole evitare che gli errori di conversione del tipo causino errori di stato del modello.</span><span class="sxs-lookup"><span data-stu-id="999e8-493">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="999e8-494">In tal caso, impostare la proprietà del modello come stringa.</span><span class="sxs-lookup"><span data-stu-id="999e8-494">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="999e8-495">Tipi semplici</span><span class="sxs-lookup"><span data-stu-id="999e8-495">Simple types</span></span>

<span data-ttu-id="999e8-496">I tipi semplici in cui lo strumento di associazione di modelli può convertire le stringhe di origine includono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="999e8-496">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="999e8-497">Boolean</span><span class="sxs-lookup"><span data-stu-id="999e8-497">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="999e8-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="999e8-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="999e8-499">Char</span><span class="sxs-lookup"><span data-stu-id="999e8-499">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="999e8-500">DateTime</span><span class="sxs-lookup"><span data-stu-id="999e8-500">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="999e8-501">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="999e8-501">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="999e8-502">Decimale</span><span class="sxs-lookup"><span data-stu-id="999e8-502">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="999e8-503">Doppio</span><span class="sxs-lookup"><span data-stu-id="999e8-503">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="999e8-504">Enumerazione</span><span class="sxs-lookup"><span data-stu-id="999e8-504">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="999e8-505">GUID</span><span class="sxs-lookup"><span data-stu-id="999e8-505">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="999e8-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="999e8-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="999e8-507">Singolo</span><span class="sxs-lookup"><span data-stu-id="999e8-507">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="999e8-508">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="999e8-508">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="999e8-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="999e8-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="999e8-510">Uri</span><span class="sxs-lookup"><span data-stu-id="999e8-510">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="999e8-511">Version</span><span class="sxs-lookup"><span data-stu-id="999e8-511">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="999e8-512">Tipi complessi</span><span class="sxs-lookup"><span data-stu-id="999e8-512">Complex types</span></span>

<span data-ttu-id="999e8-513">Un tipo pubblico deve avere un costruttore predefinito pubblico e proprietà scrivibili pubbliche da associare.</span><span class="sxs-lookup"><span data-stu-id="999e8-513">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="999e8-514">Quando si verifica l'associazione di modelli, vengono create istanze della classe usando il costruttore predefinito pubblico.</span><span class="sxs-lookup"><span data-stu-id="999e8-514">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="999e8-515">Per ogni proprietà del tipo complesso, l'associazione di modelli cerca il modello di nome *prefisso.nome_proprietà* nelle origini.</span><span class="sxs-lookup"><span data-stu-id="999e8-515">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="999e8-516">Se non viene trovato, cerca semplicemente *nome_proprietà* senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="999e8-516">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="999e8-517">Per l'associazione a un parametro, il prefisso è il nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="999e8-517">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="999e8-518">Per l'associazione a una proprietà pubblica `PageModel`, il prefisso è il nome della proprietà pubblica.</span><span class="sxs-lookup"><span data-stu-id="999e8-518">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="999e8-519">Alcuni attributi hanno una proprietà `Prefix` che consente di eseguire l'override dell'utilizzo predefinito del nome di un parametro o una proprietà.</span><span class="sxs-lookup"><span data-stu-id="999e8-519">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="999e8-520">Ad esempio, si supponga che il tipo complesso sia la classe `Instructor` seguente:</span><span class="sxs-lookup"><span data-stu-id="999e8-520">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="999e8-521">Prefisso = nome del parametro</span><span class="sxs-lookup"><span data-stu-id="999e8-521">Prefix = parameter name</span></span>

<span data-ttu-id="999e8-522">Se il modello da associare è un parametro denominato `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="999e8-522">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="999e8-523">L'associazione di modelli cerca prima di tutto la chiave `instructorToUpdate.ID` nelle origini.</span><span class="sxs-lookup"><span data-stu-id="999e8-523">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="999e8-524">Se non viene trovata, cerca `ID` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="999e8-524">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="999e8-525">Prefisso = nome della proprietà</span><span class="sxs-lookup"><span data-stu-id="999e8-525">Prefix = property name</span></span>

<span data-ttu-id="999e8-526">Se il modello da associare è una proprietà denominata `Instructor` del controller o della classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="999e8-526">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="999e8-527">L'associazione di modelli cerca prima di tutto la chiave `Instructor.ID` nelle origini.</span><span class="sxs-lookup"><span data-stu-id="999e8-527">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="999e8-528">Se non viene trovata, cerca `ID` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="999e8-528">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="999e8-529">Prefisso personalizzato</span><span class="sxs-lookup"><span data-stu-id="999e8-529">Custom prefix</span></span>

<span data-ttu-id="999e8-530">Se il modello da associare è un parametro denominato `instructorToUpdate` e un attributo `Bind` specifica `Instructor` come prefisso:</span><span class="sxs-lookup"><span data-stu-id="999e8-530">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="999e8-531">L'associazione di modelli cerca prima di tutto la chiave `Instructor.ID` nelle origini.</span><span class="sxs-lookup"><span data-stu-id="999e8-531">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="999e8-532">Se non viene trovata, cerca `ID` senza prefisso.</span><span class="sxs-lookup"><span data-stu-id="999e8-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="999e8-533">Attributi per le destinazioni di tipo complesso</span><span class="sxs-lookup"><span data-stu-id="999e8-533">Attributes for complex type targets</span></span>

<span data-ttu-id="999e8-534">Sono disponibili vari attributi predefiniti per controllare l'associazione di modelli di tipi complessi:</span><span class="sxs-lookup"><span data-stu-id="999e8-534">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="999e8-535">Questi attributi influiscono sul modello di associazione quando i dati di modulo inviati sono l'origine dei valori.</span><span class="sxs-lookup"><span data-stu-id="999e8-535">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="999e8-536">Non influiscono sui formattatori di input, che elaborano corpi di richieste JSON e XML inviati.</span><span class="sxs-lookup"><span data-stu-id="999e8-536">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="999e8-537">I formattatori di input sono descritti [più avanti in questo articolo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="999e8-537">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="999e8-538">Vedere anche la discussione relativa all'attributo `[Required]` in [Convalida del modello](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="999e8-538">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="999e8-539">Attributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="999e8-539">[BindRequired] attribute</span></span>

<span data-ttu-id="999e8-540">Può essere applicato solo alle proprietà del modello e non ai parametri di metodo.</span><span class="sxs-lookup"><span data-stu-id="999e8-540">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="999e8-541">Con questo attributo l'associazione di modelli aggiunge un errore di stato del modello se non è possibile eseguire l'associazione per una proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="999e8-541">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="999e8-542">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="999e8-542">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="999e8-543">Attributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="999e8-543">[BindNever] attribute</span></span>

<span data-ttu-id="999e8-544">Può essere applicato solo alle proprietà del modello e non ai parametri di metodo.</span><span class="sxs-lookup"><span data-stu-id="999e8-544">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="999e8-545">Impedisce all'associazione di modelli di impostare una proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="999e8-545">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="999e8-546">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="999e8-546">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="999e8-547">Attributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="999e8-547">[Bind] attribute</span></span>

<span data-ttu-id="999e8-548">Può essere applicato a una classe o a un parametro di metodo.</span><span class="sxs-lookup"><span data-stu-id="999e8-548">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="999e8-549">Specifica quali proprietà di un modello devono essere incluse nell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="999e8-549">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="999e8-550">Nell'esempio seguente vengono associate solo le proprietà specificate del modello `Instructor` quando viene chiamato qualsiasi gestore o metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="999e8-550">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="999e8-551">Nell'esempio seguente vengono associate solo le proprietà specificate del modello `Instructor` quando viene chiamato il metodo `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="999e8-551">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="999e8-552">L'attributo `[Bind]` può essere usato per evitare l'overposting negli scenari di *creazione*.</span><span class="sxs-lookup"><span data-stu-id="999e8-552">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="999e8-553">Non funziona bene negli scenari di modifica perché le proprietà escluse vengono impostate su Null o su un valore predefinito anziché rimanere inalterate.</span><span class="sxs-lookup"><span data-stu-id="999e8-553">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="999e8-554">Per difendersi dall'overposting, sono consigliati i modelli di visualizzazione anziché l'attributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="999e8-554">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="999e8-555">Per altre informazioni, vedere [Nota sulla sicurezza relativa all'overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="999e8-555">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="999e8-556">Raccolte</span><span class="sxs-lookup"><span data-stu-id="999e8-556">Collections</span></span>

<span data-ttu-id="999e8-557">Per le destinazioni che sono raccolte di tipi semplici, l'associazione di modelli cerca le corrispondenze per *nome_parametro* oppure *nome_proprietà*.</span><span class="sxs-lookup"><span data-stu-id="999e8-557">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="999e8-558">Se non viene trovata alcuna corrispondenza, viene cercato uno dei formati supportati senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="999e8-558">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="999e8-559">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="999e8-559">For example:</span></span>

* <span data-ttu-id="999e8-560">Si supponga che il parametro da associare sia una matrice denominata `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="999e8-560">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="999e8-561">I dati di modulo o di stringhe di query possono essere in uno dei formati seguenti:</span><span class="sxs-lookup"><span data-stu-id="999e8-561">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="999e8-562">Il formato seguente è supportato solo nei dati di modulo:</span><span class="sxs-lookup"><span data-stu-id="999e8-562">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="999e8-563">Per tutti i formati di esempio precedenti, l'associazione di modelli passa una matrice di due elementi al parametro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="999e8-563">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="999e8-564">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="999e8-564">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="999e8-565">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="999e8-565">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="999e8-566">Per i formati di dati che usano numeri con indice (... [0]... [1]...) è necessario assicurarsi che la numerazione sia progressiva a partire da zero.</span><span class="sxs-lookup"><span data-stu-id="999e8-566">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="999e8-567">Se sono presenti eventuali interruzioni nella numerazione con indice, tutti gli elementi dopo l'interruzione vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="999e8-567">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="999e8-568">Ad esempio, se gli indici sono 0 e 2 anziché 0 e 1, il secondo elemento viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="999e8-568">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="999e8-569">Dizionari</span><span class="sxs-lookup"><span data-stu-id="999e8-569">Dictionaries</span></span>

<span data-ttu-id="999e8-570">Per le destinazioni `Dictionary`, l'associazione di modelli cerca le corrispondenze per *nome_parametro* oppure *nome_proprietà*.</span><span class="sxs-lookup"><span data-stu-id="999e8-570">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="999e8-571">Se non viene trovata alcuna corrispondenza, viene cercato uno dei formati supportati senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="999e8-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="999e8-572">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="999e8-572">For example:</span></span>

* <span data-ttu-id="999e8-573">Si supponga che il parametro di destinazione sia un elemento `Dictionary<int, string>` denominato `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="999e8-573">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="999e8-574">I dati di modulo o di stringhe di query inviati possono essere simili a uno degli esempi seguenti:</span><span class="sxs-lookup"><span data-stu-id="999e8-574">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="999e8-575">Per tutti i formati di esempio precedenti, l'associazione di modelli passa un dizionario di due elementi al parametro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="999e8-575">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="999e8-576">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="999e8-576">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="999e8-577">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="999e8-577">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="999e8-578">Comportamento di globalizzazione dell'associazione di modelli dati della route e stringhe di query</span><span class="sxs-lookup"><span data-stu-id="999e8-578">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="999e8-579">Il provider del valore della route ASP.NET Core e il provider del valore della stringa di query:</span><span class="sxs-lookup"><span data-stu-id="999e8-579">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="999e8-580">Considera i valori come impostazioni cultura invarianti.</span><span class="sxs-lookup"><span data-stu-id="999e8-580">Treat values as invariant culture.</span></span>
* <span data-ttu-id="999e8-581">Si prevede che gli URL siano impostazioni cultura invarianti.</span><span class="sxs-lookup"><span data-stu-id="999e8-581">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="999e8-582">Al contrario, i valori provenienti dai dati del form subiscono una conversione con distinzione delle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="999e8-582">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="999e8-583">Questa operazione è progettata in modo che gli URL siano condivisibili tra le impostazioni locali.</span><span class="sxs-lookup"><span data-stu-id="999e8-583">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="999e8-584">Per rendere il provider del valore di route ASP.NET Core e il provider di valori della stringa di query sottoposti a una conversione dipendente dalle impostazioni cultura:</span><span class="sxs-lookup"><span data-stu-id="999e8-584">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="999e8-585">Ereditano da <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="999e8-585">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="999e8-586">Copiare il codice da [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) o [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="999e8-586">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="999e8-587">Sostituire il [valore delle impostazioni cultura](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passato al costruttore del provider di valori con [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="999e8-587">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="999e8-588">Sostituire la factory del provider di valori predefinito in opzioni MVC con la nuova:</span><span class="sxs-lookup"><span data-stu-id="999e8-588">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="999e8-589">Tipi di dati speciali</span><span class="sxs-lookup"><span data-stu-id="999e8-589">Special data types</span></span>

<span data-ttu-id="999e8-590">Esistono alcuni tipi di dati speciali che l'associazione di modelli può gestire.</span><span class="sxs-lookup"><span data-stu-id="999e8-590">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="999e8-591">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="999e8-591">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="999e8-592">Un file caricato incluso nella richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="999e8-592">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="999e8-593">È anche supportato `IEnumerable<IFormFile>` per più file.</span><span class="sxs-lookup"><span data-stu-id="999e8-593">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="999e8-594">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="999e8-594">CancellationToken</span></span>

<span data-ttu-id="999e8-595">usato per annullare l'attività nei controller asincroni.</span><span class="sxs-lookup"><span data-stu-id="999e8-595">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="999e8-596">FormCollection</span><span class="sxs-lookup"><span data-stu-id="999e8-596">FormCollection</span></span>

<span data-ttu-id="999e8-597">Usato per recuperare tutti i valori dai dati di modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="999e8-597">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="999e8-598">Formattatori di input</span><span class="sxs-lookup"><span data-stu-id="999e8-598">Input formatters</span></span>

<span data-ttu-id="999e8-599">I dati nel corpo della richiesta possono essere in formato JSON, XML o in altri formati.</span><span class="sxs-lookup"><span data-stu-id="999e8-599">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="999e8-600">Per analizzare questi dati, l'associazione di modelli usa un *formattatore di input* configurato in modo da gestire un particolare tipo di contenuto.</span><span class="sxs-lookup"><span data-stu-id="999e8-600">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="999e8-601">Per impostazione predefinita, ASP.NET Core include i formattatori di input basati su JSON per la gestione dei dati JSON.</span><span class="sxs-lookup"><span data-stu-id="999e8-601">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="999e8-602">È possibile aggiungere altri formattatori per altri tipi di contenuto.</span><span class="sxs-lookup"><span data-stu-id="999e8-602">You can add other formatters for other content types.</span></span>

<span data-ttu-id="999e8-603">ASP.NET Core consente di selezionare i formattatori di input in base all'attributo [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="999e8-603">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="999e8-604">Se non è presente alcun attributo, viene usata l'[intestazione Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="999e8-604">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="999e8-605">Per usare i formattatori di input XML predefiniti:</span><span class="sxs-lookup"><span data-stu-id="999e8-605">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="999e8-606">Installare il pacchetto NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="999e8-606">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="999e8-607">In `Startup.ConfigureServices` chiamare <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> o <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="999e8-607">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="999e8-608">Applicare l'attributo `Consumes` alle classi controller o ai metodi di azione che devono aspettarsi XML nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="999e8-608">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="999e8-609">Per altre informazioni, vedere [Introduzione alla serializzazione XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="999e8-609">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="999e8-610">Escludere i tipi specificati dall'associazione di modelli</span><span class="sxs-lookup"><span data-stu-id="999e8-610">Exclude specified types from model binding</span></span>

<span data-ttu-id="999e8-611">Il comportamento dell'associazione di modelli e del sistema di convalida è determinato da [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="999e8-611">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="999e8-612">È possibile personalizzare `ModelMetadata` mediante l'aggiunta di un provider di dettagli a [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="999e8-612">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="999e8-613">Sono disponibili provider di dettagli predefiniti per la disabilitazione dell'associazione di modelli o la convalida per i tipi specificati.</span><span class="sxs-lookup"><span data-stu-id="999e8-613">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="999e8-614">Per disabilitare l'associazione di modelli per tutti i modelli di un tipo specificato, aggiungere un <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="999e8-614">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="999e8-615">Ad esempio, per disabilitare l'associazione di modelli per tutti i modelli di tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="999e8-615">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="999e8-616">Per disabilitare la convalida per le proprietà di un tipo specificato, aggiungere un <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="999e8-616">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="999e8-617">Ad esempio, per disabilitare la convalida per le proprietà di tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="999e8-617">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="999e8-618">Strumenti di associazione di modelli personalizzati</span><span class="sxs-lookup"><span data-stu-id="999e8-618">Custom model binders</span></span>

<span data-ttu-id="999e8-619">È possibile estendere l'associazione di modelli scrivendo uno strumento di associazione di modelli personalizzato e usando l'attributo `[ModelBinder]` per selezionarlo per una determinata destinazione.</span><span class="sxs-lookup"><span data-stu-id="999e8-619">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="999e8-620">Altre informazioni sull'[associazione di modelli personalizzata](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="999e8-620">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="999e8-621">Associazione di modelli manuale</span><span class="sxs-lookup"><span data-stu-id="999e8-621">Manual model binding</span></span>

<span data-ttu-id="999e8-622">L'associazione di modelli può essere richiamata manualmente usando il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="999e8-622">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="999e8-623">Il metodo è definito in entrambe le classi `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="999e8-623">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="999e8-624">Gli overload del metodo consentono di specificare il prefisso e il provider di valori da usare.</span><span class="sxs-lookup"><span data-stu-id="999e8-624">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="999e8-625">Il metodo restituisce `false` se l'associazione di modelli non riesce.</span><span class="sxs-lookup"><span data-stu-id="999e8-625">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="999e8-626">Ecco un esempio:</span><span class="sxs-lookup"><span data-stu-id="999e8-626">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="999e8-627">Attributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="999e8-627">[FromServices] attribute</span></span>

<span data-ttu-id="999e8-628">Il nome di questo attributo segue il modello degli attributi di associazione di modelli che specificano un'origine dati.</span><span class="sxs-lookup"><span data-stu-id="999e8-628">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="999e8-629">Non si tratta però dell'associazione di dati da un provider di valori.</span><span class="sxs-lookup"><span data-stu-id="999e8-629">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="999e8-630">Ottiene un'istanza di un tipo dal contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="999e8-630">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="999e8-631">Lo scopo è fornire un'alternativa all'inserimento del costruttore per quando è necessario un servizio solo se viene chiamato un metodo specifico.</span><span class="sxs-lookup"><span data-stu-id="999e8-631">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="999e8-632">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="999e8-632">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
