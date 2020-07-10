---
title: "Esercitazione: creare un'API Web con ASP.NET Core"
author: rick-anderson
description: Informazioni su come creare un'API Web con ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: 79f36168d0430ceee3794cfb5a4e29f3671ac73f
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212619"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="8af1c-103">Esercitazione: creare un'API Web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8af1c-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="8af1c-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="8af1c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="8af1c-105">Questa esercitazione illustra le nozioni di base della creazione di un'API Web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8af1c-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8af1c-106">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="8af1c-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8af1c-107">Creare un progetto di API Web.</span><span class="sxs-lookup"><span data-stu-id="8af1c-107">Create a web API project.</span></span>
> * <span data-ttu-id="8af1c-108">Aggiungere una classe modello e un contesto di database.</span><span class="sxs-lookup"><span data-stu-id="8af1c-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="8af1c-109">Eseguire lo scaffolding di un controller con i metodi CRUD.</span><span class="sxs-lookup"><span data-stu-id="8af1c-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="8af1c-110">Configurare il routing, i percorsi URL e i valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="8af1c-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="8af1c-111">Chiamare l'API Web con Postman.</span><span class="sxs-lookup"><span data-stu-id="8af1c-111">Call the web API with Postman.</span></span>

<span data-ttu-id="8af1c-112">Al termine si avrà un'API Web che può gestire gli elementi di tipo "attività" archiviati in un database.</span><span class="sxs-lookup"><span data-stu-id="8af1c-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="8af1c-113">Panoramica</span><span class="sxs-lookup"><span data-stu-id="8af1c-113">Overview</span></span>

<span data-ttu-id="8af1c-114">Questa esercitazione consente di creare l'API seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="8af1c-115">API</span><span class="sxs-lookup"><span data-stu-id="8af1c-115">API</span></span> | <span data-ttu-id="8af1c-116">Descrizione</span><span class="sxs-lookup"><span data-stu-id="8af1c-116">Description</span></span> | <span data-ttu-id="8af1c-117">Corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="8af1c-117">Request body</span></span> | <span data-ttu-id="8af1c-118">Corpo della risposta</span><span class="sxs-lookup"><span data-stu-id="8af1c-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="8af1c-119">Ottiene tutti gli elementi attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-119">Get all to-do items</span></span> | <span data-ttu-id="8af1c-120">Nessuno</span><span class="sxs-lookup"><span data-stu-id="8af1c-120">None</span></span> | <span data-ttu-id="8af1c-121">Matrice di elementi attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="8af1c-122">Ottiene un elemento in base all'ID</span><span class="sxs-lookup"><span data-stu-id="8af1c-122">Get an item by ID</span></span> | <span data-ttu-id="8af1c-123">Nessuno</span><span class="sxs-lookup"><span data-stu-id="8af1c-123">None</span></span> | <span data-ttu-id="8af1c-124">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="8af1c-125">Aggiunge un nuovo elemento</span><span class="sxs-lookup"><span data-stu-id="8af1c-125">Add a new item</span></span> | <span data-ttu-id="8af1c-126">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-126">To-do item</span></span> | <span data-ttu-id="8af1c-127">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="8af1c-128">Aggiorna un elemento esistente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="8af1c-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="8af1c-129">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-129">To-do item</span></span> | <span data-ttu-id="8af1c-130">Nessuno</span><span class="sxs-lookup"><span data-stu-id="8af1c-130">None</span></span> |
|<span data-ttu-id="8af1c-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="8af1c-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="8af1c-132">Elimina un elemento &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="8af1c-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="8af1c-133">nessuno</span><span class="sxs-lookup"><span data-stu-id="8af1c-133">None</span></span> | <span data-ttu-id="8af1c-134">nessuno</span><span class="sxs-lookup"><span data-stu-id="8af1c-134">None</span></span>|

<span data-ttu-id="8af1c-135">Il diagramma seguente visualizza la struttura dell'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-135">The following diagram shows the design of the app.</span></span>

