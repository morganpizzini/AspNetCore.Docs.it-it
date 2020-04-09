---
title: Crea la Blazor tua prima app
author: guardrex
description: Crea Blazor un'app passo dopo passo.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
no-loc:
- Blazor
uid: tutorials/first-blazor-app
ms.openlocfilehash: 138057c2ceb9ed01bdf958c01f5cf2275387df23
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989428"
---
# <a name="build-your-first-opno-locblazor-app"></a><span data-ttu-id="25078-103">Crea la Blazor tua prima app</span><span class="sxs-lookup"><span data-stu-id="25078-103">Build your first Blazor app</span></span>

<span data-ttu-id="25078-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="25078-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="25078-105">Questa esercitazione illustra come compilare e modificare un'app. Blazor</span><span class="sxs-lookup"><span data-stu-id="25078-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

## <a name="build-components"></a><span data-ttu-id="25078-106">Compilare i componenti</span><span class="sxs-lookup"><span data-stu-id="25078-106">Build components</span></span>

1. <span data-ttu-id="25078-107">Seguire le indicazioni <xref:blazor/get-started> contenute Blazor nell'articolo per creare un progetto per questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="25078-107">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="25078-108">Denominare il progetto *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="25078-108">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="25078-109">Passare a ognuna delle tre pagine dell'app nella cartella *Pagine:* Dati Home, Contatore e Recupera.</span><span class="sxs-lookup"><span data-stu-id="25078-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="25078-110">Queste pagine vengono implementate dai file dei componenti Razor *Index.razor*, *Counter.razor* e *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="25078-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="25078-111">Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina.</span><span class="sxs-lookup"><span data-stu-id="25078-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="25078-112">L'incremento di un contatore in una pagina Web richiede in genere la scrittura di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="25078-112">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="25078-113">Con Blazor, è invece possibile scrivere C .</span><span class="sxs-lookup"><span data-stu-id="25078-113">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="25078-114">Esaminare l'implementazione del componente `Counter` nel file *Counter.razor*.</span><span class="sxs-lookup"><span data-stu-id="25078-114">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="25078-115">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="25078-115">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="25078-116">L'interfaccia utente del componente `Counter` viene definita tramite codice HTML.</span><span class="sxs-lookup"><span data-stu-id="25078-116">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="25078-117">La logica di rendering dinamico (ad esempio, cicli, condizioni, espressioni) viene aggiunta usando una sintassi C# incorporata chiamata [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="25078-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="25078-118">Il markup HTML e la logica di rendering C# vengono convertiti un una classe del componente in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="25078-118">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="25078-119">Il nome della classe .NET generata corrisponde al nome del file.</span><span class="sxs-lookup"><span data-stu-id="25078-119">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="25078-120">I membri della classe del componente vengono definiti in un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="25078-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="25078-121">Nel blocco `@code`, lo stato del componente (proprietà, campi) e i metodi vengono specificati per la gestione degli eventi o per definire la logica di altri componenti.</span><span class="sxs-lookup"><span data-stu-id="25078-121">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="25078-122">Questi membri vengono quindi usati come parte della logica di rendering del componente e per la gestione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="25078-122">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="25078-123">Quando viene selezionato il pulsante **Click me**:</span><span class="sxs-lookup"><span data-stu-id="25078-123">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="25078-124">Viene chiamato il gestore `onclick` registrato del componente `Counter` (metodo `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="25078-124">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="25078-125">Il componente `Counter` rigenera il relativo albero di rendering.</span><span class="sxs-lookup"><span data-stu-id="25078-125">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="25078-126">Il nuovo albero di rendering viene confrontato con quello precedente.</span><span class="sxs-lookup"><span data-stu-id="25078-126">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="25078-127">Vengono applicate solo le modifiche al modello DOM (Document Object Model).</span><span class="sxs-lookup"><span data-stu-id="25078-127">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="25078-128">Il conteggio visualizzato viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="25078-128">The displayed count is updated.</span></span>

