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
ms.openlocfilehash: 2fcfd46057935cadac76c558a78729a1c096ffc0
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451824"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="710f4-103">Esercitazione: creare un'API Web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="710f4-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="710f4-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="710f4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="710f4-105">Questa esercitazione illustra le nozioni di base della creazione di un'API Web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="710f4-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="710f4-106">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="710f4-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="710f4-107">Creare un progetto di API Web.</span><span class="sxs-lookup"><span data-stu-id="710f4-107">Create a web API project.</span></span>
> * <span data-ttu-id="710f4-108">Aggiungere una classe modello e un contesto di database.</span><span class="sxs-lookup"><span data-stu-id="710f4-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="710f4-109">Eseguire lo scaffolding di un controller con i metodi CRUD.</span><span class="sxs-lookup"><span data-stu-id="710f4-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="710f4-110">Configurare il routing, i percorsi URL e i valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="710f4-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="710f4-111">Chiamare l'API Web con Postman.</span><span class="sxs-lookup"><span data-stu-id="710f4-111">Call the web API with Postman.</span></span>

<span data-ttu-id="710f4-112">Al termine si avrà un'API Web che può gestire gli elementi di tipo "attività" archiviati in un database.</span><span class="sxs-lookup"><span data-stu-id="710f4-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="710f4-113">Panoramica</span><span class="sxs-lookup"><span data-stu-id="710f4-113">Overview</span></span>

<span data-ttu-id="710f4-114">Questa esercitazione consente di creare l'API seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="710f4-115">API</span><span class="sxs-lookup"><span data-stu-id="710f4-115">API</span></span> | <span data-ttu-id="710f4-116">Description</span><span class="sxs-lookup"><span data-stu-id="710f4-116">Description</span></span> | <span data-ttu-id="710f4-117">Testo della richiesta</span><span class="sxs-lookup"><span data-stu-id="710f4-117">Request body</span></span> | <span data-ttu-id="710f4-118">Corpo della risposta</span><span class="sxs-lookup"><span data-stu-id="710f4-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="710f4-119">Ottiene tutti gli elementi attività</span><span class="sxs-lookup"><span data-stu-id="710f4-119">Get all to-do items</span></span> | <span data-ttu-id="710f4-120">nessuno</span><span class="sxs-lookup"><span data-stu-id="710f4-120">None</span></span> | <span data-ttu-id="710f4-121">Matrice di elementi attività</span><span class="sxs-lookup"><span data-stu-id="710f4-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="710f4-122">Ottiene un elemento in base all'ID</span><span class="sxs-lookup"><span data-stu-id="710f4-122">Get an item by ID</span></span> | <span data-ttu-id="710f4-123">nessuno</span><span class="sxs-lookup"><span data-stu-id="710f4-123">None</span></span> | <span data-ttu-id="710f4-124">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="710f4-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="710f4-125">Aggiunge un nuovo elemento</span><span class="sxs-lookup"><span data-stu-id="710f4-125">Add a new item</span></span> | <span data-ttu-id="710f4-126">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="710f4-126">To-do item</span></span> | <span data-ttu-id="710f4-127">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="710f4-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="710f4-128">Aggiorna un elemento esistente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="710f4-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="710f4-129">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="710f4-129">To-do item</span></span> | <span data-ttu-id="710f4-130">nessuno</span><span class="sxs-lookup"><span data-stu-id="710f4-130">None</span></span> |
|<span data-ttu-id="710f4-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="710f4-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="710f4-132">Elimina un elemento &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="710f4-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="710f4-133">nessuno</span><span class="sxs-lookup"><span data-stu-id="710f4-133">None</span></span> | <span data-ttu-id="710f4-134">nessuno</span><span class="sxs-lookup"><span data-stu-id="710f4-134">None</span></span>|

<span data-ttu-id="710f4-135">Il diagramma seguente visualizza la struttura dell'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-135">The following diagram shows the design of the app.</span></span>

