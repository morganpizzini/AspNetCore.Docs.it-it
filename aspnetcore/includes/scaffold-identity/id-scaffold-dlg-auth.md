::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9549-101">Eseguire l'impalcatura delle identità:</span><span class="sxs-lookup"><span data-stu-id="a9549-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a9549-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9549-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a9549-103">Da **Esplora soluzioni**, fare clic con il pulsante destro del mouse sul progetto > **Aggiungi** > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="a9549-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="a9549-104">Dal riquadro sinistro della finestra di dialogo **Aggiungi impalcatura** selezionare **Identity** > **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="a9549-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="a9549-105">Nella finestra di dialogo **Aggiungi identità** selezionare le opzioni desiderate.</span><span class="sxs-lookup"><span data-stu-id="a9549-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="a9549-106">Selezionare la pagina layout esistente in modo che il file di layout non venga sovrascritto con un markup errato.</span><span class="sxs-lookup"><span data-stu-id="a9549-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="a9549-107">Quando si seleziona un file \* \_ layout. cshtml\* esistente, questo **non** viene sovrascritto.</span><span class="sxs-lookup"><span data-stu-id="a9549-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="a9549-108">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9549-108">For example:</span></span>
    * <span data-ttu-id="a9549-109">`~/Pages/Shared/_Layout.cshtml`per progetti server Razor Pages o blazer con infrastruttura Razor Pages esistente</span><span class="sxs-lookup"><span data-stu-id="a9549-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="a9549-110">`~/Views/Shared/_Layout.cshtml`per progetti MVC o progetti server Blazer con l'infrastruttura MVC esistente</span><span class="sxs-lookup"><span data-stu-id="a9549-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="a9549-111">Per usare il contesto dei dati esistente, selezionare almeno un file di cui eseguire l'override.</span><span class="sxs-lookup"><span data-stu-id="a9549-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="a9549-112">È necessario selezionare almeno un file per aggiungere il contesto dei dati.</span><span class="sxs-lookup"><span data-stu-id="a9549-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="a9549-113">Selezionare la classe del contesto dati.</span><span class="sxs-lookup"><span data-stu-id="a9549-113">Select your data context class.</span></span>
  * <span data-ttu-id="a9549-114">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="a9549-114">Select **Add**.</span></span>
* <span data-ttu-id="a9549-115">Per creare un nuovo contesto utente ed eventualmente creare una classe utente personalizzata per l'identità:</span><span class="sxs-lookup"><span data-stu-id="a9549-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="a9549-116">Selezionare il **+** pulsante per creare una nuova **classe del contesto dati**.</span><span class="sxs-lookup"><span data-stu-id="a9549-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="a9549-117">Accettare il valore predefinito o specificare una classe (ad esempio, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="a9549-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="a9549-118">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="a9549-118">Select **Add**.</span></span>

