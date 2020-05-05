---
title: "Esercitazione: creare un'API Web con ASP.NET Core"
author: rick-anderson
description: Informazioni su come creare un'API Web con ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: ddc14aba14e31c5530cda14b4792736da001246a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767239"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="91494-103">Esercitazione: creare un'API Web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91494-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="91494-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="91494-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="91494-105">Questa esercitazione illustra le nozioni di base della creazione di un'API Web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="91494-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="91494-106">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="91494-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="91494-107">Creare un progetto di API Web.</span><span class="sxs-lookup"><span data-stu-id="91494-107">Create a web API project.</span></span>
> * <span data-ttu-id="91494-108">Aggiungere una classe modello e un contesto di database.</span><span class="sxs-lookup"><span data-stu-id="91494-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="91494-109">Eseguire lo scaffolding di un controller con i metodi CRUD.</span><span class="sxs-lookup"><span data-stu-id="91494-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="91494-110">Configurare il routing, i percorsi URL e i valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="91494-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="91494-111">Chiamare l'API Web con Postman.</span><span class="sxs-lookup"><span data-stu-id="91494-111">Call the web API with Postman.</span></span>

<span data-ttu-id="91494-112">Al termine si avrà un'API Web che può gestire gli elementi di tipo "attività" archiviati in un database.</span><span class="sxs-lookup"><span data-stu-id="91494-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="91494-113">Panoramica</span><span class="sxs-lookup"><span data-stu-id="91494-113">Overview</span></span>

<span data-ttu-id="91494-114">Questa esercitazione consente di creare l'API seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="91494-115">API</span><span class="sxs-lookup"><span data-stu-id="91494-115">API</span></span> | <span data-ttu-id="91494-116">Description</span><span class="sxs-lookup"><span data-stu-id="91494-116">Description</span></span> | <span data-ttu-id="91494-117">Corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="91494-117">Request body</span></span> | <span data-ttu-id="91494-118">Corpo della risposta</span><span class="sxs-lookup"><span data-stu-id="91494-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="91494-119">Ottiene tutti gli elementi attività</span><span class="sxs-lookup"><span data-stu-id="91494-119">Get all to-do items</span></span> | <span data-ttu-id="91494-120">nessuno</span><span class="sxs-lookup"><span data-stu-id="91494-120">None</span></span> | <span data-ttu-id="91494-121">Matrice di elementi attività</span><span class="sxs-lookup"><span data-stu-id="91494-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="91494-122">Ottiene un elemento in base all'ID</span><span class="sxs-lookup"><span data-stu-id="91494-122">Get an item by ID</span></span> | <span data-ttu-id="91494-123">nessuno</span><span class="sxs-lookup"><span data-stu-id="91494-123">None</span></span> | <span data-ttu-id="91494-124">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="91494-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="91494-125">Aggiunge un nuovo elemento</span><span class="sxs-lookup"><span data-stu-id="91494-125">Add a new item</span></span> | <span data-ttu-id="91494-126">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="91494-126">To-do item</span></span> | <span data-ttu-id="91494-127">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="91494-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="91494-128">Aggiorna un elemento esistente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="91494-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="91494-129">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="91494-129">To-do item</span></span> | <span data-ttu-id="91494-130">nessuno</span><span class="sxs-lookup"><span data-stu-id="91494-130">None</span></span> |
|<span data-ttu-id="91494-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="91494-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="91494-132">Elimina un elemento &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="91494-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="91494-133">nessuno</span><span class="sxs-lookup"><span data-stu-id="91494-133">None</span></span> | <span data-ttu-id="91494-134">nessuno</span><span class="sxs-lookup"><span data-stu-id="91494-134">None</span></span>|

<span data-ttu-id="91494-135">Il diagramma seguente visualizza la struttura dell'app.</span><span class="sxs-lookup"><span data-stu-id="91494-135">The following diagram shows the design of the app.</span></span>

