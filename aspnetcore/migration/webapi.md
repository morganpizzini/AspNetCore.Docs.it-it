---
title: Eseguire la migrazione da API Web ASP.NET a ASP.NET Core
author: ardalis
description: Informazioni su come eseguire la migrazione di un'implementazione di API Web dall'API Web ASP.NET 4. x a ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/webapi
ms.openlocfilehash: dda457daa0cb8a59ccd4c326a601e375fe4a81bb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766589"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="bdc96-103">Eseguire la migrazione da API Web ASP.NET a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bdc96-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="bdc96-104">Di [Scott Addie](https://twitter.com/scott_addie) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bdc96-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bdc96-105">Un'API Web ASP.NET 4. x è un servizio HTTP che raggiunge un'ampia gamma di client, inclusi browser e dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="bdc96-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="bdc96-106">ASP.NET Core unifica i modelli di app per le API Web e MVC di ASP.NET 4. x in un modello di programmazione più semplice noto come ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="bdc96-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="bdc96-107">Questo articolo illustra i passaggi necessari per eseguire la migrazione dall'API Web ASP.NET 4. x a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="bdc96-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="bdc96-108">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bdc96-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bdc96-109">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="bdc96-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="bdc96-110">Esaminare il progetto API Web ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="bdc96-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="bdc96-111">Come punto di partenza, in questo articolo viene usato il progetto *ProductsApp* creato in [Introduzione con API Web ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="bdc96-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="bdc96-112">In tale progetto, un semplice progetto API Web ASP.NET 4. x viene configurato come segue.</span><span class="sxs-lookup"><span data-stu-id="bdc96-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="bdc96-113">In *Global.asax.cs*viene effettuata una chiamata a `WebApiConfig.Register`:</span><span class="sxs-lookup"><span data-stu-id="bdc96-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="bdc96-114">La `WebApiConfig` classe si trova nella cartella *app_start* e dispone di un metodo `Register` statico:</span><span class="sxs-lookup"><span data-stu-id="bdc96-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="bdc96-115">Questa classe configura il [routing degli attributi](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), sebbene non sia effettivamente utilizzato nel progetto.</span><span class="sxs-lookup"><span data-stu-id="bdc96-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="bdc96-116">Viene inoltre configurata la tabella di routing, che viene utilizzata da API Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="bdc96-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="bdc96-117">In questo caso, l'API Web ASP.NET 4. x prevede che gli URL corrispondano `/api/{controller}/{id}`al formato `{id}` , con l'opzione facoltativo.</span><span class="sxs-lookup"><span data-stu-id="bdc96-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="bdc96-118">Il progetto *ProductsApp* include un controller.</span><span class="sxs-lookup"><span data-stu-id="bdc96-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="bdc96-119">Il controller eredita da `ApiController` e contiene due azioni:</span><span class="sxs-lookup"><span data-stu-id="bdc96-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="bdc96-120">Il `Product` modello utilizzato da `ProductsController` è una classe semplice:</span><span class="sxs-lookup"><span data-stu-id="bdc96-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="bdc96-121">Le sezioni seguenti illustrano la migrazione del progetto API Web a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="bdc96-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="bdc96-122">Crea progetto di destinazione</span><span class="sxs-lookup"><span data-stu-id="bdc96-122">Create destination project</span></span>

