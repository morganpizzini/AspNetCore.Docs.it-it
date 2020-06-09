---
title: Crea la tua prima Blazor app
author: guardrex
description: Creare un' Blazor app dettagliata.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: ce3f20f7ee4ccfa73afc5f80a4429d9f4fe05591
ms.sourcegitcommit: 05490855e0c70565f0c4b509d392b0828bcfd141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507257"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="866e0-103">Crea la tua prima Blazor app</span><span class="sxs-lookup"><span data-stu-id="866e0-103">Build your first Blazor app</span></span>

<span data-ttu-id="866e0-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="866e0-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="866e0-105">Questa esercitazione illustra come creare e modificare un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="866e0-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="866e0-106">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="866e0-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="866e0-107">Creare un progetto di Blazor app elenco TODO</span><span class="sxs-lookup"><span data-stu-id="866e0-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="866e0-108">Modificare i Razor componenti</span><span class="sxs-lookup"><span data-stu-id="866e0-108">Modify Razor components</span></span>
> * <span data-ttu-id="866e0-109">Usare la gestione degli eventi e data binding nei componenti</span><span class="sxs-lookup"><span data-stu-id="866e0-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="866e0-110">Usare l'inserimento DI dipendenze e il routing in un' Blazor app</span><span class="sxs-lookup"><span data-stu-id="866e0-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="866e0-111">Al termine di questa esercitazione, si disporrà di un'app elenco attività.</span><span class="sxs-lookup"><span data-stu-id="866e0-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="866e0-112">Compilare i componenti</span><span class="sxs-lookup"><span data-stu-id="866e0-112">Build components</span></span>

1. <span data-ttu-id="866e0-113"><xref:blazor/get-started>Per creare un Blazor progetto per questa esercitazione, seguire le istruzioni riportate nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="866e0-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="866e0-114">Denominare il progetto *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="866e0-114">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="866e0-115">Passare a ognuna delle tre pagine dell'app nella cartella *pages* : Home, Counter e fetch data.</span><span class="sxs-lookup"><span data-stu-id="866e0-115">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="866e0-116">Queste pagine vengono implementate dai Razor file di componente *index. Razor*, *Counter. Razor*e *fetchData. Razor*.</span><span class="sxs-lookup"><span data-stu-id="866e0-116">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="866e0-117">Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina.</span><span class="sxs-lookup"><span data-stu-id="866e0-117">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="866e0-118">Per incrementare un contatore in una pagina Web è in genere necessario scrivere JavaScript.</span><span class="sxs-lookup"><span data-stu-id="866e0-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="866e0-119">Con Blazor è invece possibile scrivere in C#.</span><span class="sxs-lookup"><span data-stu-id="866e0-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="866e0-120">Esaminare l'implementazione del componente `Counter` nel file *Counter.razor*.</span><span class="sxs-lookup"><span data-stu-id="866e0-120">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="866e0-121">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="866e0-121">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="866e0-122">L'interfaccia utente del componente `Counter` viene definita tramite codice HTML.</span><span class="sxs-lookup"><span data-stu-id="866e0-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="866e0-123">La logica di rendering dinamica (ad esempio, cicli, condizionali, espressioni) viene aggiunta usando una sintassi C# incorporata denominata [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="866e0-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="866e0-124">Il markup HTML e la logica di rendering C# vengono convertiti un una classe del componente in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="866e0-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="866e0-125">Il nome della classe .NET generata corrisponde al nome del file.</span><span class="sxs-lookup"><span data-stu-id="866e0-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="866e0-126">I membri della classe del componente vengono definiti in un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="866e0-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="866e0-127">Nel blocco `@code`, lo stato del componente (proprietà, campi) e i metodi vengono specificati per la gestione degli eventi o per definire la logica di altri componenti.</span><span class="sxs-lookup"><span data-stu-id="866e0-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="866e0-128">Questi membri vengono quindi usati come parte della logica di rendering del componente e per la gestione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="866e0-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="866e0-129">Quando viene selezionato il pulsante **Click me**:</span><span class="sxs-lookup"><span data-stu-id="866e0-129">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="866e0-130">Viene chiamato il gestore `onclick` registrato del componente `Counter` (metodo `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="866e0-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="866e0-131">Il componente `Counter` rigenera il relativo albero di rendering.</span><span class="sxs-lookup"><span data-stu-id="866e0-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="866e0-132">Il nuovo albero di rendering viene confrontato con quello precedente.</span><span class="sxs-lookup"><span data-stu-id="866e0-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="866e0-133">Vengono applicate solo le modifiche al modello DOM (Document Object Model).</span><span class="sxs-lookup"><span data-stu-id="866e0-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="866e0-134">Il conteggio visualizzato viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="866e0-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="866e0-135">Modificare la logica C# del componente `Counter` per incrementare il contatore di due unità anziché una.</span><span class="sxs-lookup"><span data-stu-id="866e0-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="866e0-136">Ricompilare ed eseguire l'app per visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="866e0-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="866e0-137">Selezionare il pulsante **Fare clic qui**.</span><span class="sxs-lookup"><span data-stu-id="866e0-137">Select the **Click me** button.</span></span> <span data-ttu-id="866e0-138">Il contatore viene incrementato di due.</span><span class="sxs-lookup"><span data-stu-id="866e0-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="866e0-139">Usare i componenti</span><span class="sxs-lookup"><span data-stu-id="866e0-139">Use components</span></span>

