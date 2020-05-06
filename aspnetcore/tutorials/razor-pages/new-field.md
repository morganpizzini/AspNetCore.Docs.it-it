---
title: Aggiungere un nuovo campo a una Razor pagina in ASP.NET Core
author: rick-anderson
description: Mostra come aggiungere un nuovo campo a una Razor pagina con Entity Framework Core
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 683d6718f4dcdb73c45cbcf94f6ac4f477b71bcd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769734"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a>Aggiungere un nuovo campo a una pagina Razor in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

In questa sezione vengono usate le Migrazioni Code First di [Entity Framework](/ef/core/get-started/aspnetcore/new-db) per:

* Aggiungere un nuovo campo al modello.
* Eseguire la migrazione nel database della modifica al nuovo schema del campo.

Quando si usa Code First di Entity Framework per creare automaticamente un database, Code First:

* Aggiunge una `__EFMigrationsHistory` tabella al database per rilevare se lo schema del database è sincronizzato con le classi del modello da cui è stato generato.
* Se le classi di modelli non sono sincronizzate con il database, Entity Framework genera un'eccezione.

La verifica automatica del modello o schema sincronizzato rende più semplice individuare i problemi di codice o database incoerente.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Aggiunta di una proprietà Rating al modello Movie

Aprire il file *Models/Movie.cs* e aggiungere una proprietà `Rating`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Compilare l'app.

Modificare *Pages/Movies/Index.cshtml* e aggiungere un campo `Rating`:

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

Aggiornare le pagine seguenti:

* Aggiungere il campo `Rating` alle pagine Delete (Elimina) e Details (Dettagli).
* Aggiornare [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) con un campo `Rating`.
* Aggiungere il campo `Rating` alla pagina Edit (Modifica).

L'app non funzionerà finché non si aggiorna il database in modo da includere il nuovo campo. L'esecuzione dell'app senza aggiornare il database genera `SqlException`un'eccezione:

`SqlException: Invalid column name 'Rating'.`

L' `SqlException` eccezione è causata dal fatto che la classe del modello di film aggiornata è diversa dallo schema della tabella dei film del database. Nella tabella del database non è presente una colonna `Rating`.

Per correggere questo errore, esistono alcuni approcci:

1. Fare in modo che Entity Framework elimini e crei di nuovo automaticamente il database usando il nuovo schema di classi del modello. Questo approccio è utile nelle prime fasi del ciclo di sviluppo e consente di migliorare rapidamente lo schema del modello e il database insieme. Lo svantaggio è che si perdono i dati esistenti nel database. Non usare questo approccio in un database di produzione. L'eliminazione del database per la modifica dello schema e l'uso di un inizializzatore per inizializzare automaticamente un database con i dati di test è spesso un modo produttivo per sviluppare un'app.

2. Modificare esplicitamente lo schema del database esistente in modo che corrisponda alle classi del modello. Il vantaggio di questo approccio è che i dati vengono mantenuti. È possibile apportare questa modifica manualmente o creando uno script di modifica del database.

3. Usare Migrazioni Code First per aggiornare lo schema del database.

Per questa esercitazione usare Migrazioni Code First.

Aggiornare la classe `SeedData` in modo che fornisca un valore per la nuova colonna. Di seguito viene illustrata una modifica di esempio, ma si apporterà questa modifica per ogni blocco `new Movie`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Vedere il [file SeedData.cs completato](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).

Compilare la soluzione.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Aggiungere una migrazione per il campo Rating

Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Console di Gestione pacchetti**.
Nella Console di Gestione pacchetti immettere i comandi seguenti:

```powershell
Add-Migration Rating
Update-Database
```

Il comando `Add-Migration` indica al framework di:

* Confrontare il modello `Movie` con lo schema di database `Movie`.
* Creare codice per migrare lo schema del database nel nuovo modello.

Il nome "Rating" è arbitrario e viene usato per denominare il file di migrazione. È consigliabile usare un nome significativo per il file di migrazione.

Il `Update-Database` comando indica al Framework di applicare le modifiche dello schema al database e di mantenere i dati esistenti.

<a name="ssox"></a>

Se si eliminano tutti i record nel database, il database viene inizializzato e viene incluso il campo `Rating`. È possibile eseguire questa operazione con i collegamenti di eliminazione nel browser o da [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).

Un'altra opzione è quella di eliminare il database e usare le migrazioni per ricreare il database. Per eliminare il database da SSOX:

