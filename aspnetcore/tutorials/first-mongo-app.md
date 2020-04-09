---
title: Creare un'API Web con ASP.NET Core e MongoDB
author: prkhandelwal
description: Questa esercitazione illustra come creare un'API Web ASP.NET Core con un database NoSQL MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: d5ce4a1dc3c00b2b12edc12e26f482caa97df6b3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511418"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="fa68b-103">Creare un'API Web con ASP.NET Core e MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="fa68b-104">Di [Pratik Khandelwal](https://twitter.com/K2Prk) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="fa68b-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fa68b-105">Questa esercitazione crea un'API Web che esegue operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) su un database NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="fa68b-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="fa68b-106">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="fa68b-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fa68b-107">Configurare MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-107">Configure MongoDB</span></span>
> * <span data-ttu-id="fa68b-108">Creare un database MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="fa68b-109">Definire una raccolta e uno schema MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="fa68b-110">Eseguire operazioni CRUD di MongoDB da un'API Web</span><span class="sxs-lookup"><span data-stu-id="fa68b-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="fa68b-111">Personalizzare la serializzazione JSON</span><span class="sxs-lookup"><span data-stu-id="fa68b-111">Customize JSON serialization</span></span>

<span data-ttu-id="fa68b-112">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fa68b-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fa68b-113">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="fa68b-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa68b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa68b-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="fa68b-115">.NET Core SDK 3.0 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="fa68b-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="fa68b-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con il carico di lavoro **di sviluppo Web e ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="fa68b-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="fa68b-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa68b-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa68b-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="fa68b-119">.NET Core SDK 3.0 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="fa68b-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="fa68b-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa68b-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="fa68b-121">C# per Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa68b-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="fa68b-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa68b-123">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="fa68b-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="fa68b-124">.NET Core SDK 3.0 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="fa68b-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="fa68b-125">Visual Studio per Mac versione 7.7 o successiva</span><span class="sxs-lookup"><span data-stu-id="fa68b-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="fa68b-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="fa68b-127">Configurare MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-127">Configure MongoDB</span></span>

<span data-ttu-id="fa68b-128">Se si utilizza Windows, MongoDB viene installato in *C:\\Programmi\\MongoDB* per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="fa68b-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="fa68b-129">Aggiungere *C:\\\\Programmi\\MongoDB Server\\\<version_number>\\bin* alla variabile di `Path` ambiente.</span><span class="sxs-lookup"><span data-stu-id="fa68b-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="fa68b-130">Questa modifica consente l'accesso MongoDB da qualsiasi posizione nel computer di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="fa68b-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="fa68b-131">Usare la shell mongo nelle procedure seguenti per creare un database, creare le raccolte e archiviare i documenti.</span><span class="sxs-lookup"><span data-stu-id="fa68b-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="fa68b-132">Per altre informazioni sui comandi della shell mongo, vedere [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell) (Utilizzo della shell mongo).</span><span class="sxs-lookup"><span data-stu-id="fa68b-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="fa68b-133">Scegliere una directory nel computer di sviluppo per archiviare i dati.</span><span class="sxs-lookup"><span data-stu-id="fa68b-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="fa68b-134">Ad esempio, *\\C: BooksData* su Windows.</span><span class="sxs-lookup"><span data-stu-id="fa68b-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="fa68b-135">Creare la directory se non esiste.</span><span class="sxs-lookup"><span data-stu-id="fa68b-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="fa68b-136">La shell mongo non consente di creare nuove directory.</span><span class="sxs-lookup"><span data-stu-id="fa68b-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="fa68b-137">Aprire una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="fa68b-137">Open a command shell.</span></span> <span data-ttu-id="fa68b-138">Eseguire il comando seguente per connettersi a MongoDB sulla porta predefinita 27017.</span><span class="sxs-lookup"><span data-stu-id="fa68b-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="fa68b-139">Ricordare di sostituire `<data_directory_path>` con la directory scelta nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="fa68b-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="fa68b-140">Aprire un'altra istanza della shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="fa68b-140">Open another command shell instance.</span></span> <span data-ttu-id="fa68b-141">Connettersi al database di test predefinito eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="fa68b-142">Eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="fa68b-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="fa68b-143">Se non esiste già, viene creato un database denominato *BookstoreDb*.</span><span class="sxs-lookup"><span data-stu-id="fa68b-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="fa68b-144">Se il database esiste, la connessione viene aperta per le transazioni.</span><span class="sxs-lookup"><span data-stu-id="fa68b-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="fa68b-145">Creare una raccolta `Books` tramite il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="fa68b-146">Viene visualizzato il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="fa68b-147">Definire uno schema per la raccolta `Books` e inserire due documenti usando il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="fa68b-148">Viene visualizzato il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="fa68b-149">L'ID illustrato in questo articolo non corrisponde agli ID quando si esegue questo campione.</span><span class="sxs-lookup"><span data-stu-id="fa68b-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="fa68b-150">Visualizzare i documenti nel database usando il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="fa68b-151">Viene visualizzato il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="fa68b-152">Lo schema aggiunge una proprietà `_id` generata automaticamente di tipo `ObjectId` per ogni documento.</span><span class="sxs-lookup"><span data-stu-id="fa68b-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="fa68b-153">Il database è pronto.</span><span class="sxs-lookup"><span data-stu-id="fa68b-153">The database is ready.</span></span> <span data-ttu-id="fa68b-154">È possibile iniziare a creare l'API Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa68b-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="fa68b-155">Creare il progetto per l'API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa68b-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa68b-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa68b-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="fa68b-157">Vai a **File** > **nuovo** > **progetto**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="fa68b-158">Selezionare il tipo di progetto **Applicazione Web ASP.NET Core** e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="fa68b-159">Assegnare al progetto il nome *BooksApi* e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="fa68b-160">Selezionare il framework di destinazione **.NET Core** e **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="fa68b-161">Selezionare il modello di progetto **API** e scegliere **Crea**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="fa68b-162">Visitare la [raccolta NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) per determinare la versione stabile più recente del driver .NET per MongoDB.</span><span class="sxs-lookup"><span data-stu-id="fa68b-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="fa68b-163">Nella finestra **Console di Gestione pacchetti** passare alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="fa68b-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="fa68b-164">Eseguire il comando seguente per installare il driver .NET per MongoDB:</span><span class="sxs-lookup"><span data-stu-id="fa68b-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa68b-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa68b-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="fa68b-166">Eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="fa68b-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="fa68b-167">Un nuovo progetto API Web ASP.NET Core destinato a .NET Core viene generato e aperto in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fa68b-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="fa68b-168">Dopo che l'icona di fiamma OmniSharp della barra di stato diventa verde, una finestra di dialogo chiede **alle risorse necessarie di compilare e debug mancano da 'BooksApi'. Aggiungerli?**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="fa68b-169">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-169">Select **Yes**.</span></span>
1. <span data-ttu-id="fa68b-170">Visitare la [raccolta NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) per determinare la versione stabile più recente del driver .NET per MongoDB.</span><span class="sxs-lookup"><span data-stu-id="fa68b-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="fa68b-171">Aprire **Terminale integrato** e passare alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="fa68b-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="fa68b-172">Eseguire il comando seguente per installare il driver .NET per MongoDB:</span><span class="sxs-lookup"><span data-stu-id="fa68b-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa68b-173">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="fa68b-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="fa68b-174">Passare a **File** > **Nuova soluzione** > **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="fa68b-175">Selezionare il modello di progetto C# **API Web ASP.NET Core** e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="fa68b-176">Selezionare **.NET Core 3.0** nell'elenco a discesa **Framework di destinazione** e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="fa68b-177">Immettere *BooksApi* per **Nome progetto** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="fa68b-178">Nel riquadro **Soluzione** fare clic con il pulsante destro del mouse sul nodo **Dipendenze** del progetto e scegliere **Aggiungi pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="fa68b-179">Immettere *MongoDB.Driver* nella casella di ricerca, selezionare il pacchetto *MongoDB.Driver* e quindi **Aggiungi pacchetto**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="fa68b-180">Selezionare il pulsante **Accetta** nella finestra di dialogo **Accettazione della licenza**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="fa68b-181">Aggiungere un modello di entità</span><span class="sxs-lookup"><span data-stu-id="fa68b-181">Add an entity model</span></span>

1. <span data-ttu-id="fa68b-182">Aggiungere una directory *Models* alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="fa68b-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="fa68b-183">Aggiungere una classe `Book` alla directory *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="fa68b-184">Nella classe precedente, la proprietà `Id`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="fa68b-185">È obbligatoria per il mapping tra l'oggetto CLR (Common Language Runtime) e la raccolta MongoDB.</span><span class="sxs-lookup"><span data-stu-id="fa68b-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="fa68b-186">Viene annotato con [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) per designare questa proprietà come chiave primaria del documento.</span><span class="sxs-lookup"><span data-stu-id="fa68b-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="fa68b-187">Viene annotato con [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) per consentire il `string` passaggio del parametro come tipo anziché un [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struttura.</span><span class="sxs-lookup"><span data-stu-id="fa68b-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="fa68b-188">Mongo gestisce la conversione da `string` a `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="fa68b-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="fa68b-189">La `BookName` proprietà è annotata con l'attributo [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) .</span><span class="sxs-lookup"><span data-stu-id="fa68b-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="fa68b-190">Il valore dell'attributo `Name` rappresenta il nome della proprietà nella raccolta MongoDB.</span><span class="sxs-lookup"><span data-stu-id="fa68b-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="fa68b-191">Aggiungere un modello di configurazione</span><span class="sxs-lookup"><span data-stu-id="fa68b-191">Add a configuration model</span></span>

1. <span data-ttu-id="fa68b-192">Aggiungere i seguenti valori di configurazione del database a *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="fa68b-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="fa68b-193">Aggiungere un file *BookstoreDatabaseSettings.cs* alla directory *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="fa68b-194">La classe `BookstoreDatabaseSettings` precedente viene utilizzata per archiviare i valori della proprietà `BookstoreDatabaseSettings` del file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fa68b-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="fa68b-195">I nomi delle proprietà JSON e C# sono identici per semplificare il processo di mapping.</span><span class="sxs-lookup"><span data-stu-id="fa68b-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="fa68b-196">Aggiungere il codice evidenziato seguente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="fa68b-197">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-197">In the preceding code:</span></span>

   * <span data-ttu-id="fa68b-198">L'istanza di configurazione a cui si associa la sezione `BookstoreDatabaseSettings` del file *appsettings.json* viene registrata nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="fa68b-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="fa68b-199">Una proprietà `ConnectionString` di un oggetto `BookstoreDatabaseSettings` viene popolata con la proprietà `BookstoreDatabaseSettings:ConnectionString` in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fa68b-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="fa68b-200">L'interfaccia `IBookstoreDatabaseSettings` è registrata nell'inserimento di dipendenze con una [durata del servizio](xref:fundamentals/dependency-injection#service-lifetimes) singleton.</span><span class="sxs-lookup"><span data-stu-id="fa68b-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="fa68b-201">Quando avviene l'inserimento, l'istanza dell'interfaccia restituisce un oggetto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="fa68b-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="fa68b-202">Aggiungere il codice seguente all'inizio del file *Startup.cs* per risolvere i riferimenti a `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="fa68b-203">Aggiungere un servizio di operazioni CRUD</span><span class="sxs-lookup"><span data-stu-id="fa68b-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="fa68b-204">Aggiungere una directory *Services* alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="fa68b-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="fa68b-205">Aggiungere una classe `BookService` alla directory *Services* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="fa68b-206">Nel codice precedente un'istanza di `IBookstoreDatabaseSettings` viene recuperata dall'inserimento di dipendenze tramite l'inserimento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="fa68b-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="fa68b-207">Questa tecnica fornisce accesso ai valori di configurazione di *appsettings.json* che sono stati aggiunti nella sezione [Aggiungere un modello di configurazione](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="fa68b-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="fa68b-208">Aggiungere il codice evidenziato seguente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="fa68b-209">Nel codice precedente la classe `BookService` è registrata con l'inserimento di dipendenze per supportare l'inserimento del costruttore nelle classi che la utilizzano.</span><span class="sxs-lookup"><span data-stu-id="fa68b-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="fa68b-210">La durata del servizio singleton è più appropriata perché `BookService` assume una dipendenza diretta a `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="fa68b-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="fa68b-211">In base alle linee guida `MongoClient` ufficiali per il riutilizzo di [Mongo Client,](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)deve essere registrato in DI con una durata del servizio singleton.</span><span class="sxs-lookup"><span data-stu-id="fa68b-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="fa68b-212">Aggiungere il codice seguente all'inizio del file *Startup.cs* per risolvere il riferimento a `BookService`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="fa68b-213">La classe `BookService` usa i membri `MongoDB.Driver` seguenti per eseguire operazioni CRUD sul database:</span><span class="sxs-lookup"><span data-stu-id="fa68b-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="fa68b-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Legge l'istanza del server per l'esecuzione di operazioni di database.</span><span class="sxs-lookup"><span data-stu-id="fa68b-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="fa68b-215">Al costruttore di questa classe viene passata la stringa di connessione MongoDB:</span><span class="sxs-lookup"><span data-stu-id="fa68b-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="fa68b-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Rappresenta il database di Mongo per l'esecuzione delle operazioni.</span><span class="sxs-lookup"><span data-stu-id="fa68b-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="fa68b-217">Questa esercitazione usa il metodo [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) generico sull'interfaccia per ottenere l'accesso ai dati in una raccolta specifica.</span><span class="sxs-lookup"><span data-stu-id="fa68b-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="fa68b-218">Eseguire le operazioni CRUD sulla raccolta dopo la chiamata a questo metodo.</span><span class="sxs-lookup"><span data-stu-id="fa68b-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="fa68b-219">Nella chiamata del metodo `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="fa68b-220">`collection` rappresenta il nome della raccolta.</span><span class="sxs-lookup"><span data-stu-id="fa68b-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="fa68b-221">`TDocument` rappresenta il tipo di oggetto CLR archiviato nella raccolta.</span><span class="sxs-lookup"><span data-stu-id="fa68b-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="fa68b-222">`GetCollection<TDocument>(collection)` restituisce un oggetto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) che rappresenta la raccolta.</span><span class="sxs-lookup"><span data-stu-id="fa68b-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="fa68b-223">In questa esercitazione, vengono richiamati i metodi seguenti sulla raccolta:</span><span class="sxs-lookup"><span data-stu-id="fa68b-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="fa68b-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Elimina un singolo documento corrispondente ai criteri di ricerca specificati.</span><span class="sxs-lookup"><span data-stu-id="fa68b-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="fa68b-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Restituisce tutti i documenti nella raccolta corrispondenti ai criteri di ricerca specificati.</span><span class="sxs-lookup"><span data-stu-id="fa68b-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="fa68b-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserisce l'oggetto specificato come nuovo documento nella raccolta.</span><span class="sxs-lookup"><span data-stu-id="fa68b-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="fa68b-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Sostituisce il singolo documento corrispondente ai criteri di ricerca specificati con l'oggetto specificato.</span><span class="sxs-lookup"><span data-stu-id="fa68b-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="fa68b-228">Aggiunta di un controller</span><span class="sxs-lookup"><span data-stu-id="fa68b-228">Add a controller</span></span>

<span data-ttu-id="fa68b-229">Aggiungere una classe `BooksController` alla directory *Controllers* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="fa68b-230">Il controller dell'API Web precedente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-230">The preceding web API controller:</span></span>

* <span data-ttu-id="fa68b-231">Usa la classe `BookService` per eseguire operazioni CRUD.</span><span class="sxs-lookup"><span data-stu-id="fa68b-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="fa68b-232">Contiene metodi di azione per supportare le richieste HTTP GET, POST, PUT e DELETE.</span><span class="sxs-lookup"><span data-stu-id="fa68b-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="fa68b-233">Chiama <xref:System.Web.Http.ApiController.CreatedAtRoute*> nel metodo dell'azione `Create` per restituire una risposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="fa68b-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="fa68b-234">Il codice di stato 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="fa68b-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="fa68b-235">`Location` aggiunge anche un'intestazione `CreatedAtRoute` alla risposta.</span><span class="sxs-lookup"><span data-stu-id="fa68b-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="fa68b-236">L'intestazione `Location` specifica l'URI del libro appena creato.</span><span class="sxs-lookup"><span data-stu-id="fa68b-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="fa68b-237">Testare l'API Web</span><span class="sxs-lookup"><span data-stu-id="fa68b-237">Test the web API</span></span>

1. <span data-ttu-id="fa68b-238">Compilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="fa68b-238">Build and run the app.</span></span>

1. <span data-ttu-id="fa68b-239">Passare a `http://localhost:<port>/api/books` per testare il metodo dell'azione `Get` senza parametri del controller.</span><span class="sxs-lookup"><span data-stu-id="fa68b-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="fa68b-240">Viene visualizzata la risposta JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="fa68b-241">Passare a `http://localhost:<port>/api/books/{id here}` per testare il metodo dell'azione `Get` in overload del controller.</span><span class="sxs-lookup"><span data-stu-id="fa68b-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="fa68b-242">Viene visualizzata la risposta JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="fa68b-243">Configurare le opzioni di serializzazione JSON</span><span class="sxs-lookup"><span data-stu-id="fa68b-243">Configure JSON serialization options</span></span>

<span data-ttu-id="fa68b-244">Esistono due dettagli da modificare per le risposte JSON restituite nella sezione [Testare l'API Web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="fa68b-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="fa68b-245">La notazione a cammello predefinita per i nomi di proprietà deve essere modificata in modo da adottare la convenzione Pascal dei nomi di proprietà dell'oggetto CLR.</span><span class="sxs-lookup"><span data-stu-id="fa68b-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="fa68b-246">La proprietà `bookName` deve essere restituita come `Name`.</span><span class="sxs-lookup"><span data-stu-id="fa68b-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="fa68b-247">Per soddisfare i requisiti precedenti, apportare le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="fa68b-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="fa68b-248">JSON.NET è stato rimosso dal framework condiviso di ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="fa68b-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="fa68b-249">Aggiungere un riferimento al pacchetto a [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="fa68b-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="fa68b-250">In `Startup.ConfigureServices` concatenare il codice evidenziato seguente alla chiamata del metodo `AddControllers`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="fa68b-251">Con la modifica precedente, i nomi delle proprietà nella risposta JSON serializzata dell'API Web corrispondono ai nomi di proprietà corrispondenti nel tipo di oggetto CLR.</span><span class="sxs-lookup"><span data-stu-id="fa68b-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="fa68b-252">Ad esempio, la proprietà `Author` della classe `Book` viene serializzata come `Author`.</span><span class="sxs-lookup"><span data-stu-id="fa68b-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="fa68b-253">In *Models/Book.cs*, `BookName` annotare [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) la proprietà con il seguente attributo:</span><span class="sxs-lookup"><span data-stu-id="fa68b-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="fa68b-254">Il valore `Name` dell'attributo `[JsonProperty]` rappresenta il nome della proprietà nella risposta JSON serializzata dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="fa68b-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="fa68b-255">Aggiungere il codice seguente all'inizio di *Models/Book.cs* per risolvere il riferimento all'attributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="fa68b-256">Ripetere i passaggi definiti nella sezione [Testare l'API Web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="fa68b-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="fa68b-257">Si noti la differenza nei nomi di proprietà JSON.</span><span class="sxs-lookup"><span data-stu-id="fa68b-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fa68b-258">Questa esercitazione crea un'API Web che esegue operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) su un database NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="fa68b-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="fa68b-259">In questa esercitazione verranno illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="fa68b-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fa68b-260">Configurare MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-260">Configure MongoDB</span></span>
> * <span data-ttu-id="fa68b-261">Creare un database MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="fa68b-262">Definire una raccolta e uno schema MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="fa68b-263">Eseguire operazioni CRUD di MongoDB da un'API Web</span><span class="sxs-lookup"><span data-stu-id="fa68b-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="fa68b-264">Personalizzare la serializzazione JSON</span><span class="sxs-lookup"><span data-stu-id="fa68b-264">Customize JSON serialization</span></span>

<span data-ttu-id="fa68b-265">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fa68b-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fa68b-266">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="fa68b-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa68b-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa68b-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="fa68b-268">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="fa68b-268">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="fa68b-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con il carico di lavoro **di sviluppo Web e ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="fa68b-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="fa68b-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa68b-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa68b-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="fa68b-272">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="fa68b-272">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="fa68b-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa68b-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="fa68b-274">C# per Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa68b-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="fa68b-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa68b-276">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="fa68b-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="fa68b-277">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="fa68b-277">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="fa68b-278">Visual Studio per Mac versione 7.7 o successiva</span><span class="sxs-lookup"><span data-stu-id="fa68b-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="fa68b-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="fa68b-280">Configurare MongoDB</span><span class="sxs-lookup"><span data-stu-id="fa68b-280">Configure MongoDB</span></span>

<span data-ttu-id="fa68b-281">Se si utilizza Windows, MongoDB viene installato in *C:\\Programmi\\MongoDB* per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="fa68b-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="fa68b-282">Aggiungere *C:\\\\Programmi\\MongoDB Server\\\<version_number>\\bin* alla variabile di `Path` ambiente.</span><span class="sxs-lookup"><span data-stu-id="fa68b-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="fa68b-283">Questa modifica consente l'accesso MongoDB da qualsiasi posizione nel computer di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="fa68b-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="fa68b-284">Usare la shell mongo nelle procedure seguenti per creare un database, creare le raccolte e archiviare i documenti.</span><span class="sxs-lookup"><span data-stu-id="fa68b-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="fa68b-285">Per altre informazioni sui comandi della shell mongo, vedere [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell) (Utilizzo della shell mongo).</span><span class="sxs-lookup"><span data-stu-id="fa68b-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="fa68b-286">Scegliere una directory nel computer di sviluppo per archiviare i dati.</span><span class="sxs-lookup"><span data-stu-id="fa68b-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="fa68b-287">Ad esempio, *\\C: BooksData* su Windows.</span><span class="sxs-lookup"><span data-stu-id="fa68b-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="fa68b-288">Creare la directory se non esiste.</span><span class="sxs-lookup"><span data-stu-id="fa68b-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="fa68b-289">La shell mongo non consente di creare nuove directory.</span><span class="sxs-lookup"><span data-stu-id="fa68b-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="fa68b-290">Aprire una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="fa68b-290">Open a command shell.</span></span> <span data-ttu-id="fa68b-291">Eseguire il comando seguente per connettersi a MongoDB sulla porta predefinita 27017.</span><span class="sxs-lookup"><span data-stu-id="fa68b-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="fa68b-292">Ricordare di sostituire `<data_directory_path>` con la directory scelta nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="fa68b-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="fa68b-293">Aprire un'altra istanza della shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="fa68b-293">Open another command shell instance.</span></span> <span data-ttu-id="fa68b-294">Connettersi al database di test predefinito eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="fa68b-295">Eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="fa68b-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="fa68b-296">Se non esiste già, viene creato un database denominato *BookstoreDb*.</span><span class="sxs-lookup"><span data-stu-id="fa68b-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="fa68b-297">Se il database esiste, la connessione viene aperta per le transazioni.</span><span class="sxs-lookup"><span data-stu-id="fa68b-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="fa68b-298">Creare una raccolta `Books` tramite il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="fa68b-299">Viene visualizzato il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="fa68b-300">Definire uno schema per la raccolta `Books` e inserire due documenti usando il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="fa68b-301">Viene visualizzato il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-301">The following result is displayed:</span></span>

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
   > <span data-ttu-id="fa68b-302">L'ID illustrato in questo articolo non corrisponde agli ID quando si esegue questo campione.</span><span class="sxs-lookup"><span data-stu-id="fa68b-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="fa68b-303">Visualizzare i documenti nel database usando il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="fa68b-304">Viene visualizzato il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-304">The following result is displayed:</span></span>

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

   <span data-ttu-id="fa68b-305">Lo schema aggiunge una proprietà `_id` generata automaticamente di tipo `ObjectId` per ogni documento.</span><span class="sxs-lookup"><span data-stu-id="fa68b-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="fa68b-306">Il database è pronto.</span><span class="sxs-lookup"><span data-stu-id="fa68b-306">The database is ready.</span></span> <span data-ttu-id="fa68b-307">È possibile iniziare a creare l'API Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa68b-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="fa68b-308">Creare il progetto per l'API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa68b-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa68b-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa68b-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="fa68b-310">Vai a **File** > **nuovo** > **progetto**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="fa68b-311">Selezionare il tipo di progetto **Applicazione Web ASP.NET Core** e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="fa68b-312">Assegnare al progetto il nome *BooksApi* e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="fa68b-313">Selezionare il framework di destinazione **.NET Core** e **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="fa68b-314">Selezionare il modello di progetto **API** e scegliere **Crea**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="fa68b-315">Visitare la [raccolta NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) per determinare la versione stabile più recente del driver .NET per MongoDB.</span><span class="sxs-lookup"><span data-stu-id="fa68b-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="fa68b-316">Nella finestra **Console di Gestione pacchetti** passare alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="fa68b-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="fa68b-317">Eseguire il comando seguente per installare il driver .NET per MongoDB:</span><span class="sxs-lookup"><span data-stu-id="fa68b-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa68b-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa68b-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="fa68b-319">Eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="fa68b-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="fa68b-320">Un nuovo progetto API Web ASP.NET Core destinato a .NET Core viene generato e aperto in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fa68b-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="fa68b-321">Dopo che l'icona di fiamma OmniSharp della barra di stato diventa verde, una finestra di dialogo chiede **alle risorse necessarie di compilare e debug mancano da 'BooksApi'. Aggiungerli?**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="fa68b-322">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-322">Select **Yes**.</span></span>
1. <span data-ttu-id="fa68b-323">Visitare la [raccolta NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) per determinare la versione stabile più recente del driver .NET per MongoDB.</span><span class="sxs-lookup"><span data-stu-id="fa68b-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="fa68b-324">Aprire **Terminale integrato** e passare alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="fa68b-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="fa68b-325">Eseguire il comando seguente per installare il driver .NET per MongoDB:</span><span class="sxs-lookup"><span data-stu-id="fa68b-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa68b-326">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="fa68b-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="fa68b-327">Passare a **File** > **Nuova soluzione** > **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="fa68b-328">Selezionare il modello di progetto C# **API Web ASP.NET Core** e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="fa68b-329">Selezionare **.NET Core 2.2** nell'elenco a discesa **Framework di destinazione** e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="fa68b-330">Immettere *BooksApi* per **Nome progetto** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="fa68b-331">Nel riquadro **Soluzione** fare clic con il pulsante destro del mouse sul nodo **Dipendenze** del progetto e scegliere **Aggiungi pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="fa68b-332">Immettere *MongoDB.Driver* nella casella di ricerca, selezionare il pacchetto *MongoDB.Driver* e quindi **Aggiungi pacchetto**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="fa68b-333">Selezionare il pulsante **Accetta** nella finestra di dialogo **Accettazione della licenza**.</span><span class="sxs-lookup"><span data-stu-id="fa68b-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="fa68b-334">Aggiungere un modello di entità</span><span class="sxs-lookup"><span data-stu-id="fa68b-334">Add an entity model</span></span>

1. <span data-ttu-id="fa68b-335">Aggiungere una directory *Models* alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="fa68b-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="fa68b-336">Aggiungere una classe `Book` alla directory *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="fa68b-337">Nella classe precedente, la proprietà `Id`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="fa68b-338">È obbligatoria per il mapping tra l'oggetto CLR (Common Language Runtime) e la raccolta MongoDB.</span><span class="sxs-lookup"><span data-stu-id="fa68b-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="fa68b-339">Viene annotato con [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) per designare questa proprietà come chiave primaria del documento.</span><span class="sxs-lookup"><span data-stu-id="fa68b-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="fa68b-340">Viene annotato con [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) per consentire il `string` passaggio del parametro come tipo anziché un [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struttura.</span><span class="sxs-lookup"><span data-stu-id="fa68b-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="fa68b-341">Mongo gestisce la conversione da `string` a `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="fa68b-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="fa68b-342">La `BookName` proprietà è annotata con l'attributo [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) .</span><span class="sxs-lookup"><span data-stu-id="fa68b-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="fa68b-343">Il valore dell'attributo `Name` rappresenta il nome della proprietà nella raccolta MongoDB.</span><span class="sxs-lookup"><span data-stu-id="fa68b-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="fa68b-344">Aggiungere un modello di configurazione</span><span class="sxs-lookup"><span data-stu-id="fa68b-344">Add a configuration model</span></span>

1. <span data-ttu-id="fa68b-345">Aggiungere i seguenti valori di configurazione del database a *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="fa68b-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="fa68b-346">Aggiungere un file *BookstoreDatabaseSettings.cs* alla directory *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="fa68b-347">La classe `BookstoreDatabaseSettings` precedente viene utilizzata per archiviare i valori della proprietà `BookstoreDatabaseSettings` del file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fa68b-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="fa68b-348">I nomi delle proprietà JSON e C# sono identici per semplificare il processo di mapping.</span><span class="sxs-lookup"><span data-stu-id="fa68b-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="fa68b-349">Aggiungere il codice evidenziato seguente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="fa68b-350">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-350">In the preceding code:</span></span>

   * <span data-ttu-id="fa68b-351">L'istanza di configurazione a cui si associa la sezione `BookstoreDatabaseSettings` del file *appsettings.json* viene registrata nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="fa68b-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="fa68b-352">Una proprietà `ConnectionString` di un oggetto `BookstoreDatabaseSettings` viene popolata con la proprietà `BookstoreDatabaseSettings:ConnectionString` in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fa68b-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="fa68b-353">L'interfaccia `IBookstoreDatabaseSettings` è registrata nell'inserimento di dipendenze con una [durata del servizio](xref:fundamentals/dependency-injection#service-lifetimes) singleton.</span><span class="sxs-lookup"><span data-stu-id="fa68b-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="fa68b-354">Quando avviene l'inserimento, l'istanza dell'interfaccia restituisce un oggetto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="fa68b-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="fa68b-355">Aggiungere il codice seguente all'inizio del file *Startup.cs* per risolvere i riferimenti a `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="fa68b-356">Aggiungere un servizio di operazioni CRUD</span><span class="sxs-lookup"><span data-stu-id="fa68b-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="fa68b-357">Aggiungere una directory *Services* alla radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="fa68b-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="fa68b-358">Aggiungere una classe `BookService` alla directory *Services* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="fa68b-359">Nel codice precedente un'istanza di `IBookstoreDatabaseSettings` viene recuperata dall'inserimento di dipendenze tramite l'inserimento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="fa68b-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="fa68b-360">Questa tecnica fornisce accesso ai valori di configurazione di *appsettings.json* che sono stati aggiunti nella sezione [Aggiungere un modello di configurazione](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="fa68b-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="fa68b-361">Aggiungere il codice evidenziato seguente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="fa68b-362">Nel codice precedente la classe `BookService` è registrata con l'inserimento di dipendenze per supportare l'inserimento del costruttore nelle classi che la utilizzano.</span><span class="sxs-lookup"><span data-stu-id="fa68b-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="fa68b-363">La durata del servizio singleton è più appropriata perché `BookService` assume una dipendenza diretta a `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="fa68b-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="fa68b-364">In base alle linee guida `MongoClient` ufficiali per il riutilizzo di [Mongo Client,](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)deve essere registrato in DI con una durata del servizio singleton.</span><span class="sxs-lookup"><span data-stu-id="fa68b-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="fa68b-365">Aggiungere il codice seguente all'inizio del file *Startup.cs* per risolvere il riferimento a `BookService`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="fa68b-366">La classe `BookService` usa i membri `MongoDB.Driver` seguenti per eseguire operazioni CRUD sul database:</span><span class="sxs-lookup"><span data-stu-id="fa68b-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="fa68b-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Legge l'istanza del server per l'esecuzione di operazioni di database.</span><span class="sxs-lookup"><span data-stu-id="fa68b-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="fa68b-368">Al costruttore di questa classe viene passata la stringa di connessione MongoDB:</span><span class="sxs-lookup"><span data-stu-id="fa68b-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="fa68b-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Rappresenta il database di Mongo per l'esecuzione delle operazioni.</span><span class="sxs-lookup"><span data-stu-id="fa68b-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="fa68b-370">Questa esercitazione usa il metodo [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) generico sull'interfaccia per ottenere l'accesso ai dati in una raccolta specifica.</span><span class="sxs-lookup"><span data-stu-id="fa68b-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="fa68b-371">Eseguire le operazioni CRUD sulla raccolta dopo la chiamata a questo metodo.</span><span class="sxs-lookup"><span data-stu-id="fa68b-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="fa68b-372">Nella chiamata del metodo `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="fa68b-373">`collection` rappresenta il nome della raccolta.</span><span class="sxs-lookup"><span data-stu-id="fa68b-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="fa68b-374">`TDocument` rappresenta il tipo di oggetto CLR archiviato nella raccolta.</span><span class="sxs-lookup"><span data-stu-id="fa68b-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="fa68b-375">`GetCollection<TDocument>(collection)` restituisce un oggetto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) che rappresenta la raccolta.</span><span class="sxs-lookup"><span data-stu-id="fa68b-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="fa68b-376">In questa esercitazione, vengono richiamati i metodi seguenti sulla raccolta:</span><span class="sxs-lookup"><span data-stu-id="fa68b-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="fa68b-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Elimina un singolo documento corrispondente ai criteri di ricerca specificati.</span><span class="sxs-lookup"><span data-stu-id="fa68b-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="fa68b-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Restituisce tutti i documenti nella raccolta corrispondenti ai criteri di ricerca specificati.</span><span class="sxs-lookup"><span data-stu-id="fa68b-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="fa68b-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserisce l'oggetto specificato come nuovo documento nella raccolta.</span><span class="sxs-lookup"><span data-stu-id="fa68b-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="fa68b-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Sostituisce il singolo documento corrispondente ai criteri di ricerca specificati con l'oggetto specificato.</span><span class="sxs-lookup"><span data-stu-id="fa68b-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="fa68b-381">Aggiunta di un controller</span><span class="sxs-lookup"><span data-stu-id="fa68b-381">Add a controller</span></span>

<span data-ttu-id="fa68b-382">Aggiungere una classe `BooksController` alla directory *Controllers* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="fa68b-383">Il controller dell'API Web precedente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-383">The preceding web API controller:</span></span>

* <span data-ttu-id="fa68b-384">Usa la classe `BookService` per eseguire operazioni CRUD.</span><span class="sxs-lookup"><span data-stu-id="fa68b-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="fa68b-385">Contiene metodi di azione per supportare le richieste HTTP GET, POST, PUT e DELETE.</span><span class="sxs-lookup"><span data-stu-id="fa68b-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="fa68b-386">Chiama <xref:System.Web.Http.ApiController.CreatedAtRoute*> nel metodo dell'azione `Create` per restituire una risposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="fa68b-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="fa68b-387">Il codice di stato 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.</span><span class="sxs-lookup"><span data-stu-id="fa68b-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="fa68b-388">`Location` aggiunge anche un'intestazione `CreatedAtRoute` alla risposta.</span><span class="sxs-lookup"><span data-stu-id="fa68b-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="fa68b-389">L'intestazione `Location` specifica l'URI del libro appena creato.</span><span class="sxs-lookup"><span data-stu-id="fa68b-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="fa68b-390">Testare l'API Web</span><span class="sxs-lookup"><span data-stu-id="fa68b-390">Test the web API</span></span>

1. <span data-ttu-id="fa68b-391">Compilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="fa68b-391">Build and run the app.</span></span>

1. <span data-ttu-id="fa68b-392">Passare a `http://localhost:<port>/api/books` per testare il metodo dell'azione `Get` senza parametri del controller.</span><span class="sxs-lookup"><span data-stu-id="fa68b-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="fa68b-393">Viene visualizzata la risposta JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-393">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="fa68b-394">Passare a `http://localhost:<port>/api/books/{id here}` per testare il metodo dell'azione `Get` in overload del controller.</span><span class="sxs-lookup"><span data-stu-id="fa68b-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="fa68b-395">Viene visualizzata la risposta JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="fa68b-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="fa68b-396">Configurare le opzioni di serializzazione JSON</span><span class="sxs-lookup"><span data-stu-id="fa68b-396">Configure JSON serialization options</span></span>

<span data-ttu-id="fa68b-397">Esistono due dettagli da modificare per le risposte JSON restituite nella sezione [Testare l'API Web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="fa68b-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="fa68b-398">La notazione a cammello predefinita per i nomi di proprietà deve essere modificata in modo da adottare la convenzione Pascal dei nomi di proprietà dell'oggetto CLR.</span><span class="sxs-lookup"><span data-stu-id="fa68b-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="fa68b-399">La proprietà `bookName` deve essere restituita come `Name`.</span><span class="sxs-lookup"><span data-stu-id="fa68b-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="fa68b-400">Per soddisfare i requisiti precedenti, apportare le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="fa68b-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="fa68b-401">In `Startup.ConfigureServices` concatenare il codice evidenziato seguente alla chiamata del metodo `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="fa68b-402">Con la modifica precedente, i nomi delle proprietà nella risposta JSON serializzata dell'API Web corrispondono ai nomi di proprietà corrispondenti nel tipo di oggetto CLR.</span><span class="sxs-lookup"><span data-stu-id="fa68b-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="fa68b-403">Ad esempio, la proprietà `Author` della classe `Book` viene serializzata come `Author`.</span><span class="sxs-lookup"><span data-stu-id="fa68b-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="fa68b-404">In *Models/Book.cs*, `BookName` annotare [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) la proprietà con il seguente attributo:</span><span class="sxs-lookup"><span data-stu-id="fa68b-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="fa68b-405">Il valore `Name` dell'attributo `[JsonProperty]` rappresenta il nome della proprietà nella risposta JSON serializzata dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="fa68b-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="fa68b-406">Aggiungere il codice seguente all'inizio di *Models/Book.cs* per risolvere il riferimento all'attributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="fa68b-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="fa68b-407">Ripetere i passaggi definiti nella sezione [Testare l'API Web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="fa68b-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="fa68b-408">Si noti la differenza nei nomi di proprietà JSON.</span><span class="sxs-lookup"><span data-stu-id="fa68b-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="fa68b-409">Aggiungere il supporto dell'autenticazione a un'API WebAdd authentication support to a web API</span><span class="sxs-lookup"><span data-stu-id="fa68b-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="fa68b-410">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="fa68b-410">Next steps</span></span>

<span data-ttu-id="fa68b-411">Per altre informazioni sulla creazione di API Web ASP.NET Core, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="fa68b-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="fa68b-412">Versione YouTube di questo articolo</span><span class="sxs-lookup"><span data-stu-id="fa68b-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