<span data-ttu-id="866e0-140">Includere un componente in un altro componente usando una sintassi HTML.</span><span class="sxs-lookup"><span data-stu-id="866e0-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="866e0-141">Aggiungere il componente `Counter` al componente `Index` dell'app aggiungendo un elemento `<Counter />` al componente `Index` (*Index.razor*).</span><span class="sxs-lookup"><span data-stu-id="866e0-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="866e0-142">Se si usa Blazor webassembly per questa esperienza, un `SurveyPrompt` componente viene utilizzato dal `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="866e0-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="866e0-143">Sostituire l'elemento `<SurveyPrompt>` con un elemento `<Counter />`.</span><span class="sxs-lookup"><span data-stu-id="866e0-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="866e0-144">Se si usa un' Blazor app Server per questa esperienza, aggiungere l' `<Counter />` elemento al `Index` componente:</span><span class="sxs-lookup"><span data-stu-id="866e0-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="866e0-145">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="866e0-145">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="866e0-146">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="866e0-146">Rebuild and run the app.</span></span> <span data-ttu-id="866e0-147">Il componente `Index` ha il proprio contatore.</span><span class="sxs-lookup"><span data-stu-id="866e0-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="866e0-148">Parametri del componente</span><span class="sxs-lookup"><span data-stu-id="866e0-148">Component parameters</span></span>

<span data-ttu-id="866e0-149">I componenti possono avere anche parametri,</span><span class="sxs-lookup"><span data-stu-id="866e0-149">Components can also have parameters.</span></span> <span data-ttu-id="866e0-150">I parametri del componente vengono definiti usando proprietà pubbliche nella classe Component con l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="866e0-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="866e0-151">Usare gli attributi per specificare gli argomenti per un componente nel markup.</span><span class="sxs-lookup"><span data-stu-id="866e0-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="866e0-152">Aggiornare il `@code` codice C# del componente come segue:</span><span class="sxs-lookup"><span data-stu-id="866e0-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="866e0-153">Aggiungere una `IncrementAmount` proprietà pubblica con l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="866e0-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="866e0-154">Modificare il `IncrementCount` metodo in modo che usi la `IncrementAmount` proprietà quando si aumenta il valore di `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="866e0-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="866e0-155">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="866e0-155">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="866e0-156">Specificare un parametro `IncrementAmount` nell'elemento `<Counter>` del componente `Index` usando un attributo.</span><span class="sxs-lookup"><span data-stu-id="866e0-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="866e0-157">Impostare il valore per incrementare il contatore di dieci unità.</span><span class="sxs-lookup"><span data-stu-id="866e0-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="866e0-158">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="866e0-158">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="866e0-159">Ricaricare il componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="866e0-159">Reload the `Index` component.</span></span> <span data-ttu-id="866e0-160">Il contatore viene incrementato di dieci unità ogni volta che viene selezionato il pulsante **Click me**.</span><span class="sxs-lookup"><span data-stu-id="866e0-160">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="866e0-161">Il contatore nel componente `Counter` continua a essere incrementato di uno.</span><span class="sxs-lookup"><span data-stu-id="866e0-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="866e0-162">Indirizzare le richieste ai componenti</span><span class="sxs-lookup"><span data-stu-id="866e0-162">Route to components</span></span>

