---
title: Usare un database e ASP.NET Core
author: rick-anderson
description: Questa sezione illustra l'utilizzo di un database e di ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 159588ec750f0ede534522aa9397fc2aefb58cd6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775609"
---
# <a name="work-with-a-database-and-aspnet-core"></a>Usare un database e ASP.NET Core

Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

L'oggetto `RazorPagesMovieContext` gestisce l'attività di connessione al database e di mapping degli oggetti `Movie` ai record di database. Il contesto del database viene registrato con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) nel metodo `ConfigureServices` di *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Il sistema di [configurazione](xref:fundamentals/configuration/index) di ASP.NET Core legge la `ConnectionString`. Per lo sviluppo locale, ottiene la stringa di connessione dal file *appSettings. JSON* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Il valore del nome per il database (`Database={Database name}`) sarà diverso per il codice generato. Il valore del nome è arbitrario.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Quando l'app viene distribuita in un server di test o produzione, è possibile utilizzare una variabile di ambiente per impostare la stringa di connessione su un server di database reale. Per altre informazioni, vedere [Configurazione](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di programmi. Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa. Per impostazione predefinita, Local DB crea file con estensione `*.mdf` nella directory `C:\Users\<user>\`.

<a name="ssox"></a>
* Dal menu **Visualizzazione** aprire **Esplora oggetti di SQL Server** (SSOX).

  ![Menu Visualizza](sql/_static/ssox.png)

* Fare clic con il pulsante destro del mouse sulla tabella `Movie` e selezionare**Progettazione viste**:

  ![Menu di scelta rapida aperti per la tabella Movie](sql/_static/design.png)

  ![Tabelle Movie aperte nella finestra di progettazione](sql/_static/dv.png)

Si noti l'icona a forma di chiave accanto a `ID`. Per impostazione predefinita, Entity Framework crea una proprietà denominata `ID` per la chiave primaria.