<span data-ttu-id="a9549-119">Nota: se si sta creando un nuovo contesto utente, non è necessario selezionare un file di cui eseguire l'override.</span><span class="sxs-lookup"><span data-stu-id="a9549-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a9549-120">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="a9549-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a9549-121">Se in precedenza non è stato installato il ASP.NET Core impalcatura, installarlo ora:</span><span class="sxs-lookup"><span data-stu-id="a9549-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="a9549-122">Aggiungere i riferimenti ai pacchetti NuGet necessari al file di progetto (con*estensione csproj*).</span><span class="sxs-lookup"><span data-stu-id="a9549-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="a9549-123">Eseguire i comandi seguenti nella directory del progetto:</span><span class="sxs-lookup"><span data-stu-id="a9549-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="a9549-124">Eseguire il comando seguente per elencare le opzioni dell'impalcatura di identità:</span><span class="sxs-lookup"><span data-stu-id="a9549-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="a9549-125">Nella cartella del progetto eseguire l'impalcatura di identità con le opzioni desiderate.</span><span class="sxs-lookup"><span data-stu-id="a9549-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="a9549-126">Ad esempio, per configurare Identity con l'interfaccia utente predefinita e il numero minimo di file, eseguire il comando seguente.</span><span class="sxs-lookup"><span data-stu-id="a9549-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="a9549-127">Usare il nome completo corretto per il contesto del database:</span><span class="sxs-lookup"><span data-stu-id="a9549-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="a9549-128">PowerShell usa il punto e virgola come separatore di comandi.</span><span class="sxs-lookup"><span data-stu-id="a9549-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="a9549-129">Quando si usa PowerShell, usare il carattere di escape per i punti e virgola nell'elenco dei file o inserire l'elenco di file tra virgolette doppie.</span><span class="sxs-lookup"><span data-stu-id="a9549-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="a9549-130">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9549-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="a9549-131">Se si esegue l'impalcatura delle identità senza specificare il `--files` flag o il `--useDefaultUI` flag, tutte le pagine dell'interfaccia utente di identità disponibili verranno create nel progetto.</span><span class="sxs-lookup"><span data-stu-id="a9549-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9549-132">Eseguire l'impalcatura delle identità:</span><span class="sxs-lookup"><span data-stu-id="a9549-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a9549-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9549-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a9549-134">Da **Esplora soluzioni**, fare clic con il pulsante destro del mouse sul progetto > **Aggiungi** > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="a9549-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="a9549-135">Dal riquadro sinistro della finestra di dialogo **Aggiungi impalcatura** selezionare **Identity** > **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="a9549-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="a9549-136">Nella finestra di dialogo **Aggiungi identità** selezionare le opzioni desiderate.</span><span class="sxs-lookup"><span data-stu-id="a9549-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="a9549-137">Selezionare la pagina layout esistente. in alternativa, il file di layout verrà sovrascritto con un markup errato.</span><span class="sxs-lookup"><span data-stu-id="a9549-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="a9549-138">Quando si seleziona un file \* \_ layout. cshtml\* esistente, questo **non** viene sovrascritto.</span><span class="sxs-lookup"><span data-stu-id="a9549-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="a9549-139">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9549-139">For example:</span></span>
    * <span data-ttu-id="a9549-140">`~/Pages/Shared/_Layout.cshtml`per Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a9549-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="a9549-141">`~/Views/Shared/_Layout.cshtml`per i progetti MVC</span><span class="sxs-lookup"><span data-stu-id="a9549-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="a9549-142">Per usare il contesto dei dati esistente, selezionare almeno un file di cui eseguire l'override.</span><span class="sxs-lookup"><span data-stu-id="a9549-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="a9549-143">È necessario selezionare almeno un file per aggiungere il contesto dei dati.</span><span class="sxs-lookup"><span data-stu-id="a9549-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="a9549-144">Selezionare la classe del contesto dati.</span><span class="sxs-lookup"><span data-stu-id="a9549-144">Select your data context class.</span></span>
  * <span data-ttu-id="a9549-145">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="a9549-145">Select **Add**.</span></span>
* <span data-ttu-id="a9549-146">Per creare un nuovo contesto utente ed eventualmente creare una classe utente personalizzata per l'identità:</span><span class="sxs-lookup"><span data-stu-id="a9549-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="a9549-147">Selezionare il **+** pulsante per creare una nuova **classe del contesto dati**.</span><span class="sxs-lookup"><span data-stu-id="a9549-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="a9549-148">Accettare il valore predefinito o specificare una classe (ad esempio, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="a9549-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="a9549-149">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="a9549-149">Select **Add**.</span></span>

<span data-ttu-id="a9549-150">Nota: se si sta creando un nuovo contesto utente, non è necessario selezionare un file di cui eseguire l'override.</span><span class="sxs-lookup"><span data-stu-id="a9549-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a9549-151">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="a9549-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a9549-152">Se in precedenza non è stato installato il ASP.NET Core impalcatura, installarlo ora:</span><span class="sxs-lookup"><span data-stu-id="a9549-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="a9549-153">Aggiungere un riferimento al pacchetto [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) al file di progetto (con*estensione csproj*).</span><span class="sxs-lookup"><span data-stu-id="a9549-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="a9549-154">Eseguire i comandi seguenti nella directory del progetto:</span><span class="sxs-lookup"><span data-stu-id="a9549-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="a9549-155">Eseguire il comando seguente per elencare le opzioni dell'impalcatura di identità:</span><span class="sxs-lookup"><span data-stu-id="a9549-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="a9549-156">Nella cartella del progetto eseguire l'impalcatura di identità con le opzioni desiderate.</span><span class="sxs-lookup"><span data-stu-id="a9549-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="a9549-157">Ad esempio, per configurare Identity con l'interfaccia utente predefinita e il numero minimo di file, eseguire il comando seguente.</span><span class="sxs-lookup"><span data-stu-id="a9549-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="a9549-158">Usare il nome completo corretto per il contesto del database:</span><span class="sxs-lookup"><span data-stu-id="a9549-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="a9549-159">PowerShell usa il punto e virgola come separatore di comandi.</span><span class="sxs-lookup"><span data-stu-id="a9549-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="a9549-160">Quando si usa PowerShell, usare il carattere di escape per i punti e virgola nell'elenco dei file o inserire l'elenco di file tra virgolette doppie.</span><span class="sxs-lookup"><span data-stu-id="a9549-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="a9549-161">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9549-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="a9549-162">Se si esegue l'impalcatura delle identità senza specificare il `--files` flag o il `--useDefaultUI` flag, tutte le pagine dell'interfaccia utente di identità disponibili verranno create nel progetto.</span><span class="sxs-lookup"><span data-stu-id="a9549-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
