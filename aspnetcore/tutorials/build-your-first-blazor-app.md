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
# <a name="build-your-first-opno-locblazor-app"></a>Crea la Blazor tua prima app

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Questa esercitazione illustra come compilare e modificare un'app. Blazor

## <a name="build-components"></a>Compilare i componenti

1. Seguire le indicazioni <xref:blazor/get-started> contenute Blazor nell'articolo per creare un progetto per questa esercitazione. Denominare il progetto *ToDoList*.

1. Passare a ognuna delle tre pagine dell'app nella cartella *Pagine:* Dati Home, Contatore e Recupera. Queste pagine vengono implementate dai file dei componenti Razor *Index.razor*, *Counter.razor* e *FetchData.razor*.

1. Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina. L'incremento di un contatore in una pagina Web richiede in genere la scrittura di JavaScript. Con Blazor, è invece possibile scrivere C .

1. Esaminare l'implementazione del componente `Counter` nel file *Counter.razor*.

   *Pages/Counter.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   L'interfaccia utente del componente `Counter` viene definita tramite codice HTML. La logica di rendering dinamico (ad esempio, cicli, condizioni, espressioni) viene aggiunta usando una sintassi C# incorporata chiamata [Razor](xref:mvc/views/razor). Il markup HTML e la logica di rendering C# vengono convertiti un una classe del componente in fase di compilazione. Il nome della classe .NET generata corrisponde al nome del file.

   I membri della classe del componente vengono definiti in un blocco `@code`. Nel blocco `@code`, lo stato del componente (proprietà, campi) e i metodi vengono specificati per la gestione degli eventi o per definire la logica di altri componenti. Questi membri vengono quindi usati come parte della logica di rendering del componente e per la gestione degli eventi.

   Quando viene selezionato il pulsante **Click me**:

   * Viene chiamato il gestore `onclick` registrato del componente `Counter` (metodo `IncrementCount`).
   * Il componente `Counter` rigenera il relativo albero di rendering.
   * Il nuovo albero di rendering viene confrontato con quello precedente.
   * Vengono applicate solo le modifiche al modello DOM (Document Object Model). Il conteggio visualizzato viene aggiornato.

1. Modificare la logica C# del componente `Counter` per incrementare il contatore di due unità anziché una.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Ricompilare ed eseguire l'app per visualizzare le modifiche. Selezionare il pulsante **Fare clic qui**. Il contatore viene incrementato di due.

## <a name="use-components"></a>Usare i componenti

Includere un componente in un altro componente usando una sintassi HTML.

1. Aggiungere il componente `Counter` al componente `Index` dell'app aggiungendo un elemento `<Counter />` al componente `Index` (*Index.razor*).

   Se si utilizza Blazor WebAssembly per questa `SurveyPrompt` esperienza, un `Index` componente viene utilizzato dal componente. Sostituire l'elemento `<SurveyPrompt>` con un elemento `<Counter />`. Se si usa Blazor un'app Server per `<Counter />` questa esperienza, aggiungere l'elemento al `Index` componente:

   *Pages/Index.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Ricompilare ed eseguire l'app. Il componente `Index` ha il proprio contatore.

## <a name="component-parameters"></a>Parametri del componente

I componenti possono avere anche parametri, I parametri del componente vengono definiti utilizzando `[Parameter]` le proprietà pubbliche nella classe del componente con l'attributo . Usare gli attributi per specificare gli argomenti per un componente nel markup.

1. Aggiornare il `@code` codice C' del componente come segue:

   * Aggiungere una `IncrementAmount` proprietà `[Parameter]` pubblica con l'attributo .
   * Modificare `IncrementCount` il metodo `IncrementAmount` in modo da `currentCount`utilizzare la proprietà quando si aumenta il valore di .

   *Pages/Counter.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Specificare un parametro `IncrementAmount` nell'elemento `<Counter>` del componente `Index` usando un attributo. Impostare il valore per incrementare il contatore di dieci unità.

   *Pages/Index.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Ricaricare il componente `Index`. Il contatore viene incrementato di dieci unità ogni volta che viene selezionato il pulsante **Click me**. Il contatore nel componente `Counter` continua a essere incrementato di uno.

