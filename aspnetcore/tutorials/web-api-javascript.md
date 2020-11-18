---
title: "Esercitazione: chiamare un'API Web ASP.NET Core con JavaScript"
author: rick-anderson
description: Informazioni su come chiamare un'API Web ASP.NET Core con JavaScript.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: c32c5befe0be3b1ad4bd87649d3cc74b0296a134
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703709"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="390de-103">Esercitazione: chiamare un'API Web ASP.NET Core con JavaScript</span><span class="sxs-lookup"><span data-stu-id="390de-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="390de-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="390de-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="390de-105">Questa esercitazione illustra come chiamare un'API Web ASP.NET Core con JavaScript usando l'[API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span><span class="sxs-lookup"><span data-stu-id="390de-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="390de-106">Per ASP.NET Core 2.2, vedere la versione 2.2 di [Chiamare l'API Web con JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span><span class="sxs-lookup"><span data-stu-id="390de-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="390de-107">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="390de-107">Prerequisites</span></span>

* <span data-ttu-id="390de-108">[Esercitazione completa: creare un'API Web](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="390de-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="390de-109">Familiarità con CSS, HTML e JavaScript</span><span class="sxs-lookup"><span data-stu-id="390de-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="390de-110">Chiamare l'API Web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="390de-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="390de-111">In questa sezione verrà aggiunta una pagina HTML contenente i moduli per la creazione e la gestione di elementi attività.</span><span class="sxs-lookup"><span data-stu-id="390de-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="390de-112">Agli elementi della pagina vengono associati gestori eventi.</span><span class="sxs-lookup"><span data-stu-id="390de-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="390de-113">I gestori eventi generano richieste HTTP ai metodi di azione dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="390de-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="390de-114">La funzione `fetch` dell'API Fetch avvia ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="390de-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="390de-115">La `fetch` funzione restituisce un oggetto [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) , che contiene una risposta HTTP rappresentata come un `Response` oggetto.</span><span class="sxs-lookup"><span data-stu-id="390de-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="390de-116">Un modello comune consiste nell'estrarre il corpo della risposta JSON richiamando la funzione `json` per l'oggetto `Response`.</span><span class="sxs-lookup"><span data-stu-id="390de-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="390de-117">JavaScript aggiorna la pagina con i dettagli della risposta dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="390de-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="390de-118">La chiamata `fetch` più semplice accetta un solo parametro che rappresenta la route.</span><span class="sxs-lookup"><span data-stu-id="390de-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="390de-119">Un secondo parametro, noto come oggetto `init`, è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="390de-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="390de-120">`init` viene usato per configurare la richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="390de-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="390de-121">Configurare l'app in modo da [gestire i file statici](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [abilitare il mapping dei file predefiniti](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="390de-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="390de-122">Il codice evidenziato seguente è necessario nel metodo `Configure` di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="390de-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="390de-123">Creare una cartella *wwwroot* nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="390de-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="390de-124">Creare una cartella *JS* all'interno della cartella *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="390de-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="390de-125">Aggiungere un file HTML denominato *index.html* alla cartella *wwwroot*</span><span class="sxs-lookup"><span data-stu-id="390de-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="390de-126">Sostituire il contenuto di *index.html* con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="390de-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="390de-127">Aggiungere un file CSS denominato *site. CSS* alla cartella *wwwroot/CSS* .</span><span class="sxs-lookup"><span data-stu-id="390de-127">Add a CSS file named *site.css* to the *wwwroot/css* folder.</span></span> <span data-ttu-id="390de-128">Sostituire il contenuto di *site. CSS* con gli stili seguenti:</span><span class="sxs-lookup"><span data-stu-id="390de-128">Replace the contents of *site.css* with the following styles:</span></span>

    [!code-css[](first-web-api/samples/3.0/TodoApi/wwwroot/css/site.css)]

1. <span data-ttu-id="390de-129">Aggiungere un file JavaScript denominato *site.js* alla cartella *wwwroot/JS* .</span><span class="sxs-lookup"><span data-stu-id="390de-129">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="390de-130">Sostituire il contenuto di *site.js* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="390de-130">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="390de-131">Può essere necessario modificare le impostazioni di avvio del progetto ASP.NET Core per il test della pagina HTML in locale:</span><span class="sxs-lookup"><span data-stu-id="390de-131">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="390de-132">Aprire *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="390de-132">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="390de-133">Rimuovere la `launchUrl` proprietà per forzare l'apertura dell'app a *index.html* &mdash; file predefinito del progetto.</span><span class="sxs-lookup"><span data-stu-id="390de-133">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="390de-134">In questo esempio vengono chiamati tutti i metodi CRUD dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="390de-134">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="390de-135">Di seguito sono disponibili spiegazioni per le richieste dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="390de-135">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="390de-136">Ottenere un elenco di elementi attività</span><span class="sxs-lookup"><span data-stu-id="390de-136">Get a list of to-do items</span></span>

<span data-ttu-id="390de-137">Nel codice seguente viene inviata una richiesta HTTP GET alla route *api/TodoItems*:</span><span class="sxs-lookup"><span data-stu-id="390de-137">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="390de-138">Quando l'API Web restituisce un codice di stato di esecuzione riuscita, viene chiamata la funzione `_displayItems`.</span><span class="sxs-lookup"><span data-stu-id="390de-138">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="390de-139">Ogni elemento attività nel parametro matrice accettato da `_displayItems` viene aggiunto a una tabella con i pulsanti **Edit** (Modifica) e **Delete** (Elimina).</span><span class="sxs-lookup"><span data-stu-id="390de-139">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="390de-140">Se la richiesta dell'API Web ha esito negativo, viene registrato un errore nella console del browser.</span><span class="sxs-lookup"><span data-stu-id="390de-140">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="390de-141">Aggiungere un elemento attività</span><span class="sxs-lookup"><span data-stu-id="390de-141">Add a to-do item</span></span>

<span data-ttu-id="390de-142">Nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="390de-142">In the following code:</span></span>

* <span data-ttu-id="390de-143">Viene dichiarata una variabile `item` per costruire una rappresentazione letterale dell'oggetto dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="390de-143">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="390de-144">Viene configurata una richiesta Fetch con le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="390de-144">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="390de-145">`method`&mdash;specifica il verbo di azione HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="390de-145">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="390de-146">`body`&mdash;specifica la rappresentazione JSON del corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="390de-146">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="390de-147">Il codice JSON viene prodotto passando il valore letterale dell'oggetto archiviato in `item` alla funzione [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="390de-147">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="390de-148">`headers`&mdash;specifica le intestazioni della richiesta HTTP `Accept` e `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="390de-148">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="390de-149">Entrambe le intestazioni vengono impostate su `application/json` per specificare rispettivamente il tipo di supporto ricevuto e inviato.</span><span class="sxs-lookup"><span data-stu-id="390de-149">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="390de-150">Una richiesta HTTP POST viene inviata alla route *api/TodoItems*.</span><span class="sxs-lookup"><span data-stu-id="390de-150">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="390de-151">Quando l'API Web restituisce un codice di stato di esecuzione riuscita, viene richiamata la funzione `getItems` per aggiornare la tabella HTML.</span><span class="sxs-lookup"><span data-stu-id="390de-151">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="390de-152">Se la richiesta dell'API Web ha esito negativo, viene registrato un errore nella console del browser.</span><span class="sxs-lookup"><span data-stu-id="390de-152">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="390de-153">Aggiornare un elemento attività</span><span class="sxs-lookup"><span data-stu-id="390de-153">Update a to-do item</span></span>

<span data-ttu-id="390de-154">L'aggiornamento di un elemento attività è simile all'aggiunta di un elemento. Esistono tuttavia due differenze significative:</span><span class="sxs-lookup"><span data-stu-id="390de-154">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="390de-155">Alla route viene aggiunto come suffisso l'identificatore univoco dell'elemento da aggiornare.</span><span class="sxs-lookup"><span data-stu-id="390de-155">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="390de-156">Ad esempio, *api/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="390de-156">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="390de-157">Il verbo di azione HTTP è PUT, come indicato dall'opzione `method`.</span><span class="sxs-lookup"><span data-stu-id="390de-157">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="390de-158">Eliminare un elemento attività</span><span class="sxs-lookup"><span data-stu-id="390de-158">Delete a to-do item</span></span>

<span data-ttu-id="390de-159">Per eliminare un elemento attività, impostare l'opzione `method` della richiesta su `DELETE` e specificare l'identificatore univoco dell'elemento nell'URL.</span><span class="sxs-lookup"><span data-stu-id="390de-159">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="390de-160">Passare all'esercitazione successiva per apprendere come generare pagine della Guida dell'API Web:</span><span class="sxs-lookup"><span data-stu-id="390de-160">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
