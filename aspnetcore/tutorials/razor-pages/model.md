---
title: Parte 2, aggiungere un modello a un' Razor app di pagine in ASP.NET Core
author: rick-anderson
description: Parte 2 della serie di esercitazioni sulle Razor pagine.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/model
ms.openlocfilehash: d3f7d355625ce20a0cb45c785fbab259a5655c63
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652930"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>Parte 2, aggiungere un modello a un' Razor app di pagine in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

In questa sezione vengono aggiunte le classi per la gestione dei filmati. Le classi modello dell'app usano [Entity Framework Core (EF Core)](/ef/core) per lavorare con il database. EF Core è un mapper relazionale a oggetti (O/RM) che semplifica l'accesso ai dati.

Le classi di modello sono dette classi POCO (da "plain-old CLR objects") perché non hanno alcuna dipendenza in EF Core. Definiscono le proprietà dei dati archiviati nel database.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Aggiungere un modello di dati

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Fare clic con il pulsante destro del mouse sul progetto **RazorPagesMovie** > **Aggiungi** > **Nuova cartella**. Assegnare il nome *Modelli* alla cartella.

Fare clic con il pulsante destro del mouse sulla cartella *Models*. Selezionare **Aggiungi**  >  **classe**. Denominare la classe **Movie**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Aggiungere una cartella denominata *Modelli*.
* Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* In riquadro della soluzione fare clic con il pulsante destro del mouse sul progetto **RazorPagesMovie** , quindi scegliere **Aggiungi** > **nuova cartella.** Assegnare un nome ai *modelli*di cartella.
* Fare clic con il pulsante destro del mouse sulla cartella *Models* , quindi scegliere **Aggiungi** > **nuovo file**.
* Nel finestra di dialogo **Nuovo file**:

  * Selezionare **Generale** nel riquadro a sinistra.
  * Selezionare **Classe vuota** nel riquadro centrale.
  * Denominare la classe **Filmato** e selezionare **Nuovo**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Compilare il progetto per verificare che non siano presenti errori di compilazione.

## <a name="scaffold-the-movie-model"></a>Eseguire lo scaffolding del modello di filmato

In questa sezione viene eseguito lo scaffolding del modello *Movie*. Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello *Movie*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Creare una cartella *Pages/Movies*:

* Fare clic con il pulsante destro del mouse sulla cartella *Pages* > **Aggiungi** > **Nuova cartella**.
* Assegnare il nome *Movies* alla cartella

Fare clic con il pulsante destro del mouse sulla cartella *Pages/Movies* > **Aggiungi** > **Nuovo elemento di scaffolding**.

![Immagine relativa alle istruzioni precedenti.](model/_static/sca.png)

Nella finestra di dialogo **Aggiungi impalcatura** selezionare ** Razor pages using Entity Framework (CRUD)** > **Add**.

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :

* Nel menu a discesa **Classe modello** selezionare **Movie (RazorPagesMovie.Models)**.
* Nella riga **Classe contesto di dati** selezionare il segno più **+** e modificare il nome generato da RazorPagesMovie.**Models**.RazorPagesMovieContext a RazorPagesMovie.**Data**.RazorPagesMovieContext. [Questa modifica](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) non è obbligatoria. Crea la classe del contesto di database con lo spazio dei nomi corretto.
* Selezionare **Aggiungi**.

![Immagine relativa alle istruzioni precedenti.](model/_static/3/arp.png)

Il file *appsettings.json* è stato aggiornato con la stringa di connessione usata per connettersi a un database locale.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).
* Installare lo strumento di scaffolding:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Per Windows**: eseguire il comando seguente:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Per MacOS e Linux**: eseguire il comando seguente:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Creare una cartella *Pages/Movies*:

* Fare clic con il pulsante destro del mouse sulla cartella *Pages* > **Aggiungi** > **Nuova cartella**.
* Assegnare il nome *Movies* alla cartella

Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **Nuova impalcatura...**.

![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

Nella finestra di dialogo **Nuova impalcatura** selezionare ** Razor pagine con Entity Framework (CRUD)** > **Avanti**.

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :

* Nell'elenco a discesa **classe modello** selezionare o digitare **Movie (RazorPagesMovie. Models)**.
* Nella riga della **classe del contesto dati** Digitare il nome della nuova classe, RazorPagesMovie. **Dati**. RazorPagesMovieContext. [Questa modifica](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) non è obbligatoria. Crea la classe del contesto di database con lo spazio dei nomi corretto.
* Selezionare **Aggiungi**.

![Immagine relativa alle istruzioni precedenti.](model/_static/arpMac.png)

Il file *appsettings.json* è stato aggiornato con la stringa di connessione usata per connettersi a un database locale.

### <a name="add-ef-tools"></a>Aggiungi strumenti EF

Eseguire il comando interfaccia della riga di comando di .NET Core seguente:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Il comando precedente aggiunge gli strumenti Entity Framework Core per l'interfaccia della riga di comando di .NET Core.

---

### <a name="files-created"></a>File creati

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Il processo di scaffolding crea e aggiorna i file seguenti:

* *Pages/Movies*: pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice).
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Aggiornato