## <a name="route-to-components"></a>Indirizzare le richieste ai componenti

La direttiva `@page` all'inizio del file *Counter.razor* specifica che il componente `Counter` è un endpoint di routing. Il componente `Counter` gestisce le richieste inviate a `/counter`. Senza la direttiva `@page`, un componente non gestisce le richieste instradate, ma può comunque essere usato da altri componenti.

## <a name="dependency-injection"></a>Inserimento delle dipendenze

### <a name="opno-locblazor-server-experience"></a>BlazorEsperienza server

Se si Blazor utilizza un'app Server, il `WeatherForecastService` servizio viene registrato come [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`. Un'istanza del servizio è disponibile in tutta l'app tramite inserimento delle [dipendenze (DI)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

La `@inject` direttiva viene utilizzata per `WeatherForecastService` inserire `FetchData` l'istanza del servizio nel componente.

*Pages/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Il componente `FetchData` usa il servizio inserito, come `ForecastService`, per recuperare una matrice di oggetti `WeatherForecast`:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="opno-locblazor-webassembly-experience"></a>BlazorEsperienza WebAssembly

Se si Blazor utilizza un'app WebAssembly, `HttpClient` viene inserito l'immissione per ottenere i dati delle previsioni meteo dal file *weather.json* nella cartella *wwwroot/sample-data.*

*Pages/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Un [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo viene utilizzato per eseguire il rendering di ogni istanza di previsione come riga nella tabella dei dati meteo:An loop is used to render each forecast instance as a row in the table of weather data:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Compilare un elenco attività

Aggiungere all'app un nuovo componente che implementa un semplice elenco attività.

1. Aggiungi un `Todo` nuovo componente Razor all'app nella cartella *Pages.* In Visual Studio fare clic con il pulsante destro del mouse sulla cartella **Pages** e scegliere **Aggiungi** > **nuovo** > **componente Razor**elemento . Assegnare al file del componente il nome *Todo.razor*. In altri ambienti di sviluppo, aggiungere un file vuoto alla cartella **Pages** denominata *Todo.razor*.

1. Specificare il markup iniziale per il componente:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Aggiungere il componente `Todo` alla barra di spostamento.

   Il componente `NavMenu` (*Shared/NavMenu.razor*) viene usato nel layout dell'app. I layout sono componenti che consentono di evitare la duplicazione del contenuto nell'app.

   Aggiungere un elemento `<NavLink>` per il componente `Todo` aggiungendo il markup seguente per la voce di elenco sotto le voci di elenco esistenti nel file *Shared/NavMenu.razor*:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Ricompilare ed eseguire l'app. Visitare la nuova pagina Todo per confermare che il collegamento al componente `Todo` funzioni correttamente.

1. Aggiungere un file *TodoItem.cs* nella radice del progetto per contenere una classe per rappresentare un elemento attività. Usare il codice C# seguente per la classe `TodoItem`:

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Tornare al componente `Todo` (*Pages/Todo.razor*):

   * Aggiungere un campo per le voci todo in un blocco `@code`. Il componente `Todo` usa questo campo per mantenere lo stato dell'elenco attività.
   * Aggiungere il markup per l'elenco non ordinato e un ciclo `foreach` per eseguire il rendering di ogni elemento Todo come elemento di elenco (`<li>`).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. L'app richiede elementi dell'interfaccia utente per l'aggiunta di voci todo all'elenco. Aggiungere un input di testo (`<input>`) e un pulsante (`<button>`) sotto l'elenco non ordinato (`<ul>...</ul>`):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Ricompilare ed eseguire l'app. Quando viene selezionato il pulsante **Add todo** non accade nulla perché al pulsante non è associato un gestore eventi.

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

1. Componente `Todo` completato (*Pages/Todo.razor*):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Ricompilare ed eseguire l'app. Aggiungere elementi attività per testare il nuovo codice.

> [!div class="nextstepaction"]
> <xref:blazor/components>