![Il client è rappresentato da una casella a sinistra.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="91494-141">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="91494-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="91494-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91494-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91494-144">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="91494-145">Creare un progetto Web</span><span class="sxs-lookup"><span data-stu-id="91494-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91494-147">Scegliere **Nuovo** > **Progetto** dal menu **File**.</span><span class="sxs-lookup"><span data-stu-id="91494-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="91494-148">Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="91494-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="91494-149">Assegnare al progetto il nome *TodoApi* e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="91494-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="91494-150">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.net Core** e **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="91494-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="91494-151">Selezionare il modello **API** e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="91494-151">Select the **API** template and click **Create**.</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="91494-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91494-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91494-154">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="91494-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="91494-155">Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="91494-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="91494-156">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="91494-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="91494-157">Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="91494-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="91494-158">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="91494-158">The preceding commands:</span></span>

  * <span data-ttu-id="91494-159">Crea un nuovo progetto API Web e lo apre in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="91494-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="91494-160">Aggiunge i pacchetti NuGet necessari nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="91494-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91494-161">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91494-162">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="91494-162">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="91494-164">Selezionare **.NET Core** > **App** > **API** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="91494-164">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Finestra di dialogo Nuovo progetto di macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="91494-166">Nella finestra di dialogo **Configura nuova ASP.NET Core API Web** selezionare **Framework di destinazione** di \**.NET Core 3,1*.</span><span class="sxs-lookup"><span data-stu-id="91494-166">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="91494-167">Immettere *TodoApi* in **Nome progetto**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="91494-167">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="91494-169">Aprire un terminale di comando nella cartella di progetto ed eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="91494-169">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="91494-170">Testare l'API</span><span class="sxs-lookup"><span data-stu-id="91494-170">Test the API</span></span>

<span data-ttu-id="91494-171">Il modello di progetto crea un'API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="91494-171">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="91494-172">Chiamare il metodo `Get` da un browser per eseguire il test dell'app.</span><span class="sxs-lookup"><span data-stu-id="91494-172">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91494-174">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="91494-174">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="91494-175">Visual Studio apre un browser e naviga all'indirizzo `https://localhost:<port>/WeatherForecast`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="91494-175">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="91494-176">Se in una finestra di dialogo viene chiesto se considerare attendibile il certificato IIS Express, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="91494-176">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="91494-177">Nella finestra di dialogo **Avviso di sicurezza** successiva selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="91494-177">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91494-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91494-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91494-179">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="91494-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="91494-180">In un browser passare all'URL seguente: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="91494-180">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91494-181">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="91494-182">Selezionare **Esegui** > **Avvia debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="91494-182">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="91494-183">Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="91494-183">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="91494-184">Viene restituito un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="91494-184">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="91494-185">Accodare `/WeatherForecast` all'URL (impostare l'URL su `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="91494-185">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="91494-186">Viene restituito un codice JSON simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-186">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="91494-187">Aggiungere una classe del modello</span><span class="sxs-lookup"><span data-stu-id="91494-187">Add a model class</span></span>

<span data-ttu-id="91494-188">Un *modello* è un set di classi che rappresentano i dati gestiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="91494-188">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="91494-189">Il modello per questa app è una classe `TodoItem` singola.</span><span class="sxs-lookup"><span data-stu-id="91494-189">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91494-191">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="91494-191">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="91494-192">Selezionare **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="91494-192">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="91494-193">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="91494-193">Name the folder *Models*.</span></span>

* <span data-ttu-id="91494-194">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="91494-194">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="91494-195">Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="91494-195">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="91494-196">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-196">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91494-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91494-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91494-198">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="91494-198">Add a folder named *Models*.</span></span>

* <span data-ttu-id="91494-199">Aggiungere una classe `TodoItem` alla cartella *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-199">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91494-200">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91494-201">Fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="91494-201">Right-click the project.</span></span> <span data-ttu-id="91494-202">Selezionare **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="91494-202">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="91494-203">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="91494-203">Name the folder *Models*.</span></span>

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="91494-205">Fare clic con il pulsante destro del mouse sulla cartella *Models* e selezionare **Aggiungi** > **Nuovo file** > **Generale** > **Classe vuota**.</span><span class="sxs-lookup"><span data-stu-id="91494-205">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="91494-206">Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="91494-206">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="91494-207">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-207">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="91494-208">La proprietà `Id` funziona come chiave univoca in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="91494-208">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="91494-209">È possibile inserire le classi del modello in qualsiasi punto del progetto, ma per convenzione viene usata la cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="91494-209">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="91494-210">Aggiungere un contesto di database</span><span class="sxs-lookup"><span data-stu-id="91494-210">Add a database context</span></span>

<span data-ttu-id="91494-211">Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati.</span><span class="sxs-lookup"><span data-stu-id="91494-211">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="91494-212">Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="91494-212">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-213">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="91494-214">Aggiungere Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="91494-214">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="91494-215">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione**.</span><span class="sxs-lookup"><span data-stu-id="91494-215">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="91494-216">Selezionare la scheda **Esplora** e quindi immettere **Microsoft.EntityFrameworkCore.SqlServer** nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="91494-216">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="91494-217">Nel riquadro sinistro selezionare **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="91494-217">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="91494-218">Selezionare la casella di controllo **Progetto** nel riquadro di destra e quindi selezionare **Installa**.</span><span class="sxs-lookup"><span data-stu-id="91494-218">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="91494-219">Usare le istruzioni precedenti per aggiungere il pacchetto NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="91494-219">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gestione pacchetti NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="91494-221">Aggiungere il contesto del database TodoContext</span><span class="sxs-lookup"><span data-stu-id="91494-221">Add the TodoContext database context</span></span>

* <span data-ttu-id="91494-222">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="91494-222">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="91494-223">Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="91494-223">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="91494-224">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="91494-225">Aggiungere una classe `TodoContext` alla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="91494-225">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="91494-226">Immettere il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-226">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="91494-227">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="91494-227">Register the database context</span></span>

<span data-ttu-id="91494-228">In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="91494-228">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="91494-229">Il contenitore rende disponibile il servizio ai controller.</span><span class="sxs-lookup"><span data-stu-id="91494-229">The container provides the service to controllers.</span></span>

<span data-ttu-id="91494-230">Aggiornare *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-230">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="91494-231">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="91494-231">The preceding code:</span></span>

* <span data-ttu-id="91494-232">Rimuove le dichiarazioni `using` inutilizzate.</span><span class="sxs-lookup"><span data-stu-id="91494-232">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="91494-233">Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="91494-233">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="91494-234">Specifica che il contesto del database userà un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="91494-234">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="91494-235">Eseguire lo scaffolding di un controller</span><span class="sxs-lookup"><span data-stu-id="91494-235">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-236">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91494-237">Fare clic con il pulsante destro del mouse sulla cartella *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="91494-237">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="91494-238">Selezionare **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="91494-238">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="91494-239">Selezionare **Controller API con azioni, che usa Entity Framework** e quindi selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="91494-239">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="91494-240">Nella finestra di dialogo **Add API Controller with actions, using Entity Framework** (Aggiungi controller API con azioni, che usa Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="91494-240">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="91494-241">Selezionare **TodoItem (TodoApi. Models)** nella **classe del modello**.</span><span class="sxs-lookup"><span data-stu-id="91494-241">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="91494-242">Selezionare **TodoContext (TodoApi. Models)** nella **classe del contesto dati**.</span><span class="sxs-lookup"><span data-stu-id="91494-242">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="91494-243">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="91494-243">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="91494-244">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-244">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="91494-245">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="91494-245">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="91494-246">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="91494-246">The preceding commands:</span></span>

* <span data-ttu-id="91494-247">Aggiungere i pacchetti NuGet necessari per lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="91494-247">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="91494-248">Installa il motore di scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="91494-248">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="91494-249">Esegue lo scaffolding di `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="91494-249">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="91494-250">Il codice generato:</span><span class="sxs-lookup"><span data-stu-id="91494-250">The generated code:</span></span>

* <span data-ttu-id="91494-251">Contrassegna la classe con l' [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="91494-251">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="91494-252">L'attributo indica che il controller risponde alle richieste di API Web.</span><span class="sxs-lookup"><span data-stu-id="91494-252">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="91494-253">Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="91494-253">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="91494-254">Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="91494-254">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="91494-255">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="91494-255">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="91494-256">I modelli di ASP.NET Core per:</span><span class="sxs-lookup"><span data-stu-id="91494-256">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="91494-257">I controller con le `[action]` visualizzazioni includono nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="91494-257">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="91494-258">I controller API non `[action]` includono nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="91494-258">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="91494-259">Quando il `[action]` token non è incluso nel modello di route, il nome dell' [azione](xref:mvc/controllers/routing#action) viene escluso dalla route.</span><span class="sxs-lookup"><span data-stu-id="91494-259">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="91494-260">Ovvero, il nome del metodo associato all'azione non viene usato nella route corrispondente.</span><span class="sxs-lookup"><span data-stu-id="91494-260">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="91494-261">Esaminare il metodo di creazione PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="91494-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="91494-262">Sostituire l'istruzione return in `PostTodoItem` per usare l'operatore [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="91494-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="91494-263">Il codice precedente è un metodo HTTP POST, come indicato dall' [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="91494-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="91494-264">Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="91494-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="91494-265">Il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="91494-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="91494-266">Restituisce un codice di stato HTTP 201 in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="91494-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="91494-267">HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="91494-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="91494-268">Aggiunge un'intestazione [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) alla risposta.</span><span class="sxs-lookup"><span data-stu-id="91494-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="91494-269">L'intestazione `Location` specifica l'[URI](https://developer.mozilla.org/docs/Glossary/URI) dell'elemento attività appena creato.</span><span class="sxs-lookup"><span data-stu-id="91494-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="91494-270">Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="91494-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="91494-271">Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`.</span><span class="sxs-lookup"><span data-stu-id="91494-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="91494-272">La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="91494-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="91494-273">Installare Postman</span><span class="sxs-lookup"><span data-stu-id="91494-273">Install Postman</span></span>

<span data-ttu-id="91494-274">Questa esercitazione usa Postman per testare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="91494-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="91494-275">Installa il [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="91494-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="91494-276">Avviare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="91494-276">Start the web app.</span></span>
* <span data-ttu-id="91494-277">Avviare Postman.</span><span class="sxs-lookup"><span data-stu-id="91494-277">Start Postman.</span></span>
* <span data-ttu-id="91494-278">Disattivare **SSL certificate verification** (Verifica certificato SSL)</span><span class="sxs-lookup"><span data-stu-id="91494-278">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="91494-279">Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).</span><span class="sxs-lookup"><span data-stu-id="91494-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="91494-280">Riattivare la verifica dei certificati SSL al termine del test del controller.</span><span class="sxs-lookup"><span data-stu-id="91494-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="91494-281">Testare PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="91494-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="91494-282">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="91494-282">Create a new request.</span></span>
* <span data-ttu-id="91494-283">Impostare il metodo HTTP su `POST`.</span><span class="sxs-lookup"><span data-stu-id="91494-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="91494-284">Selezionare la scheda **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="91494-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="91494-285">Selezionare il pulsante di opzione **raw** (non elaborato).</span><span class="sxs-lookup"><span data-stu-id="91494-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="91494-286">Impostare il tipo su **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="91494-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="91494-287">Nel corpo della richiesta immettere codice JSON per un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="91494-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="91494-288">Selezionare **Invia**.</span><span class="sxs-lookup"><span data-stu-id="91494-288">Select **Send**.</span></span>

  ![Postman con richiesta di creazione](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="91494-290">Testare l'URI dell'intestazione della posizione</span><span class="sxs-lookup"><span data-stu-id="91494-290">Test the location header URI</span></span>

* <span data-ttu-id="91494-291">Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).</span><span class="sxs-lookup"><span data-stu-id="91494-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="91494-292">Copiare il valore dell'intestazione **Location** (Posizione):</span><span class="sxs-lookup"><span data-stu-id="91494-292">Copy the **Location** header value:</span></span>

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="91494-294">Impostare il metodo su GET.</span><span class="sxs-lookup"><span data-stu-id="91494-294">Set the method to GET.</span></span>
* <span data-ttu-id="91494-295">Incollare l'URI (ad esempio, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="91494-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="91494-296">Selezionare **Invia**.</span><span class="sxs-lookup"><span data-stu-id="91494-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="91494-297">Esaminare i metodi GET</span><span class="sxs-lookup"><span data-stu-id="91494-297">Examine the GET methods</span></span>