* *Startup.cs*

I file creati e aggiornati sono illustrati nella sezione successiva.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Il processo di scaffolding crea e aggiorna i file seguenti:

* *Pages/Movies*: pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice).
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Aggiornato

* *Startup.cs*

I file creati e aggiornati sono illustrati nella sezione successiva.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Il processo di scaffolding crea i file seguenti:

* *Pages/Movies*: pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice).

I file creati sono illustrati nella sezione successiva.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Migrazione iniziale

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In questa sezione viene usata la Console di Gestione pacchetti (PMC) per:

* Aggiungere una migrazione iniziale.
* Aggiornare il database con la migrazione iniziale.

Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.

  ![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

Nella Console di Gestione pacchetti immettere i comandi seguenti:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

I comandi precedenti generano l'avviso seguente: "nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '. This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite). Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()')"

È possibile ignorare tale avviso. Verrà risolto in un'esercitazione successiva.

Il comando Migrations genera il codice per creare lo schema del database iniziale. Lo schema è basato sul modello specificato in `DbContext` . L'argomento `InitialCreate` viene usato per denominare le migrazioni. È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.

Il `update` comando esegue il `Up` Metodo nelle migrazioni che non sono state applicate. In tal caso, `update` esegue il `Up` metodo nel file *migrations/ \<time-stamp> _InitialCreate. cs* , che crea il database.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Esaminare il contesto registrato con l'inserimento di dipendenze

ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection). I servizi, ad esempio il contesto di database di Entity Framework Core, vengono registrati tramite dependency injection durante l'avvio dell'applicazione. I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore. Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.

Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato per la dependency injection.

