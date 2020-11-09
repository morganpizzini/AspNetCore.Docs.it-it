---
title: Pubblicare un'app ASP.NET Core in Azure con Visual Studio Code
author: rick-anderson
description: Informazioni su come pubblicare un'app ASP.NET Core in Servizio app di Azure con Visual Studio Code
ms.author: riserrad
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: a5a863682655517e27f6540af76342f95d4ecae0
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061262"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="343aa-103">Pubblicare un'app ASP.NET Core in Azure con Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343aa-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="343aa-104">Di [Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="343aa-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="343aa-105">Per risolvere un problema di distribuzione del Servizio app di Azure, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="343aa-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="343aa-106">Introduzione</span><span class="sxs-lookup"><span data-stu-id="343aa-106">Intro</span></span>

<span data-ttu-id="343aa-107">Con questa esercitazione si apprenderà come creare un'applicazione ASP.Net Core MVC e distribuirla all'interno di Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="343aa-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="343aa-108">Configurare</span><span class="sxs-lookup"><span data-stu-id="343aa-108">Set up</span></span>

- <span data-ttu-id="343aa-109">Aprire un [account Azure gratuito](https://azure.microsoft.com/free/dotnet/) se non è già disponibile un account.</span><span class="sxs-lookup"><span data-stu-id="343aa-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="343aa-110">Installa [.NET Core SDK](https://dotnet.microsoft.com/download)</span><span class="sxs-lookup"><span data-stu-id="343aa-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="343aa-111">Installa [Visual Studio Code](https://code.visualstudio.com/Download)</span><span class="sxs-lookup"><span data-stu-id="343aa-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="343aa-112">Installare l'[estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) in Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343aa-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="343aa-113">Installare l' [estensione del servizio app Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) per Visual Studio Code e configurarla prima di procedere</span><span class="sxs-lookup"><span data-stu-id="343aa-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="343aa-114">Creare un progetto ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="343aa-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="343aa-115">Usando un terminale passare alla cartella in cui si vuole creare il progetto e usare il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="343aa-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="343aa-116">Si otterrà una struttura di cartelle simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="343aa-116">You'll have a folder structure similar to the following:</span></span>

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="343aa-117">Aprire il progetto con Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343aa-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="343aa-118">Dopo aver creato il progetto, è possibile aprirlo con Visual Studio Code usando una delle opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="343aa-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="343aa-119">Tramite la riga di comando</span><span class="sxs-lookup"><span data-stu-id="343aa-119">Through the command line</span></span>

<span data-ttu-id="343aa-120">Usare il comando seguente all'interno della cartella in cui è stato creato il progetto:</span><span class="sxs-lookup"><span data-stu-id="343aa-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="343aa-121">Se il comando seguente non funziona, controllare se l'installazione è configurata correttamente facendo riferimento alle informazioni in [questo collegamento](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span><span class="sxs-lookup"><span data-stu-id="343aa-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="343aa-122">Tramite l'interfaccia di Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343aa-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="343aa-123">Aprire Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343aa-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="343aa-124">Nel menu selezionare `File > Open Folder`</span><span class="sxs-lookup"><span data-stu-id="343aa-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="343aa-125">Selezionare la radice della cartella in cui è stato creato il progetto MVC</span><span class="sxs-lookup"><span data-stu-id="343aa-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="343aa-126">All'apertura della cartella del progetto verrà visualizzato un messaggio che informa che gli asset necessari per compilare ed eseguire il debug risultano mancanti.</span><span class="sxs-lookup"><span data-stu-id="343aa-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="343aa-127">Accettare il supporto per aggiungerli.</span><span class="sxs-lookup"><span data-stu-id="343aa-127">Accept the help to add them.</span></span>

![Interfaccia di Visual Studio Code con il progetto caricato](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="343aa-129">Verrà creata una cartella `.vscode` nella struttura del progetto.</span><span class="sxs-lookup"><span data-stu-id="343aa-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="343aa-130">Tale cartella conterrà i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="343aa-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="343aa-131">Questi sono file di utilità che consentono di compilare l'app Web .NET Core e di eseguirne il debug.</span><span class="sxs-lookup"><span data-stu-id="343aa-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="343aa-132">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="343aa-132">Run the app</span></span>

<span data-ttu-id="343aa-133">Prima della distribuzione dell'app in Azure, assicurarsi che venga eseguita correttamente nel computer locale.</span><span class="sxs-lookup"><span data-stu-id="343aa-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="343aa-134">Premere F5 per eseguire il progetto</span><span class="sxs-lookup"><span data-stu-id="343aa-134">Press F5 to run the project</span></span>

<span data-ttu-id="343aa-135">L'app Web verrà eseguita in una nuova scheda del browser predefinito.</span><span class="sxs-lookup"><span data-stu-id="343aa-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="343aa-136">Potrebbe essere visualizzato un avviso per la privacy subito dopo aver avviato l'app.</span><span class="sxs-lookup"><span data-stu-id="343aa-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="343aa-137">L'avviso viene visualizzato perché l'app verrà avviata tramite HTTP e HTTPS e passerà all'endpoint HTTPS per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="343aa-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Avviso per la privacy durante il debug dell'app in locale](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="343aa-139">Per mantenere la sessione di debug, fare clic su `Advanced` e quindi su `Continue to localhost (unsafe)`.</span><span class="sxs-lookup"><span data-stu-id="343aa-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="343aa-140">Generare il pacchetto di distribuzione in locale</span><span class="sxs-lookup"><span data-stu-id="343aa-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="343aa-141">Aprire il terminale di Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="343aa-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="343aa-142">Usare il comando seguente per generare un pacchetto `Release` in una sottocartella denominata `publish`:</span><span class="sxs-lookup"><span data-stu-id="343aa-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="343aa-143">Verrà creata una nuova cartella `publish` nella struttura del progetto</span><span class="sxs-lookup"><span data-stu-id="343aa-143">A new `publish` folder will be created under the project structure</span></span>

![Struttura della cartella di pubblicazione](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="343aa-145">Eseguire la pubblicazione nel servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="343aa-145">Publish to Azure App Service</span></span>

<span data-ttu-id="343aa-146">Sfruttando l'estensione del servizio app di Azure per Visual Studio Code, seguire questa procedura per pubblicare il sito Web direttamente nel servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="343aa-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="343aa-147">Se si crea una nuova app Web</span><span class="sxs-lookup"><span data-stu-id="343aa-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="343aa-148">Fare clic con il pulsante destro del mouse sulla cartella `publish` e scegliere `Deploy to Web App...`.</span><span class="sxs-lookup"><span data-stu-id="343aa-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="343aa-149">Selezionare la sottoscrizione in cui si vuole creare l'app Web</span><span class="sxs-lookup"><span data-stu-id="343aa-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="343aa-150">Selezionare `Create New Web App`</span><span class="sxs-lookup"><span data-stu-id="343aa-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="343aa-151">Immettere un nome per l'app Web</span><span class="sxs-lookup"><span data-stu-id="343aa-151">Enter a name for the Web App</span></span>

<span data-ttu-id="343aa-152">L'estensione creerà la nuova app Web e avvierà automaticamente la distribuzione del pacchetto in tale app.</span><span class="sxs-lookup"><span data-stu-id="343aa-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="343aa-153">Al termine della distribuzione, fare clic su `Browse Website` per convalidare la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="343aa-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Messaggio di distribuzione riuscita](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="343aa-155">Quando si fa clic su `Browse Website`, si passerà a tale sito tramite il browser predefinito:</span><span class="sxs-lookup"><span data-stu-id="343aa-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Nuova app Web distribuita correttamente](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="343aa-157">Per la distribuzione in un'app Web esistente</span><span class="sxs-lookup"><span data-stu-id="343aa-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="343aa-158">Fare clic con il pulsante destro del mouse sulla cartella `publish` e scegliere `Deploy to Web App...`.</span><span class="sxs-lookup"><span data-stu-id="343aa-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="343aa-159">Selezionare la sottoscrizione in cui risiede l'app Web esistente</span><span class="sxs-lookup"><span data-stu-id="343aa-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="343aa-160">Selezionare l'app Web nell'elenco</span><span class="sxs-lookup"><span data-stu-id="343aa-160">Select the Web App from the list</span></span>
- <span data-ttu-id="343aa-161">Visual Studio Code chiederà se si vuole sovrascrivere il contenuto esistente.</span><span class="sxs-lookup"><span data-stu-id="343aa-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="343aa-162">Fare clic su `Deploy` per confermare</span><span class="sxs-lookup"><span data-stu-id="343aa-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="343aa-163">L'estensione distribuirà il contenuto aggiornato nell'app Web.</span><span class="sxs-lookup"><span data-stu-id="343aa-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="343aa-164">Al termine, fare clic su `Browse Website` per convalidare la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="343aa-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![App Web esistente distribuita correttamente](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="343aa-166">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="343aa-166">Next steps</span></span>

- [<span data-ttu-id="343aa-167">Creare la prima pipeline di Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="343aa-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="343aa-168">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="343aa-168">Additional resources</span></span>

- [<span data-ttu-id="343aa-169">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="343aa-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="343aa-170">Gruppi di risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="343aa-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
