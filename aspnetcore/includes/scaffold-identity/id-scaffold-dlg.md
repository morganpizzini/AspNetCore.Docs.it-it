Eseguire l'impalcatura delle identità:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Da **Esplora soluzioni**, fare clic con il pulsante destro del mouse sul progetto > **Aggiungi**  >  **nuovo elemento con impalcatura**.
* Dal riquadro sinistro della finestra di dialogo **Aggiungi nuovo elemento con impalcatura** selezionare **Identity**  >  **Aggiungi**.
* Nella finestra di dialogo **Aggiungi identità** selezionare le opzioni desiderate.
  * Selezionare la pagina layout esistente. in alternativa, il file di layout verrà sovrascritto con un markup errato:
    * `~/Pages/Shared/_Layout.cshtml`per Razor Pages
    * `~/Views/Shared/_Layout.cshtml`per i progetti MVC
    * Per impostazione predefinita, le app del server Blazer create dal modello del server Blaze ( `blazorserver` ) non sono configurate per Razor Pages o MVC. Lasciare vuota la voce di pagina layout.
  * Selezionare il **+** pulsante per creare una nuova **classe del contesto dati**. Accettare il valore predefinito o specificare una classe (ad esempio, `MyApplication.Data.ApplicationDbContext` ).
* Selezionare **Aggiungi**.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Se in precedenza non è stato installato il ASP.NET Core impalcatura, installarlo ora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Aggiungere i riferimenti ai pacchetti NuGet necessari al file di progetto (con*estensione csproj*). Eseguire i comandi seguenti nella directory del progetto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Eseguire il comando seguente per elencare le opzioni dell'impalcatura di identità:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

Nella cartella del progetto eseguire l'impalcatura di identità con le opzioni desiderate. Ad esempio, per configurare Identity con l'interfaccia utente predefinita e il numero minimo di file, eseguire il comando seguente:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
