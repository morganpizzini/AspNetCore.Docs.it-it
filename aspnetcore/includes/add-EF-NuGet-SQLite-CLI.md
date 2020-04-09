<span data-ttu-id="39767-101">Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="39767-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="39767-102">I comandi precedenti aggiungono:</span><span class="sxs-lookup"><span data-stu-id="39767-102">The preceding commands add:</span></span>

* <span data-ttu-id="39767-103">Lo strumento di [scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="39767-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="39767-104">Gli strumenti di base di Entity Framework per l'interfaccia della riga di comando di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="39767-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="39767-105">Il provider EF Core SQLite, che installa il pacchetto di EF Core come dipendenza.</span><span class="sxs-lookup"><span data-stu-id="39767-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="39767-106">I pacchetti necessari per lo scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` e `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="39767-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="39767-107">Per indicazioni sulla configurazione di più ambienti che consentono a un'app di configurare i contesti di database in base all'ambiente, vedere <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span><span class="sxs-lookup"><span data-stu-id="39767-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]