<span data-ttu-id="866e0-163">La direttiva `@page` all'inizio del file *Counter.razor* specifica che il componente `Counter` è un endpoint di routing.</span><span class="sxs-lookup"><span data-stu-id="866e0-163">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="866e0-164">Il componente `Counter` gestisce le richieste inviate a `/counter`.</span><span class="sxs-lookup"><span data-stu-id="866e0-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="866e0-165">Senza la direttiva `@page`, un componente non gestisce le richieste instradate, ma può comunque essere usato da altri componenti.</span><span class="sxs-lookup"><span data-stu-id="866e0-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="866e0-166">Inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="866e0-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor<span data-ttu-id="866e0-167">Esperienza server</span><span class="sxs-lookup"><span data-stu-id="866e0-167"> Server experience</span></span>

<span data-ttu-id="866e0-168">Se si utilizza un' Blazor app Server, il `WeatherForecastService` servizio viene registrato come [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="866e0-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="866e0-169">Un'istanza del servizio è disponibile in tutte le app tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="866e0-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="866e0-170">La [`@inject`](xref:mvc/views/razor#inject) direttiva viene utilizzata per inserire l'istanza del `WeatherForecastService` servizio nel `FetchData` componente.</span><span class="sxs-lookup"><span data-stu-id="866e0-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="866e0-171">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="866e0-171">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="866e0-172">Il componente `FetchData` usa il servizio inserito, come `ForecastService`, per recuperare una matrice di oggetti `WeatherForecast`:</span><span class="sxs-lookup"><span data-stu-id="866e0-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor<span data-ttu-id="866e0-173">Esperienza webassembly</span><span class="sxs-lookup"><span data-stu-id="866e0-173"> WebAssembly experience</span></span>

<span data-ttu-id="866e0-174">Se si usa un' Blazor app webassembly, <xref:System.Net.Http.HttpClient> viene inserito per ottenere i dati delle previsioni meteo dal file *Weather. JSON* nella cartella *wwwroot/Sample-Data* .</span><span class="sxs-lookup"><span data-stu-id="866e0-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="866e0-175">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="866e0-175">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="866e0-176">Un [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo viene usato per eseguire il rendering di ogni istanza di previsione come riga nella tabella dei dati meteorologici:</span><span class="sxs-lookup"><span data-stu-id="866e0-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="866e0-177">Compilare un elenco attività</span><span class="sxs-lookup"><span data-stu-id="866e0-177">Build a todo list</span></span>

