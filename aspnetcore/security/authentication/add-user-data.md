---
title: Aggiungere, scaricare ed eliminare dati utente Identity in un progetto ASP.NET Core
author: rick-anderson
description: Informazioni su come aggiungere dati utente personalizzati a Identity in un progetto ASP.NET Core. Eliminare i dati per ogni GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/add-user-data
ms.openlocfilehash: 29c23e10d11eb1042b64fc071c221a9ead857fcc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777332"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="4a92f-104">Aggiungere, scaricare ed eliminare dati utente personalizzati nell'identità in un progetto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a92f-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="4a92f-105">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4a92f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4a92f-106">Questo articolo illustra come:</span><span class="sxs-lookup"><span data-stu-id="4a92f-106">This article shows how to:</span></span>

* <span data-ttu-id="4a92f-107">Aggiungere dati utente personalizzati a un'app Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4a92f-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="4a92f-108">Contrassegnare il modello di dati utente personalizzato <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> con l'attributo in modo che sia disponibile automaticamente per il download e l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="4a92f-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="4a92f-109">La possibilità di scaricare ed eliminare i dati consente di soddisfare i requisiti di [GDPR](xref:security/gdpr) .</span><span class="sxs-lookup"><span data-stu-id="4a92f-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="4a92f-110">Il progetto di esempio viene creato da un'app Web Razor Pages, ma le istruzioni sono simili per un'app Web MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4a92f-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="4a92f-111">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4a92f-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4a92f-112">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="4a92f-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="4a92f-113">Creare un'app Web Razor</span><span class="sxs-lookup"><span data-stu-id="4a92f-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a92f-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a92f-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="4a92f-115">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="4a92f-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="4a92f-116">Denominare il progetto **app Web 1** se si desidera che corrisponda allo spazio dei nomi del codice di [esempio di download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="4a92f-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="4a92f-117">Selezionare **ASP.NET Core applicazione** > Web **OK**</span><span class="sxs-lookup"><span data-stu-id="4a92f-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="4a92f-118">Selezionare **ASP.NET Core 3,0** nell'elenco a discesa</span><span class="sxs-lookup"><span data-stu-id="4a92f-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="4a92f-119">Selezionare l' **applicazione** > Web **OK**</span><span class="sxs-lookup"><span data-stu-id="4a92f-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="4a92f-120">Compilare ed eseguire il progetto.</span><span class="sxs-lookup"><span data-stu-id="4a92f-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="4a92f-121">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="4a92f-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="4a92f-122">Denominare il progetto **app Web 1** se si desidera che corrisponda allo spazio dei nomi del codice di [esempio di download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="4a92f-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="4a92f-123">Selezionare **ASP.NET Core applicazione** > Web **OK**</span><span class="sxs-lookup"><span data-stu-id="4a92f-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="4a92f-124">Selezionare **ASP.NET Core 2,2** nell'elenco a discesa</span><span class="sxs-lookup"><span data-stu-id="4a92f-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="4a92f-125">Selezionare l' **applicazione** > Web **OK**</span><span class="sxs-lookup"><span data-stu-id="4a92f-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="4a92f-126">Compilare ed eseguire il progetto.</span><span class="sxs-lookup"><span data-stu-id="4a92f-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="4a92f-127">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="4a92f-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="4a92f-128">Eseguire l'impalcatura delle identità</span><span class="sxs-lookup"><span data-stu-id="4a92f-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a92f-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a92f-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a92f-130">Da **Esplora soluzioni**, fare clic con il pulsante destro del mouse sul progetto > **Aggiungi** > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="4a92f-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4a92f-131">Dal riquadro sinistro della finestra di dialogo **Aggiungi impalcatura** selezionare **Identity** > **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="4a92f-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="4a92f-132">Nella finestra di dialogo **Aggiungi identità** sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="4a92f-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="4a92f-133">Selezionare il file di layout esistente *~/Pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4a92f-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="4a92f-134">Selezionare i seguenti file di cui eseguire l'override:</span><span class="sxs-lookup"><span data-stu-id="4a92f-134">Select the following files to override:</span></span>
    * <span data-ttu-id="4a92f-135">**Account/registro**</span><span class="sxs-lookup"><span data-stu-id="4a92f-135">**Account/Register**</span></span>
    * <span data-ttu-id="4a92f-136">**Account/Gestione/indice**</span><span class="sxs-lookup"><span data-stu-id="4a92f-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="4a92f-137">Selezionare il **+** pulsante per creare una nuova **classe del contesto dati**.</span><span class="sxs-lookup"><span data-stu-id="4a92f-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="4a92f-138">Accettare il tipo (**app Web 1. Models. WebApp1Context** se il progetto è denominato **app Web 1**).</span><span class="sxs-lookup"><span data-stu-id="4a92f-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="4a92f-139">Selezionare il **+** pulsante per creare una nuova **classe utente**.</span><span class="sxs-lookup"><span data-stu-id="4a92f-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="4a92f-140">Accettare il tipo (**WebApp1User** se il progetto è denominato **app Web 1**) > **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="4a92f-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="4a92f-141">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="4a92f-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4a92f-142">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="4a92f-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4a92f-143">Se in precedenza non è stato installato il ASP.NET Core impalcatura, installarlo ora:</span><span class="sxs-lookup"><span data-stu-id="4a92f-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="4a92f-144">Aggiungere un riferimento al pacchetto [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) al file di progetto (con estensione csproj).</span><span class="sxs-lookup"><span data-stu-id="4a92f-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="4a92f-145">Eseguire il comando seguente nella directory del progetto:</span><span class="sxs-lookup"><span data-stu-id="4a92f-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="4a92f-146">Eseguire il comando seguente per elencare le opzioni dell'impalcatura di identità:</span><span class="sxs-lookup"><span data-stu-id="4a92f-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="4a92f-147">Nella cartella del progetto eseguire l'impalcatura di identità:</span><span class="sxs-lookup"><span data-stu-id="4a92f-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="4a92f-148">Seguire le istruzioni in [Migrations, UseAuthentication e layout](xref:security/authentication/scaffold-identity#efm) per eseguire la procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="4a92f-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="4a92f-149">Creare una migrazione e aggiornare il database.</span><span class="sxs-lookup"><span data-stu-id="4a92f-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="4a92f-150">Aggiunta di `UseAuthentication` a `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="4a92f-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="4a92f-151">Aggiungere `<partial name="_LoginPartial" />` al file di layout.</span><span class="sxs-lookup"><span data-stu-id="4a92f-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="4a92f-152">Eseguire il test dell'app:</span><span class="sxs-lookup"><span data-stu-id="4a92f-152">Test the app:</span></span>
  * <span data-ttu-id="4a92f-153">Registrare un utente</span><span class="sxs-lookup"><span data-stu-id="4a92f-153">Register a user</span></span>
  * <span data-ttu-id="4a92f-154">Selezionare il nuovo nome utente (accanto al collegamento di **disconnessione** ).</span><span class="sxs-lookup"><span data-stu-id="4a92f-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="4a92f-155">Potrebbe essere necessario espandere la finestra o selezionare l'icona della barra di spostamento per visualizzare il nome utente e altri collegamenti.</span><span class="sxs-lookup"><span data-stu-id="4a92f-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="4a92f-156">Selezionare la scheda **dati personali** .</span><span class="sxs-lookup"><span data-stu-id="4a92f-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="4a92f-157">Selezionare il pulsante **download** ed esaminare il file *PersonalData. JSON* .</span><span class="sxs-lookup"><span data-stu-id="4a92f-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="4a92f-158">Testare il pulsante **Elimina per eliminare** l'utente che ha eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="4a92f-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="4a92f-159">Aggiungere dati utente personalizzati al database di identità</span><span class="sxs-lookup"><span data-stu-id="4a92f-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="4a92f-160">Aggiornare la `IdentityUser` classe derivata con proprietà personalizzate.</span><span class="sxs-lookup"><span data-stu-id="4a92f-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="4a92f-161">Se è stato denominato il progetto app Web 1, il file è denominato *areas/Identity/data/WebApp1User. cs*.</span><span class="sxs-lookup"><span data-stu-id="4a92f-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="4a92f-162">Aggiornare il file con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="4a92f-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="4a92f-163">Le proprietà con l'attributo [personaldata](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) sono:</span><span class="sxs-lookup"><span data-stu-id="4a92f-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="4a92f-164">Eliminato quando la pagina Razor *areas/Identity/Pages/account/Manage/DeletePersonalData. cshtml* chiama `UserManager.Delete`.</span><span class="sxs-lookup"><span data-stu-id="4a92f-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="4a92f-165">Incluso nei dati scaricati dalla pagina Razor *areas/Identity/Pages/account/Manage/DownloadPersonalData. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4a92f-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="4a92f-166">Aggiornare la pagina account/Manage/index. cshtml</span><span class="sxs-lookup"><span data-stu-id="4a92f-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="4a92f-167">Aggiornare `InputModel` in *areas/Identity/Pages/account/Manage/index. cshtml. cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="4a92f-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="4a92f-168">Aggiornare le *aree/Identity/Pages/account/Manage/index. cshtml* con il markup evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="4a92f-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="4a92f-169">Aggiornare le *aree/Identity/Pages/account/Manage/index. cshtml* con il markup evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="4a92f-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="4a92f-170">Aggiornare la pagina account/Register. cshtml</span><span class="sxs-lookup"><span data-stu-id="4a92f-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="4a92f-171">Aggiornare `InputModel` in *areas/Identity/Pages/account/Register. cshtml. cs* con il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="4a92f-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="4a92f-172">Aggiornare le *aree/Identity/Pages/account/Register. cshtml* con il markup evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="4a92f-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="4a92f-173">Aggiornare le *aree/Identity/Pages/account/Register. cshtml* con il markup evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="4a92f-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="4a92f-174">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="4a92f-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="4a92f-175">Aggiungere una migrazione per i dati utente personalizzati</span><span class="sxs-lookup"><span data-stu-id="4a92f-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a92f-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a92f-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a92f-177">Nella **console di gestione pacchetti**di Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="4a92f-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="4a92f-178">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="4a92f-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="4a92f-179">Test creare, visualizzare, scaricare, eliminare dati utente personalizzati</span><span class="sxs-lookup"><span data-stu-id="4a92f-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="4a92f-180">Eseguire il test dell'app:</span><span class="sxs-lookup"><span data-stu-id="4a92f-180">Test the app:</span></span>

* <span data-ttu-id="4a92f-181">Registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="4a92f-181">Register a new user.</span></span>
* <span data-ttu-id="4a92f-182">Visualizzare i dati utente personalizzati nella `/Identity/Account/Manage` pagina.</span><span class="sxs-lookup"><span data-stu-id="4a92f-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="4a92f-183">Scaricare e visualizzare i dati personali degli utenti dalla `/Identity/Account/Manage/PersonalData` pagina.</span><span class="sxs-lookup"><span data-stu-id="4a92f-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="4a92f-184">Aggiungere attestazioni Identity all'uso di IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="4a92f-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="4a92f-185">È possibile aggiungere attestazioni aggiuntive a Identity ASP.NET Core usando l' `IUserClaimsPrincipalFactory<T>` interfaccia.</span><span class="sxs-lookup"><span data-stu-id="4a92f-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="4a92f-186">Questa classe può essere aggiunta all'app nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="4a92f-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4a92f-187">Aggiungere l'implementazione personalizzata della classe come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="4a92f-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="4a92f-188">Il codice dimostrativo `ApplicationUser` usa la classe.</span><span class="sxs-lookup"><span data-stu-id="4a92f-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="4a92f-189">Questa classe aggiunge una `IsAdmin` proprietà che viene utilizzata per aggiungere l'attestazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="4a92f-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="4a92f-190">L'oggetto `AdditionalUserClaimsPrincipalFactory` implementa l'interfaccia `UserClaimsPrincipalFactory`.</span><span class="sxs-lookup"><span data-stu-id="4a92f-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="4a92f-191">Viene aggiunta una nuova attestazione di ruolo `ClaimsPrincipal`a.</span><span class="sxs-lookup"><span data-stu-id="4a92f-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

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

<span data-ttu-id="4a92f-192">L'attestazione aggiuntiva può quindi essere usata nell'app.</span><span class="sxs-lookup"><span data-stu-id="4a92f-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="4a92f-193">In una Razor pagina, l' `IAuthorizationService` istanza di può essere utilizzata per accedere al valore dell'attestazione.</span><span class="sxs-lookup"><span data-stu-id="4a92f-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

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
