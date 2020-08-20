---
title: Informazioni su come eseguire la migrazione da ASP.NET MVC a ASP.NET Core MVC
author: wadepickett
description: Informazioni su come iniziare a migrare un progetto MVC ASP.NET a ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: cd1a7ff57d911f96f0adfe4b548fa80ec844886d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632239"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="0a680-103">Eseguire la migrazione da ASP.NET MVC ad ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="0a680-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a680-104">Questo articolo illustra come iniziare la migrazione di un progetto MVC ASP.NET a [ASP.NET Core MVC](xref:mvc/overview).</span><span class="sxs-lookup"><span data-stu-id="0a680-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="0a680-105">Nel processo, vengono evidenziate le modifiche correlate rispetto a ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="0a680-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="0a680-106">La migrazione da ASP.NET MVC è un processo in più passaggi.</span><span class="sxs-lookup"><span data-stu-id="0a680-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="0a680-107">Questo articolo riguarda:</span><span class="sxs-lookup"><span data-stu-id="0a680-107">This article covers:</span></span>

* <span data-ttu-id="0a680-108">Configurazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="0a680-108">Initial setup.</span></span>
* <span data-ttu-id="0a680-109">Controller e visualizzazioni di base.</span><span class="sxs-lookup"><span data-stu-id="0a680-109">Basic controllers and views.</span></span>
* <span data-ttu-id="0a680-110">Contenuto statico.</span><span class="sxs-lookup"><span data-stu-id="0a680-110">Static content.</span></span>
* <span data-ttu-id="0a680-111">Dipendenze lato client.</span><span class="sxs-lookup"><span data-stu-id="0a680-111">Client-side dependencies.</span></span>