<span data-ttu-id="bdc96-123">Completare i passaggi seguenti in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="bdc96-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="bdc96-124">Passare a **file** > **nuovo** > **progetto** > **altri tipi** > di progetto**soluzioni di Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="bdc96-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="bdc96-125">Selezionare **soluzione vuota**e denominare la soluzione *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="bdc96-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="bdc96-126">Fare clic sul pulsante **OK**.</span><span class="sxs-lookup"><span data-stu-id="bdc96-126">Click the **OK** button.</span></span>
* <span data-ttu-id="bdc96-127">Aggiungere il progetto *ProductsApp* esistente alla soluzione.</span><span class="sxs-lookup"><span data-stu-id="bdc96-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="bdc96-128">Aggiungere un nuovo progetto di **applicazione Web ASP.NET Core** alla soluzione.</span><span class="sxs-lookup"><span data-stu-id="bdc96-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="bdc96-129">Selezionare **.NET Core** Target Framework dall'elenco a discesa e selezionare il modello di progetto **API** .</span><span class="sxs-lookup"><span data-stu-id="bdc96-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="bdc96-130">Denominare il progetto *ProductsCore*e fare clic sul pulsante **OK** .</span><span class="sxs-lookup"><span data-stu-id="bdc96-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="bdc96-131">La soluzione ora contiene due progetti.</span><span class="sxs-lookup"><span data-stu-id="bdc96-131">The solution now contains two projects.</span></span> <span data-ttu-id="bdc96-132">Le sezioni seguenti illustrano come eseguire la migrazione del contenuto del progetto *ProductsApp* al progetto *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="bdc96-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="bdc96-133">Eseguire la migrazione della configurazione</span><span class="sxs-lookup"><span data-stu-id="bdc96-133">Migrate configuration</span></span>

<span data-ttu-id="bdc96-134">ASP.NET Core non utilizza la cartella *app_start* o il file *Global. asax* e il file *Web. config* viene aggiunto in fase di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="bdc96-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="bdc96-135">*Startup.cs* è la sostituzione di *Global. asax* e si trova nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="bdc96-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="bdc96-136">La `Startup` classe gestisce tutte le attività di avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="bdc96-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="bdc96-137">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="bdc96-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="bdc96-138">In ASP.NET Core MVC, il routing degli attributi è incluso per <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> impostazione predefinita quando `Startup.Configure`viene chiamato in.</span><span class="sxs-lookup"><span data-stu-id="bdc96-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="bdc96-139">La chiamata `UseMvc` seguente sostituisce il file di *app_start/Webapiconfig.cs* del progetto *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="bdc96-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="bdc96-140">Eseguire la migrazione di modelli e controller</span><span class="sxs-lookup"><span data-stu-id="bdc96-140">Migrate models and controllers</span></span>

<span data-ttu-id="bdc96-141">Copiare il controller del progetto *ProductApp* e il modello usato.</span><span class="sxs-lookup"><span data-stu-id="bdc96-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="bdc96-142">A tale scopo, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="bdc96-142">Follow these steps:</span></span>

