---
title: Creare un' Blazor app elenco attività
author: guardrex
description: Creare un' Blazor app dettagliata.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 726380c42c952f47d6fdff09a811f35a20462d96
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944957"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="10ecc-103">Creare un' Blazor app elenco attività</span><span class="sxs-lookup"><span data-stu-id="10ecc-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="10ecc-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="10ecc-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="10ecc-105">Questa esercitazione illustra come creare e modificare un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="10ecc-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="10ecc-106">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="10ecc-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="10ecc-107">Creare un progetto di Blazor app elenco TODO</span><span class="sxs-lookup"><span data-stu-id="10ecc-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="10ecc-108">Modificare i Razor componenti</span><span class="sxs-lookup"><span data-stu-id="10ecc-108">Modify Razor components</span></span>
> * <span data-ttu-id="10ecc-109">Usare la gestione degli eventi e data binding nei componenti</span><span class="sxs-lookup"><span data-stu-id="10ecc-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="10ecc-110">Usare l'inserimento DI dipendenze e il routing in un' Blazor app</span><span class="sxs-lookup"><span data-stu-id="10ecc-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="10ecc-111">Al termine di questa esercitazione, si disporrà di un'app elenco attività.</span><span class="sxs-lookup"><span data-stu-id="10ecc-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

1. <span data-ttu-id="10ecc-112">Creare una nuova Blazor App denominata `TodoList` in una shell comandi:</span><span class="sxs-lookup"><span data-stu-id="10ecc-112">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="10ecc-113">Il comando precedente crea una cartella denominata `TodoList` per ospitare l'app.</span><span class="sxs-lookup"><span data-stu-id="10ecc-113">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="10ecc-114">Passare alla `TodoList` cartella con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="10ecc-114">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="10ecc-115">Aggiungere un nuovo `Todo` Razor componente all'app nella `Pages` cartella usando il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="10ecc-115">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor<span data-ttu-id="10ecc-116">i nomi dei file di componente richiedono una prima lettera maiuscola, quindi verificare che il `Todo` nome del file del componente inizi con una lettera maiuscola `T` .</span><span class="sxs-lookup"><span data-stu-id="10ecc-116"> component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="10ecc-117">In `Pages/Todo.razor` fornire il markup iniziale per il componente:</span><span class="sxs-lookup"><span data-stu-id="10ecc-117">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="10ecc-118">Aggiungere il componente `Todo` alla barra di spostamento.</span><span class="sxs-lookup"><span data-stu-id="10ecc-118">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="10ecc-119">Il `NavMenu` componente ( `Shared/NavMenu.razor` ) viene usato nel layout dell'app.</span><span class="sxs-lookup"><span data-stu-id="10ecc-119">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="10ecc-120">I layout sono componenti che consentono di evitare la duplicazione del contenuto nell'app.</span><span class="sxs-lookup"><span data-stu-id="10ecc-120">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="10ecc-121">Aggiungere un `<NavLink>` elemento per il `Todo` componente aggiungendo il markup dell'elemento di elenco seguente sotto le voci di elenco esistenti nel `Shared/NavMenu.razor` file:</span><span class="sxs-lookup"><span data-stu-id="10ecc-121">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="10ecc-122">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="10ecc-122">Rebuild and run the app.</span></span> <span data-ttu-id="10ecc-123">Visitare la nuova pagina Todo per confermare che il collegamento al componente `Todo` funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="10ecc-123">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="10ecc-124">Aggiungere un `TodoItem.cs` file alla radice del progetto per archiviare una classe che rappresenta un elemento todo.</span><span class="sxs-lookup"><span data-stu-id="10ecc-124">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="10ecc-125">Usare il codice C# seguente per la classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="10ecc-125">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="10ecc-126">Tornare al `Todo` componente ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="10ecc-126">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="10ecc-127">Aggiungere un campo per le voci todo in un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="10ecc-127">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="10ecc-128">Il componente `Todo` usa questo campo per mantenere lo stato dell'elenco attività.</span><span class="sxs-lookup"><span data-stu-id="10ecc-128">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="10ecc-129">Aggiungere il markup per l'elenco non ordinato e un ciclo `foreach` per eseguire il rendering di ogni elemento Todo come elemento di elenco (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="10ecc-129">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="10ecc-130">L'app richiede elementi dell'interfaccia utente per l'aggiunta di voci todo all'elenco.</span><span class="sxs-lookup"><span data-stu-id="10ecc-130">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="10ecc-131">Aggiungere un input di testo (`<input>`) e un pulsante (`<button>`) sotto l'elenco non ordinato (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="10ecc-131">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="10ecc-132">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="10ecc-132">Rebuild and run the app.</span></span> <span data-ttu-id="10ecc-133">Quando il **`Add todo`** pulsante è selezionato, non accade nulla perché un gestore eventi non è cablato fino al pulsante.</span><span class="sxs-lookup"><span data-stu-id="10ecc-133">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="10ecc-134">Aggiungere un metodo `AddTodo` al componente `Todo` e registrarlo per le selezioni con pulsante con l'attributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="10ecc-134">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="10ecc-135">Il metodo C# `AddTodo` viene chiamato quando viene selezionato il pulsante:</span><span class="sxs-lookup"><span data-stu-id="10ecc-135">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="10ecc-136">Per ottenere il titolo del nuovo elemento Todo, aggiungere un campo stringa `newTodo` all'inizio del blocco `@code` e associarlo al valore dell'input di testo tramite l'attributo `bind` dell'elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="10ecc-136">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="10ecc-137">Aggiornare il metodo `AddTodo` per aggiungere l'elemento `TodoItem` con il titolo specificato all'elenco.</span><span class="sxs-lookup"><span data-stu-id="10ecc-137">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="10ecc-138">Cancellare il valore dell'input di testo impostando `newTodo` su una stringa vuota:</span><span class="sxs-lookup"><span data-stu-id="10ecc-138">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="10ecc-139">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="10ecc-139">Rebuild and run the app.</span></span> <span data-ttu-id="10ecc-140">Aggiungere alcune voci todo all'elenco todo per testare il nuovo codice.</span><span class="sxs-lookup"><span data-stu-id="10ecc-140">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="10ecc-141">Il testo del titolo per ogni elemento Todo può essere reso modificabile e una casella di controllo può consentire all'utente di tenere traccia degli elementi completati.</span><span class="sxs-lookup"><span data-stu-id="10ecc-141">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="10ecc-142">Aggiungere un input casella di controllo per ogni elemento attività e associarne il valore alla proprietà `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="10ecc-142">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="10ecc-143">Modificare `@todo.Title` in un elemento `<input>` associato a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="10ecc-143">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="10ecc-144">Per verificare che questi valori siano associati, aggiornare l'intestazione `<h3>` per visualizzare un conteggio del numero di elementi attività non completati (`IsDone` è `false`).</span><span class="sxs-lookup"><span data-stu-id="10ecc-144">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="10ecc-145">Componente completato `Todo` ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="10ecc-145">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="10ecc-146">Ricompilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="10ecc-146">Rebuild and run the app.</span></span> <span data-ttu-id="10ecc-147">Aggiungere elementi attività per testare il nuovo codice.</span><span class="sxs-lookup"><span data-stu-id="10ecc-147">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="10ecc-148">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="10ecc-148">Next steps</span></span>

<span data-ttu-id="10ecc-149">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="10ecc-149">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="10ecc-150">Creare un progetto di Blazor app elenco TODO</span><span class="sxs-lookup"><span data-stu-id="10ecc-150">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="10ecc-151">Modificare i Razor componenti</span><span class="sxs-lookup"><span data-stu-id="10ecc-151">Modify Razor components</span></span>
> * <span data-ttu-id="10ecc-152">Usare la gestione degli eventi e data binding nei componenti</span><span class="sxs-lookup"><span data-stu-id="10ecc-152">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="10ecc-153">Usare l'inserimento DI dipendenze e il routing in un' Blazor app</span><span class="sxs-lookup"><span data-stu-id="10ecc-153">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="10ecc-154">Informazioni sui Blazor modelli di progetto:</span><span class="sxs-lookup"><span data-stu-id="10ecc-154">Learn about the Blazor project templates:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/templates>
