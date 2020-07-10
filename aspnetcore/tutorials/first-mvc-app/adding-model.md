---
title: "Parte 4: aggiungere un modello a un'app MVC ASP.NET Core"
author: rick-anderson
description: Parte 4 della serie di esercitazioni su ASP.NET Core MVC.
ms.author: riande
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 77c12b98462c8a84ab807abef247573b6a028fcc
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213165"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a>Parte 4: aggiungere un modello a un'app MVC ASP.NET Core

Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Tom Dykstra](https://github.com/tdykstra)

In questa sezione si aggiungono alcune classi per la gestione di filmati in un database. Queste classi saranno la parte "**M**odel" dell'app **M**VC.

Si usano queste classi con [Entity Framework Core](/ef/core) (EF Core) per usare un database. EF Core è un framework object-relational mapping (ORM) che semplifica il codice di accesso ai dati che è necessario scrivere.

Le classi di modello create sono dette classi POCO (da **P**lain **O**ld **C**LR **O**bjects) perché non hanno alcuna dipendenza in EF Core. Definiscono semplicemente le proprietà dei dati che verranno archiviati nel database.

In questa esercitazione si scrivono innanzitutto le classi di modello e EF Core crea il database.

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Aggiungere una classe del modello di dati

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Fare clic con il pulsante destro del mouse sulla cartella *Models* > **Aggiungi** > **Classe**. Denominare il file *Movie.cs*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aggiungere un file denominato *Movie.cs* alla cartella *Models*.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Fare clic con il pulsante destro del mouse sulla cartella *Models* > **Aggiungi**  >  **nuova classe**  >  **vuota**classe. Denominare il file *Movie.cs*.

---

Aggiornare il file *Movie.cs* con il codice seguente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

La classe `Movie` contiene un campo `Id`, richiesto dal database per la chiave primaria.

L' <xref:System.ComponentModel.DataAnnotations.DataType> attributo on `ReleaseDate` specifica il tipo di dati ( `Date` ). Con questo attributo:

* l'utente non deve immettere le informazioni temporali nel campo della data.
* Viene visualizzata solo la data, non le informazioni temporali.

L'attributo [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) viene analizzato in un'esercitazione successiva.

## <a name="add-nuget-packages"></a>Aggiungere pacchetti NuGet

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dal menu **Strumenti** scegliere **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.

![Menu della Console di Gestione pacchetti](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

Nella console di Gestione pacchetti eseguire il comando seguente:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

Il comando precedente aggiunge il provider EF Core SQL Server. Il pacchetto del provider installa il pacchetto di EF Core come dipendenza. I pacchetti aggiuntivi vengono installati automaticamente nel passaggio di scaffolding più avanti nell'esercitazione.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Scegliere **Gestisci pacchetti NuGet**dal menu **progetto** .

Nel campo di **ricerca** in alto a destra, immettere `Microsoft.EntityFrameworkCore.SQLite` e premere il tasto **invio** per eseguire la ricerca. Selezionare il pacchetto NuGet corrispondente e premere il pulsante **Aggiungi pacchetto** .

![Aggiungi Entity Framework Core pacchetto NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

Verrà visualizzata la finestra di dialogo **Seleziona progetti** con il `MvcMovie` progetto selezionato. Premere il pulsante **OK** .

Verrà visualizzata una finestra di dialogo **accettazione licenza** . Esaminare le licenze nel modo desiderato, quindi fare clic sul pulsante **Accetto** .

Ripetere i passaggi precedenti per installare i pacchetti NuGet seguenti:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Creare una classe di contesto di database

Una classe di contesto di database è necessaria per coordinare le funzionalità di EF Core (creazione, lettura, aggiornamento, eliminazione) per il modello `Movie`. Il contesto di database viene derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e specifica le entità da includere nel modello di dati.

Creare una cartella *Data*.

Aggiungere un file *Data/MvcMovieContext.cs* con il codice seguente: 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità. Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database. Un'entità corrisponde a una riga nella tabella.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Registrare il contesto del database

ASP.NET Core viene compilato con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection). I servizi, ad esempio il contesto di database di EF Core, devono essere registrati per l'inserimento delle dipendenze durante l'avvio dell'applicazione. I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore. Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database. In questa sezione viene registrato il contesto di database per il contenitore di inserimento delle dipendenze.

Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs*:

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Aggiungere il codice evidenziato seguente in `Startup.ConfigureServices`:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Aggiungere una stringa di connessione del database

Aggiungere una stringa di connessione al *appsettings.jsnel* file:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Compilare il progetto per controllare se sono presenti errori del compilatore.

## <a name="scaffold-movie-pages"></a>Eseguire lo scaffolding delle pagine Movie

Usare lo strumento di scaffolding per generare le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello Movie.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Controller***> Aggiungi > Nuovo elemento di scaffolding**.

![vista del passaggio precedente](adding-model/_static/add_controller21.png)

Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Controller MVC con visualizzazioni, che usa Entity Framework**.

![Finestra di dialogo Aggiungi scaffolding](adding-model/_static/add_scaffold21.png)

Completare la finestra di dialogo **Aggiungi controller**:

* **Classe di modello:** *Movie (MvcMovie.Models)*
* **Classe del contesto dati:** *MvcMovieContext (MvcMovie. Data)*

![Aggiungere il contesto dati](adding-model/_static/dc3.png)

* **Viste:** mantenere il valore predefinito di ogni opzione selezionata
* **Nome del controller:** mantenere il valore predefinito *MoviesController*
* Selezionare **Aggiungi**

Visual Studio crea:

* Un controller di film (*Controllers/MoviesController.cs*)
* RazorVisualizza file per le pagine create, DELETE, Details, Edit e index (*views/Movies/ \* . cshtml*)

La creazione automatica di questi file è nota come *scaffolding*.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).

* In Linux esportare il percorso dello strumento di scaffolding:

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Eseguire il comando seguente:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).

