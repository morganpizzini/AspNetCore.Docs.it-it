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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: f791dae5915c87d4c36f23419961e3c53e888743
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409072"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="50aa6-103">Crea la tua prima Blazor app</span><span class="sxs-lookup"><span data-stu-id="50aa6-103">Build your first Blazor app</span></span>

<span data-ttu-id="50aa6-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="50aa6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="50aa6-105">Questa esercitazione illustra come creare e modificare un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="50aa6-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="50aa6-106">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="50aa6-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="50aa6-107">Creare un progetto di Blazor app elenco TODO</span><span class="sxs-lookup"><span data-stu-id="50aa6-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="50aa6-108">Modificare i Razor componenti</span><span class="sxs-lookup"><span data-stu-id="50aa6-108">Modify Razor components</span></span>
> * <span data-ttu-id="50aa6-109">Usare la gestione degli eventi e data binding nei componenti</span><span class="sxs-lookup"><span data-stu-id="50aa6-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="50aa6-110">Usare l'inserimento DI dipendenze e il routing in un' Blazor app</span><span class="sxs-lookup"><span data-stu-id="50aa6-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="50aa6-111">Al termine di questa esercitazione, si disporrà di un'app elenco attività.</span><span class="sxs-lookup"><span data-stu-id="50aa6-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="50aa6-112">Compilare i componenti</span><span class="sxs-lookup"><span data-stu-id="50aa6-112">Build components</span></span>

1. <span data-ttu-id="50aa6-113"><xref:blazor/get-started>Per creare un Blazor progetto per questa esercitazione, seguire le istruzioni riportate nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="50aa6-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="50aa6-114">Assegnare al progetto il nome `ToDoList`.</span><span class="sxs-lookup"><span data-stu-id="50aa6-114">Name the project `ToDoList`.</span></span>

1. <span data-ttu-id="50aa6-115">Passare a ognuna delle tre pagine dell'app nella `Pages` cartella: `Home` , `Counter` e `Fetch data` .</span><span class="sxs-lookup"><span data-stu-id="50aa6-115">Browse to each of the app's three pages in the `Pages` folder: `Home`, `Counter`, and `Fetch data`.</span></span> <span data-ttu-id="50aa6-116">Queste pagine sono implementate dai Razor file dei componenti `Index.razor` , `Counter.razor` e `FetchData.razor` .</span><span class="sxs-lookup"><span data-stu-id="50aa6-116">These pages are implemented by the Razor component files `Index.razor`, `Counter.razor`, and `FetchData.razor`.</span></span>