<span data-ttu-id="91494-298">Questi metodi implementano due metodi GET:</span><span class="sxs-lookup"><span data-stu-id="91494-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="91494-299">Testare l'app chiamando i due endpoint da un browser o da Postman.</span><span class="sxs-lookup"><span data-stu-id="91494-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="91494-300">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="91494-300">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="91494-301">Una risposta simile alla seguente viene generata dalla chiamata a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="91494-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="91494-302">Testare Get con Postman</span><span class="sxs-lookup"><span data-stu-id="91494-302">Test Get with Postman</span></span>

* <span data-ttu-id="91494-303">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="91494-303">Create a new request.</span></span>
* <span data-ttu-id="91494-304">Impostare il metodo HTTP su **GET**.</span><span class="sxs-lookup"><span data-stu-id="91494-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="91494-305">Impostare l'URL della richiesta su `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="91494-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="91494-306">Ad esempio: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="91494-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="91494-307">Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.</span><span class="sxs-lookup"><span data-stu-id="91494-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="91494-308">Selezionare **Invia**.</span><span class="sxs-lookup"><span data-stu-id="91494-308">Select **Send**.</span></span>

<span data-ttu-id="91494-309">Questa app usa un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="91494-309">This app uses an in-memory database.</span></span> <span data-ttu-id="91494-310">Se l'app viene arrestata e avviata, la richiesta GET precedente non restituirà alcun dato.</span><span class="sxs-lookup"><span data-stu-id="91494-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="91494-311">Se non vengono restituiti dati, eseguire [POST](#post) per pubblicare i dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="91494-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="91494-312">Routing e percorsi di URL</span><span class="sxs-lookup"><span data-stu-id="91494-312">Routing and URL paths</span></span>

<span data-ttu-id="91494-313">L' [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attributo indica un metodo che risponde a una richiesta HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="91494-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="91494-314">Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="91494-315">Iniziare con la stringa di modello nell'attributo `Route` del controller:</span><span class="sxs-lookup"><span data-stu-id="91494-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="91494-316">Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller".</span><span class="sxs-lookup"><span data-stu-id="91494-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="91494-317">In questo esempio il nome della classe controller è **TodoItems**Controller, quindi il nome del controller è "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="91494-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="91494-318">Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="91494-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="91494-319">Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso.</span><span class="sxs-lookup"><span data-stu-id="91494-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="91494-320">In questo esempio non si usa un modello.</span><span class="sxs-lookup"><span data-stu-id="91494-320">This sample doesn't use a template.</span></span> <span data-ttu-id="91494-321">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="91494-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="91494-322">Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="91494-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="91494-323">Quando `GetTodoItem` viene richiamato, il valore `"{id}"` di nell'URL viene fornito al metodo nel `id` parametro.</span><span class="sxs-lookup"><span data-stu-id="91494-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="91494-324">Valori restituiti</span><span class="sxs-lookup"><span data-stu-id="91494-324">Return values</span></span>