* Eseguire il comando seguente:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Non è possibile usare ancora le pagine sottoposte a scaffolding perché il database non esiste. Se si esegue l'app e si fa clic sul collegamento dell' **app Movie** , viene visualizzato un messaggio di errore *non è possibile aprire il database* o una *tabella di questo tipo:* il messaggio di errore Movie.

<a name="migration"></a>

## <a name="initial-migration"></a>Migrazione iniziale

Usare la funzionalità [Migrazioni](xref:data/ef-mvc/migrations) di EF Core per creare il database. Migrazioni è un set di strumenti che consentono di creare e aggiornare un database in modo che corrisponda al modello di dati.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dal menu **Strumenti** scegliere **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.

Nella Console di Gestione pacchetti immettere i comandi seguenti:

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: Genera un file di migrazione *Migrations/{timestamp} _InitialCreate. cs* . L'argomento `InitialCreate` è il nome della migrazione. È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione. Trattandosi della prima migrazione, la classe generata contiene il codice per creare lo schema del database. Lo schema del database si basa sul modello specificato nella classe `MvcMovieContext`.

* `Update-Database`: Aggiorna il database alla migrazione più recente, che ha creato il comando precedente. Il comando esegue il metodo `Up` nel file *Migrations/{time-stamp}_InitialCreate.cs*, che crea il database.

  Il comando database update genera l'avviso seguente: 

  > No type was specified for the decimal column 'Price' on entity type 'Movie'. (Nessun tipo specificato per la colonna decimale 'Price' nel tipo di entità 'Movie'). This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite). Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()').

  È possibile ignorare tale avviso. Verrà risolto in un'esercitazione successiva.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: Genera un file di migrazione *Migrations/{timestamp} _InitialCreate. cs* . L'argomento `InitialCreate` è il nome della migrazione. È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione. Trattandosi della prima migrazione, la classe generata contiene il codice per creare lo schema del database. Lo schema del database è basato sul modello specificato nella classe `MvcMovieContext` (nel file *Data/MvcMovieContext.cs*).

* `ef database update`: Aggiorna il database alla migrazione più recente, che ha creato il comando precedente. Il comando esegue il metodo `Up` nel file *Migrations/{time-stamp}_InitialCreate.cs*, che crea il database.

---

### <a name="the-initialcreate-class"></a>Classe InitialCreate

Esaminare il file di migrazione *Migrations/{timestamp}_InitialCreate.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

Il metodo `Up` crea la tabella Movie e configura `Id` come chiave primaria. Il metodo `Down` annulla le modifiche dello schema apportate dalla migrazione `Up`.

<a name="test"></a>

## <a name="test-the-app"></a>Testare l'app

* Eseguire l'app e fare clic sul collegamento **Movie App**.

  Se si ottiene un'eccezione simile a una delle seguenti:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Probabilmente non è stato eseguito il [passaggio delle migrazioni](#migration).

