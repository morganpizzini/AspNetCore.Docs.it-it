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
ms.openlocfilehash: 892663a533a207df84b0fce9af259a7dc212bc9b
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292776"
---
# <a name="build-your-first-blazor-app"></a>Crea la tua prima Blazor app

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Questa esercitazione illustra come creare e modificare un' Blazor app. Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto di Blazor app elenco TODO
> * Modificare i Razor componenti
> * Usare la gestione degli eventi e data binding nei componenti
> * Usare l'inserimento DI dipendenze e il routing in un' Blazor app

Al termine di questa esercitazione, si disporrà di un'app elenco attività.

## <a name="build-components"></a>Compilare i componenti

1. <xref:blazor/get-started>Per creare un Blazor progetto per questa esercitazione, seguire le istruzioni riportate nell'articolo. Assegnare al progetto il nome `ToDoList`.

1. Passare a ognuna delle tre pagine dell'app nella `Pages` cartella: `Home` , `Counter` e `Fetch data` . Queste pagine sono implementate dai Razor file dei componenti `Index.razor` , `Counter.razor` e `FetchData.razor` .

1. Nella `Counter` pagina selezionare il pulsante per incrementare il contatore senza un aggiornamento della pagina. Per incrementare un contatore in una pagina Web è in genere necessario scrivere JavaScript. Con Blazor è invece possibile scrivere in C#.

1. Esaminare l'implementazione del `Counter` componente nel `Counter.razor` file.

   `Pages/Counter.razor`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   L'interfaccia utente del componente `Counter` viene definita tramite codice HTML. La logica di rendering dinamica (ad esempio, cicli, condizionali, espressioni) viene aggiunta usando una sintassi C# incorporata denominata [Razor](xref:mvc/views/razor) . Il markup HTML e la logica di rendering C# vengono convertiti un una classe del componente in fase di compilazione. Il nome della classe .NET generata corrisponde al nome del file.

   I membri della classe del componente vengono definiti in un blocco `@code`. Nel blocco `@code`, lo stato del componente (proprietà, campi) e i metodi vengono specificati per la gestione degli eventi o per definire la logica di altri componenti. Questi membri vengono quindi usati come parte della logica di rendering del componente e per la gestione degli eventi.

   Quando viene selezionato il pulsante incremento contatore:

   * Viene chiamato il gestore `onclick` registrato del componente `Counter` (metodo `IncrementCount`).
   * Il componente `Counter` rigenera il relativo albero di rendering.
   * Il nuovo albero di rendering viene confrontato con quello precedente.
   * Vengono applicate solo le modifiche al modello DOM (Document Object Model). Il conteggio visualizzato viene aggiornato.

1. Modificare la logica C# del componente `Counter` per incrementare il contatore di due unità anziché una.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Ricompilare ed eseguire l'app per visualizzare le modifiche. Selezionare il pulsante. Il contatore viene incrementato di due.

## <a name="use-components"></a>Usare i componenti

Includere un componente in un altro componente usando una sintassi HTML.

1. Aggiungere il `Counter` componente al componente dell'app `Index` aggiungendo un `<Counter />` elemento al `Index` componente ( `Index.razor` ).

   Se si usa Blazor webassembly per questa esperienza, un `SurveyPrompt` componente viene utilizzato dal `Index` componente. Sostituire l'elemento `<SurveyPrompt>` con un elemento `<Counter />`. Se si usa un' Blazor app Server per questa esperienza, aggiungere l' `<Counter />` elemento al `Index` componente:

   `Pages/Index.razor`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Ricompilare ed eseguire l'app. Il componente `Index` ha il proprio contatore.

## <a name="component-parameters"></a>Parametri del componente

I componenti possono avere anche parametri, I parametri del componente vengono definiti usando proprietà pubbliche nella classe Component con l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo. Usare gli attributi per specificare gli argomenti per un componente nel markup.

1. Aggiornare il `@code` codice C# del componente come segue:

   * Aggiungere una `IncrementAmount` proprietà pubblica con l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo.
   * Modificare il `IncrementCount` metodo in modo che usi la `IncrementAmount` proprietà quando si aumenta il valore di `currentCount` .

   `Pages/Counter.razor`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Specificare un parametro `IncrementAmount` nell'elemento `<Counter>` del componente `Index` usando un attributo. Impostare il valore per incrementare il contatore di dieci unità.

   `Pages/Index.razor`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Ricaricare il componente `Index`. Il contatore viene incrementato di dieci volte a ogni selezione del pulsante. Il contatore nel componente `Counter` continua a essere incrementato di uno.

## <a name="route-to-components"></a>Indirizzare le richieste ai componenti

