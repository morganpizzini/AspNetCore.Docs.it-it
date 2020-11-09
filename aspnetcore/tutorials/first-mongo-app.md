---
title: Creare un'API Web con ASP.NET Core e MongoDB
author: prkhandelwal
description: Questa esercitazione illustra come creare un'API Web ASP.NET Core con un database NoSQL MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/first-mongo-app
ms.openlocfilehash: 350df417886fe1ea5fef89dc221c217d596768b3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060742"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="29767-103">Creare un'API Web con ASP.NET Core e MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="29767-104">Di [Pratik Khandelwal](https://twitter.com/K2Prk) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="29767-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="29767-105">Questa esercitazione crea un'API Web che esegue operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) su un database NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="29767-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="29767-106">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="29767-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="29767-107">Configurare MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-107">Configure MongoDB</span></span>
> * <span data-ttu-id="29767-108">Creare un database MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="29767-109">Definire una raccolta e uno schema MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="29767-110">Eseguire operazioni CRUD di MongoDB da un'API Web</span><span class="sxs-lookup"><span data-stu-id="29767-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="29767-111">Personalizzare la serializzazione JSON</span><span class="sxs-lookup"><span data-stu-id="29767-111">Customize JSON serialization</span></span>

<span data-ttu-id="29767-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="29767-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="29767-113">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="29767-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29767-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29767-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="29767-115">.NET Core SDK 3.0 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="29767-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="29767-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con il carico di lavoro **sviluppo di ASP.NET e Web**</span><span class="sxs-lookup"><span data-stu-id="29767-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="29767-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="29767-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29767-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="29767-119">.NET Core SDK 3.0 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="29767-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="29767-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29767-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="29767-121">C# per Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29767-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="29767-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29767-123">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="29767-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="29767-124">.NET Core SDK 3.0 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="29767-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="29767-125">Visual Studio per Mac versione 7.7 o successiva</span><span class="sxs-lookup"><span data-stu-id="29767-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="29767-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="29767-127">Configurare MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-127">Configure MongoDB</span></span>

