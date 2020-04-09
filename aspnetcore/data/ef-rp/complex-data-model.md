---
title: Razor Pages con EF Core in ASP.NET Core - Modello di dati - 5 di 8
author: rick-anderson
description: In questa esercitazione si aggiungono altre entità e relazioni e si personalizza il modello di dati specificando regole di formattazione, convalida e mapping.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1d81a0444487c6396bb32381ed2cb26d44312c3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665718"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a>Razor Pages con EF Core in ASP.NET Core - Modello di dati - 5 di 8

Di [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Nelle esercitazioni precedenti è stato usato un modello di dati semplice costituito da tre entità. Contenuto dell'esercitazione:

* Vengono aggiunte altre entità e relazioni.
* Il modello di dati viene personalizzato specificando regole di formattazione, convalida e mapping del database.

Il modello di dati completato è illustrato nella figura seguente:

![Diagramma dell'entità](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a>Entità Student (Studente)

![Entità Student](complex-data-model/_static/student-entity.png)

Sostituire il codice in *Models/Student.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

Il codice precedente aggiunge una proprietà `FullName` e aggiunge gli attributi seguenti alle proprietà esistenti:

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a>Proprietà calcolata FullName

`FullName` è una proprietà calcolata che restituisce un valore creato concatenando altre due proprietà. `FullName` non può essere impostata, quindi include solo una funzione di accesso get. Nel database non viene creata una colonna `FullName`.

### <a name="the-datatype-attribute"></a>Attributo DataType

```csharp
[DataType(DataType.Date)]
```

Per le date di iscrizione degli studenti, tutte le pagine visualizzano attualmente l'ora del giorno insieme alla data, anche se è pertinente solo la data. Mediante gli attributi di annotazione dei dati è possibile modificare il codice per correggere il formato di visualizzazione in tutte le pagine che visualizzano i dati. 

L'attributo [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) indica un tipo di dati più specifico rispetto al tipo intrinseco del database. In questo caso deve essere visualizzata solo la data e non la data e l'ora. Il [DataType Enumerazione](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) fornisce per molti tipi di dati, ad esempio Data, ora, PhoneNumber, valuta, EmailAddress e così via. L'attributo `DataType` può anche consentire all'app di fornire automaticamente funzionalità specifiche del tipo. Ad esempio:

* Il collegamento `mailto:` viene creato automaticamente per `DataType.EmailAddress`.
* Il selettore data viene incluso per `DataType.Date` nella maggior parte dei browser.

L'attributo `DataType` genera attributi HTML 5 `data-`. Gli attributi `DataType` non garantiscono la convalida.

### <a name="the-displayformat-attribute"></a>Attributo DisplayFormat

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`DataType.Date` non specifica il formato della data visualizzata. Per impostazione predefinita il campo data viene visualizzato in base ai formati predefiniti per il valore [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) del server.

L'attributo `DisplayFormat` viene usato per specificare in modo esplicito il formato della data. L'impostazione `ApplyFormatInEditMode` specifica che la formattazione deve essere applicata anche all'interfaccia utente di modifica. Alcuni campi non devono usare `ApplyFormatInEditMode`. Ad esempio il simbolo di valuta in genere non deve essere visualizzato in una casella di testo di modifica.

L'attributo `DisplayFormat` può essere usato da solo. In genere l'uso dell'attributo `DataType` con l'attributo `DisplayFormat` è consigliato. L'attributo `DataType` offre la semantica dei dati anziché specificarne il rendering in una schermata. L'attributo `DataType` offre i vantaggi seguenti che non sono disponibili in `DisplayFormat`:

* Il browser può abilitare le funzionalità HTML5. Ad esempio può visualizzare un controllo di calendario, il simbolo della valuta appropriato per le impostazioni locali, i collegamenti alla posta elettronica e la convalida dell'input sul lato client.
* Per impostazione predefinita, il browser esegue il rendering dei dati usando il formato corretto in base alle impostazioni locali.

Per ulteriori informazioni, vedere [ \<l'input> documentazione](xref:mvc/views/working-with-forms#the-input-tag-helper)di Esempio di tag .

### <a name="the-stringlength-attribute"></a>Attributo StringLength

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

È possibile specificare regole di convalida dei dati e messaggi di errore di convalida usando gli attributi. L'attributo [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) specifica il numero minimo e massimo di caratteri consentiti in un campo dati. Il codice precedente limita i nomi a un massimo di 50 caratteri. Un esempio che imposta la lunghezza minima della stringa è disponibile [di seguito](#the-required-attribute).

L'attributo `StringLength` offre anche la convalida lato client e lato server. Il valore minimo non ha alcun effetto sullo schema del database.

L'attributo `StringLength` non impedisce a un utente di immettere spazi vuoti per un nome. L'attributo [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) può essere usato per applicare restrizioni all'input. Ad esempio il codice seguente richiede che il primo carattere sia maiuscolo e i caratteri rimanenti siano caratteri alfabetici:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In **Esplora oggetti di SQL Server** (SSOX) aprire il designer della tabella **Student** (Studente) facendo doppio clic sulla tabella.

![Tabella Student (Studenti) in SSOX prima delle migrazioni](complex-data-model/_static/ssox-before-migration.png)

L'immagine precedente visualizza lo schema per la tabella `Student`. I campi del nome sono di tipo `nvarchar(MAX)`. Quando una migrazione viene creata e applicata più avanti in questa esercitazione, i campi del nome diventano `nvarchar(50)` come risultato degli attributi di lunghezza della stringa.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Nello strumento SQLite esaminare le definizioni di colonna per la tabella `Student`. I campi del nome sono di tipo `Text`. Si noti che il campo del nome è chiamato `FirstMidName`. Nella sezione successiva il nome di tale colonna viene modificato in `FirstName`.

---

### <a name="the-column-attribute"></a>Attributo Column

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

Gli attributi possono controllare il mapping delle classi e delle proprietà nel database. Nel modello `Student` l'attributo `Column` viene usato per il mapping del nome della proprietà `FirstMidName` su "FirstName" nel database.

Quando viene creato il database, i nomi delle proprietà nel modello vengono usati per i nomi di colonna (tranne quando viene usato l'attributo `Column`). Il modello `Student` usa il nome `FirstMidName` per il campo first-name (Nome) perché il campo potrebbe contenere anche un secondo nome.

Con l'attributo `[Column]`, per `Student.FirstMidName` nel modello di dati viene eseguito il mapping alla colonna `FirstName` della tabella `Student`. L'aggiunta dell'attributo `Column` modifica il modello che supporta `SchoolContext`. Il modello che supporta `SchoolContext` non corrisponde più al database. Questa discrepanza verrà risolta aggiungendo una migrazione più avanti in questa esercitazione.

### <a name="the-required-attribute"></a>Attributo Required

```csharp
[Required]
```

L'attributo `Required` rende obbligatori i campi delle proprietà del nome. L'attributo `Required` non è necessario per i tipi che non ammettono valori Null come i tipi valore (ad esempio, `DateTime`, `int` e `double`). I tipi che non possono essere null vengono considerati automaticamente come campi obbligatori.

L'attributo `Required` deve essere usato con `MinimumLength` per l'applicazione di `MinimumLength`.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

`MinimumLength` e `Required` consentono spazi vuoti per soddisfare la convalida. Usare l'attributo `RegularExpression` per il controllo completo sulla stringa.

### <a name="the-display-attribute"></a>Attributo Display

```csharp
[Display(Name = "Last Name")]
```

L'attributo `Display` specifica che la didascalia per le caselle di testo deve essere "First Name" (Nome), "Last Name" (Cognome), "Full Name" (Nome e cognome) ed "Enrollment Date" (Data di iscrizione). Nelle didascalie predefinite le parole non erano divise da nessuno spazio, ad esempio "LastName".

### <a name="create-a-migration"></a>Creare una migrazione

Eseguire l'app e passare alla pagina Students (Studenti). Viene generata un'eccezione. Con l'attributo `[Column]` EF si aspetta di trovare una colonna denominata `FirstName`, ma il nome della colonna nel database è ancora `FirstMidName`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Il messaggio di errore è simile al seguente:

```
SqlException: Invalid column name 'FirstName'.
```

* Nella console di Gestione pacchetti immettere i comandi seguenti per creare una nuova migrazione e aggiornare il database:

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  Il primo di questi comandi genera il messaggio di avviso seguente:

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  L'avviso viene generato perché i campi nome ora sono limitati a 50 caratteri. Se un nome nel database ha più di 50 caratteri, i caratteri dal 51 all'ultimo andranno perduti.

* Aprire la tabella Student (Studente) in SSOX:

  ![Tabella Students (Studenti) in SSOX dopo le migrazioni](complex-data-model/_static/ssox-after-migration.png)

  Prima dell'applicazione della migrazione, le colonne del nome erano di tipo [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Ora le colonne del nome sono di tipo `nvarchar(50)`. Il nome della colonna è cambiato da `FirstMidName` a `FirstName`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Il messaggio di errore è simile al seguente:

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* Aprire una finestra di comando nella cartella di progetto. Immettere i comandi seguenti per creare una nuova migrazione e aggiornare il database:

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  Il comando database update visualizza un errore simile all'esempio seguente:

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

Per questa esercitazione, il modo per superare questo errore consiste nell'eliminare e creare nuovamente la migrazione iniziale. Per altre informazioni, vedere la nota di avviso per SQLite all'inizio dell'[esercitazione sulle migrazioni](xref:data/ef-rp/migrations).

* Eliminare la cartella *Migrations*.
* Eseguire i comandi seguenti per eliminare il database, creare una nuova migrazione iniziale e applicare la migrazione:

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* Esaminare la tabella Student nello strumento SQLite. La colonna FirstMidName è ora FirstName.

---

* Eseguire l'app e passare alla pagina Students (Studenti).
* Si noti che l'ora non viene inclusa nell'input o visualizzata insieme alla data.
* Selezionare **Create New** (Crea nuovo) e provare immettere un nome di lunghezza superiore a 50 caratteri.

> [!Note]
> Nelle sezioni seguenti la compilazione dell'app genera errori del compilatore in alcune fasi. Le istruzioni specificano quando compilare l'applicazione.

## <a name="the-instructor-entity"></a>Entità Instructor

![Entità Instructor](complex-data-model/_static/instructor-entity.png)

Creare *Models/Instructor.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

Un'unica riga può ospitare più attributi. Gli attributi `HireDate` possono essere scritti come segue:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a>Proprietà di navigazione

Le proprietà `CourseAssignments` e `OfficeAssignment` sono proprietà di navigazione.

Un insegnante può tenere un numero qualsiasi di corsi, pertanto `CourseAssignments` è definita come raccolta.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Un insegnante può avere al massimo un ufficio, quindi la proprietà `OfficeAssignment` contiene una singola entità `OfficeAssignment`. `OfficeAssignment` è null se non è assegnato nessun ufficio.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a>Entità OfficeAssignment

![Entità OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Creare *Models/OfficeAssignment.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Attributo Key

L'attributo `[Key]` viene usato per identificare una proprietà come chiave primaria (PK, Primary Key) quando il nome della proprietà è diverso da classnameID o ID.

È una relazione uno-a-zero-o-uno tra le entità `Instructor` e `OfficeAssignment`. L'assegnazione di un ufficio esiste solo in relazione all'insegnante al quale viene assegnato l'ufficio. La chiave primaria `OfficeAssignment` è anche la chiave esterna (FK, Foreign Key) per l'entità `Instructor`.

EF Core non è in grado di riconoscere automaticamente `InstructorID` come chiave primaria di `OfficeAssignment` perché `InstructorID` non segue la convenzione di denominazione ID o classnameID. Di conseguenza l'attributo `Key` viene usato per identificare l'entità `InstructorID` come chiave primaria:

```csharp
[Key]
public int InstructorID { get; set; }
```

Per impostazione predefinita EF Core considera la chiave come non generata dal database, perché la colonna è destinata a una relazione di identificazione.

### <a name="the-instructor-navigation-property"></a>Proprietà di navigazione Instructor

La proprietà di navigazione `Instructor.OfficeAssignment` può essere Null perché potrebbe non essere presente una riga `OfficeAssignment` per un determinato insegnante. Un insegnante potrebbe non avere un ufficio assegnato.

La proprietà di navigazione `OfficeAssignment.Instructor` avrà sempre un'entità Instructor perché il tipo `InstructorID` della chiave esterna è `int`, ovvero un tipo valore che non ammette valori Null. Un'assegnazione di ufficio non può esistere senza un insegnante.

Quando un'entità `Instructor` dispone di un'entità `OfficeAssignment` correlata, ogni entità include un riferimento all'altra entità nella relativa proprietà di navigazione.

## <a name="the-course-entity"></a>Entità Course

![Entità Course](complex-data-model/_static/course-entity.png)

Aggiornare *Models/Course.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

L'entità `Course` dispone di una proprietà chiave esterna (FK) `DepartmentID`. `DepartmentID` fa riferimento all'entità `Department` correlata. L'entità `Course` dispone di una proprietà di navigazione `Department`.

EF Core non richiede una proprietà chiave esterna per un modello di dati quando il modello include una proprietà di navigazione per un'entità correlata. EF Core crea automaticamente le chiavi esterne nel database quando sono necessarie. EF Core crea [proprietà nascoste](/ef/core/modeling/shadow-properties) per le chiavi esterne create automaticamente. Includere la chiave esterna in modo esplicito nel modello di dati, tuttavia, può rendere più semplici ed efficienti gli aggiornamenti. Si consideri ad esempio un modello in cui la proprietà chiave esterna `DepartmentID`*non* è inclusa. Quando un'entità Course viene recuperata per la modifica:

* La proprietà `Department` è Null se non viene caricata in modo esplicito.
* Per aggiornare l'entità Course, è in primo luogo necessario recuperare l'entità `Department`.

Quando la proprietà chiave esterna `DepartmentID` è inclusa nel modello di dati, non è necessario recuperare l'entità `Department` prima di un aggiornamento.

### <a name="the-databasegenerated-attribute"></a>Attributo DatabaseGenerated

L'attributo `[DatabaseGenerated(DatabaseGeneratedOption.None)]` indica che la chiave primaria viene resa disponibile dall'applicazione anziché essere generata dal database.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Per impostazione predefinita, EF Core presuppone che i valori di chiave primaria vengano generati dal database. La generazione nel database è in genere l'approccio migliore. Per le entità `Course` la chiave primaria viene specificata dall'utente. Un esempio può essere un numero di corso, quale la serie 1000 per il reparto di matematica o la serie 2000 per il reparto di lingua inglese.

L'attributo `DatabaseGenerated` può essere usato anche per generare valori predefiniti. Ad esempio, il database può generare automaticamente un campo data per registrare la data di creazione o aggiornamento di una riga. Per altre informazioni, vedere [Generated Properties](/ef/core/modeling/generated-properties) (Proprietà generate).

### <a name="foreign-key-and-navigation-properties"></a>Proprietà chiave esterna e di navigazione

Le proprietà chiave esterna (FK) e le proprietà di navigazione nell'entità `Course` riflettono le relazioni seguenti:

Un corso viene assegnato a un solo reparto, pertanto è presente una chiave esterna `DepartmentID` e una proprietà di navigazione `Department`.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Un corso può avere un numero qualsiasi di studenti iscritti, pertanto la proprietà di navigazione `Enrollments` è una raccolta:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Un corso può essere impartito da più insegnanti, pertanto la proprietà di navigazione `CourseAssignments` è una raccolta:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` viene illustrato [più avanti](#many-to-many-relationships).

## <a name="the-department-entity"></a>Entità Department

![Entità reparto](complex-data-model/_static/department-entity.png)

Creare *Models/Department.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a>Attributo Column

In precedenza l'attributo `Column` è stato usato per modificare il mapping del nome di colonna. Nel codice dell'entità `Department` l'attributo `Column` viene usato per modificare il mapping dei tipi di dati SQL. La colonna `Budget` viene definita usando il tipo SQL Server money nel database:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

In genere il mapping di colonne non è necessario. EF Core sceglie il tipo di dati di SQL Server appropriato in base al tipo CLR della proprietà. Il tipo CLR `decimal` esegue il mapping a un tipo SQL Server `decimal`. `Budget` è associato alla valuta e il tipo di dati money è più adatto per la valuta.

### <a name="foreign-key-and-navigation-properties"></a>Proprietà chiave esterna e di navigazione

Le proprietà chiave esterna e le proprietà di navigazione riflettono le relazioni seguenti:

* Un reparto può avere o non avere un amministratore.
* Un amministratore è sempre un insegnante. Di conseguenza la proprietà `InstructorID` è inclusa come chiave esterna per l'entità `Instructor`.

La proprietà di navigazione è denominata `Administrator` ma contiene un'entità `Instructor`:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Il punto interrogativo (?) nel codice precedente specifica che la proprietà è nullable.

Un reparto può avere molti corsi, pertanto è disponibile una proprietà di navigazione Courses:

```csharp
public ICollection<Course> Courses { get; set; }
```

per convenzione, EF Core abilita l'eliminazione a catena per le chiavi esterne non nullable e per le relazioni molti-a-molti. Questo comportamento predefinito può generare regole di eliminazione a catena circolari. Quando viene aggiunta una migrazione, le regole di eliminazione a catena circolari causano un'eccezione.

Ad esempio, se la definizione della proprietà `Department.InstructorID` specifica che non ammette valori Null, EF Core configurerebbe una regola di eliminazione a catena. In tal caso, il dipartimento verrebbe eliminato in seguito all'eliminazione dell'insegnante assegnato come amministratore. In questo scenario, una regola Restrict potrebbe essere più sensata. La seguente [API fluent](#fluent-api-alternative-to-attributes) imposta una regola di restrizione e disabilita l'eliminazione a catena.

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a>Entità Enrollment (Iscrizione)

Un record di iscrizione è relativo a un solo corso frequentato da un solo studente.

![Entità Enrollment](complex-data-model/_static/enrollment-entity.png)

Aggiornare *Models/Enrollment.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Proprietà chiave esterna e di navigazione

Le proprietà chiave esterna e le proprietà di navigazione riflettono le relazioni seguenti:

Un record di iscrizione è relativo a un solo corso, pertanto sono presenti una proprietà chiave esterna `CourseID` e una proprietà di navigazione `Course`:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Un record di iscrizione è relativo a un solo studente, pertanto sono presenti una proprietà chiave esterna `StudentID` e una proprietà di navigazione `Student`:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relazioni molti-a-molti

Esiste una relazione molti-a-molti tra le entità `Student` e `Course`. L'entità `Enrollment` funziona come una tabella di join molti-a-molti *con payload* nel database. "Con payload" significa che la tabella `Enrollment` contiene dati aggiuntivi oltre alle chiavi esterne delle tabelle di join (in questo caso la chiave primaria e `Grade`).

La figura seguente illustra l'aspetto di queste relazioni in un diagramma di entità. (Questo diagramma è stato generato utilizzando [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) per EF 6.x. La creazione del diagramma non fa parte dell'esercitazione.

![Relazione molti-a-molti Student-Course (Studente-Corso)](complex-data-model/_static/student-course.png)

Ogni riga della relazione inizia con un 1 e termina con un asterisco (*), per indicare una relazione uno-a-molti.

Se la tabella `Enrollment` non include informazioni sul livello, è sufficiente che contenga le due chiavi esterne `CourseID` e `StudentID`. Una tabella di join molti-a-molti senza payload è anche detta tabella di join pura (PJT, Pure Join Table).

Le entità `Instructor` e `Course` hanno una relazione molti-a-molti con una tabella di join pura.

Nota: le tabelle di join implicite per le relazioni molti-a-molti sono supportate in EF 6.x ma non in EF Core. Per altre informazioni, vedere [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/) (Relazioni molti-a-molti in EF Core 2.0).

## <a name="the-courseassignment-entity"></a>Entità CourseAssignment

![Entità CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Creare *Models/CourseAssignment.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

La relazione molti-a-molti tra insegnanti e corsi richiede una tabella di join e l'entità per tale tabella di join è CourseAssignment.

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

È pratica comune assegnare a un'entità di join un nome `EntityName1EntityName2`. Ad esempio la tabella di join istruttori-corsi che usa questa convenzione sarebbe `CourseInstructor`. È tuttavia consigliabile usare un nome che descrive la relazione.

I modelli di dati iniziano come strutture semplici, quindi le loro dimensioni aumentano. Le tabelle di join senza payload (PJT) si evolvono spesso per includere il payload. Se si assegna inizialmente un nome di entità descrittivo, non sarà necessario modificarlo quando la tabella di join cambia. Idealmente l'entità di join dovrebbe avere il proprio nome naturale (se possibile composto da un'unica parola) nel dominio di business. Ad esempio Books (Documentazione) e Customers (Clienti) potrebbero essere collegati mediante un'entità di join Ratings (Valutazioni). Per la relazione molti-a-molti Instructor-to-Courses `CourseAssignment` è preferibile a `CourseInstructor`.

### <a name="composite-key"></a>Chiave composta

Le due chiavi esterne in `CourseAssignment` (`InstructorID` e `CourseID`) identificano insieme in modo univoco ogni riga della tabella `CourseAssignment`. `CourseAssignment` non richiede una chiave primaria dedicata. Le proprietà `InstructorID` e `CourseID` funzionano come una chiave primaria composta. L'unico modo per specificare chiavi primarie composte in EF Core è l'*API Fluent*. La sezione successiva illustra come configurare la chiave primaria composta.

La chiave composta garantisce che:

* Sono consentite più righe per un corso.
* Sono consentite più righe per un insegnante.
* Non sono consentite più righe per lo stesso insegnante e lo stesso corso.

L'entità di join `Enrollment` definisce la propria chiave primaria, pertanto sono possibili i duplicati di questo tipo. Per evitare tali duplicati:

* Aggiungere un indice univoco ai campi chiave esterna oppure
* Configurare `Enrollment` con una chiave primaria composta simile a `CourseAssignment`. Per altre informazioni, vedere [Indexes](/ef/core/modeling/indexes) (Indici).

## <a name="update-the-database-context"></a>Aggiornare il contesto di database

Aggiornare *Data/SchoolContext.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

Questo codice aggiunge le nuove entità e configura la chiave primaria composta dell'entità `CourseAssignment`.

## <a name="fluent-api-alternative-to-attributes"></a>Alternativa API Fluent agli attributi

Il metodo `OnModelCreating` nel codice precedente usa l'*API Fluent* per configurare il comportamento di EF Core. L'API è denominata "API Fluent" perché viene spesso usata unendo una serie di chiamate di metodi in un'unica istruzione. Il [codice seguente](/ef/core/modeling/#use-fluent-api-to-configure-a-model) è un esempio di API Fluent:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

In questa esercitazione l'API Fluent viene usata solo per le operazioni di mapping del database che non possono essere eseguite con gli attributi. Tuttavia l'API Fluent può specificare la maggior parte delle regole di formattazione, convalida e mapping specificabili tramite gli attributi.

Alcuni attributi quali `MinimumLength` non possono essere applicati con l'API Fluent. `MinimumLength` non modifica lo schema, ma si limita ad applicare una regola di convalida per la lunghezza minima.

Alcuni sviluppatori preferiscono usare solo l'API Fluent, per dare un aspetto "ordinato" alle classi di entità. È possibile combinare gli attributi e l'API Fluent. Alcune configurazioni possono essere eseguite solo con l'API Fluent (specificando una chiave primaria composta). Altre configurazioni possono essere eseguite solo con gli attributi (`MinimumLength`). La procedura consigliata per l'uso dell'API Fluent o degli attributi è la seguente:

* Scegliere uno dei due approcci.
* Usare l'approccio scelto con la massima coerenza possibile.

Alcuni degli attributi usati in questa esercitazione vengono usati per:

* Solo convalida (ad esempio `MinimumLength`).
* Solo configurazione di EF Core (ad esempio `HasKey`).
* Convalida e configurazione di EF Core (ad esempio `[StringLength(50)]`).

Per altre informazioni sul confronto tra attributi e API Fluent, vedere [Metodi di configurazione](/ef/core/modeling/).

## <a name="entity-diagram"></a>Diagramma dell'entità

La figura seguente visualizza il diagramma creato da EF Power Tools per il modello School completato.

![Diagramma dell'entità](complex-data-model/_static/diagram.png)

Il diagramma precedente mostra quanto segue:

* Più linee di relazione uno-a-molti (da 1 a \*).
* La linea di relazione uno-a-zero-o-uno (da 1 a 0..1) tra le entità `Instructor` e `OfficeAssignment`.
* La linea di relazione zero-o-uno-a-molti (da 0..1 a *) tra le entità `Instructor` e `Department`.

## <a name="seed-the-database"></a>Specificare il valore di inizializzazione del database

Aggiornare il codice in *Data/DbInitializer.cs*:

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

Il codice precedente offre i dati di inizializzazione per le nuove entità. La maggior parte di questo codice crea nuovi oggetti entità e carica dati di esempio. I dati di esempio vengono usati per i test. Visualizzare `Enrollments` e `CourseAssignments` per alcuni esempi del modo in cui può essere impostato il valore di inizializzazione per le tabelle join molti-a-molti.

## <a name="add-a-migration"></a>Aggiungere una migrazione

Compilare il progetto.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nella console di Gestione pacchetti eseguire il comando seguente.

```powershell
Add-Migration ComplexDataModel
```

Il comando precedente visualizza un avviso sulla possibile perdita di dati.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

Se viene eseguito il comando `database update`, viene generato l'errore seguente:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

Nella prossima sezione viene descritto come procedere per questo errore.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Se si aggiunge una migrazione e si esegue il comando `database update`, viene generato l'errore seguente:

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

Nella sezione successiva si vedrà come evitare questo errore.

---

## <a name="apply-the-migration-or-drop-and-re-create"></a>Applicare la migrazione o eliminare e ricreare

Ora che è disponibile un database esistente, è necessario preoccuparsi di come applicare eventuali modifiche. Questa esercitazione illustra due alternative:

* [Eliminare e ricreare il database](#drop). Scegliere questa sezione se si usa SQLite.
* [Applicare la migrazione al database esistente.](#applyexisting) Le istruzioni riportate in questa sezione funzionano solo per SQL Server e **non per SQLite**. 

Entrambe le scelte funzionano per SQL Server. Anche se il metodo che prevede l'applicazione della migrazione è più complesso e richiede più tempo, si tratta dell'approccio consigliato per gli ambienti di produzione reali. 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a>Eliminare e ricreare il database

[Ignorare questa sezione](#apply-the-migration) se si usa SQL Server e si vuole adottare l'approccio con applicazione della migrazione nella sezione seguente.

Per forzare la creazione di un nuovo database da parte di EF Core, rimuovere e aggiornare il database:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Nella **console di Gestione pacchetti** eseguire il comando seguente:

  ```powershell
  Drop-Database
  ```

* Eliminare la cartella *Migrations* e quindi eseguire il comando seguente:

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Aprire una finestra di comando e passare alla cartella del progetto. La cartella del progetto contiene il file *ContosoUniversity.csproj*.

* Eseguire il comando seguente:

  ```dotnetcli
  dotnet ef database drop --force
  ```

* Eliminare la cartella *Migrations* e quindi eseguire il comando seguente:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Eseguire l'app. Quando si esegue l'app viene eseguito il metodo `DbInitializer.Initialize`. `DbInitializer.Initialize` popola il nuovo database.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aprire il database in SSOX:

* Se SSOX è stato aperto in precedenza, fare clic sul pulsante **Aggiorna**.
* Espandere il nodo **Tabelle**. Vengono visualizzate le tabelle create.

  ![Tabelle in SSOX](complex-data-model/_static/ssox-tables.png)

* Esaminare la tabella **CourseAssignment**:

  * Fare clic con il pulsante destro del mouse sulla tabella **CourseAssignment** e selezionare **Visualizza dati**.
  * Verificare che la tabella **CourseAssignment** contenga dati.

  ![Dati CourseAssignment in SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Usare lo strumento SQLite per esaminare il database:

* Nuove tabelle e colonne.
* Dati definiti nelle tabelle, ad esempio la tabella **CourseAssignment**.

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a>Applicare la migrazione

Questa sezione è facoltativa. Questa procedura funziona solo per SQL Server Local DB e solo se è stata ignorata la sezione [Eliminare e ricreare il database](#drop) precedente.

Quando le migrazioni vengono eseguite con dati esistenti, possono essere presenti vincoli di chiave esterna che non vengono soddisfatti con i dati esistenti. Con i dati di produzione, è necessario eseguire passaggi per la migrazione dei dati esistenti. Questa sezione visualizza un esempio di correzione delle violazioni dei vincoli di chiave esterna. Non apportare queste modifiche al codice senza un backup. Non apportare queste modifiche al codice se è stata completata la sezione precedente [Eliminare e ricreare il database](#drop).

Il file *{timestamp}_ComplexDataModel.cs* contiene il codice seguente:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

Il codice precedente aggiunge una chiave esterna non nullable `DepartmentID` alla tabella `Course`. Il database dell'esercitazione precedente contiene righe in `Course`, pertanto la tabella non può essere aggiornata mediante le migrazioni.

Per far sì che la migrazione `ComplexDataModel` funzioni con i dati esistenti:

* Modificare il codice per assegnare un valore predefinito alla nuova colonna (`DepartmentID`).
* Creare un reparto fittizio denominato "Temp" che assume il ruolo di reparto predefinito.

#### <a name="fix-the-foreign-key-constraints"></a>Risolvere i vincoli della chiave esterna

Nella classe della migrazione `ComplexDataModel` aggiornare il metodo `Up`:

* Aprire il file *{timestamp}_ComplexDataModel.cs*.
* Impostare come commento la riga di codice che aggiunge la colonna `DepartmentID` alla tabella `Course`.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Aggiungere il codice evidenziato seguente. Il nuovo codice viene inserito dopo il blocco `.CreateTable( name: "Department"`:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

Con le modifiche precedenti, le righe `Course` esistenti saranno correlate al dipartimento "Temp" dopo l'esecuzione del metodo `ComplexDataModel.Up`.

La modalità di gestione della situazione illustrata di seguito è semplificata per questa esercitazione. Un'app di produzione:

* Includerà codice o script per l'aggiunta di righe `Department` e righe `Course` correlate alle nuove righe `Department`.
* Non userà il reparto "Temp" o il valore predefinito per `Course.DepartmentID`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Nella **console di Gestione pacchetti** eseguire il comando seguente:

  ```powershell
  Update-Database
  ```

Dato che il metodo `DbInitializer.Initialize` è progettato per funzionare solo con un database vuoto, usare SSOX per eliminare tutte le righe nelle tabelle Student e Course. (L'eliminazione a catena si occuperà della tabella Enrollment.)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Se si usa SQL Server Local DB con Visual Studio Code, eseguire il comando seguente:

  ```dotnetcli
  dotnet ef database update
  ```

---

Eseguire l'app. Quando si esegue l'app viene eseguito il metodo `DbInitializer.Initialize`. `DbInitializer.Initialize` popola il nuovo database.

## <a name="next-steps"></a>Passaggi successivi

Nelle due esercitazioni successive viene illustrato come leggere e aggiornare i dati correlati.

> [!div class="step-by-step"]
> [Esercitazione](xref:data/ef-rp/migrations)
> precedente[Esercitazione successiva](xref:data/ef-rp/read-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Nelle esercitazioni precedenti è stato usato un modello di dati semplice costituito da tre entità. Contenuto dell'esercitazione:

* Vengono aggiunte altre entità e relazioni.
* Il modello di dati viene personalizzato specificando regole di formattazione, convalida e mapping del database.

Le classi di entità per il modello di dati completato sono visualizzate nella figura seguente:

![Diagramma dell'entità](complex-data-model/_static/diagram.png)

Se si verificano problemi che non si è in grado di risolvere, scaricare l'[app completa](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="customize-the-data-model-with-attributes"></a>Personalizzare il modello di dati usando gli attributi

In questa sezione il modello di dati viene personalizzato usando gli attributi.

### <a name="the-datatype-attribute"></a>Attributo DataType

Attualmente le pagine Student (Studente) visualizzano l'ora associata alla data di iscrizione. In genere i campi data visualizzano solo la data e non l'ora.

Aggiornare *Models/Student.cs* con il codice evidenziato seguente:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

L'attributo [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) indica un tipo di dati più specifico rispetto al tipo intrinseco del database. In questo caso deve essere visualizzata solo la data e non la data e l'ora. Il [DataType Enumerazione](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) fornisce per molti tipi di dati, ad esempio Data, ora, PhoneNumber, valuta, EmailAddress e così via. L'attributo `DataType` può anche consentire all'app di fornire automaticamente funzionalità specifiche del tipo. Ad esempio:

* Il collegamento `mailto:` viene creato automaticamente per `DataType.EmailAddress`.
* Il selettore data viene incluso per `DataType.Date` nella maggior parte dei browser.

L'attributo `DataType` genera attributi HTML 5 `data-` supportati dai browser HTML 5. Gli attributi `DataType` non garantiscono la convalida.

`DataType.Date` non specifica il formato della data visualizzata. Per impostazione predefinita il campo data viene visualizzato in base ai formati predefiniti per il valore [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) del server.

L'attributo `DisplayFormat` viene usato per specificare in modo esplicito il formato della data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

L'impostazione `ApplyFormatInEditMode` specifica che la formattazione deve essere applicata anche all'interfaccia utente di modifica. Alcuni campi non devono usare `ApplyFormatInEditMode`. Ad esempio il simbolo di valuta in genere non deve essere visualizzato in una casella di testo di modifica.

L'attributo `DisplayFormat` può essere usato da solo. In genere l'uso dell'attributo `DataType` con l'attributo `DisplayFormat` è consigliato. L'attributo `DataType` offre la semantica dei dati anziché specificarne il rendering in una schermata. L'attributo `DataType` offre i vantaggi seguenti che non sono disponibili in `DisplayFormat`:

* Il browser può abilitare le funzionalità HTML5. Ad esempio può visualizzare un controllo di calendario, il simbolo della valuta appropriato per le impostazioni locali, i collegamenti alla posta elettronica, alcune istanze di convalida lato client e così via.
* Per impostazione predefinita, il browser esegue il rendering dei dati usando il formato corretto in base alle impostazioni locali.

Per ulteriori informazioni, vedere [ \<l'input> documentazione](xref:mvc/views/working-with-forms#the-input-tag-helper)di Esempio di tag .

Eseguire l'app. Passare alla pagina Students Index (Indice studenti). L'ora non viene più visualizzata. Ogni visualizzazione che usa il modello `Student` visualizza la data senza l'ora.

![Pagina Students Index (Indice studenti) con date e senza ore](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Attributo StringLength

È possibile specificare regole di convalida dei dati e messaggi di errore di convalida usando gli attributi. L'attributo [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) specifica il numero minimo e massimo di caratteri consentiti in un campo dati. L'attributo `StringLength` offre anche la convalida lato client e lato server. Il valore minimo non ha alcun effetto sullo schema del database.

Aggiornare il modello `Student` con il codice seguente:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Il codice precedente limita i nomi a un massimo di 50 caratteri. L'attributo `StringLength` non impedisce a un utente di immettere spazi vuoti per un nome. L'attributo [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) viene usato per applicare restrizioni all'input. Ad esempio il codice seguente richiede che il primo carattere sia maiuscolo e i caratteri rimanenti siano caratteri alfabetici:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Eseguire l'app:

* Passare alla pagina Student (Studente).
* Selezionare **Crea nuovo** e immettere un nome di lunghezza superiore a 50 caratteri.
* Quando si fa clic su **Crea** la convalida lato client visualizza un messaggio di errore.

![Pagina Students Index (Indice studenti) con errori di lunghezza stringa](complex-data-model/_static/string-length-errors.png)

In **Esplora oggetti di SQL Server** (SSOX) aprire il designer della tabella **Student** (Studente) facendo doppio clic sulla tabella.

![Tabella Student (Studenti) in SSOX prima delle migrazioni](complex-data-model/_static/ssox-before-migration.png)

L'immagine precedente visualizza lo schema per la tabella `Student`. I campi nome hanno il tipo `nvarchar(MAX)` perché migrations non è stato eseguito nel database. Quando le istruzioni migrations verranno eseguite, più avanti in questa esercitazione, i campi nome diventeranno `nvarchar(50)`.

### <a name="the-column-attribute"></a>Attributo Column

Gli attributi possono controllare il mapping delle classi e delle proprietà nel database. In questa sezione l'attributo `Column` viene usato per il mapping del nome della proprietà `FirstMidName` su "FirstName" nel database.

Quando viene creato il database, i nomi delle proprietà nel modello vengono usati per i nomi di colonna (tranne quando viene usato l'attributo `Column`).

Il modello `Student` usa il nome `FirstMidName` per il campo first-name (Nome) perché il campo potrebbe contenere anche un secondo nome.

Aggiornare il file *Student.cs* con il codice evidenziato seguente:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Con la modifica precedente, `Student.FirstMidName` nell'app esegue il mapping alla colonna `FirstName` della tabella `Student`.

L'aggiunta dell'attributo `Column` modifica il modello che supporta `SchoolContext`. Il modello che supporta `SchoolContext` non corrisponde più al database. Se l'app viene eseguita prima di applicare migrations, viene generata l'eccezione seguente:

```
SqlException: Invalid column name 'FirstName'.
```

Per aggiornare il database:

* Compilare il progetto.
* Aprire una finestra di comando nella cartella di progetto. Immettere i comandi seguenti per creare una nuova migrazione e aggiornare il database:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

Il comando `migrations add ColumnFirstName` genera il messaggio di avviso seguente:

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

L'avviso viene generato perché i campi nome ora sono limitati a 50 caratteri. Se un nome nel database ha più di 50 caratteri, i caratteri dal 51 all'ultimo andranno perduti.

* Testare l'app.

Aprire la tabella Student (Studente) in SSOX:

![Tabella Students (Studenti) in SSOX dopo le migrazioni](complex-data-model/_static/ssox-after-migration.png)

Prima dell'applicazione della migrazione, le colonne del nome erano di tipo [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Ora le colonne del nome sono di tipo `nvarchar(50)`. Il nome della colonna è cambiato da `FirstMidName` a `FirstName`.

> [!Note]
> Nella sezione seguente la compilazione dell'applicazione genera errori del compilatore in alcune fasi. Le istruzioni specificano quando compilare l'applicazione.

## <a name="student-entity-update"></a>Aggiornamento dell'entità Student

![Entità Student](complex-data-model/_static/student-entity.png)

Aggiornare *Models/Student.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Attributo Required

L'attributo `Required` rende obbligatori i campi delle proprietà del nome. L'attributo `Required` non è necessario per i tipi non nullable, ad esempio per i tipi valore (`DateTime`, `int`, `double` e così via). I tipi che non possono essere null vengono considerati automaticamente come campi obbligatori.

L'attributo `Required` può essere sostituito con un parametro di lunghezza minima nell'attributo `StringLength`:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Attributo Display

L'attributo `Display` specifica che la didascalia per le caselle di testo deve essere "First Name" (Nome), "Last Name" (Cognome), "Full Name" (Nome e cognome) ed "Enrollment Date" (Data di iscrizione). Nelle didascalie predefinite le parole non erano divise da nessuno spazio, ad esempio "LastName".

### <a name="the-fullname-calculated-property"></a>Proprietà calcolata FullName

`FullName` è una proprietà calcolata che restituisce un valore creato concatenando altre due proprietà. `FullName` non è impostabile e include solo una funzione di accesso get. Nel database non viene creata una colonna `FullName`.

## <a name="create-the-instructor-entity"></a>Creare l'entità Instructor

![Entità Instructor](complex-data-model/_static/instructor-entity.png)

Creare *Models/Instructor.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

Un'unica riga può ospitare più attributi. Gli attributi `HireDate` possono essere scritti come segue:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Proprietà di navigazione CourseAssignments e OfficeAssignment

Le proprietà `CourseAssignments` e `OfficeAssignment` sono proprietà di navigazione.

Un insegnante può tenere un numero qualsiasi di corsi, pertanto `CourseAssignments` è definita come raccolta.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Se una proprietà di navigazione contiene più entità:

* Deve essere un tipo di elenco in cui le voci possono essere aggiunte, eliminate e aggiornate.

I tipi di proprietà di navigazione includono:

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

Se è specificato `ICollection<T>`, per impostazione predefinita EF Core crea una raccolta `HashSet<T>`.

L'entità `CourseAssignment` è illustrata nella sezione sulle relazioni molti-a-molti.

Le regole business di Contoso University specificano che un insegnante non può avere più di un ufficio. La proprietà `OfficeAssignment` contiene un'unica entità `OfficeAssignment`. `OfficeAssignment` è null se non è assegnato nessun ufficio.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>Creare l'entità OfficeAssignment

![Entità OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Creare *Models/OfficeAssignment.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Attributo Key

L'attributo `[Key]` viene usato per identificare una proprietà come chiave primaria (PK, Primary Key) quando il nome della proprietà è diverso da classnameID o ID.

È una relazione uno-a-zero-o-uno tra le entità `Instructor` e `OfficeAssignment`. L'assegnazione di un ufficio esiste solo in relazione all'insegnante al quale viene assegnato l'ufficio. La chiave primaria `OfficeAssignment` è anche la chiave esterna (FK, Foreign Key) per l'entità `Instructor`. EF Core non riconosce automaticamente `InstructorID` come chiave primaria di `OfficeAssignment` perché:

* `InstructorID` non segue la convenzione di denominazione ID o classnameID.

Di conseguenza l'attributo `Key` viene usato per identificare l'entità `InstructorID` come chiave primaria:

```csharp
[Key]
public int InstructorID { get; set; }
```

Per impostazione predefinita EF Core considera la chiave come non generata dal database, perché la colonna è destinata a una relazione di identificazione.

### <a name="the-instructor-navigation-property"></a>Proprietà di navigazione Instructor

La proprietà di navigazione `OfficeAssignment` per l'entità `Instructor` è nullable perché:

* I tipi di riferimento (ad esempio le classi) sono nullable.
* Un insegnante potrebbe non avere un ufficio assegnato.

L'entità `OfficeAssignment` ha una proprietà di navigazione `Instructor` non nullable perché:

* `InstructorID` è non nullable.
* Un'assegnazione di ufficio non può esistere senza un insegnante.

Quando un'entità `Instructor` dispone di un'entità `OfficeAssignment` correlata, ogni entità include un riferimento all'altra entità nella relativa proprietà di navigazione.

L'attributo `[Required]` può essere applicato alla proprietà di navigazione `Instructor`:

```csharp
[Required]
public Instructor Instructor { get; set; }
```

Il codice precedente specifica che deve essere presente un insegnante correlato. Il codice precedente non è necessario perché la chiave esterna `InstructorID` (che è anche la chiave primaria) è non nullable.

## <a name="modify-the-course-entity"></a>Modificare l'entità Course

![Entità Course](complex-data-model/_static/course-entity.png)

Aggiornare *Models/Course.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

L'entità `Course` dispone di una proprietà chiave esterna (FK) `DepartmentID`. `DepartmentID` fa riferimento all'entità `Department` correlata. L'entità `Course` dispone di una proprietà di navigazione `Department`.

EF Core non richiede una proprietà chiave esterna per un modello di dati quando il modello dispone di una proprietà di navigazione per un'entità correlata.

EF Core crea automaticamente le chiavi esterne nel database quando sono necessarie. EF Core crea [proprietà nascoste](/ef/core/modeling/shadow-properties) per le chiavi esterne create automaticamente. Il fatto di avere la chiave esterna nel modello di dati può rendere più semplici ed efficienti gli aggiornamenti. Si consideri ad esempio un modello in cui la proprietà chiave esterna `DepartmentID`*non* è inclusa. Quando un'entità Course viene recuperata per la modifica:

* L'entità `Department` è null se non viene caricata in modo esplicito.
* Per aggiornare l'entità Course, è in primo luogo necessario recuperare l'entità `Department`.

Quando la proprietà chiave esterna `DepartmentID` è inclusa nel modello di dati, non è necessario recuperare l'entità `Department` prima di un aggiornamento.

### <a name="the-databasegenerated-attribute"></a>Attributo DatabaseGenerated

L'attributo `[DatabaseGenerated(DatabaseGeneratedOption.None)]` indica che la chiave primaria viene resa disponibile dall'applicazione anziché essere generata dal database.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Per impostazione predefinita, Core EF presuppone che i valori di chiave primaria vengano generati dal database. La generazione dei valori di chiave primaria nel database è in genere l'approccio migliore. Per le entità `Course` la chiave primaria viene specificata dall'utente. Un esempio può essere un numero di corso, quale la serie 1000 per il reparto di matematica o la serie 2000 per il reparto di lingua inglese.

L'attributo `DatabaseGenerated` può essere usato anche per generare valori predefiniti. Ad esempio il database può generare automaticamente un campo data per registrare la data di creazione o aggiornamento di una riga. Per altre informazioni, vedere [Generated Properties](/ef/core/modeling/generated-properties) (Proprietà generate).

### <a name="foreign-key-and-navigation-properties"></a>Proprietà chiave esterna e di navigazione

Le proprietà chiave esterna (FK) e le proprietà di navigazione nell'entità `Course` riflettono le relazioni seguenti:

Un corso viene assegnato a un solo reparto, pertanto è presente una chiave esterna `DepartmentID` e una proprietà di navigazione `Department`.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Un corso può avere un numero qualsiasi di studenti iscritti, pertanto la proprietà di navigazione `Enrollments` è una raccolta:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Un corso può essere impartito da più insegnanti, pertanto la proprietà di navigazione `CourseAssignments` è una raccolta:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` viene illustrato [più avanti](#many-to-many-relationships).

## <a name="create-the-department-entity"></a>Creare l'entità Department

![Entità reparto](complex-data-model/_static/department-entity.png)

Creare *Models/Department.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Attributo Column

In precedenza l'attributo `Column` è stato usato per modificare il mapping del nome di colonna. Nel codice dell'entità `Department` l'attributo `Column` viene usato per modificare il mapping dei tipi di dati SQL. La colonna `Budget` viene definita usando il tipo SQL Server money nel database:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

In genere il mapping di colonne non è necessario. Generalmente EF Core sceglie il tipo di dati SQL Server appropriato in base al tipo CLR della proprietà. Il tipo CLR `decimal` esegue il mapping a un tipo SQL Server `decimal`. `Budget` è associato alla valuta e il tipo di dati money è più adatto per la valuta.

### <a name="foreign-key-and-navigation-properties"></a>Proprietà chiave esterna e di navigazione

Le proprietà chiave esterna e le proprietà di navigazione riflettono le relazioni seguenti:

* Un reparto può avere o non avere un amministratore.
* Un amministratore è sempre un insegnante. Di conseguenza la proprietà `InstructorID` è inclusa come chiave esterna per l'entità `Instructor`.

La proprietà di navigazione è denominata `Administrator` ma contiene un'entità `Instructor`:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Il punto interrogativo (?) nel codice precedente specifica che la proprietà è nullable.

Un reparto può avere molti corsi, pertanto è disponibile una proprietà di navigazione Courses:

```csharp
public ICollection<Course> Courses { get; set; }
```

Nota: per convenzione, EF Core abilita l'eliminazione a catena per le chiavi esterne non nullable e per le relazioni molti-a-molti. L'eliminazione a catena può generare regole di eliminazione a catena circolari. Quando viene aggiunta una migrazione, le regole di eliminazione a catena circolari determinano un'eccezione.

Ad esempio, se la proprietà `Department.InstructorID` è stata definita come nullable:

* EF Core configura una regola di eliminazione a catena per eliminare il reparto quando viene eliminato l'insegnante.
* L'eliminazione del reparto quando viene eliminato l'insegnante non è il comportamento previsto.
* La seguente [API fluent](#fluent-api-alternative-to-attributes) imposterebbe una regola di restrizione anziché a catena.

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

Il codice precedente disabilita l'eliminazione a catena per la relazione reparto-insegnante.

## <a name="update-the-enrollment-entity"></a>Aggiornare l'entità Enrollment

Un record di iscrizione è relativo a un solo corso frequentato da un solo studente.

![Entità Enrollment](complex-data-model/_static/enrollment-entity.png)

Aggiornare *Models/Enrollment.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Proprietà chiave esterna e di navigazione

Le proprietà chiave esterna e le proprietà di navigazione riflettono le relazioni seguenti:

Un record di iscrizione è relativo a un solo corso, pertanto sono presenti una proprietà chiave esterna `CourseID` e una proprietà di navigazione `Course`:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Un record di iscrizione è relativo a un solo studente, pertanto sono presenti una proprietà chiave esterna `StudentID` e una proprietà di navigazione `Student`:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relazioni molti-a-molti

Esiste una relazione molti-a-molti tra le entità `Student` e `Course`. L'entità `Enrollment` funziona come una tabella di join molti-a-molti *con payload* nel database. "Con payload" significa che la tabella `Enrollment` contiene dati aggiuntivi oltre alle chiavi esterne delle tabelle di join (in questo caso la chiave primaria e `Grade`).

La figura seguente illustra l'aspetto di queste relazioni in un diagramma di entità. (Questo diagramma è stato generato utilizzando [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) per EF 6.x. La creazione del diagramma non fa parte dell'esercitazione.

![Relazione molti-a-molti Student-Course (Studente-Corso)](complex-data-model/_static/student-course.png)

Ogni riga della relazione inizia con un 1 e termina con un asterisco (*), per indicare una relazione uno-a-molti.

Se la tabella `Enrollment` non include informazioni sul livello, è sufficiente che contenga le due chiavi esterne `CourseID` e `StudentID`. Una tabella di join molti-a-molti senza payload è anche detta tabella di join pura (PJT, Pure Join Table).

Le entità `Instructor` e `Course` hanno una relazione molti-a-molti con una tabella di join pura.

Nota: le tabelle di join implicite per le relazioni molti-a-molti sono supportate in EF 6.x ma non in EF Core. Per altre informazioni, vedere [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/) (Relazioni molti-a-molti in EF Core 2.0).

## <a name="the-courseassignment-entity"></a>Entità CourseAssignment

![Entità CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Creare *Models/CourseAssignment.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Instructor-to-Courses

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

La relazione molti-a-molti Instructor-to-Courses (Insegnante-Corsi):

* Richiede una tabella di join che deve essere rappresentata da un set di entità.
* È una tabella di join pura (tabella senza payload).

È pratica comune assegnare a un'entità di join un nome `EntityName1EntityName2`. Ad esempio la tabella di join Instructor-to-Courses che usa questa convenzione sarà `CourseInstructor`. È tuttavia consigliabile usare un nome che descrive la relazione.

I modelli di dati iniziano come strutture semplici, quindi le loro dimensioni aumentano. In molti casi ai join senza payload vengono assegnati payload in un secondo momento. Se si assegna inizialmente un nome di entità descrittivo, non sarà necessario modificarlo quando la tabella di join cambia. Idealmente l'entità di join dovrebbe avere il proprio nome naturale (se possibile composto da un'unica parola) nel dominio di business. Ad esempio Books (Documentazione) e Customers (Clienti) potrebbero essere collegati mediante un'entità di join Ratings (Valutazioni). Per la relazione molti-a-molti Instructor-to-Courses `CourseAssignment` è preferibile a `CourseInstructor`.

### <a name="composite-key"></a>Chiave composta

Le chiavi esterne non sono nullable. Le due chiavi esterne in `CourseAssignment` (`InstructorID` e `CourseID`) identificano insieme in modo univoco ogni riga della tabella `CourseAssignment`. `CourseAssignment` non richiede una chiave primaria dedicata. Le proprietà `InstructorID` e `CourseID` funzionano come una chiave primaria composta. L'unico modo per specificare chiavi primarie composte in EF Core è l'*API Fluent*. La sezione successiva illustra come configurare la chiave primaria composta.

La chiave composta garantisce quanto segue:

* Sono consentite più righe per un corso.
* Sono consentite più righe per un insegnante.
* Non sono consentite più righe per lo stesso insegnante e lo stesso corso.

L'entità di join `Enrollment` definisce la propria chiave primaria, pertanto sono possibili i duplicati di questo tipo. Per evitare tali duplicati:

* Aggiungere un indice univoco ai campi chiave esterna oppure
* Configurare `Enrollment` con una chiave primaria composta simile a `CourseAssignment`. Per altre informazioni, vedere [Indexes](/ef/core/modeling/indexes) (Indici).

## <a name="update-the-db-context"></a>Aggiornare il contesto del database

Aggiungere il codice evidenziato seguente al file *Data/SchoolContext.cs*:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Questo codice aggiunge le nuove entità e configura la chiave primaria composta dell'entità `CourseAssignment`.

## <a name="fluent-api-alternative-to-attributes"></a>Alternativa API Fluent agli attributi

Il metodo `OnModelCreating` nel codice precedente usa l'*API Fluent* per configurare il comportamento di EF Core. L'API è denominata "API Fluent" perché viene spesso usata unendo una serie di chiamate di metodi in un'unica istruzione. Il [codice seguente](/ef/core/modeling/#use-fluent-api-to-configure-a-model) è un esempio di API Fluent:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

In questa esercitazione l'API Fluent viene usata solo per le operazioni di mapping del database che non possono essere eseguite con gli attributi. Tuttavia l'API Fluent può specificare la maggior parte delle regole di formattazione, convalida e mapping specificabili tramite gli attributi.

Alcuni attributi quali `MinimumLength` non possono essere applicati con l'API Fluent. `MinimumLength` non modifica lo schema, ma si limita ad applicare una regola di convalida per la lunghezza minima.

Alcuni sviluppatori preferiscono usare solo l'API Fluent, per dare un aspetto "ordinato" alle classi di entità. È possibile combinare gli attributi e l'API Fluent. Alcune configurazioni possono essere eseguite solo con l'API Fluent (specificando una chiave primaria composta). Altre configurazioni possono essere eseguite solo con gli attributi (`MinimumLength`). La procedura consigliata per l'uso dell'API Fluent o degli attributi è la seguente:

* Scegliere uno dei due approcci.
* Usare l'approccio scelto con la massima coerenza possibile.

Alcuni attributi di questa esercitazione vengono usati per:

* Solo convalida (ad esempio `MinimumLength`).
* Solo configurazione di EF Core (ad esempio `HasKey`).
* Convalida e configurazione di EF Core (ad esempio `[StringLength(50)]`).

Per altre informazioni sul confronto tra attributi e API Fluent, vedere [Metodi di configurazione](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Diagramma dell'entità che visualizza le relazioni

La figura seguente visualizza il diagramma creato da EF Power Tools per il modello School completato.

![Diagramma dell'entità](complex-data-model/_static/diagram.png)

Il diagramma precedente mostra quanto segue:

* Più linee di relazione uno-a-molti (da 1 a \*).
* La linea di relazione uno-a-zero-o-uno (da 1 a 0..1) tra le entità `Instructor` e `OfficeAssignment`.
* La linea di relazione zero-o-uno-a-molti (da 0..1 a *) tra le entità `Instructor` e `Department`.

## <a name="seed-the-db-with-test-data"></a>Inizializzare il database con dati di test

Aggiornare il codice in *Data/DbInitializer.cs*:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

Il codice precedente offre i dati di inizializzazione per le nuove entità. La maggior parte di questo codice crea nuovi oggetti entità e carica dati di esempio. I dati di esempio vengono usati per i test. Visualizzare `Enrollments` e `CourseAssignments` per alcuni esempi del modo in cui può essere impostato il valore di inizializzazione per le tabelle join molti-a-molti.

## <a name="add-a-migration"></a>Aggiungere una migrazione

Compilare il progetto.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

Il comando precedente visualizza un avviso sulla possibile perdita di dati.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Se viene eseguito il comando `database update`, viene generato l'errore seguente:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>Applicare la migrazione

Ora che è disponibile un database esistente, è necessario preoccuparsi di come applicare eventuali modifiche future. Questa esercitazione illustra due approcci:

* [Eliminare e ricreare il database](#drop)
* [Applicare la migrazione al database esistente.](#applyexisting) Anche se questo metodo è più complesso e richiede più tempo, è l'approccio consigliato per gli ambienti di produzione reali. **Nota**: questa è una sezione facoltativa dell'esercitazione. È possibile eseguire i passaggi di eliminazione e ricreazione e ignorare questa sezione. Se si vuole seguire la procedura descritta in questa sezione, non eseguire i passaggi di eliminazione e ricreazione. 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>Eliminare e ricreare il database

Il codice aggiornato in `DbInitializer` aggiunge dati di inizializzazione per le nuove entità. Per forzare la creazione di un nuovo database da parte di EF Core, rimuovere e aggiornare il database:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nella **console di Gestione pacchetti** eseguire il comando seguente:

```powershell
Drop-Database
Update-Database
```

Eseguire `Get-Help about_EntityFrameworkCore` dalla console di Gestione pacchetti per ottenere informazioni.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aprire una finestra di comando e passare alla cartella del progetto. La cartella del progetto contiene il file *Startup.cs*.

Digitare quanto segue nella finestra di comando:

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

Eseguire l'app. Quando si esegue l'app viene eseguito il metodo `DbInitializer.Initialize`. `DbInitializer.Initialize` popola il nuovo database.

Aprire il database in SSOX:

* Se SSOX è stato aperto in precedenza, fare clic sul pulsante **Aggiorna**.
* Espandere il nodo **Tabelle**. Vengono visualizzate le tabelle create.

![Tabelle in SSOX](complex-data-model/_static/ssox-tables.png)

Esaminare la tabella **CourseAssignment**:

* Fare clic con il pulsante destro del mouse sulla tabella **CourseAssignment** e selezionare **Visualizza dati**.
* Verificare che la tabella **CourseAssignment** contenga dati.

![Dati CourseAssignment in SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>Applicare la migrazione al database esistente

Questa sezione è facoltativa. Questa procedura funziona solo se è stata ignorata la sezione [Eliminare e ricreare il database](#drop) precedente.

Quando le migrazioni vengono eseguite con dati esistenti, possono essere presenti vincoli di chiave esterna che non vengono soddisfatti con i dati esistenti. Con i dati di produzione, è necessario eseguire passaggi per la migrazione dei dati esistenti. Questa sezione visualizza un esempio di correzione delle violazioni dei vincoli di chiave esterna. Non apportare queste modifiche al codice senza un backup. Non apportare queste modifiche al codice se è stata completata la sezione precedente e il database è stato aggiornato.

Il file *{timestamp}_ComplexDataModel.cs* contiene il codice seguente:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

Il codice precedente aggiunge una chiave esterna non nullable `DepartmentID` alla tabella `Course`. Il database dell'esercitazione precedente contiene righe in `Course`, pertanto la tabella non può essere aggiornata mediante le migrazioni.

Per far sì che la migrazione `ComplexDataModel` funzioni con i dati esistenti:

* Modificare il codice per assegnare un valore predefinito alla nuova colonna (`DepartmentID`).
* Creare un reparto fittizio denominato "Temp" che assume il ruolo di reparto predefinito.

#### <a name="fix-the-foreign-key-constraints"></a>Risolvere i vincoli della chiave esterna

Aggiornare il metodo `Up` delle classi `ComplexDataModel`:

* Aprire il file *{timestamp}_ComplexDataModel.cs*.
* Impostare come commento la riga di codice che aggiunge la colonna `DepartmentID` alla tabella `Course`.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Aggiungere il codice evidenziato seguente. Il nuovo codice viene inserito dopo il blocco `.CreateTable( name: "Department"`:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Con le modifiche precedenti, le righe `Course` esistenti saranno correlate al dipartimento "Temp" dopo l'esecuzione del metodo `ComplexDataModel` `Up`.

Un'app di produzione:

* Includerà codice o script per l'aggiunta di righe `Department` e righe `Course` correlate alle nuove righe `Department`.
* Non userà il reparto "Temp" o il valore predefinito per `Course.DepartmentID`.

L'esercitazione successiva illustra i dati correlati.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Versione YouTube dell'esercitazione (parte 1)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [Versione YouTube dell'esercitazione (parte 2)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> [Successivo](xref:data/ef-rp/migrations)
> [precedente](xref:data/ef-rp/read-related-data)

::: moniker-end