La `@page` direttiva all'inizio del `Counter.razor` file specifica che il `Counter` componente è un endpoint di routing. Il componente `Counter` gestisce le richieste inviate a `/counter`. Senza la direttiva `@page`, un componente non gestisce le richieste instradate, ma può comunque essere usato da altri componenti.

## <a name="dependency-injection"></a>Inserimento di dipendenze

### <a name="blazor-server-experience"></a>BlazorEsperienza server

Se si utilizza un' Blazor app Server, il `WeatherForecastService` servizio viene registrato come [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices` . Un'istanza del servizio è disponibile in tutte le app tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

La [`@inject`](xref:mvc/views/razor#inject) direttiva viene utilizzata per inserire l'istanza del `WeatherForecastService` servizio nel `FetchData` componente.

`Pages/FetchData.razor`:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Il componente `FetchData` usa il servizio inserito, come `ForecastService`, per recuperare una matrice di oggetti `WeatherForecast`:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>BlazorEsperienza webassembly

Se si usa un' Blazor app webassembly, <xref:System.Net.Http.HttpClient> viene inserito per ottenere i dati delle previsioni meteo dal `weather.json` file nella `wwwroot/sample-data` cartella.

`Pages/FetchData.razor`:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

Un [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo viene usato per eseguire il rendering di ogni istanza di previsione come riga nella tabella dei dati meteorologici:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Compilare un elenco attività

Aggiungere all'app un nuovo componente che implementa un semplice elenco attività.

1. Aggiungere un nuovo `Todo` Razor componente all'app nella `Pages` cartella. Se si usa Visual Studio, fare clic con il pulsante destro del mouse sulla `Pages` cartella e scegliere **Aggiungi**  >  **nuovo elemento**  >  ** Razor componente**. Assegnare un nome al file del componente `Todo.razor` . In altri ambienti di sviluppo, aggiungere un file vuoto alla `Pages` cartella denominata `Todo.razor` . Razori nomi dei file di componente richiedono una prima lettera maiuscola, quindi verificare che il `Todo` nome del file del componente inizi con una lettera maiuscola `T` .

1. Specificare il markup iniziale per il componente:

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

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Tornare al `Todo` componente ( `Pages/Todo.razor` ):

   * Aggiungere un campo per le voci todo in un blocco `@code`. Il componente `Todo` usa questo campo per mantenere lo stato dell'elenco attività.
   * Aggiungere il markup per l'elenco non ordinato e un ciclo `foreach` per eseguire il rendering di ogni elemento Todo come elemento di elenco (`<li>`).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. L'app richiede elementi dell'interfaccia utente per l'aggiunta di voci todo all'elenco. Aggiungere un input di testo (`<input>`) e un pulsante (`<button>`) sotto l'elenco non ordinato (`<ul>...</ul>`):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Ricompilare ed eseguire l'app. Quando il **`Add todo`** pulsante è selezionato, non accade nulla perché un gestore eventi non è cablato fino al pulsante.

1. Aggiungere un metodo `AddTodo` al componente `Todo` e registrarlo per le selezioni con pulsante con l'attributo `@onclick`. Il metodo C# `AddTodo` viene chiamato quando viene selezionato il pulsante:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Per ottenere il titolo del nuovo elemento Todo, aggiungere un campo stringa `newTodo` all'inizio del blocco `@code` e associarlo al valore dell'input di testo tramite l'attributo `bind` dell'elemento `<input>`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Aggiornare il metodo `AddTodo` per aggiungere l'elemento `TodoItem` con il titolo specificato all'elenco. Cancellare il valore dell'input di testo impostando `newTodo` su una stringa vuota:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Ricompilare ed eseguire l'app. Aggiungere alcune voci todo all'elenco todo per testare il nuovo codice.

1. Il testo del titolo per ogni elemento Todo può essere reso modificabile e una casella di controllo può consentire all'utente di tenere traccia degli elementi completati. Aggiungere un input casella di controllo per ogni elemento attività e associarne il valore alla proprietà `IsDone`. Modificare `@todo.Title` in un elemento `<input>` associato a `@todo.Title`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Per verificare che questi valori siano associati, aggiornare l'intestazione `<h3>` per visualizzare un conteggio del numero di elementi attività non completati (`IsDone` è `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Componente completato `Todo` ( `Pages/Todo.razor` ):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Ricompilare ed eseguire l'app. Aggiungere elementi attività per testare il nuovo codice.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un progetto di Blazor app elenco TODO
> * Modificare i Razor componenti
> * Usare la gestione degli eventi e data binding nei componenti
> * Usare l'inserimento DI dipendenze e il routing in un' Blazor app

Informazioni su come creare e usare i componenti:

> [!div class="nextstepaction"]
> <xref:blazor/components/index>
