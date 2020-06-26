---
title: Introduzione ad ASP.NET Core
author: rick-anderson
description: Un'esercitazione rapida per creare ed eseguire una semplice app Hello World usando ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: getting-started
ms.openlocfilehash: b88460cdff5d8c30c6a28afdb4f67e8e0b6b819c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403365"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="c4207-103">Esercitazione: Introduzione ad ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c4207-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="c4207-104">Questa esercitazione illustra come creare ed eseguire un'app Web ASP.NET Core usando il interfaccia della riga di comando di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4207-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="c4207-105">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="c4207-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c4207-106">Creare un progetto di app Web.</span><span class="sxs-lookup"><span data-stu-id="c4207-106">Create a web app project.</span></span>
> * <span data-ttu-id="c4207-107">Considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="c4207-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="c4207-108">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="c4207-108">Run the app.</span></span>
> * <span data-ttu-id="c4207-109">Modificare una Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="c4207-109">Edit a Razor page.</span></span>

<span data-ttu-id="c4207-110">Al termine, si avrà un'app Web funzionante che viene eseguita nel computer locale.</span><span class="sxs-lookup"><span data-stu-id="c4207-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Home page dell'app Web](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="c4207-112">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="c4207-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="c4207-113">Creare un progetto di app Web</span><span class="sxs-lookup"><span data-stu-id="c4207-113">Create a web app project</span></span>

<span data-ttu-id="c4207-114">Aprire una shell dei comandi e immettere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c4207-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="c4207-115">Il comando precedente:</span><span class="sxs-lookup"><span data-stu-id="c4207-115">The preceding command:</span></span>

* <span data-ttu-id="c4207-116">Crea una nuova app Web.</span><span class="sxs-lookup"><span data-stu-id="c4207-116">Creates a new web app.</span></span>  
* <span data-ttu-id="c4207-117">Il `-o aspnetcoreapp` parametro crea una directory denominata *aspnetcoreapp* con i file di origine per l'app.</span><span class="sxs-lookup"><span data-stu-id="c4207-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="c4207-118">Considerare attendibile il certificato di sviluppo</span><span class="sxs-lookup"><span data-stu-id="c4207-118">Trust the development certificate</span></span>

<span data-ttu-id="c4207-119">Considerare attendibile il certificato di sviluppo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="c4207-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="c4207-120">Windows</span><span class="sxs-lookup"><span data-stu-id="c4207-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="c4207-121">Il comando precedente consente di visualizzare la finestra di dialogo seguente:</span><span class="sxs-lookup"><span data-stu-id="c4207-121">The preceding command displays the following dialog:</span></span>

![Finestra di dialogo Avviso di sicurezza](~/getting-started/_static/cert.png)

<span data-ttu-id="c4207-123">Selezionare **Sì** se si accetta di considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="c4207-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="c4207-124">macOS</span><span class="sxs-lookup"><span data-stu-id="c4207-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="c4207-125">Il comando precedente consente di visualizzare il messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="c4207-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="c4207-126">*È stato richiesto l'attendibilità del certificato di sviluppo HTTPS. Se il certificato non è già attendibile, verrà eseguito il comando seguente:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="c4207-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="c4207-127">Questo comando potrebbe richiedere la password per installare il certificato nel keychain di sistema.</span><span class="sxs-lookup"><span data-stu-id="c4207-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="c4207-128">Inserire la password se si accetta di considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="c4207-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="c4207-129">Linux</span><span class="sxs-lookup"><span data-stu-id="c4207-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="c4207-130">Vedere la documentazione della distribuzione di Linux su come rendere attendibile il certificato di sviluppo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c4207-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="c4207-131">Per altre informazioni, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="c4207-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="c4207-132">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="c4207-132">Run the app</span></span>

<span data-ttu-id="c4207-133">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="c4207-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="c4207-134">Dopo che la shell dei comandi indica che l'app è stata avviata, passare a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c4207-134">After the command shell indicates that the app has started, browse to `https://localhost:5001`.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="c4207-135">Modificare una Razor pagina</span><span class="sxs-lookup"><span data-stu-id="c4207-135">Edit a Razor page</span></span>

<span data-ttu-id="c4207-136">Aprire *pages/index. cshtml* e modificare e salvare la pagina con il markup evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="c4207-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="c4207-137">Individuare `https://localhost:5001` , aggiornare la pagina e verificare che le modifiche vengano visualizzate.</span><span class="sxs-lookup"><span data-stu-id="c4207-137">Browse to `https://localhost:5001`, refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c4207-138">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="c4207-138">Next steps</span></span>

<span data-ttu-id="c4207-139">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="c4207-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c4207-140">Creare un progetto di app Web.</span><span class="sxs-lookup"><span data-stu-id="c4207-140">Create a web app project.</span></span>
> * <span data-ttu-id="c4207-141">Considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="c4207-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="c4207-142">Eseguire il progetto.</span><span class="sxs-lookup"><span data-stu-id="c4207-142">Run the project.</span></span>
> * <span data-ttu-id="c4207-143">Apportare una modifica.</span><span class="sxs-lookup"><span data-stu-id="c4207-143">Make a change.</span></span>

<span data-ttu-id="c4207-144">Per altre informazioni su ASP.NET Core, vedere il percorso di apprendimento consigliato nell'introduzione:</span><span class="sxs-lookup"><span data-stu-id="c4207-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