1. <span data-ttu-id="25078-129">Modificare la logica C# del componente `Counter` per incrementare il contatore di due unità anziché una.</span><span class="sxs-lookup"><span data-stu-id="25078-129">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="25078-130">Ricompilare ed eseguire l'app per visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="25078-130">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="25078-131">Selezionare il pulsante **Fare clic qui**.</span><span class="sxs-lookup"><span data-stu-id="25078-131">Select the **Click me** button.</span></span> <span data-ttu-id="25078-132">Il contatore viene incrementato di due.</span><span class="sxs-lookup"><span data-stu-id="25078-132">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="25078-133">Usare i componenti</span><span class="sxs-lookup"><span data-stu-id="25078-133">Use components</span></span>

<span data-ttu-id="25078-134">Includere un componente in un altro componente usando una sintassi HTML.</span><span class="sxs-lookup"><span data-stu-id="25078-134">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="25078-135">Aggiungere il componente `Counter` al componente `Index` dell'app aggiungendo un elemento `<Counter />` al componente `Index` (*Index.razor*).</span><span class="sxs-lookup"><span data-stu-id="25078-135">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="25078-136">Se si utilizza Blazor WebAssembly per questa `SurveyPrompt` esperienza, un `Index` componente viene utilizzato dal componente.</span><span class="sxs-lookup"><span data-stu-id="25078-136">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="25078-137">Sostituire l'elemento `<SurveyPrompt>` con un elemento `<Counter />`.</span><span class="sxs-lookup"><span data-stu-id="25078-137">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="25078-138">Se si usa Blazor un'app Server per `<Counter />` questa esperienza, aggiungere l'elemento al `Index` componente:</span><span class="sxs-lookup"><span data-stu-id="25078-138">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="25078-139">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="25078-139">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="25078-140">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="25078-140">Rebuild and run the app.</span></span> <span data-ttu-id="25078-141">Il componente `Index` ha il proprio contatore.</span><span class="sxs-lookup"><span data-stu-id="25078-141">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="25078-142">Parametri del componente</span><span class="sxs-lookup"><span data-stu-id="25078-142">Component parameters</span></span>

<span data-ttu-id="25078-143">I componenti possono avere anche parametri,</span><span class="sxs-lookup"><span data-stu-id="25078-143">Components can also have parameters.</span></span> <span data-ttu-id="25078-144">I parametri del componente vengono definiti utilizzando `[Parameter]` le proprietà pubbliche nella classe del componente con l'attributo .</span><span class="sxs-lookup"><span data-stu-id="25078-144">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="25078-145">Usare gli attributi per specificare gli argomenti per un componente nel markup.</span><span class="sxs-lookup"><span data-stu-id="25078-145">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="25078-146">Aggiornare il `@code` codice C' del componente come segue:</span><span class="sxs-lookup"><span data-stu-id="25078-146">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="25078-147">Aggiungere una `IncrementAmount` proprietà `[Parameter]` pubblica con l'attributo .</span><span class="sxs-lookup"><span data-stu-id="25078-147">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="25078-148">Modificare `IncrementCount` il metodo `IncrementAmount` in modo da `currentCount`utilizzare la proprietà quando si aumenta il valore di .</span><span class="sxs-lookup"><span data-stu-id="25078-148">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="25078-149">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="25078-149">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="25078-150">Specificare un parametro `IncrementAmount` nell'elemento `<Counter>` del componente `Index` usando un attributo.</span><span class="sxs-lookup"><span data-stu-id="25078-150">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="25078-151">Impostare il valore per incrementare il contatore di dieci unità.</span><span class="sxs-lookup"><span data-stu-id="25078-151">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="25078-152">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="25078-152">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="25078-153">Ricaricare il componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="25078-153">Reload the `Index` component.</span></span> <span data-ttu-id="25078-154">Il contatore viene incrementato di dieci unità ogni volta che viene selezionato il pulsante **Click me**.</span><span class="sxs-lookup"><span data-stu-id="25078-154">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="25078-155">Il contatore nel componente `Counter` continua a essere incrementato di uno.</span><span class="sxs-lookup"><span data-stu-id="25078-155">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="25078-156">Indirizzare le richieste ai componenti</span><span class="sxs-lookup"><span data-stu-id="25078-156">Route to components</span></span>