* Testare la pagina **create** . Immettere e inviare i dati.

  > [!NOTE]
  > Potrebbe non essere possibile immettere virgole decimali nel campo `Price`. Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app. Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testare le pagine **Edit**, **Details** e **Delete**.

## <a name="dependency-injection-in-the-controller"></a>Inserimento delle dipendenze nel controller

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aprire il file *Controllers/MoviesController.cs* ed esaminare il costruttore:

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Il costruttore usa l'[inserimento dipendenze](xref:fundamentals/dependency-injection) per inserire il contesto del database (`MvcMovieContext`) nel controller. Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Il costruttore usa l'[inserimento dipendenze](xref:fundamentals/dependency-injection) per inserire il contesto del database (`MvcMovieContext`) nel controller. Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Usare SQLite per lo sviluppo, SQL Server per la produzione

Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo. Il codice seguente illustra come inserire all' <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> avvio. `IWebHostEnvironment`viene inserito in modo `ConfigureServices` che possa usare SQLite per lo sviluppo e la SQL Server nell'ambiente di produzione.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modelli fortemente tipizzati e parola chiave @model

In un passaggio precedente di questa esercitazione è stato esaminato come un controller può passare oggetti o dati a una vista usando il dizionario `ViewData`. Il dizionario `ViewData` è un oggetto dinamico che fornisce un modo pratico ad associazione tardiva per passare informazioni a una vista.

MVC consente anche di passare oggetti modello fortemente tipizzati a una vista. Questo approccio fortemente tipizzato consente il controllo del codice in fase di compilazione. Con il meccanismo di scaffolding è stato usato questo approccio, ovvero il passaggio di un modello fortemente tipizzato, con le viste e la classe `MoviesController`.

Aprire il metodo `Details` nel file *Controllers/MoviesController.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

In genere il parametro `id` viene passato come dati di route. Ad esempio `https://localhost:5001/movies/details/1` imposta:

* Il controller sul controller `movies` (primo segmento di URL).
* L'azione su `details` (secondo segmento di URL).
* L'ID su 1 (ultimo segmento di URL).

È possibile anche passare `id` con una stringa di query nel modo seguente:

`https://localhost:5001/movies/details?id=1`

Il `id` parametro viene definito come [tipo Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) nel caso in cui non venga fornito un valore ID.

Un'[espressione lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) viene passata a `FirstOrDefaultAsync` per selezionare le entità film che corrispondono al valore della stringa di query o dei dati di route.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Se viene trovato un film, viene passata un'istanza del modello `Movie` alla vista `Details`:

```csharp
return View(movie);
```

Esaminare il contenuto del file *Views/Movies/Details.cshtml*:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

L'istruzione `@model` all'inizio del file di vista specifica il tipo di oggetto previsto dalla vista. Quando è stato creato il controller dei film, è stata inclusa l'istruzione `@model` seguente:

```cshtml
@model MvcMovie.Models.Movie
```

Questa direttiva `@model` consente di accedere al film che il controller ha passato alla vista. L'oggetto `Model` è fortemente tipizzato. Ad esempio, nella vista *Details.cshtml* il codice passa ogni campo di film agli helper HTML `DisplayNameFor` e `DisplayFor` con l'oggetto `Model` fortemente tipizzato. Le viste e i metodi `Create` e `Edit` passano anche un oggetto modello `Movie`.

Esaminare la vista *Index.cshtml* e il metodo `Index` nel controller Movies. Si noti che il codice crea un oggetto `List` quando chiama il metodo `View`. Il codice passa questo elenco `Movies` dal metodo di azione `Index` alla vista:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Al momento della creazione del controller di film, lo scaffolding ha incluso l'istruzione `@model` all'inizio del file *Index.cshtml*:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