Esaminare il metodo `Startup.ConfigureServices`. La riga evidenziata è stata aggiunta dallo scaffolder:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext` coordina la funzionalità di EF Core (Create, Read, Update, Delete e così via) per il modello `Movie`. Il contesto dei dati (`RazorPagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il contesto dei dati specifica le entità incluse nel modello di dati.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità. Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database. Un'entità corrisponde a una riga nella tabella.

Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Esaminare il metodo `Up`.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Esaminare il metodo `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testare l'app

* Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).

Se viene visualizzato l'errore:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Non è stato eseguita la [migrazione](#pmc).

* Eseguire il test del collegamento **Crea**.

  ![Pagina di creazione](model/_static/conan.png)

  > [!NOTE]
  > Potrebbe non essere possibile immettere virgole decimali nel campo `Price`. Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app. Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.

L'esercitazione successiva illustra i file creati tramite scaffolding.

## <a name="additional-resources"></a>Risorse aggiuntive

> [!div class="step-by-step"]
> [Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura Razor Pagine](xref:tutorials/razor-pages/page) di

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

In questa sezione vengono aggiunte le classi per la gestione dei film in un [database SQLite](https://www.sqlite.org/index.html)multipiattaforma. Le app create da un modello di ASP.NET Core usano un database SQLite. Le classi modello dell'app vengono usate con [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provider di database](/ef/core/providers/sqlite)) per lavorare con il database. EF Core è un framework ORM (Object-Relational Mapping) che semplifica l'accesso ai dati.

Le classi di modello sono dette classi POCO (da "plain-old CLR objects") perché non hanno alcuna dipendenza in EF Core. Definiscono le proprietà dei dati archiviati nel database.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Aggiungere un modello di dati

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Fare clic con il pulsante destro del mouse sul progetto **RazorPagesMovie** > **Aggiungi** > **Nuova cartella**. Assegnare il nome *Modelli* alla cartella.

Fare clic con il pulsante destro del mouse sulla cartella *Models*. Selezionare **Aggiungi**  >  **classe**. Denominare la classe **Movie**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Aggiungere una cartella denominata *Modelli*.
* Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **RazorPagesMovie**, quindi selezionare **Aggiungi** > **Nuova cartella**. Assegnare il nome *Modelli* alla cartella.
* Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Nuovo file**.
* Nel finestra di dialogo **Nuovo file**:

  * Selezionare **Generale** nel riquadro a sinistra.
  * Selezionare **Classe vuota** nel riquadro centrale.
  * Denominare la classe **Filmato** e selezionare **Nuovo**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Compilare il progetto per verificare che non siano presenti errori di compilazione.

## <a name="scaffold-the-movie-model"></a>Eseguire lo scaffolding del modello di filmato

In questa sezione viene eseguito lo scaffolding del modello *Movie*. Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello *Movie*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Creare una cartella *Pages/Movies*:

* Fare clic con il pulsante destro del mouse sulla cartella *Pages* > **Aggiungi** > **Nuova cartella**.
* Assegnare il nome *Movies* alla cartella

Fare clic con il pulsante destro del mouse sulla cartella *Pages/Movies* > **Aggiungi** > **Nuovo elemento di scaffolding**.

![Immagine relativa alle istruzioni precedenti.](model/_static/sca.png)

Nella finestra di dialogo **Aggiungi impalcatura** selezionare ** Razor pages using Entity Framework (CRUD)** > **Add**.

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* Nel menu a discesa **Classe modello** selezionare **Movie (RazorPagesMovie.Models)**.
* Nella riga **Classe contesto di dati** selezionare il segno più **+** e accettare il nome generato **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Selezionare **Aggiungi**.

![Immagine relativa alle istruzioni precedenti.](model/_static/arp.png)

Il file *appsettings.json* è stato aggiornato con la stringa di connessione usata per connettersi a un database locale.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).

* **Per Windows**: eseguire il comando seguente:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Per MacOS e Linux**: eseguire il comando seguente:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Creare una cartella *Pages/Movies*:

* Fare clic con il pulsante destro del mouse sulla cartella *Pages* > **Aggiungi** > **Nuova cartella**.
* Assegnare il nome *Movies* alla cartella

Fare clic con il pulsante destro del mouse sulla cartella *Pages/Movies* > **Aggiungi** > **Nuovo elemento di scaffolding**.

![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

Nella finestra di dialogo **Aggiungi nuova impalcatura** selezionare ** Razor pages using Entity Framework (CRUD)** > **Add**.

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :

* Nell'elenco a discesa **classe modello** selezionare o digitare **Movie**.
* Nella riga della **classe del contesto dati** Digitare select the **RazorPagesMovieContext** . verrà creata una nuova classe del contesto DB con lo spazio dei nomi corretto. In questo caso sarà **RazorPagesMovie. Models. RazorPagesMovieContext**.
* Selezionare **Aggiungi**.

![Immagine relativa alle istruzioni precedenti.](model/_static/arpMac.png)

Il file *appsettings.json* è stato aggiornato con la stringa di connessione usata per connettersi a un database locale.

---

Il processo di scaffolding crea e aggiorna i file seguenti:

### <a name="files-created"></a>File creati

* *Pages/Movies*: pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice).
* *Data/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>File aggiornato

* *Startup.cs*

I file creati e aggiornati sono illustrati nella sezione successiva.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migrazione iniziale

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In questa sezione viene usata la Console di Gestione pacchetti (PMC) per:

* Aggiungere una migrazione iniziale.
* Aggiornare il database con la migrazione iniziale.

Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.

  ![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

Nella Console di Gestione pacchetti immettere i comandi seguenti:

```powershell
Add-Migration Initial
Update-Database
```

Il comando `Add-Migration` genera un codice per creare lo schema del database iniziale. Lo schema è basato sul modello specificato in `DbContext` (nel file *RazorPagesMovieContext.cs*). L' `InitialCreate` argomento viene usato per assegnare un nome alla migrazione. È possibile usare qualsiasi nome, ma per convenzione viene usato un nome che descrive la migrazione. Per altre informazioni, vedere <xref:data/ef-mvc/migrations>.

Il `Update-Database` comando esegue il `Up` metodo nel file *migrations/ \<time-stamp> _InitialCreate. cs* . Il metodo `Up` crea il database.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> I comandi precedenti generano l'avviso seguente: "*nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '. In questo modo i valori verranno troncati automaticamente se non rientrano nella precisione e nella scala predefinite. Specificare in modo esplicito il tipo di colonna di SQL Server in grado di contenere tutti i valori utilizzando ' HasColumnType ()'.* È possibile ignorare l'avviso, che verrà risolto in un'esercitazione successiva.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Esaminare il contesto registrato con l'inserimento di dipendenze

ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection). I servizi, ad esempio il contesto di database di Entity Framework Core, vengono registrati tramite dependency injection durante l'avvio dell'applicazione. I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore. Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.

Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato per la dependency injection.

Esaminare il metodo `Startup.ConfigureServices`. La riga evidenziata è stata aggiunta dallo scaffolder:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext` coordina la funzionalità di EF Core (Create, Read, Update, Delete e così via) per il modello `Movie`. Il contesto dei dati (`RazorPagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il contesto dei dati specifica le entità incluse nel modello di dati.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità. Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database. Un'entità corrisponde a una riga nella tabella.

Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Esaminare il metodo `Up`.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Esaminare il metodo `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testare l'app

* Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).

Se viene visualizzato l'errore:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Non è stato eseguita la [migrazione](#pmc).

* Eseguire il test del collegamento **Crea**.

  ![Pagina di creazione](model/_static/conan.png)

  > [!NOTE]
  > Potrebbe non essere possibile immettere virgole decimali nel campo `Price`. Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app. Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.

L'esercitazione successiva illustra i file creati tramite scaffolding.

## <a name="additional-resources"></a>Risorse aggiuntive

> [!div class="step-by-step"]
> [Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura Razor Pagine](xref:tutorials/razor-pages/page) di

::: moniker-end