<span data-ttu-id="25078-157">La direttiva `@page` all'inizio del file *Counter.razor* specifica che il componente `Counter` è un endpoint di routing.</span><span class="sxs-lookup"><span data-stu-id="25078-157">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="25078-158">Il componente `Counter` gestisce le richieste inviate a `/counter`.</span><span class="sxs-lookup"><span data-stu-id="25078-158">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="25078-159">Senza la direttiva `@page`, un componente non gestisce le richieste instradate, ma può comunque essere usato da altri componenti.</span><span class="sxs-lookup"><span data-stu-id="25078-159">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="25078-160">Inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="25078-160">Dependency injection</span></span>

### <a name="opno-locblazor-server-experience"></a>Blazor<span data-ttu-id="25078-161">Esperienza server</span><span class="sxs-lookup"><span data-stu-id="25078-161"> Server experience</span></span>

<span data-ttu-id="25078-162">Se si Blazor utilizza un'app Server, il `WeatherForecastService` servizio viene registrato come [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="25078-162">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="25078-163">Un'istanza del servizio è disponibile in tutta l'app tramite inserimento delle [dipendenze (DI)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="25078-163">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="25078-164">La `@inject` direttiva viene utilizzata per `WeatherForecastService` inserire `FetchData` l'istanza del servizio nel componente.</span><span class="sxs-lookup"><span data-stu-id="25078-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="25078-165">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="25078-165">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="25078-166">Il componente `FetchData` usa il servizio inserito, come `ForecastService`, per recuperare una matrice di oggetti `WeatherForecast`:</span><span class="sxs-lookup"><span data-stu-id="25078-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="opno-locblazor-webassembly-experience"></a>Blazor<span data-ttu-id="25078-167">Esperienza WebAssembly</span><span class="sxs-lookup"><span data-stu-id="25078-167"> WebAssembly experience</span></span>

