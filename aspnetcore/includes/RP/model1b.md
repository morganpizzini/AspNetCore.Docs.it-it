<!-- THIS INCLUDE USED BY MVC AND RP -->
<span data-ttu-id="50ac3-101">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="50ac3-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="50ac3-102">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="50ac3-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="50ac3-103">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="50ac3-103">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="50ac3-104">`[DataType(DataType.Date)]`: l'attributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) specifica il tipo di dati (Date).</span><span class="sxs-lookup"><span data-stu-id="50ac3-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date).</span></span> <span data-ttu-id="50ac3-105">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="50ac3-105">With this attribute:</span></span>

  * <span data-ttu-id="50ac3-106">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="50ac3-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="50ac3-107">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="50ac3-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="50ac3-108">L'attributo [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="50ac3-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>
