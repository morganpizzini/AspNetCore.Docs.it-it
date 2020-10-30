---
title: Introduzione ad ASP.NET Core
author: rick-anderson
description: Un'esercitazione rapida per creare ed eseguire una semplice app Hello World usando ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: getting-started
ms.openlocfilehash: fef5ae525a7c01d0ea7733e990233f413aac61a7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057791"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="068ca-103">Esercitazione: Introduzione ad ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="068ca-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="068ca-104">Questa esercitazione illustra come creare ed eseguire un'app Web ASP.NET Core usando il interfaccia della riga di comando di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="068ca-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="068ca-105">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="068ca-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="068ca-106">Creare un progetto di app Web.</span><span class="sxs-lookup"><span data-stu-id="068ca-106">Create a web app project.</span></span>
> * <span data-ttu-id="068ca-107">Considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="068ca-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="068ca-108">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="068ca-108">Run the app.</span></span>
> * <span data-ttu-id="068ca-109">Modificare una :::no-loc(Razor)::: pagina.</span><span class="sxs-lookup"><span data-stu-id="068ca-109">Edit a :::no-loc(Razor)::: page.</span></span>

<span data-ttu-id="068ca-110">Al termine, si avrà un'app Web funzionante che viene eseguita nel computer locale.</span><span class="sxs-lookup"><span data-stu-id="068ca-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Home page dell'app Web](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="068ca-112">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="068ca-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="068ca-113">Creare un progetto di app Web</span><span class="sxs-lookup"><span data-stu-id="068ca-113">Create a web app project</span></span>

<span data-ttu-id="068ca-114">Aprire una shell dei comandi e immettere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="068ca-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="068ca-115">Il comando precedente:</span><span class="sxs-lookup"><span data-stu-id="068ca-115">The preceding command:</span></span>

* <span data-ttu-id="068ca-116">Crea una nuova app Web.</span><span class="sxs-lookup"><span data-stu-id="068ca-116">Creates a new web app.</span></span>  
* <span data-ttu-id="068ca-117">Il `-o aspnetcoreapp` parametro crea una directory denominata *aspnetcoreapp* con i file di origine per l'app.</span><span class="sxs-lookup"><span data-stu-id="068ca-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="068ca-118">Considerare attendibile il certificato di sviluppo</span><span class="sxs-lookup"><span data-stu-id="068ca-118">Trust the development certificate</span></span>

<span data-ttu-id="068ca-119">Considerare attendibile il certificato di sviluppo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="068ca-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="068ca-120">Windows</span><span class="sxs-lookup"><span data-stu-id="068ca-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="068ca-121">Il comando precedente consente di visualizzare la finestra di dialogo seguente:</span><span class="sxs-lookup"><span data-stu-id="068ca-121">The preceding command displays the following dialog:</span></span>

![Finestra di dialogo Avviso di sicurezza](~/getting-started/_static/cert.png)

<span data-ttu-id="068ca-123">Selezionare **Sì** se si accetta di considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="068ca-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="068ca-124">macOS</span><span class="sxs-lookup"><span data-stu-id="068ca-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="068ca-125">Il comando precedente consente di visualizzare il messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="068ca-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="068ca-126">*È stato richiesto l'attendibilità del certificato di sviluppo HTTPS. Se il certificato non è già attendibile, verrà eseguito il comando seguente:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="068ca-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="068ca-127">Questo comando potrebbe richiedere la password per installare il certificato nel keychain di sistema.</span><span class="sxs-lookup"><span data-stu-id="068ca-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="068ca-128">Inserire la password se si accetta di considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="068ca-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="068ca-129">Linux</span><span class="sxs-lookup"><span data-stu-id="068ca-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="068ca-130">Vedere la documentazione della distribuzione di Linux su come rendere attendibile il certificato di sviluppo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="068ca-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="068ca-131">Per altre informazioni, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="068ca-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="068ca-132">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="068ca-132">Run the app</span></span>

<span data-ttu-id="068ca-133">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="068ca-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="068ca-134">Dopo che la shell dei comandi indica che l'app è stata avviata, passare a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="068ca-134">After the command shell indicates that the app has started, browse to `https://localhost:5001`.</span></span>

## <a name="edit-a-no-locrazor-page"></a><span data-ttu-id="068ca-135">Modificare una :::no-loc(Razor)::: pagina</span><span class="sxs-lookup"><span data-stu-id="068ca-135">Edit a :::no-loc(Razor)::: page</span></span>

<span data-ttu-id="068ca-136">Aprire *pages/index. cshtml* e modificare e salvare la pagina con il markup evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="068ca-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="068ca-137">Individuare `https://localhost:5001` , aggiornare la pagina e verificare che le modifiche vengano visualizzate.</span><span class="sxs-lookup"><span data-stu-id="068ca-137">Browse to `https://localhost:5001`, refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="068ca-138">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="068ca-138">Next steps</span></span>

<span data-ttu-id="068ca-139">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="068ca-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="068ca-140">Creare un progetto di app Web.</span><span class="sxs-lookup"><span data-stu-id="068ca-140">Create a web app project.</span></span>
> * <span data-ttu-id="068ca-141">Considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="068ca-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="068ca-142">Eseguire il progetto.</span><span class="sxs-lookup"><span data-stu-id="068ca-142">Run the project.</span></span>
> * <span data-ttu-id="068ca-143">Apportare una modifica.</span><span class="sxs-lookup"><span data-stu-id="068ca-143">Make a change.</span></span>

<span data-ttu-id="068ca-144">Per altre informazioni su ASP.NET Core, vedere il percorso di apprendimento consigliato nell'introduzione:</span><span class="sxs-lookup"><span data-stu-id="068ca-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
