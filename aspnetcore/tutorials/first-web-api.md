---
title: "Esercitazione: creare un'API Web con ASP.NET Core"
author: rick-anderson
description: Informazioni su come creare un'API Web con ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
- ':::no-loc(Models):::'
uid: tutorials/first-web-api
ms.openlocfilehash: 17f04dc9a0bdcf8ff016d83b915c017ff485cb36
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690699"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="0b894-103">Esercitazione: creare un'API Web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0b894-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="0b894-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="0b894-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="0b894-105">Questa esercitazione illustra le nozioni di base della creazione di un'API Web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0b894-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0b894-106">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="0b894-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0b894-107">Creare un progetto di API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-107">Create a web API project.</span></span>
> * <span data-ttu-id="0b894-108">Aggiungere una classe modello e un contesto di database.</span><span class="sxs-lookup"><span data-stu-id="0b894-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0b894-109">Eseguire lo scaffolding di un controller con i metodi CRUD.</span><span class="sxs-lookup"><span data-stu-id="0b894-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="0b894-110">Configurare il routing, i percorsi URL e i valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="0b894-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="0b894-111">Chiamare l'API Web con Postman.</span><span class="sxs-lookup"><span data-stu-id="0b894-111">Call the web API with Postman.</span></span>

<span data-ttu-id="0b894-112">Al termine si avrà un'API Web che può gestire gli elementi di tipo "attività" archiviati in un database.</span><span class="sxs-lookup"><span data-stu-id="0b894-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="0b894-113">Panoramica</span><span class="sxs-lookup"><span data-stu-id="0b894-113">Overview</span></span>

<span data-ttu-id="0b894-114">Questa esercitazione consente di creare l'API seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0b894-115">API</span><span class="sxs-lookup"><span data-stu-id="0b894-115">API</span></span> | <span data-ttu-id="0b894-116">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0b894-116">Description</span></span> | <span data-ttu-id="0b894-117">Corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="0b894-117">Request body</span></span> | <span data-ttu-id="0b894-118">Corpo della risposta</span><span class="sxs-lookup"><span data-stu-id="0b894-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="0b894-119">Ottiene tutti gli elementi attività</span><span class="sxs-lookup"><span data-stu-id="0b894-119">Get all to-do items</span></span> | <span data-ttu-id="0b894-120">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-120">None</span></span> | <span data-ttu-id="0b894-121">Matrice di elementi attività</span><span class="sxs-lookup"><span data-stu-id="0b894-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="0b894-122">Ottiene un elemento in base all'ID</span><span class="sxs-lookup"><span data-stu-id="0b894-122">Get an item by ID</span></span> | <span data-ttu-id="0b894-123">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-123">None</span></span> | <span data-ttu-id="0b894-124">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="0b894-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="0b894-125">Aggiunge un nuovo elemento</span><span class="sxs-lookup"><span data-stu-id="0b894-125">Add a new item</span></span> | <span data-ttu-id="0b894-126">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="0b894-126">To-do item</span></span> | <span data-ttu-id="0b894-127">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="0b894-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="0b894-128">Aggiorna un elemento esistente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0b894-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0b894-129">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="0b894-129">To-do item</span></span> | <span data-ttu-id="0b894-130">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-130">None</span></span> |
|<span data-ttu-id="0b894-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0b894-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="0b894-132">Elimina un elemento &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="0b894-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0b894-133">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-133">None</span></span> | <span data-ttu-id="0b894-134">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-134">None</span></span>|

<span data-ttu-id="0b894-135">Il diagramma seguente visualizza la struttura dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-135">The following diagram shows the design of the app.</span></span>

