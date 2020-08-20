---
title: Parte 7, Razor pagine con EF core nei dati correlati all'aggiornamento ASP.NET Core
author: rick-anderson
description: Parte 7 di Razor pagine e Entity Framework serie di esercitazioni.
ms.author: riande
ms.date: 07/22/2019
no-loc:
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
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 603c5e7c9f095c380461f8c6e4ead783ad35abe2
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630861"
---
# <a name="part-7-no-locrazor-pages-with-ef-core-in-aspnet-core---update-related-data"></a>Parte 7, Razor pagine con EF core nei dati correlati all'aggiornamento ASP.NET Core

Di [Tom Dykstra](https://github.com/tdykstra), e [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Questa esercitazione illustra come aggiornare i dati correlati. Le illustrazioni seguenti mostrano alcune pagine completate.

![Pagina di modifica del corso](update-related-data/_static/course-edit30.png)
![Pagina di modifica dell'insegnante](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>Aggiornare le pagine Create ed Edit per i corsi

Il codice con scaffolding per le pagine Create ed Edit per i corsi include un elenco a discesa Department che mostra l'ID del dipartimento (un numero intero). L'elenco a discesa dovrebbe visualizzare il nome del dipartimento, quindi per entrambe le pagine è necessario un elenco di nomi di dipartimento. Per fornire tale elenco, usare una classe di base per le pagine Create ed Edit.

### <a name="create-a-base-class-for-course-create-and-edit"></a>Creare una classe di base per le pagine Create ed Edit dei corsi

Creare un file *Pages/Courses/DepartmentNamePageModel.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

Il codice precedente crea un oggetto [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) in cui inserire l'elenco dei nomi dei dipartimenti. Se `selectedDepartment` è specificato, il dipartimento corrispondente viene selezionato in `SelectList`.

Le classi modello delle pagine Create (Crea) ed Edit (Modifica) derivano da `DepartmentNamePageModel`.

### <a name="update-the-course-create-page-model"></a>Aggiornare il modello della pagina Create per i corsi

Un corso viene assegnato a un dipartimento. La classe di base per le pagine Create ed Edit fornisce un oggetto `SelectList` per la selezione del dipartimento. L'elenco a discesa che usa `SelectList` imposta la proprietà di chiave esterna `Course.DepartmentID`. Core EF usa la chiave esterna `Course.DepartmentID` per caricare la proprietà di navigazione `Department`.

![Create course (Crea corso)](update-related-data/_static/ddl30.png)

Aggiornare *Pages/Courses/Create.cshtml.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Il codice precedente:

* Classe derivata da `DepartmentNamePageModel`.
* Usa `TryUpdateModelAsync` per evitare l'[overposting](xref:data/ef-rp/crud#overposting).
* Rimuove `ViewData["DepartmentID"]`. `DepartmentNameSL` dalla classe base è un modello fortemente tipizzato e verrà usato dalla Razor pagina. I modelli fortemente tipizzati sono preferibili a quelli scarsamente tipizzati. Per altre informazioni, vedere [Dati con tipizzazione debole (ViewData e ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-course-create-no-locrazor-page"></a>Aggiornare la pagina di creazione del corso Razor

Aggiornare *Pages/Courses/Create.cshtml* con il codice seguente:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

Il codice precedente apporta le modifiche seguenti:

* Modifica la didascalia da **DepartmentID** a **Department**.
* Sostituisce `"ViewBag.DepartmentID"` con `DepartmentNameSL` (dalla classe di base).
* Aggiunge l'opzione "Select Department" (Selezionare il dipartimento). Questa modifica esegue il rendering di "Select Department" nell'elenco a discesa quando non è ancora stato selezionato alcun dipartimento, anziché visualizzare il primo dipartimento.
* Aggiunge un messaggio di convalida quando il dipartimento non è selezionato.

La Razor pagina usa l' [Helper tag SELECT](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Testare la pagina Create (Crea). La pagina Create (Crea) visualizza il nome, anziché l'ID, del dipartimento.

### <a name="update-the-course-edit-page-model"></a>Aggiornare il modello della pagina Edit per i corsi

Aggiornare *Pages/Courses/Edit.cshtml.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

Le modifiche sono simili a quelle apportate nel modello della pagina Create (Crea). Nel codice precedente, `PopulateDepartmentsDropDownList` passa l'ID del dipartimento, che seleziona il dipartimento nell'elenco a discesa.

### <a name="update-the-course-edit-no-locrazor-page"></a>Aggiornare la pagina di modifica del corso Razor

Aggiornare *Pages/Courses/Edit.cshtml* con il codice seguente:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Il codice precedente apporta le modifiche seguenti:

* Visualizza l'ID del corso. In genere, la chiave primaria di un'entità non viene visualizzata. Le chiavi primarie di solito non sono significative per gli utenti. In questo caso, la chiave primaria corrisponde al numero del corso.
* Modifica la didascalia per l'elenco a discesa dei dipartimenti da **DepartmentID** a **Department**.
* Sostituisce `"ViewBag.DepartmentID"` con `DepartmentNameSL` (dalla classe di base).

La pagina contiene un campo nascosto (`<input type="hidden">`) per il numero del corso. L'aggiunta di un helper tag `<label>` con `asp-for="Course.CourseID"` non elimina la necessità del campo nascosto. `<input type="hidden">` è necessario per includere il numero del corso tra i dati inviati quando l'utente fa clic su **Save** (Salva).

## <a name="update-the-course-details-and-delete-pages"></a>Aggiornare le pagine Details e Delete per i corsi

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) può migliorare le prestazioni quando la registrazione non è necessaria.

### <a name="update-the-course-page-models"></a>Aggiornare i modelli di pagina per i corsi

Aggiornare *Pages/Courses/Delete.cshtml.cs* con il codice seguente per aggiungere `AsNoTracking`:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

Apportare la stessa modifica nel file *Pages/Courses/Details.cshtml.cs*:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-no-locrazor-pages"></a>Aggiornare le pagine del corso Razor

Aggiornare *Pages/Courses/Delete.cshtml* con il codice seguente:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

Apportare le stesse modifiche alla pagina Details (Dettagli).

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a>Testare le pagine del corso

Testare le pagine Create, Edit, Details e Delete.

## <a name="update-the-instructor-create-and-edit-pages"></a>Aggiornare le pagine Create ed Edit per gli insegnanti

Gli insegnanti possono tenere un numero qualsiasi di corsi. La figura seguente mostra la pagina Edit per gli insegnanti con una matrice di caselle di controllo dei corsi.

![Pagina di modifica dell'insegnante con corsi](update-related-data/_static/instructor-edit-courses30.png)

Le caselle di controllo consentono di modificare i corsi a cui è assegnato un insegnante. Per ogni corso nel database è visualizzata una casella di controllo. I corsi assegnati all'insegnante corrente sono selezionati. L'utente può selezionare o deselezionare le caselle di controllo per modificare le assegnazioni dei corsi. Se il numero di corsi fosse molto più elevato, un'interfaccia utente diversa potrebbe funzionare meglio. Tuttavia, il metodo di gestione di una relazione molti-a-molti qui illustrato non cambierebbe. Per creare o eliminare relazioni, è necessario modificare un'entità di join.

### <a name="create-a-class-for-assigned-courses-data"></a>Creare una classe per i dati dei corsi assegnati

Creare *SchoolViewModels/AssignedCourseData.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

La classe `AssignedCourseData` contiene i dati per la creazione delle caselle di controllo per i corsi assegnati a un insegnante.

### <a name="create-an-instructor-page-model-base-class"></a>Creare una classe di base del modello di pagina Instructor

Creare la classe di base *Pages/Instructors/InstructorCoursesPageModel.cs*:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

`InstructorCoursesPageModel` è la classe di base che verrà usata per i modelli delle pagine Edit (Modifica) e Create (Crea). `PopulateAssignedCourseData` legge tutte le entità `Course` per popolare `AssignedCourseDataList`. Per ogni corso, il codice imposta `CourseID` e titolo, e stabilisce se l'insegnante è assegnato al corso. Viene usato un [HashSet](/dotnet/api/system.collections.generic.hashset-1) per ricerche efficienti.

Poiché la Razor pagina non include una raccolta di entità Course, lo strumento di associazione di modelli non può aggiornare automaticamente la `CourseAssignments` proprietà di navigazione. Anziché usare lo strumento di associazione di modelli per aggiornare la proprietà di navigazione `CourseAssignments`, questa operazione viene eseguita nel nuovo metodo `UpdateInstructorCourses`. È pertanto necessario escludere la proprietà `CourseAssignments` dall'associazione di modelli. Questa operazione non richiede alcuna modifica al codice che chiama `TryUpdateModel` perché si sta usando l'overload con proprietà dichiarate e `CourseAssignments` non è incluso nell'elenco di inclusione.

Se nessuna casella di controllo è selezionata, il codice in `UpdateInstructorCourses` inizializza la proprietà di navigazione `CourseAssignments` con una raccolta vuota e torna:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

Il codice scorre quindi ciclicamente tutti i corsi nel database e controlla ogni corso a fronte di quelli assegnati all'insegnante rispetto a quelli selezionati nella pagina. Per facilitare l'esecuzione di ricerche efficienti, le ultime due raccolte sono archiviate all'interno di oggetti `HashSet`.

Se la casella di controllo di un corso è selezionata ma il corso non è presente nella proprietà di navigazione `Instructor.CourseAssignments`, il corso viene aggiunto alla raccolta nella proprietà di navigazione.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

Se la casella di controllo di un corso non è selezionata ma il corso è presente nella proprietà di navigazione `Instructor.CourseAssignments`, il corso viene rimosso dalla proprietà di navigazione.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a>Gestire la posizione dell'ufficio

Un'altra relazione che la pagina di modifica deve gestire è la relazione uno-a-zero-o-uno che l'entità Instructor ha con l'entità `OfficeAssignment`. Il codice di modifica dell'insegnante deve gestire gli scenari seguenti: 

* Se l'utente cancella l'assegnazione dell'ufficio, eliminare l'entità `OfficeAssignment`.
* Se l'utente immette l'assegnazione dell'ufficio e questa era vuota, creare una nuova entità `OfficeAssignment`.
* Se l'utente modifica l'assegnazione dell'ufficio, aggiornare l'entità `OfficeAssignment`.

### <a name="update-the-instructor-edit-page-model"></a>Aggiornare il modello della pagina Edit per gli insegnanti

Aggiornare *Pages/Instructors/Edit.cshtml.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

Il codice precedente:

* Ottiene l'entità `Instructor` corrente dal database tramite il caricamento eager per la proprietà di navigazione `OfficeAssignment`, `CourseAssignment` e `CourseAssignment.Course`.
* Aggiorna l'entità `Instructor` recuperata con valori dallo strumento di associazione di modelli. `TryUpdateModel` impedisce l'[overposting](xref:data/ef-rp/crud#overposting).
* Se la posizione dell'ufficio è vuota, imposta `Instructor.OfficeAssignment` su Null. Se `Instructor.OfficeAssignment` è Null, la riga correlata nella tabella `OfficeAssignment` viene eliminata.
* Chiama `PopulateAssignedCourseData` in `OnGetAsync` per fornire informazioni per le caselle di controllo usando la classe del modello di visualizzazione `AssignedCourseData`.
* Chiama `UpdateInstructorCourses` in `OnPostAsync` per applicare le informazioni dalle caselle di controllo all'entità Instructor in corso di modifica.
* Chiama `PopulateAssignedCourseData` e `UpdateInstructorCourses` in `OnPostAsync` se `TryUpdateModel` ha esito negativo. Queste chiamate di metodo ripristinano i dati dei corsi assegnati immessi nella pagina quando viene rivisualizzata con un messaggio di errore.

### <a name="update-the-instructor-edit-no-locrazor-page"></a>Aggiornare la pagina di modifica dell'insegnante Razor

Aggiornare *Pages/Instructors/Edit.cshtml* con il codice seguente:

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

Il codice precedente crea una tabella HTML con tre colonne. Ogni colonna ha una casella di controllo e una didascalia che contiene il numero e il titolo del corso. Le caselle di controllo hanno tutte lo stesso nome ("selectedCourses"). L'uso dello stesso nome informa lo strumento di associazione di modelli che deve considerarle come un gruppo. L'attributo value di ogni casella di controllo è impostato su `CourseID`. Quando la pagina viene pubblicata, lo strumento di associazione di modelli passa una matrice costituita dai valori `CourseID` delle sole caselle di controllo selezionate.

Quando viene eseguito il rendering iniziale delle caselle di controllo, vengono selezionati i corsi assegnati all'insegnante.

Nota: l'approccio qui adottato per la modifica dei dati dei corsi degli insegnanti funziona bene quando è presente un numero limitato di corsi. Per raccolte molto più grandi, un'interfaccia utente diversa e un altro metodo di aggiornamento sono più efficienti e pratici.

Eseguire l'app e testare la pagina Edit per gli insegnanti aggiornata. Modificare alcune assegnazioni di corsi. Le modifiche si riflettono nella pagina di indice.

### <a name="update-the-instructor-create-page"></a>Aggiornare la pagina Create per gli insegnanti

Aggiornare la pagina e il modello di creazione pagina dell'insegnante Razor con codice simile a quello della pagina Edit (modifica):

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

Testare la pagina Create (Crea) dell'insegnante.

## <a name="update-the-instructor-delete-page"></a>Aggiornare la pagina Delete per gli insegnanti

Aggiornare *Pages/Instructors/Delete.cshtml.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

Il codice precedente apporta le modifiche seguenti:

* Usare il caricamento eager per la proprietà di navigazione `CourseAssignments`. È necessario includere `CourseAssignments`, in caso contrario le assegnazioni non vengono eliminate quando viene eliminato l'insegnante. Per evitare la necessità di leggerle, configurare l'eliminazione a catena nel database.

* Se l'insegnante da eliminare è assegnato come responsabile di un dipartimento, tale assegnazione viene rimossa dal dipartimento.

Eseguire l'app e testare la pagina Delete.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="step-by-step"]
> [Esercitazione precedente](xref:data/ef-rp/read-related-data) 
>  [Esercitazione successiva](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Questa esercitazione illustra l'aggiornamento di dati correlati. Se si verificano problemi che non si è in grado di risolvere, [scaricare o visualizzare l'app completa](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). [Istruzioni per il download](xref:index#how-to-download-a-sample).

Le illustrazioni seguenti mostrano alcune pagine completate.

![Pagina di modifica del corso](update-related-data/_static/course-edit.png)
![Pagina di modifica dell'insegnante](update-related-data/_static/instructor-edit-courses.png)

Esaminare e testare le pagine di creazione e modifica del corso. Creare un nuovo corso. Il dipartimento viene selezionato in base alla chiave primaria (numero intero), non in base al nome. Modificare il nuovo corso. Al termine del test, eliminare il nuovo corso.

## <a name="create-a-base-class-to-share-common-code"></a>Creare una classe di base per condividere il codice comune

La pagina di creazione e quella di modifica del corso hanno bisogno dell'elenco dei nomi dei dipartimenti. Creare la classe di base *Pages/Courses/DepartmentNamePageModel.cshtml.cs* per le pagine Create (Crea) ed Edit (Modifica):

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Il codice precedente crea un oggetto [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) in cui inserire l'elenco dei nomi dei dipartimenti. Se `selectedDepartment` è specificato, il dipartimento corrispondente viene selezionato in `SelectList`.

Le classi modello delle pagine Create (Crea) ed Edit (Modifica) derivano da `DepartmentNamePageModel`.

## <a name="customize-the-courses-pages"></a>Personalizzare le pagine dei corsi

Quando viene creata, una nuova entità corso deve essere in relazione con un dipartimento esistente. Per consentire l'aggiunta di un dipartimento durante la creazione di un corso, la classe di base per Create (Crea) ed Edit (Modifica) contiene un elenco a discesa per la selezione del dipartimento. L'elenco a discesa imposta la proprietà di chiave esterna `Course.DepartmentID`. Core EF usa la chiave esterna `Course.DepartmentID` per caricare la proprietà di navigazione `Department`.

![Create course (Crea corso)](update-related-data/_static/ddl.png)

Aggiornare il modello della pagina Create (Crea) con il codice seguente:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

Il codice precedente:

* Classe derivata da `DepartmentNamePageModel`.
* Usa `TryUpdateModelAsync` per evitare l'[overposting](xref:data/ef-rp/crud#overposting).
* Sostituisce `ViewData["DepartmentID"]` con `DepartmentNameSL` (dalla classe di base).

`ViewData["DepartmentID"]` viene sostituito con `DepartmentNameSL` fortemente tipizzato. I modelli fortemente tipizzati sono preferibili a quelli scarsamente tipizzati. Per altre informazioni, vedere [Dati con tipizzazione debole (ViewData e ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Aggiornare la pagina di creazione dei corsi

Aggiornare *Pages/Courses/Create.cshtml* con il codice seguente:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Il markup precedente apporta le modifiche seguenti:

* Modifica la didascalia da **DepartmentID** a **Department**.
* Sostituisce `"ViewBag.DepartmentID"` con `DepartmentNameSL` (dalla classe di base).
* Aggiunge l'opzione "Select Department" (Selezionare il dipartimento). Questa modifica esegue il rendering di "Select Department" (Selezionare il dipartimento) anziché del primo dipartimento.
* Aggiunge un messaggio di convalida quando il dipartimento non è selezionato.

La Razor pagina usa l' [Helper tag SELECT](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Testare la pagina Create (Crea). La pagina Create (Crea) visualizza il nome, anziché l'ID, del dipartimento.

### <a name="update-the-courses-edit-page"></a>Aggiornare la pagina di modifica dei corsi.

Sostituire il codice in *Pages/Courses/Edit.cshtml.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

Le modifiche sono simili a quelle apportate nel modello della pagina Create (Crea). Nel codice precedente, `PopulateDepartmentsDropDownList` passa l'ID del dipartimento, che seleziona il dipartimento specificato nell'elenco a discesa.

Aggiornare *Pages/Courses/Edit.cshtml* con il markup seguente:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Il markup precedente apporta le modifiche seguenti:

* Visualizza l'ID del corso. In genere, la chiave primaria di un'entità non viene visualizzata. Le chiavi primarie di solito non sono significative per gli utenti. In questo caso, la chiave primaria corrisponde al numero del corso.
* Modifica la didascalia da **DepartmentID** a **Department**.
* Sostituisce `"ViewBag.DepartmentID"` con `DepartmentNameSL` (dalla classe di base).

La pagina contiene un campo nascosto (`<input type="hidden">`) per il numero del corso. L'aggiunta di un helper tag `<label>` con `asp-for="Course.CourseID"` non elimina la necessità del campo nascosto. `<input type="hidden">` è necessario per includere il numero del corso tra i dati inviati quando l'utente fa clic su **Save** (Salva).

Testare il codice aggiornato. Creare, modificare ed eliminare un corso.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Aggiungere AsNoTracking ai modelli delle pagine Details (Dettagli) e Delete (Elimina)

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) può migliorare le prestazioni quando la registrazione non è necessaria. Aggiungere `AsNoTracking` alle pagine Delete (Elimina) e Details (Dettagli). Il codice seguente illustra il modello della pagina Delete (Elimina) aggiornato:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Aggiornare il metodo `OnGetAsync` nel file *Pages/Courses/Details.cshtml.cs*:

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Modificare le pagine Delete (Elimina) e Details (Dettagli)

Aggiornare la Razor pagina Delete con il markup seguente:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Apportare le stesse modifiche alla pagina Details (Dettagli).

### <a name="test-the-course-pages"></a>Testare le pagine del corso

Testare le pagine di creazione, modifica, dettagli ed eliminazione.

## <a name="update-the-instructor-pages"></a>Aggiornare le pagine dell'insegnante

Le sezioni seguenti aggiornano le pagine dell'insegnante.

### <a name="add-office-location"></a>Aggiungere la posizione dell'ufficio

Quando si modifica il record di un insegnante, può essere necessario aggiornare l'assegnazione dell'ufficio. L'entità `Instructor` ha una relazione uno-a-zero-o-uno con l'entità `OfficeAssignment`. Il codice relativo all'insegnante deve gestire i casi seguenti:

* Se l'utente cancella l'assegnazione dell'ufficio, eliminare l'entità `OfficeAssignment`.
* Se l'utente immette l'assegnazione dell'ufficio e questa era vuota, creare una nuova entità `OfficeAssignment`.
* Se l'utente modifica l'assegnazione dell'ufficio, aggiornare l'entità `OfficeAssignment`.

Aggiornare il modello della pagina Edit (Modifica) dell'insegnante con il codice seguente:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

Il codice precedente:

* Ottiene l'entità `Instructor` corrente dal database tramite il caricamento eager per la proprietà di navigazione `OfficeAssignment`.
* Aggiorna l'entità `Instructor` recuperata con valori dallo strumento di associazione di modelli. `TryUpdateModel` impedisce l'[overposting](xref:data/ef-rp/crud#overposting).
* Se la posizione dell'ufficio è vuota, imposta `Instructor.OfficeAssignment` su Null. Se `Instructor.OfficeAssignment` è Null, la riga correlata nella tabella `OfficeAssignment` viene eliminata.

### <a name="update-the-instructor-edit-page"></a>Aggiornare la pagina di modifica dell'insegnante

Aggiornare *Pages/Instructors/Edit.cshtml* con la posizione dell'ufficio:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Verificare che sia possibile modificare la posizione dell'ufficio di un insegnante.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Aggiungere assegnazioni di corso nella pagina di modifica dell'insegnante

Gli insegnanti possono tenere un numero qualsiasi di corsi. In questa sezione, viene aggiunta la possibilità di modificare le assegnazioni dei corsi. La figura seguente illustra la pagina Edit (Modifica) dell'insegnante aggiornata:

![Pagina di modifica dell'insegnante con corsi](update-related-data/_static/instructor-edit-courses.png)

`Course` e `Instructor` hanno una relazione molti-a-molti. Per aggiungere e rimuovere relazioni, aggiungere e rimuovere entità dal set di entità di join `CourseAssignments`.

Per consentire la modifica dei corsi assegnati a un insegnante, si usano caselle di controllo. Per ogni corso nel database è visualizzata una casella di controllo. I corsi assegnati all'insegnante corrente sono selezionati. L'utente può selezionare e deselezionare le caselle di controllo per modificare le assegnazioni dei corsi. Se il numero di corsi è molto più elevato:

* Per visualizzare i corsi è consigliabile usare un'interfaccia utente diversa.
* Il metodo di modifica di un'entità di join per creare o eliminare relazioni non cambia.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Aggiungere classi per supportare le pagine di creazione e modifica dell'insegnante

Creare *SchoolViewModels/AssignedCourseData.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

La classe `AssignedCourseData` contiene i dati per la creazione delle caselle di controllo per i corsi assegnati a un insegnante.

Creare la classe di base *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs*:

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

`InstructorCoursesPageModel` è la classe di base che verrà usata per i modelli delle pagine Edit (Modifica) e Create (Crea). `PopulateAssignedCourseData` legge tutte le entità `Course` per popolare `AssignedCourseDataList`. Per ogni corso, il codice imposta `CourseID` e titolo, e stabilisce se l'insegnante è assegnato al corso. Per creare ricerche efficienti, si usa un [HashSet](/dotnet/api/system.collections.generic.hashset-1).

### <a name="instructors-edit-page-model"></a>Modello della pagina di modifica dell'insegnante

Aggiornare il modello della pagina Edit (Modifica) dell'insegnante con il codice seguente:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

Il codice precedente gestisce le modifiche alle assegnazioni di ufficio.

Aggiornare la Razor visualizzazione Instructor:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Quando si incolla il codice in Visual Studio, le interruzioni di riga vengono modificate in modo tale da danneggiare il codice. Premere Ctrl + Z una volta per annullare la formattazione automatica. Premendo CTRL + Z si correggono le interruzioni di riga, che vengono visualizzate come illustrato qui. Il rientro non deve necessariamente essere perfetto, ma le righe `@:</tr><tr>`, `@:<td>`, `@:</td>` e `@:</tr>` devono trovarsi in una sola riga, come illustrato. Dopo aver selezionato il blocco di nuovo codice, premere Tab tre volte per allineare il nuovo codice con il codice esistente. Esprimere un voto o vedere lo stato di questo bug [con questo collegamento](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Il codice precedente crea una tabella HTML con tre colonne. Ogni colonna ha una casella di controllo e una didascalia che contiene il numero e il titolo del corso. Le caselle di controllo hanno tutte lo stesso nome ("selectedCourses"). L'uso dello stesso nome informa lo strumento di associazione di modelli che deve considerarle come un gruppo. L'attributo value di ogni casella di controllo è impostato su `CourseID`. Quando la pagina viene pubblicata, lo strumento di associazione di modelli passa una matrice costituita dai valori `CourseID` delle sole caselle di controllo selezionate.

Quando viene eseguito il rendering iniziale delle caselle di controllo, i corsi assegnati all'insegnante hanno attributi selezionati.

Eseguire l'app e testare la pagina Edit (Modifica) dell'insegnante aggiornata. Modificare alcune assegnazioni di corsi. Le modifiche si riflettono nella pagina di indice.

Nota: l'approccio qui adottato per la modifica dei dati dei corsi degli insegnanti funziona bene quando è presente un numero limitato di corsi. Per raccolte molto più grandi, un'interfaccia utente diversa e un altro metodo di aggiornamento sono più efficienti e pratici.

### <a name="update-the-instructors-create-page"></a>Aggiornare la pagina di creazione dell'insegnante

Aggiornare il modello della pagina Create (Crea) dell'insegnante con il codice seguente:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Il codice precedente è simile al codice *Pages/Instructors/Edit.cshtml.cs*.

Aggiornare la pagina di creazione dell'insegnante Razor con il markup seguente:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Testare la pagina Create (Crea) dell'insegnante.

## <a name="update-the-delete-page"></a>Aggiornare la pagina Delete (Elimina)

Aggiornare il modello di pagina Delete (Elimina) con il codice seguente:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

Il codice precedente apporta le modifiche seguenti:

* Usare il caricamento eager per la proprietà di navigazione `CourseAssignments`. È necessario includere `CourseAssignments`, in caso contrario le assegnazioni non vengono eliminate quando viene eliminato l'insegnante. Per evitare la necessità di leggerle, configurare l'eliminazione a catena nel database.

* Se l'insegnante da eliminare è assegnato come responsabile di un dipartimento, tale assegnazione viene rimossa dal dipartimento.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Versione YouTube dell'esercitazione (parte 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Versione YouTube dell'esercitazione (parte 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Precedente](xref:data/ef-rp/read-related-data) 
>  [Avanti](xref:data/ef-rp/concurrency)

::: moniker-end
