### <a name="use-sqlite-for-development-sql-server-for-production"></a>Usare SQLite per lo sviluppo, SQL Server per la produzioneUse SQLite for development, SQL Server for production

Quando SQLite è selezionato, il codice generato dal modello è pronto per lo sviluppo. Il codice seguente viene <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> illustrato come inserire in avvio. `IWebHostEnvironment`viene inserito `ConfigureServices` in modo da poter utilizzare SQLite in fase di sviluppo e SQL Server nell'ambiente di produzione.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]
