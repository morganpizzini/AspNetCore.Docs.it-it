---
title: "Esercitazione: creare un'API Web con ASP.NET Core"
author: rick-anderson
description: Informazioni su come creare un'API Web con ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: ccbfc27eb89e23938a69f0ab4cb306d6a4136889
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175052"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="9fb0e-103">Esercitazione: creare un'API Web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9fb0e-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="9fb0e-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="9fb0e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="9fb0e-105">Questa esercitazione illustra le nozioni di base della creazione di un'API Web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9fb0e-106">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9fb0e-107">Creare un progetto di API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-107">Create a web API project.</span></span>
> * <span data-ttu-id="9fb0e-108">Aggiungere una classe modello e un contesto di database.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="9fb0e-109">Eseguire lo scaffolding di un controller con i metodi CRUD.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="9fb0e-110">Configurare il routing, i percorsi URL e i valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="9fb0e-111">Chiamare l'API Web con Postman.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-111">Call the web API with Postman.</span></span>

<span data-ttu-id="9fb0e-112">Al termine si avrà un'API Web che può gestire gli elementi di tipo "attività" archiviati in un database.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="9fb0e-113">Panoramica</span><span class="sxs-lookup"><span data-stu-id="9fb0e-113">Overview</span></span>

<span data-ttu-id="9fb0e-114">Questa esercitazione consente di creare l'API seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="9fb0e-115">API</span><span class="sxs-lookup"><span data-stu-id="9fb0e-115">API</span></span> | <span data-ttu-id="9fb0e-116">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9fb0e-116">Description</span></span> | <span data-ttu-id="9fb0e-117">Corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="9fb0e-117">Request body</span></span> | <span data-ttu-id="9fb0e-118">Corpo della risposta</span><span class="sxs-lookup"><span data-stu-id="9fb0e-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="9fb0e-119">Ottiene tutti gli elementi attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-119">Get all to-do items</span></span> | <span data-ttu-id="9fb0e-120">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-120">None</span></span> | <span data-ttu-id="9fb0e-121">Matrice di elementi attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="9fb0e-122">Ottiene un elemento in base all'ID</span><span class="sxs-lookup"><span data-stu-id="9fb0e-122">Get an item by ID</span></span> | <span data-ttu-id="9fb0e-123">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-123">None</span></span> | <span data-ttu-id="9fb0e-124">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="9fb0e-125">Aggiunge un nuovo elemento</span><span class="sxs-lookup"><span data-stu-id="9fb0e-125">Add a new item</span></span> | <span data-ttu-id="9fb0e-126">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-126">To-do item</span></span> | <span data-ttu-id="9fb0e-127">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="9fb0e-128">Aggiorna un elemento esistente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9fb0e-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="9fb0e-129">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-129">To-do item</span></span> | <span data-ttu-id="9fb0e-130">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-130">None</span></span> |
|<span data-ttu-id="9fb0e-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9fb0e-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="9fb0e-132">Elimina un elemento &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="9fb0e-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="9fb0e-133">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-133">None</span></span> | <span data-ttu-id="9fb0e-134">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-134">None</span></span>|

<span data-ttu-id="9fb0e-135">Il diagramma seguente visualizza la struttura dell'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-135">The following diagram shows the design of the app.</span></span>