1. <span data-ttu-id="50aa6-117">Nella `Counter` pagina selezionare il pulsante per incrementare il contatore senza un aggiornamento della pagina.</span><span class="sxs-lookup"><span data-stu-id="50aa6-117">On the `Counter` page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="50aa6-118">Per incrementare un contatore in una pagina Web è in genere necessario scrivere JavaScript.</span><span class="sxs-lookup"><span data-stu-id="50aa6-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="50aa6-119">Con Blazor è invece possibile scrivere in C#.</span><span class="sxs-lookup"><span data-stu-id="50aa6-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="50aa6-120">Esaminare l'implementazione del `Counter` componente nel `Counter.razor` file.</span><span class="sxs-lookup"><span data-stu-id="50aa6-120">Examine the implementation of the `Counter` component in the `Counter.razor` file.</span></span>

   <span data-ttu-id="50aa6-121">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="50aa6-121">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="50aa6-122">L'interfaccia utente del componente `Counter` viene definita tramite codice HTML.</span><span class="sxs-lookup"><span data-stu-id="50aa6-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="50aa6-123">La logica di rendering dinamica (ad esempio, cicli, condizionali, espressioni) viene aggiunta usando una sintassi C# incorporata denominata [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="50aa6-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="50aa6-124">Il markup HTML e la logica di rendering C# vengono convertiti un una classe del componente in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="50aa6-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="50aa6-125">Il nome della classe .NET generata corrisponde al nome del file.</span><span class="sxs-lookup"><span data-stu-id="50aa6-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="50aa6-126">I membri della classe del componente vengono definiti in un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="50aa6-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="50aa6-127">Nel blocco `@code`, lo stato del componente (proprietà, campi) e i metodi vengono specificati per la gestione degli eventi o per definire la logica di altri componenti.</span><span class="sxs-lookup"><span data-stu-id="50aa6-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="50aa6-128">Questi membri vengono quindi usati come parte della logica di rendering del componente e per la gestione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="50aa6-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="50aa6-129">Quando viene selezionato il pulsante incremento contatore:</span><span class="sxs-lookup"><span data-stu-id="50aa6-129">When the counter increment button is selected:</span></span>

   * <span data-ttu-id="50aa6-130">Viene chiamato il gestore `onclick` registrato del componente `Counter` (metodo `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="50aa6-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="50aa6-131">Il componente `Counter` rigenera il relativo albero di rendering.</span><span class="sxs-lookup"><span data-stu-id="50aa6-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="50aa6-132">Il nuovo albero di rendering viene confrontato con quello precedente.</span><span class="sxs-lookup"><span data-stu-id="50aa6-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="50aa6-133">Vengono applicate solo le modifiche al modello DOM (Document Object Model).</span><span class="sxs-lookup"><span data-stu-id="50aa6-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="50aa6-134">Il conteggio visualizzato viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="50aa6-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="50aa6-135">Modificare la logica C# del componente `Counter` per incrementare il contatore di due unità anziché una.</span><span class="sxs-lookup"><span data-stu-id="50aa6-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="50aa6-136">Ricompilare ed eseguire l'app per visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="50aa6-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="50aa6-137">Selezionare il pulsante.</span><span class="sxs-lookup"><span data-stu-id="50aa6-137">Select the button.</span></span> <span data-ttu-id="50aa6-138">Il contatore viene incrementato di due.</span><span class="sxs-lookup"><span data-stu-id="50aa6-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="50aa6-139">Usare i componenti</span><span class="sxs-lookup"><span data-stu-id="50aa6-139">Use components</span></span>

<span data-ttu-id="50aa6-140">Includere un componente in un altro componente usando una sintassi HTML.</span><span class="sxs-lookup"><span data-stu-id="50aa6-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="50aa6-141">Aggiungere il `Counter` componente al componente dell'app `Index` aggiungendo un `<Counter />` elemento al `Index` componente ( `Index.razor` ).</span><span class="sxs-lookup"><span data-stu-id="50aa6-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (`Index.razor`).</span></span>

   <span data-ttu-id="50aa6-142">Se si utilizza Blazor WebAssembly per questa esperienza, un `SurveyPrompt` componente viene utilizzato dal `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="50aa6-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="50aa6-143">Sostituire l'elemento `<SurveyPrompt>` con un elemento `<Counter />`.</span><span class="sxs-lookup"><span data-stu-id="50aa6-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="50aa6-144">Se si usa un' Blazor Server app per questa esperienza, aggiungere l' `<Counter />` elemento al `Index` componente:</span><span class="sxs-lookup"><span data-stu-id="50aa6-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="50aa6-145">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="50aa6-145">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="50aa6-146">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="50aa6-146">Rebuild and run the app.</span></span> <span data-ttu-id="50aa6-147">Il componente `Index` ha il proprio contatore.</span><span class="sxs-lookup"><span data-stu-id="50aa6-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="50aa6-148">Parametri del componente</span><span class="sxs-lookup"><span data-stu-id="50aa6-148">Component parameters</span></span>

<span data-ttu-id="50aa6-149">I componenti possono avere anche parametri,</span><span class="sxs-lookup"><span data-stu-id="50aa6-149">Components can also have parameters.</span></span> <span data-ttu-id="50aa6-150">I parametri del componente vengono definiti usando proprietà pubbliche nella classe Component con l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="50aa6-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="50aa6-151">Usare gli attributi per specificare gli argomenti per un componente nel markup.</span><span class="sxs-lookup"><span data-stu-id="50aa6-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="50aa6-152">Aggiornare il `@code` codice C# del componente come segue:</span><span class="sxs-lookup"><span data-stu-id="50aa6-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="50aa6-153">Aggiungere una `IncrementAmount` proprietà pubblica con l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="50aa6-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="50aa6-154">Modificare il `IncrementCount` metodo in modo che usi la `IncrementAmount` proprietà quando si aumenta il valore di `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="50aa6-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="50aa6-155">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="50aa6-155">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="50aa6-156">Specificare un parametro `IncrementAmount` nell'elemento `<Counter>` del componente `Index` usando un attributo.</span><span class="sxs-lookup"><span data-stu-id="50aa6-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="50aa6-157">Impostare il valore per incrementare il contatore di dieci unità.</span><span class="sxs-lookup"><span data-stu-id="50aa6-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="50aa6-158">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="50aa6-158">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="50aa6-159">Ricaricare il componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="50aa6-159">Reload the `Index` component.</span></span> <span data-ttu-id="50aa6-160">Il contatore viene incrementato di dieci volte a ogni selezione del pulsante.</span><span class="sxs-lookup"><span data-stu-id="50aa6-160">The counter increments by ten each time the button is selected.</span></span> <span data-ttu-id="50aa6-161">Il contatore nel componente `Counter` continua a essere incrementato di uno.</span><span class="sxs-lookup"><span data-stu-id="50aa6-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="50aa6-162">Indirizzare le richieste ai componenti</span><span class="sxs-lookup"><span data-stu-id="50aa6-162">Route to components</span></span>

<span data-ttu-id="50aa6-163">La `@page` direttiva all'inizio del `Counter.razor` file specifica che il `Counter` componente è un endpoint di routing.</span><span class="sxs-lookup"><span data-stu-id="50aa6-163">The `@page` directive at the top of the `Counter.razor` file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="50aa6-164">Il componente `Counter` gestisce le richieste inviate a `/counter`.</span><span class="sxs-lookup"><span data-stu-id="50aa6-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="50aa6-165">Senza la direttiva `@page`, un componente non gestisce le richieste instradate, ma può comunque essere usato da altri componenti.</span><span class="sxs-lookup"><span data-stu-id="50aa6-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="50aa6-166">Inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="50aa6-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor Server<span data-ttu-id="50aa6-167">esperienza</span><span class="sxs-lookup"><span data-stu-id="50aa6-167"> experience</span></span>

<span data-ttu-id="50aa6-168">Se si utilizza un' Blazor Server app, il `WeatherForecastService` servizio viene registrato come [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50aa6-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="50aa6-169">Un'istanza del servizio è disponibile in tutte le app tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="50aa6-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="50aa6-170">La [`@inject`](xref:mvc/views/razor#inject) direttiva viene utilizzata per inserire l'istanza del `WeatherForecastService` servizio nel `FetchData` componente.</span><span class="sxs-lookup"><span data-stu-id="50aa6-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="50aa6-171">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="50aa6-171">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="50aa6-172">Il componente `FetchData` usa il servizio inserito, come `ForecastService`, per recuperare una matrice di oggetti `WeatherForecast`:</span><span class="sxs-lookup"><span data-stu-id="50aa6-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor WebAssembly<span data-ttu-id="50aa6-173">esperienza</span><span class="sxs-lookup"><span data-stu-id="50aa6-173"> experience</span></span>

<span data-ttu-id="50aa6-174">Se si usa un' Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> viene inserito per ottenere i dati delle previsioni meteo dal `weather.json` file nella `wwwroot/sample-data` cartella.</span><span class="sxs-lookup"><span data-stu-id="50aa6-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the `weather.json` file in the `wwwroot/sample-data` folder.</span></span>

<span data-ttu-id="50aa6-175">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="50aa6-175">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="50aa6-176">Un [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo viene usato per eseguire il rendering di ogni istanza di previsione come riga nella tabella dei dati meteorologici:</span><span class="sxs-lookup"><span data-stu-id="50aa6-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="50aa6-177">Compilare un elenco attività</span><span class="sxs-lookup"><span data-stu-id="50aa6-177">Build a todo list</span></span>

<span data-ttu-id="50aa6-178">Aggiungere all'app un nuovo componente che implementa un semplice elenco attività.</span><span class="sxs-lookup"><span data-stu-id="50aa6-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="50aa6-179">Aggiungere un nuovo `Todo` Razor componente all'app nella `Pages` cartella.</span><span class="sxs-lookup"><span data-stu-id="50aa6-179">Add a new `Todo` Razor component to the app in the `Pages` folder.</span></span> <span data-ttu-id="50aa6-180">Se si usa Visual Studio, fare clic con il pulsante destro del mouse sulla `Pages` cartella e scegliere **Aggiungi**  >  **nuovo elemento**  >  \*\* Razor componente\*\*.</span><span class="sxs-lookup"><span data-stu-id="50aa6-180">If you're using Visual Studio, right-click the `Pages` folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="50aa6-181">Assegnare un nome al file del componente `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="50aa6-181">Name the component's file `Todo.razor`.</span></span> <span data-ttu-id="50aa6-182">In altri ambienti di sviluppo, aggiungere un file vuoto alla `Pages` cartella denominata `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="50aa6-182">In other development environments, add a blank file to the `Pages` folder named `Todo.razor`.</span></span> Razor<span data-ttu-id="50aa6-183">i nomi dei file di componente richiedono una prima lettera maiuscola, quindi verificare che il `Todo` nome del file del componente inizi con una lettera maiuscola `T` .</span><span class="sxs-lookup"><span data-stu-id="50aa6-183"> component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="50aa6-184">Specificare il markup iniziale per il componente:</span><span class="sxs-lookup"><span data-stu-id="50aa6-184">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="50aa6-185">Aggiungere il componente `Todo` alla barra di spostamento.</span><span class="sxs-lookup"><span data-stu-id="50aa6-185">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="50aa6-186">Il `NavMenu` componente ( `Shared/NavMenu.razor` ) viene usato nel layout dell'app.</span><span class="sxs-lookup"><span data-stu-id="50aa6-186">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="50aa6-187">I layout sono componenti che consentono di evitare la duplicazione del contenuto nell'app.</span><span class="sxs-lookup"><span data-stu-id="50aa6-187">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="50aa6-188">Aggiungere un `<NavLink>` elemento per il `Todo` componente aggiungendo il markup dell'elemento di elenco seguente sotto le voci di elenco esistenti nel `Shared/NavMenu.razor` file:</span><span class="sxs-lookup"><span data-stu-id="50aa6-188">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="50aa6-189">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="50aa6-189">Rebuild and run the app.</span></span> <span data-ttu-id="50aa6-190">Visitare la nuova pagina Todo per confermare che il collegamento al componente `Todo` funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="50aa6-190">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="50aa6-191">Aggiungere un `TodoItem.cs` file alla radice del progetto per archiviare una classe che rappresenta un elemento todo.</span><span class="sxs-lookup"><span data-stu-id="50aa6-191">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="50aa6-192">Usare il codice C# seguente per la classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="50aa6-192">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="50aa6-193">Tornare al `Todo` componente ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="50aa6-193">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="50aa6-194">Aggiungere un campo per le voci todo in un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="50aa6-194">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="50aa6-195">Il componente `Todo` usa questo campo per mantenere lo stato dell'elenco attività.</span><span class="sxs-lookup"><span data-stu-id="50aa6-195">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="50aa6-196">Aggiungere il markup per l'elenco non ordinato e un ciclo `foreach` per eseguire il rendering di ogni elemento Todo come elemento di elenco (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="50aa6-196">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="50aa6-197">L'app richiede elementi dell'interfaccia utente per l'aggiunta di voci todo all'elenco.</span><span class="sxs-lookup"><span data-stu-id="50aa6-197">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="50aa6-198">Aggiungere un input di testo (`<input>`) e un pulsante (`<button>`) sotto l'elenco non ordinato (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="50aa6-198">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="50aa6-199">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="50aa6-199">Rebuild and run the app.</span></span> <span data-ttu-id="50aa6-200">Quando il **`Add todo`** pulsante è selezionato, non accade nulla perché un gestore eventi non è cablato fino al pulsante.</span><span class="sxs-lookup"><span data-stu-id="50aa6-200">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="50aa6-201">Aggiungere un metodo `AddTodo` al componente `Todo` e registrarlo per le selezioni con pulsante con l'attributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="50aa6-201">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="50aa6-202">Il metodo C# `AddTodo` viene chiamato quando viene selezionato il pulsante:</span><span class="sxs-lookup"><span data-stu-id="50aa6-202">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="50aa6-203">Per ottenere il titolo del nuovo elemento Todo, aggiungere un campo stringa `newTodo` all'inizio del blocco `@code` e associarlo al valore dell'input di testo tramite l'attributo `bind` dell'elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="50aa6-203">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="50aa6-204">Aggiornare il metodo `AddTodo` per aggiungere l'elemento `TodoItem` con il titolo specificato all'elenco.</span><span class="sxs-lookup"><span data-stu-id="50aa6-204">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="50aa6-205">Cancellare il valore dell'input di testo impostando `newTodo` su una stringa vuota:</span><span class="sxs-lookup"><span data-stu-id="50aa6-205">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="50aa6-206">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="50aa6-206">Rebuild and run the app.</span></span> <span data-ttu-id="50aa6-207">Aggiungere alcune voci todo all'elenco todo per testare il nuovo codice.</span><span class="sxs-lookup"><span data-stu-id="50aa6-207">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="50aa6-208">Il testo del titolo per ogni elemento Todo può essere reso modificabile e una casella di controllo può consentire all'utente di tenere traccia degli elementi completati.</span><span class="sxs-lookup"><span data-stu-id="50aa6-208">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="50aa6-209">Aggiungere un input casella di controllo per ogni elemento attività e associarne il valore alla proprietà `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="50aa6-209">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="50aa6-210">Modificare `@todo.Title` in un elemento `<input>` associato a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="50aa6-210">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="50aa6-211">Per verificare che questi valori siano associati, aggiornare l'intestazione `<h3>` per visualizzare un conteggio del numero di elementi attività non completati (`IsDone` è `false`).</span><span class="sxs-lookup"><span data-stu-id="50aa6-211">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="50aa6-212">Componente completato `Todo` ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="50aa6-212">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="50aa6-213">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="50aa6-213">Rebuild and run the app.</span></span> <span data-ttu-id="50aa6-214">Aggiungere elementi attività per testare il nuovo codice.</span><span class="sxs-lookup"><span data-stu-id="50aa6-214">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="50aa6-215">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="50aa6-215">Next steps</span></span>

<span data-ttu-id="50aa6-216">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="50aa6-216">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="50aa6-217">Creare un progetto di Blazor app elenco TODO</span><span class="sxs-lookup"><span data-stu-id="50aa6-217">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="50aa6-218">Modificare i Razor componenti</span><span class="sxs-lookup"><span data-stu-id="50aa6-218">Modify Razor components</span></span>
> * <span data-ttu-id="50aa6-219">Usare la gestione degli eventi e data binding nei componenti</span><span class="sxs-lookup"><span data-stu-id="50aa6-219">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="50aa6-220">Usare l'inserimento DI dipendenze e il routing in un' Blazor app</span><span class="sxs-lookup"><span data-stu-id="50aa6-220">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="50aa6-221">Informazioni su come creare e usare i componenti:</span><span class="sxs-lookup"><span data-stu-id="50aa6-221">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components/index>