1. <span data-ttu-id="bdc96-143">Copiare *Controllers/ProductsController. cs* dal progetto originale a quello nuovo.</span><span class="sxs-lookup"><span data-stu-id="bdc96-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="bdc96-144">Copiare l'intera cartella *Models* dal progetto originale a quella nuova.</span><span class="sxs-lookup"><span data-stu-id="bdc96-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="bdc96-145">Modificare gli spazi dei nomi dei file copiati in modo che corrispondano al nuovo nome del progetto (*ProductsCore*).</span><span class="sxs-lookup"><span data-stu-id="bdc96-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="bdc96-146">Modificare anche `using ProductsApp.Models;` l'istruzione in *ProductsController.cs* .</span><span class="sxs-lookup"><span data-stu-id="bdc96-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="bdc96-147">A questo punto, la compilazione dell'app comporta una serie di errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="bdc96-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="bdc96-148">Gli errori si verificano perché i componenti seguenti non esistono in ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bdc96-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="bdc96-149">Classe `ApiController`</span><span class="sxs-lookup"><span data-stu-id="bdc96-149">`ApiController` class</span></span>
* <span data-ttu-id="bdc96-150">Spazio dei nomi `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="bdc96-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="bdc96-151">Interfaccia `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="bdc96-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="bdc96-152">Correggere gli errori nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="bdc96-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="bdc96-153">Cambiare `ApiController` in <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="bdc96-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="bdc96-154">Aggiungere `using Microsoft.AspNetCore.Mvc;` per risolvere il `ControllerBase` riferimento.</span><span class="sxs-lookup"><span data-stu-id="bdc96-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="bdc96-155">Eliminare `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="bdc96-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="bdc96-156">Modificare il `GetProduct` tipo restituito dell'azione da `IHttpActionResult` a `ActionResult<Product>`.</span><span class="sxs-lookup"><span data-stu-id="bdc96-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="bdc96-157">Semplificare l' `GetProduct` `return` istruzione dell'azione a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="bdc96-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="bdc96-158">Configurare il routing</span><span class="sxs-lookup"><span data-stu-id="bdc96-158">Configure routing</span></span>

<span data-ttu-id="bdc96-159">Configurare il routing come segue:</span><span class="sxs-lookup"><span data-stu-id="bdc96-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="bdc96-160">Contrassegnare `ProductsController` la classe con gli attributi seguenti:</span><span class="sxs-lookup"><span data-stu-id="bdc96-160">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="bdc96-161">L'attributo [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) precedente configura il modello di routing degli attributi del controller.</span><span class="sxs-lookup"><span data-stu-id="bdc96-161">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="bdc96-162">L' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attributo rende il routing degli attributi un requisito per tutte le azioni nel controller.</span><span class="sxs-lookup"><span data-stu-id="bdc96-162">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="bdc96-163">Il `[controller]` routing degli attributi supporta i token, ad `[action]`esempio e.</span><span class="sxs-lookup"><span data-stu-id="bdc96-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="bdc96-164">In fase di esecuzione, ogni token viene sostituito rispettivamente dal nome del controller o dell'azione a cui è stato applicato l'attributo.</span><span class="sxs-lookup"><span data-stu-id="bdc96-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="bdc96-165">I token riducono il numero di stringhe magiche nel progetto.</span><span class="sxs-lookup"><span data-stu-id="bdc96-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="bdc96-166">I token assicurano inoltre che le route rimangano sincronizzate con i controller e le azioni corrispondenti quando vengono applicati i refactoring di ridenominazione automatica.</span><span class="sxs-lookup"><span data-stu-id="bdc96-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="bdc96-167">Impostare la modalità di compatibilità del progetto su ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="bdc96-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="bdc96-168">La modifica precedente:</span><span class="sxs-lookup"><span data-stu-id="bdc96-168">The preceding change:</span></span>

    * <span data-ttu-id="bdc96-169">È necessario per usare l' `[ApiController]` attributo a livello di controller.</span><span class="sxs-lookup"><span data-stu-id="bdc96-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="bdc96-170">Consente di optare per i comportamenti potenzialmente innovatori introdotti in ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="bdc96-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="bdc96-171">Abilitare le richieste HTTP Get alle `ProductController` azioni:</span><span class="sxs-lookup"><span data-stu-id="bdc96-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="bdc96-172">Applicare l' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attributo all' `GetAllProducts` azione.</span><span class="sxs-lookup"><span data-stu-id="bdc96-172">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="bdc96-173">Applicare l' `[HttpGet("{id}")]` attributo all' `GetProduct` azione.</span><span class="sxs-lookup"><span data-stu-id="bdc96-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="bdc96-174">Dopo le modifiche precedenti e la rimozione delle istruzioni inutilizzate `using` , il file *ProductsController.cs* ha un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="bdc96-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="bdc96-175">Eseguire il progetto migrato e passare a `/api/products`.</span><span class="sxs-lookup"><span data-stu-id="bdc96-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="bdc96-176">Viene visualizzato un elenco completo di tre prodotti.</span><span class="sxs-lookup"><span data-stu-id="bdc96-176">A full list of three products appears.</span></span> <span data-ttu-id="bdc96-177">Passare a `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="bdc96-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="bdc96-178">Il primo prodotto verrà visualizzato.</span><span class="sxs-lookup"><span data-stu-id="bdc96-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="bdc96-179">Shim di compatibilità</span><span class="sxs-lookup"><span data-stu-id="bdc96-179">Compatibility shim</span></span>

