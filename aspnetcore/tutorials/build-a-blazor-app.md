---
title: Creare un' Blazor app elenco attività
author: guardrex
description: Creare un' Blazor app dettagliata.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/30/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 42c4f94869586bfcd4e8b27e4bdf4ef226094072
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022498"
---
# <a name="build-a-no-locblazor-todo-list-app"></a>Creare un' Blazor app elenco attività

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Questa esercitazione illustra come creare e modificare un' Blazor app. Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto di Blazor app elenco TODO
> * Modificare i Razor componenti
> * Usare la gestione degli eventi e data binding nei componenti
> * Usare il routing in un' Blazor app

Al termine di questa esercitazione, si disporrà di un'app elenco attività.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-todo-list-no-locblazor-app"></a>Creare un'app elenco attività Blazor

1. Creare una nuova Blazor App denominata `TodoList` in una shell comandi:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Il comando precedente crea una cartella denominata `TodoList` per ospitare l'app. Passare alla `TodoList` cartella con il comando seguente:

   ```dotnetcli
   cd TodoList
   ```

1. Aggiungere un nuovo `Todo` Razor componente all'app nella `Pages` cartella usando il comando seguente:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razori nomi dei file di componente richiedono una prima lettera maiuscola, quindi verificare che il `Todo` nome del file del componente inizi con una lettera maiuscola `T` .

1. In `Pages/Todo.razor` fornire il markup iniziale per il componente:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Aggiungere il componente `Todo` alla barra di spostamento.

   Il `NavMenu` componente ( `Shared/NavMenu.razor` ) viene usato nel layout dell'app. I layout sono componenti che consentono di evitare la duplicazione del contenuto nell'app.

   Aggiungere un `<NavLink>` elemento per il `Todo` componente aggiungendo il markup dell'elemento di elenco seguente sotto le voci di elenco esistenti nel `Shared/NavMenu.razor` file:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Ricompilare ed eseguire l'app. Visitare la nuova pagina Todo per confermare che il collegamento al componente `Todo` funzioni correttamente.

1. Aggiungere un `TodoItem.cs` file alla radice del progetto per archiviare una classe che rappresenta un elemento todo. Usare il codice C# seguente per la classe `TodoItem`:

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Tornare al `Todo` componente ( `Pages/Todo.razor` ):

   * Aggiungere un campo per le voci todo in un blocco `@code`. Il componente `Todo` usa questo campo per mantenere lo stato dell'elenco attività.
   * Aggiungere il markup per l'elenco non ordinato e un ciclo `foreach` per eseguire il rendering di ogni elemento Todo come elemento di elenco (`<li>`).

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. L'app richiede elementi dell'interfaccia utente per l'aggiunta di voci todo all'elenco. Aggiungere un input di testo (`<input>`) e un pulsante (`<button>`) sotto l'elenco non ordinato (`<ul>...</ul>`):

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Ricompilare ed eseguire l'app. Quando il **`Add todo`** pulsante è selezionato, non accade nulla perché un gestore eventi non è cablato fino al pulsante.

1. Aggiungere un metodo `AddTodo` al componente `Todo` e registrarlo per le selezioni con pulsante con l'attributo `@onclick`. Il metodo C# `AddTodo` viene chiamato quando viene selezionato il pulsante:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Per ottenere il titolo del nuovo elemento Todo, aggiungere un campo stringa `newTodo` all'inizio del blocco `@code` e associarlo al valore dell'input di testo tramite l'attributo `bind` dell'elemento `<input>`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Aggiornare il metodo `AddTodo` per aggiungere l'elemento `TodoItem` con il titolo specificato all'elenco. Cancellare il valore dell'input di testo impostando `newTodo` su una stringa vuota:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Ricompilare ed eseguire l'app. Aggiungere alcune voci todo all'elenco todo per testare il nuovo codice.

1. Il testo del titolo per ogni elemento Todo può essere reso modificabile e una casella di controllo può consentire all'utente di tenere traccia degli elementi completati. Aggiungere un input casella di controllo per ogni elemento attività e associarne il valore alla proprietà `IsDone`. Modificare `@todo.Title` in un elemento `<input>` associato a `@todo.Title`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Per verificare che questi valori siano associati, aggiornare l'intestazione `<h3>` per visualizzare un conteggio del numero di elementi attività non completati (`IsDone` è `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Componente completato `Todo` ( `Pages/Todo.razor` ):

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Ricompilare ed eseguire l'app. Aggiungere elementi attività per testare il nuovo codice.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un progetto di Blazor app elenco TODO
> * Modificare i Razor componenti
> * Usare la gestione degli eventi e data binding nei componenti
> * Usare il routing in un' Blazor app

Informazioni sugli strumenti per ASP.NET Core Blazor :

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
