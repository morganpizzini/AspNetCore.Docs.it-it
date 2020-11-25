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

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a>Creare un'app elenco attività Blazor

1. Creare una nuova Blazor App denominata `TodoList` in una shell comandi:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Il comando precedente crea una cartella denominata `TodoList` per ospitare l'app. La `TodoList` cartella è la *cartella radice* del progetto. Passare alla `TodoList` cartella con il comando seguente:

   ```dotnetcli
   cd TodoList
   ```

1. Aggiungere un nuovo `Todo` Razor componente all'app nella `Pages` cartella usando il comando seguente:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor i nomi dei file di componente richiedono una prima lettera maiuscola. Aprire la `Pages` cartella e verificare che il `Todo` nome del file del componente inizi con una lettera maiuscola `T` . Il nome del file deve essere `Todo.razor` .

1. In `Pages/Todo.razor` fornire il markup iniziale per il componente:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   Salvare il file.`Pages/Todo.razor`

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

   Salvare il file.`Shared/NavMenu.razor`

1. Compilare ed eseguire l'app eseguendo il [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) comando nella shell dei comandi dalla `TodoList` cartella. Visitare la nuova pagina todo in `https://localhost:5001/todo` per confermare che il collegamento di navigazione all'intestazione laterale per il `Todo` componente funziona.

1. Aggiungere un `TodoItem.cs` file alla radice del progetto (la `TodoList` cartella) in cui archiviare una classe che rappresenta un elemento todo. Usare il codice C# seguente per la classe `TodoItem`:

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. Tornare al `Todo` componente ( `Pages/Todo.razor` ):

   * Aggiungere un campo per le voci todo in un blocco `@code`. Il componente `Todo` usa questo campo per mantenere lo stato dell'elenco attività.
   * Aggiungere il markup per l'elenco non ordinato e un ciclo `foreach` per eseguire il rendering di ogni elemento Todo come elemento di elenco (`<li>`).

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. L'app richiede elementi dell'interfaccia utente per l'aggiunta di voci todo all'elenco. Aggiungere un input di testo (`<input>`) e un pulsante (`<button>`) sotto l'elenco non ordinato (`<ul>...</ul>`):

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. Salvare il `TodoItem.cs` file e il `Pages/Todo.razor` file aggiornato. Nella shell dei comandi l'app viene ricompilata automaticamente quando i file vengono salvati. Il browser perde temporaneamente la connessione all'app e quindi ricarica la pagina quando viene ristabilita la connessione.

1. Quando il **`Add todo`** pulsante è selezionato, non accade nulla perché un gestore eventi non è associato al pulsante.

1. Aggiungere un metodo `AddTodo` al componente `Todo` e registrarlo per le selezioni con pulsante con l'attributo `@onclick`. Il metodo C# `AddTodo` viene chiamato quando viene selezionato il pulsante:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. Per ottenere il titolo del nuovo elemento Todo, aggiungere un campo stringa `newTodo` all'inizio del blocco `@code` e associarlo al valore dell'input di testo tramite l'attributo `bind` dell'elemento `<input>`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Aggiornare il metodo `AddTodo` per aggiungere l'elemento `TodoItem` con il titolo specificato all'elenco. Cancellare il valore dell'input di testo impostando `newTodo` su una stringa vuota:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. Salvare il file.`Pages/ToDo.razor` L'app viene ricompilata automaticamente nella shell dei comandi. La pagina viene ricaricata nel browser dopo la riconnessione del browser all'app.

1. Il testo del titolo per ogni elemento Todo può essere reso modificabile e una casella di controllo può consentire all'utente di tenere traccia degli elementi completati. Aggiungere un input casella di controllo per ogni elemento attività e associarne il valore alla proprietà `IsDone`. Modificare `@todo.Title` in un elemento `<input>` associato a `@todo.Title`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. Per verificare che questi valori siano associati, aggiornare l'intestazione `<h3>` per visualizzare un conteggio del numero di elementi attività non completati (`IsDone` è `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Componente completato `Todo` ( `Pages/Todo.razor` ):

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. Salvare il file.`Pages/ToDo.razor` L'app viene ricompilata automaticamente nella shell dei comandi. La pagina viene ricaricata nel browser dopo la riconnessione del browser all'app.

1. Aggiungere elementi attività per testare il nuovo codice.

1. Al termine, arrestare l'app nella shell dei comandi. Molte shell dei comandi accettano il comando della tastiera <kbd>CTRL</kbd> + <kbd>c</kbd> per arrestare un'app.

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
