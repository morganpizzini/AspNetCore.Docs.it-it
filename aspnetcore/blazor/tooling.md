---
title: Strumenti per ASP.NET Core Blazor
author: guardrex
description: Informazioni sugli strumenti disponibili per la compilazione di Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
no-loc:
- appsettings.json
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 5901a1cb693dfe8e34e62ce2a28456bcf584221c
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252266"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="49b84-103">Strumenti per ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="49b84-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="49b84-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="49b84-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="49b84-105">Installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro **sviluppo di ASP.NET e Web** .</span><span class="sxs-lookup"><span data-stu-id="49b84-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="49b84-106">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="49b84-106">Create a new project.</span></span>

1. <span data-ttu-id="49b84-107">Selezionare **Blazor app**.</span><span class="sxs-lookup"><span data-stu-id="49b84-107">Select **Blazor App**.</span></span> <span data-ttu-id="49b84-108">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="49b84-108">Select **Next**.</span></span>

1. <span data-ttu-id="49b84-109">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="49b84-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="49b84-110">Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto.</span><span class="sxs-lookup"><span data-stu-id="49b84-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="49b84-111">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="49b84-111">Select **Create**.</span></span>

1. <span data-ttu-id="49b84-112">Per un' Blazor WebAssembly esperienza, scegliere il modello **Blazor WebAssembly app** .</span><span class="sxs-lookup"><span data-stu-id="49b84-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="49b84-113">Per un' Blazor Server esperienza, scegliere il modello **Blazor Server app** .</span><span class="sxs-lookup"><span data-stu-id="49b84-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="49b84-114">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="49b84-114">Select **Create**.</span></span>

   <span data-ttu-id="49b84-115">Per un'esperienza ospitata Blazor WebAssembly , selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="49b84-115">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="49b84-116">Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* (standalone e Hosted) e *Blazor Server* , vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="49b84-116">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="49b84-117">Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="49b84-117">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="49b84-118">Per ulteriori informazioni su come considerare attendibile il certificato di sviluppo HTTPS ASP.NET Core, vedere <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="49b84-118">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="49b84-119">Installare la versione più recente del [.NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="49b84-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="49b84-120">Se in precedenza è stato installato l'SDK, è possibile determinare la versione installata eseguendo il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="49b84-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="49b84-121">Installare la versione più recente di [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="49b84-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="49b84-122">Installare la versione più recente [di C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="49b84-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="49b84-123">Per un' Blazor WebAssembly esperienza, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="49b84-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="49b84-124">Per un'esperienza ospitata Blazor WebAssembly , aggiungere l'opzione Hosted ( `-ho` o `--hosted` ) al comando:</span><span class="sxs-lookup"><span data-stu-id="49b84-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="49b84-125">Per un' Blazor Server esperienza, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="49b84-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="49b84-126">Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* (standalone e Hosted) e *Blazor Server* , vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="49b84-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="49b84-127">Aprire la cartella `WebApplication1` in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="49b84-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="49b84-128">Le richieste dell'IDE aggiungono risorse per compilare ed eseguire il debug del progetto.</span><span class="sxs-lookup"><span data-stu-id="49b84-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="49b84-129">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="49b84-129">Select **Yes**.</span></span>

1. <span data-ttu-id="49b84-130">Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="49b84-130">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="49b84-131">Attendibilità di un certificato di sviluppo</span><span class="sxs-lookup"><span data-stu-id="49b84-131">Trust a development certificate</span></span>

<span data-ttu-id="49b84-132">Non esiste un modo centralizzato per considerare attendibile un certificato in Linux.</span><span class="sxs-lookup"><span data-stu-id="49b84-132">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="49b84-133">Viene in genere adottato uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="49b84-133">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="49b84-134">Escludere l'URL dell'app nell'elenco di esclusione del browser.</span><span class="sxs-lookup"><span data-stu-id="49b84-134">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="49b84-135">Considerare attendibili tutti i certificati autofirmati per `localhost` .</span><span class="sxs-lookup"><span data-stu-id="49b84-135">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="49b84-136">Aggiungere il certificato all'elenco dei certificati attendibili nel browser.</span><span class="sxs-lookup"><span data-stu-id="49b84-136">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="49b84-137">Per ulteriori informazioni, vedere le indicazioni fornite dal produttore del browser e dalla distribuzione di Linux.</span><span class="sxs-lookup"><span data-stu-id="49b84-137">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="49b84-138">Installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="49b84-138">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="49b84-139">Selezionare **file**  >  **nuova soluzione** o creare un **nuovo** progetto dalla **finestra Start**.</span><span class="sxs-lookup"><span data-stu-id="49b84-139">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="49b84-140">Nella barra laterale selezionare app **Web e console**  >  .</span><span class="sxs-lookup"><span data-stu-id="49b84-140">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="49b84-141">Per un' Blazor WebAssembly esperienza, scegliere il modello **Blazor WebAssembly app** .</span><span class="sxs-lookup"><span data-stu-id="49b84-141">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="49b84-142">Per un' Blazor Server esperienza, scegliere il modello **Blazor Server app** .</span><span class="sxs-lookup"><span data-stu-id="49b84-142">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="49b84-143">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="49b84-143">Select **Next**.</span></span>

   <span data-ttu-id="49b84-144">Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* (standalone e Hosted) e *Blazor Server* , vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="49b84-144">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="49b84-145">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="49b84-145">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="49b84-146">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="49b84-146">Select **Next**.</span></span>

1. <span data-ttu-id="49b84-147">Per un'esperienza ospitata Blazor WebAssembly , selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="49b84-147">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="49b84-148">Nel campo **nome progetto** assegnare un nome all'app `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="49b84-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="49b84-149">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="49b84-149">Select **Create**.</span></span>

1. <span data-ttu-id="49b84-150">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per eseguire l'app *senza il debugger*.</span><span class="sxs-lookup"><span data-stu-id="49b84-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="49b84-151">Eseguire l'app con **Esegui**  >  **debug Avvia debug** o il pulsante Esegui (&#9654;) per eseguire l'app *con il debugger*.</span><span class="sxs-lookup"><span data-stu-id="49b84-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="49b84-152">Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare.</span><span class="sxs-lookup"><span data-stu-id="49b84-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="49b84-153">Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.</span><span class="sxs-lookup"><span data-stu-id="49b84-153">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="49b84-154">Per ulteriori informazioni su come considerare attendibile il certificato di sviluppo HTTPS ASP.NET Core, vedere <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="49b84-154">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a><span data-ttu-id="49b84-155">Usare Visual Studio Code per Blazor lo sviluppo multipiattaforma</span><span class="sxs-lookup"><span data-stu-id="49b84-155">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="49b84-156">[Visual Studio Code](https://code.visualstudio.com/) è un ambiente di sviluppo integrato (IDE) open source e multipiattaforma che può essere usato per sviluppare Blazor app.</span><span class="sxs-lookup"><span data-stu-id="49b84-156">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="49b84-157">Usare l'interfaccia della riga di comando di .NET per creare una nuova Blazor app per lo sviluppo con Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="49b84-157">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="49b84-158">Per altre informazioni, vedere la [versione di questo articolo](?pivots=linux)relativa a Linux.</span><span class="sxs-lookup"><span data-stu-id="49b84-158">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="no-locblazor-template-options"></a><span data-ttu-id="49b84-159">Blazor opzioni del modello</span><span class="sxs-lookup"><span data-stu-id="49b84-159">Blazor template options</span></span>

<span data-ttu-id="49b84-160">Il Blazor Framework fornisce modelli per la creazione di nuove app per ognuno dei due Blazor modelli di hosting.</span><span class="sxs-lookup"><span data-stu-id="49b84-160">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="49b84-161">I modelli vengono usati per creare nuovi Blazor progetti e soluzioni indipendentemente dagli strumenti selezionati per Blazor lo sviluppo (Visual Studio, Visual Studio per Mac, Visual Studio Code o l'interfaccia della riga di comando di .NET):</span><span class="sxs-lookup"><span data-stu-id="49b84-161">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="49b84-162">Blazor WebAssembly modello di progetto: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="49b84-162">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="49b84-163">Blazor Server modello di progetto: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="49b84-163">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="49b84-164">Per ulteriori informazioni sui Blazor modelli di hosting di, vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="49b84-164">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="49b84-165">Le opzioni del modello sono disponibili passando l'opzione Help ( `-h` o `--help` ) al [`dotnet new`](/dotnet/core/tools/dotnet-new) comando CLI in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="49b84-165">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```