![Il client è rappresentato da una casella a sinistra.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="9fb0e-141">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="9fb0e-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9fb0e-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9fb0e-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-144">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="9fb0e-145">Creare un progetto Web</span><span class="sxs-lookup"><span data-stu-id="9fb0e-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fb0e-147">Scegliere **nuovo** progetto dal menu **file** > **Project**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9fb0e-148">Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="9fb0e-149">Assegnare al progetto il nome *TodoApi* e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="9fb0e-150">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.net Core** e **ASP.NET Core 5,0** .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="9fb0e-151">Selezionare il modello **API** e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-151">Select the **API** template and click **Create**.</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9fb0e-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9fb0e-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9fb0e-154">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9fb0e-155">Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="9fb0e-156">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="9fb0e-157">Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="9fb0e-158">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-158">The preceding commands:</span></span>

  * <span data-ttu-id="9fb0e-159">Crea un nuovo progetto API Web e lo apre in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="9fb0e-160">Aggiunge i pacchetti NuGet necessari nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-161">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9fb0e-162">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-162">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="9fb0e-164">In Visual Studio per Mac precedente alla versione 8,6 Selezionare API **app .NET Core**  >  **App**  >  **API**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="9fb0e-165">Nella versione 8,6 o successive selezionare API **app Web e console**  >  **App**  >  **API**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Selezione modello API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="9fb0e-167">Nella finestra di dialogo **Configura nuova ASP.NET Core API Web** selezionare il **Framework di destinazione**.NET Core 5. x più recente.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="9fb0e-168">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-168">Select **Next**.</span></span>

* <span data-ttu-id="9fb0e-169">Immettere *TodoApi* in **Nome progetto**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="9fb0e-171">Aprire un terminale di comando nella cartella di progetto ed eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="9fb0e-172">Testare il progetto</span><span class="sxs-lookup"><span data-stu-id="9fb0e-172">Test the project</span></span>

<span data-ttu-id="9fb0e-173">Il modello di progetto crea un' `WeatherForecast` API con supporto per [spavalderia](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9fb0e-175">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="9fb0e-176">Visual Studio viene avviato:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-176">Visual Studio launches:</span></span>

* <span data-ttu-id="9fb0e-177">Server Web IIS Express.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-177">The IIS Express web server.</span></span>
* <span data-ttu-id="9fb0e-178">Il browser predefinito e passa a `https://localhost:<port>/swagger/index.html` , dove `<port>` è un numero di porta scelto in modo casuale.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9fb0e-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9fb0e-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="9fb0e-180">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9fb0e-181">In un browser passare all'URL seguente: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="9fb0e-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-182">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9fb0e-183">Selezionare **Esegui**  >  **Avvia debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="9fb0e-184">Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="9fb0e-185">Viene restituito un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="9fb0e-186">Accodare `/swagger` all'URL (impostare l'URL su `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="9fb0e-187">Viene visualizzata la pagina di spavalderia `/swagger/index.html` .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="9fb0e-188">Selezionare **Get**  >  **try it out**  >  **Execute (Esegui**).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="9fb0e-189">Viene visualizzata la pagina:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-189">The page displays:</span></span>

* <span data-ttu-id="9fb0e-190">Comando [curl](https://curl.haxx.se/) per testare l'API weatherforecast.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="9fb0e-191">URL per testare l'API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="9fb0e-192">Il codice di risposta, il corpo e le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="9fb0e-193">Casella di riepilogo a discesa con i tipi di supporto e il valore e lo schema di esempio.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="9fb0e-194">Spavalderia viene usato per generare la documentazione e le pagine della guida utili per le API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="9fb0e-195">Questa esercitazione è incentrata sulla creazione di un'API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="9fb0e-196">Per ulteriori informazioni su spavalderia, vedere <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="9fb0e-197">Copiare e incollare l' **URL della richiesta** nel browser:  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="9fb0e-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="9fb0e-198">Viene restituito un codice JSON simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-198">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a><span data-ttu-id="9fb0e-199">Aggiornare launchUrl</span><span class="sxs-lookup"><span data-stu-id="9fb0e-199">Update the launchUrl</span></span>

<span data-ttu-id="9fb0e-200">In *Properties\launchSettings.json* aggiornare `launchUrl` da `"swagger"` a `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="9fb0e-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="9fb0e-201">Poiché spavalderia è stato rimosso, il markup precedente modifica l'URL che viene avviato al metodo GET del controller aggiunto nelle sezioni riportate di seguito.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="9fb0e-202">Aggiungere una classe del modello</span><span class="sxs-lookup"><span data-stu-id="9fb0e-202">Add a model class</span></span>

<span data-ttu-id="9fb0e-203">Un *modello* è un set di classi che rappresentano i dati gestiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="9fb0e-204">Il modello per questa app è una classe `TodoItem` singola.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fb0e-206">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="9fb0e-207">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9fb0e-208">Assegnare un nome alla cartella *Models* .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="9fb0e-209">Fare clic con il pulsante destro del mouse sulla *Models* cartella e scegliere **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9fb0e-210">Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="9fb0e-211">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9fb0e-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9fb0e-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9fb0e-213">Aggiungere una cartella denominata *Models* .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="9fb0e-214">Aggiungere una `TodoItem` classe alla *Models* cartella con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-215">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9fb0e-216">Fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-216">Right-click the project.</span></span> <span data-ttu-id="9fb0e-217">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9fb0e-218">Assegnare un nome alla cartella *Models* .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-218">Name the folder *Models*.</span></span>

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="9fb0e-220">Fare clic con il pulsante destro del mouse sulla *Models* cartella e scegliere **Aggiungi** > **nuovo file** > **generale** > **vuota classe**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="9fb0e-221">Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="9fb0e-222">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="9fb0e-223">La proprietà `Id` funziona come chiave univoca in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="9fb0e-224">Le classi di modelli possono essere inserite in qualsiasi punto del progetto, ma la *Models* cartella viene utilizzata per convenzione.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="9fb0e-225">Aggiungere un contesto di database</span><span class="sxs-lookup"><span data-stu-id="9fb0e-225">Add a database context</span></span>

<span data-ttu-id="9fb0e-226">Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="9fb0e-227">Questa classe viene creata mediante derivazione dalla classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="9fb0e-229">Aggiungere pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="9fb0e-229">Add NuGet packages</span></span>

* <span data-ttu-id="9fb0e-230">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="9fb0e-231">Selezionare la scheda **Esplora** e quindi immettere **Microsoft.EntityFrameworkCore.SqlServer** nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="9fb0e-232">Nel riquadro sinistro selezionare **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="9fb0e-233">Selezionare la casella di controllo **Progetto** nel riquadro di destra e quindi selezionare **Installa**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="9fb0e-234">Usare le istruzioni precedenti per aggiungere il pacchetto NuGet **Microsoft. EntityFrameworkCore. InMemory** .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="9fb0e-235">![Gestione pacchetti NuGet](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="9fb0e-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="9fb0e-236">Aggiungere il contesto del database TodoContext</span><span class="sxs-lookup"><span data-stu-id="9fb0e-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="9fb0e-237">Fare clic con il pulsante destro del mouse sulla *Models* cartella e scegliere **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9fb0e-238">Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-239">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9fb0e-240">Aggiungere una `TodoContext` classe alla *Models* cartella.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="9fb0e-241">Immettere il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="9fb0e-242">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="9fb0e-242">Register the database context</span></span>

<span data-ttu-id="9fb0e-243">In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9fb0e-244">Il contenitore rende disponibile il servizio ai controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="9fb0e-245">Aggiornare *Startup.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="9fb0e-246">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-246">The preceding code:</span></span>

* <span data-ttu-id="9fb0e-247">Rimuove le chiamate di spavalderia.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="9fb0e-248">Rimuove le dichiarazioni `using` inutilizzate.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="9fb0e-249">Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="9fb0e-250">Specifica che il contesto del database userà un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="9fb0e-251">Eseguire lo scaffolding di un controller</span><span class="sxs-lookup"><span data-stu-id="9fb0e-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fb0e-253">Fare clic con il pulsante destro del mouse sulla cartella *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="9fb0e-254">Selezionare **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="9fb0e-255">Selezionare **Controller API con azioni, che usa Entity Framework** e quindi selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="9fb0e-256">Nella finestra di dialogo **Add API Controller with actions, using Entity Framework** (Aggiungi controller API con azioni, che usa Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="9fb0e-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="9fb0e-257">Selezionare **TodoItem (TodoApi. Models )** nella **classe del modello**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="9fb0e-258">Selezionare **TodoContext (TodoApi. Models )** nella **classe del contesto dati**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="9fb0e-259">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-260">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9fb0e-261">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="9fb0e-262">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-262">The preceding commands:</span></span>

* <span data-ttu-id="9fb0e-263">Aggiungere i pacchetti NuGet necessari per lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="9fb0e-264">Installa il motore di scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="9fb0e-265">Esegue lo scaffolding di `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="9fb0e-266">Il codice generato:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-266">The generated code:</span></span>

* <span data-ttu-id="9fb0e-267">Contrassegna la classe con l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="9fb0e-268">L'attributo indica che il controller risponde alle richieste di API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="9fb0e-269">Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="9fb0e-270">Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="9fb0e-271">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="9fb0e-272">I modelli di ASP.NET Core per:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="9fb0e-273">I controller con le visualizzazioni includono `[action]` nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="9fb0e-274">I controller API non includono `[action]` nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="9fb0e-275">Quando il `[action]` token non è incluso nel modello di route, il nome dell' [azione](xref:mvc/controllers/routing#action) viene escluso dalla route.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="9fb0e-276">Ovvero, il nome del metodo associato all'azione non viene usato nella route corrispondente.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="9fb0e-277">Aggiornare il metodo di creazione PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="9fb0e-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="9fb0e-278">Sostituire l'istruzione return in `PostTodoItem` per usare l'operatore [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="9fb0e-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="9fb0e-279">Il codice precedente è un metodo HTTP POST, come indicato dall' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="9fb0e-280">Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="9fb0e-281">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9fb0e-282">Il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="9fb0e-283">Restituisce un [codice di stato HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="9fb0e-284">HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="9fb0e-285">Aggiunge un'intestazione [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) alla risposta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="9fb0e-286">L'intestazione `Location` specifica l'[URI](https://developer.mozilla.org/docs/Glossary/URI) dell'elemento attività appena creato.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="9fb0e-287">Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="9fb0e-288">Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="9fb0e-289">La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="9fb0e-290">Installare Postman</span><span class="sxs-lookup"><span data-stu-id="9fb0e-290">Install Postman</span></span>

<span data-ttu-id="9fb0e-291">Questa esercitazione usa Postman per testare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="9fb0e-292">Installa il [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="9fb0e-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="9fb0e-293">Avviare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-293">Start the web app.</span></span>
* <span data-ttu-id="9fb0e-294">Avviare Postman.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-294">Start Postman.</span></span>
* <span data-ttu-id="9fb0e-295">Disattivare **SSL certificate verification** (Verifica certificato SSL)</span><span class="sxs-lookup"><span data-stu-id="9fb0e-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="9fb0e-296">Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="9fb0e-297">Riattivare la verifica dei certificati SSL al termine del test del controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="9fb0e-298">Testare PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="9fb0e-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="9fb0e-299">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-299">Create a new request.</span></span>
* <span data-ttu-id="9fb0e-300">Impostare il metodo HTTP su `POST`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="9fb0e-301">Impostare l'URI su `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="9fb0e-302">Ad esempio, `https://localhost:5001/api/TodoItems`</span><span class="sxs-lookup"><span data-stu-id="9fb0e-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="9fb0e-303">Selezionare la scheda **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="9fb0e-304">Selezionare il pulsante di opzione **raw** (non elaborato).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="9fb0e-305">Impostare il tipo su **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="9fb0e-306">Nel corpo della richiesta immettere codice JSON per un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="9fb0e-307">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-307">Select **Send**.</span></span>

  ![Postman con richiesta di creazione](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="9fb0e-309">Testare l'URI dell'intestazione della posizione</span><span class="sxs-lookup"><span data-stu-id="9fb0e-309">Test the location header URI</span></span>

<span data-ttu-id="9fb0e-310">L'URI dell'intestazione Location può essere testato nel browser.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="9fb0e-311">Copiare e incollare l'URI dell'intestazione location nel browser.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="9fb0e-312">Per eseguire il test in un post:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-312">To test in Postman:</span></span>

* <span data-ttu-id="9fb0e-313">Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="9fb0e-314">Copiare il valore dell'intestazione **Location** (Posizione):</span><span class="sxs-lookup"><span data-stu-id="9fb0e-314">Copy the **Location** header value:</span></span>

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="9fb0e-316">Impostare il metodo HTTP su `GET`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="9fb0e-317">Impostare l'URI su `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="9fb0e-318">Ad esempio, `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="9fb0e-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="9fb0e-319">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="9fb0e-320">Esaminare i metodi GET</span><span class="sxs-lookup"><span data-stu-id="9fb0e-320">Examine the GET methods</span></span>

<span data-ttu-id="9fb0e-321">Vengono implementati due endpoint GET:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="9fb0e-322">Testare l'app chiamando i due endpoint da un browser o da Postman.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="9fb0e-323">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="9fb0e-324">Una risposta simile alla seguente viene generata dalla chiamata a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="9fb0e-325">Testare Get con Postman</span><span class="sxs-lookup"><span data-stu-id="9fb0e-325">Test Get with Postman</span></span>

* <span data-ttu-id="9fb0e-326">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-326">Create a new request.</span></span>
* <span data-ttu-id="9fb0e-327">Impostare il metodo HTTP su **GET**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="9fb0e-328">Impostare l'URI della richiesta su `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="9fb0e-329">Ad esempio, `https://localhost:5001/api/TodoItems`</span><span class="sxs-lookup"><span data-stu-id="9fb0e-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="9fb0e-330">Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="9fb0e-331">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-331">Select **Send**.</span></span>

<span data-ttu-id="9fb0e-332">Questa app usa un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-332">This app uses an in-memory database.</span></span> <span data-ttu-id="9fb0e-333">Se l'app viene arrestata e avviata, la richiesta GET precedente non restituirà alcun dato.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="9fb0e-334">Se non vengono restituiti dati, eseguire [POST](#post) per pubblicare i dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="9fb0e-335">Routing e percorsi di URL</span><span class="sxs-lookup"><span data-stu-id="9fb0e-335">Routing and URL paths</span></span>

<span data-ttu-id="9fb0e-336">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attributo indica un metodo che risponde a una richiesta HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="9fb0e-337">Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="9fb0e-338">Iniziare con la stringa di modello nell'attributo `Route` del controller:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="9fb0e-339">Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller".</span><span class="sxs-lookup"><span data-stu-id="9fb0e-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="9fb0e-340">In questo esempio il nome della classe controller è **TodoItems** Controller, quindi il nome del controller è "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="9fb0e-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="9fb0e-341">Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="9fb0e-342">Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="9fb0e-343">In questo esempio non si usa un modello.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-343">This sample doesn't use a template.</span></span> <span data-ttu-id="9fb0e-344">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9fb0e-345">Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="9fb0e-346">Quando `GetTodoItem` viene richiamato, il valore di `"{id}"` nell'URL viene fornito al metodo nel `id` parametro.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="9fb0e-347">Valori restituiti</span><span class="sxs-lookup"><span data-stu-id="9fb0e-347">Return values</span></span>

<span data-ttu-id="9fb0e-348">Il tipo restituito dei `GetTodoItems` metodi e `GetTodoItem` è [ActionResult. \<T> ](xref:web-api/action-return-types#actionresultt-type)</span><span class="sxs-lookup"><span data-stu-id="9fb0e-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="9fb0e-349">ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="9fb0e-350">Il codice di risposta per questo tipo restituito è [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), supponendo che non esistano eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="9fb0e-351">Le eccezioni non gestite vengono convertite in errori 5xx.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="9fb0e-352">I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="9fb0e-353">Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="9fb0e-354">Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un codice di errore di [stato 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="9fb0e-355">In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="9fb0e-356">La restituzione di `item` risulta in una risposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="9fb0e-357">Metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="9fb0e-357">The PutTodoItem method</span></span>

<span data-ttu-id="9fb0e-358">Esaminare il metodo `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="9fb0e-359">`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="9fb0e-360">La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="9fb0e-361">In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="9fb0e-362">Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="9fb0e-363">Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="9fb0e-364">Testare il metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="9fb0e-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="9fb0e-365">Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="9fb0e-366">Deve esistere un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="9fb0e-367">Chiamare GET per assicurarsi che sia presente un elemento nel database prima di effettuare una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="9fb0e-368">Aggiornare l'elemento to-do con ID = 1 e impostarne il nome su `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="9fb0e-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="9fb0e-369">L'immagine seguente visualizza l'aggiornamento di Postman:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-369">The following image shows the Postman update:</span></span>

![Console Postman con risposta 204 (No Content)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="9fb0e-371">Metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="9fb0e-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="9fb0e-372">Esaminare il metodo `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="9fb0e-373">Testare il metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="9fb0e-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="9fb0e-374">Usare Postman per eliminare un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="9fb0e-375">Impostare il metodo su `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="9fb0e-376">Impostare l'URI dell'oggetto da eliminare (ad esempio `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="9fb0e-377">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="9fb0e-378">Impedisci overposting</span><span class="sxs-lookup"><span data-stu-id="9fb0e-378">Prevent over-posting</span></span>

<span data-ttu-id="9fb0e-379">Attualmente l'app di esempio espone l'intero `TodoItem` oggetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="9fb0e-380">Le app di produzione limitano in genere i dati di input e restituiti usando un subset del modello.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="9fb0e-381">Esistono diversi motivi alla base di questo e la sicurezza è una delle principali.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="9fb0e-382">Il subset di un modello è in genere indicato come oggetto Trasferimento dati (DTO), modello di input o modello di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="9fb0e-383">**Dto** viene usato in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="9fb0e-384">Un DTO può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-384">A DTO may be used to:</span></span>

* <span data-ttu-id="9fb0e-385">Impedisci l'overposting.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-385">Prevent over-posting.</span></span>
* <span data-ttu-id="9fb0e-386">Nascondere le proprietà che i client non dovrebbero visualizzare.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="9fb0e-387">Omettere alcune proprietà per ridurre le dimensioni del payload.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="9fb0e-388">Rendere flat gli oggetti grafici che contengono oggetti annidati.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="9fb0e-389">I grafici a oggetti flat possono essere più pratici per i client.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="9fb0e-390">Per illustrare l'approccio DTO, aggiornare la `TodoItem` classe in modo da includere un campo Secret:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="9fb0e-391">Il campo segreto deve essere nascosto da questa app, ma un'app amministrativa può scegliere di esporla.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="9fb0e-392">Verificare che sia possibile pubblicare e ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="9fb0e-393">Creare un modello DTO:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="9fb0e-394">Aggiornare `TodoItemsController` da usare `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="9fb0e-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="9fb0e-395">Verificare che non sia possibile inserire o ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="9fb0e-396">Chiamare l'API Web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="9fb0e-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="9fb0e-397">Vedere [esercitazione: chiamare un'API web ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="9fb0e-398">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9fb0e-399">Creare un progetto di API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-399">Create a web API project.</span></span>
> * <span data-ttu-id="9fb0e-400">Aggiungere una classe modello e un contesto di database.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="9fb0e-401">Eseguire lo scaffolding di un controller con i metodi CRUD.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="9fb0e-402">Configurare il routing, i percorsi URL e i valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="9fb0e-403">Chiamare l'API Web con Postman.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-403">Call the web API with Postman.</span></span>

<span data-ttu-id="9fb0e-404">Al termine si avrà un'API Web che può gestire gli elementi di tipo "attività" archiviati in un database.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="9fb0e-405">Panoramica</span><span class="sxs-lookup"><span data-stu-id="9fb0e-405">Overview</span></span>

<span data-ttu-id="9fb0e-406">Questa esercitazione consente di creare l'API seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="9fb0e-407">API</span><span class="sxs-lookup"><span data-stu-id="9fb0e-407">API</span></span> | <span data-ttu-id="9fb0e-408">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9fb0e-408">Description</span></span> | <span data-ttu-id="9fb0e-409">Corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="9fb0e-409">Request body</span></span> | <span data-ttu-id="9fb0e-410">Corpo della risposta</span><span class="sxs-lookup"><span data-stu-id="9fb0e-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="9fb0e-411">Ottiene tutti gli elementi attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-411">Get all to-do items</span></span> | <span data-ttu-id="9fb0e-412">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-412">None</span></span> | <span data-ttu-id="9fb0e-413">Matrice di elementi attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="9fb0e-414">Ottiene un elemento in base all'ID</span><span class="sxs-lookup"><span data-stu-id="9fb0e-414">Get an item by ID</span></span> | <span data-ttu-id="9fb0e-415">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-415">None</span></span> | <span data-ttu-id="9fb0e-416">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="9fb0e-417">Aggiunge un nuovo elemento</span><span class="sxs-lookup"><span data-stu-id="9fb0e-417">Add a new item</span></span> | <span data-ttu-id="9fb0e-418">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-418">To-do item</span></span> | <span data-ttu-id="9fb0e-419">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="9fb0e-420">Aggiorna un elemento esistente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9fb0e-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="9fb0e-421">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-421">To-do item</span></span> | <span data-ttu-id="9fb0e-422">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-422">None</span></span> |
|<span data-ttu-id="9fb0e-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9fb0e-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="9fb0e-424">Elimina un elemento &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="9fb0e-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="9fb0e-425">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-425">None</span></span> | <span data-ttu-id="9fb0e-426">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-426">None</span></span>|

<span data-ttu-id="9fb0e-427">Il diagramma seguente visualizza la struttura dell'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-427">The following diagram shows the design of the app.</span></span>

![Il client è rappresentato da una casella a sinistra.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="9fb0e-433">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="9fb0e-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9fb0e-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9fb0e-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-436">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="9fb0e-437">Creare un progetto Web</span><span class="sxs-lookup"><span data-stu-id="9fb0e-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fb0e-439">Scegliere **nuovo** progetto dal menu **file** > **Project**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9fb0e-440">Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="9fb0e-441">Assegnare al progetto il nome *TodoApi* e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="9fb0e-442">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.net Core** e **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="9fb0e-443">Selezionare il modello **API** e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-443">Select the **API** template and click **Create**.</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9fb0e-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9fb0e-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9fb0e-446">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9fb0e-447">Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="9fb0e-448">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="9fb0e-449">Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="9fb0e-450">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-450">The preceding commands:</span></span>

  * <span data-ttu-id="9fb0e-451">Crea un nuovo progetto API Web e lo apre in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="9fb0e-452">Aggiunge i pacchetti NuGet necessari nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-453">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9fb0e-454">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-454">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="9fb0e-456">In Visual Studio per Mac precedente alla versione 8,6 Selezionare API **app .NET Core**  >  **App**  >  **API**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="9fb0e-457">Nella versione 8,6 o successive selezionare API **app Web e console**  >  **App**  >  **API**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Selezione modello API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="9fb0e-459">Nella finestra di dialogo **Configura nuova ASP.NET Core API Web** selezionare il **Framework di destinazione**.NET Core 3. x più recente.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="9fb0e-460">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-460">Select **Next**.</span></span>

* <span data-ttu-id="9fb0e-461">Immettere *TodoApi* in **Nome progetto**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="9fb0e-463">Aprire un terminale di comando nella cartella di progetto ed eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="9fb0e-464">Testare l'API</span><span class="sxs-lookup"><span data-stu-id="9fb0e-464">Test the API</span></span>

<span data-ttu-id="9fb0e-465">Il modello di progetto crea un'API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="9fb0e-466">Chiamare il metodo `Get` da un browser per eseguire il test dell'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9fb0e-468">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9fb0e-469">Visual Studio apre un browser e naviga all'indirizzo `https://localhost:<port>/WeatherForecast`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="9fb0e-470">Se in una finestra di dialogo viene chiesto se considerare attendibile il certificato IIS Express, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="9fb0e-471">Nella finestra di dialogo **Avviso di sicurezza** successiva selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9fb0e-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9fb0e-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9fb0e-473">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9fb0e-474">In un browser passare all'URL seguente: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-475">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9fb0e-476">Selezionare **Esegui**  >  **Avvia debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="9fb0e-477">Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="9fb0e-478">Viene restituito un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="9fb0e-479">Accodare `/WeatherForecast` all'URL (impostare l'URL su `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="9fb0e-480">Viene restituito un codice JSON simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-480">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="9fb0e-481">Aggiungere una classe del modello</span><span class="sxs-lookup"><span data-stu-id="9fb0e-481">Add a model class</span></span>

<span data-ttu-id="9fb0e-482">Un *modello* è un set di classi che rappresentano i dati gestiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="9fb0e-483">Il modello per questa app è una classe `TodoItem` singola.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fb0e-485">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="9fb0e-486">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9fb0e-487">Assegnare un nome alla cartella *Models* .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="9fb0e-488">Fare clic con il pulsante destro del mouse sulla *Models* cartella e scegliere **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9fb0e-489">Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="9fb0e-490">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9fb0e-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9fb0e-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9fb0e-492">Aggiungere una cartella denominata *Models* .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="9fb0e-493">Aggiungere una `TodoItem` classe alla *Models* cartella con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-494">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9fb0e-495">Fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-495">Right-click the project.</span></span> <span data-ttu-id="9fb0e-496">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9fb0e-497">Assegnare un nome alla cartella *Models* .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-497">Name the folder *Models*.</span></span>

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="9fb0e-499">Fare clic con il pulsante destro del mouse sulla *Models* cartella e scegliere **Aggiungi** > **nuovo file** > **generale** > **vuota classe**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="9fb0e-500">Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="9fb0e-501">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="9fb0e-502">La proprietà `Id` funziona come chiave univoca in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="9fb0e-503">Le classi di modelli possono essere inserite in qualsiasi punto del progetto, ma la *Models* cartella viene utilizzata per convenzione.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="9fb0e-504">Aggiungere un contesto di database</span><span class="sxs-lookup"><span data-stu-id="9fb0e-504">Add a database context</span></span>

<span data-ttu-id="9fb0e-505">Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="9fb0e-506">Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="9fb0e-508">Aggiungere pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="9fb0e-508">Add NuGet packages</span></span>

* <span data-ttu-id="9fb0e-509">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="9fb0e-510">Selezionare la scheda **Esplora** e quindi immettere **Microsoft.EntityFrameworkCore.SqlServer** nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="9fb0e-511">Nel riquadro sinistro selezionare **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="9fb0e-512">Selezionare la casella di controllo **Progetto** nel riquadro di destra e quindi selezionare **Installa**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="9fb0e-513">Usare le istruzioni precedenti per aggiungere il pacchetto NuGet **Microsoft. EntityFrameworkCore. InMemory** .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Gestione pacchetti NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="9fb0e-515">Aggiungere il contesto del database TodoContext</span><span class="sxs-lookup"><span data-stu-id="9fb0e-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="9fb0e-516">Fare clic con il pulsante destro del mouse sulla *Models* cartella e scegliere **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9fb0e-517">Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-518">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9fb0e-519">Aggiungere una `TodoContext` classe alla *Models* cartella.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="9fb0e-520">Immettere il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="9fb0e-521">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="9fb0e-521">Register the database context</span></span>

<span data-ttu-id="9fb0e-522">In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9fb0e-523">Il contenitore rende disponibile il servizio ai controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="9fb0e-524">Aggiornare *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="9fb0e-525">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-525">The preceding code:</span></span>

* <span data-ttu-id="9fb0e-526">Rimuove le dichiarazioni `using` inutilizzate.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="9fb0e-527">Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="9fb0e-528">Specifica che il contesto del database userà un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="9fb0e-529">Eseguire lo scaffolding di un controller</span><span class="sxs-lookup"><span data-stu-id="9fb0e-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-530">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fb0e-531">Fare clic con il pulsante destro del mouse sulla cartella *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="9fb0e-532">Selezionare **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="9fb0e-533">Selezionare **Controller API con azioni, che usa Entity Framework** e quindi selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="9fb0e-534">Nella finestra di dialogo **Add API Controller with actions, using Entity Framework** (Aggiungi controller API con azioni, che usa Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="9fb0e-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="9fb0e-535">Selezionare **TodoItem (TodoApi. Models )** nella **classe del modello**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="9fb0e-536">Selezionare **TodoContext (TodoApi. Models )** nella **classe del contesto dati**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="9fb0e-537">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-538">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9fb0e-539">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="9fb0e-540">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-540">The preceding commands:</span></span>

* <span data-ttu-id="9fb0e-541">Aggiungere i pacchetti NuGet necessari per lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="9fb0e-542">Installa il motore di scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="9fb0e-543">Esegue lo scaffolding di `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="9fb0e-544">Il codice generato:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-544">The generated code:</span></span>

* <span data-ttu-id="9fb0e-545">Contrassegna la classe con l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="9fb0e-546">L'attributo indica che il controller risponde alle richieste di API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="9fb0e-547">Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="9fb0e-548">Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="9fb0e-549">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="9fb0e-550">I modelli di ASP.NET Core per:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="9fb0e-551">I controller con le visualizzazioni includono `[action]` nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="9fb0e-552">I controller API non includono `[action]` nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="9fb0e-553">Quando il `[action]` token non è incluso nel modello di route, il nome dell' [azione](xref:mvc/controllers/routing#action) viene escluso dalla route.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="9fb0e-554">Ovvero, il nome del metodo associato all'azione non viene usato nella route corrispondente.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="9fb0e-555">Esaminare il metodo di creazione PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="9fb0e-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="9fb0e-556">Sostituire l'istruzione return in `PostTodoItem` per usare l'operatore [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="9fb0e-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="9fb0e-557">Il codice precedente è un metodo HTTP POST, come indicato dall' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="9fb0e-558">Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="9fb0e-559">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9fb0e-560">Il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="9fb0e-561">Restituisce un codice di stato HTTP 201 in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="9fb0e-562">HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="9fb0e-563">Aggiunge un'intestazione [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) alla risposta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="9fb0e-564">L'intestazione `Location` specifica l'[URI](https://developer.mozilla.org/docs/Glossary/URI) dell'elemento attività appena creato.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="9fb0e-565">Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="9fb0e-566">Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="9fb0e-567">La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="9fb0e-568">Installare Postman</span><span class="sxs-lookup"><span data-stu-id="9fb0e-568">Install Postman</span></span>

<span data-ttu-id="9fb0e-569">Questa esercitazione usa Postman per testare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="9fb0e-570">Installa il [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="9fb0e-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="9fb0e-571">Avviare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-571">Start the web app.</span></span>
* <span data-ttu-id="9fb0e-572">Avviare Postman.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-572">Start Postman.</span></span>
* <span data-ttu-id="9fb0e-573">Disattivare **SSL certificate verification** (Verifica certificato SSL)</span><span class="sxs-lookup"><span data-stu-id="9fb0e-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="9fb0e-574">Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="9fb0e-575">Riattivare la verifica dei certificati SSL al termine del test del controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="9fb0e-576">Testare PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="9fb0e-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="9fb0e-577">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-577">Create a new request.</span></span>
* <span data-ttu-id="9fb0e-578">Impostare il metodo HTTP su `POST`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="9fb0e-579">Impostare l'URI su `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="9fb0e-580">Ad esempio, `https://localhost:5001/api/TodoItems`</span><span class="sxs-lookup"><span data-stu-id="9fb0e-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="9fb0e-581">Selezionare la scheda **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="9fb0e-582">Selezionare il pulsante di opzione **raw** (non elaborato).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="9fb0e-583">Impostare il tipo su **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="9fb0e-584">Nel corpo della richiesta immettere codice JSON per un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="9fb0e-585">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-585">Select **Send**.</span></span>

  ![Postman con richiesta di creazione](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="9fb0e-587">Testare l'URI dell'intestazione Location con post</span><span class="sxs-lookup"><span data-stu-id="9fb0e-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="9fb0e-588">Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="9fb0e-589">Copiare il valore dell'intestazione **Location** (Posizione):</span><span class="sxs-lookup"><span data-stu-id="9fb0e-589">Copy the **Location** header value:</span></span>

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="9fb0e-591">Impostare il metodo HTTP su `GET`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="9fb0e-592">Impostare l'URI su `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="9fb0e-593">Ad esempio, `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="9fb0e-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="9fb0e-594">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="9fb0e-595">Esaminare i metodi GET</span><span class="sxs-lookup"><span data-stu-id="9fb0e-595">Examine the GET methods</span></span>

<span data-ttu-id="9fb0e-596">Questi metodi implementano due metodi GET:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="9fb0e-597">Testare l'app chiamando i due endpoint da un browser o da Postman.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="9fb0e-598">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="9fb0e-599">Una risposta simile alla seguente viene generata dalla chiamata a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="9fb0e-600">Testare Get con Postman</span><span class="sxs-lookup"><span data-stu-id="9fb0e-600">Test Get with Postman</span></span>

* <span data-ttu-id="9fb0e-601">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-601">Create a new request.</span></span>
* <span data-ttu-id="9fb0e-602">Impostare il metodo HTTP su **GET**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="9fb0e-603">Impostare l'URI della richiesta su `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="9fb0e-604">Ad esempio, `https://localhost:5001/api/TodoItems`</span><span class="sxs-lookup"><span data-stu-id="9fb0e-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="9fb0e-605">Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="9fb0e-606">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-606">Select **Send**.</span></span>

<span data-ttu-id="9fb0e-607">Questa app usa un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-607">This app uses an in-memory database.</span></span> <span data-ttu-id="9fb0e-608">Se l'app viene arrestata e avviata, la richiesta GET precedente non restituirà alcun dato.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="9fb0e-609">Se non vengono restituiti dati, eseguire [POST](#post) per pubblicare i dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="9fb0e-610">Routing e percorsi di URL</span><span class="sxs-lookup"><span data-stu-id="9fb0e-610">Routing and URL paths</span></span>

<span data-ttu-id="9fb0e-611">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attributo indica un metodo che risponde a una richiesta HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="9fb0e-612">Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="9fb0e-613">Iniziare con la stringa di modello nell'attributo `Route` del controller:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="9fb0e-614">Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller".</span><span class="sxs-lookup"><span data-stu-id="9fb0e-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="9fb0e-615">In questo esempio il nome della classe controller è **TodoItems** Controller, quindi il nome del controller è "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="9fb0e-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="9fb0e-616">Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="9fb0e-617">Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="9fb0e-618">In questo esempio non si usa un modello.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-618">This sample doesn't use a template.</span></span> <span data-ttu-id="9fb0e-619">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9fb0e-620">Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="9fb0e-621">Quando `GetTodoItem` viene richiamato, il valore di `"{id}"` nell'URL viene fornito al metodo nel `id` parametro.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="9fb0e-622">Valori restituiti</span><span class="sxs-lookup"><span data-stu-id="9fb0e-622">Return values</span></span> 

<span data-ttu-id="9fb0e-623">Il tipo restituito dei `GetTodoItems` metodi e `GetTodoItem` è [ActionResult. \<T> ](xref:web-api/action-return-types#actionresultt-type)</span><span class="sxs-lookup"><span data-stu-id="9fb0e-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="9fb0e-624">ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="9fb0e-625">Il codice di risposta per questo tipo restituito è 200, presupponendo che non esistano eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="9fb0e-626">Le eccezioni non gestite vengono convertite in errori 5xx.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="9fb0e-627">I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="9fb0e-628">Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="9fb0e-629">Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> codice di errore 404.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="9fb0e-630">In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="9fb0e-631">La restituzione di `item` risulta in una risposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="9fb0e-632">Metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="9fb0e-632">The PutTodoItem method</span></span>

<span data-ttu-id="9fb0e-633">Esaminare il metodo `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="9fb0e-634">`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="9fb0e-635">La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="9fb0e-636">In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="9fb0e-637">Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="9fb0e-638">Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="9fb0e-639">Testare il metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="9fb0e-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="9fb0e-640">Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="9fb0e-641">Deve esistere un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="9fb0e-642">Chiamare GET per assicurarsi che sia presente un elemento nel database prima di effettuare una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="9fb0e-643">Aggiornare l'attività con ID = 1 e impostarne il nome su "feed Fish":</span><span class="sxs-lookup"><span data-stu-id="9fb0e-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="9fb0e-644">L'immagine seguente visualizza l'aggiornamento di Postman:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-644">The following image shows the Postman update:</span></span>

![Console Postman con risposta 204 (No Content)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="9fb0e-646">Metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="9fb0e-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="9fb0e-647">Esaminare il metodo `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="9fb0e-648">Testare il metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="9fb0e-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="9fb0e-649">Usare Postman per eliminare un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="9fb0e-650">Impostare il metodo su `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="9fb0e-651">Impostare l'URI dell'oggetto da eliminare (ad esempio `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="9fb0e-652">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="9fb0e-653">Impedisci overposting</span><span class="sxs-lookup"><span data-stu-id="9fb0e-653">Prevent over-posting</span></span>

<span data-ttu-id="9fb0e-654">Attualmente l'app di esempio espone l'intero `TodoItem` oggetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="9fb0e-655">Le app di produzione limitano in genere i dati di input e restituiti usando un subset del modello.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="9fb0e-656">Esistono diversi motivi alla base di questo e la sicurezza è una delle principali.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="9fb0e-657">Il subset di un modello è in genere indicato come oggetto Trasferimento dati (DTO), modello di input o modello di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="9fb0e-658">**Dto** viene usato in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="9fb0e-659">Un DTO può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-659">A DTO may be used to:</span></span>

* <span data-ttu-id="9fb0e-660">Impedisci l'overposting.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-660">Prevent over-posting.</span></span>
* <span data-ttu-id="9fb0e-661">Nascondere le proprietà che i client non dovrebbero visualizzare.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="9fb0e-662">Omettere alcune proprietà per ridurre le dimensioni del payload.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="9fb0e-663">Rendere flat gli oggetti grafici che contengono oggetti annidati.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="9fb0e-664">I grafici a oggetti flat possono essere più pratici per i client.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="9fb0e-665">Per illustrare l'approccio DTO, aggiornare la `TodoItem` classe in modo da includere un campo Secret:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="9fb0e-666">Il campo segreto deve essere nascosto da questa app, ma un'app amministrativa può scegliere di esporla.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="9fb0e-667">Verificare che sia possibile pubblicare e ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="9fb0e-668">Creare un modello DTO:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="9fb0e-669">Aggiornare `TodoItemsController` da usare `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="9fb0e-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="9fb0e-670">Verificare che non sia possibile inserire o ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="9fb0e-671">Chiamare l'API Web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="9fb0e-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="9fb0e-672">Vedere [esercitazione: chiamare un'API web ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9fb0e-673">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9fb0e-674">Creare un progetto di API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-674">Create a web API project.</span></span>
> * <span data-ttu-id="9fb0e-675">Aggiungere una classe modello e un contesto di database.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="9fb0e-676">Aggiungere un controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-676">Add a controller.</span></span>
> * <span data-ttu-id="9fb0e-677">Aggiungere metodi CRUD.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="9fb0e-678">Configurare routing e percorsi URL.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="9fb0e-679">Specificare valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-679">Specify return values.</span></span>
> * <span data-ttu-id="9fb0e-680">Chiamare l'API Web con Postman.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="9fb0e-681">Chiamare l'API Web con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="9fb0e-682">Al termine si dispone di un'API web che può gestire gli elementi di tipo "attività" archiviati in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="9fb0e-683">Panoramica 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-683">Overview 2.1</span></span>

<span data-ttu-id="9fb0e-684">Questa esercitazione consente di creare l'API seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="9fb0e-685">API</span><span class="sxs-lookup"><span data-stu-id="9fb0e-685">API</span></span> | <span data-ttu-id="9fb0e-686">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9fb0e-686">Description</span></span> | <span data-ttu-id="9fb0e-687">Corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="9fb0e-687">Request body</span></span> | <span data-ttu-id="9fb0e-688">Corpo della risposta</span><span class="sxs-lookup"><span data-stu-id="9fb0e-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="9fb0e-689">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="9fb0e-689">GET /api/TodoItems</span></span> | <span data-ttu-id="9fb0e-690">Ottiene tutti gli elementi attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-690">Get all to-do items</span></span> | <span data-ttu-id="9fb0e-691">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-691">None</span></span> | <span data-ttu-id="9fb0e-692">Matrice di elementi attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-692">Array of to-do items</span></span>|
|<span data-ttu-id="9fb0e-693">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="9fb0e-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="9fb0e-694">Ottiene un elemento in base all'ID</span><span class="sxs-lookup"><span data-stu-id="9fb0e-694">Get an item by ID</span></span> | <span data-ttu-id="9fb0e-695">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-695">None</span></span> | <span data-ttu-id="9fb0e-696">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-696">To-do item</span></span>|
|<span data-ttu-id="9fb0e-697">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="9fb0e-697">POST /api/TodoItems</span></span> | <span data-ttu-id="9fb0e-698">Aggiunge un nuovo elemento</span><span class="sxs-lookup"><span data-stu-id="9fb0e-698">Add a new item</span></span> | <span data-ttu-id="9fb0e-699">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-699">To-do item</span></span> | <span data-ttu-id="9fb0e-700">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-700">To-do item</span></span> |
|<span data-ttu-id="9fb0e-701">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="9fb0e-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="9fb0e-702">Aggiorna un elemento esistente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9fb0e-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="9fb0e-703">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="9fb0e-703">To-do item</span></span> | <span data-ttu-id="9fb0e-704">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-704">None</span></span> |
|<span data-ttu-id="9fb0e-705">Elimina/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="9fb0e-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="9fb0e-706">Elimina un elemento &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="9fb0e-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="9fb0e-707">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-707">None</span></span> | <span data-ttu-id="9fb0e-708">nessuno</span><span class="sxs-lookup"><span data-stu-id="9fb0e-708">None</span></span>|

<span data-ttu-id="9fb0e-709">Il diagramma seguente visualizza la struttura dell'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-709">The following diagram shows the design of the app.</span></span>

![Il client è rappresentato da una casella a sinistra.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="9fb0e-715">Prerequisiti 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-716">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9fb0e-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9fb0e-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-718">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="9fb0e-719">Creare un progetto Web 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-720">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fb0e-721">Scegliere **nuovo** progetto dal menu **file** > **Project**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9fb0e-722">Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="9fb0e-723">Assegnare al progetto il nome *TodoApi* e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="9fb0e-724">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.NET Core** e **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="9fb0e-725">Selezionare il modello **API** e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="9fb0e-726">**Non** selezionare **Abilita supporto Docker**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-726">**Don't** select **Enable Docker Support**.</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9fb0e-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9fb0e-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9fb0e-729">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9fb0e-730">Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="9fb0e-731">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="9fb0e-732">Questi comandi creano un nuovo progetto API Web e aprono una nuova istanza di Visual Studio Code nella nuova cartella di progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="9fb0e-733">Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-734">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9fb0e-735">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-735">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="9fb0e-737">In Visual Studio per Mac precedente alla versione 8,6 Selezionare API **app .NET Core**  >  **App**  >  **API**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="9fb0e-738">Nella versione 8,6 o successive selezionare API **app Web e console**  >  **App**  >  **API**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="9fb0e-739">Nella finestra di dialogo **Configura nuova ASP.NET Core API Web** selezionare il **Framework di destinazione**.NET Core 2. x più recente.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="9fb0e-740">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-740">Select **Next**.</span></span>

* <span data-ttu-id="9fb0e-741">Immettere *TodoApi* in **Nome progetto**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="9fb0e-743">Testare l'API 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-743">Test the API 2.1</span></span>

<span data-ttu-id="9fb0e-744">Il modello di progetto crea un'API `values`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-744">The project template creates a `values` API.</span></span> <span data-ttu-id="9fb0e-745">Chiamare il metodo `Get` da un browser per eseguire il test dell'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-746">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9fb0e-747">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9fb0e-748">Visual Studio apre un browser e naviga all'indirizzo `https://localhost:<port>/api/values`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="9fb0e-749">Se in una finestra di dialogo viene chiesto se considerare attendibile il certificato IIS Express, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="9fb0e-750">Nella finestra di dialogo **Avviso di sicurezza** successiva selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9fb0e-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9fb0e-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9fb0e-752">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9fb0e-753">In un browser passare all'URL seguente: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-754">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9fb0e-755">Selezionare **Esegui**  >  **Avvia debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="9fb0e-756">Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="9fb0e-757">Viene restituito un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="9fb0e-758">Accodare `/api/values` all'URL (impostare l'URL su `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="9fb0e-759">Viene restituito il codice JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="9fb0e-760">Aggiungere una classe modello 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-760">Add a model class 2.1</span></span>

<span data-ttu-id="9fb0e-761">Un *modello* è un set di classi che rappresentano i dati gestiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="9fb0e-762">Il modello per questa app è una classe `TodoItem` singola.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-763">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fb0e-764">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="9fb0e-765">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9fb0e-766">Assegnare un nome alla cartella *Models* .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="9fb0e-767">Fare clic con il pulsante destro del mouse sulla *Models* cartella e scegliere **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9fb0e-768">Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="9fb0e-769">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9fb0e-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9fb0e-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9fb0e-771">Aggiungere una cartella denominata *Models* .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="9fb0e-772">Aggiungere una `TodoItem` classe alla *Models* cartella con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-773">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9fb0e-774">Fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-774">Right-click the project.</span></span> <span data-ttu-id="9fb0e-775">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9fb0e-776">Assegnare un nome alla cartella *Models* .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-776">Name the folder *Models*.</span></span>

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="9fb0e-778">Fare clic con il pulsante destro del mouse sulla *Models* cartella e scegliere **Aggiungi** > **nuovo file** > **generale** > **vuota classe**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="9fb0e-779">Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="9fb0e-780">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="9fb0e-781">La proprietà `Id` funziona come chiave univoca in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="9fb0e-782">Le classi di modelli possono essere inserite in qualsiasi punto del progetto, ma la *Models* cartella viene utilizzata per convenzione.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="9fb0e-783">Aggiungere un contesto di database 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-783">Add a database context 2.1</span></span>

<span data-ttu-id="9fb0e-784">Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="9fb0e-785">Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-786">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fb0e-787">Fare clic con il pulsante destro del mouse sulla *Models* cartella e scegliere **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9fb0e-788">Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-789">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9fb0e-790">Aggiungere una `TodoContext` classe alla *Models* cartella.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="9fb0e-791">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="9fb0e-792">Registrare il contesto del database 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-792">Register the database context 2.1</span></span>

<span data-ttu-id="9fb0e-793">In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9fb0e-794">Il contenitore rende disponibile il servizio ai controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="9fb0e-795">Aggiornare *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="9fb0e-796">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-796">The preceding code:</span></span>

* <span data-ttu-id="9fb0e-797">Rimuove le dichiarazioni `using` inutilizzate.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="9fb0e-798">Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="9fb0e-799">Specifica che il contesto del database userà un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="9fb0e-800">Aggiungere un controller 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-801">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fb0e-802">Fare clic con il pulsante destro del mouse sulla cartella *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="9fb0e-803">Selezionare **Aggiungi** > **nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="9fb0e-804">Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare il modello **API Controller Class** (Classe controller API).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="9fb0e-805">Assegnare alla classe il nome *TodoController* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![Finestra di dialogo Aggiungi nuovo elemento con controller nella casella di ricerca e controller API Web selezionato](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-807">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9fb0e-808">Nella cartella *Controllers* creare una classe denominata `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="9fb0e-809">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="9fb0e-810">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-810">The preceding code:</span></span>

* <span data-ttu-id="9fb0e-811">Definisce una classe controller API senza metodi.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="9fb0e-812">Contrassegna la classe con l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="9fb0e-813">L'attributo indica che il controller risponde alle richieste di API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="9fb0e-814">Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="9fb0e-815">Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="9fb0e-816">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="9fb0e-817">Aggiunge un elemento denominato `Item1` al database se il database è vuoto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="9fb0e-818">Questo codice si trova nel costruttore, quindi viene eseguito ogni volta che è presente una nuova richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="9fb0e-819">Se si eliminano tutti gli elementi, il costruttore crea di nuovo `Item1` quando viene nuovamente chiamato un metodo API.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="9fb0e-820">Potrebbe pertanto sembrare che l'eliminazione non abbia funzionato, mentre di fatto ha funzionato.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="9fb0e-821">Aggiungi metodi Get 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-821">Add Get methods 2.1</span></span>

<span data-ttu-id="9fb0e-822">Per specificare un'API che recupera elementi attività, aggiungere i metodi seguenti alla classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="9fb0e-823">Questi metodi implementano due metodi GET:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="9fb0e-824">Arrestare l'app se è ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-824">Stop the app if it's still running.</span></span> <span data-ttu-id="9fb0e-825">Quindi eseguirla di nuovo per includere le modifiche più recenti.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="9fb0e-826">Testare l'app chiamando i due endpoint da un browser.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="9fb0e-827">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="9fb0e-828">La chiamata a `GetTodoItems` genera la risposta HTTP seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="9fb0e-829">Percorsi URL e routing 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="9fb0e-830">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attributo indica un metodo che risponde a una richiesta HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="9fb0e-831">Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="9fb0e-832">Iniziare con la stringa di modello nell'attributo `Route` del controller:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="9fb0e-833">Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller".</span><span class="sxs-lookup"><span data-stu-id="9fb0e-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="9fb0e-834">In questo esempio il nome della classe controller è **Todo** Controller, pertanto il nome del controller è "todo".</span><span class="sxs-lookup"><span data-stu-id="9fb0e-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="9fb0e-835">Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="9fb0e-836">Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="9fb0e-837">In questo esempio non si usa un modello.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-837">This sample doesn't use a template.</span></span> <span data-ttu-id="9fb0e-838">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9fb0e-839">Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="9fb0e-840">Quando viene chiamato `GetTodoItem`, il valore di `"{id}"` viene specificato per il metodo nel rispettivo parametro `id`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="9fb0e-841">Valori restituiti 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-841">Return values 2.1</span></span>

<span data-ttu-id="9fb0e-842">Il tipo restituito dei `GetTodoItems` metodi e `GetTodoItem` è [ActionResult. \<T> ](xref:web-api/action-return-types#actionresultt-type)</span><span class="sxs-lookup"><span data-stu-id="9fb0e-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="9fb0e-843">ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="9fb0e-844">Il codice di risposta per questo tipo restituito è 200, presupponendo che non esistano eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="9fb0e-845">Le eccezioni non gestite vengono convertite in errori 5xx.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="9fb0e-846">I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="9fb0e-847">Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="9fb0e-848">Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> codice di errore 404.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="9fb0e-849">In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="9fb0e-850">La restituzione di `item` risulta in una risposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="9fb0e-851">Testare il metodo GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="9fb0e-852">Questa esercitazione usa Postman per testare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="9fb0e-853">Installare [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="9fb0e-854">Avviare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-854">Start the web app.</span></span>
* <span data-ttu-id="9fb0e-855">Avviare Postman.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-855">Start Postman.</span></span>
* <span data-ttu-id="9fb0e-856">Disabilitare la **Verifica del certificato SSL**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fb0e-857">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fb0e-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fb0e-858">Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9fb0e-859">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9fb0e-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9fb0e-860">Dalle preferenze del **poster**  >  **Preferences** (scheda **generale** ) disabilitare la **Verifica del certificato SSL**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="9fb0e-861">In alternativa, selezionare la chiave inglese e selezionare **Settings** (Impostazioni), quindi disabilitare la verifica del certificato SSL.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="9fb0e-862">Riattivare la verifica dei certificati SSL al termine del test del controller.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="9fb0e-863">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-863">Create a new request.</span></span>
  * <span data-ttu-id="9fb0e-864">Impostare il metodo HTTP su **GET**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="9fb0e-865">Impostare l'URI della richiesta su `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="9fb0e-866">Ad esempio, `https://localhost:5001/api/todo`</span><span class="sxs-lookup"><span data-stu-id="9fb0e-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="9fb0e-867">Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="9fb0e-868">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-868">Select **Send**.</span></span>

![Postman con richiesta GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="9fb0e-870">Aggiungere un metodo Create 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-870">Add a Create method 2.1</span></span>

<span data-ttu-id="9fb0e-871">Aggiungere il metodo `PostTodoItem` seguente in *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="9fb0e-872">Il codice precedente è un metodo HTTP POST, come indicato dall' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="9fb0e-873">Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="9fb0e-874">Il metodo `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="9fb0e-875">Restituisce un codice di stato HTTP 201 in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="9fb0e-876">HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="9fb0e-877">Aggiunge un'intestazione `Location` alla risposta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="9fb0e-878">L'intestazione `Location` specifica l'URI dell'elemento attività appena creato.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="9fb0e-879">Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="9fb0e-880">Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="9fb0e-881">La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="9fb0e-882">Testare il metodo PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="9fb0e-883">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-883">Build the project.</span></span>
* <span data-ttu-id="9fb0e-884">In Postman impostare il metodo HTTP su `POST`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="9fb0e-885">Impostare l'URI su `https://localhost:<port>/api/Todo` .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="9fb0e-886">Ad esempio, `https://localhost:5001/api/Todo`</span><span class="sxs-lookup"><span data-stu-id="9fb0e-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="9fb0e-887">Selezionare la scheda **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="9fb0e-888">Selezionare il pulsante di opzione **raw** (non elaborato).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="9fb0e-889">Impostare il tipo su **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="9fb0e-890">Nel corpo della richiesta immettere codice JSON per un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="9fb0e-891">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-891">Select **Send**.</span></span>

  ![Postman con richiesta di creazione](first-web-api/_static/create.png)

  <span data-ttu-id="9fb0e-893">Se viene visualizzato un errore HTTP 405 - Metodo non consentito, è probabile che il progetto non sia stato compilato dopo l'aggiunta del metodo `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="9fb0e-894">Testare l'URI dell'intestazione Location 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="9fb0e-895">Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="9fb0e-896">Copiare il valore dell'intestazione **Location** (Posizione):</span><span class="sxs-lookup"><span data-stu-id="9fb0e-896">Copy the **Location** header value:</span></span>

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="9fb0e-898">Impostare il metodo su GET.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-898">Set the method to GET.</span></span>
* <span data-ttu-id="9fb0e-899">Impostare l'URI su `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="9fb0e-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="9fb0e-900">Ad esempio, `https://localhost:5001/api/TodoItems/2`</span><span class="sxs-lookup"><span data-stu-id="9fb0e-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="9fb0e-901">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="9fb0e-902">Aggiungere un metodo PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="9fb0e-903">Aggiungere il metodo `PutTodoItem` seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="9fb0e-904">`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="9fb0e-905">La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="9fb0e-906">In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="9fb0e-907">Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="9fb0e-908">Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="9fb0e-909">Testare il metodo PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="9fb0e-910">Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="9fb0e-911">Deve esistere un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="9fb0e-912">Chiamare GET per assicurarsi che sia presente un elemento nel database prima di effettuare una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="9fb0e-913">Aggiornare l'attività con ID = 1 e impostarne il nome su "feed Fish":</span><span class="sxs-lookup"><span data-stu-id="9fb0e-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="9fb0e-914">L'immagine seguente visualizza l'aggiornamento di Postman:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-914">The following image shows the Postman update:</span></span>

![Console Postman con risposta 204 (No Content)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="9fb0e-916">Aggiungere un metodo DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="9fb0e-917">Aggiungere il metodo `DeleteTodoItem` seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="9fb0e-918">La `DeleteTodoItem` risposta è [204 (nessun contenuto)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="9fb0e-919">Testare il metodo DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="9fb0e-920">Usare Postman per eliminare un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="9fb0e-921">Impostare il metodo su `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="9fb0e-922">Impostare l'URI dell'oggetto da eliminare (ad esempio, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="9fb0e-923">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-923">Select **Send**.</span></span>

<span data-ttu-id="9fb0e-924">L'app di esempio consente di eliminare tutti gli elementi.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="9fb0e-925">Quando viene eliminato l'ultimo elemento, tuttavia, ne viene creato uno nuovo dal costruttore della classe modello alla successiva chiamata dell'API.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="9fb0e-926">Chiamare l'API Web con JavaScript 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="9fb0e-927">In questa sezione viene aggiunta una pagina HTML che usa JavaScript per chiamare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="9fb0e-928">jQuery avvia la richiesta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-928">jQuery initiates the request.</span></span> <span data-ttu-id="9fb0e-929">JavaScript aggiorna la pagina con i dettagli della risposta dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="9fb0e-930">Configurare l'app per [servire file statici](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) e [abilitare il mapping del file predefinito](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) aggiornando *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="9fb0e-931">Creare una cartella *wwwroot* nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="9fb0e-932">Aggiungere un file HTML denominato *index.html* alla directory *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="9fb0e-933">Sostituire il contenuto con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="9fb0e-934">Aggiungere un file JavaScript con nome *site.js* alla directory *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="9fb0e-935">Sostituire il contenuto con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="9fb0e-936">Può essere necessario modificare le impostazioni di avvio del progetto ASP.NET Core per il test della pagina HTML in locale:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="9fb0e-937">Aprire *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="9fb0e-938">Rimuovere la `launchUrl` proprietà per forzare l'apertura dell'app a *index.html* &mdash; file predefinito del progetto.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="9fb0e-939">In questo esempio vengono chiamati tutti i metodi CRUD dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="9fb0e-940">Di seguito sono incluse le spiegazioni delle chiamate all'API.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="9fb0e-941">Ottenere un elenco di elementi attività 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="9fb0e-942">jQuery invia una richiesta HTTP GET all'API Web, che restituisce JSON che rappresenta una matrice di elementi attività.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="9fb0e-943">La funzione di callback `success` viene chiamata se la richiesta ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="9fb0e-944">Nel callback il modello DOM viene aggiornato con le informazioni dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="9fb0e-945">Aggiungere un'attività 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="9fb0e-946">jQuery invia una richiesta HTTP POST con l'elemento attività nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="9fb0e-947">Le opzioni `accepts` e `contentType` sono impostate su `application/json` per specificare il tipo di supporto ricevuto e inviato.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="9fb0e-948">L'elemento attività viene convertito in JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="9fb0e-949">Quando l'API restituisce un codice di stato di esecuzione riuscita, viene chiamata la funzione `getData` per aggiornare la tabella HTML.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="9fb0e-950">Aggiornare un'attività 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="9fb0e-951">L'aggiornamento di un elemento attività è simile all'aggiunta di un elemento di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="9fb0e-952">L'`url` cambia per includere l'identificatore univoco dell'elemento e `type` è `PUT`.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="9fb0e-953">Eliminare un'attività 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="9fb0e-954">È possibile eliminare un elemento attività impostando `type` su `DELETE` nella chiamata AJAX e specificando l'identificatore univoco dell'elemento nell'URL.</span><span class="sxs-lookup"><span data-stu-id="9fb0e-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="9fb0e-955">Aggiungere il supporto per l'autenticazione a un'API Web 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="9fb0e-956">Risorse aggiuntive 2,1</span><span class="sxs-lookup"><span data-stu-id="9fb0e-956">Additional resources 2.1</span></span>

<span data-ttu-id="9fb0e-957">[Visualizzare o scaricare il codice di esempio per questa esercitazione](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="9fb0e-958">Vedere [come scaricare un esempio](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="9fb0e-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="9fb0e-959">Per altre informazioni, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="9fb0e-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="9fb0e-960">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="9fb0e-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