![Il client è rappresentato da una casella a sinistra.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="710f4-141">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="710f4-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="710f4-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="710f4-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="710f4-144">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="710f4-145">Creare un progetto Web</span><span class="sxs-lookup"><span data-stu-id="710f4-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="710f4-147">Scegliere **Nuovo** > **Progetto** dal menu **File**.</span><span class="sxs-lookup"><span data-stu-id="710f4-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="710f4-148">Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="710f4-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="710f4-149">Assegnare al progetto il nome *TodoApi* e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="710f4-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="710f4-150">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.net Core** e **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="710f4-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="710f4-151">Selezionare il modello **API** e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="710f4-151">Select the **API** template and click **Create**.</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="710f4-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="710f4-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="710f4-154">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="710f4-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="710f4-155">Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="710f4-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="710f4-156">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="710f4-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="710f4-157">Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="710f4-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="710f4-158">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="710f4-158">The preceding commands:</span></span>

  * <span data-ttu-id="710f4-159">Crea un nuovo progetto API Web e lo apre in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="710f4-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="710f4-160">Aggiunge i pacchetti NuGet necessari nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="710f4-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="710f4-161">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="710f4-162">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="710f4-162">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="710f4-164">In Visual Studio per Mac precedente alla versione 8,6 Selezionare API **app .NET Core**  >  **App**  >  **API**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="710f4-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="710f4-165">Nella versione 8,6 o successive selezionare API **app Web e console**  >  **App**  >  **API**  >  **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="710f4-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![Selezione modello API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="710f4-167">Verificare che il **Framework di destinazione** sia impostato su **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="710f4-167">Confirm the **Target Framework** is set to **.NET Core 3.1**.</span></span> <span data-ttu-id="710f4-168">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="710f4-168">Select **Next**.</span></span>

  ![selezione di macOS .NET Core 3,1](first-web-api-mac/_static/api_31_config.png)

* <span data-ttu-id="710f4-170">Immettere *TodoApi* in **Nome progetto**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="710f4-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="710f4-172">Aprire un terminale di comando nella cartella di progetto ed eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="710f4-172">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="710f4-173">Testare l'API</span><span class="sxs-lookup"><span data-stu-id="710f4-173">Test the API</span></span>

<span data-ttu-id="710f4-174">Il modello di progetto crea un'API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="710f4-174">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="710f4-175">Chiamare il metodo `Get` da un browser per eseguire il test dell'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-175">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="710f4-177">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-177">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="710f4-178">Visual Studio apre un browser e naviga all'indirizzo `https://localhost:<port>/WeatherForecast`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="710f4-178">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="710f4-179">Se in una finestra di dialogo viene chiesto se considerare attendibile il certificato IIS Express, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="710f4-179">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="710f4-180">Nella finestra di dialogo **Avviso di sicurezza** successiva selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="710f4-180">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="710f4-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="710f4-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="710f4-182">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-182">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="710f4-183">In un browser passare all'URL seguente: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="710f4-183">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="710f4-184">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-184">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="710f4-185">Selezionare **Esegui**  >  **Avvia debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-185">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="710f4-186">Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="710f4-186">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="710f4-187">Viene restituito un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="710f4-187">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="710f4-188">Accodare `/WeatherForecast` all'URL (impostare l'URL su `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="710f4-188">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="710f4-189">Viene restituito un codice JSON simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-189">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="710f4-190">Aggiungere una classe del modello</span><span class="sxs-lookup"><span data-stu-id="710f4-190">Add a model class</span></span>

<span data-ttu-id="710f4-191">Un *modello* è un set di classi che rappresentano i dati gestiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-191">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="710f4-192">Il modello per questa app è una classe `TodoItem` singola.</span><span class="sxs-lookup"><span data-stu-id="710f4-192">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="710f4-194">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="710f4-194">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="710f4-195">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="710f4-195">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="710f4-196">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="710f4-196">Name the folder *Models*.</span></span>

* <span data-ttu-id="710f4-197">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="710f4-197">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="710f4-198">Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="710f4-198">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="710f4-199">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-199">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="710f4-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="710f4-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="710f4-201">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="710f4-201">Add a folder named *Models*.</span></span>

* <span data-ttu-id="710f4-202">Aggiungere una classe `TodoItem` alla cartella *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-202">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="710f4-203">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="710f4-204">Fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="710f4-204">Right-click the project.</span></span> <span data-ttu-id="710f4-205">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="710f4-205">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="710f4-206">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="710f4-206">Name the folder *Models*.</span></span>

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="710f4-208">Fare clic con il pulsante destro del mouse sulla cartella *Models* e selezionare **Aggiungi** > **Nuovo file** > **Generale** > **Classe vuota**.</span><span class="sxs-lookup"><span data-stu-id="710f4-208">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="710f4-209">Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="710f4-209">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="710f4-210">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-210">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="710f4-211">La proprietà `Id` funziona come chiave univoca in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="710f4-211">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="710f4-212">È possibile inserire le classi del modello in qualsiasi punto del progetto, ma per convenzione viene usata la cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="710f4-212">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="710f4-213">Aggiungere un contesto di database</span><span class="sxs-lookup"><span data-stu-id="710f4-213">Add a database context</span></span>

<span data-ttu-id="710f4-214">Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati.</span><span class="sxs-lookup"><span data-stu-id="710f4-214">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="710f4-215">Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="710f4-215">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-216">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="710f4-217">Aggiungere Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="710f4-217">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="710f4-218">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione**.</span><span class="sxs-lookup"><span data-stu-id="710f4-218">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="710f4-219">Selezionare la scheda **Esplora** e quindi immettere **Microsoft.EntityFrameworkCore.SqlServer** nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="710f4-219">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="710f4-220">Nel riquadro sinistro selezionare **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="710f4-220">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="710f4-221">Selezionare la casella di controllo **Progetto** nel riquadro di destra e quindi selezionare **Installa**.</span><span class="sxs-lookup"><span data-stu-id="710f4-221">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="710f4-222">Usare le istruzioni precedenti per aggiungere il pacchetto NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="710f4-222">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gestione pacchetti NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="710f4-224">Aggiungere il contesto del database TodoContext</span><span class="sxs-lookup"><span data-stu-id="710f4-224">Add the TodoContext database context</span></span>

* <span data-ttu-id="710f4-225">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="710f4-225">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="710f4-226">Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="710f4-226">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="710f4-227">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-227">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="710f4-228">Aggiungere una classe `TodoContext` alla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="710f4-228">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="710f4-229">Immettere il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-229">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="710f4-230">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="710f4-230">Register the database context</span></span>

<span data-ttu-id="710f4-231">In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="710f4-231">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="710f4-232">Il contenitore rende disponibile il servizio ai controller.</span><span class="sxs-lookup"><span data-stu-id="710f4-232">The container provides the service to controllers.</span></span>

<span data-ttu-id="710f4-233">Aggiornare *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-233">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="710f4-234">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="710f4-234">The preceding code:</span></span>

* <span data-ttu-id="710f4-235">Rimuove le dichiarazioni `using` inutilizzate.</span><span class="sxs-lookup"><span data-stu-id="710f4-235">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="710f4-236">Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="710f4-236">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="710f4-237">Specifica che il contesto del database userà un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="710f4-237">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="710f4-238">Eseguire lo scaffolding di un controller</span><span class="sxs-lookup"><span data-stu-id="710f4-238">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-239">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="710f4-240">Fare clic con il pulsante destro del mouse sulla cartella *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="710f4-240">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="710f4-241">Selezionare **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="710f4-241">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="710f4-242">Selezionare **Controller API con azioni, che usa Entity Framework** e quindi selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="710f4-242">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="710f4-243">Nella finestra di dialogo **Add API Controller with actions, using Entity Framework** (Aggiungi controller API con azioni, che usa Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="710f4-243">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="710f4-244">Selezionare **TodoItem (TodoApi. Models)** nella **classe del modello**.</span><span class="sxs-lookup"><span data-stu-id="710f4-244">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="710f4-245">Selezionare **TodoContext (TodoApi. Models)** nella **classe del contesto dati**.</span><span class="sxs-lookup"><span data-stu-id="710f4-245">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="710f4-246">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="710f4-246">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="710f4-247">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="710f4-248">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="710f4-248">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="710f4-249">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="710f4-249">The preceding commands:</span></span>

* <span data-ttu-id="710f4-250">Aggiungere i pacchetti NuGet necessari per lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="710f4-250">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="710f4-251">Installa il motore di scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="710f4-251">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="710f4-252">Esegue lo scaffolding di `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="710f4-252">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="710f4-253">Il codice generato:</span><span class="sxs-lookup"><span data-stu-id="710f4-253">The generated code:</span></span>

* <span data-ttu-id="710f4-254">Contrassegna la classe con l' [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="710f4-254">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="710f4-255">L'attributo indica che il controller risponde alle richieste di API Web.</span><span class="sxs-lookup"><span data-stu-id="710f4-255">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="710f4-256">Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="710f4-256">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="710f4-257">Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="710f4-257">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="710f4-258">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="710f4-258">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="710f4-259">I modelli di ASP.NET Core per:</span><span class="sxs-lookup"><span data-stu-id="710f4-259">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="710f4-260">I controller con le visualizzazioni includono `[action]` nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="710f4-260">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="710f4-261">I controller API non includono `[action]` nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="710f4-261">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="710f4-262">Quando il `[action]` token non è incluso nel modello di route, il nome dell' [azione](xref:mvc/controllers/routing#action) viene escluso dalla route.</span><span class="sxs-lookup"><span data-stu-id="710f4-262">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="710f4-263">Ovvero, il nome del metodo associato all'azione non viene usato nella route corrispondente.</span><span class="sxs-lookup"><span data-stu-id="710f4-263">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="710f4-264">Esaminare il metodo di creazione PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="710f4-264">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="710f4-265">Sostituire l'istruzione return in `PostTodoItem` per usare l'operatore [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="710f4-265">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="710f4-266">Il codice precedente è un metodo HTTP POST, come indicato dall' [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="710f4-266">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="710f4-267">Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="710f4-267">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="710f4-268">Il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="710f4-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="710f4-269">Restituisce un codice di stato HTTP 201 in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="710f4-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="710f4-270">HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="710f4-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="710f4-271">Aggiunge un'intestazione [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) alla risposta.</span><span class="sxs-lookup"><span data-stu-id="710f4-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="710f4-272">L'intestazione `Location` specifica l'[URI](https://developer.mozilla.org/docs/Glossary/URI) dell'elemento attività appena creato.</span><span class="sxs-lookup"><span data-stu-id="710f4-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="710f4-273">Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="710f4-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="710f4-274">Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`.</span><span class="sxs-lookup"><span data-stu-id="710f4-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="710f4-275">La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="710f4-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="710f4-276">Installare Postman</span><span class="sxs-lookup"><span data-stu-id="710f4-276">Install Postman</span></span>

<span data-ttu-id="710f4-277">Questa esercitazione usa Postman per testare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="710f4-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="710f4-278">Installa il [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="710f4-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="710f4-279">Avviare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="710f4-279">Start the web app.</span></span>
* <span data-ttu-id="710f4-280">Avviare Postman.</span><span class="sxs-lookup"><span data-stu-id="710f4-280">Start Postman.</span></span>
* <span data-ttu-id="710f4-281">Disattivare **SSL certificate verification** (Verifica certificato SSL)</span><span class="sxs-lookup"><span data-stu-id="710f4-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="710f4-282">Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).</span><span class="sxs-lookup"><span data-stu-id="710f4-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="710f4-283">Riattivare la verifica dei certificati SSL al termine del test del controller.</span><span class="sxs-lookup"><span data-stu-id="710f4-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="710f4-284">Testare PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="710f4-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="710f4-285">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="710f4-285">Create a new request.</span></span>
* <span data-ttu-id="710f4-286">Impostare il metodo HTTP su `POST`.</span><span class="sxs-lookup"><span data-stu-id="710f4-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="710f4-287">Selezionare la scheda **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="710f4-287">Select the **Body** tab.</span></span>
* <span data-ttu-id="710f4-288">Selezionare il pulsante di opzione **raw** (non elaborato).</span><span class="sxs-lookup"><span data-stu-id="710f4-288">Select the **raw** radio button.</span></span>
* <span data-ttu-id="710f4-289">Impostare il tipo su **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="710f4-289">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="710f4-290">Nel corpo della richiesta immettere codice JSON per un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="710f4-290">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="710f4-291">Selezionare**Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="710f4-291">Select **Send**.</span></span>

  ![Postman con richiesta di creazione](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="710f4-293">Testare l'URI dell'intestazione della posizione</span><span class="sxs-lookup"><span data-stu-id="710f4-293">Test the location header URI</span></span>

* <span data-ttu-id="710f4-294">Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).</span><span class="sxs-lookup"><span data-stu-id="710f4-294">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="710f4-295">Copiare il valore dell'intestazione **Location** (Posizione):</span><span class="sxs-lookup"><span data-stu-id="710f4-295">Copy the **Location** header value:</span></span>

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="710f4-297">Impostare il metodo su GET.</span><span class="sxs-lookup"><span data-stu-id="710f4-297">Set the method to GET.</span></span>
* <span data-ttu-id="710f4-298">Incollare l'URI (ad esempio, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="710f4-298">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="710f4-299">Selezionare**Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="710f4-299">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="710f4-300">Esaminare i metodi GET</span><span class="sxs-lookup"><span data-stu-id="710f4-300">Examine the GET methods</span></span>

<span data-ttu-id="710f4-301">Questi metodi implementano due metodi GET:</span><span class="sxs-lookup"><span data-stu-id="710f4-301">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="710f4-302">Testare l'app chiamando i due endpoint da un browser o da Postman.</span><span class="sxs-lookup"><span data-stu-id="710f4-302">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="710f4-303">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="710f4-303">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="710f4-304">Una risposta simile alla seguente viene generata dalla chiamata a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="710f4-304">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="710f4-305">Testare Get con Postman</span><span class="sxs-lookup"><span data-stu-id="710f4-305">Test Get with Postman</span></span>

* <span data-ttu-id="710f4-306">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="710f4-306">Create a new request.</span></span>
* <span data-ttu-id="710f4-307">Impostare il metodo HTTP su **GET**.</span><span class="sxs-lookup"><span data-stu-id="710f4-307">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="710f4-308">Impostare l'URL della richiesta su `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="710f4-308">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="710f4-309">Ad esempio: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="710f4-309">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="710f4-310">Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.</span><span class="sxs-lookup"><span data-stu-id="710f4-310">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="710f4-311">Selezionare**Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="710f4-311">Select **Send**.</span></span>

<span data-ttu-id="710f4-312">Questa app usa un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="710f4-312">This app uses an in-memory database.</span></span> <span data-ttu-id="710f4-313">Se l'app viene arrestata e avviata, la richiesta GET precedente non restituirà alcun dato.</span><span class="sxs-lookup"><span data-stu-id="710f4-313">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="710f4-314">Se non vengono restituiti dati, eseguire [POST](#post) per pubblicare i dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-314">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="710f4-315">Routing e percorsi di URL</span><span class="sxs-lookup"><span data-stu-id="710f4-315">Routing and URL paths</span></span>

<span data-ttu-id="710f4-316">L' [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attributo indica un metodo che risponde a una richiesta HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="710f4-316">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="710f4-317">Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-317">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="710f4-318">Iniziare con la stringa di modello nell'attributo `Route` del controller:</span><span class="sxs-lookup"><span data-stu-id="710f4-318">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="710f4-319">Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller".</span><span class="sxs-lookup"><span data-stu-id="710f4-319">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="710f4-320">In questo esempio il nome della classe controller è **TodoItems**Controller, quindi il nome del controller è "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="710f4-320">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="710f4-321">Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="710f4-321">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="710f4-322">Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso.</span><span class="sxs-lookup"><span data-stu-id="710f4-322">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="710f4-323">In questo esempio non si usa un modello.</span><span class="sxs-lookup"><span data-stu-id="710f4-323">This sample doesn't use a template.</span></span> <span data-ttu-id="710f4-324">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="710f4-324">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="710f4-325">Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="710f4-325">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="710f4-326">Quando `GetTodoItem` viene richiamato, il valore di `"{id}"` nell'URL viene fornito al metodo nel `id` parametro.</span><span class="sxs-lookup"><span data-stu-id="710f4-326">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="710f4-327">Valori restituiti</span><span class="sxs-lookup"><span data-stu-id="710f4-327">Return values</span></span>

<span data-ttu-id="710f4-328">Il tipo restituito dei `GetTodoItems` metodi e `GetTodoItem` è [ActionResult. \<T> ](xref:web-api/action-return-types#actionresultt-type)</span><span class="sxs-lookup"><span data-stu-id="710f4-328">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="710f4-329">ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="710f4-329">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="710f4-330">Il codice di risposta per questo tipo restituito è 200, presupponendo che non esistano eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="710f4-330">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="710f4-331">Le eccezioni non gestite vengono convertite in errori 5xx.</span><span class="sxs-lookup"><span data-stu-id="710f4-331">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="710f4-332">I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="710f4-332">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="710f4-333">Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:</span><span class="sxs-lookup"><span data-stu-id="710f4-333">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="710f4-334">Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un codice di errore 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="710f4-334">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="710f4-335">In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON.</span><span class="sxs-lookup"><span data-stu-id="710f4-335">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="710f4-336">La restituzione di `item` risulta in una risposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="710f4-336">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="710f4-337">Metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="710f4-337">The PutTodoItem method</span></span>

<span data-ttu-id="710f4-338">Esaminare il metodo `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="710f4-338">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="710f4-339">`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="710f4-339">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="710f4-340">La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="710f4-340">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="710f4-341">In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="710f4-341">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="710f4-342">Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="710f4-342">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="710f4-343">Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.</span><span class="sxs-lookup"><span data-stu-id="710f4-343">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="710f4-344">Testare il metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="710f4-344">Test the PutTodoItem method</span></span>

<span data-ttu-id="710f4-345">Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="710f4-345">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="710f4-346">Deve esistere un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="710f4-346">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="710f4-347">Chiamare GET per assicurarsi che sia presente un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="710f4-347">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="710f4-348">Aggiornare l'elemento attività con ID = 1 e impostarne il nome su "feed fish":</span><span class="sxs-lookup"><span data-stu-id="710f4-348">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="710f4-349">L'immagine seguente visualizza l'aggiornamento di Postman:</span><span class="sxs-lookup"><span data-stu-id="710f4-349">The following image shows the Postman update:</span></span>

![Console Postman con risposta 204 (No Content)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="710f4-351">Metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="710f4-351">The DeleteTodoItem method</span></span>

<span data-ttu-id="710f4-352">Esaminare il metodo `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="710f4-352">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="710f4-353">Testare il metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="710f4-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="710f4-354">Usare Postman per eliminare un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="710f4-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="710f4-355">Impostare il metodo su `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="710f4-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="710f4-356">Impostare l'URI dell'oggetto da eliminare (ad esempio `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="710f4-356">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="710f4-357">Selezionare**Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="710f4-357">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="710f4-358">Impedisci overposting</span><span class="sxs-lookup"><span data-stu-id="710f4-358">Prevent over-posting</span></span>

<span data-ttu-id="710f4-359">Attualmente l'app di esempio espone l'intero `TodoItem` oggetto.</span><span class="sxs-lookup"><span data-stu-id="710f4-359">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="710f4-360">Le app Productions limitano in genere i dati di input e restituiti usando un subset del modello.</span><span class="sxs-lookup"><span data-stu-id="710f4-360">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="710f4-361">Esistono diversi motivi alla base di questo e la sicurezza è una delle principali.</span><span class="sxs-lookup"><span data-stu-id="710f4-361">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="710f4-362">Il subset di un modello è in genere indicato come oggetto Trasferimento dati (DTO), modello di input o modello di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="710f4-362">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="710f4-363">**Dto** viene usato in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="710f4-363">**DTO** is used in this article.</span></span>

<span data-ttu-id="710f4-364">Un DTO può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="710f4-364">A DTO may be used to:</span></span>

* <span data-ttu-id="710f4-365">Impedisci l'overposting.</span><span class="sxs-lookup"><span data-stu-id="710f4-365">Prevent over-posting.</span></span>
* <span data-ttu-id="710f4-366">Nascondere le proprietà che i client non dovrebbero visualizzare.</span><span class="sxs-lookup"><span data-stu-id="710f4-366">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="710f4-367">Omettere alcune proprietà per ridurre le dimensioni del payload.</span><span class="sxs-lookup"><span data-stu-id="710f4-367">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="710f4-368">Rendere flat gli oggetti grafici che contengono oggetti annidati.</span><span class="sxs-lookup"><span data-stu-id="710f4-368">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="710f4-369">I grafici a oggetti flat possono essere più pratici per i client.</span><span class="sxs-lookup"><span data-stu-id="710f4-369">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="710f4-370">Per illustrare l'approccio DTO, aggiornare la `TodoItem` classe in modo da includere un campo Secret:</span><span class="sxs-lookup"><span data-stu-id="710f4-370">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="710f4-371">Il campo segreto deve essere nascosto da questa app, ma un'app amministrativa può scegliere di esporla.</span><span class="sxs-lookup"><span data-stu-id="710f4-371">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="710f4-372">Verificare che sia possibile pubblicare e ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="710f4-372">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="710f4-373">Creare un modello DTO:</span><span class="sxs-lookup"><span data-stu-id="710f4-373">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="710f4-374">Aggiornare `TodoItemsController` da usare `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="710f4-374">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="710f4-375">Verificare che non sia possibile inserire o ottenere il campo secret.</span><span class="sxs-lookup"><span data-stu-id="710f4-375">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="710f4-376">Chiamare l'API Web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="710f4-376">Call the web API with JavaScript</span></span>

<span data-ttu-id="710f4-377">Vedere [esercitazione: chiamare un'API web ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="710f4-377">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="710f4-378">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="710f4-378">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="710f4-379">Creare un progetto di API Web.</span><span class="sxs-lookup"><span data-stu-id="710f4-379">Create a web API project.</span></span>
> * <span data-ttu-id="710f4-380">Aggiungere una classe modello e un contesto di database.</span><span class="sxs-lookup"><span data-stu-id="710f4-380">Add a model class and a database context.</span></span>
> * <span data-ttu-id="710f4-381">Aggiungere un controller.</span><span class="sxs-lookup"><span data-stu-id="710f4-381">Add a controller.</span></span>
> * <span data-ttu-id="710f4-382">Aggiungere metodi CRUD.</span><span class="sxs-lookup"><span data-stu-id="710f4-382">Add CRUD methods.</span></span>
> * <span data-ttu-id="710f4-383">Configurare routing e percorsi URL.</span><span class="sxs-lookup"><span data-stu-id="710f4-383">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="710f4-384">Specificare valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="710f4-384">Specify return values.</span></span>
> * <span data-ttu-id="710f4-385">Chiamare l'API Web con Postman.</span><span class="sxs-lookup"><span data-stu-id="710f4-385">Call the web API with Postman.</span></span>
> * <span data-ttu-id="710f4-386">Chiamare l'API Web con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="710f4-386">Call the web API with JavaScript.</span></span>

<span data-ttu-id="710f4-387">Al termine si dispone di un'API web che può gestire gli elementi di tipo "attività" archiviati in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="710f4-387">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="710f4-388">Panoramica</span><span class="sxs-lookup"><span data-stu-id="710f4-388">Overview</span></span>

<span data-ttu-id="710f4-389">Questa esercitazione consente di creare l'API seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-389">This tutorial creates the following API:</span></span>

|<span data-ttu-id="710f4-390">API</span><span class="sxs-lookup"><span data-stu-id="710f4-390">API</span></span> | <span data-ttu-id="710f4-391">Description</span><span class="sxs-lookup"><span data-stu-id="710f4-391">Description</span></span> | <span data-ttu-id="710f4-392">Testo della richiesta</span><span class="sxs-lookup"><span data-stu-id="710f4-392">Request body</span></span> | <span data-ttu-id="710f4-393">Corpo della risposta</span><span class="sxs-lookup"><span data-stu-id="710f4-393">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="710f4-394">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="710f4-394">GET /api/TodoItems</span></span> | <span data-ttu-id="710f4-395">Ottiene tutti gli elementi attività</span><span class="sxs-lookup"><span data-stu-id="710f4-395">Get all to-do items</span></span> | <span data-ttu-id="710f4-396">nessuno</span><span class="sxs-lookup"><span data-stu-id="710f4-396">None</span></span> | <span data-ttu-id="710f4-397">Matrice di elementi attività</span><span class="sxs-lookup"><span data-stu-id="710f4-397">Array of to-do items</span></span>|
|<span data-ttu-id="710f4-398">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="710f4-398">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="710f4-399">Ottiene un elemento in base all'ID</span><span class="sxs-lookup"><span data-stu-id="710f4-399">Get an item by ID</span></span> | <span data-ttu-id="710f4-400">nessuno</span><span class="sxs-lookup"><span data-stu-id="710f4-400">None</span></span> | <span data-ttu-id="710f4-401">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="710f4-401">To-do item</span></span>|
|<span data-ttu-id="710f4-402">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="710f4-402">POST /api/TodoItems</span></span> | <span data-ttu-id="710f4-403">Aggiunge un nuovo elemento</span><span class="sxs-lookup"><span data-stu-id="710f4-403">Add a new item</span></span> | <span data-ttu-id="710f4-404">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="710f4-404">To-do item</span></span> | <span data-ttu-id="710f4-405">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="710f4-405">To-do item</span></span> |
|<span data-ttu-id="710f4-406">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="710f4-406">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="710f4-407">Aggiorna un elemento esistente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="710f4-407">Update an existing item &nbsp;</span></span> | <span data-ttu-id="710f4-408">Elemento attività</span><span class="sxs-lookup"><span data-stu-id="710f4-408">To-do item</span></span> | <span data-ttu-id="710f4-409">nessuno</span><span class="sxs-lookup"><span data-stu-id="710f4-409">None</span></span> |
|<span data-ttu-id="710f4-410">Elimina/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="710f4-410">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="710f4-411">Elimina un elemento &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="710f4-411">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="710f4-412">nessuno</span><span class="sxs-lookup"><span data-stu-id="710f4-412">None</span></span> | <span data-ttu-id="710f4-413">nessuno</span><span class="sxs-lookup"><span data-stu-id="710f4-413">None</span></span>|

<span data-ttu-id="710f4-414">Il diagramma seguente visualizza la struttura dell'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-414">The following diagram shows the design of the app.</span></span>

![Il client è rappresentato da una casella a sinistra.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="710f4-420">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="710f4-420">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-421">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="710f4-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="710f4-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="710f4-423">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-423">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="710f4-424">Creare un progetto Web</span><span class="sxs-lookup"><span data-stu-id="710f4-424">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-425">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-425">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="710f4-426">Scegliere **Nuovo** > **Progetto** dal menu **File**.</span><span class="sxs-lookup"><span data-stu-id="710f4-426">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="710f4-427">Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="710f4-427">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="710f4-428">Assegnare al progetto il nome *TodoApi* e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="710f4-428">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="710f4-429">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.NET Core** e **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="710f4-429">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="710f4-430">Selezionare il modello **API** e fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="710f4-430">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="710f4-431">**Non** selezionare **Abilita supporto Docker**.</span><span class="sxs-lookup"><span data-stu-id="710f4-431">**Don't** select **Enable Docker Support**.</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="710f4-433">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="710f4-433">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="710f4-434">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="710f4-434">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="710f4-435">Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="710f4-435">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="710f4-436">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="710f4-436">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="710f4-437">Questi comandi creano un nuovo progetto API Web e aprono una nuova istanza di Visual Studio Code nella nuova cartella di progetto.</span><span class="sxs-lookup"><span data-stu-id="710f4-437">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="710f4-438">Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="710f4-438">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="710f4-439">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="710f4-440">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="710f4-440">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="710f4-442">In Visual Studio per Mac precedente alla versione 8,6 Selezionare API **app .NET Core**  >  **App**  >  **API**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="710f4-442">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="710f4-443">Nella versione 8,6 o successive selezionare API **app Web e console**  >  **App**  >  **API**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="710f4-443">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="710f4-444">Nella finestra di dialogo **Configure your new ASP.NET Core Web API** (Configura la nuova API Web ASP.NET Core), accettare l'impostazione predefinita di **Framework di destinazione**, ovvero \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="710f4-444">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="710f4-445">Immettere *TodoApi* in **Nome progetto**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="710f4-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="710f4-447">Testare l'API</span><span class="sxs-lookup"><span data-stu-id="710f4-447">Test the API</span></span>

<span data-ttu-id="710f4-448">Il modello di progetto crea un'API `values`.</span><span class="sxs-lookup"><span data-stu-id="710f4-448">The project template creates a `values` API.</span></span> <span data-ttu-id="710f4-449">Chiamare il metodo `Get` da un browser per eseguire il test dell'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="710f4-451">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="710f4-452">Visual Studio apre un browser e naviga all'indirizzo `https://localhost:<port>/api/values`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="710f4-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="710f4-453">Se in una finestra di dialogo viene chiesto se considerare attendibile il certificato IIS Express, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="710f4-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="710f4-454">Nella finestra di dialogo **Avviso di sicurezza** successiva selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="710f4-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="710f4-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="710f4-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="710f4-456">Premere CTRL+F5 per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="710f4-457">In un browser passare all'URL seguente: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="710f4-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="710f4-458">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="710f4-459">Selezionare **Esegui**  >  **Avvia debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="710f4-460">Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="710f4-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="710f4-461">Viene restituito un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="710f4-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="710f4-462">Accodare `/api/values` all'URL (impostare l'URL su `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="710f4-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="710f4-463">Viene restituito il codice JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="710f4-464">Aggiungere una classe del modello</span><span class="sxs-lookup"><span data-stu-id="710f4-464">Add a model class</span></span>

<span data-ttu-id="710f4-465">Un *modello* è un set di classi che rappresentano i dati gestiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="710f4-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="710f4-466">Il modello per questa app è una classe `TodoItem` singola.</span><span class="sxs-lookup"><span data-stu-id="710f4-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="710f4-468">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="710f4-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="710f4-469">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="710f4-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="710f4-470">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="710f4-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="710f4-471">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="710f4-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="710f4-472">Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="710f4-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="710f4-473">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="710f4-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="710f4-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="710f4-475">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="710f4-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="710f4-476">Aggiungere una classe `TodoItem` alla cartella *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="710f4-477">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="710f4-478">Fare clic con il pulsante destro del mouse sul progetto.</span><span class="sxs-lookup"><span data-stu-id="710f4-478">Right-click the project.</span></span> <span data-ttu-id="710f4-479">Selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="710f4-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="710f4-480">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="710f4-480">Name the folder *Models*.</span></span>

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="710f4-482">Fare clic con il pulsante destro del mouse sulla cartella *Models* e selezionare **Aggiungi** > **Nuovo file** > **Generale** > **Classe vuota**.</span><span class="sxs-lookup"><span data-stu-id="710f4-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="710f4-483">Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="710f4-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="710f4-484">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="710f4-485">La proprietà `Id` funziona come chiave univoca in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="710f4-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="710f4-486">È possibile inserire le classi del modello in qualsiasi punto del progetto, ma per convenzione viene usata la cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="710f4-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="710f4-487">Aggiungere un contesto di database</span><span class="sxs-lookup"><span data-stu-id="710f4-487">Add a database context</span></span>

<span data-ttu-id="710f4-488">Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati.</span><span class="sxs-lookup"><span data-stu-id="710f4-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="710f4-489">Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="710f4-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="710f4-491">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="710f4-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="710f4-492">Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="710f4-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="710f4-493">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="710f4-494">Aggiungere una classe `TodoContext` alla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="710f4-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="710f4-495">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="710f4-496">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="710f4-496">Register the database context</span></span>

<span data-ttu-id="710f4-497">In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="710f4-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="710f4-498">Il contenitore rende disponibile il servizio ai controller.</span><span class="sxs-lookup"><span data-stu-id="710f4-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="710f4-499">Aggiornare *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="710f4-500">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="710f4-500">The preceding code:</span></span>

* <span data-ttu-id="710f4-501">Rimuove le dichiarazioni `using` inutilizzate.</span><span class="sxs-lookup"><span data-stu-id="710f4-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="710f4-502">Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="710f4-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="710f4-503">Specifica che il contesto del database userà un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="710f4-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="710f4-504">Aggiungere un controller</span><span class="sxs-lookup"><span data-stu-id="710f4-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="710f4-506">Fare clic con il pulsante destro del mouse sulla cartella *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="710f4-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="710f4-507">Scegliere **Aggiungi** > **Nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="710f4-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="710f4-508">Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare il modello **API Controller Class** (Classe controller API).</span><span class="sxs-lookup"><span data-stu-id="710f4-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="710f4-509">Assegnare alla classe il nome *TodoController* e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="710f4-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Finestra di dialogo Aggiungi nuovo elemento con controller nella casella di ricerca e controller API Web selezionato](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="710f4-511">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="710f4-512">Nella cartella *Controllers* creare una classe denominata `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="710f4-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="710f4-513">Sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="710f4-514">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="710f4-514">The preceding code:</span></span>

* <span data-ttu-id="710f4-515">Definisce una classe controller API senza metodi.</span><span class="sxs-lookup"><span data-stu-id="710f4-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="710f4-516">Contrassegna la classe con l' [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="710f4-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="710f4-517">L'attributo indica che il controller risponde alle richieste di API Web.</span><span class="sxs-lookup"><span data-stu-id="710f4-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="710f4-518">Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="710f4-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="710f4-519">Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="710f4-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="710f4-520">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="710f4-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="710f4-521">Aggiunge un elemento denominato `Item1` al database se il database è vuoto.</span><span class="sxs-lookup"><span data-stu-id="710f4-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="710f4-522">Questo codice si trova nel costruttore, quindi viene eseguito ogni volta che è presente una nuova richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="710f4-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="710f4-523">Se si eliminano tutti gli elementi, il costruttore crea di nuovo `Item1` quando viene nuovamente chiamato un metodo API.</span><span class="sxs-lookup"><span data-stu-id="710f4-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="710f4-524">Potrebbe pertanto sembrare che l'eliminazione non abbia funzionato, mentre di fatto ha funzionato.</span><span class="sxs-lookup"><span data-stu-id="710f4-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="710f4-525">Aggiungere metodi Get</span><span class="sxs-lookup"><span data-stu-id="710f4-525">Add Get methods</span></span>

<span data-ttu-id="710f4-526">Per specificare un'API che recupera elementi attività, aggiungere i metodi seguenti alla classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="710f4-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="710f4-527">Questi metodi implementano due metodi GET:</span><span class="sxs-lookup"><span data-stu-id="710f4-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="710f4-528">Arrestare l'app se è ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="710f4-528">Stop the app if it's still running.</span></span> <span data-ttu-id="710f4-529">Quindi eseguirla di nuovo per includere le modifiche più recenti.</span><span class="sxs-lookup"><span data-stu-id="710f4-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="710f4-530">Testare l'app chiamando i due endpoint da un browser.</span><span class="sxs-lookup"><span data-stu-id="710f4-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="710f4-531">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="710f4-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="710f4-532">La chiamata a `GetTodoItems` genera la risposta HTTP seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="710f4-533">Routing e percorsi di URL</span><span class="sxs-lookup"><span data-stu-id="710f4-533">Routing and URL paths</span></span>

<span data-ttu-id="710f4-534">L' [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attributo indica un metodo che risponde a una richiesta HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="710f4-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="710f4-535">Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="710f4-536">Iniziare con la stringa di modello nell'attributo `Route` del controller:</span><span class="sxs-lookup"><span data-stu-id="710f4-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="710f4-537">Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller".</span><span class="sxs-lookup"><span data-stu-id="710f4-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="710f4-538">In questo esempio il nome della classe controller è **Todo**Controller, pertanto il nome del controller è "todo".</span><span class="sxs-lookup"><span data-stu-id="710f4-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="710f4-539">Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="710f4-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="710f4-540">Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso.</span><span class="sxs-lookup"><span data-stu-id="710f4-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="710f4-541">In questo esempio non si usa un modello.</span><span class="sxs-lookup"><span data-stu-id="710f4-541">This sample doesn't use a template.</span></span> <span data-ttu-id="710f4-542">Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="710f4-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="710f4-543">Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="710f4-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="710f4-544">Quando viene chiamato `GetTodoItem`, il valore di `"{id}"` viene specificato per il metodo nel rispettivo parametro `id`.</span><span class="sxs-lookup"><span data-stu-id="710f4-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="710f4-545">Valori restituiti</span><span class="sxs-lookup"><span data-stu-id="710f4-545">Return values</span></span>

<span data-ttu-id="710f4-546">Il tipo restituito dei `GetTodoItems` metodi e `GetTodoItem` è [ActionResult. \<T> ](xref:web-api/action-return-types#actionresultt-type)</span><span class="sxs-lookup"><span data-stu-id="710f4-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="710f4-547">ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="710f4-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="710f4-548">Il codice di risposta per questo tipo restituito è 200, presupponendo che non esistano eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="710f4-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="710f4-549">Le eccezioni non gestite vengono convertite in errori 5xx.</span><span class="sxs-lookup"><span data-stu-id="710f4-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="710f4-550">I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP.</span><span class="sxs-lookup"><span data-stu-id="710f4-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="710f4-551">Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:</span><span class="sxs-lookup"><span data-stu-id="710f4-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="710f4-552">Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un codice di errore 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="710f4-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="710f4-553">In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON.</span><span class="sxs-lookup"><span data-stu-id="710f4-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="710f4-554">La restituzione di `item` risulta in una risposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="710f4-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="710f4-555">Testare il metodo GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="710f4-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="710f4-556">Questa esercitazione usa Postman per testare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="710f4-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="710f4-557">Installare il [post](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="710f4-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="710f4-558">Avviare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="710f4-558">Start the web app.</span></span>
* <span data-ttu-id="710f4-559">Avviare Postman.</span><span class="sxs-lookup"><span data-stu-id="710f4-559">Start Postman.</span></span>
* <span data-ttu-id="710f4-560">Disabilitare la **Verifica del certificato SSL**.</span><span class="sxs-lookup"><span data-stu-id="710f4-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="710f4-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="710f4-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="710f4-562">Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).</span><span class="sxs-lookup"><span data-stu-id="710f4-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="710f4-563">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="710f4-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="710f4-564">Dalle preferenze del **poster**  >  **Preferences** (scheda**generale** ) disabilitare la **Verifica del certificato SSL**.</span><span class="sxs-lookup"><span data-stu-id="710f4-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="710f4-565">In alternativa, selezionare la chiave inglese e selezionare **Settings** (Impostazioni), quindi disabilitare la verifica del certificato SSL.</span><span class="sxs-lookup"><span data-stu-id="710f4-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="710f4-566">Riattivare la verifica dei certificati SSL al termine del test del controller.</span><span class="sxs-lookup"><span data-stu-id="710f4-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="710f4-567">Creare una nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="710f4-567">Create a new request.</span></span>
  * <span data-ttu-id="710f4-568">Impostare il metodo HTTP su **GET**.</span><span class="sxs-lookup"><span data-stu-id="710f4-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="710f4-569">Impostare l'URL della richiesta su `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="710f4-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="710f4-570">Ad esempio: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="710f4-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="710f4-571">Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.</span><span class="sxs-lookup"><span data-stu-id="710f4-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="710f4-572">Selezionare**Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="710f4-572">Select **Send**.</span></span>

![Postman con richiesta GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="710f4-574">Aggiungere un metodo Create</span><span class="sxs-lookup"><span data-stu-id="710f4-574">Add a Create method</span></span>

<span data-ttu-id="710f4-575">Aggiungere il metodo `PostTodoItem` seguente in *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="710f4-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="710f4-576">Il codice precedente è un metodo HTTP POST, come indicato dall' [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="710f4-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="710f4-577">Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="710f4-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="710f4-578">Il metodo `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="710f4-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="710f4-579">Restituisce un codice di stato HTTP 201 in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="710f4-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="710f4-580">HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="710f4-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="710f4-581">Aggiunge un'intestazione `Location` alla risposta.</span><span class="sxs-lookup"><span data-stu-id="710f4-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="710f4-582">L'intestazione `Location` specifica l'URI dell'elemento attività appena creato.</span><span class="sxs-lookup"><span data-stu-id="710f4-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="710f4-583">Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="710f4-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="710f4-584">Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`.</span><span class="sxs-lookup"><span data-stu-id="710f4-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="710f4-585">La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="710f4-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="710f4-586">Testare il metodo PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="710f4-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="710f4-587">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="710f4-587">Build the project.</span></span>
* <span data-ttu-id="710f4-588">In Postman impostare il metodo HTTP su `POST`.</span><span class="sxs-lookup"><span data-stu-id="710f4-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="710f4-589">Selezionare la scheda **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="710f4-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="710f4-590">Selezionare il pulsante di opzione **raw** (non elaborato).</span><span class="sxs-lookup"><span data-stu-id="710f4-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="710f4-591">Impostare il tipo su **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="710f4-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="710f4-592">Nel corpo della richiesta immettere codice JSON per un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="710f4-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="710f4-593">Selezionare**Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="710f4-593">Select **Send**.</span></span>

  ![Postman con richiesta di creazione](first-web-api/_static/create.png)

  <span data-ttu-id="710f4-595">Se viene visualizzato un errore HTTP 405 - Metodo non consentito, è probabile che il progetto non sia stato compilato dopo l'aggiunta del metodo `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="710f4-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="710f4-596">Testare l'URI dell'intestazione della posizione</span><span class="sxs-lookup"><span data-stu-id="710f4-596">Test the location header URI</span></span>

* <span data-ttu-id="710f4-597">Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).</span><span class="sxs-lookup"><span data-stu-id="710f4-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="710f4-598">Copiare il valore dell'intestazione **Location** (Posizione):</span><span class="sxs-lookup"><span data-stu-id="710f4-598">Copy the **Location** header value:</span></span>

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="710f4-600">Impostare il metodo su GET.</span><span class="sxs-lookup"><span data-stu-id="710f4-600">Set the method to GET.</span></span>
* <span data-ttu-id="710f4-601">Incollare l'URI (ad esempio, `https://localhost:5001/api/Todo/2` ).</span><span class="sxs-lookup"><span data-stu-id="710f4-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="710f4-602">Selezionare**Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="710f4-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="710f4-603">Aggiungere un metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="710f4-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="710f4-604">Aggiungere il metodo `PutTodoItem` seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="710f4-605">`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="710f4-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="710f4-606">La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="710f4-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="710f4-607">In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche.</span><span class="sxs-lookup"><span data-stu-id="710f4-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="710f4-608">Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="710f4-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="710f4-609">Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.</span><span class="sxs-lookup"><span data-stu-id="710f4-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="710f4-610">Testare il metodo PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="710f4-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="710f4-611">Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="710f4-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="710f4-612">Deve esistere un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="710f4-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="710f4-613">Chiamare GET per assicurarsi che sia presente un elemento nel database prima di eseguire una chiamata PUT.</span><span class="sxs-lookup"><span data-stu-id="710f4-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="710f4-614">Aggiornare l'elemento attività con id = 1 e impostarne il nome su "feed fish":</span><span class="sxs-lookup"><span data-stu-id="710f4-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="710f4-615">L'immagine seguente visualizza l'aggiornamento di Postman:</span><span class="sxs-lookup"><span data-stu-id="710f4-615">The following image shows the Postman update:</span></span>

![Console Postman con risposta 204 (No Content)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="710f4-617">Aggiungere un metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="710f4-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="710f4-618">Aggiungere il metodo `DeleteTodoItem` seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="710f4-619">La `DeleteTodoItem` risposta è [204 (nessun contenuto)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="710f4-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="710f4-620">Testare il metodo DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="710f4-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="710f4-621">Usare Postman per eliminare un elemento attività:</span><span class="sxs-lookup"><span data-stu-id="710f4-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="710f4-622">Impostare il metodo su `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="710f4-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="710f4-623">Impostare l'URI dell'oggetto da eliminare (ad esempio, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="710f4-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="710f4-624">Selezionare**Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="710f4-624">Select **Send**.</span></span>

<span data-ttu-id="710f4-625">L'app di esempio consente di eliminare tutti gli elementi.</span><span class="sxs-lookup"><span data-stu-id="710f4-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="710f4-626">Quando viene eliminato l'ultimo elemento, tuttavia, ne viene creato uno nuovo dal costruttore della classe modello alla successiva chiamata dell'API.</span><span class="sxs-lookup"><span data-stu-id="710f4-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="710f4-627">Chiamare l'API Web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="710f4-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="710f4-628">In questa sezione viene aggiunta una pagina HTML che usa JavaScript per chiamare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="710f4-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="710f4-629">jQuery avvia la richiesta.</span><span class="sxs-lookup"><span data-stu-id="710f4-629">jQuery initiates the request.</span></span> <span data-ttu-id="710f4-630">JavaScript aggiorna la pagina con i dettagli della risposta dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="710f4-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="710f4-631">Configurare l'app per [servire file statici](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [abilitare il mapping del file predefinito](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aggiornando *Startup.cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="710f4-632">Creare una cartella *wwwroot* nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="710f4-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="710f4-633">Aggiungere un file HTML denominato *index.html* alla directory *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="710f4-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="710f4-634">Sostituire il contenuto con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="710f4-635">Aggiungere un file JavaScript con nome *site.js* alla directory *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="710f4-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="710f4-636">Sostituire il contenuto con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="710f4-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="710f4-637">Può essere necessario modificare le impostazioni di avvio del progetto ASP.NET Core per il test della pagina HTML in locale:</span><span class="sxs-lookup"><span data-stu-id="710f4-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="710f4-638">Aprire *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="710f4-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="710f4-639">Rimuovere la `launchUrl` proprietà per forzare l'apertura dell'app nel file *index. html* &mdash; del file predefinito del progetto.</span><span class="sxs-lookup"><span data-stu-id="710f4-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="710f4-640">In questo esempio vengono chiamati tutti i metodi CRUD dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="710f4-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="710f4-641">Di seguito sono incluse le spiegazioni delle chiamate all'API.</span><span class="sxs-lookup"><span data-stu-id="710f4-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="710f4-642">Ottenere un elenco di elementi attività</span><span class="sxs-lookup"><span data-stu-id="710f4-642">Get a list of to-do items</span></span>

<span data-ttu-id="710f4-643">jQuery invia una richiesta HTTP GET all'API Web, che restituisce JSON che rappresenta una matrice di elementi attività.</span><span class="sxs-lookup"><span data-stu-id="710f4-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="710f4-644">La funzione di callback `success` viene chiamata se la richiesta ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="710f4-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="710f4-645">Nel callback il modello DOM viene aggiornato con le informazioni dell'elemento attività.</span><span class="sxs-lookup"><span data-stu-id="710f4-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="710f4-646">Aggiungere un elemento attività</span><span class="sxs-lookup"><span data-stu-id="710f4-646">Add a to-do item</span></span>

<span data-ttu-id="710f4-647">jQuery invia una richiesta HTTP POST con l'elemento attività nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="710f4-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="710f4-648">Le opzioni `accepts` e `contentType` sono impostate su `application/json` per specificare il tipo di supporto ricevuto e inviato.</span><span class="sxs-lookup"><span data-stu-id="710f4-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="710f4-649">L'elemento attività viene convertito in JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="710f4-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="710f4-650">Quando l'API restituisce un codice di stato di esecuzione riuscita, viene chiamata la funzione `getData` per aggiornare la tabella HTML.</span><span class="sxs-lookup"><span data-stu-id="710f4-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="710f4-651">Aggiornare un elemento attività</span><span class="sxs-lookup"><span data-stu-id="710f4-651">Update a to-do item</span></span>

<span data-ttu-id="710f4-652">L'aggiornamento di un elemento attività è simile all'aggiunta di un elemento di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="710f4-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="710f4-653">L'`url` cambia per includere l'identificatore univoco dell'elemento e `type` è `PUT`.</span><span class="sxs-lookup"><span data-stu-id="710f4-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="710f4-654">Eliminare un elemento attività</span><span class="sxs-lookup"><span data-stu-id="710f4-654">Delete a to-do item</span></span>

<span data-ttu-id="710f4-655">È possibile eliminare un elemento attività impostando `type` su `DELETE` nella chiamata AJAX e specificando l'identificatore univoco dell'elemento nell'URL.</span><span class="sxs-lookup"><span data-stu-id="710f4-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="710f4-656">Aggiungere il supporto per l'autenticazione a un'API Web</span><span class="sxs-lookup"><span data-stu-id="710f4-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="710f4-657">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="710f4-657">Additional resources</span></span>

<span data-ttu-id="710f4-658">[Visualizzare o scaricare il codice di esempio per questa esercitazione](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="710f4-658">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="710f4-659">Vedere [come scaricare un esempio](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="710f4-659">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="710f4-660">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="710f4-660">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="710f4-661">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="710f4-661">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