<span data-ttu-id="29767-128">Se si usa Windows, MongoDB viene installato in *C: \\ programmi \\ MongoDB* per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="29767-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="29767-129">Aggiungere *C: \\ Program Files \\ MongoDB \\ server \\ \<version_number> \\ bin* alla `Path` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="29767-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="29767-130">Questa modifica consente l'accesso MongoDB da qualsiasi posizione nel computer di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="29767-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="29767-131">Usare la shell mongo nelle procedure seguenti per creare un database, creare le raccolte e archiviare i documenti.</span><span class="sxs-lookup"><span data-stu-id="29767-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="29767-132">Per altre informazioni sui comandi della shell mongo, vedere [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell) (Utilizzo della shell mongo).</span><span class="sxs-lookup"><span data-stu-id="29767-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="29767-133">Scegliere una directory nel computer di sviluppo per archiviare i dati.</span><span class="sxs-lookup"><span data-stu-id="29767-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="29767-134">Ad esempio, *C: \\ BooksData* in Windows.</span><span class="sxs-lookup"><span data-stu-id="29767-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="29767-135">Creare la directory se non esiste.</span><span class="sxs-lookup"><span data-stu-id="29767-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="29767-136">La shell mongo non consente di creare nuove directory.</span><span class="sxs-lookup"><span data-stu-id="29767-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="29767-137">Aprire una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="29767-137">Open a command shell.</span></span> <span data-ttu-id="29767-138">Eseguire il comando seguente per connettersi a MongoDB sulla porta predefinita 27017.</span><span class="sxs-lookup"><span data-stu-id="29767-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="29767-139">Ricordare di sostituire `<data_directory_path>` con la directory scelta nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="29767-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="29767-140">Aprire un'altra istanza della shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="29767-140">Open another command shell instance.</span></span> <span data-ttu-id="29767-141">Connettersi al database di test predefinito eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="29767-142">Eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="29767-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="29767-143">Se non esiste già, viene creato un database denominato *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="29767-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="29767-144">Se il database esiste, la connessione viene aperta per le transazioni.</span><span class="sxs-lookup"><span data-stu-id="29767-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="29767-145">Creare una raccolta `Books` tramite il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="29767-146">Viene visualizzato il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="29767-147">Definire uno schema per la raccolta `Books` e inserire due documenti usando il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="29767-148">Viene visualizzato il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="29767-149">L'ID illustrato in questo articolo non corrisponde agli ID quando si esegue questo campione.</span><span class="sxs-lookup"><span data-stu-id="29767-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="29767-150">Visualizzare i documenti nel database usando il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="29767-151">Viene visualizzato il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="29767-152">Lo schema aggiunge una proprietà `_id` generata automaticamente di tipo `ObjectId` per ogni documento.</span><span class="sxs-lookup"><span data-stu-id="29767-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="29767-153">Il database è pronto.</span><span class="sxs-lookup"><span data-stu-id="29767-153">The database is ready.</span></span> <span data-ttu-id="29767-154">È possibile iniziare a creare l'API Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="29767-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="29767-155">Creare il progetto per l'API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29767-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29767-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29767-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="29767-157">Passare a **file** > **nuovo** > **progetto** .</span><span class="sxs-lookup"><span data-stu-id="29767-157">Go to **File** > **New** > **Project** .</span></span>
1. <span data-ttu-id="29767-158">Selezionare il tipo di progetto **Applicazione Web ASP.NET Core** e selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="29767-158">Select the **ASP.NET Core Web Application** project type, and select **Next** .</span></span>
1. <span data-ttu-id="29767-159">Assegnare al progetto il nome *BooksApi* e selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="29767-159">Name the project *BooksApi* , and select **Create** .</span></span>
1. <span data-ttu-id="29767-160">Selezionare il framework di destinazione **.NET Core** e **ASP.NET Core 3.0** .</span><span class="sxs-lookup"><span data-stu-id="29767-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0** .</span></span> <span data-ttu-id="29767-161">Selezionare il modello di progetto **API** e scegliere **Crea** .</span><span class="sxs-lookup"><span data-stu-id="29767-161">Select the **API** project template, and select **Create** .</span></span>
1. <span data-ttu-id="29767-162">Visitare la [raccolta NuGet: MongoDB. driver](https://www.nuget.org/packages/MongoDB.Driver/) per determinare la versione stabile più recente del driver .NET per MongoDB.</span><span class="sxs-lookup"><span data-stu-id="29767-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="29767-163">Nella finestra **Console di Gestione pacchetti** passare alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="29767-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="29767-164">Eseguire il comando seguente per installare il driver .NET per MongoDB:</span><span class="sxs-lookup"><span data-stu-id="29767-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="29767-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29767-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="29767-166">Eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="29767-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="29767-167">Un nuovo progetto API Web ASP.NET Core destinato a .NET Core viene generato e aperto in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="29767-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="29767-168">Dopo che l'icona di OmniSharp Flame della barra di stato è verde, una finestra di dialogo richiede che le **risorse necessarie per la compilazione e il debug siano mancanti da' BooksApi '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="29767-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?** .</span></span> <span data-ttu-id="29767-169">Selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="29767-169">Select **Yes** .</span></span>
1. <span data-ttu-id="29767-170">Visitare la [raccolta NuGet: MongoDB. driver](https://www.nuget.org/packages/MongoDB.Driver/) per determinare la versione stabile più recente del driver .NET per MongoDB.</span><span class="sxs-lookup"><span data-stu-id="29767-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="29767-171">Aprire **Terminale integrato** e passare alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="29767-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="29767-172">Eseguire il comando seguente per installare il driver .NET per MongoDB:</span><span class="sxs-lookup"><span data-stu-id="29767-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29767-173">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="29767-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="29767-174">In Visual Studio per Mac precedente alla versione 8,6, selezionare **file**  >  **nuova soluzione**  >  **.NET Core**  >  **app** dall'intestazione laterale.</span><span class="sxs-lookup"><span data-stu-id="29767-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="29767-175">Nella versione 8,6 o successive selezionare **file**  >  **nuova soluzione**  >  **Web e**  >  **app** console dalla barra laterale.</span><span class="sxs-lookup"><span data-stu-id="29767-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="29767-176">Selezionare il **ASP.NET Core** > modello di progetto **API** ASP.NET Core C# e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="29767-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next** .</span></span>
1. <span data-ttu-id="29767-177">Selezionare **.NET Core 3,1** dall'elenco a discesa **Framework di destinazione** e quindi fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="29767-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next** .</span></span>
1. <span data-ttu-id="29767-178">Immettere *BooksApi* per **Nome progetto** e selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="29767-178">Enter *BooksApi* for the **Project Name** , and select **Create** .</span></span>
1. <span data-ttu-id="29767-179">Nel riquadro **Soluzione** fare clic con il pulsante destro del mouse sul nodo **Dipendenze** del progetto e scegliere **Aggiungi pacchetti** .</span><span class="sxs-lookup"><span data-stu-id="29767-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages** .</span></span>
1. <span data-ttu-id="29767-180">Immettere *MongoDB.Driver* nella casella di ricerca, selezionare il pacchetto *MongoDB.Driver* e quindi **Aggiungi pacchetto** .</span><span class="sxs-lookup"><span data-stu-id="29767-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package** .</span></span>
1. <span data-ttu-id="29767-181">Selezionare il pulsante **Accetta** nella finestra di dialogo **Accettazione della licenza** .</span><span class="sxs-lookup"><span data-stu-id="29767-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="29767-182">Aggiungere un modello di entità</span><span class="sxs-lookup"><span data-stu-id="29767-182">Add an entity model</span></span>

1. <span data-ttu-id="29767-183">Aggiungere una directory *Models* alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="29767-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="29767-184">Aggiungere una classe `Book` alla directory *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="29767-185">Nella classe precedente, la proprietà `Id`:</span><span class="sxs-lookup"><span data-stu-id="29767-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="29767-186">È obbligatoria per il mapping tra l'oggetto CLR (Common Language Runtime) e la raccolta MongoDB.</span><span class="sxs-lookup"><span data-stu-id="29767-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="29767-187">Viene annotato con [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) per indicare questa proprietà come chiave primaria del documento.</span><span class="sxs-lookup"><span data-stu-id="29767-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="29767-188">Viene annotato con [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) per consentire il passaggio del parametro come tipo `string` anziché come una struttura [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="29767-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="29767-189">Mongo gestisce la conversione da `string` a `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="29767-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="29767-190">La `BookName` proprietà è annotata con l' [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attributo.</span><span class="sxs-lookup"><span data-stu-id="29767-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="29767-191">Il valore dell'attributo `Name` rappresenta il nome della proprietà nella raccolta MongoDB.</span><span class="sxs-lookup"><span data-stu-id="29767-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="29767-192">Aggiungere un modello di configurazione</span><span class="sxs-lookup"><span data-stu-id="29767-192">Add a configuration model</span></span>

1. <span data-ttu-id="29767-193">Aggiungere i valori di configurazione del database seguenti a *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="29767-193">Add the following database configuration values to *appsettings.json* :</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="29767-194">Aggiungere un file *BookstoreDatabaseSettings.cs* alla directory *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="29767-195">La `BookstoreDatabaseSettings` classe precedente viene utilizzata per archiviare i *appsettings.json* valori delle `BookstoreDatabaseSettings` proprietà del file.</span><span class="sxs-lookup"><span data-stu-id="29767-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="29767-196">I nomi delle proprietà JSON e C# sono identici per semplificare il processo di mapping.</span><span class="sxs-lookup"><span data-stu-id="29767-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="29767-197">Aggiungere il codice evidenziato seguente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="29767-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="29767-198">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="29767-198">In the preceding code:</span></span>

   * <span data-ttu-id="29767-199">L'istanza di configurazione a cui *appsettings.json* `BookstoreDatabaseSettings` viene associata la sezione del file è registrata nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="29767-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="29767-200">Ad esempio, la `BookstoreDatabaseSettings` proprietà di un oggetto `ConnectionString` viene popolata con la `BookstoreDatabaseSettings:ConnectionString` Proprietà in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="29767-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json* .</span></span>
   * <span data-ttu-id="29767-201">L'interfaccia `IBookstoreDatabaseSettings` è registrata nell'inserimento di dipendenze con una [durata del servizio](xref:fundamentals/dependency-injection#service-lifetimes) singleton.</span><span class="sxs-lookup"><span data-stu-id="29767-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="29767-202">Quando avviene l'inserimento, l'istanza dell'interfaccia restituisce un oggetto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="29767-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="29767-203">Aggiungere il codice seguente all'inizio del file *Startup.cs* per risolvere i riferimenti a `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="29767-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="29767-204">Aggiungere un servizio di operazioni CRUD</span><span class="sxs-lookup"><span data-stu-id="29767-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="29767-205">Aggiungere una directory *Services* alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="29767-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="29767-206">Aggiungere una classe `BookService` alla directory *Services* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="29767-207">Nel codice precedente un'istanza di `IBookstoreDatabaseSettings` viene recuperata dall'inserimento di dipendenze tramite l'inserimento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="29767-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="29767-208">Questa tecnica consente di accedere ai *appsettings.json* valori di configurazione aggiunti nella sezione [aggiungere un modello di configurazione](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="29767-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="29767-209">Aggiungere il codice evidenziato seguente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="29767-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="29767-210">Nel codice precedente la classe `BookService` è registrata con l'inserimento di dipendenze per supportare l'inserimento del costruttore nelle classi che la utilizzano.</span><span class="sxs-lookup"><span data-stu-id="29767-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="29767-211">La durata del servizio singleton è più appropriata perché `BookService` assume una dipendenza diretta a `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="29767-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="29767-212">Le [linee guida per il riutilizzo dei client mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)ufficiali `MongoClient` devono essere registrate in di con una durata del servizio singleton.</span><span class="sxs-lookup"><span data-stu-id="29767-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="29767-213">Aggiungere il codice seguente all'inizio del file *Startup.cs* per risolvere il riferimento a `BookService`:</span><span class="sxs-lookup"><span data-stu-id="29767-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="29767-214">La classe `BookService` usa i membri `MongoDB.Driver` seguenti per eseguire operazioni CRUD sul database:</span><span class="sxs-lookup"><span data-stu-id="29767-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="29767-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): legge l'istanza del server per l'esecuzione di operazioni di database.</span><span class="sxs-lookup"><span data-stu-id="29767-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="29767-216">Al costruttore di questa classe viene passata la stringa di connessione MongoDB:</span><span class="sxs-lookup"><span data-stu-id="29767-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="29767-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): rappresenta il database Mongo per l'esecuzione di operazioni.</span><span class="sxs-lookup"><span data-stu-id="29767-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="29767-218">Questa esercitazione usa il metodo [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) generico sull'interfaccia per ottenere l'accesso ai dati in una raccolta specifica.</span><span class="sxs-lookup"><span data-stu-id="29767-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="29767-219">Eseguire le operazioni CRUD sulla raccolta dopo la chiamata a questo metodo.</span><span class="sxs-lookup"><span data-stu-id="29767-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="29767-220">Nella chiamata del metodo `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="29767-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="29767-221">`collection` rappresenta il nome della raccolta.</span><span class="sxs-lookup"><span data-stu-id="29767-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="29767-222">`TDocument` rappresenta il tipo di oggetto CLR archiviato nella raccolta.</span><span class="sxs-lookup"><span data-stu-id="29767-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="29767-223">`GetCollection<TDocument>(collection)` restituisce un oggetto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) che rappresenta la raccolta.</span><span class="sxs-lookup"><span data-stu-id="29767-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="29767-224">In questa esercitazione, vengono richiamati i metodi seguenti sulla raccolta:</span><span class="sxs-lookup"><span data-stu-id="29767-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="29767-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Elimina un singolo documento corrispondente ai criteri di ricerca specificati.</span><span class="sxs-lookup"><span data-stu-id="29767-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="29767-226">[Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): restituisce tutti i documenti nella raccolta che corrispondono ai criteri di ricerca specificati.</span><span class="sxs-lookup"><span data-stu-id="29767-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="29767-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): inserisce l'oggetto fornito come nuovo documento nella raccolta.</span><span class="sxs-lookup"><span data-stu-id="29767-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="29767-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): sostituisce il singolo documento corrispondente ai criteri di ricerca specificati con l'oggetto fornito.</span><span class="sxs-lookup"><span data-stu-id="29767-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="29767-229">Aggiunta di un controller</span><span class="sxs-lookup"><span data-stu-id="29767-229">Add a controller</span></span>

<span data-ttu-id="29767-230">Aggiungere una classe `BooksController` alla directory *Controllers* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="29767-231">Il controller dell'API Web precedente:</span><span class="sxs-lookup"><span data-stu-id="29767-231">The preceding web API controller:</span></span>

* <span data-ttu-id="29767-232">Usa la classe `BookService` per eseguire operazioni CRUD.</span><span class="sxs-lookup"><span data-stu-id="29767-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="29767-233">Contiene metodi di azione per supportare le richieste HTTP GET, POST, PUT e DELETE.</span><span class="sxs-lookup"><span data-stu-id="29767-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="29767-234">Chiama <xref:System.Web.Http.ApiController.CreatedAtRoute*> nel metodo dell'azione `Create` per restituire una risposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="29767-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="29767-235">Il codice di stato 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="29767-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="29767-236">`Location` aggiunge anche un'intestazione `CreatedAtRoute` alla risposta.</span><span class="sxs-lookup"><span data-stu-id="29767-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="29767-237">L'intestazione `Location` specifica l'URI del libro appena creato.</span><span class="sxs-lookup"><span data-stu-id="29767-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="29767-238">Testare l'API Web</span><span class="sxs-lookup"><span data-stu-id="29767-238">Test the web API</span></span>

1. <span data-ttu-id="29767-239">Compilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="29767-239">Build and run the app.</span></span>

1. <span data-ttu-id="29767-240">Passare a `http://localhost:<port>/api/books` per testare il metodo dell'azione `Get` senza parametri del controller.</span><span class="sxs-lookup"><span data-stu-id="29767-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="29767-241">Viene visualizzata la risposta JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-241">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="29767-242">Passare a `http://localhost:<port>/api/books/{id here}` per testare il metodo dell'azione `Get` in overload del controller.</span><span class="sxs-lookup"><span data-stu-id="29767-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="29767-243">Viene visualizzata la risposta JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="29767-244">Configurare le opzioni di serializzazione JSON</span><span class="sxs-lookup"><span data-stu-id="29767-244">Configure JSON serialization options</span></span>

<span data-ttu-id="29767-245">Esistono due dettagli da modificare per le risposte JSON restituite nella sezione [Testare l'API Web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="29767-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="29767-246">La notazione a cammello predefinita per i nomi di proprietà deve essere modificata in modo da adottare la convenzione Pascal dei nomi di proprietà dell'oggetto CLR.</span><span class="sxs-lookup"><span data-stu-id="29767-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="29767-247">La proprietà `bookName` deve essere restituita come `Name`.</span><span class="sxs-lookup"><span data-stu-id="29767-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="29767-248">Per soddisfare i requisiti precedenti, apportare le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="29767-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="29767-249">JSON.NET è stato rimosso dal framework condiviso di ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="29767-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="29767-250">Aggiungere un riferimento al pacchetto a [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) .</span><span class="sxs-lookup"><span data-stu-id="29767-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="29767-251">In `Startup.ConfigureServices` concatenare il codice evidenziato seguente alla chiamata del metodo `AddControllers`:</span><span class="sxs-lookup"><span data-stu-id="29767-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="29767-252">Con la modifica precedente, i nomi delle proprietà nella risposta JSON serializzata dell'API Web corrispondono ai nomi di proprietà corrispondenti nel tipo di oggetto CLR.</span><span class="sxs-lookup"><span data-stu-id="29767-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="29767-253">Ad esempio, la proprietà `Author` della classe `Book` viene serializzata come `Author`.</span><span class="sxs-lookup"><span data-stu-id="29767-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="29767-254">In *models/book. cs* annotare la `BookName` proprietà con l' [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attributo seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-254">In *Models/Book.cs* , annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="29767-255">Il valore `Name` dell'attributo `[JsonProperty]` rappresenta il nome della proprietà nella risposta JSON serializzata dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="29767-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="29767-256">Aggiungere il codice seguente all'inizio di *Models/Book.cs* per risolvere il riferimento all'attributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="29767-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="29767-257">Ripetere i passaggi definiti nella sezione [Testare l'API Web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="29767-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="29767-258">Si noti la differenza nei nomi di proprietà JSON.</span><span class="sxs-lookup"><span data-stu-id="29767-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="29767-259">Questa esercitazione crea un'API Web che esegue operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) su un database NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="29767-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="29767-260">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="29767-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="29767-261">Configurare MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-261">Configure MongoDB</span></span>
> * <span data-ttu-id="29767-262">Creare un database MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="29767-263">Definire una raccolta e uno schema MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="29767-264">Eseguire operazioni CRUD di MongoDB da un'API Web</span><span class="sxs-lookup"><span data-stu-id="29767-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="29767-265">Personalizzare la serializzazione JSON</span><span class="sxs-lookup"><span data-stu-id="29767-265">Customize JSON serialization</span></span>

<span data-ttu-id="29767-266">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="29767-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="29767-267">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="29767-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29767-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29767-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="29767-269">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="29767-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="29767-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con il carico di lavoro **sviluppo di ASP.NET e Web**</span><span class="sxs-lookup"><span data-stu-id="29767-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="29767-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="29767-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29767-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="29767-273">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="29767-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="29767-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29767-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="29767-275">C# per Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29767-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="29767-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29767-277">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="29767-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="29767-278">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="29767-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="29767-279">Visual Studio per Mac versione 7.7 o successiva</span><span class="sxs-lookup"><span data-stu-id="29767-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="29767-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="29767-281">Configurare MongoDB</span><span class="sxs-lookup"><span data-stu-id="29767-281">Configure MongoDB</span></span>

<span data-ttu-id="29767-282">Se si usa Windows, MongoDB viene installato in *C: \\ programmi \\ MongoDB* per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="29767-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="29767-283">Aggiungere *C: \\ Program Files \\ MongoDB \\ server \\ \<version_number> \\ bin* alla `Path` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="29767-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="29767-284">Questa modifica consente l'accesso MongoDB da qualsiasi posizione nel computer di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="29767-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="29767-285">Usare la shell mongo nelle procedure seguenti per creare un database, creare le raccolte e archiviare i documenti.</span><span class="sxs-lookup"><span data-stu-id="29767-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="29767-286">Per altre informazioni sui comandi della shell mongo, vedere [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell) (Utilizzo della shell mongo).</span><span class="sxs-lookup"><span data-stu-id="29767-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="29767-287">Scegliere una directory nel computer di sviluppo per archiviare i dati.</span><span class="sxs-lookup"><span data-stu-id="29767-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="29767-288">Ad esempio, *C: \\ BooksData* in Windows.</span><span class="sxs-lookup"><span data-stu-id="29767-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="29767-289">Creare la directory se non esiste.</span><span class="sxs-lookup"><span data-stu-id="29767-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="29767-290">La shell mongo non consente di creare nuove directory.</span><span class="sxs-lookup"><span data-stu-id="29767-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="29767-291">Aprire una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="29767-291">Open a command shell.</span></span> <span data-ttu-id="29767-292">Eseguire il comando seguente per connettersi a MongoDB sulla porta predefinita 27017.</span><span class="sxs-lookup"><span data-stu-id="29767-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="29767-293">Ricordare di sostituire `<data_directory_path>` con la directory scelta nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="29767-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="29767-294">Aprire un'altra istanza della shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="29767-294">Open another command shell instance.</span></span> <span data-ttu-id="29767-295">Connettersi al database di test predefinito eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="29767-296">Eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="29767-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="29767-297">Se non esiste già, viene creato un database denominato *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="29767-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="29767-298">Se il database esiste, la connessione viene aperta per le transazioni.</span><span class="sxs-lookup"><span data-stu-id="29767-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="29767-299">Creare una raccolta `Books` tramite il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="29767-300">Viene visualizzato il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="29767-301">Definire uno schema per la raccolta `Books` e inserire due documenti usando il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="29767-302">Viene visualizzato il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-302">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="29767-303">L'ID illustrato in questo articolo non corrisponde agli ID quando si esegue questo campione.</span><span class="sxs-lookup"><span data-stu-id="29767-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="29767-304">Visualizzare i documenti nel database usando il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="29767-305">Viene visualizzato il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-305">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="29767-306">Lo schema aggiunge una proprietà `_id` generata automaticamente di tipo `ObjectId` per ogni documento.</span><span class="sxs-lookup"><span data-stu-id="29767-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="29767-307">Il database è pronto.</span><span class="sxs-lookup"><span data-stu-id="29767-307">The database is ready.</span></span> <span data-ttu-id="29767-308">È possibile iniziare a creare l'API Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="29767-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="29767-309">Creare il progetto per l'API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29767-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29767-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29767-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="29767-311">Passare a **file** > **nuovo** > **progetto** .</span><span class="sxs-lookup"><span data-stu-id="29767-311">Go to **File** > **New** > **Project** .</span></span>
1. <span data-ttu-id="29767-312">Selezionare il tipo di progetto **Applicazione Web ASP.NET Core** e selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="29767-312">Select the **ASP.NET Core Web Application** project type, and select **Next** .</span></span>
1. <span data-ttu-id="29767-313">Assegnare al progetto il nome *BooksApi* e selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="29767-313">Name the project *BooksApi* , and select **Create** .</span></span>
1. <span data-ttu-id="29767-314">Selezionare il framework di destinazione **.NET Core** e **ASP.NET Core 2.2** .</span><span class="sxs-lookup"><span data-stu-id="29767-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2** .</span></span> <span data-ttu-id="29767-315">Selezionare il modello di progetto **API** e scegliere **Crea** .</span><span class="sxs-lookup"><span data-stu-id="29767-315">Select the **API** project template, and select **Create** .</span></span>
1. <span data-ttu-id="29767-316">Visitare la [raccolta NuGet: MongoDB. driver](https://www.nuget.org/packages/MongoDB.Driver/) per determinare la versione stabile più recente del driver .NET per MongoDB.</span><span class="sxs-lookup"><span data-stu-id="29767-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="29767-317">Nella finestra **Console di Gestione pacchetti** passare alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="29767-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="29767-318">Eseguire il comando seguente per installare il driver .NET per MongoDB:</span><span class="sxs-lookup"><span data-stu-id="29767-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="29767-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29767-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="29767-320">Eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="29767-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="29767-321">Un nuovo progetto API Web ASP.NET Core destinato a .NET Core viene generato e aperto in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="29767-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="29767-322">Dopo che l'icona di OmniSharp Flame della barra di stato è verde, una finestra di dialogo richiede che le **risorse necessarie per la compilazione e il debug siano mancanti da' BooksApi '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="29767-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?** .</span></span> <span data-ttu-id="29767-323">Selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="29767-323">Select **Yes** .</span></span>
1. <span data-ttu-id="29767-324">Visitare la [raccolta NuGet: MongoDB. driver](https://www.nuget.org/packages/MongoDB.Driver/) per determinare la versione stabile più recente del driver .NET per MongoDB.</span><span class="sxs-lookup"><span data-stu-id="29767-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="29767-325">Aprire **Terminale integrato** e passare alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="29767-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="29767-326">Eseguire il comando seguente per installare il driver .NET per MongoDB:</span><span class="sxs-lookup"><span data-stu-id="29767-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="29767-327">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="29767-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="29767-328">In Visual Studio per Mac precedente alla versione 8,6, selezionare **file**  >  **nuova soluzione**  >  **.NET Core**  >  **app** dall'intestazione laterale.</span><span class="sxs-lookup"><span data-stu-id="29767-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="29767-329">Nella versione 8,6 o successive selezionare **file**  >  **nuova soluzione**  >  **Web e**  >  **app** console dalla barra laterale.</span><span class="sxs-lookup"><span data-stu-id="29767-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="29767-330">Selezionare il modello di progetto C# **API Web ASP.NET Core** e selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="29767-330">Select the **ASP.NET Core Web API** C# project template, and select **Next** .</span></span>
1. <span data-ttu-id="29767-331">Selezionare **.NET Core 2.2** nell'elenco a discesa **Framework di destinazione** e selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="29767-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next** .</span></span>
1. <span data-ttu-id="29767-332">Immettere *BooksApi* per **Nome progetto** e selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="29767-332">Enter *BooksApi* for the **Project Name** , and select **Create** .</span></span>
1. <span data-ttu-id="29767-333">Nel riquadro **Soluzione** fare clic con il pulsante destro del mouse sul nodo **Dipendenze** del progetto e scegliere **Aggiungi pacchetti** .</span><span class="sxs-lookup"><span data-stu-id="29767-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages** .</span></span>
1. <span data-ttu-id="29767-334">Immettere *MongoDB.Driver* nella casella di ricerca, selezionare il pacchetto *MongoDB.Driver* e quindi **Aggiungi pacchetto** .</span><span class="sxs-lookup"><span data-stu-id="29767-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package** .</span></span>
1. <span data-ttu-id="29767-335">Selezionare il pulsante **Accetta** nella finestra di dialogo **Accettazione della licenza** .</span><span class="sxs-lookup"><span data-stu-id="29767-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="29767-336">Aggiungere un modello di entità</span><span class="sxs-lookup"><span data-stu-id="29767-336">Add an entity model</span></span>

1. <span data-ttu-id="29767-337">Aggiungere una directory *Models* alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="29767-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="29767-338">Aggiungere una classe `Book` alla directory *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="29767-339">Nella classe precedente, la proprietà `Id`:</span><span class="sxs-lookup"><span data-stu-id="29767-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="29767-340">È obbligatoria per il mapping tra l'oggetto CLR (Common Language Runtime) e la raccolta MongoDB.</span><span class="sxs-lookup"><span data-stu-id="29767-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="29767-341">Viene annotato con [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) per indicare questa proprietà come chiave primaria del documento.</span><span class="sxs-lookup"><span data-stu-id="29767-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="29767-342">Viene annotato con [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) per consentire il passaggio del parametro come tipo `string` anziché come una struttura [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="29767-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="29767-343">Mongo gestisce la conversione da `string` a `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="29767-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="29767-344">La `BookName` proprietà è annotata con l' [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attributo.</span><span class="sxs-lookup"><span data-stu-id="29767-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="29767-345">Il valore dell'attributo `Name` rappresenta il nome della proprietà nella raccolta MongoDB.</span><span class="sxs-lookup"><span data-stu-id="29767-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="29767-346">Aggiungere un modello di configurazione</span><span class="sxs-lookup"><span data-stu-id="29767-346">Add a configuration model</span></span>

1. <span data-ttu-id="29767-347">Aggiungere i valori di configurazione del database seguenti a *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="29767-347">Add the following database configuration values to *appsettings.json* :</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="29767-348">Aggiungere un file *BookstoreDatabaseSettings.cs* alla directory *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="29767-349">La `BookstoreDatabaseSettings` classe precedente viene utilizzata per archiviare i *appsettings.json* valori delle `BookstoreDatabaseSettings` proprietà del file.</span><span class="sxs-lookup"><span data-stu-id="29767-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="29767-350">I nomi delle proprietà JSON e C# sono identici per semplificare il processo di mapping.</span><span class="sxs-lookup"><span data-stu-id="29767-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="29767-351">Aggiungere il codice evidenziato seguente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="29767-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="29767-352">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="29767-352">In the preceding code:</span></span>

   * <span data-ttu-id="29767-353">L'istanza di configurazione a cui *appsettings.json* `BookstoreDatabaseSettings` viene associata la sezione del file è registrata nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="29767-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="29767-354">Ad esempio, la `BookstoreDatabaseSettings` proprietà di un oggetto `ConnectionString` viene popolata con la `BookstoreDatabaseSettings:ConnectionString` Proprietà in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="29767-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json* .</span></span>
   * <span data-ttu-id="29767-355">L'interfaccia `IBookstoreDatabaseSettings` è registrata nell'inserimento di dipendenze con una [durata del servizio](xref:fundamentals/dependency-injection#service-lifetimes) singleton.</span><span class="sxs-lookup"><span data-stu-id="29767-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="29767-356">Quando avviene l'inserimento, l'istanza dell'interfaccia restituisce un oggetto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="29767-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="29767-357">Aggiungere il codice seguente all'inizio del file *Startup.cs* per risolvere i riferimenti a `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="29767-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="29767-358">Aggiungere un servizio di operazioni CRUD</span><span class="sxs-lookup"><span data-stu-id="29767-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="29767-359">Aggiungere una directory *Services* alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="29767-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="29767-360">Aggiungere una classe `BookService` alla directory *Services* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="29767-361">Nel codice precedente un'istanza di `IBookstoreDatabaseSettings` viene recuperata dall'inserimento di dipendenze tramite l'inserimento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="29767-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="29767-362">Questa tecnica consente di accedere ai *appsettings.json* valori di configurazione aggiunti nella sezione [aggiungere un modello di configurazione](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="29767-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="29767-363">Aggiungere il codice evidenziato seguente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="29767-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="29767-364">Nel codice precedente la classe `BookService` è registrata con l'inserimento di dipendenze per supportare l'inserimento del costruttore nelle classi che la utilizzano.</span><span class="sxs-lookup"><span data-stu-id="29767-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="29767-365">La durata del servizio singleton è più appropriata perché `BookService` assume una dipendenza diretta a `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="29767-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="29767-366">Le [linee guida per il riutilizzo dei client mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)ufficiali `MongoClient` devono essere registrate in di con una durata del servizio singleton.</span><span class="sxs-lookup"><span data-stu-id="29767-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="29767-367">Aggiungere il codice seguente all'inizio del file *Startup.cs* per risolvere il riferimento a `BookService`:</span><span class="sxs-lookup"><span data-stu-id="29767-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="29767-368">La classe `BookService` usa i membri `MongoDB.Driver` seguenti per eseguire operazioni CRUD sul database:</span><span class="sxs-lookup"><span data-stu-id="29767-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="29767-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): legge l'istanza del server per l'esecuzione di operazioni di database.</span><span class="sxs-lookup"><span data-stu-id="29767-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="29767-370">Al costruttore di questa classe viene passata la stringa di connessione MongoDB:</span><span class="sxs-lookup"><span data-stu-id="29767-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="29767-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): rappresenta il database Mongo per l'esecuzione di operazioni.</span><span class="sxs-lookup"><span data-stu-id="29767-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="29767-372">Questa esercitazione usa il metodo [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) generico sull'interfaccia per ottenere l'accesso ai dati in una raccolta specifica.</span><span class="sxs-lookup"><span data-stu-id="29767-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="29767-373">Eseguire le operazioni CRUD sulla raccolta dopo la chiamata a questo metodo.</span><span class="sxs-lookup"><span data-stu-id="29767-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="29767-374">Nella chiamata del metodo `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="29767-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="29767-375">`collection` rappresenta il nome della raccolta.</span><span class="sxs-lookup"><span data-stu-id="29767-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="29767-376">`TDocument` rappresenta il tipo di oggetto CLR archiviato nella raccolta.</span><span class="sxs-lookup"><span data-stu-id="29767-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="29767-377">`GetCollection<TDocument>(collection)` restituisce un oggetto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) che rappresenta la raccolta.</span><span class="sxs-lookup"><span data-stu-id="29767-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="29767-378">In questa esercitazione, vengono richiamati i metodi seguenti sulla raccolta:</span><span class="sxs-lookup"><span data-stu-id="29767-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="29767-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Elimina un singolo documento corrispondente ai criteri di ricerca specificati.</span><span class="sxs-lookup"><span data-stu-id="29767-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="29767-380">[Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): restituisce tutti i documenti nella raccolta che corrispondono ai criteri di ricerca specificati.</span><span class="sxs-lookup"><span data-stu-id="29767-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="29767-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): inserisce l'oggetto fornito come nuovo documento nella raccolta.</span><span class="sxs-lookup"><span data-stu-id="29767-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="29767-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): sostituisce il singolo documento corrispondente ai criteri di ricerca specificati con l'oggetto fornito.</span><span class="sxs-lookup"><span data-stu-id="29767-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="29767-383">Aggiunta di un controller</span><span class="sxs-lookup"><span data-stu-id="29767-383">Add a controller</span></span>

<span data-ttu-id="29767-384">Aggiungere una classe `BooksController` alla directory *Controllers* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="29767-385">Il controller dell'API Web precedente:</span><span class="sxs-lookup"><span data-stu-id="29767-385">The preceding web API controller:</span></span>

* <span data-ttu-id="29767-386">Usa la classe `BookService` per eseguire operazioni CRUD.</span><span class="sxs-lookup"><span data-stu-id="29767-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="29767-387">Contiene metodi di azione per supportare le richieste HTTP GET, POST, PUT e DELETE.</span><span class="sxs-lookup"><span data-stu-id="29767-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="29767-388">Chiama <xref:System.Web.Http.ApiController.CreatedAtRoute*> nel metodo dell'azione `Create` per restituire una risposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="29767-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="29767-389">Il codice di stato 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="29767-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="29767-390">`Location` aggiunge anche un'intestazione `CreatedAtRoute` alla risposta.</span><span class="sxs-lookup"><span data-stu-id="29767-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="29767-391">L'intestazione `Location` specifica l'URI del libro appena creato.</span><span class="sxs-lookup"><span data-stu-id="29767-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="29767-392">Testare l'API Web</span><span class="sxs-lookup"><span data-stu-id="29767-392">Test the web API</span></span>

1. <span data-ttu-id="29767-393">Compilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="29767-393">Build and run the app.</span></span>

1. <span data-ttu-id="29767-394">Passare a `http://localhost:<port>/api/books` per testare il metodo dell'azione `Get` senza parametri del controller.</span><span class="sxs-lookup"><span data-stu-id="29767-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="29767-395">Viene visualizzata la risposta JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-395">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="29767-396">Passare a `http://localhost:<port>/api/books/{id here}` per testare il metodo dell'azione `Get` in overload del controller.</span><span class="sxs-lookup"><span data-stu-id="29767-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="29767-397">Viene visualizzata la risposta JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="29767-398">Configurare le opzioni di serializzazione JSON</span><span class="sxs-lookup"><span data-stu-id="29767-398">Configure JSON serialization options</span></span>

<span data-ttu-id="29767-399">Esistono due dettagli da modificare per le risposte JSON restituite nella sezione [Testare l'API Web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="29767-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="29767-400">La notazione a cammello predefinita per i nomi di proprietà deve essere modificata in modo da adottare la convenzione Pascal dei nomi di proprietà dell'oggetto CLR.</span><span class="sxs-lookup"><span data-stu-id="29767-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="29767-401">La proprietà `bookName` deve essere restituita come `Name`.</span><span class="sxs-lookup"><span data-stu-id="29767-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="29767-402">Per soddisfare i requisiti precedenti, apportare le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="29767-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="29767-403">In `Startup.ConfigureServices` concatenare il codice evidenziato seguente alla chiamata del metodo `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="29767-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="29767-404">Con la modifica precedente, i nomi delle proprietà nella risposta JSON serializzata dell'API Web corrispondono ai nomi di proprietà corrispondenti nel tipo di oggetto CLR.</span><span class="sxs-lookup"><span data-stu-id="29767-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="29767-405">Ad esempio, la proprietà `Author` della classe `Book` viene serializzata come `Author`.</span><span class="sxs-lookup"><span data-stu-id="29767-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="29767-406">In *models/book. cs* annotare la `BookName` proprietà con l' [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attributo seguente:</span><span class="sxs-lookup"><span data-stu-id="29767-406">In *Models/Book.cs* , annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="29767-407">Il valore `Name` dell'attributo `[JsonProperty]` rappresenta il nome della proprietà nella risposta JSON serializzata dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="29767-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="29767-408">Aggiungere il codice seguente all'inizio di *Models/Book.cs* per risolvere il riferimento all'attributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="29767-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="29767-409">Ripetere i passaggi definiti nella sezione [Testare l'API Web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="29767-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="29767-410">Si noti la differenza nei nomi di proprietà JSON.</span><span class="sxs-lookup"><span data-stu-id="29767-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="29767-411">Aggiungere il supporto per l'autenticazione a un'API Web</span><span class="sxs-lookup"><span data-stu-id="29767-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="29767-412">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="29767-412">Next steps</span></span>

<span data-ttu-id="29767-413">Per altre informazioni sulla creazione di API Web ASP.NET Core, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="29767-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="29767-414">Versione di YouTube di questo articolo</span><span class="sxs-lookup"><span data-stu-id="29767-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
