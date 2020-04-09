---
title: Razor Pages con EF Core in ASP.NET Core - Aggiornare dati correlati - 7 di 8
author: rick-anderson
description: In questa esercitazione vengono aggiornati i dati correlati tramite l'aggiornamento dei campi di chiave esterna e delle proprietà di navigazione.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/update-related-data
ms.openlocfilehash: fdfdb14ff8414b8bf30f9b95be7ba0a6bcbd2995
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656422"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a><span data-ttu-id="8b485-103">Razor Pages con EF Core in ASP.NET Core - Aggiornare dati correlati - 7 di 8</span><span class="sxs-lookup"><span data-stu-id="8b485-103">Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8</span></span>

<span data-ttu-id="8b485-104">Di [Tom Dykstra](https://github.com/tdykstra), e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8b485-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b485-105">Questa esercitazione illustra come aggiornare i dati correlati.</span><span class="sxs-lookup"><span data-stu-id="8b485-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="8b485-106">Le illustrazioni seguenti mostrano alcune pagine completate.</span><span class="sxs-lookup"><span data-stu-id="8b485-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="8b485-107">![Pagina di modifica del corso](update-related-data/_static/course-edit30.png)
![Pagina di modifica dell'insegnante](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="8b485-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="8b485-108">Aggiornare le pagine Create ed Edit per i corsi</span><span class="sxs-lookup"><span data-stu-id="8b485-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="8b485-109">Il codice con scaffolding per le pagine Create ed Edit per i corsi include un elenco a discesa Department che mostra l'ID del dipartimento (un numero intero).</span><span class="sxs-lookup"><span data-stu-id="8b485-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="8b485-110">L'elenco a discesa dovrebbe visualizzare il nome del dipartimento, quindi per entrambe le pagine è necessario un elenco di nomi di dipartimento.</span><span class="sxs-lookup"><span data-stu-id="8b485-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="8b485-111">Per fornire tale elenco, usare una classe di base per le pagine Create ed Edit.</span><span class="sxs-lookup"><span data-stu-id="8b485-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="8b485-112">Creare una classe di base per le pagine Create ed Edit dei corsi</span><span class="sxs-lookup"><span data-stu-id="8b485-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="8b485-113">Creare un file *Pages/Courses/DepartmentNamePageModel.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="8b485-114">Il codice precedente crea un oggetto [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) in cui inserire l'elenco dei nomi dei dipartimenti.</span><span class="sxs-lookup"><span data-stu-id="8b485-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="8b485-115">Se `selectedDepartment` è specificato, il dipartimento corrispondente viene selezionato in `SelectList`.</span><span class="sxs-lookup"><span data-stu-id="8b485-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="8b485-116">Le classi modello delle pagine Create (Crea) ed Edit (Modifica) derivano da `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="8b485-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="8b485-117">Aggiornare il modello della pagina Create per i corsi</span><span class="sxs-lookup"><span data-stu-id="8b485-117">Update the Course Create page model</span></span>

<span data-ttu-id="8b485-118">Un corso viene assegnato a un dipartimento.</span><span class="sxs-lookup"><span data-stu-id="8b485-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="8b485-119">La classe di base per le pagine Create ed Edit fornisce un oggetto `SelectList` per la selezione del dipartimento.</span><span class="sxs-lookup"><span data-stu-id="8b485-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="8b485-120">L'elenco a discesa che usa `SelectList` imposta la proprietà di chiave esterna `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="8b485-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="8b485-121">Core EF usa la chiave esterna `Course.DepartmentID` per caricare la proprietà di navigazione `Department`.</span><span class="sxs-lookup"><span data-stu-id="8b485-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Create course (Crea corso)](update-related-data/_static/ddl30.png)

<span data-ttu-id="8b485-123">Aggiornare *Pages/Courses/Create.cshtml.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="8b485-124">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="8b485-124">The preceding code:</span></span>

* <span data-ttu-id="8b485-125">Classe derivata da `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="8b485-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="8b485-126">Usa `TryUpdateModelAsync` per evitare l'[overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="8b485-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="8b485-127">Rimuove `ViewData["DepartmentID"]`.</span><span class="sxs-lookup"><span data-stu-id="8b485-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="8b485-128">`DepartmentNameSL` dalla classe di base è un modello fortemente tipizzato e verrà usato dalla pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="8b485-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="8b485-129">I modelli fortemente tipizzati sono preferibili a quelli scarsamente tipizzati.</span><span class="sxs-lookup"><span data-stu-id="8b485-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="8b485-130">Per altre informazioni, vedere [Dati con tipizzazione debole (ViewData e ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="8b485-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="8b485-131">Aggiornare la pagina Razor Create per i corsi</span><span class="sxs-lookup"><span data-stu-id="8b485-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="8b485-132">Aggiornare *Pages/Courses/Create.cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="8b485-133">Il codice precedente apporta le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="8b485-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="8b485-134">Modifica la didascalia da **DepartmentID** a **Department**.</span><span class="sxs-lookup"><span data-stu-id="8b485-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="8b485-135">Sostituisce `"ViewBag.DepartmentID"` con `DepartmentNameSL` (dalla classe di base).</span><span class="sxs-lookup"><span data-stu-id="8b485-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="8b485-136">Aggiunge l'opzione "Select Department" (Selezionare il dipartimento).</span><span class="sxs-lookup"><span data-stu-id="8b485-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="8b485-137">Questa modifica esegue il rendering di "Select Department" nell'elenco a discesa quando non è ancora stato selezionato alcun dipartimento, anziché visualizzare il primo dipartimento.</span><span class="sxs-lookup"><span data-stu-id="8b485-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="8b485-138">Aggiunge un messaggio di convalida quando il dipartimento non è selezionato.</span><span class="sxs-lookup"><span data-stu-id="8b485-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="8b485-139">La pagina Razor usa l'[helper tag di selezione](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="8b485-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="8b485-140">Testare la pagina Create (Crea).</span><span class="sxs-lookup"><span data-stu-id="8b485-140">Test the Create page.</span></span> <span data-ttu-id="8b485-141">La pagina Create (Crea) visualizza il nome, anziché l'ID, del dipartimento.</span><span class="sxs-lookup"><span data-stu-id="8b485-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="8b485-142">Aggiornare il modello della pagina Edit per i corsi</span><span class="sxs-lookup"><span data-stu-id="8b485-142">Update the Course Edit page model</span></span>

<span data-ttu-id="8b485-143">Aggiornare *Pages/Courses/Edit.cshtml.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="8b485-144">Le modifiche sono simili a quelle apportate nel modello della pagina Create (Crea).</span><span class="sxs-lookup"><span data-stu-id="8b485-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="8b485-145">Nel codice precedente, `PopulateDepartmentsDropDownList` passa l'ID del dipartimento, che seleziona il dipartimento nell'elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="8b485-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="8b485-146">Aggiornare la pagina Razor Edit per i corsi</span><span class="sxs-lookup"><span data-stu-id="8b485-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="8b485-147">Aggiornare *Pages/Courses/Edit.cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="8b485-148">Il codice precedente apporta le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="8b485-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="8b485-149">Visualizza l'ID del corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-149">Displays the course ID.</span></span> <span data-ttu-id="8b485-150">In genere, la chiave primaria di un'entità non viene visualizzata.</span><span class="sxs-lookup"><span data-stu-id="8b485-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="8b485-151">Le chiavi primarie di solito non sono significative per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="8b485-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="8b485-152">In questo caso, la chiave primaria corrisponde al numero del corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="8b485-153">Modifica la didascalia per l'elenco a discesa dei dipartimenti da **DepartmentID** a **Department**.</span><span class="sxs-lookup"><span data-stu-id="8b485-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="8b485-154">Sostituisce `"ViewBag.DepartmentID"` con `DepartmentNameSL` (dalla classe di base).</span><span class="sxs-lookup"><span data-stu-id="8b485-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="8b485-155">La pagina contiene un campo nascosto (`<input type="hidden">`) per il numero del corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="8b485-156">L'aggiunta di un helper tag `<label>` con `asp-for="Course.CourseID"` non elimina la necessità del campo nascosto.</span><span class="sxs-lookup"><span data-stu-id="8b485-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="8b485-157">`<input type="hidden">` è necessario per includere il numero del corso tra i dati inviati quando l'utente fa clic su **Save** (Salva).</span><span class="sxs-lookup"><span data-stu-id="8b485-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="8b485-158">Aggiornare le pagine Details e Delete per i corsi</span><span class="sxs-lookup"><span data-stu-id="8b485-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="8b485-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) può migliorare le prestazioni quando la registrazione non è necessaria.</span><span class="sxs-lookup"><span data-stu-id="8b485-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="8b485-160">Aggiornare i modelli di pagina per i corsi</span><span class="sxs-lookup"><span data-stu-id="8b485-160">Update the Course page models</span></span>

<span data-ttu-id="8b485-161">Aggiornare *Pages/Courses/Delete.cshtml.cs* con il codice seguente per aggiungere `AsNoTracking`:</span><span class="sxs-lookup"><span data-stu-id="8b485-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="8b485-162">Apportare la stessa modifica nel file *Pages/Courses/Details.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b485-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="8b485-163">Aggiornare le pagine Razor per i corsi</span><span class="sxs-lookup"><span data-stu-id="8b485-163">Update the Course Razor pages</span></span>

<span data-ttu-id="8b485-164">Aggiornare *Pages/Courses/Delete.cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="8b485-165">Apportare le stesse modifiche alla pagina Details (Dettagli).</span><span class="sxs-lookup"><span data-stu-id="8b485-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="8b485-166">Testare le pagine del corso</span><span class="sxs-lookup"><span data-stu-id="8b485-166">Test the Course pages</span></span>

<span data-ttu-id="8b485-167">Testare le pagine Create, Edit, Details e Delete.</span><span class="sxs-lookup"><span data-stu-id="8b485-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="8b485-168">Aggiornare le pagine Create ed Edit per gli insegnanti</span><span class="sxs-lookup"><span data-stu-id="8b485-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="8b485-169">Gli insegnanti possono tenere un numero qualsiasi di corsi.</span><span class="sxs-lookup"><span data-stu-id="8b485-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="8b485-170">La figura seguente mostra la pagina Edit per gli insegnanti con una matrice di caselle di controllo dei corsi.</span><span class="sxs-lookup"><span data-stu-id="8b485-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Pagina di modifica dell'insegnante con corsi](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="8b485-172">Le caselle di controllo consentono di modificare i corsi a cui è assegnato un insegnante.</span><span class="sxs-lookup"><span data-stu-id="8b485-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="8b485-173">Per ogni corso nel database è visualizzata una casella di controllo.</span><span class="sxs-lookup"><span data-stu-id="8b485-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="8b485-174">I corsi assegnati all'insegnante corrente sono selezionati.</span><span class="sxs-lookup"><span data-stu-id="8b485-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="8b485-175">L'utente può selezionare o deselezionare le caselle di controllo per modificare le assegnazioni dei corsi.</span><span class="sxs-lookup"><span data-stu-id="8b485-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="8b485-176">Se il numero di corsi fosse molto più elevato, un'interfaccia utente diversa potrebbe funzionare meglio.</span><span class="sxs-lookup"><span data-stu-id="8b485-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="8b485-177">Tuttavia, il metodo di gestione di una relazione molti-a-molti qui illustrato non cambierebbe.</span><span class="sxs-lookup"><span data-stu-id="8b485-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="8b485-178">Per creare o eliminare relazioni, è necessario modificare un'entità di join.</span><span class="sxs-lookup"><span data-stu-id="8b485-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="8b485-179">Creare una classe per i dati dei corsi assegnati</span><span class="sxs-lookup"><span data-stu-id="8b485-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="8b485-180">Creare *SchoolViewModels/AssignedCourseData.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="8b485-181">La classe `AssignedCourseData` contiene i dati per la creazione delle caselle di controllo per i corsi assegnati a un insegnante.</span><span class="sxs-lookup"><span data-stu-id="8b485-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="8b485-182">Creare una classe di base del modello di pagina Instructor</span><span class="sxs-lookup"><span data-stu-id="8b485-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="8b485-183">Creare la classe di base *Pages/Instructors/InstructorCoursesPageModel.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b485-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="8b485-184">`InstructorCoursesPageModel` è la classe di base che verrà usata per i modelli delle pagine Edit (Modifica) e Create (Crea).</span><span class="sxs-lookup"><span data-stu-id="8b485-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="8b485-185">`PopulateAssignedCourseData` legge tutte le entità `Course` per popolare `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="8b485-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="8b485-186">Per ogni corso, il codice imposta `CourseID` e titolo, e stabilisce se l'insegnante è assegnato al corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="8b485-187">Viene usato un [HashSet](/dotnet/api/system.collections.generic.hashset-1) per ricerche efficienti.</span><span class="sxs-lookup"><span data-stu-id="8b485-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="8b485-188">Dato che la pagina Razor non ha una raccolta di entità Course, lo strumento di associazione di modelli non può aggiornare automaticamente la proprietà di navigazione `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="8b485-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="8b485-189">Anziché usare lo strumento di associazione di modelli per aggiornare la proprietà di navigazione `CourseAssignments`, questa operazione viene eseguita nel nuovo metodo `UpdateInstructorCourses`.</span><span class="sxs-lookup"><span data-stu-id="8b485-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="8b485-190">È pertanto necessario escludere la proprietà `CourseAssignments` dall'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="8b485-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="8b485-191">Ciò non richiede modifiche al codice che chiama `TryUpdateModel`, poiché si sta usando l'overload dell'elenco elementi consentiti e `CourseAssignments` non è presente nell'elenco di inclusione.</span><span class="sxs-lookup"><span data-stu-id="8b485-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="8b485-192">Se nessuna casella di controllo è selezionata, il codice in `UpdateInstructorCourses` inizializza la proprietà di navigazione `CourseAssignments` con una raccolta vuota e torna:</span><span class="sxs-lookup"><span data-stu-id="8b485-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="8b485-193">Il codice scorre quindi ciclicamente tutti i corsi nel database e controlla ogni corso a fronte di quelli assegnati all'insegnante rispetto a quelli selezionati nella pagina.</span><span class="sxs-lookup"><span data-stu-id="8b485-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="8b485-194">Per facilitare l'esecuzione di ricerche efficienti, le ultime due raccolte sono archiviate all'interno di oggetti `HashSet`.</span><span class="sxs-lookup"><span data-stu-id="8b485-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="8b485-195">Se la casella di controllo di un corso è selezionata ma il corso non è presente nella proprietà di navigazione `Instructor.CourseAssignments`, il corso viene aggiunto alla raccolta nella proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="8b485-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="8b485-196">Se la casella di controllo di un corso non è selezionata ma il corso è presente nella proprietà di navigazione `Instructor.CourseAssignments`, il corso viene rimosso dalla proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="8b485-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="8b485-197">Gestire la posizione dell'ufficio</span><span class="sxs-lookup"><span data-stu-id="8b485-197">Handle office location</span></span>

<span data-ttu-id="8b485-198">Un'altra relazione che la pagina di modifica deve gestire è la relazione uno-a-zero-o-uno che l'entità Instructor ha con l'entità `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="8b485-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="8b485-199">Il codice di modifica dell'insegnante deve gestire gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="8b485-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="8b485-200">Se l'utente cancella l'assegnazione dell'ufficio, eliminare l'entità `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="8b485-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="8b485-201">Se l'utente immette l'assegnazione dell'ufficio e questa era vuota, creare una nuova entità `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="8b485-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="8b485-202">Se l'utente modifica l'assegnazione dell'ufficio, aggiornare l'entità `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="8b485-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="8b485-203">Aggiornare il modello della pagina Edit per gli insegnanti</span><span class="sxs-lookup"><span data-stu-id="8b485-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="8b485-204">Aggiornare *Pages/Instructors/Edit.cshtml.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="8b485-205">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="8b485-205">The preceding code:</span></span>

* <span data-ttu-id="8b485-206">Ottiene l'entità `Instructor` corrente dal database tramite il caricamento eager per la proprietà di navigazione `OfficeAssignment`, `CourseAssignment` e `CourseAssignment.Course`.</span><span class="sxs-lookup"><span data-stu-id="8b485-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="8b485-207">Aggiorna l'entità `Instructor` recuperata con valori dallo strumento di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="8b485-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="8b485-208">`TryUpdateModel` impedisce l'[overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="8b485-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="8b485-209">Se la posizione dell'ufficio è vuota, imposta `Instructor.OfficeAssignment` su Null.</span><span class="sxs-lookup"><span data-stu-id="8b485-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="8b485-210">Se `Instructor.OfficeAssignment` è Null, la riga correlata nella tabella `OfficeAssignment` viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="8b485-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="8b485-211">Chiama `PopulateAssignedCourseData` in `OnGetAsync` per fornire informazioni per le caselle di controllo usando la classe del modello di visualizzazione `AssignedCourseData`.</span><span class="sxs-lookup"><span data-stu-id="8b485-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="8b485-212">Chiama `UpdateInstructorCourses` in `OnPostAsync` per applicare le informazioni dalle caselle di controllo all'entità Instructor in corso di modifica.</span><span class="sxs-lookup"><span data-stu-id="8b485-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="8b485-213">Chiama `PopulateAssignedCourseData` e `UpdateInstructorCourses` in `OnPostAsync` se `TryUpdateModel` ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="8b485-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="8b485-214">Queste chiamate di metodo ripristinano i dati dei corsi assegnati immessi nella pagina quando viene rivisualizzata con un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="8b485-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="8b485-215">Aggiornare la pagina Razor Edit per gli insegnanti</span><span class="sxs-lookup"><span data-stu-id="8b485-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="8b485-216">Aggiornare *Pages/Instructors/Edit.cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="8b485-217">Il codice precedente crea una tabella HTML con tre colonne.</span><span class="sxs-lookup"><span data-stu-id="8b485-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="8b485-218">Ogni colonna ha una casella di controllo e una didascalia che contiene il numero e il titolo del corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="8b485-219">Le caselle di controllo hanno tutte lo stesso nome ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="8b485-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="8b485-220">L'uso dello stesso nome informa lo strumento di associazione di modelli che deve considerarle come un gruppo.</span><span class="sxs-lookup"><span data-stu-id="8b485-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="8b485-221">L'attributo value di ogni casella di controllo è impostato su `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="8b485-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="8b485-222">Quando la pagina viene pubblicata, lo strumento di associazione di modelli passa una matrice costituita dai valori `CourseID` delle sole caselle di controllo selezionate.</span><span class="sxs-lookup"><span data-stu-id="8b485-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="8b485-223">Quando viene eseguito il rendering iniziale delle caselle di controllo, vengono selezionati i corsi assegnati all'insegnante.</span><span class="sxs-lookup"><span data-stu-id="8b485-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="8b485-224">Nota: l'approccio qui adottato per la modifica dei dati dei corsi degli insegnanti funziona bene quando è presente un numero limitato di corsi.</span><span class="sxs-lookup"><span data-stu-id="8b485-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="8b485-225">Per raccolte molto più grandi, un'interfaccia utente diversa e un altro metodo di aggiornamento sono più efficienti e pratici.</span><span class="sxs-lookup"><span data-stu-id="8b485-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="8b485-226">Eseguire l'app e testare la pagina Edit per gli insegnanti aggiornata.</span><span class="sxs-lookup"><span data-stu-id="8b485-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="8b485-227">Modificare alcune assegnazioni di corsi.</span><span class="sxs-lookup"><span data-stu-id="8b485-227">Change some course assignments.</span></span> <span data-ttu-id="8b485-228">Le modifiche si riflettono nella pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="8b485-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="8b485-229">Aggiornare la pagina Create per gli insegnanti</span><span class="sxs-lookup"><span data-stu-id="8b485-229">Update the Instructor Create page</span></span>

<span data-ttu-id="8b485-230">Aggiornare il modello della pagina e la pagina Razor Create per gli insegnanti con codice simile a quello della pagina Edit:</span><span class="sxs-lookup"><span data-stu-id="8b485-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="8b485-231">Testare la pagina Create (Crea) dell'insegnante.</span><span class="sxs-lookup"><span data-stu-id="8b485-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="8b485-232">Aggiornare la pagina Delete per gli insegnanti</span><span class="sxs-lookup"><span data-stu-id="8b485-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="8b485-233">Aggiornare *Pages/Instructors/Delete.cshtml.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="8b485-234">Il codice precedente apporta le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="8b485-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="8b485-235">Usare il caricamento eager per la proprietà di navigazione `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="8b485-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="8b485-236">È necessario includere `CourseAssignments`, in caso contrario le assegnazioni non vengono eliminate quando viene eliminato l'insegnante.</span><span class="sxs-lookup"><span data-stu-id="8b485-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="8b485-237">Per evitare la necessità di leggerle, configurare l'eliminazione a catena nel database.</span><span class="sxs-lookup"><span data-stu-id="8b485-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="8b485-238">Se l'insegnante da eliminare è assegnato come responsabile di un dipartimento, tale assegnazione viene rimossa dal dipartimento.</span><span class="sxs-lookup"><span data-stu-id="8b485-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="8b485-239">Eseguire l'app e testare la pagina Delete.</span><span class="sxs-lookup"><span data-stu-id="8b485-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8b485-240">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="8b485-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8b485-241">[Esercitazione](xref:data/ef-rp/read-related-data)
> precedente[Esercitazione successiva](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="8b485-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b485-242">Questa esercitazione illustra l'aggiornamento di dati correlati.</span><span class="sxs-lookup"><span data-stu-id="8b485-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="8b485-243">Se si verificano problemi che non si è in grado di risolvere, [scaricare o visualizzare l'app completa](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="8b485-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="8b485-244">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="8b485-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="8b485-245">Le illustrazioni seguenti mostrano alcune pagine completate.</span><span class="sxs-lookup"><span data-stu-id="8b485-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="8b485-246">![Pagina di modifica del corso](update-related-data/_static/course-edit.png)
![Pagina di modifica dell'insegnante](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="8b485-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="8b485-247">Esaminare e testare le pagine di creazione e modifica del corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="8b485-248">Creare un nuovo corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-248">Create a new course.</span></span> <span data-ttu-id="8b485-249">Il dipartimento viene selezionato in base alla chiave primaria (numero intero), non in base al nome.</span><span class="sxs-lookup"><span data-stu-id="8b485-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="8b485-250">Modificare il nuovo corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-250">Edit the new course.</span></span> <span data-ttu-id="8b485-251">Al termine del test, eliminare il nuovo corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="8b485-252">Creare una classe di base per condividere il codice comune</span><span class="sxs-lookup"><span data-stu-id="8b485-252">Create a base class to share common code</span></span>

<span data-ttu-id="8b485-253">La pagina di creazione e quella di modifica del corso hanno bisogno dell'elenco dei nomi dei dipartimenti.</span><span class="sxs-lookup"><span data-stu-id="8b485-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="8b485-254">Creare la classe di base *Pages/Courses/DepartmentNamePageModel.cshtml.cs* per le pagine Create (Crea) ed Edit (Modifica):</span><span class="sxs-lookup"><span data-stu-id="8b485-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="8b485-255">Il codice precedente crea un oggetto [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) in cui inserire l'elenco dei nomi dei dipartimenti.</span><span class="sxs-lookup"><span data-stu-id="8b485-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="8b485-256">Se `selectedDepartment` è specificato, il dipartimento corrispondente viene selezionato in `SelectList`.</span><span class="sxs-lookup"><span data-stu-id="8b485-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="8b485-257">Le classi modello delle pagine Create (Crea) ed Edit (Modifica) derivano da `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="8b485-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="8b485-258">Personalizzare le pagine dei corsi</span><span class="sxs-lookup"><span data-stu-id="8b485-258">Customize the Courses Pages</span></span>

<span data-ttu-id="8b485-259">Quando viene creata, una nuova entità corso deve essere in relazione con un dipartimento esistente.</span><span class="sxs-lookup"><span data-stu-id="8b485-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="8b485-260">Per consentire l'aggiunta di un dipartimento durante la creazione di un corso, la classe di base per Create (Crea) ed Edit (Modifica) contiene un elenco a discesa per la selezione del dipartimento.</span><span class="sxs-lookup"><span data-stu-id="8b485-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="8b485-261">L'elenco a discesa imposta la proprietà di chiave esterna `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="8b485-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="8b485-262">Core EF usa la chiave esterna `Course.DepartmentID` per caricare la proprietà di navigazione `Department`.</span><span class="sxs-lookup"><span data-stu-id="8b485-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Create course (Crea corso)](update-related-data/_static/ddl.png)

<span data-ttu-id="8b485-264">Aggiornare il modello della pagina Create (Crea) con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="8b485-265">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="8b485-265">The preceding code:</span></span>

* <span data-ttu-id="8b485-266">Classe derivata da `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="8b485-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="8b485-267">Usa `TryUpdateModelAsync` per evitare l'[overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="8b485-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="8b485-268">Sostituisce `ViewData["DepartmentID"]` con `DepartmentNameSL` (dalla classe di base).</span><span class="sxs-lookup"><span data-stu-id="8b485-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="8b485-269">`ViewData["DepartmentID"]` viene sostituito con `DepartmentNameSL` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="8b485-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="8b485-270">I modelli fortemente tipizzati sono preferibili a quelli scarsamente tipizzati.</span><span class="sxs-lookup"><span data-stu-id="8b485-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="8b485-271">Per altre informazioni, vedere [Dati con tipizzazione debole (ViewData e ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="8b485-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="8b485-272">Aggiornare la pagina di creazione dei corsi</span><span class="sxs-lookup"><span data-stu-id="8b485-272">Update the Courses Create page</span></span>

<span data-ttu-id="8b485-273">Aggiornare *Pages/Courses/Create.cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="8b485-274">Il markup precedente apporta le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="8b485-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="8b485-275">Modifica la didascalia da **DepartmentID** a **Department**.</span><span class="sxs-lookup"><span data-stu-id="8b485-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="8b485-276">Sostituisce `"ViewBag.DepartmentID"` con `DepartmentNameSL` (dalla classe di base).</span><span class="sxs-lookup"><span data-stu-id="8b485-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="8b485-277">Aggiunge l'opzione "Select Department" (Selezionare il dipartimento).</span><span class="sxs-lookup"><span data-stu-id="8b485-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="8b485-278">Questa modifica esegue il rendering di "Select Department" (Selezionare il dipartimento) anziché del primo dipartimento.</span><span class="sxs-lookup"><span data-stu-id="8b485-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="8b485-279">Aggiunge un messaggio di convalida quando il dipartimento non è selezionato.</span><span class="sxs-lookup"><span data-stu-id="8b485-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="8b485-280">La pagina Razor usa l'[helper tag di selezione](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="8b485-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="8b485-281">Testare la pagina Create (Crea).</span><span class="sxs-lookup"><span data-stu-id="8b485-281">Test the Create page.</span></span> <span data-ttu-id="8b485-282">La pagina Create (Crea) visualizza il nome, anziché l'ID, del dipartimento.</span><span class="sxs-lookup"><span data-stu-id="8b485-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="8b485-283">Aggiornare la pagina di modifica dei corsi.</span><span class="sxs-lookup"><span data-stu-id="8b485-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="8b485-284">Sostituire il codice in *Pages/Courses/Edit.cshtml.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="8b485-285">Le modifiche sono simili a quelle apportate nel modello della pagina Create (Crea).</span><span class="sxs-lookup"><span data-stu-id="8b485-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="8b485-286">Nel codice precedente, `PopulateDepartmentsDropDownList` passa l'ID del dipartimento, che seleziona il dipartimento specificato nell'elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="8b485-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="8b485-287">Aggiornare *Pages/Courses/Edit.cshtml* con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="8b485-288">Il markup precedente apporta le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="8b485-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="8b485-289">Visualizza l'ID del corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-289">Displays the course ID.</span></span> <span data-ttu-id="8b485-290">In genere, la chiave primaria di un'entità non viene visualizzata.</span><span class="sxs-lookup"><span data-stu-id="8b485-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="8b485-291">Le chiavi primarie di solito non sono significative per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="8b485-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="8b485-292">In questo caso, la chiave primaria corrisponde al numero del corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="8b485-293">Modifica la didascalia da **DepartmentID** a **Department**.</span><span class="sxs-lookup"><span data-stu-id="8b485-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="8b485-294">Sostituisce `"ViewBag.DepartmentID"` con `DepartmentNameSL` (dalla classe di base).</span><span class="sxs-lookup"><span data-stu-id="8b485-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="8b485-295">La pagina contiene un campo nascosto (`<input type="hidden">`) per il numero del corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="8b485-296">L'aggiunta di un helper tag `<label>` con `asp-for="Course.CourseID"` non elimina la necessità del campo nascosto.</span><span class="sxs-lookup"><span data-stu-id="8b485-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="8b485-297">`<input type="hidden">` è necessario per includere il numero del corso tra i dati inviati quando l'utente fa clic su **Save** (Salva).</span><span class="sxs-lookup"><span data-stu-id="8b485-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="8b485-298">Testare il codice aggiornato.</span><span class="sxs-lookup"><span data-stu-id="8b485-298">Test the updated code.</span></span> <span data-ttu-id="8b485-299">Creare, modificare ed eliminare un corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="8b485-300">Aggiungere AsNoTracking ai modelli delle pagine Details (Dettagli) e Delete (Elimina)</span><span class="sxs-lookup"><span data-stu-id="8b485-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="8b485-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) può migliorare le prestazioni quando la registrazione non è necessaria.</span><span class="sxs-lookup"><span data-stu-id="8b485-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="8b485-302">Aggiungere `AsNoTracking` alle pagine Delete (Elimina) e Details (Dettagli).</span><span class="sxs-lookup"><span data-stu-id="8b485-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="8b485-303">Il codice seguente illustra il modello della pagina Delete (Elimina) aggiornato:</span><span class="sxs-lookup"><span data-stu-id="8b485-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="8b485-304">Aggiornare il metodo `OnGetAsync` nel file *Pages/Courses/Details.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b485-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="8b485-305">Modificare le pagine Delete (Elimina) e Details (Dettagli)</span><span class="sxs-lookup"><span data-stu-id="8b485-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="8b485-306">Aggiornare la pagina Razor Delete (Elimina) con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="8b485-307">Apportare le stesse modifiche alla pagina Details (Dettagli).</span><span class="sxs-lookup"><span data-stu-id="8b485-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="8b485-308">Testare le pagine del corso</span><span class="sxs-lookup"><span data-stu-id="8b485-308">Test the Course pages</span></span>

<span data-ttu-id="8b485-309">Testare le pagine di creazione, modifica, dettagli ed eliminazione.</span><span class="sxs-lookup"><span data-stu-id="8b485-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="8b485-310">Aggiornare le pagine dell'insegnante</span><span class="sxs-lookup"><span data-stu-id="8b485-310">Update the instructor pages</span></span>

<span data-ttu-id="8b485-311">Le sezioni seguenti aggiornano le pagine dell'insegnante.</span><span class="sxs-lookup"><span data-stu-id="8b485-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="8b485-312">Aggiungere la posizione dell'ufficio</span><span class="sxs-lookup"><span data-stu-id="8b485-312">Add office location</span></span>

<span data-ttu-id="8b485-313">Quando si modifica il record di un insegnante, può essere necessario aggiornare l'assegnazione dell'ufficio.</span><span class="sxs-lookup"><span data-stu-id="8b485-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="8b485-314">L'entità `Instructor` ha una relazione uno-a-zero-o-uno con l'entità `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="8b485-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="8b485-315">Il codice relativo all'insegnante deve gestire i casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8b485-315">The instructor code must handle:</span></span>

* <span data-ttu-id="8b485-316">Se l'utente cancella l'assegnazione dell'ufficio, eliminare l'entità `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="8b485-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="8b485-317">Se l'utente immette l'assegnazione dell'ufficio e questa era vuota, creare una nuova entità `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="8b485-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="8b485-318">Se l'utente modifica l'assegnazione dell'ufficio, aggiornare l'entità `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="8b485-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="8b485-319">Aggiornare il modello della pagina Edit (Modifica) dell'insegnante con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="8b485-320">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="8b485-320">The preceding code:</span></span>

* <span data-ttu-id="8b485-321">Ottiene l'entità `Instructor` corrente dal database tramite il caricamento eager per la proprietà di navigazione `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="8b485-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="8b485-322">Aggiorna l'entità `Instructor` recuperata con valori dallo strumento di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="8b485-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="8b485-323">`TryUpdateModel` impedisce l'[overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="8b485-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="8b485-324">Se la posizione dell'ufficio è vuota, imposta `Instructor.OfficeAssignment` su Null.</span><span class="sxs-lookup"><span data-stu-id="8b485-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="8b485-325">Se `Instructor.OfficeAssignment` è Null, la riga correlata nella tabella `OfficeAssignment` viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="8b485-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="8b485-326">Aggiornare la pagina di modifica dell'insegnante</span><span class="sxs-lookup"><span data-stu-id="8b485-326">Update the instructor Edit page</span></span>

<span data-ttu-id="8b485-327">Aggiornare *Pages/Instructors/Edit.cshtml* con la posizione dell'ufficio:</span><span class="sxs-lookup"><span data-stu-id="8b485-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="8b485-328">Verificare che sia possibile modificare la posizione dell'ufficio di un insegnante.</span><span class="sxs-lookup"><span data-stu-id="8b485-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="8b485-329">Aggiungere assegnazioni di corso nella pagina di modifica dell'insegnante</span><span class="sxs-lookup"><span data-stu-id="8b485-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="8b485-330">Gli insegnanti possono tenere un numero qualsiasi di corsi.</span><span class="sxs-lookup"><span data-stu-id="8b485-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="8b485-331">In questa sezione, viene aggiunta la possibilità di modificare le assegnazioni dei corsi.</span><span class="sxs-lookup"><span data-stu-id="8b485-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="8b485-332">La figura seguente illustra la pagina Edit (Modifica) dell'insegnante aggiornata:</span><span class="sxs-lookup"><span data-stu-id="8b485-332">The following image shows the updated instructor Edit page:</span></span>

![Pagina di modifica dell'insegnante con corsi](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="8b485-334">`Course` e `Instructor` hanno una relazione molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="8b485-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="8b485-335">Per aggiungere e rimuovere relazioni, aggiungere e rimuovere entità dal set di entità di join `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="8b485-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="8b485-336">Per consentire la modifica dei corsi assegnati a un insegnante, si usano caselle di controllo.</span><span class="sxs-lookup"><span data-stu-id="8b485-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="8b485-337">Per ogni corso nel database è visualizzata una casella di controllo.</span><span class="sxs-lookup"><span data-stu-id="8b485-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="8b485-338">I corsi assegnati all'insegnante corrente sono selezionati.</span><span class="sxs-lookup"><span data-stu-id="8b485-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="8b485-339">L'utente può selezionare e deselezionare le caselle di controllo per modificare le assegnazioni dei corsi.</span><span class="sxs-lookup"><span data-stu-id="8b485-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="8b485-340">Se il numero di corsi è molto più elevato:</span><span class="sxs-lookup"><span data-stu-id="8b485-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="8b485-341">Per visualizzare i corsi è consigliabile usare un'interfaccia utente diversa.</span><span class="sxs-lookup"><span data-stu-id="8b485-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="8b485-342">Il metodo di modifica di un'entità di join per creare o eliminare relazioni non cambia.</span><span class="sxs-lookup"><span data-stu-id="8b485-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="8b485-343">Aggiungere classi per supportare le pagine di creazione e modifica dell'insegnante</span><span class="sxs-lookup"><span data-stu-id="8b485-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="8b485-344">Creare *SchoolViewModels/AssignedCourseData.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="8b485-345">La classe `AssignedCourseData` contiene i dati per la creazione delle caselle di controllo per i corsi assegnati a un insegnante.</span><span class="sxs-lookup"><span data-stu-id="8b485-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="8b485-346">Creare la classe di base *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b485-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="8b485-347">`InstructorCoursesPageModel` è la classe di base che verrà usata per i modelli delle pagine Edit (Modifica) e Create (Crea).</span><span class="sxs-lookup"><span data-stu-id="8b485-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="8b485-348">`PopulateAssignedCourseData` legge tutte le entità `Course` per popolare `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="8b485-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="8b485-349">Per ogni corso, il codice imposta `CourseID` e titolo, e stabilisce se l'insegnante è assegnato al corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="8b485-350">Per creare ricerche efficienti, si usa un [HashSet](/dotnet/api/system.collections.generic.hashset-1).</span><span class="sxs-lookup"><span data-stu-id="8b485-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="8b485-351">Modello della pagina di modifica dell'insegnante</span><span class="sxs-lookup"><span data-stu-id="8b485-351">Instructors Edit page model</span></span>

<span data-ttu-id="8b485-352">Aggiornare il modello della pagina Edit (Modifica) dell'insegnante con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="8b485-353">Il codice precedente gestisce le modifiche alle assegnazioni di ufficio.</span><span class="sxs-lookup"><span data-stu-id="8b485-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="8b485-354">Aggiornare la visualizzazione Razor degli insegnanti:</span><span class="sxs-lookup"><span data-stu-id="8b485-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="8b485-355">Quando si incolla il codice in Visual Studio, le interruzioni di riga vengono modificate in modo tale da danneggiare il codice.</span><span class="sxs-lookup"><span data-stu-id="8b485-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="8b485-356">Premere Ctrl + Z una volta per annullare la formattazione automatica.</span><span class="sxs-lookup"><span data-stu-id="8b485-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="8b485-357">Premendo CTRL + Z si correggono le interruzioni di riga, che vengono visualizzate come illustrato qui.</span><span class="sxs-lookup"><span data-stu-id="8b485-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="8b485-358">Il rientro non deve necessariamente essere perfetto, ma le righe `@:</tr><tr>`, `@:<td>`, `@:</td>` e `@:</tr>` devono trovarsi in una sola riga, come illustrato.</span><span class="sxs-lookup"><span data-stu-id="8b485-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="8b485-359">Dopo aver selezionato il blocco di nuovo codice, premere Tab tre volte per allineare il nuovo codice con il codice esistente.</span><span class="sxs-lookup"><span data-stu-id="8b485-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="8b485-360">Esprimere un voto o vedere lo stato di questo bug [con questo collegamento](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="8b485-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="8b485-361">Il codice precedente crea una tabella HTML con tre colonne.</span><span class="sxs-lookup"><span data-stu-id="8b485-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="8b485-362">Ogni colonna ha una casella di controllo e una didascalia che contiene il numero e il titolo del corso.</span><span class="sxs-lookup"><span data-stu-id="8b485-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="8b485-363">Le caselle di controllo hanno tutte lo stesso nome ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="8b485-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="8b485-364">L'uso dello stesso nome informa lo strumento di associazione di modelli che deve considerarle come un gruppo.</span><span class="sxs-lookup"><span data-stu-id="8b485-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="8b485-365">L'attributo value di ogni casella di controllo è impostato su `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="8b485-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="8b485-366">Quando la pagina viene pubblicata, lo strumento di associazione di modelli passa una matrice costituita dai valori `CourseID` delle sole caselle di controllo selezionate.</span><span class="sxs-lookup"><span data-stu-id="8b485-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="8b485-367">Quando viene eseguito il rendering iniziale delle caselle di controllo, i corsi assegnati all'insegnante hanno attributi selezionati.</span><span class="sxs-lookup"><span data-stu-id="8b485-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="8b485-368">Eseguire l'app e testare la pagina Edit (Modifica) dell'insegnante aggiornata.</span><span class="sxs-lookup"><span data-stu-id="8b485-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="8b485-369">Modificare alcune assegnazioni di corsi.</span><span class="sxs-lookup"><span data-stu-id="8b485-369">Change some course assignments.</span></span> <span data-ttu-id="8b485-370">Le modifiche si riflettono nella pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="8b485-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="8b485-371">Nota: l'approccio qui adottato per la modifica dei dati dei corsi degli insegnanti funziona bene quando è presente un numero limitato di corsi.</span><span class="sxs-lookup"><span data-stu-id="8b485-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="8b485-372">Per raccolte molto più grandi, un'interfaccia utente diversa e un altro metodo di aggiornamento sono più efficienti e pratici.</span><span class="sxs-lookup"><span data-stu-id="8b485-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="8b485-373">Aggiornare la pagina di creazione dell'insegnante</span><span class="sxs-lookup"><span data-stu-id="8b485-373">Update the instructors Create page</span></span>

<span data-ttu-id="8b485-374">Aggiornare il modello della pagina Create (Crea) dell'insegnante con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="8b485-375">Il codice precedente è simile al codice *Pages/Instructors/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="8b485-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="8b485-376">Aggiornare la pagina Razor Create (Crea) dell'insegnante con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="8b485-377">Testare la pagina Create (Crea) dell'insegnante.</span><span class="sxs-lookup"><span data-stu-id="8b485-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="8b485-378">Aggiornare la pagina Delete (Elimina)</span><span class="sxs-lookup"><span data-stu-id="8b485-378">Update the Delete page</span></span>

<span data-ttu-id="8b485-379">Aggiornare il modello di pagina Delete (Elimina) con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8b485-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="8b485-380">Il codice precedente apporta le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="8b485-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="8b485-381">Usare il caricamento eager per la proprietà di navigazione `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="8b485-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="8b485-382">È necessario includere `CourseAssignments`, in caso contrario le assegnazioni non vengono eliminate quando viene eliminato l'insegnante.</span><span class="sxs-lookup"><span data-stu-id="8b485-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="8b485-383">Per evitare la necessità di leggerle, configurare l'eliminazione a catena nel database.</span><span class="sxs-lookup"><span data-stu-id="8b485-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="8b485-384">Se l'insegnante da eliminare è assegnato come responsabile di un dipartimento, tale assegnazione viene rimossa dal dipartimento.</span><span class="sxs-lookup"><span data-stu-id="8b485-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b485-385">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8b485-385">Additional resources</span></span>

* [<span data-ttu-id="8b485-386">Versione YouTube dell'esercitazione (parte 1)</span><span class="sxs-lookup"><span data-stu-id="8b485-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="8b485-387">Versione YouTube dell'esercitazione (parte 2)</span><span class="sxs-lookup"><span data-stu-id="8b485-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="8b485-388">[Successivo](xref:data/ef-rp/read-related-data)
> [precedente](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="8b485-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