* Fare clic con il pulsante destro del mouse sulla tabella `Movie` e selezionare**Visualizza dati**:

  ![Tabella Movie aperta con i dati della tabella](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Specificare il valore di inizializzazione del database

Creare una nuova classe denominata `SeedData` nella cartella *Models* usando il codice seguente:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Se sono presenti eventuali film nel database, l'inizializzatore del valore di inizializzazione viene restituito e non vengono aggiunti film.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Aggiungere l'inizializzatore del valore di inizializzazione

In *Program.cs* modificare il metodo `Main` per eseguire le operazioni seguenti:

* Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.
* Chiamare il metodo di inizializzazione, passandolo al contesto.
* Eliminare il contesto dopo che il metodo di inizializzazione è stato completato.

Il codice seguente illustra il file *Program.cs* aggiornato.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

Quando `Update-Database` non è stato eseguito, si verifica l'eccezione seguente:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Testare l'app

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Eliminare tutti i record nel database. Questa operazione può essere eseguita con i collegamenti di eliminazione nel browser o da [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Forzare l'inizializzazione dell'app (chiamare i metodi nella classe `Startup`) in modo che venga eseguito il metodo di inizializzazione. Per forzare l'inizializzazione, IIS Express deve essere arrestato e riavviato. È possibile eseguire questa operazione adottando uno degli approcci seguenti:

  * Fare clic con il pulsante destro del mouse sull'icona della barra di sistema IIS Express nell'area di notifica e toccare **Esci** o **Arresta sito**:

    ![Icona dell'area di notifica di IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu di scelta rapida](sql/_static/stopIIS.png)

    * Se Visual Studio è in esecuzione in modalità non di debug, premere F5 per attivare la modalità di debug.
    * Se Visual Studio è in esecuzione in modalità di debug, arrestare il debugger e premere F5.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

Eliminare tutti i record del database; verrà quindi eseguito il metodo di inizializzazione. Arrestare e avviare l'app per inizializzare il database.

L'app mostra i dati inizializzati.

---

L'esercitazione successiva consentirà di migliorare la presentazione dei dati.

## <a name="additional-resources"></a>Risorse aggiuntive

> [!div class="step-by-step"]
> [Precedente: impalcatura Razor Pages](xref:tutorials/razor-pages/page)
> [successiva: aggiornamento delle pagine](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

L'oggetto `RazorPagesMovieContext` gestisce l'attività di connessione al database e di mapping degli oggetti `Movie` ai record di database. Il contesto del database viene registrato con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) nel metodo `ConfigureServices` di *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Per altre informazioni sui metodi usati in `ConfigureServices`, vedere:

* [Supporto per il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea in ASP.NET Core](xref:security/gdpr) per `CookiePolicyOptions`.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

Il sistema di [configurazione](xref:fundamentals/configuration/index) di ASP.NET Core legge la `ConnectionString`. Per lo sviluppo locale, ottiene la stringa di connessione dal file *appSettings. JSON* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Il valore del nome per il database (`Database={Database name}`) sarà diverso per il codice generato. Il valore del nome è arbitrario.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Quando l'app viene distribuita in un server di test o produzione, è possibile utilizzare una variabile di ambiente per impostare la stringa di connessione su un server di database reale. Per altre informazioni, vedere [Configurazione](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di programmi. Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa. Per impostazione predefinita, Local DB crea file con estensione `*.mdf` nella directory `C:/Users/<user/>`.

<a name="ssox"></a>
* Dal menu **Visualizzazione** aprire **Esplora oggetti di SQL Server** (SSOX).

  ![Menu Visualizza](sql/_static/ssox.png)

* Fare clic con il pulsante destro del mouse sulla tabella `Movie` e selezionare**Progettazione viste**:

  ![Menu di scelta rapida aperto per la tabella Movie](sql/_static/design.png)

  ![Tabella Movie aperta nella finestra di progettazione](sql/_static/dv.png)

Si noti l'icona a forma di chiave accanto a `ID`. Per impostazione predefinita, Entity Framework crea una proprietà denominata `ID` per la chiave primaria.

* Fare clic con il pulsante destro del mouse sulla tabella `Movie` e selezionare**Visualizza dati**:

  ![Tabella Movie aperta con i dati della tabella](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Specificare il valore di inizializzazione del database

Creare una nuova classe denominata `SeedData` nella cartella *Models* usando il codice seguente:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Se sono presenti eventuali film nel database, l'inizializzatore del valore di inizializzazione viene restituito e non vengono aggiunti film.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Aggiungere l'inizializzatore del valore di inizializzazione

In *Program.cs* modificare il metodo `Main` per eseguire le operazioni seguenti:

* Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.
* Chiamare il metodo di inizializzazione, passandolo al contesto.
* Eliminare il contesto dopo che il metodo di inizializzazione è stato completato.

Il codice seguente illustra il file *Program.cs* aggiornato.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Un'app di produzione non chiamerà `Database.Migrate`. Sarà aggiunto al codice precedente per evitare l'eccezione seguente quando `Update-Database` non è stato eseguito:

SqlException: Impossibile aprire il database "RazorPagesMovieContext-21" richiesto dall'account di accesso. Accesso non riuscito.
Accesso non riuscito per l'utente "nome-utente".

### <a name="test-the-app"></a>Testare l'app

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Eliminare tutti i record nel database. Questa operazione può essere eseguita con i collegamenti di eliminazione nel browser o da [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Forzare l'inizializzazione dell'app (chiamare i metodi nella classe `Startup`) in modo che venga eseguito il metodo di inizializzazione. Per forzare l'inizializzazione, IIS Express deve essere arrestato e riavviato. È possibile eseguire questa operazione adottando uno degli approcci seguenti:

  * Fare clic con il pulsante destro del mouse sull'icona dell'area di notifica di IIS Express e toccare **Esci** o **Arresta sito**:

    ![Icona dell'area di notifica di IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu di scelta rapida](sql/_static/stopIIS.png)

    * Se Visual Studio è in esecuzione in modalità non di debug, premere F5 per attivare la modalità di debug.
    * Se Visual Studio è in esecuzione in modalità di debug, arrestare il debugger e premere F5.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Eliminare tutti i record del database; verrà quindi eseguito il metodo di inizializzazione. Arrestare e avviare l'app per inizializzare il database.

L'app mostra i dati inizializzati.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Eliminare tutti i record del database; verrà quindi eseguito il metodo di inizializzazione. Arrestare e avviare l'app per inizializzare il database.

L'app mostra i dati inizializzati.

---

L'app visualizza i dati sottoposti a seed:

![App per i film aperta in Chrome con i dati sui film](sql/_static/m55.png)

L'esercitazione successiva consentirà di pulire la presentazione dei dati.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Versione YouTube dell'esercitazione](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Precedente: Razor pagine](xref:tutorials/razor-pages/page)
> con impalcature[successive: aggiornamento delle pagine](xref:tutorials/razor-pages/da1)

::: moniker-end