* Selezionare il database in SSOX.
* Fare clic con il pulsante destro del mouse sul database e selezionare *Elimina*.
* Selezionare **Chiudi connessioni esistenti**.
* Selezionare **OK**.
* In [PMC](xref:tutorials/razor-pages/new-field#pmc)aggiornare il database:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Eliminare e ricreare il database

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Eliminare la cartella della migrazione.  Usare i comandi seguenti per ricreare il database.

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Eseguire l'app e verificare che sia possibile creare/modificare/visualizzare i film con un campo `Rating`. Se il database non è inizializzato, impostare un punto di interruzione nel metodo `SeedData.Initialize`.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Versione YouTube dell'esercitazione](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Precedente: aggiunta della ricerca](xref:tutorials/razor-pages/search)
> [successiva: aggiunta della convalida](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

In questa sezione vengono usate le Migrazioni Code First di [Entity Framework](/ef/core/get-started/aspnetcore/new-db) per:

* Aggiungere un nuovo campo al modello.
* Eseguire la migrazione nel database della modifica al nuovo schema del campo.

Quando si usa Code First di Entity Framework per creare automaticamente un database, Code First:

* Aggiunge una tabella al database per rilevare se lo schema del database è sincronizzato con le classi di modelli da cui è stato generato.
* Se le classi di modelli non sono sincronizzate con il database, Entity Framework genera un'eccezione.

La verifica automatica del modello o schema sincronizzato rende più semplice individuare i problemi di codice o database incoerente.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Aggiunta di una proprietà Rating al modello Movie

Aprire il file *Models/Movie.cs* e aggiungere una proprietà `Rating`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Compilare l'app.

Modificare *Pages/Movies/Index.cshtml* e aggiungere un campo `Rating`:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

Aggiornare le pagine seguenti:

* Aggiungere il campo `Rating` alle pagine Delete (Elimina) e Details (Dettagli).
* Aggiornare [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) con un campo `Rating`.
* Aggiungere il campo `Rating` alla pagina Edit (Modifica).

L'app non funzionerà finché non si aggiorna il database in modo da includere il nuovo campo. Se si esegue l'app ora, verrà visualizzato un errore `SqlException`:

`SqlException: Invalid column name 'Rating'.`

Questo errore viene visualizzato perché la classe del modello Movie aggiornata è diversa rispetto allo schema della tabella Movie nel database. Nella tabella del database non è presente una colonna `Rating`.

Per correggere questo errore, esistono alcuni approcci:

1. Fare in modo che Entity Framework elimini e crei di nuovo automaticamente il database usando il nuovo schema di classi del modello. Questo approccio è utile nelle prime fasi del ciclo di sviluppo e consente di migliorare rapidamente lo schema del modello e il database insieme. Lo svantaggio è che si perdono i dati esistenti nel database. Non usare questo approccio in un database di produzione. L'eliminazione del database per la modifica dello schema e l'uso di un inizializzatore per inizializzare automaticamente un database con i dati di test è spesso un modo produttivo per sviluppare un'app.

2. Modificare esplicitamente lo schema del database esistente in modo che corrisponda alle classi del modello. Il vantaggio di questo approccio è che i dati vengono mantenuti. È possibile apportare questa modifica manualmente o creando uno script di modifica del database.

3. Usare Migrazioni Code First per aggiornare lo schema del database.

Per questa esercitazione usare Migrazioni Code First.

Aggiornare la classe `SeedData` in modo che fornisca un valore per la nuova colonna. Di seguito viene illustrata una modifica di esempio, ma si apporterà questa modifica per ogni blocco `new Movie`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Vedere il [file SeedData.cs completato](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).

Compilare la soluzione.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Aggiungere una migrazione per il campo Rating

Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Console di Gestione pacchetti**.
Nella Console di Gestione pacchetti immettere i comandi seguenti:

```powershell
Add-Migration Rating
Update-Database
```

Il comando `Add-Migration` indica al framework di:

* Confrontare il modello `Movie` con lo schema di database `Movie`.
* Creare codice per migrare lo schema del database nel nuovo modello.

Il nome "Rating" è arbitrario e viene usato per denominare il file di migrazione. È consigliabile usare un nome significativo per il file di migrazione.

Il comando `Update-Database` indica al framework di applicare le modifiche dello schema al database.

<a name="ssox"></a>

Se si eliminano tutti i record nel database, il database viene inizializzato e viene incluso il campo `Rating`. È possibile eseguire questa operazione con i collegamenti di eliminazione nel browser o da [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).

Un'altra opzione è quella di eliminare il database e usare le migrazioni per ricreare il database. Per eliminare il database da SSOX:

* Selezionare il database in SSOX.
* Fare clic con il pulsante destro del mouse sul database e selezionare *Elimina*.
* Selezionare **Chiudi connessioni esistenti**.
* Selezionare **OK**.
* In [PMC](xref:tutorials/razor-pages/new-field#pmc)aggiornare il database:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Eliminare e ricreare il database

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Eliminare il database e usare le migrazioni per ricreare il database. Per eliminare il database, eliminare il file di database (*MvcMovie.db*). Eseguire quindi il comando `ef database update`:

```dotnetcli
dotnet ef database update
```

---

Eseguire l'app e verificare che sia possibile creare/modificare/visualizzare i film con un campo `Rating`. Se il database non è inizializzato, impostare un punto di interruzione nel metodo `SeedData.Initialize`.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Versione YouTube dell'esercitazione](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Precedente: aggiunta della ricerca](xref:tutorials/razor-pages/search)
> [successiva: aggiunta della convalida](xref:tutorials/razor-pages/validation)

::: moniker-end
