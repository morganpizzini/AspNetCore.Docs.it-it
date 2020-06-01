# <a name="aspnet-core-distributed-cache-sample"></a>Esempio di ASP.NET Core cache distribuita

Questo esempio illustra l'uso di una cache distribuita. Questo esempio illustra lo scenario descritto nell'argomento [lavoro con una cache distribuita in ASP.NET Core](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) .

Nell'ambiente di produzione l'app di esempio è configurata per l'uso di una cache SQL Server distribuita. Per riconfigurare l'app per l'uso di una cache Redis distribuita, modificare la direttiva per il preprocessore nella parte superiore del file *Startup.cs* per l'uso di redis ( `#define Redis // SQLServer` ). Per altre informazioni, vedere [direttive per il preprocessore nel codice di esempio](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core#preprocessor-directives-in-sample-code).