La direttiva `@model` consente di accedere all'elenco di film che il controller ha passato alla vista usando un oggetto `Model` fortemente tipizzato. Ad esempio, nella vista *Index.cshtml* il codice scorre i film con un'istruzione `foreach` sull'oggetto fortemente tipizzato `Model`:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Poiché l'oggetto `Model` è fortemente tipizzato (come un oggetto `IEnumerable<Movie>`), ogni elemento nel ciclo viene tipizzato come `Movie`. Tra gli altri vantaggi, si ottiene un controllo del codice in fase di compilazione.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Helper per i tag](xref:mvc/views/tag-helpers/intro)
* [Globalizzazione e localizzazione](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Precedente aggiunta di una vista](adding-view.md) 
>  [Prossima collaborazione con SQL](working-with-sql.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Aggiungere una classe del modello di dati

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Fare clic con il pulsante destro del mouse sulla cartella *Models* > **Aggiungi** > **Classe**. Denominare la classe **Movie**.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

* Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>Eseguire lo scaffolding del modello di filmato

In questa sezione viene eseguito lo scaffolding del modello *Movie*. Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello *Movie*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Controller***> Aggiungi > Nuovo elemento di scaffolding**.

![vista del passaggio precedente](adding-model/_static/add_controller21.png)

Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Controller MVC con visualizzazioni, che usa Entity Framework**.

![Finestra di dialogo Aggiungi scaffolding](adding-model/_static/add_scaffold21.png)

Completare la finestra di dialogo **Aggiungi controller**:

* **Classe di modello:** *Movie (MvcMovie.Models)*
* **Classe del contesto dati:** selezionare l'icona **+** e aggiungere il valore predefinito **MvcMovie.Models.MvcMovieContext**

![Aggiungere il contesto dati](adding-model/_static/dc.png)

* **Viste:** mantenere il valore predefinito di ogni opzione selezionata
* **Nome del controller:** mantenere il valore predefinito *MoviesController*
* Selezionare **Aggiungi**

![Finestra di dialogo Aggiungi controller](adding-model/_static/add_controller2.png)

Visual Studio crea:

* Una [classe del contesto di database](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*Data/MvcMovieContext.cs*)
* Un controller di film (*Controllers/MoviesController.cs*)
* RazorVisualizza file per le pagine create, DELETE, Details, Edit e index (*views/Movies/ \* . cshtml*)

La creazione automatica del contesto di database e di viste e metodi di azione [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, delete) è nota come *scaffolding*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).
* Installare lo strumento di scaffolding:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* In Linux esportare il percorso dello strumento di scaffolding:

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Eseguire il comando seguente:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).
* Installare lo strumento di scaffolding:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Eseguire il comando seguente:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

Se si esegue l'app e si fa clic sul collegamento **Mvc Movie**, viene visualizzato un errore simile al seguente:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

È necessario creare il database, quindi si usa la funzionalità di [migrazioni](xref:data/ef-mvc/migrations) di Entity Framework Core a tale scopo. Le migrazioni consentono di creare un database che corrisponde al modello di dati e aggiornare lo schema del database quando cambia il modello di dati.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migrazione iniziale

In questa sezione vengono completate le attività seguenti:

* Aggiungere una migrazione iniziale.
* Aggiornare il database con la migrazione iniziale.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Dal menu **Strumenti** scegliere **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.

   ![Menu della Console di Gestione pacchetti](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. Nella Console di Gestione pacchetti immettere i comandi seguenti:

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   Il comando `Add-Migration` genera un codice per creare lo schema del database iniziale.

   Lo schema del database si basa sul modello specificato nella classe `MvcMovieContext`. L'argomento `Initial` è il nome della migrazione. È possibile usare qualsiasi nome, ma per convenzione viene usato un nome che descrive la migrazione. Per altre informazioni, vedere <xref:data/ef-mvc/migrations>.

   Il `Update-Database` comando esegue il `Up` metodo nel file *Migrations/{Time-Stamp} _InitialCreate. cs* , che crea il database.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

Il comando `ef migrations add InitialCreate` genera un codice per creare lo schema del database iniziale.

Lo schema del database è basato sul modello specificato nella classe `MvcMovieContext` (nel file *Data/MvcMovieContext.cs*). L'argomento `InitialCreate` è il nome della migrazione. È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>Esaminare il contesto registrato con l'inserimento di dipendenze

ASP.NET Core viene compilato con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection). I servizi, ad esempio il contesto di database di EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione. I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore. Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato con il contenitore di inserimento delle dipendenze.

Esaminare il metodo `Startup.ConfigureServices` seguente. La riga evidenziata è stata aggiunta dallo scaffolder:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

