> [!WARNING]
> <span data-ttu-id="7495f-101">Un parametro **catch-all** può corrispondere erroneamente alle route a causa di un [bug](https://github.com/dotnet/aspnetcore/issues/18677) nel routing.</span><span class="sxs-lookup"><span data-stu-id="7495f-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="7495f-102">Le app interessate da questo bug hanno le seguenti caratteristiche:</span><span class="sxs-lookup"><span data-stu-id="7495f-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="7495f-103">Una route catch-all, ad esempio`{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="7495f-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="7495f-104">La route catch-all non riesce a trovare le richieste corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="7495f-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="7495f-105">La rimozione di altre route rende l'avvio del funzionamento della route catch-all.</span><span class="sxs-lookup"><span data-stu-id="7495f-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="7495f-106">Vedere i bug di GitHub [18677](https://github.com/dotnet/aspnetcore/issues/18677) e [16579](https://github.com/dotnet/aspnetcore/issues/16579) per i casi di esempio che hanno raggiunto questo bug.</span><span class="sxs-lookup"><span data-stu-id="7495f-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="7495f-107">Una correzione del consenso esplicito per questo bug è inclusa in [.NET Core 3.1.301 SDK e versioni successive](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="7495f-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="7495f-108">Il codice seguente imposta un'opzione interna che corregge questo bug:</span><span class="sxs-lookup"><span data-stu-id="7495f-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```