<span data-ttu-id="0a680-112">Per la migrazione della configurazione e del Identity codice, vedere eseguire la migrazione della [configurazione per ASP.NET Core](xref:migration/configuration) ed [eseguire la migrazione dell'autenticazione e Identity di ASP.NET Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="0a680-112">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0a680-113">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="0a680-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="0a680-114">Creare il progetto MVC Starter ASP.NET</span><span class="sxs-lookup"><span data-stu-id="0a680-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="0a680-115">Creare un esempio di progetto MVC ASP.NET in Visual Studio per eseguire la migrazione:</span><span class="sxs-lookup"><span data-stu-id="0a680-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="0a680-116">Scegliere **Nuovo** > **Progetto** dal menu **File**.</span><span class="sxs-lookup"><span data-stu-id="0a680-116">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="0a680-117">Selezionare **applicazione Web ASP.NET (.NET Framework)** e quindi fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="0a680-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next**.</span></span>
1. <span data-ttu-id="0a680-118">Denominare il progetto *app Web 1* in modo che lo spazio dei nomi corrisponda al progetto ASP.NET Core creato nel passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="0a680-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="0a680-119">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="0a680-119">Select **Create**.</span></span>
1. <span data-ttu-id="0a680-120">Selezionare **MVC**, quindi fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="0a680-120">Select **MVC**, and then select **Create**.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="0a680-121">Creare il progetto di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a680-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="0a680-122">Crea una nuova soluzione con un nuovo progetto di ASP.NET Core di cui eseguire la migrazione:</span><span class="sxs-lookup"><span data-stu-id="0a680-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="0a680-123">Avviare una seconda istanza di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0a680-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="0a680-124">Scegliere **Nuovo** > **Progetto** dal menu **File**.</span><span class="sxs-lookup"><span data-stu-id="0a680-124">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="0a680-125">Selezionare **applicazione web ASP.NET Web Core** e quindi fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="0a680-125">Select **ASP.NET Web Core Web Application** and then select **Next**.</span></span>
1. <span data-ttu-id="0a680-126">Nella finestra di dialogo **Configura nuovo progetto** assegnare al progetto il nome *app Web 1*.</span><span class="sxs-lookup"><span data-stu-id="0a680-126">In the **Configure your new project** dialog, Name the project *WebApp1*.</span></span>
1. <span data-ttu-id="0a680-127">Impostare il percorso su una directory diversa da quella del progetto precedente per usare lo stesso nome di progetto.</span><span class="sxs-lookup"><span data-stu-id="0a680-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="0a680-128">L'utilizzo dello stesso spazio dei nomi rende più semplice la copia di codice tra i due progetti.</span><span class="sxs-lookup"><span data-stu-id="0a680-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="0a680-129">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="0a680-129">Select **Create**.</span></span>
1. <span data-ttu-id="0a680-130">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.net Core** e **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="0a680-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="0a680-131">Selezionare il modello di progetto **applicazione Web (modello-vista-controller)** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="0a680-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create**.</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="0a680-132">Configurare il sito di ASP.NET Core per l'uso di MVC</span><span class="sxs-lookup"><span data-stu-id="0a680-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="0a680-133">Nei progetti ASP.NET Core 3,0 e versioni successive .NET Framework non è più un Framework di destinazione supportato.</span><span class="sxs-lookup"><span data-stu-id="0a680-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="0a680-134">Il progetto deve avere come destinazione .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-134">Your project must target .NET Core.</span></span> <span data-ttu-id="0a680-135">Il ASP.NET Core Framework condiviso, che include MVC, fa parte dell'installazione del runtime di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="0a680-136">Al Framework condiviso viene fatto automaticamente riferimento quando si usa l' `Microsoft.NET.Sdk.Web` SDK nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="0a680-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="0a680-137">Per ulteriori informazioni, vedere [riferimento a Framework](xref:migration/22-to-30#framework-reference).</span><span class="sxs-lookup"><span data-stu-id="0a680-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="0a680-138">In ASP.NET Core la `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="0a680-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="0a680-139">Sostituisce *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="0a680-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="0a680-140">Gestisce tutte le attività di avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a680-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="0a680-141">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0a680-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="0a680-142">Nel progetto ASP.NET Core aprire il file *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="0a680-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="0a680-143">ASP.NET Core app devono acconsentire esplicitamente alle funzionalità del Framework con middleware.</span><span class="sxs-lookup"><span data-stu-id="0a680-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="0a680-144">Il codice generato dal modello precedente aggiunge i servizi e il middleware seguenti:</span><span class="sxs-lookup"><span data-stu-id="0a680-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="0a680-145">Il <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> metodo di estensione registra il supporto del servizio MVC per i controller, le funzionalità correlate all'API e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="0a680-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="0a680-146">Per ulteriori informazioni sulle opzioni di registrazione del servizio MVC, vedere la pagina relativa alla [registrazione del servizio MVC](xref:migration/22-to-30#mvc-service-registration)</span><span class="sxs-lookup"><span data-stu-id="0a680-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="0a680-147">Il <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> metodo di estensione aggiunge il gestore di file statici `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="0a680-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="0a680-148">Il `UseStaticFiles` metodo di estensione deve essere chiamato prima di `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="0a680-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="0a680-149">Per altre informazioni, vedere <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="0a680-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="0a680-150">Il <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> metodo di estensione aggiunge il routing.</span><span class="sxs-lookup"><span data-stu-id="0a680-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="0a680-151">Per altre informazioni, vedere <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="0a680-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="0a680-152">Questa configurazione esistente include gli elementi necessari per eseguire la migrazione del progetto ASP.NET MVC di esempio.</span><span class="sxs-lookup"><span data-stu-id="0a680-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="0a680-153">Per ulteriori informazioni sulle opzioni del middleware ASP.NET Core, vedere <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="0a680-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="0a680-154">Migrare controller e visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="0a680-154">Migrate controllers and views</span></span>

<span data-ttu-id="0a680-155">Nel progetto ASP.NET Core verranno aggiunte una nuova classe controller vuota e una classe di visualizzazione per fungere da segnaposto usando gli stessi nomi delle classi controller e View in qualsiasi progetto MVC ASP.NET da cui eseguire la migrazione.</span><span class="sxs-lookup"><span data-stu-id="0a680-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="0a680-156">Il progetto *app web 1* ASP.NET Core include già un controller e una visualizzazione di esempio minimi con lo stesso nome del progetto MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="0a680-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="0a680-157">Quindi, verranno usati come segnaposto per il controller e le visualizzazioni MVC ASP.NET di cui eseguire la migrazione dal progetto ASP.NET MVC *app Web 1* .</span><span class="sxs-lookup"><span data-stu-id="0a680-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="0a680-158">Copiare i metodi da ASP.NET MVC `HomeController` per sostituire i nuovi metodi di ASP.NET Core `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="0a680-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="0a680-159">Non è necessario modificare il tipo restituito dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="0a680-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="0a680-160">Il tipo restituito del metodo di azione del controller del modello predefinito MVC ASP.NET è [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC i metodi di azione restituiscono `IActionResult` invece.</span><span class="sxs-lookup"><span data-stu-id="0a680-160">The ASP.NET MVC built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="0a680-161">`ActionResult` implementa `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="0a680-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="0a680-162">Nel progetto ASP.NET Core fare clic con il pulsante destro del mouse sulla directory *views/Home* e scegliere **Aggiungi** > **elemento esistente**.</span><span class="sxs-lookup"><span data-stu-id="0a680-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="0a680-163">Nella finestra di dialogo **Aggiungi elemento esistente** passare alla *Home* directory ASP.NET MVC *app Web 1* Project.</span><span class="sxs-lookup"><span data-stu-id="0a680-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="0a680-164">Selezionare i file *di visualizzazione About. cshtml*, *Contact. cshtml*e *index. cshtml* Razor , quindi selezionare **Aggiungi**, sostituendo i file esistenti.</span><span class="sxs-lookup"><span data-stu-id="0a680-164">Select the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files, then select **Add**, replacing the existing files.</span></span>

<span data-ttu-id="0a680-165">Per altre informazioni, vedere <xref:mvc/controllers/actions> e <xref:mvc/views/overview>.</span><span class="sxs-lookup"><span data-stu-id="0a680-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="0a680-166">Testare ogni metodo</span><span class="sxs-lookup"><span data-stu-id="0a680-166">Test each method</span></span>

<span data-ttu-id="0a680-167">Ogni endpoint controller può essere testato, tuttavia, il layout e gli stili vengono trattati più avanti nel documento.</span><span class="sxs-lookup"><span data-stu-id="0a680-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="0a680-168">Eseguire l'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="0a680-169">Richiamare le visualizzazioni sottoposte a rendering dal browser nell'app ASP.NET Core in esecuzione sostituendo il numero di porta corrente con il numero di porta usato nel progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="0a680-170">Ad esempio: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="0a680-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="0a680-171">Migrare contenuto statico</span><span class="sxs-lookup"><span data-stu-id="0a680-171">Migrate static content</span></span>

<span data-ttu-id="0a680-172">In ASP.NET MVC 5 e versioni precedenti, il contenuto statico era ospitato dalla directory radice del progetto Web ed era combinato con i file lato server.</span><span class="sxs-lookup"><span data-stu-id="0a680-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="0a680-173">In ASP.NET Core i file statici vengono archiviati nella directory [radice Web](xref:fundamentals/index#web-root) del progetto.</span><span class="sxs-lookup"><span data-stu-id="0a680-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="0a680-174">La directory predefinita è *{Content root}/wwwroot*, ma può essere modificata.</span><span class="sxs-lookup"><span data-stu-id="0a680-174">The default directory is *{content root}/wwwroot*, but it can be changed.</span></span> <span data-ttu-id="0a680-175">Per altre informazioni, vedere [File statici in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span><span class="sxs-lookup"><span data-stu-id="0a680-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="0a680-176">Copiare il contenuto statico dal progetto ASP.NET MVC *app Web 1* alla directory *wwwroot* nel progetto ASP.NET Core *app Web 1* :</span><span class="sxs-lookup"><span data-stu-id="0a680-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="0a680-177">Nel progetto ASP.NET Core fare clic con il pulsante destro del mouse sulla directory *wwwroot* , quindi scegliere **Aggiungi** > **elemento esistente**.</span><span class="sxs-lookup"><span data-stu-id="0a680-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="0a680-178">Nella finestra di dialogo **Aggiungi elemento esistente** passare al progetto ASP.NET MVC *app Web 1* .</span><span class="sxs-lookup"><span data-stu-id="0a680-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="0a680-179">Selezionare il file *favicon. ico* , quindi selezionare **Aggiungi**, sostituendo il file esistente.</span><span class="sxs-lookup"><span data-stu-id="0a680-179">Select the *favicon.ico* file, then select **Add**, replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="0a680-180">Migrare i file di layout</span><span class="sxs-lookup"><span data-stu-id="0a680-180">Migrate the layout files</span></span>

<span data-ttu-id="0a680-181">Copiare i file di layout del progetto MVC ASP.NET nel progetto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0a680-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="0a680-182">Nel progetto ASP.NET Core fare clic con il pulsante destro del mouse sulla directory *views* , quindi scegliere **Aggiungi** > **elemento esistente**.</span><span class="sxs-lookup"><span data-stu-id="0a680-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="0a680-183">Nella finestra di dialogo **Aggiungi elemento esistente** passare alla directory *Views* del progetto ASP.NET MVC *app Web 1* .</span><span class="sxs-lookup"><span data-stu-id="0a680-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="0a680-184">Selezionare il file *_ViewStart. cshtml,* quindi selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="0a680-184">Select the *_ViewStart.cshtml* file then select **Add**.</span></span>

<span data-ttu-id="0a680-185">Copiare i file di layout condiviso del progetto MVC ASP.NET nel progetto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0a680-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="0a680-186">Nel progetto ASP.NET Core fare clic con il pulsante destro del mouse sulla directory *Views/Shared* e scegliere **Aggiungi** > **elemento esistente**.</span><span class="sxs-lookup"><span data-stu-id="0a680-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="0a680-187">Nella finestra di dialogo **Aggiungi elemento esistente** passare alla directory *viste/condivise* del progetto ASP.NET MVC *app Web 1* .</span><span class="sxs-lookup"><span data-stu-id="0a680-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="0a680-188">Selezionare il file *_Layout. cshtml* , quindi selezionare **Aggiungi**, sostituendo il file esistente.</span><span class="sxs-lookup"><span data-stu-id="0a680-188">Select the *_Layout.cshtml* file, then select **Add**, replacing the existing file.</span></span>

<span data-ttu-id="0a680-189">Nel progetto ASP.NET Core aprire il file *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="0a680-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="0a680-190">Apportare le seguenti modifiche in modo che corrispondano al codice completato riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="0a680-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="0a680-191">Aggiornare l'inclusione CSS bootstrap in modo che corrisponda al codice completato riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="0a680-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="0a680-192">Sostituire `@Styles.Render("~/Content/css")` con un `<link>` elemento per caricare *bootstrap. CSS* (vedere di seguito).</span><span class="sxs-lookup"><span data-stu-id="0a680-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="0a680-193">Rimuovere `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="0a680-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="0a680-194">Markup di sostituzione completato per l'inclusione CSS bootstrap:</span><span class="sxs-lookup"><span data-stu-id="0a680-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="0a680-195">Aggiornare l'inclusione di JavaScript jQuery e bootstrap in modo che corrisponda al codice completato riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="0a680-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="0a680-196">Sostituire `@Scripts.Render("~/bundles/jquery")` con un `<script>` elemento (vedere di seguito).</span><span class="sxs-lookup"><span data-stu-id="0a680-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="0a680-197">Sostituire `@Scripts.Render("~/bundles/bootstrap")` con un `<script>` elemento (vedere di seguito).</span><span class="sxs-lookup"><span data-stu-id="0a680-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="0a680-198">Markup di sostituzione completato per l'inclusione di jQuery e bootstrap JavaScript:</span><span class="sxs-lookup"><span data-stu-id="0a680-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="0a680-199">Il file *_Layout. cshtml* aggiornato è illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="0a680-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="0a680-200">Visualizzare il sito nel browser.</span><span class="sxs-lookup"><span data-stu-id="0a680-200">View the site in the browser.</span></span> <span data-ttu-id="0a680-201">Il rendering dovrebbe essere eseguito con gli stili previsti sul posto.</span><span class="sxs-lookup"><span data-stu-id="0a680-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="0a680-202">Configurare la creazione di bundle e minification</span><span class="sxs-lookup"><span data-stu-id="0a680-202">Configure bundling and minification</span></span>

<span data-ttu-id="0a680-203">ASP.NET Core è compatibile con diverse soluzioni open source per la creazione di bundle e minification, ad esempio [Weboptimizer](https://github.com/ligershark/WebOptimizer) e altre librerie simili.</span><span class="sxs-lookup"><span data-stu-id="0a680-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="0a680-204">ASP.NET Core non fornisce una soluzione nativa per la creazione di bundle e minification.</span><span class="sxs-lookup"><span data-stu-id="0a680-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="0a680-205">Per informazioni sulla configurazione della creazione di bundle e minification, vedere [bundleing and minification](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="0a680-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="0a680-206">Risolvere gli errori HTTP 500</span><span class="sxs-lookup"><span data-stu-id="0a680-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="0a680-207">Ci sono molti problemi che possono causare un messaggio di errore HTTP 500 che non contiene informazioni sull'origine del problema.</span><span class="sxs-lookup"><span data-stu-id="0a680-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="0a680-208">Se, ad esempio, il file *views/_ViewImports. cshtml* contiene uno spazio dei nomi che non esiste nel progetto, viene generato un errore HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="0a680-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="0a680-209">Per impostazione predefinita, nelle app ASP.NET Core, l' `UseDeveloperExceptionPage` estensione viene aggiunta a `IApplicationBuilder` ed eseguita quando l'ambiente è in *fase di sviluppo*.</span><span class="sxs-lookup"><span data-stu-id="0a680-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development*.</span></span> <span data-ttu-id="0a680-210">Questa operazione è descritta in dettaglio nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0a680-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="0a680-211">ASP.NET Core converte le eccezioni non gestite in risposte di errore HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="0a680-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="0a680-212">In genere, i dettagli dell'errore non sono inclusi in queste risposte per impedire la divulgazione di informazioni potenzialmente riservate sul server.</span><span class="sxs-lookup"><span data-stu-id="0a680-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="0a680-213">Per ulteriori informazioni, vedere [pagina delle eccezioni per sviluppatori](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="0a680-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="0a680-214">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="0a680-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="0a680-215">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0a680-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="0a680-216">Questo articolo illustra come avviare la migrazione di un progetto MVC ASP.NET a [ASP.NET Core mvc](xref:mvc/overview) 2,2.</span><span class="sxs-lookup"><span data-stu-id="0a680-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="0a680-217">Nel processo, vengono evidenziati molti degli elementi che sono stati modificati rispetto a ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="0a680-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="0a680-218">La migrazione da ASP.NET MVC è un processo in più passaggi.</span><span class="sxs-lookup"><span data-stu-id="0a680-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="0a680-219">Questo articolo riguarda:</span><span class="sxs-lookup"><span data-stu-id="0a680-219">This article covers:</span></span>

* <span data-ttu-id="0a680-220">Configurazione iniziale</span><span class="sxs-lookup"><span data-stu-id="0a680-220">Initial setup</span></span>
* <span data-ttu-id="0a680-221">Controller e visualizzazioni di base</span><span class="sxs-lookup"><span data-stu-id="0a680-221">Basic controllers and views</span></span>
* <span data-ttu-id="0a680-222">Contenuto statico</span><span class="sxs-lookup"><span data-stu-id="0a680-222">Static content</span></span>
* <span data-ttu-id="0a680-223">Dipendenze lato client.</span><span class="sxs-lookup"><span data-stu-id="0a680-223">Client-side dependencies.</span></span>

<span data-ttu-id="0a680-224">Per la migrazione della configurazione e del Identity codice, vedere <xref:migration/configuration> e <xref:migration/identity> .</span><span class="sxs-lookup"><span data-stu-id="0a680-224">For migrating configuration and Identity code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="0a680-225">I numeri di versione negli esempi potrebbero non essere aggiornati, aggiornare i progetti di conseguenza.</span><span class="sxs-lookup"><span data-stu-id="0a680-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="0a680-226">Creare il progetto MVC Starter ASP.NET</span><span class="sxs-lookup"><span data-stu-id="0a680-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="0a680-227">Per illustrare l'aggiornamento, si inizierà con la creazione di un'app MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="0a680-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="0a680-228">Crearlo con il nome *app Web 1* in modo che lo spazio dei nomi corrisponda al progetto ASP.NET Core creato nel passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="0a680-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](mvc/_static/new-project.png)

![Finestra di dialogo nuova applicazione Web: modello di progetto MVC selezionato nel pannello modelli ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="0a680-231">*Facoltativo:* Modificare il nome della soluzione da *app Web 1* a *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="0a680-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="0a680-232">In Visual Studio viene visualizzato il nuovo nome della soluzione (*Mvc5*), che rende più semplice la creazione di questo progetto dal progetto successivo.</span><span class="sxs-lookup"><span data-stu-id="0a680-232">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="0a680-233">Creare il progetto di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a680-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="0a680-234">Creare una nuova app Web ASP.NET Core *vuota* con lo stesso nome del progetto precedente (*app Web 1*) in modo che gli spazi dei nomi nei due progetti corrispondano.</span><span class="sxs-lookup"><span data-stu-id="0a680-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="0a680-235">La presenza dello stesso spazio dei nomi rende più semplice la copia di codice tra i due progetti.</span><span class="sxs-lookup"><span data-stu-id="0a680-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="0a680-236">Creare il progetto in una directory diversa da quella del progetto precedente per usare lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="0a680-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![Finestra di dialogo Nuovo progetto](mvc/_static/new_core.png)

![Finestra di dialogo nuova applicazione Web ASP.NET: modello di progetto vuoto selezionato nel pannello modelli ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="0a680-239">*Facoltativo:* Creare una nuova app ASP.NET Core usando il modello di progetto *applicazione Web* .</span><span class="sxs-lookup"><span data-stu-id="0a680-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="0a680-240">Denominare il progetto *app Web 1*e selezionare un'opzione di autenticazione per **singoli account utente**.</span><span class="sxs-lookup"><span data-stu-id="0a680-240">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="0a680-241">Rinominare l'app in *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="0a680-241">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="0a680-242">La creazione di questo progetto consente di risparmiare tempo nella conversione.</span><span class="sxs-lookup"><span data-stu-id="0a680-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="0a680-243">Il risultato finale può essere visualizzato nel codice generato dal modello, il codice può essere copiato nel progetto di conversione o confrontato con il progetto generato dal modello.</span><span class="sxs-lookup"><span data-stu-id="0a680-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="0a680-244">Configurare il sito per l'uso di MVC</span><span class="sxs-lookup"><span data-stu-id="0a680-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="0a680-245">Quando la destinazione è .NET Core, per impostazione predefinita viene fatto riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="0a680-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="0a680-246">Questo pacchetto contiene i pacchetti usati comunemente dalle app MVC.</span><span class="sxs-lookup"><span data-stu-id="0a680-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="0a680-247">Se la destinazione è .NET Framework, i riferimenti ai pacchetti devono essere elencati singolarmente nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="0a680-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="0a680-248">`Microsoft.AspNetCore.Mvc` è il Framework di MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="0a680-249">`Microsoft.AspNetCore.StaticFiles` è il gestore di file statici.</span><span class="sxs-lookup"><span data-stu-id="0a680-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="0a680-250">ASP.NET Core le app scelgono esplicitamente il middleware, ad esempio per la conservazione dei file statici.</span><span class="sxs-lookup"><span data-stu-id="0a680-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="0a680-251">Per altre informazioni, vedere [File statici](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="0a680-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="0a680-252">Aprire il file *Startup.cs* e modificare il codice in modo che corrisponda a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="0a680-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="0a680-253">Il <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> metodo di estensione aggiunge il gestore di file statici.</span><span class="sxs-lookup"><span data-stu-id="0a680-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="0a680-254">Per ulteriori informazioni, vedere Startup e [routing](xref:fundamentals/routing) [dell'applicazione](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="0a680-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="0a680-255">Aggiungere un controller e una visualizzazione</span><span class="sxs-lookup"><span data-stu-id="0a680-255">Add a controller and view</span></span>

<span data-ttu-id="0a680-256">In questa sezione vengono aggiunti un controller e una visualizzazione minimi per fungere da segnaposto per il controller MVC ASP.NET e le visualizzazioni migrate nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="0a680-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="0a680-257">Aggiungere una directory *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="0a680-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="0a680-258">Aggiungere una **classe controller** denominata *HomeController.cs* alla directory *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="0a680-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Finestra di dialogo Aggiungi nuovo elemento](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="0a680-260">Aggiungere una directory *views* .</span><span class="sxs-lookup"><span data-stu-id="0a680-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="0a680-261">Aggiungere una directory *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="0a680-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="0a680-262">Aggiungere una \*\* Razor vista\*\* denominata *index. cshtml* alla directory *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="0a680-262">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Finestra di dialogo Aggiungi nuovo elemento](mvc/_static/view.png)

<span data-ttu-id="0a680-264">La struttura del progetto è illustrata di seguito:</span><span class="sxs-lookup"><span data-stu-id="0a680-264">The project structure is shown below:</span></span>

![Esplora soluzioni la visualizzazione di file e directory di app Web 1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="0a680-266">Sostituire il contenuto del file *Views/Home/Index.cshtml* con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="0a680-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="0a680-267">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="0a680-267">Run the app.</span></span>

![App Web aperta in Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="0a680-269">Per altre informazioni, vedere [controller](xref:mvc/controllers/actions) e [visualizzazioni](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="0a680-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="0a680-270">Le funzionalità seguenti richiedono la migrazione dal progetto MVC ASP.NET di esempio al progetto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0a680-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="0a680-271">contenuto lato client (CSS, tipi di carattere e script)</span><span class="sxs-lookup"><span data-stu-id="0a680-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="0a680-272">controllers</span><span class="sxs-lookup"><span data-stu-id="0a680-272">controllers</span></span>

* <span data-ttu-id="0a680-273">Viste</span><span class="sxs-lookup"><span data-stu-id="0a680-273">views</span></span>

* <span data-ttu-id="0a680-274">modelli</span><span class="sxs-lookup"><span data-stu-id="0a680-274">models</span></span>

* <span data-ttu-id="0a680-275">Bundle</span><span class="sxs-lookup"><span data-stu-id="0a680-275">bundling</span></span>

* <span data-ttu-id="0a680-276">filters</span><span class="sxs-lookup"><span data-stu-id="0a680-276">filters</span></span>

* <span data-ttu-id="0a680-277">Accesso/uscita Identity (operazione eseguita nell'esercitazione successiva).</span><span class="sxs-lookup"><span data-stu-id="0a680-277">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="0a680-278">Controller e visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="0a680-278">Controllers and views</span></span>

* <span data-ttu-id="0a680-279">Copiare tutti i metodi da ASP.NET MVC `HomeController` al nuovo `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="0a680-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="0a680-280">In ASP.NET MVC, il tipo restituito del metodo di azione del controller del modello predefinito è [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC i metodi di azione restituiscono `IActionResult` invece.</span><span class="sxs-lookup"><span data-stu-id="0a680-280">In ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="0a680-281">`ActionResult` implementa `IActionResult` , quindi non è necessario modificare il tipo restituito dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="0a680-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="0a680-282">Copiare i file *di visualizzazione About. cshtml*, *Contact. cshtml*e *index. cshtml* Razor dal progetto MVC ASP.NET al progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-282">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="0a680-283">Testare ogni metodo</span><span class="sxs-lookup"><span data-stu-id="0a680-283">Test each method</span></span>

<span data-ttu-id="0a680-284">Il file di layout e gli stili non sono ancora stati migrati, quindi le visualizzazioni sottoposte a rendering contengono solo il contenuto dei file di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="0a680-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="0a680-285">Il file di layout i collegamenti generati per le `About` `Contact` visualizzazioni e non saranno ancora disponibili.</span><span class="sxs-lookup"><span data-stu-id="0a680-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="0a680-286">Richiamare le visualizzazioni sottoposte a rendering dal browser nell'app ASP.NET Core in esecuzione sostituendo il numero di porta corrente con il numero di porta usato nel progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="0a680-287">Ad esempio: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="0a680-287">For example: `https://localhost:44375/home/about`.</span></span>

![Pagina contatto](mvc/_static/contact-page.png)

<span data-ttu-id="0a680-289">Si noti la mancanza di stili e voci di menu.</span><span class="sxs-lookup"><span data-stu-id="0a680-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="0a680-290">Lo stile verrà risolto nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="0a680-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="0a680-291">Contenuto statico</span><span class="sxs-lookup"><span data-stu-id="0a680-291">Static content</span></span>

<span data-ttu-id="0a680-292">In ASP.NET MVC 5 e versioni precedenti, il contenuto statico era ospitato dalla radice del progetto Web ed era combinato con i file lato server.</span><span class="sxs-lookup"><span data-stu-id="0a680-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="0a680-293">In ASP.NET Core, il contenuto statico è ospitato nella directory *wwwroot*</span><span class="sxs-lookup"><span data-stu-id="0a680-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="0a680-294">Copiare il contenuto statico dall'app MVC ASP.NET alla directory *wwwroot* nel progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="0a680-295">In questo esempio di conversione:</span><span class="sxs-lookup"><span data-stu-id="0a680-295">In this sample conversion:</span></span>

* <span data-ttu-id="0a680-296">Copiare il file *favicon. ico* dal progetto MVC ASP.NET alla directory *wwwroot* nel progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="0a680-297">Il progetto MVC ASP.NET usa [bootstrap](https://getbootstrap.com/) per lo stile e archivia i file bootstrap nelle directory *Content* e *Scripts* .</span><span class="sxs-lookup"><span data-stu-id="0a680-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="0a680-298">Il modello, che ha generato il progetto MVC ASP.NET, fa riferimento a bootstrap nel file di layout (*Views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="0a680-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="0a680-299">È possibile copiare i file *bootstrap.js* e *bootstrap. CSS* dal progetto MVC ASP.NET alla directory *wwwroot* del nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="0a680-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="0a680-300">Al contrario, questo documento aggiunge il supporto per bootstrap (e altre librerie lato client) usando CDNs nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="0a680-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="0a680-301">Eseguire la migrazione del file di layout</span><span class="sxs-lookup"><span data-stu-id="0a680-301">Migrate the layout file</span></span>

* <span data-ttu-id="0a680-302">Copiare il file *_ViewStart. cshtml* dalla directory *views* del progetto MVC ASP.NET alla directory *views* del progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="0a680-303">Il file *_ViewStart. cshtml* non è stato modificato in ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0a680-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="0a680-304">Creare una *vista/directory condivisa* .</span><span class="sxs-lookup"><span data-stu-id="0a680-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="0a680-305">*Facoltativo:* Copiare *_ViewImports. cshtml* dalla directory *views* del progetto MVC *FullAspNetCore* alla directory *views* del progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="0a680-306">Rimuovere qualsiasi dichiarazione dello spazio dei nomi nel file *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="0a680-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="0a680-307">Il file *_ViewImports. cshtml* fornisce gli spazi dei nomi per tutti i file di visualizzazione e porta gli [Helper Tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="0a680-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="0a680-308">Gli helper tag vengono usati nel nuovo file di layout.</span><span class="sxs-lookup"><span data-stu-id="0a680-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="0a680-309">Il file *_ViewImports. cshtml* è nuovo per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="0a680-310">Copiare il file *_Layout. cshtml* dalla directory *Views/Shared* del progetto MVC ASP.NET nella directory *views/* Shared del progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="0a680-311">Aprire *_Layout file cshtml* e apportare le modifiche seguenti (il codice completato è riportato di seguito):</span><span class="sxs-lookup"><span data-stu-id="0a680-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="0a680-312">Sostituire `@Styles.Render("~/Content/css")` con un `<link>` elemento per caricare *bootstrap. CSS* (vedere di seguito).</span><span class="sxs-lookup"><span data-stu-id="0a680-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="0a680-313">Rimuovere `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="0a680-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="0a680-314">Imposta come commento la riga, che `@Html.Partial("_LoginPartial")` racchiude la riga con `@*...*@` .</span><span class="sxs-lookup"><span data-stu-id="0a680-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="0a680-315">Per ulteriori informazioni, vedere la pagina relativa [alla migrazione dell'autenticazione e Identity alla ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="0a680-315">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="0a680-316">Sostituire `@Scripts.Render("~/bundles/jquery")` con un `<script>` elemento (vedere di seguito).</span><span class="sxs-lookup"><span data-stu-id="0a680-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="0a680-317">Sostituire `@Scripts.Render("~/bundles/bootstrap")` con un `<script>` elemento (vedere di seguito).</span><span class="sxs-lookup"><span data-stu-id="0a680-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="0a680-318">Markup sostitutivo per l'inclusione CSS bootstrap:</span><span class="sxs-lookup"><span data-stu-id="0a680-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="0a680-319">Markup sostitutivo per jQuery e bootstrap JavaScript inclusion:</span><span class="sxs-lookup"><span data-stu-id="0a680-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="0a680-320">Il file *_Layout. cshtml* aggiornato è illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="0a680-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="0a680-321">Visualizzare il sito nel browser.</span><span class="sxs-lookup"><span data-stu-id="0a680-321">View the site in the browser.</span></span> <span data-ttu-id="0a680-322">A questo punto, dovrebbe essere caricato correttamente con gli stili previsti.</span><span class="sxs-lookup"><span data-stu-id="0a680-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="0a680-323">*Facoltativo:* Provare a usare il nuovo file di layout.</span><span class="sxs-lookup"><span data-stu-id="0a680-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="0a680-324">Copiare il file di layout dal progetto *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="0a680-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="0a680-325">Il nuovo file di layout utilizza gli [Helper Tag](xref:mvc/views/tag-helpers/intro) e presenta altri miglioramenti.</span><span class="sxs-lookup"><span data-stu-id="0a680-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="0a680-326">Configurare la creazione di bundle e minification</span><span class="sxs-lookup"><span data-stu-id="0a680-326">Configure bundling and minification</span></span>

<span data-ttu-id="0a680-327">Per informazioni su come configurare la creazione di bundle e minification, vedere [aggregazione e minification](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="0a680-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="0a680-328">Risolvere gli errori HTTP 500</span><span class="sxs-lookup"><span data-stu-id="0a680-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="0a680-329">Ci sono molti problemi che possono causare un messaggio di errore HTTP 500 che non contengono informazioni sull'origine del problema.</span><span class="sxs-lookup"><span data-stu-id="0a680-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="0a680-330">Se, ad esempio, il file *views/_ViewImports. cshtml* contiene uno spazio dei nomi che non esiste nel progetto, viene generato un errore HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="0a680-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="0a680-331">Per impostazione predefinita, nelle app ASP.NET Core, l' `UseDeveloperExceptionPage` estensione viene aggiunta a `IApplicationBuilder` ed eseguita quando la configurazione è in *fase di sviluppo*.</span><span class="sxs-lookup"><span data-stu-id="0a680-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="0a680-332">Vedere un esempio nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0a680-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="0a680-333">ASP.NET Core converte le eccezioni non gestite in risposte di errore HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="0a680-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="0a680-334">In genere, i dettagli dell'errore non sono inclusi in queste risposte per impedire la divulgazione di informazioni potenzialmente riservate sul server.</span><span class="sxs-lookup"><span data-stu-id="0a680-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="0a680-335">Per ulteriori informazioni, vedere [pagina delle eccezioni per sviluppatori](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="0a680-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a680-336">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0a680-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="0a680-337">Questo articolo illustra come avviare la migrazione di un progetto MVC ASP.NET a [ASP.NET Core mvc](xref:mvc/overview) 2,1.</span><span class="sxs-lookup"><span data-stu-id="0a680-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="0a680-338">Nel processo, vengono evidenziati molti degli elementi che sono stati modificati rispetto a ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="0a680-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="0a680-339">La migrazione da ASP.NET MVC è un processo in più passaggi.</span><span class="sxs-lookup"><span data-stu-id="0a680-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="0a680-340">Questo articolo riguarda:</span><span class="sxs-lookup"><span data-stu-id="0a680-340">This article covers:</span></span>

* <span data-ttu-id="0a680-341">Configurazione iniziale</span><span class="sxs-lookup"><span data-stu-id="0a680-341">Initial setup</span></span>
* <span data-ttu-id="0a680-342">Controller e visualizzazioni di base</span><span class="sxs-lookup"><span data-stu-id="0a680-342">Basic controllers and views</span></span>
* <span data-ttu-id="0a680-343">Contenuto statico</span><span class="sxs-lookup"><span data-stu-id="0a680-343">Static content</span></span>
* <span data-ttu-id="0a680-344">Dipendenze lato client.</span><span class="sxs-lookup"><span data-stu-id="0a680-344">Client-side dependencies.</span></span>

<span data-ttu-id="0a680-345">Per la migrazione della configurazione e del Identity codice, vedere eseguire la migrazione della [configurazione per ASP.NET Core](xref:migration/configuration) ed [eseguire la migrazione dell'autenticazione e Identity di ASP.NET Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="0a680-345">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="0a680-346">I numeri di versione negli esempi potrebbero non essere aggiornati, aggiornare i progetti di conseguenza.</span><span class="sxs-lookup"><span data-stu-id="0a680-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="0a680-347">Creare il progetto MVC Starter ASP.NET</span><span class="sxs-lookup"><span data-stu-id="0a680-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="0a680-348">Per illustrare l'aggiornamento, si inizierà con la creazione di un'app MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="0a680-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="0a680-349">Crearlo con il nome *app Web 1* in modo che lo spazio dei nomi corrisponda al progetto ASP.NET Core creato nel passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="0a680-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](mvc/_static/new-project.png)

![Finestra di dialogo nuova applicazione Web: modello di progetto MVC selezionato nel pannello modelli ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="0a680-352">*Facoltativo:* Modificare il nome della soluzione da *app Web 1* a *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="0a680-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="0a680-353">In Visual Studio viene visualizzato il nuovo nome della soluzione (*Mvc5*), che rende più semplice la creazione di questo progetto dal progetto successivo.</span><span class="sxs-lookup"><span data-stu-id="0a680-353">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="0a680-354">Creare il progetto di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a680-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="0a680-355">Creare una nuova app Web ASP.NET Core *vuota* con lo stesso nome del progetto precedente (*app Web 1*) in modo che gli spazi dei nomi nei due progetti corrispondano.</span><span class="sxs-lookup"><span data-stu-id="0a680-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="0a680-356">La presenza dello stesso spazio dei nomi rende più semplice la copia di codice tra i due progetti.</span><span class="sxs-lookup"><span data-stu-id="0a680-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="0a680-357">Creare il progetto in una directory diversa da quella del progetto precedente per usare lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="0a680-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![Finestra di dialogo Nuovo progetto](mvc/_static/new_core.png)

![Finestra di dialogo nuova applicazione Web ASP.NET: modello di progetto vuoto selezionato nel pannello modelli ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="0a680-360">*Facoltativo:* Creare una nuova app ASP.NET Core usando il modello di progetto *applicazione Web* .</span><span class="sxs-lookup"><span data-stu-id="0a680-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="0a680-361">Denominare il progetto *app Web 1*e selezionare un'opzione di autenticazione per **singoli account utente**.</span><span class="sxs-lookup"><span data-stu-id="0a680-361">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="0a680-362">Rinominare l'app in *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="0a680-362">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="0a680-363">La creazione di questo progetto consente di risparmiare tempo nella conversione.</span><span class="sxs-lookup"><span data-stu-id="0a680-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="0a680-364">Il risultato finale può essere visualizzato nel codice generato dal modello, il codice può essere copiato nel progetto di conversione o confrontato con il progetto generato dal modello.</span><span class="sxs-lookup"><span data-stu-id="0a680-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="0a680-365">Configurare il sito per l'uso di MVC</span><span class="sxs-lookup"><span data-stu-id="0a680-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="0a680-366">Quando la destinazione è .NET Core, per impostazione predefinita viene fatto riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="0a680-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="0a680-367">Questo pacchetto contiene i pacchetti usati comunemente dalle app MVC.</span><span class="sxs-lookup"><span data-stu-id="0a680-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="0a680-368">Se la destinazione è .NET Framework, i riferimenti ai pacchetti devono essere elencati singolarmente nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="0a680-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="0a680-369">`Microsoft.AspNetCore.Mvc` è il Framework di MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="0a680-370">`Microsoft.AspNetCore.StaticFiles` è il gestore di file statici.</span><span class="sxs-lookup"><span data-stu-id="0a680-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="0a680-371">ASP.NET Core le app scelgono esplicitamente il middleware, ad esempio per la conservazione dei file statici.</span><span class="sxs-lookup"><span data-stu-id="0a680-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="0a680-372">Per altre informazioni, vedere [File statici](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="0a680-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="0a680-373">Aprire il file *Startup.cs* e modificare il codice in modo che corrisponda a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="0a680-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="0a680-374">Il <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> metodo di estensione aggiunge il gestore di file statici.</span><span class="sxs-lookup"><span data-stu-id="0a680-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="0a680-375">Il `UseMvc` metodo di estensione aggiunge il routing.</span><span class="sxs-lookup"><span data-stu-id="0a680-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="0a680-376">Per ulteriori informazioni, vedere Startup e [routing](xref:fundamentals/routing) [dell'applicazione](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="0a680-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="0a680-377">Aggiungere un controller e una visualizzazione</span><span class="sxs-lookup"><span data-stu-id="0a680-377">Add a controller and view</span></span>

<span data-ttu-id="0a680-378">In questa sezione vengono aggiunti un controller e una visualizzazione minimi per fungere da segnaposto per il controller MVC ASP.NET e le visualizzazioni migrate nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="0a680-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="0a680-379">Aggiungere una directory *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="0a680-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="0a680-380">Aggiungere una **classe controller** denominata *HomeController.cs* alla directory *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="0a680-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Finestra di dialogo Aggiungi nuovo elemento](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="0a680-382">Aggiungere una directory *views* .</span><span class="sxs-lookup"><span data-stu-id="0a680-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="0a680-383">Aggiungere una directory *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="0a680-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="0a680-384">Aggiungere una \*\* Razor vista\*\* denominata *index. cshtml* alla directory *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="0a680-384">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Finestra di dialogo Aggiungi nuovo elemento](mvc/_static/view.png)

<span data-ttu-id="0a680-386">La struttura del progetto è illustrata di seguito:</span><span class="sxs-lookup"><span data-stu-id="0a680-386">The project structure is shown below:</span></span>

![Esplora soluzioni la visualizzazione di file e directory di app Web 1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="0a680-388">Sostituire il contenuto del file *Views/Home/Index.cshtml* con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="0a680-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="0a680-389">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="0a680-389">Run the app.</span></span>

![App Web aperta in Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="0a680-391">Per altre informazioni, vedere [controller](xref:mvc/controllers/actions) e [visualizzazioni](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="0a680-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="0a680-392">Le funzionalità seguenti richiedono la migrazione dal progetto MVC ASP.NET di esempio al progetto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0a680-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="0a680-393">contenuto lato client (CSS, tipi di carattere e script)</span><span class="sxs-lookup"><span data-stu-id="0a680-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="0a680-394">controllers</span><span class="sxs-lookup"><span data-stu-id="0a680-394">controllers</span></span>

* <span data-ttu-id="0a680-395">Viste</span><span class="sxs-lookup"><span data-stu-id="0a680-395">views</span></span>

* <span data-ttu-id="0a680-396">modelli</span><span class="sxs-lookup"><span data-stu-id="0a680-396">models</span></span>

* <span data-ttu-id="0a680-397">Bundle</span><span class="sxs-lookup"><span data-stu-id="0a680-397">bundling</span></span>

* <span data-ttu-id="0a680-398">filters</span><span class="sxs-lookup"><span data-stu-id="0a680-398">filters</span></span>

* <span data-ttu-id="0a680-399">Accesso/uscita Identity (operazione eseguita nell'esercitazione successiva).</span><span class="sxs-lookup"><span data-stu-id="0a680-399">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="0a680-400">Controller e visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="0a680-400">Controllers and views</span></span>

* <span data-ttu-id="0a680-401">Copiare tutti i metodi da ASP.NET MVC `HomeController` al nuovo `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="0a680-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="0a680-402">In ASP.NET MVC, il tipo restituito del metodo di azione del controller del modello predefinito è [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC i metodi di azione restituiscono `IActionResult` invece.</span><span class="sxs-lookup"><span data-stu-id="0a680-402">In ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="0a680-403">`ActionResult` implementa `IActionResult` , quindi non è necessario modificare il tipo restituito dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="0a680-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="0a680-404">Copiare i file *di visualizzazione About. cshtml*, *Contact. cshtml*e *index. cshtml* Razor dal progetto MVC ASP.NET al progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-404">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="0a680-405">Testare ogni metodo</span><span class="sxs-lookup"><span data-stu-id="0a680-405">Test each method</span></span>

<span data-ttu-id="0a680-406">Il file di layout e gli stili non sono ancora stati migrati, quindi le visualizzazioni sottoposte a rendering contengono solo il contenuto dei file di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="0a680-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="0a680-407">Il file di layout i collegamenti generati per le `About` `Contact` visualizzazioni e non saranno ancora disponibili.</span><span class="sxs-lookup"><span data-stu-id="0a680-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="0a680-408">Richiamare le visualizzazioni sottoposte a rendering dal browser nell'app ASP.NET Core in esecuzione sostituendo il numero di porta corrente con il numero di porta usato nel progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="0a680-409">Ad esempio: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="0a680-409">For example: `https://localhost:44375/home/about`.</span></span>

![Pagina contatto](mvc/_static/contact-page.png)

<span data-ttu-id="0a680-411">Si noti la mancanza di stili e voci di menu.</span><span class="sxs-lookup"><span data-stu-id="0a680-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="0a680-412">Lo stile verrà risolto nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="0a680-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="0a680-413">Contenuto statico</span><span class="sxs-lookup"><span data-stu-id="0a680-413">Static content</span></span>

<span data-ttu-id="0a680-414">In ASP.NET MVC 5 e versioni precedenti, il contenuto statico era ospitato dalla radice del progetto Web ed era combinato con i file lato server.</span><span class="sxs-lookup"><span data-stu-id="0a680-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="0a680-415">In ASP.NET Core, il contenuto statico è ospitato nella directory *wwwroot*</span><span class="sxs-lookup"><span data-stu-id="0a680-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="0a680-416">Copiare il contenuto statico dall'app MVC ASP.NET alla directory *wwwroot* nel progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="0a680-417">In questo esempio di conversione:</span><span class="sxs-lookup"><span data-stu-id="0a680-417">In this sample conversion:</span></span>

* <span data-ttu-id="0a680-418">Copiare il file *favicon. ico* dal progetto MVC ASP.NET alla directory *wwwroot* nel progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="0a680-419">Il progetto MVC ASP.NET usa [bootstrap](https://getbootstrap.com/) per lo stile e archivia i file bootstrap nelle directory *Content* e *Scripts* .</span><span class="sxs-lookup"><span data-stu-id="0a680-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="0a680-420">Il modello, che ha generato il progetto MVC ASP.NET, fa riferimento a bootstrap nel file di layout (*Views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="0a680-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="0a680-421">È possibile copiare i file *bootstrap.js* e *bootstrap. CSS* dal progetto MVC ASP.NET alla directory *wwwroot* del nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="0a680-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="0a680-422">Al contrario, questo documento aggiunge il supporto per bootstrap (e altre librerie lato client) usando CDNs nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="0a680-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="0a680-423">Eseguire la migrazione del file di layout</span><span class="sxs-lookup"><span data-stu-id="0a680-423">Migrate the layout file</span></span>

* <span data-ttu-id="0a680-424">Copiare il file *_ViewStart. cshtml* dalla directory *views* del progetto MVC ASP.NET alla directory *views* del progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="0a680-425">Il file *_ViewStart. cshtml* non è stato modificato in ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0a680-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="0a680-426">Creare una *vista/directory condivisa* .</span><span class="sxs-lookup"><span data-stu-id="0a680-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="0a680-427">*Facoltativo:* Copiare *_ViewImports. cshtml* dalla directory *views* del progetto MVC *FullAspNetCore* alla directory *views* del progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="0a680-428">Rimuovere qualsiasi dichiarazione dello spazio dei nomi nel file *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="0a680-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="0a680-429">Il file *_ViewImports. cshtml* fornisce gli spazi dei nomi per tutti i file di visualizzazione e porta gli [Helper Tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="0a680-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="0a680-430">Gli helper tag vengono usati nel nuovo file di layout.</span><span class="sxs-lookup"><span data-stu-id="0a680-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="0a680-431">Il file *_ViewImports. cshtml* è nuovo per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="0a680-432">Copiare il file *_Layout. cshtml* dalla directory *Views/Shared* del progetto MVC ASP.NET nella directory *views/* Shared del progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a680-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="0a680-433">Aprire *_Layout file cshtml* e apportare le modifiche seguenti (il codice completato è riportato di seguito):</span><span class="sxs-lookup"><span data-stu-id="0a680-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="0a680-434">Sostituire `@Styles.Render("~/Content/css")` con un `<link>` elemento per caricare *bootstrap. CSS* (vedere di seguito).</span><span class="sxs-lookup"><span data-stu-id="0a680-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="0a680-435">Rimuovere `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="0a680-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="0a680-436">Imposta come commento la riga, che `@Html.Partial("_LoginPartial")` racchiude la riga con `@*...*@` .</span><span class="sxs-lookup"><span data-stu-id="0a680-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="0a680-437">Per ulteriori informazioni, vedere la pagina relativa [alla migrazione dell'autenticazione e Identity alla ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="0a680-437">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="0a680-438">Sostituire `@Scripts.Render("~/bundles/jquery")` con un `<script>` elemento (vedere di seguito).</span><span class="sxs-lookup"><span data-stu-id="0a680-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="0a680-439">Sostituire `@Scripts.Render("~/bundles/bootstrap")` con un `<script>` elemento (vedere di seguito).</span><span class="sxs-lookup"><span data-stu-id="0a680-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="0a680-440">Markup sostitutivo per l'inclusione CSS bootstrap:</span><span class="sxs-lookup"><span data-stu-id="0a680-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="0a680-441">Markup sostitutivo per jQuery e bootstrap JavaScript inclusion:</span><span class="sxs-lookup"><span data-stu-id="0a680-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="0a680-442">Il file *_Layout. cshtml* aggiornato è illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="0a680-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="0a680-443">Visualizzare il sito nel browser.</span><span class="sxs-lookup"><span data-stu-id="0a680-443">View the site in the browser.</span></span> <span data-ttu-id="0a680-444">A questo punto, dovrebbe essere caricato correttamente con gli stili previsti.</span><span class="sxs-lookup"><span data-stu-id="0a680-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="0a680-445">*Facoltativo:* Provare a usare il nuovo file di layout.</span><span class="sxs-lookup"><span data-stu-id="0a680-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="0a680-446">Copiare il file di layout dal progetto *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="0a680-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="0a680-447">Il nuovo file di layout utilizza gli [Helper Tag](xref:mvc/views/tag-helpers/intro) e presenta altri miglioramenti.</span><span class="sxs-lookup"><span data-stu-id="0a680-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="0a680-448">Configurare la creazione di bundle e minification</span><span class="sxs-lookup"><span data-stu-id="0a680-448">Configure bundling and minification</span></span>

<span data-ttu-id="0a680-449">Per informazioni su come configurare la creazione di bundle e minification, vedere [aggregazione e minification](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="0a680-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="0a680-450">Risolvere gli errori HTTP 500</span><span class="sxs-lookup"><span data-stu-id="0a680-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="0a680-451">Ci sono molti problemi che possono causare un messaggio di errore HTTP 500 che non contengono informazioni sull'origine del problema.</span><span class="sxs-lookup"><span data-stu-id="0a680-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="0a680-452">Se, ad esempio, il file *views/_ViewImports. cshtml* contiene uno spazio dei nomi che non esiste nel progetto, viene generato un errore HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="0a680-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="0a680-453">Per impostazione predefinita, nelle app ASP.NET Core, l' `UseDeveloperExceptionPage` estensione viene aggiunta a `IApplicationBuilder` ed eseguita quando la configurazione è in *fase di sviluppo*.</span><span class="sxs-lookup"><span data-stu-id="0a680-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="0a680-454">Vedere un esempio nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0a680-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="0a680-455">ASP.NET Core converte le eccezioni non gestite in risposte di errore HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="0a680-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="0a680-456">In genere, i dettagli dell'errore non sono inclusi in queste risposte per impedire la divulgazione di informazioni potenzialmente riservate sul server.</span><span class="sxs-lookup"><span data-stu-id="0a680-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="0a680-457">Per ulteriori informazioni, vedere [pagina delle eccezioni per sviluppatori](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="0a680-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a680-458">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0a680-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