`MvcMovieContext` coordina la funzionalità di EF Core (Create, Read, Update, Delete e così via) per il modello `Movie`. Il contesto dei dati (`MvcMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il contesto dei dati specifica le entità incluse nel modello di dati:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità. Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database. Un'entità corrisponde a una riga nella tabella.

Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

È stato creato un contesto del database, poi registrato per il contenitore di inserimento delle dipendenze.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testare l'app

* Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).

Se si verifica un'eccezione di database simile al seguente:

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Non è stato eseguita la [migrazione](#pmc).

* Eseguire il test del collegamento **Crea**. Immettere e inviare i dati.

  > [!NOTE]
  > Potrebbe non essere possibile immettere virgole decimali nel campo `Price`. Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app. Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.

Esaminare la classe `Startup`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

Il codice evidenziato riportato in precedenza mostra il contesto del database dei film che viene aggiunto al contenitore di [inserimento dipendenze](xref:fundamentals/dependency-injection):

* `services.AddDbContext<MvcMovieContext>(options =>` specifica il database da usare e la stringa di connessione.
* `=>`è un [operatore lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)

Aprire il file *Controllers/MoviesController.cs* ed esaminare il costruttore:

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Il costruttore usa l'[inserimento dipendenze](xref:fundamentals/dependency-injection) per inserire il contesto del database (`MvcMovieContext`) nel controller. Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modelli fortemente tipizzati e parola chiave @model

In un passaggio precedente di questa esercitazione è stato esaminato come un controller può passare oggetti o dati a una vista usando il dizionario `ViewData`. Il dizionario `ViewData` è un oggetto dinamico che fornisce un modo pratico ad associazione tardiva per passare informazioni a una vista.

MVC consente anche di passare oggetti modello fortemente tipizzati a una vista. Questo approccio fortemente tipizzato consente un migliore controllo del codice in fase di compilazione. Con il meccanismo di scaffolding è stato usato questo approccio, ovvero il passaggio di un modello fortemente tipizzato, con le viste e la classe `MoviesController` quando sono stati creati i metodi e le viste.

Aprire il metodo `Details` nel file *Controllers/MoviesController.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

In genere il parametro `id` viene passato come dati di route. Ad esempio `https://localhost:5001/movies/details/1` imposta:

* Il controller sul controller `movies` (primo segmento di URL).
* L'azione su `details` (secondo segmento di URL).
* L'ID su 1 (ultimo segmento di URL).

È possibile anche passare `id` con una stringa di query nel modo seguente:

`https://localhost:5001/movies/details?id=1`

Il `id` parametro viene definito come [tipo Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) nel caso in cui non venga fornito un valore ID.

Un'[espressione lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) viene passata a `FirstOrDefaultAsync` per selezionare le entità film che corrispondono al valore della stringa di query o dei dati di route.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Se viene trovato un film, viene passata un'istanza del modello `Movie` alla vista `Details`:

```csharp
return View(movie);
   ```

Esaminare il contenuto del file *Views/Movies/Details.cshtml*:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Includendo un'istruzione `@model` all'inizio del file di vista, è possibile specificare il tipo di oggetto previsto dalla vista. Al momento della creazione del controller di film, l'istruzione `@model` seguente è stata inclusa automaticamente all'inizio del file *Details.cshtml*:

```cshtml
@model MvcMovie.Models.Movie
```

Questa direttiva `@model` consente di accedere al film passato dal controller alla vista usando un oggetto `Model` fortemente tipizzato. Ad esempio, nella vista *Details.cshtml* il codice passa ogni campo di film agli helper HTML `DisplayNameFor` e `DisplayFor` con l'oggetto `Model` fortemente tipizzato. Le viste e i metodi `Create` e `Edit` passano anche un oggetto modello `Movie`.

Esaminare la vista *Index.cshtml* e il metodo `Index` nel controller Movies. Si noti che il codice crea un oggetto `List` quando chiama il metodo `View`. Il codice passa questo elenco `Movies` dal metodo di azione `Index` alla vista:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Al momento della creazione del controller di film, lo scaffolding ha incluso automaticamente l'istruzione `@model` all'inizio del file *Index.cshtml*:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

La direttiva `@model` consente di accedere all'elenco di film che il controller ha passato alla vista usando un oggetto `Model` fortemente tipizzato. Ad esempio, nella vista *Index.cshtml* il codice scorre i film con un'istruzione `foreach` sull'oggetto fortemente tipizzato `Model`:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Poiché l'oggetto `Model` è fortemente tipizzato (come un oggetto `IEnumerable<Movie>`), ogni elemento nel ciclo viene tipizzato come `Movie`. Tra gli altri vantaggi, si ottiene un controllo del codice in fase di compilazione:

## <a name="additional-resources"></a>Risorse aggiuntive

* [Helper per i tag](xref:mvc/views/tag-helpers/intro)
* [Globalizzazione e localizzazione](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Precedente aggiunta di una vista](adding-view.md) 
>  [Successiva utilizzo di un database](working-with-sql.md)

::: moniker-end
