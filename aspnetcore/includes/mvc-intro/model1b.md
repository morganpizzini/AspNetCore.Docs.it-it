<span data-ttu-id="aad3b-101">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="aad3b-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="aad3b-102">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="aad3b-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="aad3b-103">Il campo `Id`, richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="aad3b-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="aad3b-104">`[DataType(DataType.Date)]`: L'attributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="aad3b-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="aad3b-105">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="aad3b-105">With this attribute:</span></span>

  * <span data-ttu-id="aad3b-106">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="aad3b-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="aad3b-107">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="aad3b-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="aad3b-108">L'attributo [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="aad3b-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>