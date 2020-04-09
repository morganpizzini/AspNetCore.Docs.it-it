Aggiungere le proprietà seguenti alla classe `Movie`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

La classe `Movie` contiene:

* Il campo `Id`, richiesto dal database per la chiave primaria.
* `[DataType(DataType.Date)]`: l'attributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) specifica il`Date`tipo di dati ( ). Con questo attributo:

  * l'utente non deve immettere le informazioni temporali nel campo della data.
  * Viene visualizzata solo la data, non le informazioni temporali.

L'attributo [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) viene analizzato in un'esercitazione successiva.