<span data-ttu-id="bdc96-180">La libreria [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) fornisce uno shim di compatibilità per spostare i progetti API Web ASP.NET 4. x in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdc96-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="bdc96-181">Lo shim di compatibilità estende ASP.NET Core per supportare una serie di convenzioni di ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="bdc96-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="bdc96-182">L'esempio portato in precedenza in questo documento è abbastanza elementare che lo shim di compatibilità non fosse necessario.</span><span class="sxs-lookup"><span data-stu-id="bdc96-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="bdc96-183">Per i progetti di grandi dimensioni, l'uso dello shim di compatibilità può essere utile per colmare temporaneamente il gap dell'API tra ASP.NET Core e ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="bdc96-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="bdc96-184">Lo shim di compatibilità dell'API Web è pensato per essere usato come misura temporanea per supportare la migrazione di progetti API Web ASP.NET 4. x di grandi dimensioni a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdc96-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="bdc96-185">Nel corso del tempo, è necessario aggiornare i progetti in modo da usare ASP.NET Core modelli anziché basarsi sullo shim di compatibilità.</span><span class="sxs-lookup"><span data-stu-id="bdc96-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="bdc96-186">Le funzionalità di compatibilità `Microsoft.AspNetCore.Mvc.WebApiCompatShim` incluse in includono:</span><span class="sxs-lookup"><span data-stu-id="bdc96-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="bdc96-187">Aggiunge un `ApiController` tipo in modo che i tipi di base dei controller non debbano essere aggiornati.</span><span class="sxs-lookup"><span data-stu-id="bdc96-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="bdc96-188">Abilita l'associazione di modelli di tipo API Web.</span><span class="sxs-lookup"><span data-stu-id="bdc96-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="bdc96-189">ASP.NET Core funzioni di associazione di modelli MVC in modo analogo a quello di ASP.NET 4. x MVC 5, per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="bdc96-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="bdc96-190">Lo shim di compatibilità modifica l'associazione di modelli in modo da essere più simile alle convenzioni di associazione del modello ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="bdc96-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="bdc96-191">Ad esempio, i tipi complessi vengono automaticamente associati dal corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="bdc96-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="bdc96-192">Estende l'associazione di modelli in modo che le azioni del controller `HttpRequestMessage`possano prendere parametri di tipo.</span><span class="sxs-lookup"><span data-stu-id="bdc96-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="bdc96-193">Aggiunge formattatori di messaggi che consentono alle azioni di restituire `HttpResponseMessage`risultati di tipo.</span><span class="sxs-lookup"><span data-stu-id="bdc96-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="bdc96-194">Aggiunge metodi di risposta aggiuntivi che possono essere usati dalle azioni API Web 2 per rispondere alle risposte:</span><span class="sxs-lookup"><span data-stu-id="bdc96-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="bdc96-195">`HttpResponseMessage`generatori</span><span class="sxs-lookup"><span data-stu-id="bdc96-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="bdc96-196">Metodi di risultato dell'azione:</span><span class="sxs-lookup"><span data-stu-id="bdc96-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="bdc96-197">Aggiunge un'istanza di `IContentNegotiator` al contenitore di inserimento delle dipendenze dell'app e rende disponibili i tipi correlati alla negoziazione del contenuto da [Microsoft. AspNet. WebAPI. client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="bdc96-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="bdc96-198">Esempi di tali tipi includono `DefaultContentNegotiator` e `MediaTypeFormatter`.</span><span class="sxs-lookup"><span data-stu-id="bdc96-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="bdc96-199">Per usare lo shim di compatibilità:</span><span class="sxs-lookup"><span data-stu-id="bdc96-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="bdc96-200">Installare il pacchetto NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="bdc96-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="bdc96-201">Registrare i servizi dello shim di compatibilità con il contenitore DI dell'app chiamando `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bdc96-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="bdc96-202">Definire route specifiche dell'API Web usando `MapWebApiRoute` `IRouteBuilder` nella `IApplicationBuilder.UseMvc` chiamata dell'app.</span><span class="sxs-lookup"><span data-stu-id="bdc96-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bdc96-203">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="bdc96-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
