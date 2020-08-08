---
title: Usare gli analizzatori dell'API Web
author: pranavkm
description: Informazioni sul pacchetto degli analizzatori di API Web ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/analyzers
ms.openlocfilehash: 571046052dbe131e9cdcf981aaee0921ed8c2ea1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021848"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="f02b7-103">Usare gli analizzatori dell'API Web</span><span class="sxs-lookup"><span data-stu-id="f02b7-103">Use web API analyzers</span></span>

<span data-ttu-id="f02b7-104">ASP.NET Core 2,2 e versioni successive fornisce un pacchetto di analizzatori MVC progettato per l'uso con i progetti API Web.</span><span class="sxs-lookup"><span data-stu-id="f02b7-104">ASP.NET Core 2.2 and later provides an MVC analyzers package intended for use with web API projects.</span></span> <span data-ttu-id="f02b7-105">Gli analizzatori funzionano con i controller annotati con <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> , durante la compilazione in [convenzioni API Web](xref:web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="f02b7-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [web API conventions](xref:web-api/advanced/conventions).</span></span>

<span data-ttu-id="f02b7-106">Il pacchetto degli analizzatori informa l'utente di qualsiasi azione del controller che:</span><span class="sxs-lookup"><span data-stu-id="f02b7-106">The analyzers package notifies you of any controller action that:</span></span>

* <span data-ttu-id="f02b7-107">Restituisce un codice di stato non dichiarato.</span><span class="sxs-lookup"><span data-stu-id="f02b7-107">Returns an undeclared status code.</span></span>
* <span data-ttu-id="f02b7-108">Restituisce un risultato non dichiarato di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="f02b7-108">Returns an undeclared success result.</span></span>
* <span data-ttu-id="f02b7-109">Documenta un codice di stato che non viene restituito.</span><span class="sxs-lookup"><span data-stu-id="f02b7-109">Documents a status code that isn't returned.</span></span>
* <span data-ttu-id="f02b7-110">Include un controllo di convalida del modello esplicito.</span><span class="sxs-lookup"><span data-stu-id="f02b7-110">Includes an explicit model validation check.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a><span data-ttu-id="f02b7-111">Fare riferimento al pacchetto dell'analizzatore</span><span class="sxs-lookup"><span data-stu-id="f02b7-111">Reference the analyzer package</span></span>

<span data-ttu-id="f02b7-112">In ASP.NET Core 3,0 o versioni successive, gli analizzatori sono inclusi nel .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="f02b7-112">In ASP.NET Core 3.0 or later, the analyzers are included in the .NET Core SDK.</span></span> <span data-ttu-id="f02b7-113">Per abilitare l'analizzatore nel progetto, includere la `IncludeOpenAPIAnalyzers` proprietà nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="f02b7-113">To enable the analyzer in your project, include the `IncludeOpenAPIAnalyzers` property in the project file:</span></span>

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a><span data-ttu-id="f02b7-114">Installazione del pacchetto</span><span class="sxs-lookup"><span data-stu-id="f02b7-114">Package installation</span></span>

<span data-ttu-id="f02b7-115">Installare il pacchetto NuGet [Microsoft. AspNetCore. Mvc. API. Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) con uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="f02b7-115">Install the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package with one of the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="f02b7-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f02b7-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f02b7-117">Dalla finestra **Console di Gestione pacchetti**:</span><span class="sxs-lookup"><span data-stu-id="f02b7-117">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="f02b7-118">Passare a **Visualizza** > **altre** > **console di gestione pacchetti**di Windows.</span><span class="sxs-lookup"><span data-stu-id="f02b7-118">Go to **View** > **Other Windows** > **Package Manager Console**.</span></span>
  * <span data-ttu-id="f02b7-119">Passare alla directory che contiene il file *ApiConventions.csproj*.</span><span class="sxs-lookup"><span data-stu-id="f02b7-119">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="f02b7-120">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="f02b7-120">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f02b7-121">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="f02b7-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f02b7-122">Fare clic con il pulsante destro del mouse sulla cartella *pacchetti* in **riquadro della soluzione** > **Aggiungi pacchetti...**.</span><span class="sxs-lookup"><span data-stu-id="f02b7-122">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**.</span></span>
* <span data-ttu-id="f02b7-123">Impostare l'elenco a discesa di **origine** della finestra **Aggiungi pacchetti** su "NuGet.org".</span><span class="sxs-lookup"><span data-stu-id="f02b7-123">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="f02b7-124">Immettere "Microsoft.AspNetCore.Mvc.Api.Analyzers" nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="f02b7-124">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="f02b7-125">Selezionare il pacchetto "Microsoft.AspNetCore.Mvc.Api.Analyzers" nel riquadro dei risultati e fare clic su **Aggiungi pacchetto**.</span><span class="sxs-lookup"><span data-stu-id="f02b7-125">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package**.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="f02b7-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f02b7-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f02b7-127">Eseguire il comando seguente da **Terminale integrato**:</span><span class="sxs-lookup"><span data-stu-id="f02b7-127">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[<span data-ttu-id="f02b7-128">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="f02b7-128">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f02b7-129">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="f02b7-129">Run the following command:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a><span data-ttu-id="f02b7-130">Analizzatori per le convenzioni API Web</span><span class="sxs-lookup"><span data-stu-id="f02b7-130">Analyzers for web API conventions</span></span>

<span data-ttu-id="f02b7-131">I documenti OpenAPI contengono i codici di stato e i tipi di risposta che può restituire un'azione.</span><span class="sxs-lookup"><span data-stu-id="f02b7-131">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="f02b7-132">In ASP.NET Core MVC, per documentare un'azione vengono usati attributi come <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> e <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>.</span><span class="sxs-lookup"><span data-stu-id="f02b7-132">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="f02b7-133"><xref:tutorials/web-api-help-pages-using-swagger>viene illustrato in dettaglio la documentazione dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="f02b7-133"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your web API.</span></span>

<span data-ttu-id="f02b7-134">Uno degli analizzatori del pacchetto verifica i controller annotati con <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> e identifica le azioni che non documentano completamente le relative risposte.</span><span class="sxs-lookup"><span data-stu-id="f02b7-134">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="f02b7-135">Prendere in considerazione gli esempi seguenti:</span><span class="sxs-lookup"><span data-stu-id="f02b7-135">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

<span data-ttu-id="f02b7-136">L'azione precedente documenta il tipo restituito HTTP 200 (operazione riuscita) ma non documenta il codice di stato HTTP 404 (operazione non riuscita).</span><span class="sxs-lookup"><span data-stu-id="f02b7-136">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="f02b7-137">L'analizzatore segnala l'assenza di documentazione per il codice di stato HTTP 404 come un avviso.</span><span class="sxs-lookup"><span data-stu-id="f02b7-137">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="f02b7-138">È disponibile un'opzione per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="f02b7-138">An option to fix the problem is provided.</span></span>

![Analizzatore che restituisce un avviso](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="f02b7-140">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f02b7-140">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
