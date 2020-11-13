---
title: "Creare un' :::no-loc(Blazor)::: app elenco attività"
author: guardrex
description: "Creare un' :::no-loc(Blazor)::: app dettagliata."
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 1efcd167d9a45b2def271b239c9b360749d72791
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570185"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="630b7-103">Creare un' :::no-loc(Blazor)::: app elenco attività</span><span class="sxs-lookup"><span data-stu-id="630b7-103">Build a :::no-loc(Blazor)::: todo list app</span></span>

<span data-ttu-id="630b7-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="630b7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="630b7-105">Questa esercitazione illustra come creare e modificare un' :::no-loc(Blazor)::: app.</span><span class="sxs-lookup"><span data-stu-id="630b7-105">This tutorial shows you how to build and modify a :::no-loc(Blazor)::: app.</span></span> <span data-ttu-id="630b7-106">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="630b7-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="630b7-107">Creare un progetto di :::no-loc(Blazor)::: app elenco TODO</span><span class="sxs-lookup"><span data-stu-id="630b7-107">Create a todo list :::no-loc(Blazor)::: app project</span></span>
> * <span data-ttu-id="630b7-108">Modificare i :::no-loc(Razor)::: componenti</span><span class="sxs-lookup"><span data-stu-id="630b7-108">Modify :::no-loc(Razor)::: components</span></span>
> * <span data-ttu-id="630b7-109">Usare la gestione degli eventi e data binding nei componenti</span><span class="sxs-lookup"><span data-stu-id="630b7-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="630b7-110">Usare il routing in un' :::no-loc(Blazor)::: app</span><span class="sxs-lookup"><span data-stu-id="630b7-110">Use routing in a :::no-loc(Blazor)::: app</span></span>

<span data-ttu-id="630b7-111">Al termine di questa esercitazione, si disporrà di un'app elenco attività.</span><span class="sxs-lookup"><span data-stu-id="630b7-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="630b7-112">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="630b7-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="630b7-113">Creare un'app elenco attività :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="630b7-113">Create a todo list :::no-loc(Blazor)::: app</span></span>

