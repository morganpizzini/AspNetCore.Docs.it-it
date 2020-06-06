<span data-ttu-id="569cd-101">Eseguire l'impalcatura delle identità:</span><span class="sxs-lookup"><span data-stu-id="569cd-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="569cd-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="569cd-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="569cd-103">Da **Esplora soluzioni**, fare clic con il pulsante destro del mouse sul progetto > **Aggiungi**  >  **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="569cd-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="569cd-104">Dal riquadro sinistro della finestra di dialogo **Aggiungi nuovo elemento con impalcatura** selezionare **Identity**  >  **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="569cd-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="569cd-105">Nella finestra di dialogo **Aggiungi identità** selezionare le opzioni desiderate.</span><span class="sxs-lookup"><span data-stu-id="569cd-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="569cd-106">Selezionare la pagina layout esistente. in alternativa, il file di layout verrà sovrascritto con un markup errato:</span><span class="sxs-lookup"><span data-stu-id="569cd-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="569cd-107">`~/Pages/Shared/_Layout.cshtml`per Razor Pages</span><span class="sxs-lookup"><span data-stu-id="569cd-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="569cd-108">`~/Views/Shared/_Layout.cshtml`per i progetti MVC</span><span class="sxs-lookup"><span data-stu-id="569cd-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="569cd-109">Per impostazione predefinita, le app del server Blazer create dal modello del server Blaze ( `blazorserver` ) non sono configurate per Razor Pages o MVC.</span><span class="sxs-lookup"><span data-stu-id="569cd-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="569cd-110">Lasciare vuota la voce di pagina layout.</span><span class="sxs-lookup"><span data-stu-id="569cd-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="569cd-111">Selezionare il **+** pulsante per creare una nuova **classe del contesto dati**.</span><span class="sxs-lookup"><span data-stu-id="569cd-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="569cd-112">Accettare il valore predefinito o specificare una classe (ad esempio, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="569cd-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="569cd-113">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="569cd-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="569cd-114">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="569cd-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="569cd-115">Se in precedenza non è stato installato il ASP.NET Core impalcatura, installarlo ora:</span><span class="sxs-lookup"><span data-stu-id="569cd-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="569cd-116">Aggiungere i riferimenti ai pacchetti NuGet necessari al file di progetto (con*estensione csproj*).</span><span class="sxs-lookup"><span data-stu-id="569cd-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="569cd-117">Eseguire i comandi seguenti nella directory del progetto:</span><span class="sxs-lookup"><span data-stu-id="569cd-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="569cd-118">Eseguire il comando seguente per elencare le opzioni dell'impalcatura di identità:</span><span class="sxs-lookup"><span data-stu-id="569cd-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="569cd-119">Nella cartella del progetto eseguire l'impalcatura di identità con le opzioni desiderate.</span><span class="sxs-lookup"><span data-stu-id="569cd-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="569cd-120">Ad esempio, per configurare Identity con l'interfaccia utente predefinita e il numero minimo di file, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="569cd-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