<span data-ttu-id="866e0-178">Aggiungere all'app un nuovo componente che implementa un semplice elenco attività.</span><span class="sxs-lookup"><span data-stu-id="866e0-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="866e0-179">Aggiungere un nuovo `Todo` Razor componente all'app nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="866e0-179">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="866e0-180">Se si usa Visual Studio, fare clic con il pulsante destro del mouse sulla cartella **pagine** e scegliere **Aggiungi**  >  **nuovo elemento**  >  \*\* Razor componente\*\*.</span><span class="sxs-lookup"><span data-stu-id="866e0-180">If you're using Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="866e0-181">Denominare il file del componente *todo. Razor*.</span><span class="sxs-lookup"><span data-stu-id="866e0-181">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="866e0-182">In altri ambienti di sviluppo aggiungere un file vuoto alla cartella **pages** denominata *todo. Razor*.</span><span class="sxs-lookup"><span data-stu-id="866e0-182">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="866e0-183">Specificare il markup iniziale per il componente:</span><span class="sxs-lookup"><span data-stu-id="866e0-183">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="866e0-184">Aggiungere il componente `Todo` alla barra di spostamento.</span><span class="sxs-lookup"><span data-stu-id="866e0-184">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="866e0-185">Il componente `NavMenu` (*Shared/NavMenu.razor*) viene usato nel layout dell'app.</span><span class="sxs-lookup"><span data-stu-id="866e0-185">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="866e0-186">I layout sono componenti che consentono di evitare la duplicazione del contenuto nell'app.</span><span class="sxs-lookup"><span data-stu-id="866e0-186">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="866e0-187">Aggiungere un elemento `<NavLink>` per il componente `Todo` aggiungendo il markup seguente per la voce di elenco sotto le voci di elenco esistenti nel file *Shared/NavMenu.razor*:</span><span class="sxs-lookup"><span data-stu-id="866e0-187">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="866e0-188">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="866e0-188">Rebuild and run the app.</span></span> <span data-ttu-id="866e0-189">Visitare la nuova pagina Todo per confermare che il collegamento al componente `Todo` funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="866e0-189">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="866e0-190">Aggiungere un file *TodoItem.cs* nella radice del progetto per contenere una classe per rappresentare un elemento attività.</span><span class="sxs-lookup"><span data-stu-id="866e0-190">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="866e0-191">Usare il codice C# seguente per la classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="866e0-191">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="866e0-192">Tornare al componente `Todo` (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="866e0-192">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="866e0-193">Aggiungere un campo per le voci todo in un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="866e0-193">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="866e0-194">Il componente `Todo` usa questo campo per mantenere lo stato dell'elenco attività.</span><span class="sxs-lookup"><span data-stu-id="866e0-194">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="866e0-195">Aggiungere il markup per l'elenco non ordinato e un ciclo `foreach` per eseguire il rendering di ogni elemento Todo come elemento di elenco (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="866e0-195">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="866e0-196">L'app richiede elementi dell'interfaccia utente per l'aggiunta di voci todo all'elenco.</span><span class="sxs-lookup"><span data-stu-id="866e0-196">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="866e0-197">Aggiungere un input di testo (`<input>`) e un pulsante (`<button>`) sotto l'elenco non ordinato (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="866e0-197">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="866e0-198">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="866e0-198">Rebuild and run the app.</span></span> <span data-ttu-id="866e0-199">Quando viene selezionato il pulsante **Add todo** non accade nulla perché al pulsante non è associato un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="866e0-199">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="866e0-200">Aggiungere un metodo `AddTodo` al componente `Todo` e registrarlo per le selezioni con pulsante con l'attributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="866e0-200">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="866e0-201">Il metodo C# `AddTodo` viene chiamato quando viene selezionato il pulsante:</span><span class="sxs-lookup"><span data-stu-id="866e0-201">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="866e0-202">Per ottenere il titolo del nuovo elemento Todo, aggiungere un campo stringa `newTodo` all'inizio del blocco `@code` e associarlo al valore dell'input di testo tramite l'attributo `bind` dell'elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="866e0-202">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="866e0-203">Aggiornare il metodo `AddTodo` per aggiungere l'elemento `TodoItem` con il titolo specificato all'elenco.</span><span class="sxs-lookup"><span data-stu-id="866e0-203">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="866e0-204">Cancellare il valore dell'input di testo impostando `newTodo` su una stringa vuota:</span><span class="sxs-lookup"><span data-stu-id="866e0-204">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="866e0-205">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="866e0-205">Rebuild and run the app.</span></span> <span data-ttu-id="866e0-206">Aggiungere alcune voci todo all'elenco todo per testare il nuovo codice.</span><span class="sxs-lookup"><span data-stu-id="866e0-206">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="866e0-207">Il testo del titolo per ogni elemento Todo può essere reso modificabile e una casella di controllo può consentire all'utente di tenere traccia degli elementi completati.</span><span class="sxs-lookup"><span data-stu-id="866e0-207">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="866e0-208">Aggiungere un input casella di controllo per ogni elemento attività e associarne il valore alla proprietà `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="866e0-208">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="866e0-209">Modificare `@todo.Title` in un elemento `<input>` associato a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="866e0-209">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="866e0-210">Per verificare che questi valori siano associati, aggiornare l'intestazione `<h3>` per visualizzare un conteggio del numero di elementi attività non completati (`IsDone` è `false`).</span><span class="sxs-lookup"><span data-stu-id="866e0-210">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="866e0-211">Componente `Todo` completato (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="866e0-211">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="866e0-212">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="866e0-212">Rebuild and run the app.</span></span> <span data-ttu-id="866e0-213">Aggiungere elementi attività per testare il nuovo codice.</span><span class="sxs-lookup"><span data-stu-id="866e0-213">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="866e0-214">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="866e0-214">Next steps</span></span>

<span data-ttu-id="866e0-215">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="866e0-215">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="866e0-216">Creare un progetto di Blazor app elenco TODO</span><span class="sxs-lookup"><span data-stu-id="866e0-216">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="866e0-217">Modificare i Razor componenti</span><span class="sxs-lookup"><span data-stu-id="866e0-217">Modify Razor components</span></span>
> * <span data-ttu-id="866e0-218">Usare la gestione degli eventi e data binding nei componenti</span><span class="sxs-lookup"><span data-stu-id="866e0-218">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="866e0-219">Usare l'inserimento DI dipendenze e il routing in un' Blazor app</span><span class="sxs-lookup"><span data-stu-id="866e0-219">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="866e0-220">Informazioni su come creare e usare i componenti:</span><span class="sxs-lookup"><span data-stu-id="866e0-220">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