![Il client è rappresentato da una casella a sinistra.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="8af1c-141">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="8af1c-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af1c-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af1c-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-144">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="8af1c-145">Creare un progetto Web</span><span class="sxs-lookup"><span data-stu-id="8af1c-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8af1c-147">Scegliere **Nuovo** > **Progetto** dal menu **File**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8af1c-148">Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="8af1c-149">Assegnare al progetto il nome *TodoApi* e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="8af1c-150">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.net Core** e **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="8af1c-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="8af1c-151">Selezionare il modello **API** e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-151">Select the **API** template and click **Create**.</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af1c-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af1c-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8af1c-154">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8af1c-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8af1c-155">Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="8af1c-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="8af1c-156">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8af1c-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="8af1c-157">Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="8af1c-158">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="8af1c-158">The preceding commands:</span></span>

  * <span data-ttu-id="8af1c-159">Crea un nuovo progetto API Web e lo apre in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8af1c-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="8af1c-160">Aggiunge i pacchetti NuGet necessari nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="8af1c-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-161">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8af1c-162">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-162">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="8af1c-164">In Visual Studio per Mac precedente alla versione 8,6 Selezionare API **app .NET Core**  >  **App**  >  **API**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="8af1c-165">Nella versione 8,6 o successive selezionare API **app Web e console**  >  **App**  >  **API**  >  **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="8af1c-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![Selezione modello API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="8af1c-167">Nella finestra di dialogo **Configura nuova ASP.NET Core API Web** selezionare il **Framework di destinazione**.NET Core 3. x più recente.</span><span class="sxs-lookup"><span data-stu-id="8af1c-167">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="8af1c-168">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-168">Select **Next**.</span></span>

* <span data-ttu-id="8af1c-169">Immettere *TodoApi* in **Nome progetto**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="8af1c-171">Aprire un terminale di comando nella cartella di progetto ed eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8af1c-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="8af1c-172">Testare l'API</span><span class="sxs-lookup"><span data-stu-id="8af1c-172">Test the API</span></span>

<span data-ttu-id="8af1c-173">Il modello di progetto crea un'API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="8af1c-174">Chiamare il metodo `Get` da un browser per eseguire il test dell'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8af1c-176">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8af1c-177">Visual Studio apre un browser e naviga all'indirizzo `https://localhost:<port>/WeatherForecast`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="8af1c-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="8af1c-178">Se in una finestra di dialogo viene chiesto se considerare attendibile il certificato IIS Express, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="8af1c-179">Nella finestra di dialogo **Avviso di sicurezza** successiva selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af1c-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af1c-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8af1c-181">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8af1c-182">In un browser passare all'URL seguente: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-183">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8af1c-184">Selezionare **Esegui**  >  **Avvia debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="8af1c-185">Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="8af1c-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="8af1c-186">Viene restituito un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="8af1c-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="8af1c-187">Accodare `/WeatherForecast` all'URL (impostare l'URL su `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="8af1c-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="8af1c-188">Viene restituito un codice JSON simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="8af1c-189">Aggiungere una classe del modello</span><span class="sxs-lookup"><span data-stu-id="8af1c-189">Add a model class</span></span>

<span data-ttu-id="8af1c-190">Un *modello* è un set di classi che rappresentano i dati gestiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="8af1c-191">Il modello per questa app è una classe `TodoItem` singola.</span><span class="sxs-lookup"><span data-stu-id="8af1c-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8af1c-193">In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="8af1c-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="8af1c-194">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8af1c-195">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="8af1c-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="8af1c-196">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8af1c-197">Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="8af1c-198">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af1c-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af1c-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8af1c-200">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="8af1c-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="8af1c-201">Aggiungere una classe `TodoItem` alla cartella *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-202">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8af1c-203">Fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="8af1c-203">Right-click the project.</span></span> <span data-ttu-id="8af1c-204">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8af1c-205">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="8af1c-205">Name the folder *Models*.</span></span>

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="8af1c-207">Fare clic con il pulsante destro del mouse sulla cartella *Models* e selezionare **Aggiungi** > **Nuovo file** > **Generale** > **Classe vuota**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="8af1c-208">Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="8af1c-209">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="8af1c-210">La proprietà `Id` funziona come chiave univoca in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="8af1c-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="8af1c-211">È possibile inserire le classi del modello in qualsiasi punto del progetto, ma per convenzione viene usata la cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="8af1c-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="8af1c-212">Aggiungere un contesto di database</span><span class="sxs-lookup"><span data-stu-id="8af1c-212">Add a database context</span></span>

<span data-ttu-id="8af1c-213">Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati.</span><span class="sxs-lookup"><span data-stu-id="8af1c-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="8af1c-214">Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="8af1c-216">Aggiungere Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="8af1c-216">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="8af1c-217">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="8af1c-218">Selezionare la scheda **Esplora** e quindi immettere **Microsoft.EntityFrameworkCore.SqlServer** nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="8af1c-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="8af1c-219">Nel riquadro sinistro selezionare **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="8af1c-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="8af1c-220">Selezionare la casella di controllo **Progetto** nel riquadro di destra e quindi selezionare **Installa**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="8af1c-221">Usare le istruzioni precedenti per aggiungere il pacchetto NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-221">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gestione pacchetti NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="8af1c-223">Aggiungere il contesto del database TodoContext</span><span class="sxs-lookup"><span data-stu-id="8af1c-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="8af1c-224">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8af1c-225">Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-226">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8af1c-227">Aggiungere una classe `TodoContext` alla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="8af1c-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="8af1c-228">Immettere il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="8af1c-229">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="8af1c-229">Register the database context</span></span>

<span data-ttu-id="8af1c-230">In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8af1c-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8af1c-231">Il contenitore rende disponibile il servizio ai controller.</span><span class="sxs-lookup"><span data-stu-id="8af1c-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="8af1c-232">Aggiornare *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="8af1c-233">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-233">The preceding code:</span></span>

* <span data-ttu-id="8af1c-234">Rimuove le dichiarazioni `using` inutilizzate.</span><span class="sxs-lookup"><span data-stu-id="8af1c-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="8af1c-235">Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8af1c-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="8af1c-236">Specifica che il contesto del database userà un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="8af1c-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="8af1c-237">Eseguire lo scaffolding di un controller</span><span class="sxs-lookup"><span data-stu-id="8af1c-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8af1c-239">Fare clic con il pulsante destro del mouse sulla cartella *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="8af1c-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="8af1c-240">Selezionare **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="8af1c-241">Selezionare **Controller API con azioni, che usa Entity Framework** e quindi selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="8af1c-242">Nella finestra di dialogo **Add API Controller with actions, using Entity Framework** (Aggiungi controller API con azioni, che usa Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="8af1c-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="8af1c-243">Selezionare **TodoItem (TodoApi. Models)** nella **classe del modello**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="8af1c-244">Selezionare **TodoContext (TodoApi. Models)** nella **classe del contesto dati**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="8af1c-245">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-246">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8af1c-247">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8af1c-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="8af1c-248">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="8af1c-248">The preceding commands:</span></span>

* <span data-ttu-id="8af1c-249">Aggiungere i pacchetti NuGet necessari per lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8af1c-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="8af1c-250">Installa il motore di scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="8af1c-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="8af1c-251">Esegue lo scaffolding di `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="8af1c-252">Il codice generato:</span><span class="sxs-lookup"><span data-stu-id="8af1c-252">The generated code:</span></span>

* <span data-ttu-id="8af1c-253">Contrassegna la classe con l' [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="8af1c-253">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="8af1c-254">L'attributo indica che il controller risponde alle richieste di API Web.</span><span class="sxs-lookup"><span data-stu-id="8af1c-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="8af1c-255">Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="8af1c-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="8af1c-256">Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="8af1c-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="8af1c-257">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="8af1c-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="8af1c-258">I modelli di ASP.NET Core per:</span><span class="sxs-lookup"><span data-stu-id="8af1c-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="8af1c-259">I controller con le visualizzazioni includono `[action]` nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="8af1c-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="8af1c-260">I controller API non includono `[action]` nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="8af1c-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="8af1c-261">Quando il `[action]` token non è incluso nel modello di route, il nome dell' [azione](xref:mvc/controllers/routing#action) viene escluso dalla route.</span><span class="sxs-lookup"><span data-stu-id="8af1c-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="8af1c-262">Ovvero, il nome del metodo associato all'azione non viene usato nella route corrispondente.</span><span class="sxs-lookup"><span data-stu-id="8af1c-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="8af1c-263">Esaminare il metodo di creazione PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="8af1c-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="8af1c-264">Sostituire l'istruzione return in `PostTodoItem` per usare l'operatore [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="8af1c-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="8af1c-265">Il codice precedente è un metodo HTTP POST, come indicato dall' [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="8af1c-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="8af1c-266">Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="8af1c-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="8af1c-267">Il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="8af1c-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="8af1c-268">Restituisce un codice di stato HTTP 201 in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="8af1c-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="8af1c-269">HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="8af1c-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="8af1c-270">Aggiunge un'intestazione [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) alla risposta.</span><span class="sxs-lookup"><span data-stu-id="8af1c-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="8af1c-271">L'intestazione `Location` specifica l'[URI](https://developer.mozilla.org/docs/Glossary/URI) dell'elemento attività appena creato.</span><span class="sxs-lookup"><span data-stu-id="8af1c-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="8af1c-272">Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="8af1c-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="8af1c-273">Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="8af1c-274">La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="8af1c-275">Installare Postman</span><span class="sxs-lookup"><span data-stu-id="8af1c-275">Install Postman</span></span>

<span data-ttu-id="8af1c-276">Questa esercitazione usa Postman per testare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="8af1c-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="8af1c-277">Installa il [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="8af1c-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="8af1c-278">Avviare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="8af1c-278">Start the web app.</span></span>
* <span data-ttu-id="8af1c-279">Avviare Postman.</span><span class="sxs-lookup"><span data-stu-id="8af1c-279">Start Postman.</span></span>
* <span data-ttu-id="8af1c-280">Disattivare **SSL certificate verification** (Verifica certificato SSL)</span><span class="sxs-lookup"><span data-stu-id="8af1c-280">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="8af1c-281">Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).</span><span class="sxs-lookup"><span data-stu-id="8af1c-281">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="8af1c-282">Riattivare la verifica dei certificati SSL al termine del test del controller.</span><span class="sxs-lookup"><span data-stu-id="8af1c-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="8af1c-283">Testare PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="8af1c-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="8af1c-284">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="8af1c-284">Create a new request.</span></span>
* <span data-ttu-id="8af1c-285">Impostare il metodo HTTP su `POST`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="8af1c-286">Selezionare la scheda **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="8af1c-287">Selezionare il pulsante di opzione **raw** (non elaborato).</span><span class="sxs-lookup"><span data-stu-id="8af1c-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="8af1c-288">Impostare il tipo su **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="8af1c-289">Nel corpo della richiesta immettere codice JSON per un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="8af1c-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="8af1c-290">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="8af1c-290">Select **Send**.</span></span>

  ![Postman con richiesta di creazione](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="8af1c-292">Testare l'URI dell'intestazione della posizione</span><span class="sxs-lookup"><span data-stu-id="8af1c-292">Test the location header URI</span></span>

* <span data-ttu-id="8af1c-293">Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).</span><span class="sxs-lookup"><span data-stu-id="8af1c-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="8af1c-294">Copiare il valore dell'intestazione **Location** (Posizione):</span><span class="sxs-lookup"><span data-stu-id="8af1c-294">Copy the **Location** header value:</span></span>

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="8af1c-296">Impostare il metodo su GET.</span><span class="sxs-lookup"><span data-stu-id="8af1c-296">Set the method to GET.</span></span>
* <span data-ttu-id="8af1c-297">Incollare l'URI (ad esempio, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="8af1c-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="8af1c-298">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="8af1c-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="8af1c-299">Esaminare i metodi GET</span><span class="sxs-lookup"><span data-stu-id="8af1c-299">Examine the GET methods</span></span>

<span data-ttu-id="8af1c-300">Questi metodi implementano due metodi GET:</span><span class="sxs-lookup"><span data-stu-id="8af1c-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="8af1c-301">Testare l'app chiamando i due endpoint da un browser o da Postman.</span><span class="sxs-lookup"><span data-stu-id="8af1c-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="8af1c-302">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8af1c-302">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="8af1c-303">Una risposta simile alla seguente viene generata dalla chiamata a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="8af1c-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="8af1c-304">Testare Get con Postman</span><span class="sxs-lookup"><span data-stu-id="8af1c-304">Test Get with Postman</span></span>

* <span data-ttu-id="8af1c-305">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="8af1c-305">Create a new request.</span></span>
* <span data-ttu-id="8af1c-306">Impostare il metodo HTTP su **GET**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="8af1c-307">Impostare l'URL della richiesta su `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="8af1c-308">Ad esempio, `https://localhost:5001/api/TodoItems`</span><span class="sxs-lookup"><span data-stu-id="8af1c-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="8af1c-309">Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.</span><span class="sxs-lookup"><span data-stu-id="8af1c-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="8af1c-310">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="8af1c-310">Select **Send**.</span></span>

<span data-ttu-id="8af1c-311">Questa app usa un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="8af1c-311">This app uses an in-memory database.</span></span> <span data-ttu-id="8af1c-312">Se l'app viene arrestata e avviata, la richiesta GET precedente non restituirà alcun dato.</span><span class="sxs-lookup"><span data-stu-id="8af1c-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="8af1c-313">Se non vengono restituiti dati, eseguire [POST](#post) per pubblicare i dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="8af1c-314">Routing e percorsi di URL</span><span class="sxs-lookup"><span data-stu-id="8af1c-314">Routing and URL paths</span></span>

<span data-ttu-id="8af1c-315">L' [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attributo indica un metodo che risponde a una richiesta HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="8af1c-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="8af1c-316">Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="8af1c-317">Iniziare con la stringa di modello nell'attributo `Route` del controller:</span><span class="sxs-lookup"><span data-stu-id="8af1c-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="8af1c-318">Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller".</span><span class="sxs-lookup"><span data-stu-id="8af1c-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="8af1c-319">In questo esempio il nome della classe controller è **TodoItems**Controller, quindi il nome del controller è "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="8af1c-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="8af1c-320">Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="8af1c-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="8af1c-321">Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso.</span><span class="sxs-lookup"><span data-stu-id="8af1c-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="8af1c-322">In questo esempio non si usa un modello.</span><span class="sxs-lookup"><span data-stu-id="8af1c-322">This sample doesn't use a template.</span></span> <span data-ttu-id="8af1c-323">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="8af1c-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="8af1c-324">Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="8af1c-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="8af1c-325">Quando `GetTodoItem` viene richiamato, il valore di `"{id}"` nell'URL viene fornito al metodo nel `id` parametro.</span><span class="sxs-lookup"><span data-stu-id="8af1c-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="8af1c-326">Valori restituiti</span><span class="sxs-lookup"><span data-stu-id="8af1c-326">Return values</span></span>

<span data-ttu-id="8af1c-327">Il tipo restituito dei `GetTodoItems` metodi e `GetTodoItem` è [ActionResult. \<T> ](xref:web-api/action-return-types#actionresultt-type)</span><span class="sxs-lookup"><span data-stu-id="8af1c-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="8af1c-328">ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="8af1c-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="8af1c-329">Il codice di risposta per questo tipo restituito è 200, presupponendo che non esistano eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="8af1c-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="8af1c-330">Le eccezioni non gestite vengono convertite in errori 5xx.</span><span class="sxs-lookup"><span data-stu-id="8af1c-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="8af1c-331">I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="8af1c-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="8af1c-332">Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:</span><span class="sxs-lookup"><span data-stu-id="8af1c-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="8af1c-333">Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un codice di errore 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="8af1c-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="8af1c-334">In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON.</span><span class="sxs-lookup"><span data-stu-id="8af1c-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="8af1c-335">La restituzione di `item` risulta in una risposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="8af1c-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="8af1c-336">Metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="8af1c-336">The PutTodoItem method</span></span>

<span data-ttu-id="8af1c-337">Esaminare il metodo `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="8af1c-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="8af1c-338">`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="8af1c-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="8af1c-339">La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="8af1c-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="8af1c-340">In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="8af1c-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="8af1c-341">Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="8af1c-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="8af1c-342">Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.</span><span class="sxs-lookup"><span data-stu-id="8af1c-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="8af1c-343">Testare il metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="8af1c-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="8af1c-344">Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="8af1c-344">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="8af1c-345">Deve esistere un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="8af1c-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="8af1c-346">Chiamare GET per assicurarsi che sia presente un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="8af1c-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="8af1c-347">Aggiornare l'elemento attività con ID = 1 e impostarne il nome su "feed fish":</span><span class="sxs-lookup"><span data-stu-id="8af1c-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="8af1c-348">L'immagine seguente visualizza l'aggiornamento di Postman:</span><span class="sxs-lookup"><span data-stu-id="8af1c-348">The following image shows the Postman update:</span></span>

![Console Postman con risposta 204 (No Content)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="8af1c-350">Metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="8af1c-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="8af1c-351">Esaminare il metodo `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="8af1c-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="8af1c-352">Testare il metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="8af1c-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="8af1c-353">Usare Postman per eliminare un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="8af1c-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="8af1c-354">Impostare il metodo su `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="8af1c-355">Impostare l'URI dell'oggetto da eliminare (ad esempio `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="8af1c-355">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="8af1c-356">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="8af1c-356">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="8af1c-357">Impedisci overposting</span><span class="sxs-lookup"><span data-stu-id="8af1c-357">Prevent over-posting</span></span>

<span data-ttu-id="8af1c-358">Attualmente l'app di esempio espone l'intero `TodoItem` oggetto.</span><span class="sxs-lookup"><span data-stu-id="8af1c-358">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="8af1c-359">Le app di produzione limitano in genere i dati di input e restituiti usando un subset del modello.</span><span class="sxs-lookup"><span data-stu-id="8af1c-359">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="8af1c-360">Esistono diversi motivi alla base di questo e la sicurezza è una delle principali.</span><span class="sxs-lookup"><span data-stu-id="8af1c-360">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="8af1c-361">Il subset di un modello è in genere indicato come oggetto Trasferimento dati (DTO), modello di input o modello di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="8af1c-361">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="8af1c-362">**Dto** viene usato in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="8af1c-362">**DTO** is used in this article.</span></span>

<span data-ttu-id="8af1c-363">Un DTO può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="8af1c-363">A DTO may be used to:</span></span>

* <span data-ttu-id="8af1c-364">Impedisci l'overposting.</span><span class="sxs-lookup"><span data-stu-id="8af1c-364">Prevent over-posting.</span></span>
* <span data-ttu-id="8af1c-365">Nascondere le proprietà che i client non dovrebbero visualizzare.</span><span class="sxs-lookup"><span data-stu-id="8af1c-365">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="8af1c-366">Omettere alcune proprietà per ridurre le dimensioni del payload.</span><span class="sxs-lookup"><span data-stu-id="8af1c-366">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="8af1c-367">Rendere flat gli oggetti grafici che contengono oggetti annidati.</span><span class="sxs-lookup"><span data-stu-id="8af1c-367">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="8af1c-368">I grafici a oggetti flat possono essere più pratici per i client.</span><span class="sxs-lookup"><span data-stu-id="8af1c-368">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="8af1c-369">Per illustrare l'approccio DTO, aggiornare la `TodoItem` classe in modo da includere un campo Secret:</span><span class="sxs-lookup"><span data-stu-id="8af1c-369">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="8af1c-370">Il campo segreto deve essere nascosto da questa app, ma un'app amministrativa può scegliere di esporla.</span><span class="sxs-lookup"><span data-stu-id="8af1c-370">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="8af1c-371">Verificare che sia possibile pubblicare e ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="8af1c-371">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="8af1c-372">Creare un modello DTO:</span><span class="sxs-lookup"><span data-stu-id="8af1c-372">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="8af1c-373">Aggiornare `TodoItemsController` da usare `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="8af1c-373">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="8af1c-374">Verificare che non sia possibile inserire o ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="8af1c-374">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="8af1c-375">Chiamare l'API Web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="8af1c-375">Call the web API with JavaScript</span></span>

<span data-ttu-id="8af1c-376">Vedere [esercitazione: chiamare un'API web ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="8af1c-376">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8af1c-377">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="8af1c-377">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8af1c-378">Creare un progetto di API Web.</span><span class="sxs-lookup"><span data-stu-id="8af1c-378">Create a web API project.</span></span>
> * <span data-ttu-id="8af1c-379">Aggiungere una classe modello e un contesto di database.</span><span class="sxs-lookup"><span data-stu-id="8af1c-379">Add a model class and a database context.</span></span>
> * <span data-ttu-id="8af1c-380">Aggiungere un controller.</span><span class="sxs-lookup"><span data-stu-id="8af1c-380">Add a controller.</span></span>
> * <span data-ttu-id="8af1c-381">Aggiungere metodi CRUD.</span><span class="sxs-lookup"><span data-stu-id="8af1c-381">Add CRUD methods.</span></span>
> * <span data-ttu-id="8af1c-382">Configurare routing e percorsi URL.</span><span class="sxs-lookup"><span data-stu-id="8af1c-382">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="8af1c-383">Specificare valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="8af1c-383">Specify return values.</span></span>
> * <span data-ttu-id="8af1c-384">Chiamare l'API Web con Postman.</span><span class="sxs-lookup"><span data-stu-id="8af1c-384">Call the web API with Postman.</span></span>
> * <span data-ttu-id="8af1c-385">Chiamare l'API Web con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8af1c-385">Call the web API with JavaScript.</span></span>

<span data-ttu-id="8af1c-386">Al termine si dispone di un'API web che può gestire gli elementi di tipo "attività" archiviati in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="8af1c-386">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="8af1c-387">Panoramica</span><span class="sxs-lookup"><span data-stu-id="8af1c-387">Overview</span></span>

<span data-ttu-id="8af1c-388">Questa esercitazione consente di creare l'API seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-388">This tutorial creates the following API:</span></span>

|<span data-ttu-id="8af1c-389">API</span><span class="sxs-lookup"><span data-stu-id="8af1c-389">API</span></span> | <span data-ttu-id="8af1c-390">Descrizione</span><span class="sxs-lookup"><span data-stu-id="8af1c-390">Description</span></span> | <span data-ttu-id="8af1c-391">Corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="8af1c-391">Request body</span></span> | <span data-ttu-id="8af1c-392">Corpo della risposta</span><span class="sxs-lookup"><span data-stu-id="8af1c-392">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="8af1c-393">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="8af1c-393">GET /api/TodoItems</span></span> | <span data-ttu-id="8af1c-394">Ottiene tutti gli elementi attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-394">Get all to-do items</span></span> | <span data-ttu-id="8af1c-395">Nessuno</span><span class="sxs-lookup"><span data-stu-id="8af1c-395">None</span></span> | <span data-ttu-id="8af1c-396">Matrice di elementi attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-396">Array of to-do items</span></span>|
|<span data-ttu-id="8af1c-397">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="8af1c-397">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="8af1c-398">Ottiene un elemento in base all'ID</span><span class="sxs-lookup"><span data-stu-id="8af1c-398">Get an item by ID</span></span> | <span data-ttu-id="8af1c-399">Nessuno</span><span class="sxs-lookup"><span data-stu-id="8af1c-399">None</span></span> | <span data-ttu-id="8af1c-400">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-400">To-do item</span></span>|
|<span data-ttu-id="8af1c-401">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="8af1c-401">POST /api/TodoItems</span></span> | <span data-ttu-id="8af1c-402">Aggiunge un nuovo elemento</span><span class="sxs-lookup"><span data-stu-id="8af1c-402">Add a new item</span></span> | <span data-ttu-id="8af1c-403">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-403">To-do item</span></span> | <span data-ttu-id="8af1c-404">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-404">To-do item</span></span> |
|<span data-ttu-id="8af1c-405">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="8af1c-405">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="8af1c-406">Aggiorna un elemento esistente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="8af1c-406">Update an existing item &nbsp;</span></span> | <span data-ttu-id="8af1c-407">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-407">To-do item</span></span> | <span data-ttu-id="8af1c-408">Nessuno</span><span class="sxs-lookup"><span data-stu-id="8af1c-408">None</span></span> |
|<span data-ttu-id="8af1c-409">Elimina/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="8af1c-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="8af1c-410">Elimina un elemento &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="8af1c-410">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="8af1c-411">nessuno</span><span class="sxs-lookup"><span data-stu-id="8af1c-411">None</span></span> | <span data-ttu-id="8af1c-412">nessuno</span><span class="sxs-lookup"><span data-stu-id="8af1c-412">None</span></span>|

<span data-ttu-id="8af1c-413">Il diagramma seguente visualizza la struttura dell'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-413">The following diagram shows the design of the app.</span></span>

![Il client è rappresentato da una casella a sinistra.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="8af1c-419">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="8af1c-419">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-420">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af1c-421">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af1c-421">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-422">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="8af1c-423">Creare un progetto Web</span><span class="sxs-lookup"><span data-stu-id="8af1c-423">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-424">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8af1c-425">Scegliere **Nuovo** > **Progetto** dal menu **File**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-425">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8af1c-426">Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-426">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="8af1c-427">Assegnare al progetto il nome *TodoApi* e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-427">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="8af1c-428">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.NET Core** e **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-428">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="8af1c-429">Selezionare il modello **API** e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-429">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="8af1c-430">**Non** selezionare **Abilita supporto Docker**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-430">**Don't** select **Enable Docker Support**.</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af1c-432">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af1c-432">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8af1c-433">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8af1c-433">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8af1c-434">Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="8af1c-434">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="8af1c-435">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8af1c-435">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="8af1c-436">Questi comandi creano un nuovo progetto API Web e aprono una nuova istanza di Visual Studio Code nella nuova cartella di progetto.</span><span class="sxs-lookup"><span data-stu-id="8af1c-436">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="8af1c-437">Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-437">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-438">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8af1c-439">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-439">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="8af1c-441">In Visual Studio per Mac precedente alla versione 8,6 Selezionare API **app .NET Core**  >  **App**  >  **API**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-441">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="8af1c-442">Nella versione 8,6 o successive selezionare API **app Web e console**  >  **App**  >  **API**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-442">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="8af1c-443">Nella finestra di dialogo **Configura nuova ASP.NET Core API Web** selezionare il **Framework di destinazione**.NET Core 2. x più recente.</span><span class="sxs-lookup"><span data-stu-id="8af1c-443">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="8af1c-444">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-444">Select **Next**.</span></span>

* <span data-ttu-id="8af1c-445">Immettere *TodoApi* in **Nome progetto**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="8af1c-447">Testare l'API</span><span class="sxs-lookup"><span data-stu-id="8af1c-447">Test the API</span></span>

<span data-ttu-id="8af1c-448">Il modello di progetto crea un'API `values`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-448">The project template creates a `values` API.</span></span> <span data-ttu-id="8af1c-449">Chiamare il metodo `Get` da un browser per eseguire il test dell'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8af1c-451">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8af1c-452">Visual Studio apre un browser e naviga all'indirizzo `https://localhost:<port>/api/values`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="8af1c-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="8af1c-453">Se in una finestra di dialogo viene chiesto se considerare attendibile il certificato IIS Express, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="8af1c-454">Nella finestra di dialogo **Avviso di sicurezza** successiva selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af1c-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af1c-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8af1c-456">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8af1c-457">In un browser passare all'URL seguente: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-458">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8af1c-459">Selezionare **Esegui**  >  **Avvia debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="8af1c-460">Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="8af1c-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="8af1c-461">Viene restituito un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="8af1c-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="8af1c-462">Accodare `/api/values` all'URL (impostare l'URL su `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="8af1c-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="8af1c-463">Viene restituito il codice JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="8af1c-464">Aggiungere una classe del modello</span><span class="sxs-lookup"><span data-stu-id="8af1c-464">Add a model class</span></span>

<span data-ttu-id="8af1c-465">Un *modello* è un set di classi che rappresentano i dati gestiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="8af1c-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="8af1c-466">Il modello per questa app è una classe `TodoItem` singola.</span><span class="sxs-lookup"><span data-stu-id="8af1c-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8af1c-468">In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="8af1c-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="8af1c-469">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8af1c-470">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="8af1c-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="8af1c-471">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8af1c-472">Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="8af1c-473">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af1c-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af1c-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8af1c-475">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="8af1c-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="8af1c-476">Aggiungere una classe `TodoItem` alla cartella *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-477">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8af1c-478">Fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="8af1c-478">Right-click the project.</span></span> <span data-ttu-id="8af1c-479">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8af1c-480">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="8af1c-480">Name the folder *Models*.</span></span>

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="8af1c-482">Fare clic con il pulsante destro del mouse sulla cartella *Models* e selezionare **Aggiungi** > **Nuovo file** > **Generale** > **Classe vuota**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="8af1c-483">Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="8af1c-484">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="8af1c-485">La proprietà `Id` funziona come chiave univoca in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="8af1c-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="8af1c-486">È possibile inserire le classi del modello in qualsiasi punto del progetto, ma per convenzione viene usata la cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="8af1c-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="8af1c-487">Aggiungere un contesto di database</span><span class="sxs-lookup"><span data-stu-id="8af1c-487">Add a database context</span></span>

<span data-ttu-id="8af1c-488">Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati.</span><span class="sxs-lookup"><span data-stu-id="8af1c-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="8af1c-489">Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8af1c-491">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8af1c-492">Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-493">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8af1c-494">Aggiungere una classe `TodoContext` alla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="8af1c-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="8af1c-495">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="8af1c-496">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="8af1c-496">Register the database context</span></span>

<span data-ttu-id="8af1c-497">In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8af1c-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8af1c-498">Il contenitore rende disponibile il servizio ai controller.</span><span class="sxs-lookup"><span data-stu-id="8af1c-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="8af1c-499">Aggiornare *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="8af1c-500">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-500">The preceding code:</span></span>

* <span data-ttu-id="8af1c-501">Rimuove le dichiarazioni `using` inutilizzate.</span><span class="sxs-lookup"><span data-stu-id="8af1c-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="8af1c-502">Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8af1c-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="8af1c-503">Specifica che il contesto del database userà un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="8af1c-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="8af1c-504">Aggiungere un controller</span><span class="sxs-lookup"><span data-stu-id="8af1c-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8af1c-506">Fare clic con il pulsante destro del mouse sulla cartella *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="8af1c-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="8af1c-507">Scegliere **Aggiungi** > **Nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="8af1c-508">Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare il modello **API Controller Class** (Classe controller API).</span><span class="sxs-lookup"><span data-stu-id="8af1c-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="8af1c-509">Assegnare alla classe il nome *TodoController* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Finestra di dialogo Aggiungi nuovo elemento con controller nella casella di ricerca e controller API Web selezionato](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-511">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8af1c-512">Nella cartella *Controllers* creare una classe denominata `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="8af1c-513">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="8af1c-514">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-514">The preceding code:</span></span>

* <span data-ttu-id="8af1c-515">Definisce una classe controller API senza metodi.</span><span class="sxs-lookup"><span data-stu-id="8af1c-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="8af1c-516">Contrassegna la classe con l' [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="8af1c-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="8af1c-517">L'attributo indica che il controller risponde alle richieste di API Web.</span><span class="sxs-lookup"><span data-stu-id="8af1c-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="8af1c-518">Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="8af1c-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="8af1c-519">Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="8af1c-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="8af1c-520">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="8af1c-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="8af1c-521">Aggiunge un elemento denominato `Item1` al database se il database è vuoto.</span><span class="sxs-lookup"><span data-stu-id="8af1c-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="8af1c-522">Questo codice si trova nel costruttore, quindi viene eseguito ogni volta che è presente una nuova richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="8af1c-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="8af1c-523">Se si eliminano tutti gli elementi, il costruttore crea di nuovo `Item1` quando viene nuovamente chiamato un metodo API.</span><span class="sxs-lookup"><span data-stu-id="8af1c-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="8af1c-524">Potrebbe pertanto sembrare che l'eliminazione non abbia funzionato, mentre di fatto ha funzionato.</span><span class="sxs-lookup"><span data-stu-id="8af1c-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="8af1c-525">Aggiungere metodi Get</span><span class="sxs-lookup"><span data-stu-id="8af1c-525">Add Get methods</span></span>

<span data-ttu-id="8af1c-526">Per specificare un'API che recupera elementi attività, aggiungere i metodi seguenti alla classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="8af1c-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="8af1c-527">Questi metodi implementano due metodi GET:</span><span class="sxs-lookup"><span data-stu-id="8af1c-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="8af1c-528">Arrestare l'app se è ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8af1c-528">Stop the app if it's still running.</span></span> <span data-ttu-id="8af1c-529">Quindi eseguirla di nuovo per includere le modifiche più recenti.</span><span class="sxs-lookup"><span data-stu-id="8af1c-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="8af1c-530">Testare l'app chiamando i due endpoint da un browser.</span><span class="sxs-lookup"><span data-stu-id="8af1c-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="8af1c-531">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8af1c-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="8af1c-532">La chiamata a `GetTodoItems` genera la risposta HTTP seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="8af1c-533">Routing e percorsi di URL</span><span class="sxs-lookup"><span data-stu-id="8af1c-533">Routing and URL paths</span></span>

<span data-ttu-id="8af1c-534">L' [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attributo indica un metodo che risponde a una richiesta HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="8af1c-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="8af1c-535">Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="8af1c-536">Iniziare con la stringa di modello nell'attributo `Route` del controller:</span><span class="sxs-lookup"><span data-stu-id="8af1c-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="8af1c-537">Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller".</span><span class="sxs-lookup"><span data-stu-id="8af1c-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="8af1c-538">In questo esempio il nome della classe controller è **Todo**Controller, pertanto il nome del controller è "todo".</span><span class="sxs-lookup"><span data-stu-id="8af1c-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="8af1c-539">Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="8af1c-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="8af1c-540">Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso.</span><span class="sxs-lookup"><span data-stu-id="8af1c-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="8af1c-541">In questo esempio non si usa un modello.</span><span class="sxs-lookup"><span data-stu-id="8af1c-541">This sample doesn't use a template.</span></span> <span data-ttu-id="8af1c-542">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="8af1c-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="8af1c-543">Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="8af1c-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="8af1c-544">Quando viene chiamato `GetTodoItem`, il valore di `"{id}"` viene specificato per il metodo nel rispettivo parametro `id`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="8af1c-545">Valori restituiti</span><span class="sxs-lookup"><span data-stu-id="8af1c-545">Return values</span></span>

<span data-ttu-id="8af1c-546">Il tipo restituito dei `GetTodoItems` metodi e `GetTodoItem` è [ActionResult. \<T> ](xref:web-api/action-return-types#actionresultt-type)</span><span class="sxs-lookup"><span data-stu-id="8af1c-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="8af1c-547">ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="8af1c-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="8af1c-548">Il codice di risposta per questo tipo restituito è 200, presupponendo che non esistano eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="8af1c-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="8af1c-549">Le eccezioni non gestite vengono convertite in errori 5xx.</span><span class="sxs-lookup"><span data-stu-id="8af1c-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="8af1c-550">I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="8af1c-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="8af1c-551">Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:</span><span class="sxs-lookup"><span data-stu-id="8af1c-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="8af1c-552">Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un codice di errore 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="8af1c-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="8af1c-553">In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON.</span><span class="sxs-lookup"><span data-stu-id="8af1c-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="8af1c-554">La restituzione di `item` risulta in una risposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="8af1c-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="8af1c-555">Testare il metodo GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="8af1c-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="8af1c-556">Questa esercitazione usa Postman per testare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="8af1c-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="8af1c-557">Installare il [post](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="8af1c-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="8af1c-558">Avviare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="8af1c-558">Start the web app.</span></span>
* <span data-ttu-id="8af1c-559">Avviare Postman.</span><span class="sxs-lookup"><span data-stu-id="8af1c-559">Start Postman.</span></span>
* <span data-ttu-id="8af1c-560">Disabilitare la **Verifica del certificato SSL**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af1c-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af1c-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8af1c-562">Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).</span><span class="sxs-lookup"><span data-stu-id="8af1c-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8af1c-563">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8af1c-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8af1c-564">Dalle preferenze del **poster**  >  **Preferences** (scheda**generale** ) disabilitare la **Verifica del certificato SSL**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="8af1c-565">In alternativa, selezionare la chiave inglese e selezionare **Settings** (Impostazioni), quindi disabilitare la verifica del certificato SSL.</span><span class="sxs-lookup"><span data-stu-id="8af1c-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="8af1c-566">Riattivare la verifica dei certificati SSL al termine del test del controller.</span><span class="sxs-lookup"><span data-stu-id="8af1c-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="8af1c-567">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="8af1c-567">Create a new request.</span></span>
  * <span data-ttu-id="8af1c-568">Impostare il metodo HTTP su **GET**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="8af1c-569">Impostare l'URL della richiesta su `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="8af1c-570">Ad esempio, `https://localhost:5001/api/todo`</span><span class="sxs-lookup"><span data-stu-id="8af1c-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="8af1c-571">Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.</span><span class="sxs-lookup"><span data-stu-id="8af1c-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="8af1c-572">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="8af1c-572">Select **Send**.</span></span>

![Postman con richiesta GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="8af1c-574">Aggiungere un metodo Create</span><span class="sxs-lookup"><span data-stu-id="8af1c-574">Add a Create method</span></span>

<span data-ttu-id="8af1c-575">Aggiungere il metodo `PostTodoItem` seguente in *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="8af1c-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="8af1c-576">Il codice precedente è un metodo HTTP POST, come indicato dall' [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="8af1c-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="8af1c-577">Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="8af1c-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="8af1c-578">Il metodo `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="8af1c-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="8af1c-579">Restituisce un codice di stato HTTP 201 in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="8af1c-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="8af1c-580">HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="8af1c-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="8af1c-581">Aggiunge un'intestazione `Location` alla risposta.</span><span class="sxs-lookup"><span data-stu-id="8af1c-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="8af1c-582">L'intestazione `Location` specifica l'URI dell'elemento attività appena creato.</span><span class="sxs-lookup"><span data-stu-id="8af1c-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="8af1c-583">Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="8af1c-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="8af1c-584">Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="8af1c-585">La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="8af1c-586">Testare il metodo PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="8af1c-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="8af1c-587">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="8af1c-587">Build the project.</span></span>
* <span data-ttu-id="8af1c-588">In Postman impostare il metodo HTTP su `POST`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="8af1c-589">Selezionare la scheda **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="8af1c-590">Selezionare il pulsante di opzione **raw** (non elaborato).</span><span class="sxs-lookup"><span data-stu-id="8af1c-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="8af1c-591">Impostare il tipo su **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="8af1c-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="8af1c-592">Nel corpo della richiesta immettere codice JSON per un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="8af1c-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="8af1c-593">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="8af1c-593">Select **Send**.</span></span>

  ![Postman con richiesta di creazione](first-web-api/_static/create.png)

  <span data-ttu-id="8af1c-595">Se viene visualizzato un errore HTTP 405 - Metodo non consentito, è probabile che il progetto non sia stato compilato dopo l'aggiunta del metodo `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="8af1c-596">Testare l'URI dell'intestazione della posizione</span><span class="sxs-lookup"><span data-stu-id="8af1c-596">Test the location header URI</span></span>

* <span data-ttu-id="8af1c-597">Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).</span><span class="sxs-lookup"><span data-stu-id="8af1c-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="8af1c-598">Copiare il valore dell'intestazione **Location** (Posizione):</span><span class="sxs-lookup"><span data-stu-id="8af1c-598">Copy the **Location** header value:</span></span>

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="8af1c-600">Impostare il metodo su GET.</span><span class="sxs-lookup"><span data-stu-id="8af1c-600">Set the method to GET.</span></span>
* <span data-ttu-id="8af1c-601">Incollare l'URI (ad esempio, `https://localhost:5001/api/Todo/2` ).</span><span class="sxs-lookup"><span data-stu-id="8af1c-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="8af1c-602">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="8af1c-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="8af1c-603">Aggiungere un metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="8af1c-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="8af1c-604">Aggiungere il metodo `PutTodoItem` seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="8af1c-605">`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="8af1c-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="8af1c-606">La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="8af1c-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="8af1c-607">In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="8af1c-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="8af1c-608">Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="8af1c-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="8af1c-609">Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.</span><span class="sxs-lookup"><span data-stu-id="8af1c-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="8af1c-610">Testare il metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="8af1c-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="8af1c-611">Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="8af1c-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="8af1c-612">Deve esistere un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="8af1c-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="8af1c-613">Chiamare GET per assicurarsi che sia presente un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="8af1c-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="8af1c-614">Aggiornare l'elemento attività con id = 1 e impostarne il nome su "feed fish":</span><span class="sxs-lookup"><span data-stu-id="8af1c-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="8af1c-615">L'immagine seguente visualizza l'aggiornamento di Postman:</span><span class="sxs-lookup"><span data-stu-id="8af1c-615">The following image shows the Postman update:</span></span>

![Console Postman con risposta 204 (No Content)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="8af1c-617">Aggiungere un metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="8af1c-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="8af1c-618">Aggiungere il metodo `DeleteTodoItem` seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="8af1c-619">La `DeleteTodoItem` risposta è [204 (nessun contenuto)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="8af1c-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="8af1c-620">Testare il metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="8af1c-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="8af1c-621">Usare Postman per eliminare un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="8af1c-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="8af1c-622">Impostare il metodo su `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="8af1c-623">Impostare l'URI dell'oggetto da eliminare (ad esempio, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="8af1c-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="8af1c-624">Selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="8af1c-624">Select **Send**.</span></span>

<span data-ttu-id="8af1c-625">L'app di esempio consente di eliminare tutti gli elementi.</span><span class="sxs-lookup"><span data-stu-id="8af1c-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="8af1c-626">Quando viene eliminato l'ultimo elemento, tuttavia, ne viene creato uno nuovo dal costruttore della classe modello alla successiva chiamata dell'API.</span><span class="sxs-lookup"><span data-stu-id="8af1c-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="8af1c-627">Chiamare l'API Web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="8af1c-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="8af1c-628">In questa sezione viene aggiunta una pagina HTML che usa JavaScript per chiamare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="8af1c-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="8af1c-629">jQuery avvia la richiesta.</span><span class="sxs-lookup"><span data-stu-id="8af1c-629">jQuery initiates the request.</span></span> <span data-ttu-id="8af1c-630">JavaScript aggiorna la pagina con i dettagli della risposta dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="8af1c-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="8af1c-631">Configurare l'app per [servire file statici](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [abilitare il mapping del file predefinito](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aggiornando *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="8af1c-632">Creare una cartella *wwwroot* nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="8af1c-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="8af1c-633">Aggiungere un file HTML denominato *index.html* alla directory *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8af1c-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="8af1c-634">Sostituire il contenuto con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="8af1c-635">Aggiungere un file JavaScript con nome *site.js* alla directory *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8af1c-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="8af1c-636">Sostituire il contenuto con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8af1c-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="8af1c-637">Può essere necessario modificare le impostazioni di avvio del progetto ASP.NET Core per il test della pagina HTML in locale:</span><span class="sxs-lookup"><span data-stu-id="8af1c-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="8af1c-638">Aprire *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8af1c-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="8af1c-639">Rimuovere la `launchUrl` proprietà per forzare l'apertura dell'app a *index.html* &mdash; file predefinito del progetto.</span><span class="sxs-lookup"><span data-stu-id="8af1c-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="8af1c-640">In questo esempio vengono chiamati tutti i metodi CRUD dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="8af1c-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="8af1c-641">Di seguito sono incluse le spiegazioni delle chiamate all'API.</span><span class="sxs-lookup"><span data-stu-id="8af1c-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="8af1c-642">Ottenere un elenco di elementi attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-642">Get a list of to-do items</span></span>

<span data-ttu-id="8af1c-643">jQuery invia una richiesta HTTP GET all'API Web, che restituisce JSON che rappresenta una matrice di elementi attività.</span><span class="sxs-lookup"><span data-stu-id="8af1c-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="8af1c-644">La funzione di callback `success` viene chiamata se la richiesta ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="8af1c-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="8af1c-645">Nel callback il modello DOM viene aggiornato con le informazioni dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="8af1c-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="8af1c-646">Aggiungere un elemento attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-646">Add a to-do item</span></span>

<span data-ttu-id="8af1c-647">jQuery invia una richiesta HTTP POST con l'elemento attività nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="8af1c-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="8af1c-648">Le opzioni `accepts` e `contentType` sono impostate su `application/json` per specificare il tipo di supporto ricevuto e inviato.</span><span class="sxs-lookup"><span data-stu-id="8af1c-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="8af1c-649">L'elemento attività viene convertito in JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="8af1c-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="8af1c-650">Quando l'API restituisce un codice di stato di esecuzione riuscita, viene chiamata la funzione `getData` per aggiornare la tabella HTML.</span><span class="sxs-lookup"><span data-stu-id="8af1c-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="8af1c-651">Aggiornare un elemento attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-651">Update a to-do item</span></span>

<span data-ttu-id="8af1c-652">L'aggiornamento di un elemento attività è simile all'aggiunta di un elemento di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="8af1c-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="8af1c-653">L'`url` cambia per includere l'identificatore univoco dell'elemento e `type` è `PUT`.</span><span class="sxs-lookup"><span data-stu-id="8af1c-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="8af1c-654">Eliminare un elemento attività</span><span class="sxs-lookup"><span data-stu-id="8af1c-654">Delete a to-do item</span></span>

<span data-ttu-id="8af1c-655">È possibile eliminare un elemento attività impostando `type` su `DELETE` nella chiamata AJAX e specificando l'identificatore univoco dell'elemento nell'URL.</span><span class="sxs-lookup"><span data-stu-id="8af1c-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="8af1c-656">Aggiungere il supporto per l'autenticazione a un'API Web</span><span class="sxs-lookup"><span data-stu-id="8af1c-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="8af1c-657">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="8af1c-657">Server4.md)]</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8af1c-658">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8af1c-658">Additional resources</span></span>

<span data-ttu-id="8af1c-659">[Visualizzare o scaricare il codice di esempio per questa esercitazione](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="8af1c-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="8af1c-660">Vedere [come scaricare un esempio](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="8af1c-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="8af1c-661">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="8af1c-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="8af1c-662">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="8af1c-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
