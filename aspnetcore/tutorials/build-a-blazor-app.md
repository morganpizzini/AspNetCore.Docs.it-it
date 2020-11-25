---
title: Creare un' Blazor app elenco attività
author: guardrex
description: Creare un' Blazor app dettagliata.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: a32655b8afedb73ad436f023d2f821b6920c2edd
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870438"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="c4a76-103">Creare un' Blazor app elenco attività</span><span class="sxs-lookup"><span data-stu-id="c4a76-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="c4a76-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c4a76-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c4a76-105">Questa esercitazione illustra come creare e modificare un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="c4a76-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="c4a76-106">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="c4a76-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c4a76-107">Creare un progetto di Blazor app elenco TODO</span><span class="sxs-lookup"><span data-stu-id="c4a76-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="c4a76-108">Modificare i Razor componenti</span><span class="sxs-lookup"><span data-stu-id="c4a76-108">Modify Razor components</span></span>
> * <span data-ttu-id="c4a76-109">Usare la gestione degli eventi e data binding nei componenti</span><span class="sxs-lookup"><span data-stu-id="c4a76-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="c4a76-110">Usare il routing in un' Blazor app</span><span class="sxs-lookup"><span data-stu-id="c4a76-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="c4a76-111">Al termine di questa esercitazione, si disporrà di un'app elenco attività.</span><span class="sxs-lookup"><span data-stu-id="c4a76-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c4a76-112">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="c4a76-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="c4a76-113">Creare un'app elenco attività Blazor</span><span class="sxs-lookup"><span data-stu-id="c4a76-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="c4a76-114">Creare una nuova Blazor App denominata `TodoList` in una shell comandi:</span><span class="sxs-lookup"><span data-stu-id="c4a76-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="c4a76-115">Il comando precedente crea una cartella denominata `TodoList` per ospitare l'app.</span><span class="sxs-lookup"><span data-stu-id="c4a76-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="c4a76-116">La `TodoList` cartella è la *cartella radice* del progetto.</span><span class="sxs-lookup"><span data-stu-id="c4a76-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="c4a76-117">Passare alla `TodoList` cartella con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c4a76-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="c4a76-118">Aggiungere un nuovo `Todo` Razor componente all'app nella `Pages` cartella usando il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c4a76-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="c4a76-119">Razor i nomi dei file di componente richiedono una prima lettera maiuscola.</span><span class="sxs-lookup"><span data-stu-id="c4a76-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="c4a76-120">Aprire la `Pages` cartella e verificare che il `Todo` nome del file del componente inizi con una lettera maiuscola `T` .</span><span class="sxs-lookup"><span data-stu-id="c4a76-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="c4a76-121">Il nome del file deve essere `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="c4a76-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="c4a76-122">In `Pages/Todo.razor` fornire il markup iniziale per il componente:</span><span class="sxs-lookup"><span data-stu-id="c4a76-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   <span data-ttu-id="c4a76-123">Salvare il file.`Pages/Todo.razor`</span><span class="sxs-lookup"><span data-stu-id="c4a76-123">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="c4a76-124">Aggiungere il componente `Todo` alla barra di spostamento.</span><span class="sxs-lookup"><span data-stu-id="c4a76-124">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="c4a76-125">Il `NavMenu` componente ( `Shared/NavMenu.razor` ) viene usato nel layout dell'app.</span><span class="sxs-lookup"><span data-stu-id="c4a76-125">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="c4a76-126">I layout sono componenti che consentono di evitare la duplicazione del contenuto nell'app.</span><span class="sxs-lookup"><span data-stu-id="c4a76-126">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="c4a76-127">Aggiungere un `<NavLink>` elemento per il `Todo` componente aggiungendo il markup dell'elemento di elenco seguente sotto le voci di elenco esistenti nel `Shared/NavMenu.razor` file:</span><span class="sxs-lookup"><span data-stu-id="c4a76-127">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

   <span data-ttu-id="c4a76-128">Salvare il file.`Shared/NavMenu.razor`</span><span class="sxs-lookup"><span data-stu-id="c4a76-128">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="c4a76-129">Compilare ed eseguire l'app eseguendo il [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) comando nella shell dei comandi dalla `TodoList` cartella.</span><span class="sxs-lookup"><span data-stu-id="c4a76-129">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="c4a76-130">Visitare la nuova pagina todo in `https://localhost:5001/todo` per confermare che il collegamento di navigazione all'intestazione laterale per il `Todo` componente funziona.</span><span class="sxs-lookup"><span data-stu-id="c4a76-130">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the sidebar navigation link to the `Todo` component works.</span></span>