<span data-ttu-id="91494-325">Il tipo restituito dei metodi `GetTodoItems` e `GetTodoItem` è [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="91494-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="91494-326">ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="91494-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="91494-327">Il codice di risposta per questo tipo restituito è 200, presupponendo che non esistano eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="91494-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="91494-328">Le eccezioni non gestite vengono convertite in errori 5xx.</span><span class="sxs-lookup"><span data-stu-id="91494-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="91494-329">I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="91494-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="91494-330">Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:</span><span class="sxs-lookup"><span data-stu-id="91494-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="91494-331">Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un codice di errore 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="91494-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="91494-332">In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON.</span><span class="sxs-lookup"><span data-stu-id="91494-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="91494-333">La restituzione di `item` risulta in una risposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="91494-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="91494-334">Metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="91494-334">The PutTodoItem method</span></span>

<span data-ttu-id="91494-335">Esaminare il metodo `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="91494-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="91494-336">`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="91494-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="91494-337">La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="91494-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="91494-338">In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="91494-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="91494-339">Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="91494-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="91494-340">Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.</span><span class="sxs-lookup"><span data-stu-id="91494-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="91494-341">Testare il metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="91494-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="91494-342">Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="91494-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="91494-343">Deve esistere un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="91494-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="91494-344">Chiamare GET per assicurarsi che sia presente un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="91494-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="91494-345">Aggiornare l'elemento attività con ID = 1 e impostarne il nome su "feed fish":</span><span class="sxs-lookup"><span data-stu-id="91494-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="91494-346">L'immagine seguente visualizza l'aggiornamento di Postman:</span><span class="sxs-lookup"><span data-stu-id="91494-346">The following image shows the Postman update:</span></span>

![Console Postman con risposta 204 (No Content)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="91494-348">Metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="91494-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="91494-349">Esaminare il metodo `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="91494-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="91494-350">Testare il metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="91494-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="91494-351">Usare Postman per eliminare un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="91494-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="91494-352">Impostare il metodo su `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="91494-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="91494-353">Impostare l'URI dell'oggetto da eliminare (ad esempio `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="91494-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="91494-354">Selezionare **Invia**.</span><span class="sxs-lookup"><span data-stu-id="91494-354">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="91494-355">Impedisci overposting</span><span class="sxs-lookup"><span data-stu-id="91494-355">Prevent over-posting</span></span>

<span data-ttu-id="91494-356">Attualmente l'app di esempio espone l' `TodoItem` intero oggetto.</span><span class="sxs-lookup"><span data-stu-id="91494-356">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="91494-357">Le app Productions limitano in genere i dati di input e restituiti usando un subset del modello.</span><span class="sxs-lookup"><span data-stu-id="91494-357">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="91494-358">Esistono diversi motivi alla base di questo e la sicurezza è una delle principali.</span><span class="sxs-lookup"><span data-stu-id="91494-358">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="91494-359">Il subset di un modello è in genere indicato come oggetto Trasferimento dati (DTO), modello di input o modello di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="91494-359">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="91494-360">**Dto** viene usato in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="91494-360">**DTO** is used in this article.</span></span>

<span data-ttu-id="91494-361">Un DTO può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="91494-361">A DTO may be used to:</span></span>

* <span data-ttu-id="91494-362">Impedisci l'overposting.</span><span class="sxs-lookup"><span data-stu-id="91494-362">Prevent over-posting.</span></span>
* <span data-ttu-id="91494-363">Nascondere le proprietà che i client non dovrebbero visualizzare.</span><span class="sxs-lookup"><span data-stu-id="91494-363">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="91494-364">Omettere alcune proprietà per ridurre le dimensioni del payload.</span><span class="sxs-lookup"><span data-stu-id="91494-364">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="91494-365">Rendere flat gli oggetti grafici che contengono oggetti annidati.</span><span class="sxs-lookup"><span data-stu-id="91494-365">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="91494-366">I grafici a oggetti flat possono essere più pratici per i client.</span><span class="sxs-lookup"><span data-stu-id="91494-366">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="91494-367">Per illustrare l'approccio DTO, aggiornare `TodoItem` la classe in modo da includere un campo Secret:</span><span class="sxs-lookup"><span data-stu-id="91494-367">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="91494-368">Il campo segreto deve essere nascosto da questa app, ma un'app amministrativa può scegliere di esporla.</span><span class="sxs-lookup"><span data-stu-id="91494-368">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="91494-369">Verificare che sia possibile pubblicare e ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="91494-369">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="91494-370">Creare un modello DTO:</span><span class="sxs-lookup"><span data-stu-id="91494-370">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="91494-371">Aggiornare `TodoItemsController` da usare `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="91494-371">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="91494-372">Verificare che non sia possibile inserire o ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="91494-372">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="91494-373">Chiamare l'API Web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="91494-373">Call the web API with JavaScript</span></span>

<span data-ttu-id="91494-374">Vedere [esercitazione: chiamare un'API web ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="91494-374">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="91494-375">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="91494-375">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="91494-376">Creare un progetto di API Web.</span><span class="sxs-lookup"><span data-stu-id="91494-376">Create a web API project.</span></span>
> * <span data-ttu-id="91494-377">Aggiungere una classe modello e un contesto di database.</span><span class="sxs-lookup"><span data-stu-id="91494-377">Add a model class and a database context.</span></span>
> * <span data-ttu-id="91494-378">Aggiungere un controller.</span><span class="sxs-lookup"><span data-stu-id="91494-378">Add a controller.</span></span>
> * <span data-ttu-id="91494-379">Aggiungere metodi CRUD.</span><span class="sxs-lookup"><span data-stu-id="91494-379">Add CRUD methods.</span></span>
> * <span data-ttu-id="91494-380">Configurare routing e percorsi URL.</span><span class="sxs-lookup"><span data-stu-id="91494-380">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="91494-381">Specificare valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="91494-381">Specify return values.</span></span>
> * <span data-ttu-id="91494-382">Chiamare l'API Web con Postman.</span><span class="sxs-lookup"><span data-stu-id="91494-382">Call the web API with Postman.</span></span>
> * <span data-ttu-id="91494-383">Chiamare l'API Web con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="91494-383">Call the web API with JavaScript.</span></span>

<span data-ttu-id="91494-384">Al termine si dispone di un'API web che può gestire gli elementi di tipo "attività" archiviati in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="91494-384">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="91494-385">Panoramica</span><span class="sxs-lookup"><span data-stu-id="91494-385">Overview</span></span>

<span data-ttu-id="91494-386">Questa esercitazione consente di creare l'API seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-386">This tutorial creates the following API:</span></span>

|<span data-ttu-id="91494-387">API</span><span class="sxs-lookup"><span data-stu-id="91494-387">API</span></span> | <span data-ttu-id="91494-388">Description</span><span class="sxs-lookup"><span data-stu-id="91494-388">Description</span></span> | <span data-ttu-id="91494-389">Corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="91494-389">Request body</span></span> | <span data-ttu-id="91494-390">Corpo della risposta</span><span class="sxs-lookup"><span data-stu-id="91494-390">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="91494-391">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="91494-391">GET /api/TodoItems</span></span> | <span data-ttu-id="91494-392">Ottiene tutti gli elementi attività</span><span class="sxs-lookup"><span data-stu-id="91494-392">Get all to-do items</span></span> | <span data-ttu-id="91494-393">nessuno</span><span class="sxs-lookup"><span data-stu-id="91494-393">None</span></span> | <span data-ttu-id="91494-394">Matrice di elementi attività</span><span class="sxs-lookup"><span data-stu-id="91494-394">Array of to-do items</span></span>|
|<span data-ttu-id="91494-395">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="91494-395">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="91494-396">Ottiene un elemento in base all'ID</span><span class="sxs-lookup"><span data-stu-id="91494-396">Get an item by ID</span></span> | <span data-ttu-id="91494-397">nessuno</span><span class="sxs-lookup"><span data-stu-id="91494-397">None</span></span> | <span data-ttu-id="91494-398">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="91494-398">To-do item</span></span>|
|<span data-ttu-id="91494-399">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="91494-399">POST /api/TodoItems</span></span> | <span data-ttu-id="91494-400">Aggiunge un nuovo elemento</span><span class="sxs-lookup"><span data-stu-id="91494-400">Add a new item</span></span> | <span data-ttu-id="91494-401">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="91494-401">To-do item</span></span> | <span data-ttu-id="91494-402">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="91494-402">To-do item</span></span> |
|<span data-ttu-id="91494-403">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="91494-403">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="91494-404">Aggiorna un elemento esistente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="91494-404">Update an existing item &nbsp;</span></span> | <span data-ttu-id="91494-405">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="91494-405">To-do item</span></span> | <span data-ttu-id="91494-406">nessuno</span><span class="sxs-lookup"><span data-stu-id="91494-406">None</span></span> |
|<span data-ttu-id="91494-407">Elimina/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="91494-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="91494-408">Elimina un elemento &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="91494-408">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="91494-409">nessuno</span><span class="sxs-lookup"><span data-stu-id="91494-409">None</span></span> | <span data-ttu-id="91494-410">nessuno</span><span class="sxs-lookup"><span data-stu-id="91494-410">None</span></span>|

<span data-ttu-id="91494-411">Il diagramma seguente visualizza la struttura dell'app.</span><span class="sxs-lookup"><span data-stu-id="91494-411">The following diagram shows the design of the app.</span></span>

![Il client è rappresentato da una casella a sinistra.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="91494-417">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="91494-417">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-418">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="91494-419">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91494-419">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91494-420">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="91494-421">Creare un progetto Web</span><span class="sxs-lookup"><span data-stu-id="91494-421">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91494-423">Scegliere **Nuovo** > **Progetto** dal menu **File**.</span><span class="sxs-lookup"><span data-stu-id="91494-423">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="91494-424">Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="91494-424">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="91494-425">Assegnare al progetto il nome *TodoApi* e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="91494-425">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="91494-426">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.NET Core** e **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="91494-426">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="91494-427">Selezionare il modello **API** e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="91494-427">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="91494-428">**Non** selezionare **Abilita supporto Docker**.</span><span class="sxs-lookup"><span data-stu-id="91494-428">**Don't** select **Enable Docker Support**.</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="91494-430">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91494-430">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91494-431">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="91494-431">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="91494-432">Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="91494-432">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="91494-433">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="91494-433">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="91494-434">Questi comandi creano un nuovo progetto API Web e aprono una nuova istanza di Visual Studio Code nella nuova cartella di progetto.</span><span class="sxs-lookup"><span data-stu-id="91494-434">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="91494-435">Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="91494-435">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91494-436">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91494-437">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="91494-437">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="91494-439">Selezionare **.NET Core** > **App** > **API** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="91494-439">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Finestra di dialogo Nuovo progetto di macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="91494-441">Nella finestra di dialogo **Configure your new ASP.NET Core Web API** (Configura la nuova API Web ASP.NET Core), accettare l'impostazione predefinita di **Framework di destinazione**, ovvero \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="91494-441">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="91494-442">Immettere *TodoApi* in **Nome progetto**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="91494-442">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="91494-444">Testare l'API</span><span class="sxs-lookup"><span data-stu-id="91494-444">Test the API</span></span>

<span data-ttu-id="91494-445">Il modello di progetto crea un'API `values`.</span><span class="sxs-lookup"><span data-stu-id="91494-445">The project template creates a `values` API.</span></span> <span data-ttu-id="91494-446">Chiamare il metodo `Get` da un browser per eseguire il test dell'app.</span><span class="sxs-lookup"><span data-stu-id="91494-446">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-447">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91494-448">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="91494-448">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="91494-449">Visual Studio apre un browser e naviga all'indirizzo `https://localhost:<port>/api/values`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="91494-449">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="91494-450">Se in una finestra di dialogo viene chiesto se considerare attendibile il certificato IIS Express, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="91494-450">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="91494-451">Nella finestra di dialogo **Avviso di sicurezza** successiva selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="91494-451">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91494-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91494-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91494-453">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="91494-453">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="91494-454">In un browser passare all'URL seguente: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="91494-454">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91494-455">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="91494-456">Selezionare **Esegui** > **Avvia debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="91494-456">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="91494-457">Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="91494-457">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="91494-458">Viene restituito un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="91494-458">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="91494-459">Accodare `/api/values` all'URL (impostare l'URL su `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="91494-459">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="91494-460">Viene restituito il codice JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-460">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="91494-461">Aggiungere una classe del modello</span><span class="sxs-lookup"><span data-stu-id="91494-461">Add a model class</span></span>

<span data-ttu-id="91494-462">Un *modello* è un set di classi che rappresentano i dati gestiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="91494-462">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="91494-463">Il modello per questa app è una classe `TodoItem` singola.</span><span class="sxs-lookup"><span data-stu-id="91494-463">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-464">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91494-465">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="91494-465">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="91494-466">Selezionare **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="91494-466">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="91494-467">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="91494-467">Name the folder *Models*.</span></span>

* <span data-ttu-id="91494-468">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="91494-468">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="91494-469">Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="91494-469">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="91494-470">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-470">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91494-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91494-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91494-472">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="91494-472">Add a folder named *Models*.</span></span>

* <span data-ttu-id="91494-473">Aggiungere una classe `TodoItem` alla cartella *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-473">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91494-474">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91494-475">Fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="91494-475">Right-click the project.</span></span> <span data-ttu-id="91494-476">Selezionare **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="91494-476">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="91494-477">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="91494-477">Name the folder *Models*.</span></span>

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="91494-479">Fare clic con il pulsante destro del mouse sulla cartella *Models* e selezionare **Aggiungi** > **Nuovo file** > **Generale** > **Classe vuota**.</span><span class="sxs-lookup"><span data-stu-id="91494-479">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="91494-480">Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="91494-480">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="91494-481">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-481">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="91494-482">La proprietà `Id` funziona come chiave univoca in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="91494-482">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="91494-483">È possibile inserire le classi del modello in qualsiasi punto del progetto, ma per convenzione viene usata la cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="91494-483">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="91494-484">Aggiungere un contesto di database</span><span class="sxs-lookup"><span data-stu-id="91494-484">Add a database context</span></span>

<span data-ttu-id="91494-485">Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati.</span><span class="sxs-lookup"><span data-stu-id="91494-485">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="91494-486">Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="91494-486">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91494-488">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="91494-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="91494-489">Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="91494-489">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="91494-490">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="91494-491">Aggiungere una classe `TodoContext` alla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="91494-491">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="91494-492">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="91494-493">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="91494-493">Register the database context</span></span>

<span data-ttu-id="91494-494">In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="91494-494">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="91494-495">Il contenitore rende disponibile il servizio ai controller.</span><span class="sxs-lookup"><span data-stu-id="91494-495">The container provides the service to controllers.</span></span>

<span data-ttu-id="91494-496">Aggiornare *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-496">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="91494-497">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="91494-497">The preceding code:</span></span>

* <span data-ttu-id="91494-498">Rimuove le dichiarazioni `using` inutilizzate.</span><span class="sxs-lookup"><span data-stu-id="91494-498">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="91494-499">Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="91494-499">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="91494-500">Specifica che il contesto del database userà un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="91494-500">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="91494-501">Aggiungere un controller</span><span class="sxs-lookup"><span data-stu-id="91494-501">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-502">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-502">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91494-503">Fare clic con il pulsante destro del mouse sulla cartella *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="91494-503">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="91494-504">Scegliere **Aggiungi** > **Nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="91494-504">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="91494-505">Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare il modello **API Controller Class** (Classe controller API).</span><span class="sxs-lookup"><span data-stu-id="91494-505">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="91494-506">Assegnare alla classe il nome *TodoController* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="91494-506">Name the class *TodoController*, and select **Add**.</span></span>

  ![Finestra di dialogo Aggiungi nuovo elemento con controller nella casella di ricerca e controller API Web selezionato](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="91494-508">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-508">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="91494-509">Nella cartella *Controllers* creare una classe denominata `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="91494-509">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="91494-510">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-510">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="91494-511">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="91494-511">The preceding code:</span></span>

* <span data-ttu-id="91494-512">Definisce una classe controller API senza metodi.</span><span class="sxs-lookup"><span data-stu-id="91494-512">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="91494-513">Contrassegna la classe con l' [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="91494-513">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="91494-514">L'attributo indica che il controller risponde alle richieste di API Web.</span><span class="sxs-lookup"><span data-stu-id="91494-514">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="91494-515">Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="91494-515">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="91494-516">Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="91494-516">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="91494-517">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="91494-517">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="91494-518">Aggiunge un elemento denominato `Item1` al database se il database è vuoto.</span><span class="sxs-lookup"><span data-stu-id="91494-518">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="91494-519">Questo codice si trova nel costruttore, quindi viene eseguito ogni volta che è presente una nuova richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="91494-519">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="91494-520">Se si eliminano tutti gli elementi, il costruttore crea di nuovo `Item1` quando viene nuovamente chiamato un metodo API.</span><span class="sxs-lookup"><span data-stu-id="91494-520">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="91494-521">Potrebbe pertanto sembrare che l'eliminazione non abbia funzionato, mentre di fatto ha funzionato.</span><span class="sxs-lookup"><span data-stu-id="91494-521">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="91494-522">Aggiungere metodi Get</span><span class="sxs-lookup"><span data-stu-id="91494-522">Add Get methods</span></span>

<span data-ttu-id="91494-523">Per specificare un'API che recupera elementi attività, aggiungere i metodi seguenti alla classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="91494-523">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="91494-524">Questi metodi implementano due metodi GET:</span><span class="sxs-lookup"><span data-stu-id="91494-524">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="91494-525">Arrestare l'app se è ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="91494-525">Stop the app if it's still running.</span></span> <span data-ttu-id="91494-526">Quindi eseguirla di nuovo per includere le modifiche più recenti.</span><span class="sxs-lookup"><span data-stu-id="91494-526">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="91494-527">Testare l'app chiamando i due endpoint da un browser.</span><span class="sxs-lookup"><span data-stu-id="91494-527">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="91494-528">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="91494-528">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="91494-529">La chiamata a `GetTodoItems` genera la risposta HTTP seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-529">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="91494-530">Routing e percorsi di URL</span><span class="sxs-lookup"><span data-stu-id="91494-530">Routing and URL paths</span></span>

<span data-ttu-id="91494-531">L' [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attributo indica un metodo che risponde a una richiesta HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="91494-531">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="91494-532">Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-532">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="91494-533">Iniziare con la stringa di modello nell'attributo `Route` del controller:</span><span class="sxs-lookup"><span data-stu-id="91494-533">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="91494-534">Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller".</span><span class="sxs-lookup"><span data-stu-id="91494-534">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="91494-535">In questo esempio il nome della classe controller è **Todo**Controller, pertanto il nome del controller è "todo".</span><span class="sxs-lookup"><span data-stu-id="91494-535">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="91494-536">Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="91494-536">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="91494-537">Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso.</span><span class="sxs-lookup"><span data-stu-id="91494-537">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="91494-538">In questo esempio non si usa un modello.</span><span class="sxs-lookup"><span data-stu-id="91494-538">This sample doesn't use a template.</span></span> <span data-ttu-id="91494-539">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="91494-539">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="91494-540">Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="91494-540">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="91494-541">Quando viene chiamato `GetTodoItem`, il valore di `"{id}"` viene specificato per il metodo nel rispettivo parametro `id`.</span><span class="sxs-lookup"><span data-stu-id="91494-541">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="91494-542">Valori restituiti</span><span class="sxs-lookup"><span data-stu-id="91494-542">Return values</span></span>

<span data-ttu-id="91494-543">Il tipo restituito dei metodi `GetTodoItems` e `GetTodoItem` è [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="91494-543">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="91494-544">ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="91494-544">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="91494-545">Il codice di risposta per questo tipo restituito è 200, presupponendo che non esistano eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="91494-545">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="91494-546">Le eccezioni non gestite vengono convertite in errori 5xx.</span><span class="sxs-lookup"><span data-stu-id="91494-546">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="91494-547">I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="91494-547">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="91494-548">Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:</span><span class="sxs-lookup"><span data-stu-id="91494-548">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="91494-549">Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un codice di errore 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="91494-549">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="91494-550">In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON.</span><span class="sxs-lookup"><span data-stu-id="91494-550">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="91494-551">La restituzione di `item` risulta in una risposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="91494-551">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="91494-552">Testare il metodo GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="91494-552">Test the GetTodoItems method</span></span>

<span data-ttu-id="91494-553">Questa esercitazione usa Postman per testare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="91494-553">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="91494-554">Installare il [post](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="91494-554">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="91494-555">Avviare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="91494-555">Start the web app.</span></span>
* <span data-ttu-id="91494-556">Avviare Postman.</span><span class="sxs-lookup"><span data-stu-id="91494-556">Start Postman.</span></span>
* <span data-ttu-id="91494-557">Disabilitare la **Verifica del certificato SSL**.</span><span class="sxs-lookup"><span data-stu-id="91494-557">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91494-558">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91494-558">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91494-559">Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).</span><span class="sxs-lookup"><span data-stu-id="91494-559">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="91494-560">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="91494-560">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="91494-561">Dalle**Preferenze** del **poster** > (scheda**generale** ) disabilitare la **Verifica del certificato SSL**.</span><span class="sxs-lookup"><span data-stu-id="91494-561">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="91494-562">In alternativa, selezionare la chiave inglese e selezionare **Settings** (Impostazioni), quindi disabilitare la verifica del certificato SSL.</span><span class="sxs-lookup"><span data-stu-id="91494-562">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="91494-563">Riattivare la verifica dei certificati SSL al termine del test del controller.</span><span class="sxs-lookup"><span data-stu-id="91494-563">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="91494-564">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="91494-564">Create a new request.</span></span>
  * <span data-ttu-id="91494-565">Impostare il metodo HTTP su **GET**.</span><span class="sxs-lookup"><span data-stu-id="91494-565">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="91494-566">Impostare l'URL della richiesta su `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="91494-566">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="91494-567">Ad esempio: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="91494-567">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="91494-568">Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.</span><span class="sxs-lookup"><span data-stu-id="91494-568">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="91494-569">Selezionare **Invia**.</span><span class="sxs-lookup"><span data-stu-id="91494-569">Select **Send**.</span></span>

![Postman con richiesta GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="91494-571">Aggiungere un metodo Create</span><span class="sxs-lookup"><span data-stu-id="91494-571">Add a Create method</span></span>

<span data-ttu-id="91494-572">Aggiungere il metodo `PostTodoItem` seguente in *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="91494-572">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="91494-573">Il codice precedente è un metodo HTTP POST, come indicato dall' [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="91494-573">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="91494-574">Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="91494-574">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="91494-575">Il metodo `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="91494-575">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="91494-576">Restituisce un codice di stato HTTP 201 in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="91494-576">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="91494-577">HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="91494-577">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="91494-578">Aggiunge un'intestazione `Location` alla risposta.</span><span class="sxs-lookup"><span data-stu-id="91494-578">Adds a `Location` header to the response.</span></span> <span data-ttu-id="91494-579">L'intestazione `Location` specifica l'URI dell'elemento attività appena creato.</span><span class="sxs-lookup"><span data-stu-id="91494-579">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="91494-580">Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="91494-580">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="91494-581">Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`.</span><span class="sxs-lookup"><span data-stu-id="91494-581">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="91494-582">La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="91494-582">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="91494-583">Testare il metodo PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="91494-583">Test the PostTodoItem method</span></span>

* <span data-ttu-id="91494-584">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="91494-584">Build the project.</span></span>
* <span data-ttu-id="91494-585">In Postman impostare il metodo HTTP su `POST`.</span><span class="sxs-lookup"><span data-stu-id="91494-585">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="91494-586">Selezionare la scheda **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="91494-586">Select the **Body** tab.</span></span>
* <span data-ttu-id="91494-587">Selezionare il pulsante di opzione **raw** (non elaborato).</span><span class="sxs-lookup"><span data-stu-id="91494-587">Select the **raw** radio button.</span></span>
* <span data-ttu-id="91494-588">Impostare il tipo su **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="91494-588">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="91494-589">Nel corpo della richiesta immettere codice JSON per un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="91494-589">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="91494-590">Selezionare **Invia**.</span><span class="sxs-lookup"><span data-stu-id="91494-590">Select **Send**.</span></span>

  ![Postman con richiesta di creazione](first-web-api/_static/create.png)

  <span data-ttu-id="91494-592">Se viene visualizzato un errore HTTP 405 - Metodo non consentito, è probabile che il progetto non sia stato compilato dopo l'aggiunta del metodo `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="91494-592">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="91494-593">Testare l'URI dell'intestazione della posizione</span><span class="sxs-lookup"><span data-stu-id="91494-593">Test the location header URI</span></span>

* <span data-ttu-id="91494-594">Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).</span><span class="sxs-lookup"><span data-stu-id="91494-594">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="91494-595">Copiare il valore dell'intestazione **Location** (Posizione):</span><span class="sxs-lookup"><span data-stu-id="91494-595">Copy the **Location** header value:</span></span>

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="91494-597">Impostare il metodo su GET.</span><span class="sxs-lookup"><span data-stu-id="91494-597">Set the method to GET.</span></span>
* <span data-ttu-id="91494-598">Incollare l'URI (ad esempio, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="91494-598">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="91494-599">Selezionare **Invia**.</span><span class="sxs-lookup"><span data-stu-id="91494-599">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="91494-600">Aggiungere un metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="91494-600">Add a PutTodoItem method</span></span>

<span data-ttu-id="91494-601">Aggiungere il metodo `PutTodoItem` seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-601">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="91494-602">`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="91494-602">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="91494-603">La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="91494-603">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="91494-604">In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="91494-604">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="91494-605">Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="91494-605">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="91494-606">Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.</span><span class="sxs-lookup"><span data-stu-id="91494-606">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="91494-607">Testare il metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="91494-607">Test the PutTodoItem method</span></span>

<span data-ttu-id="91494-608">Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="91494-608">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="91494-609">Deve esistere un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="91494-609">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="91494-610">Chiamare GET per assicurarsi che sia presente un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="91494-610">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="91494-611">Aggiornare l'elemento attività con id = 1 e impostarne il nome su "feed fish":</span><span class="sxs-lookup"><span data-stu-id="91494-611">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="91494-612">L'immagine seguente visualizza l'aggiornamento di Postman:</span><span class="sxs-lookup"><span data-stu-id="91494-612">The following image shows the Postman update:</span></span>

![Console Postman con risposta 204 (No Content)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="91494-614">Aggiungere un metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="91494-614">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="91494-615">Aggiungere il metodo `DeleteTodoItem` seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-615">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="91494-616">La `DeleteTodoItem` risposta è [204 (nessun contenuto)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="91494-616">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="91494-617">Testare il metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="91494-617">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="91494-618">Usare Postman per eliminare un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="91494-618">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="91494-619">Impostare il metodo su `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="91494-619">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="91494-620">Impostare l'URI dell'oggetto da eliminare (ad esempio, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="91494-620">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="91494-621">Selezionare **Invia**.</span><span class="sxs-lookup"><span data-stu-id="91494-621">Select **Send**.</span></span>

<span data-ttu-id="91494-622">L'app di esempio consente di eliminare tutti gli elementi.</span><span class="sxs-lookup"><span data-stu-id="91494-622">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="91494-623">Quando viene eliminato l'ultimo elemento, tuttavia, ne viene creato uno nuovo dal costruttore della classe modello alla successiva chiamata dell'API.</span><span class="sxs-lookup"><span data-stu-id="91494-623">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="91494-624">Chiamare l'API Web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="91494-624">Call the web API with JavaScript</span></span>

<span data-ttu-id="91494-625">In questa sezione viene aggiunta una pagina HTML che usa JavaScript per chiamare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="91494-625">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="91494-626">jQuery avvia la richiesta.</span><span class="sxs-lookup"><span data-stu-id="91494-626">jQuery initiates the request.</span></span> <span data-ttu-id="91494-627">JavaScript aggiorna la pagina con i dettagli della risposta dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="91494-627">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="91494-628">Configurare l'app per [servire file statici](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [abilitare il mapping del file predefinito](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aggiornando *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-628">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="91494-629">Creare una cartella *wwwroot* nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="91494-629">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="91494-630">Aggiungere un file HTML denominato *index.html* alla directory *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="91494-630">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="91494-631">Sostituire il contenuto con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-631">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="91494-632">Aggiungere un file JavaScript con nome *site.js* alla directory *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="91494-632">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="91494-633">Sostituire il contenuto con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="91494-633">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="91494-634">Può essere necessario modificare le impostazioni di avvio del progetto ASP.NET Core per il test della pagina HTML in locale:</span><span class="sxs-lookup"><span data-stu-id="91494-634">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="91494-635">Aprire *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="91494-635">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="91494-636">Rimuovere la `launchUrl` proprietà per forzare l'apertura dell'app nel file *index. html*&mdash;del file predefinito del progetto.</span><span class="sxs-lookup"><span data-stu-id="91494-636">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="91494-637">In questo esempio vengono chiamati tutti i metodi CRUD dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="91494-637">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="91494-638">Di seguito sono incluse le spiegazioni delle chiamate all'API.</span><span class="sxs-lookup"><span data-stu-id="91494-638">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="91494-639">Ottenere un elenco di elementi attività</span><span class="sxs-lookup"><span data-stu-id="91494-639">Get a list of to-do items</span></span>

<span data-ttu-id="91494-640">jQuery invia una richiesta HTTP GET all'API Web, che restituisce JSON che rappresenta una matrice di elementi attività.</span><span class="sxs-lookup"><span data-stu-id="91494-640">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="91494-641">La funzione di callback `success` viene chiamata se la richiesta ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="91494-641">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="91494-642">Nel callback il modello DOM viene aggiornato con le informazioni dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="91494-642">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="91494-643">Aggiungere un elemento attività</span><span class="sxs-lookup"><span data-stu-id="91494-643">Add a to-do item</span></span>

<span data-ttu-id="91494-644">jQuery invia una richiesta HTTP POST con l'elemento attività nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="91494-644">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="91494-645">Le opzioni `accepts` e `contentType` sono impostate su `application/json` per specificare il tipo di supporto ricevuto e inviato.</span><span class="sxs-lookup"><span data-stu-id="91494-645">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="91494-646">L'elemento attività viene convertito in JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="91494-646">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="91494-647">Quando l'API restituisce un codice di stato di esecuzione riuscita, viene chiamata la funzione `getData` per aggiornare la tabella HTML.</span><span class="sxs-lookup"><span data-stu-id="91494-647">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="91494-648">Aggiornare un elemento attività</span><span class="sxs-lookup"><span data-stu-id="91494-648">Update a to-do item</span></span>

<span data-ttu-id="91494-649">L'aggiornamento di un elemento attività è simile all'aggiunta di un elemento di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="91494-649">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="91494-650">L'`url` cambia per includere l'identificatore univoco dell'elemento e `type` è `PUT`.</span><span class="sxs-lookup"><span data-stu-id="91494-650">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="91494-651">Eliminare un elemento attività</span><span class="sxs-lookup"><span data-stu-id="91494-651">Delete a to-do item</span></span>

<span data-ttu-id="91494-652">È possibile eliminare un elemento attività impostando `type` su `DELETE` nella chiamata AJAX e specificando l'identificatore univoco dell'elemento nell'URL.</span><span class="sxs-lookup"><span data-stu-id="91494-652">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="91494-653">Aggiungere il supporto per l'autenticazione a un'API Web</span><span class="sxs-lookup"><span data-stu-id="91494-653">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="91494-654">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="91494-654">Additional resources</span></span>

<span data-ttu-id="91494-655">[Visualizzare o scaricare il codice di esempio per questa esercitazione](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="91494-655">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="91494-656">Vedere [come scaricare un esempio](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="91494-656">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="91494-657">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="91494-657">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="91494-658">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="91494-658">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