<span data-ttu-id="25078-168">Se si Blazor utilizza un'app WebAssembly, `HttpClient` viene inserito l'immissione per ottenere i dati delle previsioni meteo dal file *weather.json* nella cartella *wwwroot/sample-data.*</span><span class="sxs-lookup"><span data-stu-id="25078-168">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="25078-169">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="25078-169">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="25078-170">Un [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo viene utilizzato per eseguire il rendering di ogni istanza di previsione come riga nella tabella dei dati meteo:An loop is used to render each forecast instance as a row in the table of weather data:</span><span class="sxs-lookup"><span data-stu-id="25078-170">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="25078-171">Compilare un elenco attività</span><span class="sxs-lookup"><span data-stu-id="25078-171">Build a todo list</span></span>

<span data-ttu-id="25078-172">Aggiungere all'app un nuovo componente che implementa un semplice elenco attività.</span><span class="sxs-lookup"><span data-stu-id="25078-172">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="25078-173">Aggiungi un `Todo` nuovo componente Razor all'app nella cartella *Pages.*</span><span class="sxs-lookup"><span data-stu-id="25078-173">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="25078-174">In Visual Studio fare clic con il pulsante destro del mouse sulla cartella **Pages** e scegliere **Aggiungi** > **nuovo** > **componente Razor**elemento .</span><span class="sxs-lookup"><span data-stu-id="25078-174">In Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="25078-175">Assegnare al file del componente il nome *Todo.razor*.</span><span class="sxs-lookup"><span data-stu-id="25078-175">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="25078-176">In altri ambienti di sviluppo, aggiungere un file vuoto alla cartella **Pages** denominata *Todo.razor*.</span><span class="sxs-lookup"><span data-stu-id="25078-176">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="25078-177">Specificare il markup iniziale per il componente:</span><span class="sxs-lookup"><span data-stu-id="25078-177">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="25078-178">Aggiungere il componente `Todo` alla barra di spostamento.</span><span class="sxs-lookup"><span data-stu-id="25078-178">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="25078-179">Il componente `NavMenu` (*Shared/NavMenu.razor*) viene usato nel layout dell'app.</span><span class="sxs-lookup"><span data-stu-id="25078-179">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="25078-180">I layout sono componenti che consentono di evitare la duplicazione del contenuto nell'app.</span><span class="sxs-lookup"><span data-stu-id="25078-180">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="25078-181">Aggiungere un elemento `<NavLink>` per il componente `Todo` aggiungendo il markup seguente per la voce di elenco sotto le voci di elenco esistenti nel file *Shared/NavMenu.razor*:</span><span class="sxs-lookup"><span data-stu-id="25078-181">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="25078-182">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="25078-182">Rebuild and run the app.</span></span> <span data-ttu-id="25078-183">Visitare la nuova pagina Todo per confermare che il collegamento al componente `Todo` funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="25078-183">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="25078-184">Aggiungere un file *TodoItem.cs* nella radice del progetto per contenere una classe per rappresentare un elemento attività.</span><span class="sxs-lookup"><span data-stu-id="25078-184">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="25078-185">Usare il codice C# seguente per la classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="25078-185">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="25078-186">Tornare al componente `Todo` (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="25078-186">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="25078-187">Aggiungere un campo per le voci todo in un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="25078-187">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="25078-188">Il componente `Todo` usa questo campo per mantenere lo stato dell'elenco attività.</span><span class="sxs-lookup"><span data-stu-id="25078-188">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="25078-189">Aggiungere il markup per l'elenco non ordinato e un ciclo `foreach` per eseguire il rendering di ogni elemento Todo come elemento di elenco (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="25078-189">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="25078-190">L'app richiede elementi dell'interfaccia utente per l'aggiunta di voci todo all'elenco.</span><span class="sxs-lookup"><span data-stu-id="25078-190">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="25078-191">Aggiungere un input di testo (`<input>`) e un pulsante (`<button>`) sotto l'elenco non ordinato (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="25078-191">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="25078-192">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="25078-192">Rebuild and run the app.</span></span> <span data-ttu-id="25078-193">Quando viene selezionato il pulsante **Add todo** non accade nulla perché al pulsante non è associato un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="25078-193">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="25078-194">Aggiungere un metodo `AddTodo` al componente `Todo` e registrarlo per le selezioni con pulsante con l'attributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="25078-194">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="25078-195">Il metodo C# `AddTodo` viene chiamato quando viene selezionato il pulsante:</span><span class="sxs-lookup"><span data-stu-id="25078-195">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="25078-196">Per ottenere il titolo del nuovo elemento Todo, aggiungere un campo stringa `newTodo` all'inizio del blocco `@code` e associarlo al valore dell'input di testo tramite l'attributo `bind` dell'elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="25078-196">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="25078-197">Aggiornare il metodo `AddTodo` per aggiungere l'elemento `TodoItem` con il titolo specificato all'elenco.</span><span class="sxs-lookup"><span data-stu-id="25078-197">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="25078-198">Cancellare il valore dell'input di testo impostando `newTodo` su una stringa vuota:</span><span class="sxs-lookup"><span data-stu-id="25078-198">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="25078-199">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="25078-199">Rebuild and run the app.</span></span> <span data-ttu-id="25078-200">Aggiungere alcune voci todo all'elenco todo per testare il nuovo codice.</span><span class="sxs-lookup"><span data-stu-id="25078-200">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="25078-201">Il testo del titolo per ogni elemento Todo può essere reso modificabile e una casella di controllo può consentire all'utente di tenere traccia degli elementi completati.</span><span class="sxs-lookup"><span data-stu-id="25078-201">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="25078-202">Aggiungere un input casella di controllo per ogni elemento attività e associarne il valore alla proprietà `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="25078-202">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="25078-203">Modificare `@todo.Title` in un elemento `<input>` associato a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="25078-203">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="25078-204">Per verificare che questi valori siano associati, aggiornare l'intestazione `<h3>` per visualizzare un conteggio del numero di elementi attività non completati (`IsDone` è `false`).</span><span class="sxs-lookup"><span data-stu-id="25078-204">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="25078-205">Componente `Todo` completato (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="25078-205">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="25078-206">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="25078-206">Rebuild and run the app.</span></span> <span data-ttu-id="25078-207">Aggiungere elementi attività per testare il nuovo codice.</span><span class="sxs-lookup"><span data-stu-id="25078-207">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