1. <span data-ttu-id="c4a76-131">Aggiungere un `TodoItem.cs` file alla radice del progetto (la `TodoList` cartella) in cui archiviare una classe che rappresenta un elemento todo.</span><span class="sxs-lookup"><span data-stu-id="c4a76-131">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="c4a76-132">Usare il codice C# seguente per la classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="c4a76-132">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="c4a76-133">Tornare al `Todo` componente ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="c4a76-133">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="c4a76-134">Aggiungere un campo per le voci todo in un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="c4a76-134">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="c4a76-135">Il componente `Todo` usa questo campo per mantenere lo stato dell'elenco attività.</span><span class="sxs-lookup"><span data-stu-id="c4a76-135">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="c4a76-136">Aggiungere il markup per l'elenco non ordinato e un ciclo `foreach` per eseguire il rendering di ogni elemento Todo come elemento di elenco (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="c4a76-136">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="c4a76-137">L'app richiede elementi dell'interfaccia utente per l'aggiunta di voci todo all'elenco.</span><span class="sxs-lookup"><span data-stu-id="c4a76-137">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="c4a76-138">Aggiungere un input di testo (`<input>`) e un pulsante (`<button>`) sotto l'elenco non ordinato (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="c4a76-138">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="c4a76-139">Salvare il `TodoItem.cs` file e il `Pages/Todo.razor` file aggiornato.</span><span class="sxs-lookup"><span data-stu-id="c4a76-139">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="c4a76-140">Nella shell dei comandi l'app viene ricompilata automaticamente quando i file vengono salvati.</span><span class="sxs-lookup"><span data-stu-id="c4a76-140">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="c4a76-141">Il browser perde temporaneamente la connessione all'app e quindi ricarica la pagina quando viene ristabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="c4a76-141">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="c4a76-142">Quando il **`Add todo`** pulsante è selezionato, non accade nulla perché un gestore eventi non è associato al pulsante.</span><span class="sxs-lookup"><span data-stu-id="c4a76-142">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="c4a76-143">Aggiungere un metodo `AddTodo` al componente `Todo` e registrarlo per le selezioni con pulsante con l'attributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="c4a76-143">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="c4a76-144">Il metodo C# `AddTodo` viene chiamato quando viene selezionato il pulsante:</span><span class="sxs-lookup"><span data-stu-id="c4a76-144">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="c4a76-145">Per ottenere il titolo del nuovo elemento Todo, aggiungere un campo stringa `newTodo` all'inizio del blocco `@code` e associarlo al valore dell'input di testo tramite l'attributo `bind` dell'elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="c4a76-145">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="c4a76-146">Aggiornare il metodo `AddTodo` per aggiungere l'elemento `TodoItem` con il titolo specificato all'elenco.</span><span class="sxs-lookup"><span data-stu-id="c4a76-146">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="c4a76-147">Cancellare il valore dell'input di testo impostando `newTodo` su una stringa vuota:</span><span class="sxs-lookup"><span data-stu-id="c4a76-147">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="c4a76-148">Salvare il file.`Pages/ToDo.razor`</span><span class="sxs-lookup"><span data-stu-id="c4a76-148">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="c4a76-149">L'app viene ricompilata automaticamente nella shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="c4a76-149">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="c4a76-150">La pagina viene ricaricata nel browser dopo la riconnessione del browser all'app.</span><span class="sxs-lookup"><span data-stu-id="c4a76-150">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="c4a76-151">Il testo del titolo per ogni elemento Todo può essere reso modificabile e una casella di controllo può consentire all'utente di tenere traccia degli elementi completati.</span><span class="sxs-lookup"><span data-stu-id="c4a76-151">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="c4a76-152">Aggiungere un input casella di controllo per ogni elemento attività e associarne il valore alla proprietà `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="c4a76-152">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="c4a76-153">Modificare `@todo.Title` in un elemento `<input>` associato a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="c4a76-153">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="c4a76-154">Per verificare che questi valori siano associati, aggiornare l'intestazione `<h3>` per visualizzare un conteggio del numero di elementi attività non completati (`IsDone` è `false`).</span><span class="sxs-lookup"><span data-stu-id="c4a76-154">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="c4a76-155">Componente completato `Todo` ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="c4a76-155">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="c4a76-156">Salvare il file.`Pages/ToDo.razor`</span><span class="sxs-lookup"><span data-stu-id="c4a76-156">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="c4a76-157">L'app viene ricompilata automaticamente nella shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="c4a76-157">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="c4a76-158">La pagina viene ricaricata nel browser dopo la riconnessione del browser all'app.</span><span class="sxs-lookup"><span data-stu-id="c4a76-158">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="c4a76-159">Aggiungere elementi attività per testare il nuovo codice.</span><span class="sxs-lookup"><span data-stu-id="c4a76-159">Add todo items to test the new code.</span></span>

1. <span data-ttu-id="c4a76-160">Al termine, arrestare l'app nella shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="c4a76-160">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="c4a76-161">Molte shell dei comandi accettano il comando della tastiera <kbd>CTRL</kbd> + <kbd>c</kbd> per arrestare un'app.</span><span class="sxs-lookup"><span data-stu-id="c4a76-161">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c4a76-162">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="c4a76-162">Next steps</span></span>

<span data-ttu-id="c4a76-163">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="c4a76-163">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c4a76-164">Creare un progetto di Blazor app elenco TODO</span><span class="sxs-lookup"><span data-stu-id="c4a76-164">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="c4a76-165">Modificare i Razor componenti</span><span class="sxs-lookup"><span data-stu-id="c4a76-165">Modify Razor components</span></span>
> * <span data-ttu-id="c4a76-166">Usare la gestione degli eventi e data binding nei componenti</span><span class="sxs-lookup"><span data-stu-id="c4a76-166">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="c4a76-167">Usare il routing in un' Blazor app</span><span class="sxs-lookup"><span data-stu-id="c4a76-167">Use routing in a Blazor app</span></span>

<span data-ttu-id="c4a76-168">Informazioni sugli strumenti per ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="c4a76-168">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