![Il client è rappresentato da una casella a sinistra.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="0b894-141">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="0b894-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b894-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b894-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b894-144">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="0b894-145">Creare un progetto Web</span><span class="sxs-lookup"><span data-stu-id="0b894-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b894-147">Scegliere **nuovo** progetto dal menu **file** > **Project** .</span><span class="sxs-lookup"><span data-stu-id="0b894-147">From the **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="0b894-148">Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="0b894-148">Select the **ASP.NET Core Web Application** template and click **Next** .</span></span>
* <span data-ttu-id="0b894-149">Assegnare al progetto il nome *TodoApi* e fare clic su **Crea** .</span><span class="sxs-lookup"><span data-stu-id="0b894-149">Name the project *TodoApi* and click **Create** .</span></span>
* <span data-ttu-id="0b894-150">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.net Core** e **ASP.NET Core 5,0** .</span><span class="sxs-lookup"><span data-stu-id="0b894-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="0b894-151">Selezionare il modello **API** e fare clic su **Crea** .</span><span class="sxs-lookup"><span data-stu-id="0b894-151">Select the **API** template and click **Create** .</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b894-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b894-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0b894-154">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0b894-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0b894-155">Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0b894-156">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b894-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="0b894-157">Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="0b894-157">When a dialog box asks if you want to add required assets to the project, select **Yes** .</span></span>

  <span data-ttu-id="0b894-158">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="0b894-158">The preceding commands:</span></span>

  * <span data-ttu-id="0b894-159">Crea un nuovo progetto API Web e lo apre in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0b894-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="0b894-160">Aggiunge i pacchetti NuGet necessari nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="0b894-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b894-161">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0b894-162">Selezionare **File** > **Nuova soluzione** .</span><span class="sxs-lookup"><span data-stu-id="0b894-162">Select **File** > **New Solution** .</span></span>

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0b894-164">In Visual Studio per Mac precedente alla versione 8,6 Selezionare API **app .NET Core**  >  **App**  >  **API**  >  **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="0b894-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next** .</span></span> <span data-ttu-id="0b894-165">Nella versione 8,6 o successive selezionare API **app Web e console**  >  **App**  >  **API**  >  **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="0b894-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![Selezione modello API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="0b894-167">Nella finestra di dialogo **Configura nuova ASP.NET Core API Web** selezionare il **Framework di destinazione** .NET Core 3. x più recente.</span><span class="sxs-lookup"><span data-stu-id="0b894-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework** .</span></span> <span data-ttu-id="0b894-168">Selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="0b894-168">Select **Next** .</span></span>

* <span data-ttu-id="0b894-169">Immettere *TodoApi* in **Nome progetto** , quindi selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="0b894-169">Enter *TodoApi* for the **Project Name** and then select **Create** .</span></span>

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="0b894-171">Aprire un terminale di comando nella cartella di progetto ed eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b894-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="0b894-172">Testare il progetto</span><span class="sxs-lookup"><span data-stu-id="0b894-172">Test the project</span></span>

<span data-ttu-id="0b894-173">Il modello di progetto crea un' `WeatherForecast` API con supporto per [spavalderia](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="0b894-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0b894-175">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="0b894-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="0b894-176">Visual Studio viene avviato:</span><span class="sxs-lookup"><span data-stu-id="0b894-176">Visual Studio launches:</span></span>

* <span data-ttu-id="0b894-177">Server Web IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0b894-177">The IIS Express web server.</span></span>
* <span data-ttu-id="0b894-178">Il browser predefinito e passa a `https://localhost:<port>/https://localhost:5001/swagger/index.html` , dove `<port>` è un numero di porta scelto in modo casuale.</span><span class="sxs-lookup"><span data-stu-id="0b894-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b894-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b894-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="0b894-180">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0b894-181">In un browser passare all'URL seguente: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="0b894-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b894-182">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0b894-183">Selezionare **Esegui**  >  **Avvia debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0b894-184">Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="0b894-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0b894-185">Viene restituito un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="0b894-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0b894-186">Accodare `/swagger` all'URL (impostare l'URL su `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="0b894-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="0b894-187">Viene visualizzata la pagina di spavalderia `/swagger/index.html` .</span><span class="sxs-lookup"><span data-stu-id="0b894-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="0b894-188">Selezionare **Get**  >  **try it out**  >  **Execute (Esegui** ).</span><span class="sxs-lookup"><span data-stu-id="0b894-188">Select **GET** > **Try it out** > **Execute** .</span></span> <span data-ttu-id="0b894-189">Viene visualizzata la pagina:</span><span class="sxs-lookup"><span data-stu-id="0b894-189">The page displays:</span></span>

* <span data-ttu-id="0b894-190">Comando [curl](https://curl.haxx.se/) per testare l'API weatherforecast.</span><span class="sxs-lookup"><span data-stu-id="0b894-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="0b894-191">URL per testare l'API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="0b894-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="0b894-192">Il codice di risposta, il corpo e le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="0b894-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="0b894-193">Casella di riepilogo a discesa con i tipi di supporto e il valore e lo schema di esempio.</span><span class="sxs-lookup"><span data-stu-id="0b894-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="0b894-194">Spavalderia viene usato per generare la documentazione e le pagine della guida utili per le API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="0b894-195">Questa esercitazione è incentrata sulla creazione di un'API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="0b894-196">Per ulteriori informazioni su spavalderia, vedere <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="0b894-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="0b894-197">Copiare e incollare l' **URL della richiesta** nel browser:  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="0b894-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="0b894-198">Viene restituito un codice JSON simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="0b894-199">Aggiornare launchUrl</span><span class="sxs-lookup"><span data-stu-id="0b894-199">Update the launchUrl</span></span>

<span data-ttu-id="0b894-200">In *Properties\launchSettings.json* aggiornare `launchUrl` da `"swagger"` a `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="0b894-200">In *Properties\launchSettings.json* , update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="0b894-201">Poiché spavalderia è stato rimosso, il markup precedente modifica l'URL che viene avviato al metodo GET del controller aggiunto nelle sezioni riportate di seguito.</span><span class="sxs-lookup"><span data-stu-id="0b894-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="0b894-202">Aggiungere una classe del modello</span><span class="sxs-lookup"><span data-stu-id="0b894-202">Add a model class</span></span>

<span data-ttu-id="0b894-203">Un *modello* è un set di classi che rappresentano i dati gestiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0b894-204">Il modello per questa app è una classe `TodoItem` singola.</span><span class="sxs-lookup"><span data-stu-id="0b894-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b894-206">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-206">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="0b894-207">Selezionare **Aggiungi**  >  **nuova cartella** .</span><span class="sxs-lookup"><span data-stu-id="0b894-207">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="0b894-208">Assegnare un nome alla cartella *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="0b894-208">Name the folder *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="0b894-209">Fare clic con il pulsante destro del mouse sulla *:::no-loc(Models):::* cartella e scegliere **Aggiungi**  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="0b894-209">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="0b894-210">Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="0b894-210">Name the class *TodoItem* and select **Add** .</span></span>

* <span data-ttu-id="0b894-211">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b894-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b894-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0b894-213">Aggiungere una cartella denominata *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="0b894-213">Add a folder named *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="0b894-214">Aggiungere una `TodoItem` classe alla *:::no-loc(Models):::* cartella con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-214">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b894-215">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0b894-216">Fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-216">Right-click the project.</span></span> <span data-ttu-id="0b894-217">Selezionare **Aggiungi**  >  **nuova cartella** .</span><span class="sxs-lookup"><span data-stu-id="0b894-217">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="0b894-218">Assegnare un nome alla cartella *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="0b894-218">Name the folder *:::no-loc(Models):::* .</span></span>

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0b894-220">Fare clic con il pulsante destro del mouse sulla *:::no-loc(Models):::* cartella e scegliere **Aggiungi** > **nuovo file** > **generale** > **vuota classe** .</span><span class="sxs-lookup"><span data-stu-id="0b894-220">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class** .</span></span>

* <span data-ttu-id="0b894-221">Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo** .</span><span class="sxs-lookup"><span data-stu-id="0b894-221">Name the class *TodoItem* , and then click **New** .</span></span>

* <span data-ttu-id="0b894-222">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/:::no-loc(Models):::/TodoItem.cs?name=snippet)]

<span data-ttu-id="0b894-223">La proprietà `Id` funziona come chiave univoca in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="0b894-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0b894-224">Le classi di modelli possono essere inserite in qualsiasi punto del progetto, ma la *:::no-loc(Models):::* cartella viene utilizzata per convenzione.</span><span class="sxs-lookup"><span data-stu-id="0b894-224">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="0b894-225">Aggiungere un contesto di database</span><span class="sxs-lookup"><span data-stu-id="0b894-225">Add a database context</span></span>

<span data-ttu-id="0b894-226">Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati.</span><span class="sxs-lookup"><span data-stu-id="0b894-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0b894-227">Questa classe viene creata mediante derivazione dalla classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="0b894-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="0b894-229">Aggiungere pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="0b894-229">Add NuGet packages</span></span>

* <span data-ttu-id="0b894-230">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione** .</span><span class="sxs-lookup"><span data-stu-id="0b894-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution** .</span></span>
* <span data-ttu-id="0b894-231">Selezionare la scheda **Sfoglia** e quindi immettere \* \* Microsoft.</span><span class="sxs-lookup"><span data-stu-id="0b894-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="0b894-232">**EntityFrameworkCore. SqlServer** nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="0b894-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="0b894-233">Selezionare la casella di controllo **Includi versione preliminare** in modo che sia disponibile la versione 5,0 RC.</span><span class="sxs-lookup"><span data-stu-id="0b894-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="0b894-234">Nel riquadro sinistro selezionare **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="0b894-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="0b894-235">Selezionare la casella di controllo **Progetto** nel riquadro di destra e quindi selezionare **Installa** .</span><span class="sxs-lookup"><span data-stu-id="0b894-235">Select the **Project** check box in the right pane and then select **Install** .</span></span>
* <span data-ttu-id="0b894-236">Usare le istruzioni precedenti per aggiungere il pacchetto NuGet **Microsoft. EntityFrameworkCore. InMemory** .</span><span class="sxs-lookup"><span data-stu-id="0b894-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="0b894-237">![Gestione pacchetti NuGet](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="0b894-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="0b894-238">Aggiungere il contesto del database TodoContext</span><span class="sxs-lookup"><span data-stu-id="0b894-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="0b894-239">Fare clic con il pulsante destro del mouse sulla *:::no-loc(Models):::* cartella e scegliere **Aggiungi**  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="0b894-239">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="0b894-240">Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="0b894-240">Name the class *TodoContext* and click **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0b894-241">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0b894-242">Aggiungere una `TodoContext` classe alla *:::no-loc(Models):::* cartella.</span><span class="sxs-lookup"><span data-stu-id="0b894-242">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="0b894-243">Immettere il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="0b894-244">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="0b894-244">Register the database context</span></span>

<span data-ttu-id="0b894-245">In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0b894-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0b894-246">Il contenitore rende disponibile il servizio ai controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="0b894-247">Aggiornare *Startup.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="0b894-248">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="0b894-248">The preceding code:</span></span>

* <span data-ttu-id="0b894-249">Rimuove le chiamate di spavalderia.</span><span class="sxs-lookup"><span data-stu-id="0b894-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="0b894-250">Rimuove le dichiarazioni `using` inutilizzate.</span><span class="sxs-lookup"><span data-stu-id="0b894-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0b894-251">Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="0b894-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0b894-252">Specifica che il contesto del database userà un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="0b894-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="0b894-253">Eseguire lo scaffolding di un controller</span><span class="sxs-lookup"><span data-stu-id="0b894-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b894-255">Fare clic con il pulsante destro del mouse sulla cartella *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="0b894-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0b894-256">Selezionare **Aggiungi** > **Nuovo elemento di scaffolding** .</span><span class="sxs-lookup"><span data-stu-id="0b894-256">Select **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="0b894-257">Selezionare **Controller API con azioni, che usa Entity Framework** e quindi selezionare **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="0b894-257">Select **API Controller with actions, using Entity Framework** , and then select **Add** .</span></span>
* <span data-ttu-id="0b894-258">Nella finestra di dialogo **Add API Controller with actions, using Entity Framework** (Aggiungi controller API con azioni, che usa Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="0b894-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="0b894-259">Selezionare **TodoItem (TodoApi. :::no-loc(Models)::: )** nella **classe del modello** .</span><span class="sxs-lookup"><span data-stu-id="0b894-259">Select **TodoItem (TodoApi.:::no-loc(Models):::)** in the **Model class** .</span></span>
  * <span data-ttu-id="0b894-260">Selezionare **TodoContext (TodoApi. :::no-loc(Models)::: )** nella **classe del contesto dati** .</span><span class="sxs-lookup"><span data-stu-id="0b894-260">Select **TodoContext (TodoApi.:::no-loc(Models):::)** in the **Data context class** .</span></span>
  * <span data-ttu-id="0b894-261">Selezionare **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="0b894-261">Select **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0b894-262">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0b894-263">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b894-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="0b894-264">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="0b894-264">The preceding commands:</span></span>

* <span data-ttu-id="0b894-265">Aggiungere i pacchetti NuGet necessari per lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="0b894-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="0b894-266">Installa il motore di scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="0b894-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="0b894-267">Esegue lo scaffolding di `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="0b894-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="0b894-268">Il codice generato:</span><span class="sxs-lookup"><span data-stu-id="0b894-268">The generated code:</span></span>

* <span data-ttu-id="0b894-269">Contrassegna la classe con l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="0b894-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="0b894-270">L'attributo indica che il controller risponde alle richieste di API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0b894-271">Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="0b894-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0b894-272">Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0b894-273">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="0b894-274">I modelli di ASP.NET Core per:</span><span class="sxs-lookup"><span data-stu-id="0b894-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="0b894-275">I controller con le visualizzazioni includono `[action]` nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="0b894-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="0b894-276">I controller API non includono `[action]` nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="0b894-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="0b894-277">Quando il `[action]` token non è incluso nel modello di route, il nome dell' [azione](xref:mvc/controllers/routing#action) viene escluso dalla route.</span><span class="sxs-lookup"><span data-stu-id="0b894-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="0b894-278">Ovvero, il nome del metodo associato all'azione non viene usato nella route corrispondente.</span><span class="sxs-lookup"><span data-stu-id="0b894-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="0b894-279">Aggiornare il metodo di creazione PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="0b894-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="0b894-280">Sostituire l'istruzione return in `PostTodoItem` per usare l'operatore [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="0b894-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="0b894-281">Il codice precedente è un metodo HTTP POST, come indicato dall' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="0b894-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="0b894-282">Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b894-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0b894-283">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0b894-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0b894-284">Il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="0b894-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="0b894-285">Restituisce un [codice di stato HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="0b894-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="0b894-286">HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="0b894-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0b894-287">Aggiunge un'intestazione [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) alla risposta.</span><span class="sxs-lookup"><span data-stu-id="0b894-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="0b894-288">L'intestazione `Location` specifica l'[URI](https://developer.mozilla.org/docs/Glossary/URI) dell'elemento attività appena creato.</span><span class="sxs-lookup"><span data-stu-id="0b894-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="0b894-289">Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="0b894-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0b894-290">Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`.</span><span class="sxs-lookup"><span data-stu-id="0b894-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0b894-291">La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="0b894-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="0b894-292">Installare Postman</span><span class="sxs-lookup"><span data-stu-id="0b894-292">Install Postman</span></span>

<span data-ttu-id="0b894-293">Questa esercitazione usa Postman per testare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0b894-294">Installa il [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="0b894-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="0b894-295">Avviare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-295">Start the web app.</span></span>
* <span data-ttu-id="0b894-296">Avviare Postman.</span><span class="sxs-lookup"><span data-stu-id="0b894-296">Start Postman.</span></span>
* <span data-ttu-id="0b894-297">Disattivare **SSL certificate verification** (Verifica certificato SSL)</span><span class="sxs-lookup"><span data-stu-id="0b894-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="0b894-298">Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).</span><span class="sxs-lookup"><span data-stu-id="0b894-298">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification** .</span></span>
    > [!WARNING]
    > <span data-ttu-id="0b894-299">Riattivare la verifica dei certificati SSL al termine del test del controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="0b894-300">Testare PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="0b894-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="0b894-301">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="0b894-301">Create a new request.</span></span>
* <span data-ttu-id="0b894-302">Impostare il metodo HTTP su `POST`.</span><span class="sxs-lookup"><span data-stu-id="0b894-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0b894-303">Impostare l'URI su `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="0b894-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0b894-304">Ad esempio, `https://localhost:5001/api/TodoItems`</span><span class="sxs-lookup"><span data-stu-id="0b894-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0b894-305">Selezionare la scheda **Corpo** .</span><span class="sxs-lookup"><span data-stu-id="0b894-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="0b894-306">Selezionare il pulsante di opzione **raw** (non elaborato).</span><span class="sxs-lookup"><span data-stu-id="0b894-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0b894-307">Impostare il tipo su **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="0b894-307">Set the type to **JSON (application/json)** .</span></span>
* <span data-ttu-id="0b894-308">Nel corpo della richiesta immettere codice JSON per un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="0b894-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0b894-309">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="0b894-309">Select **Send** .</span></span>

  ![Postman con richiesta di creazione](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="0b894-311">Testare l'URI dell'intestazione della posizione</span><span class="sxs-lookup"><span data-stu-id="0b894-311">Test the location header URI</span></span>

<span data-ttu-id="0b894-312">L'URI dell'intestazione Location può essere testato nel browser.</span><span class="sxs-lookup"><span data-stu-id="0b894-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="0b894-313">Copiare e incollare l'URI dell'intestazione location nel browser.</span><span class="sxs-lookup"><span data-stu-id="0b894-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="0b894-314">Per eseguire il test in un post:</span><span class="sxs-lookup"><span data-stu-id="0b894-314">To test in Postman:</span></span>

* <span data-ttu-id="0b894-315">Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).</span><span class="sxs-lookup"><span data-stu-id="0b894-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0b894-316">Copiare il valore dell'intestazione **Location** (Posizione):</span><span class="sxs-lookup"><span data-stu-id="0b894-316">Copy the **Location** header value:</span></span>

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="0b894-318">Impostare il metodo HTTP su `GET`.</span><span class="sxs-lookup"><span data-stu-id="0b894-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="0b894-319">Impostare l'URI su `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="0b894-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="0b894-320">Ad esempio, `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="0b894-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="0b894-321">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="0b894-321">Select **Send** .</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="0b894-322">Esaminare i metodi GET</span><span class="sxs-lookup"><span data-stu-id="0b894-322">Examine the GET methods</span></span>

<span data-ttu-id="0b894-323">Vengono implementati due endpoint GET:</span><span class="sxs-lookup"><span data-stu-id="0b894-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="0b894-324">Testare l'app chiamando i due endpoint da un browser o da Postman.</span><span class="sxs-lookup"><span data-stu-id="0b894-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="0b894-325">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0b894-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="0b894-326">Una risposta simile alla seguente viene generata dalla chiamata a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="0b894-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="0b894-327">Testare Get con Postman</span><span class="sxs-lookup"><span data-stu-id="0b894-327">Test Get with Postman</span></span>

* <span data-ttu-id="0b894-328">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="0b894-328">Create a new request.</span></span>
* <span data-ttu-id="0b894-329">Impostare il metodo HTTP su **GET** .</span><span class="sxs-lookup"><span data-stu-id="0b894-329">Set the HTTP method to **GET** .</span></span>
* <span data-ttu-id="0b894-330">Impostare l'URI della richiesta su `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="0b894-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0b894-331">Ad esempio, `https://localhost:5001/api/TodoItems`</span><span class="sxs-lookup"><span data-stu-id="0b894-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0b894-332">Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.</span><span class="sxs-lookup"><span data-stu-id="0b894-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0b894-333">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="0b894-333">Select **Send** .</span></span>

<span data-ttu-id="0b894-334">Questa app usa un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="0b894-334">This app uses an in-memory database.</span></span> <span data-ttu-id="0b894-335">Se l'app viene arrestata e avviata, la richiesta GET precedente non restituirà alcun dato.</span><span class="sxs-lookup"><span data-stu-id="0b894-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="0b894-336">Se non vengono restituiti dati, eseguire [POST](#post) per pubblicare i dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="0b894-337">Routing e percorsi di URL</span><span class="sxs-lookup"><span data-stu-id="0b894-337">Routing and URL paths</span></span>

<span data-ttu-id="0b894-338">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attributo indica un metodo che risponde a una richiesta HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="0b894-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0b894-339">Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0b894-340">Iniziare con la stringa di modello nell'attributo `Route` del controller:</span><span class="sxs-lookup"><span data-stu-id="0b894-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="0b894-341">Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller".</span><span class="sxs-lookup"><span data-stu-id="0b894-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0b894-342">In questo esempio il nome della classe controller è **TodoItems** Controller, quindi il nome del controller è "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="0b894-342">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="0b894-343">Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="0b894-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0b894-344">Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso.</span><span class="sxs-lookup"><span data-stu-id="0b894-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0b894-345">In questo esempio non si usa un modello.</span><span class="sxs-lookup"><span data-stu-id="0b894-345">This sample doesn't use a template.</span></span> <span data-ttu-id="0b894-346">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0b894-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0b894-347">Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="0b894-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0b894-348">Quando `GetTodoItem` viene richiamato, il valore di `"{id}"` nell'URL viene fornito al metodo nel `id` parametro.</span><span class="sxs-lookup"><span data-stu-id="0b894-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="0b894-349">Valori restituiti</span><span class="sxs-lookup"><span data-stu-id="0b894-349">Return values</span></span>

<span data-ttu-id="0b894-350">Il tipo restituito dei `GetTodoItems` metodi e `GetTodoItem` è [ActionResult. \<T> ](xref:web-api/action-return-types#actionresultt-type)</span><span class="sxs-lookup"><span data-stu-id="0b894-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0b894-351">ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="0b894-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0b894-352">Il codice di risposta per questo tipo restituito è [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), supponendo che non esistano eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="0b894-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0b894-353">Le eccezioni non gestite vengono convertite in errori 5xx.</span><span class="sxs-lookup"><span data-stu-id="0b894-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0b894-354">I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b894-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0b894-355">Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:</span><span class="sxs-lookup"><span data-stu-id="0b894-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0b894-356">Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un codice di errore di [stato 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="0b894-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="0b894-357">In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON.</span><span class="sxs-lookup"><span data-stu-id="0b894-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0b894-358">La restituzione di `item` risulta in una risposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="0b894-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="0b894-359">Metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="0b894-359">The PutTodoItem method</span></span>

<span data-ttu-id="0b894-360">Esaminare il metodo `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="0b894-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="0b894-361">`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="0b894-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0b894-362">La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0b894-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0b894-363">In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="0b894-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0b894-364">Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="0b894-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0b894-365">Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.</span><span class="sxs-lookup"><span data-stu-id="0b894-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="0b894-366">Testare il metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="0b894-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="0b894-367">Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="0b894-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0b894-368">Deve esistere un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="0b894-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0b894-369">Chiamare GET per assicurarsi che sia presente un elemento nel database prima di effettuare una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="0b894-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0b894-370">Aggiornare l'elemento to-do con ID = 1 e impostarne il nome su `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="0b894-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0b894-371">L'immagine seguente visualizza l'aggiornamento di Postman:</span><span class="sxs-lookup"><span data-stu-id="0b894-371">The following image shows the Postman update:</span></span>

![Console Postman con risposta 204 (No Content)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="0b894-373">Metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="0b894-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="0b894-374">Esaminare il metodo `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="0b894-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="0b894-375">Testare il metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="0b894-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="0b894-376">Usare Postman per eliminare un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="0b894-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0b894-377">Impostare il metodo su `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="0b894-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0b894-378">Impostare l'URI dell'oggetto da eliminare (ad esempio `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="0b894-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="0b894-379">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="0b894-379">Select **Send** .</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="0b894-380">Impedisci overposting</span><span class="sxs-lookup"><span data-stu-id="0b894-380">Prevent over-posting</span></span>

<span data-ttu-id="0b894-381">Attualmente l'app di esempio espone l'intero `TodoItem` oggetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="0b894-382">Le app di produzione limitano in genere i dati di input e restituiti usando un subset del modello.</span><span class="sxs-lookup"><span data-stu-id="0b894-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="0b894-383">Esistono diversi motivi alla base di questo e la sicurezza è una delle principali.</span><span class="sxs-lookup"><span data-stu-id="0b894-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="0b894-384">Il subset di un modello è in genere indicato come oggetto Trasferimento dati (DTO), modello di input o modello di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="0b894-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="0b894-385">**Dto** viene usato in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="0b894-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="0b894-386">Un DTO può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="0b894-386">A DTO may be used to:</span></span>

* <span data-ttu-id="0b894-387">Impedisci l'overposting.</span><span class="sxs-lookup"><span data-stu-id="0b894-387">Prevent over-posting.</span></span>
* <span data-ttu-id="0b894-388">Nascondere le proprietà che i client non dovrebbero visualizzare.</span><span class="sxs-lookup"><span data-stu-id="0b894-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="0b894-389">Omettere alcune proprietà per ridurre le dimensioni del payload.</span><span class="sxs-lookup"><span data-stu-id="0b894-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="0b894-390">Rendere flat gli oggetti grafici che contengono oggetti annidati.</span><span class="sxs-lookup"><span data-stu-id="0b894-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="0b894-391">I grafici a oggetti flat possono essere più pratici per i client.</span><span class="sxs-lookup"><span data-stu-id="0b894-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="0b894-392">Per illustrare l'approccio DTO, aggiornare la `TodoItem` classe in modo da includere un campo Secret:</span><span class="sxs-lookup"><span data-stu-id="0b894-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/:::no-loc(Models):::/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="0b894-393">Il campo segreto deve essere nascosto da questa app, ma un'app amministrativa può scegliere di esporla.</span><span class="sxs-lookup"><span data-stu-id="0b894-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="0b894-394">Verificare che sia possibile pubblicare e ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="0b894-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="0b894-395">Creare un modello DTO:</span><span class="sxs-lookup"><span data-stu-id="0b894-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/:::no-loc(Models):::/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="0b894-396">Aggiornare `TodoItemsController` da usare `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="0b894-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="0b894-397">Verificare che non sia possibile inserire o ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="0b894-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="0b894-398">Chiamare l'API Web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b894-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="0b894-399">Vedere [esercitazione: chiamare un'API web ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="0b894-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="0b894-400">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="0b894-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0b894-401">Creare un progetto di API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-401">Create a web API project.</span></span>
> * <span data-ttu-id="0b894-402">Aggiungere una classe modello e un contesto di database.</span><span class="sxs-lookup"><span data-stu-id="0b894-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0b894-403">Eseguire lo scaffolding di un controller con i metodi CRUD.</span><span class="sxs-lookup"><span data-stu-id="0b894-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="0b894-404">Configurare il routing, i percorsi URL e i valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="0b894-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="0b894-405">Chiamare l'API Web con Postman.</span><span class="sxs-lookup"><span data-stu-id="0b894-405">Call the web API with Postman.</span></span>

<span data-ttu-id="0b894-406">Al termine si avrà un'API Web che può gestire gli elementi di tipo "attività" archiviati in un database.</span><span class="sxs-lookup"><span data-stu-id="0b894-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="0b894-407">Panoramica</span><span class="sxs-lookup"><span data-stu-id="0b894-407">Overview</span></span>

<span data-ttu-id="0b894-408">Questa esercitazione consente di creare l'API seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0b894-409">API</span><span class="sxs-lookup"><span data-stu-id="0b894-409">API</span></span> | <span data-ttu-id="0b894-410">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0b894-410">Description</span></span> | <span data-ttu-id="0b894-411">Corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="0b894-411">Request body</span></span> | <span data-ttu-id="0b894-412">Corpo della risposta</span><span class="sxs-lookup"><span data-stu-id="0b894-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="0b894-413">Ottiene tutti gli elementi attività</span><span class="sxs-lookup"><span data-stu-id="0b894-413">Get all to-do items</span></span> | <span data-ttu-id="0b894-414">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-414">None</span></span> | <span data-ttu-id="0b894-415">Matrice di elementi attività</span><span class="sxs-lookup"><span data-stu-id="0b894-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="0b894-416">Ottiene un elemento in base all'ID</span><span class="sxs-lookup"><span data-stu-id="0b894-416">Get an item by ID</span></span> | <span data-ttu-id="0b894-417">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-417">None</span></span> | <span data-ttu-id="0b894-418">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="0b894-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="0b894-419">Aggiunge un nuovo elemento</span><span class="sxs-lookup"><span data-stu-id="0b894-419">Add a new item</span></span> | <span data-ttu-id="0b894-420">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="0b894-420">To-do item</span></span> | <span data-ttu-id="0b894-421">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="0b894-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="0b894-422">Aggiorna un elemento esistente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0b894-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0b894-423">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="0b894-423">To-do item</span></span> | <span data-ttu-id="0b894-424">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-424">None</span></span> |
|<span data-ttu-id="0b894-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0b894-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="0b894-426">Elimina un elemento &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="0b894-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0b894-427">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-427">None</span></span> | <span data-ttu-id="0b894-428">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-428">None</span></span>|

<span data-ttu-id="0b894-429">Il diagramma seguente visualizza la struttura dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-429">The following diagram shows the design of the app.</span></span>

![Il client è rappresentato da una casella a sinistra.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="0b894-435">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="0b894-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b894-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b894-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b894-438">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="0b894-439">Creare un progetto Web</span><span class="sxs-lookup"><span data-stu-id="0b894-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b894-441">Scegliere **nuovo** progetto dal menu **file** > **Project** .</span><span class="sxs-lookup"><span data-stu-id="0b894-441">From the **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="0b894-442">Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="0b894-442">Select the **ASP.NET Core Web Application** template and click **Next** .</span></span>
* <span data-ttu-id="0b894-443">Assegnare al progetto il nome *TodoApi* e fare clic su **Crea** .</span><span class="sxs-lookup"><span data-stu-id="0b894-443">Name the project *TodoApi* and click **Create** .</span></span>
* <span data-ttu-id="0b894-444">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.net Core** e **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="0b894-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="0b894-445">Selezionare il modello **API** e fare clic su **Crea** .</span><span class="sxs-lookup"><span data-stu-id="0b894-445">Select the **API** template and click **Create** .</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b894-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b894-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0b894-448">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0b894-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0b894-449">Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0b894-450">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b894-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="0b894-451">Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="0b894-451">When a dialog box asks if you want to add required assets to the project, select **Yes** .</span></span>

  <span data-ttu-id="0b894-452">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="0b894-452">The preceding commands:</span></span>

  * <span data-ttu-id="0b894-453">Crea un nuovo progetto API Web e lo apre in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0b894-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="0b894-454">Aggiunge i pacchetti NuGet necessari nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="0b894-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b894-455">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0b894-456">Selezionare **File** > **Nuova soluzione** .</span><span class="sxs-lookup"><span data-stu-id="0b894-456">Select **File** > **New Solution** .</span></span>

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0b894-458">In Visual Studio per Mac precedente alla versione 8,6 Selezionare API **app .NET Core**  >  **App**  >  **API**  >  **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="0b894-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next** .</span></span> <span data-ttu-id="0b894-459">Nella versione 8,6 o successive selezionare API **app Web e console**  >  **App**  >  **API**  >  **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="0b894-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![Selezione modello API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="0b894-461">Nella finestra di dialogo **Configura nuova ASP.NET Core API Web** selezionare il **Framework di destinazione** .NET Core 3. x più recente.</span><span class="sxs-lookup"><span data-stu-id="0b894-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework** .</span></span> <span data-ttu-id="0b894-462">Selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="0b894-462">Select **Next** .</span></span>

* <span data-ttu-id="0b894-463">Immettere *TodoApi* in **Nome progetto** , quindi selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="0b894-463">Enter *TodoApi* for the **Project Name** and then select **Create** .</span></span>

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="0b894-465">Aprire un terminale di comando nella cartella di progetto ed eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b894-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="0b894-466">Testare l'API</span><span class="sxs-lookup"><span data-stu-id="0b894-466">Test the API</span></span>

<span data-ttu-id="0b894-467">Il modello di progetto crea un'API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="0b894-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="0b894-468">Chiamare il metodo `Get` da un browser per eseguire il test dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0b894-470">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0b894-471">Visual Studio apre un browser e naviga all'indirizzo `https://localhost:<port>/WeatherForecast`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="0b894-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="0b894-472">Se in una finestra di dialogo viene chiesto se considerare attendibile il certificato IIS Express, selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="0b894-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes** .</span></span> <span data-ttu-id="0b894-473">Nella finestra di dialogo **Avviso di sicurezza** successiva selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="0b894-473">In the **Security Warning** dialog that appears next, select **Yes** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b894-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b894-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0b894-475">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0b894-476">In un browser passare all'URL seguente: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="0b894-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b894-477">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0b894-478">Selezionare **Esegui**  >  **Avvia debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0b894-479">Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="0b894-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0b894-480">Viene restituito un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="0b894-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0b894-481">Accodare `/WeatherForecast` all'URL (impostare l'URL su `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="0b894-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="0b894-482">Viene restituito un codice JSON simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-482">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="0b894-483">Aggiungere una classe del modello</span><span class="sxs-lookup"><span data-stu-id="0b894-483">Add a model class</span></span>

<span data-ttu-id="0b894-484">Un *modello* è un set di classi che rappresentano i dati gestiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0b894-485">Il modello per questa app è una classe `TodoItem` singola.</span><span class="sxs-lookup"><span data-stu-id="0b894-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b894-487">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-487">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="0b894-488">Selezionare **Aggiungi**  >  **nuova cartella** .</span><span class="sxs-lookup"><span data-stu-id="0b894-488">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="0b894-489">Assegnare un nome alla cartella *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="0b894-489">Name the folder *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="0b894-490">Fare clic con il pulsante destro del mouse sulla *:::no-loc(Models):::* cartella e scegliere **Aggiungi**  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="0b894-490">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="0b894-491">Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="0b894-491">Name the class *TodoItem* and select **Add** .</span></span>

* <span data-ttu-id="0b894-492">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b894-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b894-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0b894-494">Aggiungere una cartella denominata *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="0b894-494">Add a folder named *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="0b894-495">Aggiungere una `TodoItem` classe alla *:::no-loc(Models):::* cartella con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-495">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b894-496">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0b894-497">Fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-497">Right-click the project.</span></span> <span data-ttu-id="0b894-498">Selezionare **Aggiungi**  >  **nuova cartella** .</span><span class="sxs-lookup"><span data-stu-id="0b894-498">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="0b894-499">Assegnare un nome alla cartella *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="0b894-499">Name the folder *:::no-loc(Models):::* .</span></span>

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0b894-501">Fare clic con il pulsante destro del mouse sulla *:::no-loc(Models):::* cartella e scegliere **Aggiungi** > **nuovo file** > **generale** > **vuota classe** .</span><span class="sxs-lookup"><span data-stu-id="0b894-501">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class** .</span></span>

* <span data-ttu-id="0b894-502">Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo** .</span><span class="sxs-lookup"><span data-stu-id="0b894-502">Name the class *TodoItem* , and then click **New** .</span></span>

* <span data-ttu-id="0b894-503">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/:::no-loc(Models):::/TodoItem.cs?name=snippet)]

<span data-ttu-id="0b894-504">La proprietà `Id` funziona come chiave univoca in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="0b894-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0b894-505">Le classi di modelli possono essere inserite in qualsiasi punto del progetto, ma la *:::no-loc(Models):::* cartella viene utilizzata per convenzione.</span><span class="sxs-lookup"><span data-stu-id="0b894-505">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="0b894-506">Aggiungere un contesto di database</span><span class="sxs-lookup"><span data-stu-id="0b894-506">Add a database context</span></span>

<span data-ttu-id="0b894-507">Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati.</span><span class="sxs-lookup"><span data-stu-id="0b894-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0b894-508">Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="0b894-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="0b894-510">Aggiungere pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="0b894-510">Add NuGet packages</span></span>

* <span data-ttu-id="0b894-511">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione** .</span><span class="sxs-lookup"><span data-stu-id="0b894-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution** .</span></span>
* <span data-ttu-id="0b894-512">Selezionare la scheda **Esplora** e quindi immettere **Microsoft.EntityFrameworkCore.SqlServer** nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="0b894-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="0b894-513">Nel riquadro sinistro selezionare **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="0b894-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="0b894-514">Selezionare la casella di controllo **Progetto** nel riquadro di destra e quindi selezionare **Installa** .</span><span class="sxs-lookup"><span data-stu-id="0b894-514">Select the **Project** check box in the right pane and then select **Install** .</span></span>
* <span data-ttu-id="0b894-515">Usare le istruzioni precedenti per aggiungere il pacchetto NuGet **Microsoft. EntityFrameworkCore. InMemory** .</span><span class="sxs-lookup"><span data-stu-id="0b894-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Gestione pacchetti NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="0b894-517">Aggiungere il contesto del database TodoContext</span><span class="sxs-lookup"><span data-stu-id="0b894-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="0b894-518">Fare clic con il pulsante destro del mouse sulla *:::no-loc(Models):::* cartella e scegliere **Aggiungi**  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="0b894-518">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="0b894-519">Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="0b894-519">Name the class *TodoContext* and click **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0b894-520">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0b894-521">Aggiungere una `TodoContext` classe alla *:::no-loc(Models):::* cartella.</span><span class="sxs-lookup"><span data-stu-id="0b894-521">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="0b894-522">Immettere il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="0b894-523">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="0b894-523">Register the database context</span></span>

<span data-ttu-id="0b894-524">In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0b894-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0b894-525">Il contenitore rende disponibile il servizio ai controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="0b894-526">Aggiornare *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="0b894-527">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="0b894-527">The preceding code:</span></span>

* <span data-ttu-id="0b894-528">Rimuove le dichiarazioni `using` inutilizzate.</span><span class="sxs-lookup"><span data-stu-id="0b894-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0b894-529">Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="0b894-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0b894-530">Specifica che il contesto del database userà un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="0b894-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="0b894-531">Eseguire lo scaffolding di un controller</span><span class="sxs-lookup"><span data-stu-id="0b894-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b894-533">Fare clic con il pulsante destro del mouse sulla cartella *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="0b894-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0b894-534">Selezionare **Aggiungi** > **Nuovo elemento di scaffolding** .</span><span class="sxs-lookup"><span data-stu-id="0b894-534">Select **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="0b894-535">Selezionare **Controller API con azioni, che usa Entity Framework** e quindi selezionare **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="0b894-535">Select **API Controller with actions, using Entity Framework** , and then select **Add** .</span></span>
* <span data-ttu-id="0b894-536">Nella finestra di dialogo **Add API Controller with actions, using Entity Framework** (Aggiungi controller API con azioni, che usa Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="0b894-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="0b894-537">Selezionare **TodoItem (TodoApi. :::no-loc(Models)::: )** nella **classe del modello** .</span><span class="sxs-lookup"><span data-stu-id="0b894-537">Select **TodoItem (TodoApi.:::no-loc(Models):::)** in the **Model class** .</span></span>
  * <span data-ttu-id="0b894-538">Selezionare **TodoContext (TodoApi. :::no-loc(Models)::: )** nella **classe del contesto dati** .</span><span class="sxs-lookup"><span data-stu-id="0b894-538">Select **TodoContext (TodoApi.:::no-loc(Models):::)** in the **Data context class** .</span></span>
  * <span data-ttu-id="0b894-539">Selezionare **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="0b894-539">Select **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0b894-540">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0b894-541">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b894-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="0b894-542">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="0b894-542">The preceding commands:</span></span>

* <span data-ttu-id="0b894-543">Aggiungere i pacchetti NuGet necessari per lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="0b894-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="0b894-544">Installa il motore di scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="0b894-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="0b894-545">Esegue lo scaffolding di `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="0b894-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="0b894-546">Il codice generato:</span><span class="sxs-lookup"><span data-stu-id="0b894-546">The generated code:</span></span>

* <span data-ttu-id="0b894-547">Contrassegna la classe con l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="0b894-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="0b894-548">L'attributo indica che il controller risponde alle richieste di API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0b894-549">Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="0b894-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0b894-550">Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0b894-551">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="0b894-552">I modelli di ASP.NET Core per:</span><span class="sxs-lookup"><span data-stu-id="0b894-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="0b894-553">I controller con le visualizzazioni includono `[action]` nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="0b894-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="0b894-554">I controller API non includono `[action]` nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="0b894-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="0b894-555">Quando il `[action]` token non è incluso nel modello di route, il nome dell' [azione](xref:mvc/controllers/routing#action) viene escluso dalla route.</span><span class="sxs-lookup"><span data-stu-id="0b894-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="0b894-556">Ovvero, il nome del metodo associato all'azione non viene usato nella route corrispondente.</span><span class="sxs-lookup"><span data-stu-id="0b894-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="0b894-557">Esaminare il metodo di creazione PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="0b894-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="0b894-558">Sostituire l'istruzione return in `PostTodoItem` per usare l'operatore [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="0b894-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="0b894-559">Il codice precedente è un metodo HTTP POST, come indicato dall' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="0b894-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="0b894-560">Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b894-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0b894-561">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0b894-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0b894-562">Il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="0b894-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="0b894-563">Restituisce un codice di stato HTTP 201 in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="0b894-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="0b894-564">HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="0b894-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0b894-565">Aggiunge un'intestazione [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) alla risposta.</span><span class="sxs-lookup"><span data-stu-id="0b894-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="0b894-566">L'intestazione `Location` specifica l'[URI](https://developer.mozilla.org/docs/Glossary/URI) dell'elemento attività appena creato.</span><span class="sxs-lookup"><span data-stu-id="0b894-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="0b894-567">Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="0b894-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0b894-568">Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`.</span><span class="sxs-lookup"><span data-stu-id="0b894-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0b894-569">La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="0b894-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="0b894-570">Installare Postman</span><span class="sxs-lookup"><span data-stu-id="0b894-570">Install Postman</span></span>

<span data-ttu-id="0b894-571">Questa esercitazione usa Postman per testare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0b894-572">Installa il [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="0b894-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="0b894-573">Avviare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-573">Start the web app.</span></span>
* <span data-ttu-id="0b894-574">Avviare Postman.</span><span class="sxs-lookup"><span data-stu-id="0b894-574">Start Postman.</span></span>
* <span data-ttu-id="0b894-575">Disattivare **SSL certificate verification** (Verifica certificato SSL)</span><span class="sxs-lookup"><span data-stu-id="0b894-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="0b894-576">Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).</span><span class="sxs-lookup"><span data-stu-id="0b894-576">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification** .</span></span>
    > [!WARNING]
    > <span data-ttu-id="0b894-577">Riattivare la verifica dei certificati SSL al termine del test del controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="0b894-578">Testare PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="0b894-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="0b894-579">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="0b894-579">Create a new request.</span></span>
* <span data-ttu-id="0b894-580">Impostare il metodo HTTP su `POST`.</span><span class="sxs-lookup"><span data-stu-id="0b894-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0b894-581">Impostare l'URI su `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="0b894-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0b894-582">Ad esempio, `https://localhost:5001/api/TodoItems`</span><span class="sxs-lookup"><span data-stu-id="0b894-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0b894-583">Selezionare la scheda **Corpo** .</span><span class="sxs-lookup"><span data-stu-id="0b894-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="0b894-584">Selezionare il pulsante di opzione **raw** (non elaborato).</span><span class="sxs-lookup"><span data-stu-id="0b894-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0b894-585">Impostare il tipo su **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="0b894-585">Set the type to **JSON (application/json)** .</span></span>
* <span data-ttu-id="0b894-586">Nel corpo della richiesta immettere codice JSON per un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="0b894-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0b894-587">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="0b894-587">Select **Send** .</span></span>

  ![Postman con richiesta di creazione](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="0b894-589">Testare l'URI dell'intestazione Location con post</span><span class="sxs-lookup"><span data-stu-id="0b894-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="0b894-590">Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).</span><span class="sxs-lookup"><span data-stu-id="0b894-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0b894-591">Copiare il valore dell'intestazione **Location** (Posizione):</span><span class="sxs-lookup"><span data-stu-id="0b894-591">Copy the **Location** header value:</span></span>

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="0b894-593">Impostare il metodo HTTP su `GET`.</span><span class="sxs-lookup"><span data-stu-id="0b894-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="0b894-594">Impostare l'URI su `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="0b894-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="0b894-595">Ad esempio, `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="0b894-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="0b894-596">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="0b894-596">Select **Send** .</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="0b894-597">Esaminare i metodi GET</span><span class="sxs-lookup"><span data-stu-id="0b894-597">Examine the GET methods</span></span>

<span data-ttu-id="0b894-598">Questi metodi implementano due metodi GET:</span><span class="sxs-lookup"><span data-stu-id="0b894-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="0b894-599">Testare l'app chiamando i due endpoint da un browser o da Postman.</span><span class="sxs-lookup"><span data-stu-id="0b894-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="0b894-600">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0b894-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="0b894-601">Una risposta simile alla seguente viene generata dalla chiamata a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="0b894-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="0b894-602">Testare Get con Postman</span><span class="sxs-lookup"><span data-stu-id="0b894-602">Test Get with Postman</span></span>

* <span data-ttu-id="0b894-603">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="0b894-603">Create a new request.</span></span>
* <span data-ttu-id="0b894-604">Impostare il metodo HTTP su **GET** .</span><span class="sxs-lookup"><span data-stu-id="0b894-604">Set the HTTP method to **GET** .</span></span>
* <span data-ttu-id="0b894-605">Impostare l'URI della richiesta su `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="0b894-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0b894-606">Ad esempio, `https://localhost:5001/api/TodoItems`</span><span class="sxs-lookup"><span data-stu-id="0b894-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0b894-607">Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.</span><span class="sxs-lookup"><span data-stu-id="0b894-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0b894-608">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="0b894-608">Select **Send** .</span></span>

<span data-ttu-id="0b894-609">Questa app usa un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="0b894-609">This app uses an in-memory database.</span></span> <span data-ttu-id="0b894-610">Se l'app viene arrestata e avviata, la richiesta GET precedente non restituirà alcun dato.</span><span class="sxs-lookup"><span data-stu-id="0b894-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="0b894-611">Se non vengono restituiti dati, eseguire [POST](#post) per pubblicare i dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="0b894-612">Routing e percorsi di URL</span><span class="sxs-lookup"><span data-stu-id="0b894-612">Routing and URL paths</span></span>

<span data-ttu-id="0b894-613">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attributo indica un metodo che risponde a una richiesta HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="0b894-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0b894-614">Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0b894-615">Iniziare con la stringa di modello nell'attributo `Route` del controller:</span><span class="sxs-lookup"><span data-stu-id="0b894-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="0b894-616">Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller".</span><span class="sxs-lookup"><span data-stu-id="0b894-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0b894-617">In questo esempio il nome della classe controller è **TodoItems** Controller, quindi il nome del controller è "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="0b894-617">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="0b894-618">Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="0b894-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0b894-619">Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso.</span><span class="sxs-lookup"><span data-stu-id="0b894-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0b894-620">In questo esempio non si usa un modello.</span><span class="sxs-lookup"><span data-stu-id="0b894-620">This sample doesn't use a template.</span></span> <span data-ttu-id="0b894-621">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0b894-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0b894-622">Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="0b894-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0b894-623">Quando `GetTodoItem` viene richiamato, il valore di `"{id}"` nell'URL viene fornito al metodo nel `id` parametro.</span><span class="sxs-lookup"><span data-stu-id="0b894-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="0b894-624">Valori restituiti</span><span class="sxs-lookup"><span data-stu-id="0b894-624">Return values</span></span> 

<span data-ttu-id="0b894-625">Il tipo restituito dei `GetTodoItems` metodi e `GetTodoItem` è [ActionResult. \<T> ](xref:web-api/action-return-types#actionresultt-type)</span><span class="sxs-lookup"><span data-stu-id="0b894-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0b894-626">ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="0b894-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0b894-627">Il codice di risposta per questo tipo restituito è 200, presupponendo che non esistano eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="0b894-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0b894-628">Le eccezioni non gestite vengono convertite in errori 5xx.</span><span class="sxs-lookup"><span data-stu-id="0b894-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0b894-629">I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b894-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0b894-630">Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:</span><span class="sxs-lookup"><span data-stu-id="0b894-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0b894-631">Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> codice di errore 404.</span><span class="sxs-lookup"><span data-stu-id="0b894-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="0b894-632">In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON.</span><span class="sxs-lookup"><span data-stu-id="0b894-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0b894-633">La restituzione di `item` risulta in una risposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="0b894-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="0b894-634">Metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="0b894-634">The PutTodoItem method</span></span>

<span data-ttu-id="0b894-635">Esaminare il metodo `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="0b894-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="0b894-636">`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="0b894-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0b894-637">La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0b894-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0b894-638">In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="0b894-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0b894-639">Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="0b894-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0b894-640">Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.</span><span class="sxs-lookup"><span data-stu-id="0b894-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="0b894-641">Testare il metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="0b894-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="0b894-642">Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="0b894-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0b894-643">Deve esistere un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="0b894-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0b894-644">Chiamare GET per assicurarsi che sia presente un elemento nel database prima di effettuare una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="0b894-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0b894-645">Aggiornare l'attività con ID = 1 e impostarne il nome su "feed Fish":</span><span class="sxs-lookup"><span data-stu-id="0b894-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0b894-646">L'immagine seguente visualizza l'aggiornamento di Postman:</span><span class="sxs-lookup"><span data-stu-id="0b894-646">The following image shows the Postman update:</span></span>

![Console Postman con risposta 204 (No Content)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="0b894-648">Metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="0b894-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="0b894-649">Esaminare il metodo `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="0b894-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="0b894-650">Testare il metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="0b894-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="0b894-651">Usare Postman per eliminare un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="0b894-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0b894-652">Impostare il metodo su `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="0b894-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0b894-653">Impostare l'URI dell'oggetto da eliminare (ad esempio `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="0b894-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="0b894-654">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="0b894-654">Select **Send** .</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="0b894-655">Impedisci overposting</span><span class="sxs-lookup"><span data-stu-id="0b894-655">Prevent over-posting</span></span>

<span data-ttu-id="0b894-656">Attualmente l'app di esempio espone l'intero `TodoItem` oggetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="0b894-657">Le app di produzione limitano in genere i dati di input e restituiti usando un subset del modello.</span><span class="sxs-lookup"><span data-stu-id="0b894-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="0b894-658">Esistono diversi motivi alla base di questo e la sicurezza è una delle principali.</span><span class="sxs-lookup"><span data-stu-id="0b894-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="0b894-659">Il subset di un modello è in genere indicato come oggetto Trasferimento dati (DTO), modello di input o modello di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="0b894-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="0b894-660">**Dto** viene usato in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="0b894-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="0b894-661">Un DTO può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="0b894-661">A DTO may be used to:</span></span>

* <span data-ttu-id="0b894-662">Impedisci l'overposting.</span><span class="sxs-lookup"><span data-stu-id="0b894-662">Prevent over-posting.</span></span>
* <span data-ttu-id="0b894-663">Nascondere le proprietà che i client non dovrebbero visualizzare.</span><span class="sxs-lookup"><span data-stu-id="0b894-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="0b894-664">Omettere alcune proprietà per ridurre le dimensioni del payload.</span><span class="sxs-lookup"><span data-stu-id="0b894-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="0b894-665">Rendere flat gli oggetti grafici che contengono oggetti annidati.</span><span class="sxs-lookup"><span data-stu-id="0b894-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="0b894-666">I grafici a oggetti flat possono essere più pratici per i client.</span><span class="sxs-lookup"><span data-stu-id="0b894-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="0b894-667">Per illustrare l'approccio DTO, aggiornare la `TodoItem` classe in modo da includere un campo Secret:</span><span class="sxs-lookup"><span data-stu-id="0b894-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/:::no-loc(Models):::/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="0b894-668">Il campo segreto deve essere nascosto da questa app, ma un'app amministrativa può scegliere di esporla.</span><span class="sxs-lookup"><span data-stu-id="0b894-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="0b894-669">Verificare che sia possibile pubblicare e ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="0b894-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="0b894-670">Creare un modello DTO:</span><span class="sxs-lookup"><span data-stu-id="0b894-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/:::no-loc(Models):::/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="0b894-671">Aggiornare `TodoItemsController` da usare `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="0b894-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="0b894-672">Verificare che non sia possibile inserire o ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="0b894-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="0b894-673">Chiamare l'API Web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b894-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="0b894-674">Vedere [esercitazione: chiamare un'API web ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="0b894-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0b894-675">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="0b894-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0b894-676">Creare un progetto di API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-676">Create a web API project.</span></span>
> * <span data-ttu-id="0b894-677">Aggiungere una classe modello e un contesto di database.</span><span class="sxs-lookup"><span data-stu-id="0b894-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0b894-678">Aggiungere un controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-678">Add a controller.</span></span>
> * <span data-ttu-id="0b894-679">Aggiungere metodi CRUD.</span><span class="sxs-lookup"><span data-stu-id="0b894-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="0b894-680">Configurare routing e percorsi URL.</span><span class="sxs-lookup"><span data-stu-id="0b894-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="0b894-681">Specificare valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="0b894-681">Specify return values.</span></span>
> * <span data-ttu-id="0b894-682">Chiamare l'API Web con Postman.</span><span class="sxs-lookup"><span data-stu-id="0b894-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="0b894-683">Chiamare l'API Web con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0b894-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="0b894-684">Al termine si dispone di un'API web che può gestire gli elementi di tipo "attività" archiviati in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="0b894-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="0b894-685">Panoramica 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-685">Overview 2.1</span></span>

<span data-ttu-id="0b894-686">Questa esercitazione consente di creare l'API seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0b894-687">API</span><span class="sxs-lookup"><span data-stu-id="0b894-687">API</span></span> | <span data-ttu-id="0b894-688">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0b894-688">Description</span></span> | <span data-ttu-id="0b894-689">Corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="0b894-689">Request body</span></span> | <span data-ttu-id="0b894-690">Corpo della risposta</span><span class="sxs-lookup"><span data-stu-id="0b894-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="0b894-691">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0b894-691">GET /api/TodoItems</span></span> | <span data-ttu-id="0b894-692">Ottiene tutti gli elementi attività</span><span class="sxs-lookup"><span data-stu-id="0b894-692">Get all to-do items</span></span> | <span data-ttu-id="0b894-693">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-693">None</span></span> | <span data-ttu-id="0b894-694">Matrice di elementi attività</span><span class="sxs-lookup"><span data-stu-id="0b894-694">Array of to-do items</span></span>|
|<span data-ttu-id="0b894-695">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="0b894-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="0b894-696">Ottiene un elemento in base all'ID</span><span class="sxs-lookup"><span data-stu-id="0b894-696">Get an item by ID</span></span> | <span data-ttu-id="0b894-697">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-697">None</span></span> | <span data-ttu-id="0b894-698">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="0b894-698">To-do item</span></span>|
|<span data-ttu-id="0b894-699">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0b894-699">POST /api/TodoItems</span></span> | <span data-ttu-id="0b894-700">Aggiunge un nuovo elemento</span><span class="sxs-lookup"><span data-stu-id="0b894-700">Add a new item</span></span> | <span data-ttu-id="0b894-701">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="0b894-701">To-do item</span></span> | <span data-ttu-id="0b894-702">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="0b894-702">To-do item</span></span> |
|<span data-ttu-id="0b894-703">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="0b894-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="0b894-704">Aggiorna un elemento esistente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0b894-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0b894-705">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="0b894-705">To-do item</span></span> | <span data-ttu-id="0b894-706">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-706">None</span></span> |
|<span data-ttu-id="0b894-707">Elimina/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="0b894-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="0b894-708">Elimina un elemento &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="0b894-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0b894-709">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-709">None</span></span> | <span data-ttu-id="0b894-710">nessuno</span><span class="sxs-lookup"><span data-stu-id="0b894-710">None</span></span>|

<span data-ttu-id="0b894-711">Il diagramma seguente visualizza la struttura dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-711">The following diagram shows the design of the app.</span></span>

![Il client è rappresentato da una casella a sinistra.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="0b894-717">Prerequisiti 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b894-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b894-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b894-720">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="0b894-721">Creare un progetto Web 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b894-723">Scegliere **nuovo** progetto dal menu **file** > **Project** .</span><span class="sxs-lookup"><span data-stu-id="0b894-723">From the **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="0b894-724">Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="0b894-724">Select the **ASP.NET Core Web Application** template and click **Next** .</span></span>
* <span data-ttu-id="0b894-725">Assegnare al progetto il nome *TodoApi* e fare clic su **Crea** .</span><span class="sxs-lookup"><span data-stu-id="0b894-725">Name the project *TodoApi* and click **Create** .</span></span>
* <span data-ttu-id="0b894-726">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.NET Core** e **ASP.NET Core 2.2** .</span><span class="sxs-lookup"><span data-stu-id="0b894-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="0b894-727">Selezionare il modello **API** e fare clic su **Crea** .</span><span class="sxs-lookup"><span data-stu-id="0b894-727">Select the **API** template and click **Create** .</span></span> <span data-ttu-id="0b894-728">**Non** selezionare **Abilita supporto Docker** .</span><span class="sxs-lookup"><span data-stu-id="0b894-728">**Don't** select **Enable Docker Support** .</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b894-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b894-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0b894-731">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0b894-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0b894-732">Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0b894-733">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b894-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="0b894-734">Questi comandi creano un nuovo progetto API Web e aprono una nuova istanza di Visual Studio Code nella nuova cartella di progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="0b894-735">Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="0b894-735">When a dialog box asks if you want to add required assets to the project, select **Yes** .</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b894-736">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0b894-737">Selezionare **File** > **Nuova soluzione** .</span><span class="sxs-lookup"><span data-stu-id="0b894-737">Select **File** > **New Solution** .</span></span>

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0b894-739">In Visual Studio per Mac precedente alla versione 8,6 Selezionare API **app .NET Core**  >  **App**  >  **API**  >  **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="0b894-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next** .</span></span> <span data-ttu-id="0b894-740">Nella versione 8,6 o successive selezionare API **app Web e console**  >  **App**  >  **API**  >  **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="0b894-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>
  
* <span data-ttu-id="0b894-741">Nella finestra di dialogo **Configura nuova ASP.NET Core API Web** selezionare il **Framework di destinazione** .NET Core 2. x più recente.</span><span class="sxs-lookup"><span data-stu-id="0b894-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework** .</span></span> <span data-ttu-id="0b894-742">Selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="0b894-742">Select **Next** .</span></span>

* <span data-ttu-id="0b894-743">Immettere *TodoApi* in **Nome progetto** , quindi selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="0b894-743">Enter *TodoApi* for the **Project Name** and then select **Create** .</span></span>

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="0b894-745">Testare l'API 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-745">Test the API 2.1</span></span>

<span data-ttu-id="0b894-746">Il modello di progetto crea un'API `values`.</span><span class="sxs-lookup"><span data-stu-id="0b894-746">The project template creates a `values` API.</span></span> <span data-ttu-id="0b894-747">Chiamare il metodo `Get` da un browser per eseguire il test dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0b894-749">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0b894-750">Visual Studio apre un browser e naviga all'indirizzo `https://localhost:<port>/api/values`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="0b894-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="0b894-751">Se in una finestra di dialogo viene chiesto se considerare attendibile il certificato IIS Express, selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="0b894-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes** .</span></span> <span data-ttu-id="0b894-752">Nella finestra di dialogo **Avviso di sicurezza** successiva selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="0b894-752">In the **Security Warning** dialog that appears next, select **Yes** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b894-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b894-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0b894-754">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0b894-755">In un browser passare all'URL seguente: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="0b894-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b894-756">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0b894-757">Selezionare **Esegui**  >  **Avvia debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0b894-758">Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="0b894-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0b894-759">Viene restituito un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="0b894-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0b894-760">Accodare `/api/values` all'URL (impostare l'URL su `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="0b894-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="0b894-761">Viene restituito il codice JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="0b894-762">Aggiungere una classe modello 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-762">Add a model class 2.1</span></span>

<span data-ttu-id="0b894-763">Un *modello* è un set di classi che rappresentano i dati gestiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="0b894-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0b894-764">Il modello per questa app è una classe `TodoItem` singola.</span><span class="sxs-lookup"><span data-stu-id="0b894-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b894-766">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-766">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="0b894-767">Selezionare **Aggiungi**  >  **nuova cartella** .</span><span class="sxs-lookup"><span data-stu-id="0b894-767">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="0b894-768">Assegnare un nome alla cartella *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="0b894-768">Name the folder *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="0b894-769">Fare clic con il pulsante destro del mouse sulla *:::no-loc(Models):::* cartella e scegliere **Aggiungi**  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="0b894-769">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="0b894-770">Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="0b894-770">Name the class *TodoItem* and select **Add** .</span></span>

* <span data-ttu-id="0b894-771">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0b894-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b894-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0b894-773">Aggiungere una cartella denominata *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="0b894-773">Add a folder named *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="0b894-774">Aggiungere una `TodoItem` classe alla *:::no-loc(Models):::* cartella con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-774">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0b894-775">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0b894-776">Fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-776">Right-click the project.</span></span> <span data-ttu-id="0b894-777">Selezionare **Aggiungi**  >  **nuova cartella** .</span><span class="sxs-lookup"><span data-stu-id="0b894-777">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="0b894-778">Assegnare un nome alla cartella *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="0b894-778">Name the folder *:::no-loc(Models):::* .</span></span>

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0b894-780">Fare clic con il pulsante destro del mouse sulla *:::no-loc(Models):::* cartella e scegliere **Aggiungi** > **nuovo file** > **generale** > **vuota classe** .</span><span class="sxs-lookup"><span data-stu-id="0b894-780">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class** .</span></span>

* <span data-ttu-id="0b894-781">Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo** .</span><span class="sxs-lookup"><span data-stu-id="0b894-781">Name the class *TodoItem* , and then click **New** .</span></span>

* <span data-ttu-id="0b894-782">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/:::no-loc(Models):::/TodoItem.cs)]

<span data-ttu-id="0b894-783">La proprietà `Id` funziona come chiave univoca in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="0b894-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0b894-784">Le classi di modelli possono essere inserite in qualsiasi punto del progetto, ma la *:::no-loc(Models):::* cartella viene utilizzata per convenzione.</span><span class="sxs-lookup"><span data-stu-id="0b894-784">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="0b894-785">Aggiungere un contesto di database 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-785">Add a database context 2.1</span></span>

<span data-ttu-id="0b894-786">Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati.</span><span class="sxs-lookup"><span data-stu-id="0b894-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0b894-787">Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="0b894-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b894-789">Fare clic con il pulsante destro del mouse sulla *:::no-loc(Models):::* cartella e scegliere **Aggiungi**  >  **classe** .</span><span class="sxs-lookup"><span data-stu-id="0b894-789">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="0b894-790">Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="0b894-790">Name the class *TodoContext* and click **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0b894-791">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0b894-792">Aggiungere una `TodoContext` classe alla *:::no-loc(Models):::* cartella.</span><span class="sxs-lookup"><span data-stu-id="0b894-792">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="0b894-793">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="0b894-794">Registrare il contesto del database 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-794">Register the database context 2.1</span></span>

<span data-ttu-id="0b894-795">In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0b894-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0b894-796">Il contenitore rende disponibile il servizio ai controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="0b894-797">Aggiornare *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="0b894-798">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="0b894-798">The preceding code:</span></span>

* <span data-ttu-id="0b894-799">Rimuove le dichiarazioni `using` inutilizzate.</span><span class="sxs-lookup"><span data-stu-id="0b894-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0b894-800">Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="0b894-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0b894-801">Specifica che il contesto del database userà un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="0b894-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="0b894-802">Aggiungere un controller 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b894-804">Fare clic con il pulsante destro del mouse sulla cartella *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="0b894-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0b894-805">Selezionare **Aggiungi** > **nuovo elemento** .</span><span class="sxs-lookup"><span data-stu-id="0b894-805">Select **Add** > **New Item** .</span></span>
* <span data-ttu-id="0b894-806">Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare il modello **API Controller Class** (Classe controller API).</span><span class="sxs-lookup"><span data-stu-id="0b894-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="0b894-807">Assegnare alla classe il nome *TodoController* e selezionare **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="0b894-807">Name the class *TodoController* , and select **Add** .</span></span>

  ![Finestra di dialogo Aggiungi nuovo elemento con controller nella casella di ricerca e controller API Web selezionato](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0b894-809">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0b894-810">Nella cartella *Controllers* creare una classe denominata `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="0b894-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="0b894-811">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="0b894-812">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="0b894-812">The preceding code:</span></span>

* <span data-ttu-id="0b894-813">Definisce una classe controller API senza metodi.</span><span class="sxs-lookup"><span data-stu-id="0b894-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="0b894-814">Contrassegna la classe con l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="0b894-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="0b894-815">L'attributo indica che il controller risponde alle richieste di API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0b894-816">Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="0b894-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0b894-817">Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0b894-818">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="0b894-819">Aggiunge un elemento denominato `Item1` al database se il database è vuoto.</span><span class="sxs-lookup"><span data-stu-id="0b894-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="0b894-820">Questo codice si trova nel costruttore, quindi viene eseguito ogni volta che è presente una nuova richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b894-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="0b894-821">Se si eliminano tutti gli elementi, il costruttore crea di nuovo `Item1` quando viene nuovamente chiamato un metodo API.</span><span class="sxs-lookup"><span data-stu-id="0b894-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="0b894-822">Potrebbe pertanto sembrare che l'eliminazione non abbia funzionato, mentre di fatto ha funzionato.</span><span class="sxs-lookup"><span data-stu-id="0b894-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="0b894-823">Aggiungi metodi Get 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-823">Add Get methods 2.1</span></span>

<span data-ttu-id="0b894-824">Per specificare un'API che recupera elementi attività, aggiungere i metodi seguenti alla classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="0b894-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="0b894-825">Questi metodi implementano due metodi GET:</span><span class="sxs-lookup"><span data-stu-id="0b894-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="0b894-826">Arrestare l'app se è ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="0b894-826">Stop the app if it's still running.</span></span> <span data-ttu-id="0b894-827">Quindi eseguirla di nuovo per includere le modifiche più recenti.</span><span class="sxs-lookup"><span data-stu-id="0b894-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="0b894-828">Testare l'app chiamando i due endpoint da un browser.</span><span class="sxs-lookup"><span data-stu-id="0b894-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="0b894-829">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0b894-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="0b894-830">La chiamata a `GetTodoItems` genera la risposta HTTP seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="0b894-831">Percorsi URL e routing 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="0b894-832">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attributo indica un metodo che risponde a una richiesta HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="0b894-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0b894-833">Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0b894-834">Iniziare con la stringa di modello nell'attributo `Route` del controller:</span><span class="sxs-lookup"><span data-stu-id="0b894-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="0b894-835">Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller".</span><span class="sxs-lookup"><span data-stu-id="0b894-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0b894-836">In questo esempio il nome della classe controller è **Todo** Controller, pertanto il nome del controller è "todo".</span><span class="sxs-lookup"><span data-stu-id="0b894-836">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="0b894-837">Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="0b894-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0b894-838">Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso.</span><span class="sxs-lookup"><span data-stu-id="0b894-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0b894-839">In questo esempio non si usa un modello.</span><span class="sxs-lookup"><span data-stu-id="0b894-839">This sample doesn't use a template.</span></span> <span data-ttu-id="0b894-840">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0b894-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0b894-841">Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="0b894-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0b894-842">Quando viene chiamato `GetTodoItem`, il valore di `"{id}"` viene specificato per il metodo nel rispettivo parametro `id`.</span><span class="sxs-lookup"><span data-stu-id="0b894-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="0b894-843">Valori restituiti 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-843">Return values 2.1</span></span>

<span data-ttu-id="0b894-844">Il tipo restituito dei `GetTodoItems` metodi e `GetTodoItem` è [ActionResult. \<T> ](xref:web-api/action-return-types#actionresultt-type)</span><span class="sxs-lookup"><span data-stu-id="0b894-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0b894-845">ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="0b894-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0b894-846">Il codice di risposta per questo tipo restituito è 200, presupponendo che non esistano eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="0b894-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0b894-847">Le eccezioni non gestite vengono convertite in errori 5xx.</span><span class="sxs-lookup"><span data-stu-id="0b894-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0b894-848">I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b894-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0b894-849">Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:</span><span class="sxs-lookup"><span data-stu-id="0b894-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0b894-850">Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> codice di errore 404.</span><span class="sxs-lookup"><span data-stu-id="0b894-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="0b894-851">In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON.</span><span class="sxs-lookup"><span data-stu-id="0b894-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0b894-852">La restituzione di `item` risulta in una risposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="0b894-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="0b894-853">Testare il metodo GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="0b894-854">Questa esercitazione usa Postman per testare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0b894-855">Installare [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="0b894-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="0b894-856">Avviare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-856">Start the web app.</span></span>
* <span data-ttu-id="0b894-857">Avviare Postman.</span><span class="sxs-lookup"><span data-stu-id="0b894-857">Start Postman.</span></span>
* <span data-ttu-id="0b894-858">Disabilitare la **Verifica del certificato SSL** .</span><span class="sxs-lookup"><span data-stu-id="0b894-858">Disable **SSL certificate verification** .</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0b894-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b894-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b894-860">Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).</span><span class="sxs-lookup"><span data-stu-id="0b894-860">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0b894-861">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0b894-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0b894-862">Dalle preferenze del **poster**  >  **Preferences** (scheda **generale** ) disabilitare la **Verifica del certificato SSL** .</span><span class="sxs-lookup"><span data-stu-id="0b894-862">From **Postman** > **Preferences** ( **General** tab), disable **SSL certificate verification** .</span></span> <span data-ttu-id="0b894-863">In alternativa, selezionare la chiave inglese e selezionare **Settings** (Impostazioni), quindi disabilitare la verifica del certificato SSL.</span><span class="sxs-lookup"><span data-stu-id="0b894-863">Alternatively, select the wrench and select **Settings** , then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="0b894-864">Riattivare la verifica dei certificati SSL al termine del test del controller.</span><span class="sxs-lookup"><span data-stu-id="0b894-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="0b894-865">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="0b894-865">Create a new request.</span></span>
  * <span data-ttu-id="0b894-866">Impostare il metodo HTTP su **GET** .</span><span class="sxs-lookup"><span data-stu-id="0b894-866">Set the HTTP method to **GET** .</span></span>
  * <span data-ttu-id="0b894-867">Impostare l'URI della richiesta su `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="0b894-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="0b894-868">Ad esempio, `https://localhost:5001/api/todo`</span><span class="sxs-lookup"><span data-stu-id="0b894-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="0b894-869">Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.</span><span class="sxs-lookup"><span data-stu-id="0b894-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0b894-870">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="0b894-870">Select **Send** .</span></span>

![Postman con richiesta GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="0b894-872">Aggiungere un metodo Create 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-872">Add a Create method 2.1</span></span>

<span data-ttu-id="0b894-873">Aggiungere il metodo `PostTodoItem` seguente in *Controllers/TodoController.cs* :</span><span class="sxs-lookup"><span data-stu-id="0b894-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs* :</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="0b894-874">Il codice precedente è un metodo HTTP POST, come indicato dall' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="0b894-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="0b894-875">Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b894-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0b894-876">Il metodo `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="0b894-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="0b894-877">Restituisce un codice di stato HTTP 201 in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="0b894-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="0b894-878">HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="0b894-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0b894-879">Aggiunge un'intestazione `Location` alla risposta.</span><span class="sxs-lookup"><span data-stu-id="0b894-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="0b894-880">L'intestazione `Location` specifica l'URI dell'elemento attività appena creato.</span><span class="sxs-lookup"><span data-stu-id="0b894-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="0b894-881">Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="0b894-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0b894-882">Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`.</span><span class="sxs-lookup"><span data-stu-id="0b894-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0b894-883">La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="0b894-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="0b894-884">Testare il metodo PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="0b894-885">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-885">Build the project.</span></span>
* <span data-ttu-id="0b894-886">In Postman impostare il metodo HTTP su `POST`.</span><span class="sxs-lookup"><span data-stu-id="0b894-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0b894-887">Impostare l'URI su `https://localhost:<port>/api/TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="0b894-887">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="0b894-888">Ad esempio, `https://localhost:5001/api/TodoItem`</span><span class="sxs-lookup"><span data-stu-id="0b894-888">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="0b894-889">Selezionare la scheda **Corpo** .</span><span class="sxs-lookup"><span data-stu-id="0b894-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="0b894-890">Selezionare il pulsante di opzione **raw** (non elaborato).</span><span class="sxs-lookup"><span data-stu-id="0b894-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0b894-891">Impostare il tipo su **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="0b894-891">Set the type to **JSON (application/json)** .</span></span>
* <span data-ttu-id="0b894-892">Nel corpo della richiesta immettere codice JSON per un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="0b894-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0b894-893">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="0b894-893">Select **Send** .</span></span>

  ![Postman con richiesta di creazione](first-web-api/_static/create.png)

  <span data-ttu-id="0b894-895">Se viene visualizzato un errore HTTP 405 - Metodo non consentito, è probabile che il progetto non sia stato compilato dopo l'aggiunta del metodo `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="0b894-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="0b894-896">Testare l'URI dell'intestazione Location 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="0b894-897">Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).</span><span class="sxs-lookup"><span data-stu-id="0b894-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0b894-898">Copiare il valore dell'intestazione **Location** (Posizione):</span><span class="sxs-lookup"><span data-stu-id="0b894-898">Copy the **Location** header value:</span></span>

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="0b894-900">Impostare il metodo su GET.</span><span class="sxs-lookup"><span data-stu-id="0b894-900">Set the method to GET.</span></span>
* <span data-ttu-id="0b894-901">Impostare l'URI su `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="0b894-901">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="0b894-902">Ad esempio, `https://localhost:5001/api/TodoItems/2`</span><span class="sxs-lookup"><span data-stu-id="0b894-902">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="0b894-903">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="0b894-903">Select **Send** .</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="0b894-904">Aggiungere un metodo PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="0b894-905">Aggiungere il metodo `PutTodoItem` seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="0b894-906">`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="0b894-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0b894-907">La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0b894-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0b894-908">In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="0b894-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0b894-909">Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="0b894-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0b894-910">Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.</span><span class="sxs-lookup"><span data-stu-id="0b894-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="0b894-911">Testare il metodo PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="0b894-912">Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="0b894-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0b894-913">Deve esistere un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="0b894-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0b894-914">Chiamare GET per assicurarsi che sia presente un elemento nel database prima di effettuare una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="0b894-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0b894-915">Aggiornare l'attività con ID = 1 e impostarne il nome su "feed Fish":</span><span class="sxs-lookup"><span data-stu-id="0b894-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0b894-916">L'immagine seguente visualizza l'aggiornamento di Postman:</span><span class="sxs-lookup"><span data-stu-id="0b894-916">The following image shows the Postman update:</span></span>

![Console Postman con risposta 204 (No Content)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="0b894-918">Aggiungere un metodo DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="0b894-919">Aggiungere il metodo `DeleteTodoItem` seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="0b894-920">La `DeleteTodoItem` risposta è [204 (nessun contenuto)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0b894-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="0b894-921">Testare il metodo DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="0b894-922">Usare Postman per eliminare un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="0b894-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0b894-923">Impostare il metodo su `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="0b894-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0b894-924">Impostare l'URI dell'oggetto da eliminare (ad esempio, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="0b894-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="0b894-925">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="0b894-925">Select **Send** .</span></span>

<span data-ttu-id="0b894-926">L'app di esempio consente di eliminare tutti gli elementi.</span><span class="sxs-lookup"><span data-stu-id="0b894-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="0b894-927">Quando viene eliminato l'ultimo elemento, tuttavia, ne viene creato uno nuovo dal costruttore della classe modello alla successiva chiamata dell'API.</span><span class="sxs-lookup"><span data-stu-id="0b894-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="0b894-928">Chiamare l'API Web con JavaScript 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="0b894-929">In questa sezione viene aggiunta una pagina HTML che usa JavaScript per chiamare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="0b894-930">jQuery avvia la richiesta.</span><span class="sxs-lookup"><span data-stu-id="0b894-930">jQuery initiates the request.</span></span> <span data-ttu-id="0b894-931">JavaScript aggiorna la pagina con i dettagli della risposta dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="0b894-932">Configurare l'app per [servire file statici](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) e [abilitare il mapping del file predefinito](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) aggiornando *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="0b894-933">Creare una cartella *wwwroot* nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="0b894-934">Aggiungere un file HTML denominato *index.html* alla directory *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="0b894-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="0b894-935">Sostituire il contenuto con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="0b894-936">Aggiungere un file JavaScript con nome *site.js* alla directory *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="0b894-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="0b894-937">Sostituirne il contenuto con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0b894-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="0b894-938">Può essere necessario modificare le impostazioni di avvio del progetto ASP.NET Core per il test della pagina HTML in locale:</span><span class="sxs-lookup"><span data-stu-id="0b894-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="0b894-939">Aprire *Properties\launchSettings.json* .</span><span class="sxs-lookup"><span data-stu-id="0b894-939">Open *Properties\launchSettings.json* .</span></span>
* <span data-ttu-id="0b894-940">Rimuovere la `launchUrl` proprietà per forzare l'apertura dell'app a *index.html* &mdash; file predefinito del progetto.</span><span class="sxs-lookup"><span data-stu-id="0b894-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="0b894-941">In questo esempio vengono chiamati tutti i metodi CRUD dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="0b894-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="0b894-942">Di seguito sono incluse le spiegazioni delle chiamate all'API.</span><span class="sxs-lookup"><span data-stu-id="0b894-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="0b894-943">Ottenere un elenco di elementi attività 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="0b894-944">jQuery invia una richiesta HTTP GET all'API Web, che restituisce JSON che rappresenta una matrice di elementi attività.</span><span class="sxs-lookup"><span data-stu-id="0b894-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="0b894-945">La funzione di callback `success` viene chiamata se la richiesta ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="0b894-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="0b894-946">Nel callback il modello DOM viene aggiornato con le informazioni dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="0b894-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="0b894-947">Aggiungere un'attività 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="0b894-948">jQuery invia una richiesta HTTP POST con l'elemento attività nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="0b894-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="0b894-949">Le opzioni `accepts` e `contentType` sono impostate su `application/json` per specificare il tipo di supporto ricevuto e inviato.</span><span class="sxs-lookup"><span data-stu-id="0b894-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="0b894-950">L'elemento attività viene convertito in JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="0b894-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="0b894-951">Quando l'API restituisce un codice di stato di esecuzione riuscita, viene chiamata la funzione `getData` per aggiornare la tabella HTML.</span><span class="sxs-lookup"><span data-stu-id="0b894-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="0b894-952">Aggiornare un'attività 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="0b894-953">L'aggiornamento di un elemento attività è simile all'aggiunta di un elemento di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="0b894-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="0b894-954">L'`url` cambia per includere l'identificatore univoco dell'elemento e `type` è `PUT`.</span><span class="sxs-lookup"><span data-stu-id="0b894-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="0b894-955">Eliminare un'attività 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="0b894-956">È possibile eliminare un elemento attività impostando `type` su `DELETE` nella chiamata AJAX e specificando l'identificatore univoco dell'elemento nell'URL.</span><span class="sxs-lookup"><span data-stu-id="0b894-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="0b894-957">Aggiungere il supporto per l'autenticazione a un'API Web 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="0b894-958">Risorse aggiuntive 2,1</span><span class="sxs-lookup"><span data-stu-id="0b894-958">Additional resources 2.1</span></span>

<span data-ttu-id="0b894-959">[Visualizzare o scaricare il codice di esempio per questa esercitazione](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="0b894-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="0b894-960">Vedere [come scaricare un esempio](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0b894-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="0b894-961">Per altre informazioni, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b894-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="0b894-962">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="0b894-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
