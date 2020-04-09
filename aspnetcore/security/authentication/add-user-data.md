---
title: Aggiungere, scaricare ed eliminare i dati utente in Identity in un progetto ASP.NET CoreAdd, download, and delete user data to Identity in an ASP.NET Core project
author: rick-anderson
description: Informazioni su come aggiungere dati utente personalizzati a Identity in un progetto ASP.NET Core. Eliminare i dati per GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501221"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="b3f60-104">Aggiungere, scaricare ed eliminare dati utente personalizzati in Identity in un progetto ASP.NET CoreAdd, download, and delete custom user data to Identity in an ASP.NET Core project</span><span class="sxs-lookup"><span data-stu-id="b3f60-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="b3f60-105">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b3f60-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b3f60-106">Questo articolo illustra come:</span><span class="sxs-lookup"><span data-stu-id="b3f60-106">This article shows how to:</span></span>

* <span data-ttu-id="b3f60-107">Aggiungere dati utente personalizzati a un'app Web ASP.NET Core.Add custom user data to an ASP.NET Core web app.</span><span class="sxs-lookup"><span data-stu-id="b3f60-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="b3f60-108">Contrassegnare il modello di <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> dati utente personalizzato con l'attributo in modo che sia automaticamente disponibile per il download e l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="b3f60-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="b3f60-109">Rendere i dati in grado di essere scaricati ed eliminati aiuta a soddisfare i requisiti [GDPR.](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="b3f60-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="b3f60-110">L'esempio di progetto viene creato da un'app Web Razor Pages, ma le istruzioni sono simili per un'app Web MVC di base ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="b3f60-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="b3f60-111">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b3f60-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b3f60-112">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="b3f60-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="b3f60-113">Creare un'app Web Razor</span><span class="sxs-lookup"><span data-stu-id="b3f60-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3f60-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3f60-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="b3f60-115">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="b3f60-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="b3f60-116">Assegnare al progetto il nome **WebApp1** se si desidera che corrisponda allo spazio dei nomi del codice di esempio di [download.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)</span><span class="sxs-lookup"><span data-stu-id="b3f60-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="b3f60-117">Selezionare **ASP.NETapplicazione** > Web di base **OK**</span><span class="sxs-lookup"><span data-stu-id="b3f60-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="b3f60-118">Selezionare **ASP.NET Core 3.0** nell'elenco a discesa</span><span class="sxs-lookup"><span data-stu-id="b3f60-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="b3f60-119">Selezionare **Applicazione** > Web **OK**</span><span class="sxs-lookup"><span data-stu-id="b3f60-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="b3f60-120">Compilare ed eseguire il progetto.</span><span class="sxs-lookup"><span data-stu-id="b3f60-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="b3f60-121">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="b3f60-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="b3f60-122">Assegnare al progetto il nome **WebApp1** se si desidera che corrisponda allo spazio dei nomi del codice di esempio di [download.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)</span><span class="sxs-lookup"><span data-stu-id="b3f60-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="b3f60-123">Selezionare **ASP.NETapplicazione** > Web di base **OK**</span><span class="sxs-lookup"><span data-stu-id="b3f60-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="b3f60-124">Selezionare **ASP.NET Core 2.2** nell'elenco a discesa</span><span class="sxs-lookup"><span data-stu-id="b3f60-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="b3f60-125">Selezionare **Applicazione** > Web **OK**</span><span class="sxs-lookup"><span data-stu-id="b3f60-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="b3f60-126">Compilare ed eseguire il progetto.</span><span class="sxs-lookup"><span data-stu-id="b3f60-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="b3f60-127">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="b3f60-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="b3f60-128">Eseguire lo scaffolder di identità</span><span class="sxs-lookup"><span data-stu-id="b3f60-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3f60-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3f60-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b3f60-130">In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto > **Aggiungi** > **nuovo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="b3f60-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b3f60-131">Nel riquadro sinistro della finestra di dialogo **Aggiungi scaffold** selezionare **Identità** > **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="b3f60-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="b3f60-132">Nella finestra di dialogo **Aggiungi identità,** le seguenti opzioni:</span><span class="sxs-lookup"><span data-stu-id="b3f60-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="b3f60-133">Selezionare il file di layout *esistente: /Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b3f60-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="b3f60-134">Selezionare i seguenti file da sostituire:</span><span class="sxs-lookup"><span data-stu-id="b3f60-134">Select the following files to override:</span></span>
    * <span data-ttu-id="b3f60-135">**Conto/Registra**</span><span class="sxs-lookup"><span data-stu-id="b3f60-135">**Account/Register**</span></span>
    * <span data-ttu-id="b3f60-136">**Account/Gestione/Indice**</span><span class="sxs-lookup"><span data-stu-id="b3f60-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="b3f60-137">Selezionare **+** il pulsante per creare una nuova **classe di contesto dati**.</span><span class="sxs-lookup"><span data-stu-id="b3f60-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="b3f60-138">Accettare il tipo (**WebApp1.Models.WebApp1Context** se il progetto è denominato **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="b3f60-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="b3f60-139">Selezionare **+** il pulsante per creare una nuova **classe User**.</span><span class="sxs-lookup"><span data-stu-id="b3f60-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="b3f60-140">Accettare il tipo (**WebApp1User** se il progetto è denominato **WebApp1**) > **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="b3f60-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="b3f60-141">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="b3f60-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b3f60-142">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="b3f60-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b3f60-143">Se in precedenza non è stato installato lo scaffolder ASP.NET Core, installarlo ora:</span><span class="sxs-lookup"><span data-stu-id="b3f60-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="b3f60-144">Aggiungere un riferimento al pacchetto [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) al file di progetto (con estensione csproj).</span><span class="sxs-lookup"><span data-stu-id="b3f60-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="b3f60-145">Eseguire il comando seguente nella directory del progetto:</span><span class="sxs-lookup"><span data-stu-id="b3f60-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="b3f60-146">Eseguire il comando seguente per elencare le opzioni di Identity scaffolder:</span><span class="sxs-lookup"><span data-stu-id="b3f60-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="b3f60-147">Nella cartella del progetto, eseguire lo scaffolder di identità:</span><span class="sxs-lookup"><span data-stu-id="b3f60-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="b3f60-148">Seguire le istruzioni in [Migrazioni, UseAuthentication e layout](xref:security/authentication/scaffold-identity#efm) per eseguire la procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="b3f60-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="b3f60-149">Creare una migrazione e aggiornare il database.</span><span class="sxs-lookup"><span data-stu-id="b3f60-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="b3f60-150">Aggiunta di `UseAuthentication` a `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b3f60-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="b3f60-151">Aggiungi `<partial name="_LoginPartial" />` al file di layout.</span><span class="sxs-lookup"><span data-stu-id="b3f60-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="b3f60-152">Eseguire il test dell'app:</span><span class="sxs-lookup"><span data-stu-id="b3f60-152">Test the app:</span></span>
  * <span data-ttu-id="b3f60-153">Registrare un utente</span><span class="sxs-lookup"><span data-stu-id="b3f60-153">Register a user</span></span>
  * <span data-ttu-id="b3f60-154">Selezionare il nuovo nome utente (accanto al collegamento **Disconnetti).**</span><span class="sxs-lookup"><span data-stu-id="b3f60-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="b3f60-155">Potrebbe essere necessario espandere la finestra o selezionare l'icona della barra di spostamento per visualizzare il nome utente e altri collegamenti.</span><span class="sxs-lookup"><span data-stu-id="b3f60-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="b3f60-156">Selezionare la scheda **Dati personali.**</span><span class="sxs-lookup"><span data-stu-id="b3f60-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="b3f60-157">Selezionare il pulsante **Download** ed esaminare il file *PersonalData.json.*</span><span class="sxs-lookup"><span data-stu-id="b3f60-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="b3f60-158">Verificare il pulsante **Elimina,** che elimina l'utente connesso.</span><span class="sxs-lookup"><span data-stu-id="b3f60-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="b3f60-159">Aggiungere dati utente personalizzati al database di identitàAdd custom user data to the Identity DB</span><span class="sxs-lookup"><span data-stu-id="b3f60-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="b3f60-160">Aggiornare `IdentityUser` la classe derivata con proprietà personalizzate.</span><span class="sxs-lookup"><span data-stu-id="b3f60-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="b3f60-161">Se è stato denominato il progetto WebApp1, il file è denominato *Areas/Identity/Data/WebApp1User.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3f60-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="b3f60-162">Aggiornare il file con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="b3f60-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="b3f60-163">Le proprietà con l'attributo [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) sono:</span><span class="sxs-lookup"><span data-stu-id="b3f60-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="b3f60-164">Eliminato quando la pagina Razor *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* chiama `UserManager.Delete`.</span><span class="sxs-lookup"><span data-stu-id="b3f60-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="b3f60-165">Incluso nei dati scaricati dalla pagina Razor *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b3f60-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="b3f60-166">Aggiornare la pagina Account/Manage/Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b3f60-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="b3f60-167">Aggiornare `InputModel` il in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* con il seguente codice evidenziato:</span><span class="sxs-lookup"><span data-stu-id="b3f60-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="b3f60-168">Aggiornare *aree/Identity/Pages/Account/Manage/Index.cshtml* con il seguente markup evidenziato:</span><span class="sxs-lookup"><span data-stu-id="b3f60-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="b3f60-169">Aggiornare *aree/Identity/Pages/Account/Manage/Index.cshtml* con il seguente markup evidenziato:</span><span class="sxs-lookup"><span data-stu-id="b3f60-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="b3f60-170">Aggiornare la pagina Account/Register.cshtml</span><span class="sxs-lookup"><span data-stu-id="b3f60-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="b3f60-171">Aggiornare `InputModel` il in *Areas/Identity/Pages/Account/Register.cshtml.cs* con il seguente codice evidenziato:</span><span class="sxs-lookup"><span data-stu-id="b3f60-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="b3f60-172">Aggiornare il *codice Areas/Identity/Pages/Account/Register.cshtml* con il seguente markup evidenziato:</span><span class="sxs-lookup"><span data-stu-id="b3f60-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="b3f60-173">Aggiornare il *codice Areas/Identity/Pages/Account/Register.cshtml* con il seguente markup evidenziato:</span><span class="sxs-lookup"><span data-stu-id="b3f60-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="b3f60-174">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="b3f60-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="b3f60-175">Aggiungere una migrazione per i dati utente personalizzatiAdd a migration for the custom user data</span><span class="sxs-lookup"><span data-stu-id="b3f60-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3f60-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3f60-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b3f60-177">Nella console di Gestione **pacchetti**di Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b3f60-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="b3f60-178">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="b3f60-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="b3f60-179">Testare la creazione, la visualizzazione, il download, l'eliminazione dei dati utente personalizzati</span><span class="sxs-lookup"><span data-stu-id="b3f60-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="b3f60-180">Eseguire il test dell'app:</span><span class="sxs-lookup"><span data-stu-id="b3f60-180">Test the app:</span></span>

* <span data-ttu-id="b3f60-181">Registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="b3f60-181">Register a new user.</span></span>
* <span data-ttu-id="b3f60-182">Visualizzare i dati utente `/Identity/Account/Manage` personalizzati nella pagina.</span><span class="sxs-lookup"><span data-stu-id="b3f60-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="b3f60-183">Scaricare e visualizzare i dati `/Identity/Account/Manage/PersonalData` personali degli utenti dalla pagina.</span><span class="sxs-lookup"><span data-stu-id="b3f60-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="b3f60-184">Aggiungere attestazioni all'identità tramite IUserClaimsPrincipalFactoryAdd claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="b3f60-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="b3f60-185">È possibile aggiungere attestazioni aggiuntive a `IUserClaimsPrincipalFactory<T>` ASP.NETidentità di base tramite l'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="b3f60-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="b3f60-186">Questa classe può essere aggiunta `Startup.ConfigureServices` all'app nel metodo.</span><span class="sxs-lookup"><span data-stu-id="b3f60-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b3f60-187">Aggiungere l'implementazione personalizzata della classe come segue:</span><span class="sxs-lookup"><span data-stu-id="b3f60-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="b3f60-188">Il codice demo `ApplicationUser` utilizza la classe.</span><span class="sxs-lookup"><span data-stu-id="b3f60-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="b3f60-189">Questa classe `IsAdmin` aggiunge una proprietà che viene utilizzata per aggiungere l'attestazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="b3f60-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="b3f60-190">L'oggetto `AdditionalUserClaimsPrincipalFactory` implementa l'interfaccia `UserClaimsPrincipalFactory`.</span><span class="sxs-lookup"><span data-stu-id="b3f60-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="b3f60-191">Una nuova attestazione di `ClaimsPrincipal`ruolo viene aggiunta al file .</span><span class="sxs-lookup"><span data-stu-id="b3f60-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="b3f60-192">L'attestazione aggiuntiva può quindi essere usata nell'app.</span><span class="sxs-lookup"><span data-stu-id="b3f60-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="b3f60-193">In una pagina Razor, l'istanza `IAuthorizationService` può essere utilizzata per accedere al valore dell'attestazione.</span><span class="sxs-lookup"><span data-stu-id="b3f60-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
