> [!WARNING]
> <span data-ttu-id="22a3a-101">Un parametro **catch-all** può corrispondere erroneamente alle route a causa di un [bug](https://github.com/dotnet/aspnetcore/issues/18677) nel routing.</span><span class="sxs-lookup"><span data-stu-id="22a3a-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="22a3a-102">Le app interessate da questo bug hanno le seguenti caratteristiche:</span><span class="sxs-lookup"><span data-stu-id="22a3a-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="22a3a-103">Una route catch-all, ad esempio`{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="22a3a-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="22a3a-104">La route catch-all non riesce a trovare le richieste corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="22a3a-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="22a3a-105">La rimozione di altre route rende l'avvio del funzionamento della route catch-all.</span><span class="sxs-lookup"><span data-stu-id="22a3a-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="22a3a-106">Vedere i bug di GitHub [18677](https://github.com/dotnet/aspnetcore/issues/18677) e [16579](https://github.com/dotnet/aspnetcore/issues/16579) per i casi di esempio che hanno raggiunto questo bug.</span><span class="sxs-lookup"><span data-stu-id="22a3a-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="22a3a-107">È prevista una correzione del consenso esplicito per questo bug.</span><span class="sxs-lookup"><span data-stu-id="22a3a-107">An opt-in fix for this bug is planned.</span></span> <span data-ttu-id="22a3a-108">Questo documento verrà aggiornato al rilascio della patch.</span><span class="sxs-lookup"><span data-stu-id="22a3a-108">This doc will be updated when the patch is released.</span></span> <span data-ttu-id="22a3a-109">Quando la patch viene rilasciata, il codice seguente imposta un commutatore interno che corregge il bug:</span><span class="sxs-lookup"><span data-stu-id="22a3a-109">When the patch is released, the following code will set an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```