1. <span data-ttu-id="630b7-114">Creare una nuova :::no-loc(Blazor)::: App denominata `TodoList` in una shell comandi:</span><span class="sxs-lookup"><span data-stu-id="630b7-114">Create a new :::no-loc(Blazor)::: app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="630b7-115">Il comando precedente crea una cartella denominata `TodoList` per ospitare l'app.</span><span class="sxs-lookup"><span data-stu-id="630b7-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="630b7-116">La `TodoList` cartella è la *cartella radice* del progetto.</span><span class="sxs-lookup"><span data-stu-id="630b7-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="630b7-117">Passare alla `TodoList` cartella con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="630b7-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="630b7-118">Aggiungere un nuovo `Todo` :::no-loc(Razor)::: componente all'app nella `Pages` cartella usando il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="630b7-118">Add a new `Todo` :::no-loc(Razor)::: component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="630b7-119">:::no-loc(Razor)::: i nomi dei file di componente richiedono una prima lettera maiuscola.</span><span class="sxs-lookup"><span data-stu-id="630b7-119">:::no-loc(Razor)::: component file names require a capitalized first letter.</span></span> <span data-ttu-id="630b7-120">Aprire la `Pages` cartella e verificare che il `Todo` nome del file del componente inizi con una lettera maiuscola `T` .</span><span class="sxs-lookup"><span data-stu-id="630b7-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="630b7-121">Il nome del file deve essere `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="630b7-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="630b7-122">In `Pages/Todo.razor` fornire il markup iniziale per il componente:</span><span class="sxs-lookup"><span data-stu-id="630b7-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="630b7-123">Aggiungere il componente `Todo` alla barra di spostamento.</span><span class="sxs-lookup"><span data-stu-id="630b7-123">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="630b7-124">Il `NavMenu` componente ( `Shared/NavMenu.razor` ) viene usato nel layout dell'app.</span><span class="sxs-lookup"><span data-stu-id="630b7-124">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="630b7-125">I layout sono componenti che consentono di evitare la duplicazione del contenuto nell'app.</span><span class="sxs-lookup"><span data-stu-id="630b7-125">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="630b7-126">Aggiungere un `<NavLink>` elemento per il `Todo` componente aggiungendo il markup dell'elemento di elenco seguente sotto le voci di elenco esistenti nel `Shared/NavMenu.razor` file:</span><span class="sxs-lookup"><span data-stu-id="630b7-126">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="630b7-127">Compilare ed eseguire l'app eseguendo il `dotnet run` comando nella shell dei comandi dalla `TodoList` cartella.</span><span class="sxs-lookup"><span data-stu-id="630b7-127">Build and run the app by executing the `dotnet run` command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="630b7-128">Visitare la nuova pagina todo in `https://localhost:5001/todo` per confermare la funzione del collegamento al `Todo` componente.</span><span class="sxs-lookup"><span data-stu-id="630b7-128">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="630b7-129">Aggiungere un `TodoItem.cs` file alla radice del progetto (la `TodoList` cartella) in cui archiviare una classe che rappresenta un elemento todo.</span><span class="sxs-lookup"><span data-stu-id="630b7-129">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="630b7-130">Usare il codice C# seguente per la classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="630b7-130">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="630b7-131">Tornare al `Todo` componente ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="630b7-131">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="630b7-132">Aggiungere un campo per le voci todo in un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="630b7-132">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="630b7-133">Il componente `Todo` usa questo campo per mantenere lo stato dell'elenco attività.</span><span class="sxs-lookup"><span data-stu-id="630b7-133">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="630b7-134">Aggiungere il markup per l'elenco non ordinato e un ciclo `foreach` per eseguire il rendering di ogni elemento Todo come elemento di elenco (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="630b7-134">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="630b7-135">L'app richiede elementi dell'interfaccia utente per l'aggiunta di voci todo all'elenco.</span><span class="sxs-lookup"><span data-stu-id="630b7-135">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="630b7-136">Aggiungere un input di testo (`<input>`) e un pulsante (`<button>`) sotto l'elenco non ordinato (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="630b7-136">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="630b7-137">Arrestare l'app in esecuzione nella shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="630b7-137">Stop the running app in the command shell.</span></span> <span data-ttu-id="630b7-138">Molte shell dei comandi accettano il comando della tastiera <kbd>CTRL</kbd> + <kbd>c</kbd> per arrestare un'app.</span><span class="sxs-lookup"><span data-stu-id="630b7-138">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span> <span data-ttu-id="630b7-139">Ricompilare ed eseguire l'app con il `dotnet run` comando.</span><span class="sxs-lookup"><span data-stu-id="630b7-139">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="630b7-140">Quando il **`Add todo`** pulsante è selezionato, non accade nulla perché un gestore eventi non è cablato fino al pulsante.</span><span class="sxs-lookup"><span data-stu-id="630b7-140">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="630b7-141">Aggiungere un metodo `AddTodo` al componente `Todo` e registrarlo per le selezioni con pulsante con l'attributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="630b7-141">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="630b7-142">Il metodo C# `AddTodo` viene chiamato quando viene selezionato il pulsante:</span><span class="sxs-lookup"><span data-stu-id="630b7-142">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="630b7-143">Per ottenere il titolo del nuovo elemento Todo, aggiungere un campo stringa `newTodo` all'inizio del blocco `@code` e associarlo al valore dell'input di testo tramite l'attributo `bind` dell'elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="630b7-143">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="630b7-144">Aggiornare il metodo `AddTodo` per aggiungere l'elemento `TodoItem` con il titolo specificato all'elenco.</span><span class="sxs-lookup"><span data-stu-id="630b7-144">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="630b7-145">Cancellare il valore dell'input di testo impostando `newTodo` su una stringa vuota:</span><span class="sxs-lookup"><span data-stu-id="630b7-145">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="630b7-146">Arrestare l'app in esecuzione nella shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="630b7-146">Stop the running app in the command shell.</span></span> <span data-ttu-id="630b7-147">Ricompilare ed eseguire l'app con il `dotnet run` comando.</span><span class="sxs-lookup"><span data-stu-id="630b7-147">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="630b7-148">Aggiungere alcune voci todo all'elenco todo per testare il nuovo codice.</span><span class="sxs-lookup"><span data-stu-id="630b7-148">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="630b7-149">Il testo del titolo per ogni elemento Todo può essere reso modificabile e una casella di controllo può consentire all'utente di tenere traccia degli elementi completati.</span><span class="sxs-lookup"><span data-stu-id="630b7-149">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="630b7-150">Aggiungere un input casella di controllo per ogni elemento attività e associarne il valore alla proprietà `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="630b7-150">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="630b7-151">Modificare `@todo.Title` in un elemento `<input>` associato a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="630b7-151">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="630b7-152">Per verificare che questi valori siano associati, aggiornare l'intestazione `<h3>` per visualizzare un conteggio del numero di elementi attività non completati (`IsDone` è `false`).</span><span class="sxs-lookup"><span data-stu-id="630b7-152">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="630b7-153">Componente completato `Todo` ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="630b7-153">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="630b7-154">Arrestare l'app in esecuzione nella shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="630b7-154">Stop the running app in the command shell.</span></span> <span data-ttu-id="630b7-155">Ricompilare ed eseguire l'app con il `dotnet run` comando.</span><span class="sxs-lookup"><span data-stu-id="630b7-155">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="630b7-156">Aggiungere elementi attività per testare il nuovo codice.</span><span class="sxs-lookup"><span data-stu-id="630b7-156">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="630b7-157">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="630b7-157">Next steps</span></span>

<span data-ttu-id="630b7-158">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="630b7-158">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="630b7-159">Creare un progetto di :::no-loc(Blazor)::: app elenco TODO</span><span class="sxs-lookup"><span data-stu-id="630b7-159">Create a todo list :::no-loc(Blazor)::: app project</span></span>
> * <span data-ttu-id="630b7-160">Modificare i :::no-loc(Razor)::: componenti</span><span class="sxs-lookup"><span data-stu-id="630b7-160">Modify :::no-loc(Razor)::: components</span></span>
> * <span data-ttu-id="630b7-161">Usare la gestione degli eventi e data binding nei componenti</span><span class="sxs-lookup"><span data-stu-id="630b7-161">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="630b7-162">Usare il routing in un' :::no-loc(Blazor)::: app</span><span class="sxs-lookup"><span data-stu-id="630b7-162">Use routing in a :::no-loc(Blazor)::: app</span></span>

<span data-ttu-id="630b7-163">Informazioni sugli strumenti per ASP.NET Core :::no-loc(Blazor)::: :</span><span class="sxs-lookup"><span data-stu-id="630b7-163">Learn about tooling for ASP.NET Core :::no-loc(Blazor)::::</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
