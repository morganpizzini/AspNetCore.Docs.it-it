---
<span data-ttu-id="acb13-101">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-102">'Blazor'</span></span>
- <span data-ttu-id="acb13-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-103">'Identity'</span></span>
- <span data-ttu-id="acb13-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-105">'Razor'</span></span>
- <span data-ttu-id="acb13-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-106">'SignalR' uid:</span></span> 

---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="acb13-107">Globalizzazione e localizzazione in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="acb13-107">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="acb13-108">[Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="acb13-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="acb13-109">Un sito Web multilingue consente al sito di raggiungere un pubblico più ampio.</span><span class="sxs-lookup"><span data-stu-id="acb13-109">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="acb13-110">AP.NET Core offre servizi e middleware per la localizzazione in diverse lingue e culture.</span><span class="sxs-lookup"><span data-stu-id="acb13-110">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="acb13-111">L'internazionalizzazione implica la [globalizzazione](/dotnet/api/system.globalization) e la [localizzazione](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="acb13-111">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="acb13-112">La globalizzazione è il processo di progettazione di app che supportano impostazioni cultura diverse.</span><span class="sxs-lookup"><span data-stu-id="acb13-112">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="acb13-113">La globalizzazione aggiunge supporto per l'input, la visualizzazione e l'output di una specifica serie di script della lingua legati a determinate aree geografiche.</span><span class="sxs-lookup"><span data-stu-id="acb13-113">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="acb13-114">La localizzazione è il processo di adattamento di un'app globalizzata, già elaborata per la localizzazione, a particolari impostazioni cultura e impostazioni locali.</span><span class="sxs-lookup"><span data-stu-id="acb13-114">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="acb13-115">Per altre informazioni, vedere **Termini relativi alla globalizzazione e alla localizzazione** nella parte finale di questo documento.</span><span class="sxs-lookup"><span data-stu-id="acb13-115">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="acb13-116">La localizzazione dell'app comporta quanto segue:</span><span class="sxs-lookup"><span data-stu-id="acb13-116">App localization involves the following:</span></span>

1. <span data-ttu-id="acb13-117">Rendere localizzabile il contenuto dell'app</span><span class="sxs-lookup"><span data-stu-id="acb13-117">Make the app's content localizable</span></span>
1. <span data-ttu-id="acb13-118">Fornire le risorse localizzate per le lingue e le impostazioni cultura supportate</span><span class="sxs-lookup"><span data-stu-id="acb13-118">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="acb13-119">Implementare una strategia per la selezione della lingua o delle impostazioni cultura per ogni richiesta</span><span class="sxs-lookup"><span data-stu-id="acb13-119">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="acb13-120">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="acb13-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="acb13-121">Rendere localizzabile il contenuto dell'app</span><span class="sxs-lookup"><span data-stu-id="acb13-121">Make the app's content localizable</span></span>

<span data-ttu-id="acb13-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer ' non richiede l'archiviazione delle stringhe di lingua predefinite in un file di risorse.</span><span class="sxs-lookup"><span data-stu-id="acb13-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="acb13-123">È possibile sviluppare un'app per la localizzazione senza creare file di risorse nelle prime fasi di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="acb13-123">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="acb13-124">Il codice seguente illustra come eseguire il wrapping della stringa "About Title" per la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-124">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="acb13-125">Nel codice precedente, l' `IStringLocalizer<T>` implementazione deriva dall' [inserimento delle dipendenze](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="acb13-125">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="acb13-126">Se non viene trovato il valore localizzato di "About Title", viene restituita la chiave dell'indicizzatore, ovvero la stringa "About Title".</span><span class="sxs-lookup"><span data-stu-id="acb13-126">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="acb13-127">È possibile lasciare le stringhe letterali della lingua predefinita nell'app ed eseguirne il wrapping nel localizzatore per potersi concentrare sullo sviluppo dell'app.</span><span class="sxs-lookup"><span data-stu-id="acb13-127">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="acb13-128">Sviluppare l'app con la lingua predefinita e prepararla per la fase di localizzazione senza prima creare un file di risorse predefinito.</span><span class="sxs-lookup"><span data-stu-id="acb13-128">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="acb13-129">In alternativa, è possibile usare l'approccio tradizionale e fornire una chiave per recuperare la stringa della lingua predefinita.</span><span class="sxs-lookup"><span data-stu-id="acb13-129">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="acb13-130">Per molti sviluppatori il nuovo flusso di lavoro che prevede di non avere un file con estensione *resx* della lingua predefinita e di eseguire semplicemente il wrapping dei valori letterali di stringa consente di ridurre il sovraccarico della localizzazione di un'app.</span><span class="sxs-lookup"><span data-stu-id="acb13-130">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="acb13-131">Altri sviluppatori preferiscono il flusso di lavoro tradizionale poiché semplifica l'uso di valori letterali di stringa più lunghi e l'aggiornamento delle stringhe localizzate.</span><span class="sxs-lookup"><span data-stu-id="acb13-131">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="acb13-132">Usare l'implementazione `IHtmlLocalizer<T>` per le risorse che contengono HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-132">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="acb13-133">L'HTML di `IHtmlLocalizer` codifica gli argomenti formattati nella stringa di risorsa, ma non codifica in HTML la stringa di risorsa.</span><span class="sxs-lookup"><span data-stu-id="acb13-133">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="acb13-134">Nell'esempio evidenziato di seguito, solo il valore del parametro `name` è codificato in HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-134">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="acb13-135">**Nota:** in genere si vuole localizzare solo testo e non il codice HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-135">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="acb13-136">Al livello più basso, è possibile ottenere `IStringLocalizerFactory` dall'[inserimento delle dipendenze](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="acb13-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="acb13-137">Il codice precedente illustra ognuno dei due metodi di creazione della factory.</span><span class="sxs-lookup"><span data-stu-id="acb13-137">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="acb13-138">È possibile suddividere le stringhe localizzate per controller o area oppure usare un unico contenitore.</span><span class="sxs-lookup"><span data-stu-id="acb13-138">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="acb13-139">Nell'app di esempio, viene usata una classe fittizia denominata `SharedResource` per le risorse condivise.</span><span class="sxs-lookup"><span data-stu-id="acb13-139">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="acb13-140">Alcuni sviluppatori usano la classe `Startup` per contenere le stringhe globali o condivise.</span><span class="sxs-lookup"><span data-stu-id="acb13-140">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="acb13-141">Nell'esempio seguente vengono usati i localizzatori `InfoController` e `SharedResource`:</span><span class="sxs-lookup"><span data-stu-id="acb13-141">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="acb13-142">Localizzazione delle visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="acb13-142">View localization</span></span>

<span data-ttu-id="acb13-143">Il servizio `IViewLocalizer` fornisce le stringhe localizzate per una [visualizzazione](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="acb13-143">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="acb13-144">La classe `ViewLocalizer` implementa questa interfaccia e individua la posizione della risorsa dal percorso del file della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-144">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="acb13-145">Il codice seguente illustra come usare l'implementazione predefinita di `IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="acb13-145">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="acb13-146">L'implementazione predefinita di `IViewLocalizer` individua il file di risorse in base al nome file della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-146">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="acb13-147">Non è disponibile alcuna opzione per l'uso di un file di risorse condivise globali.</span><span class="sxs-lookup"><span data-stu-id="acb13-147">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="acb13-148">`ViewLocalizer`implementa il localizzatore usando `IHtmlLocalizer` , quindi Razor non codifica la stringa localizzata in HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-148">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="acb13-149">È possibile parametrizzare le stringhe di risorsa e `IViewLocalizer` codificherà in HTML i parametri ma non la stringa di risorsa.</span><span class="sxs-lookup"><span data-stu-id="acb13-149">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="acb13-150">Si consideri il Razor markup seguente:</span><span class="sxs-lookup"><span data-stu-id="acb13-150">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="acb13-151">Un file di risorse francese può contenere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="acb13-151">A French resource file could contain the following:</span></span>

| <span data-ttu-id="acb13-152">Chiave</span><span class="sxs-lookup"><span data-stu-id="acb13-152">Key</span></span> | <span data-ttu-id="acb13-153">valore</span><span class="sxs-lookup"><span data-stu-id="acb13-153">Value</span></span> |
| ----- | ---
<span data-ttu-id="acb13-154">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-154">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-155">'Blazor'</span></span>
- <span data-ttu-id="acb13-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-156">'Identity'</span></span>
- <span data-ttu-id="acb13-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-158">'Razor'</span></span>
- <span data-ttu-id="acb13-159">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-159">'SignalR' uid:</span></span> 

<span data-ttu-id="acb13-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="acb13-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="acb13-161">La visualizzazione di cui è stato eseguito il rendering conterrà il markup HTML del file di risorse.</span><span class="sxs-lookup"><span data-stu-id="acb13-161">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="acb13-162">**Nota:** in genere si vuole localizzare solo testo e non il codice HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-162">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="acb13-163">Per usare un file di risorse condivise in una visualizzazione, inserire `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="acb13-163">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="acb13-164">Localizzazione di DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="acb13-164">DataAnnotations localization</span></span>

<span data-ttu-id="acb13-165">I messaggi di errore DataAnnotations vengono localizzati con `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="acb13-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="acb13-166">Usando l'opzione `ResourcesPath = "Resources"` è possibile memorizzare i messaggi di errore in `RegisterViewModel` in uno dei percorsi seguenti:</span><span class="sxs-lookup"><span data-stu-id="acb13-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="acb13-167">*Risorse/ViewModels. account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="acb13-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="acb13-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acb13-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="acb13-169">In ASP.NET Core MVC 1.1.0 e versioni successive, gli attributi non di convalida vengono localizzati.</span><span class="sxs-lookup"><span data-stu-id="acb13-169">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="acb13-170">ASP.NET Core MVC 1.0 **non** ricerca le stringhe localizzate per gli attributi non di convalida.</span><span class="sxs-lookup"><span data-stu-id="acb13-170">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="acb13-171">Uso di un'unica stringa di risorsa per più classi</span><span class="sxs-lookup"><span data-stu-id="acb13-171">Using one resource string for multiple classes</span></span>

<span data-ttu-id="acb13-172">Il codice seguente illustra come usare una sola stringa di risorsa per gli attributi di convalida con più classi:</span><span class="sxs-lookup"><span data-stu-id="acb13-172">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="acb13-173">Nel codice precedente `SharedResource` è la classe corrispondente al file con estensione resx in cui sono memorizzati i messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="acb13-173">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="acb13-174">Con questo approccio, DataAnnotations userà solo `SharedResource` anziché la risorsa per ogni classe.</span><span class="sxs-lookup"><span data-stu-id="acb13-174">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="acb13-175">Fornire le risorse localizzate per le lingue e le impostazioni cultura supportate</span><span class="sxs-lookup"><span data-stu-id="acb13-175">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="acb13-176">SupportedCultures e SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="acb13-176">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="acb13-177">ASP.NET Core consente di specificare due valori di impostazioni cultura, `SupportedCultures` e `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="acb13-177">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="acb13-178">L'oggetto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) per `SupportedCultures` determina i risultati delle funzioni dipendenti dalle impostazioni cultura, ad esempio date, ore, numeri e formattazione delle valute.</span><span class="sxs-lookup"><span data-stu-id="acb13-178">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="acb13-179">`SupportedCultures` determina anche l'ordinamento del testo, le convenzioni di maiuscole e minuscole e i confronti di stringhe.</span><span class="sxs-lookup"><span data-stu-id="acb13-179">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="acb13-180">Per altre informazioni su come il server ottiene le impostazioni cultura, vedere [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="acb13-180">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="acb13-181">`SupportedUICultures`Determina quali stringhe tradotte (dai file *resx* ) vengono cercate da [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="acb13-181">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="acb13-182">`ResourceManager` esegue la ricerca nelle stringhe specifiche delle impostazioni cultura determinate da `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-182">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="acb13-183">Ogni thread in .NET include oggetti `CurrentCulture` e `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-183">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="acb13-184">ASP.NET Core controlla questi valori quando viene eseguito il rendering delle funzioni dipendenti dalle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="acb13-184">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="acb13-185">Ad esempio, se le impostazioni cultura del thread corrente sono impostate su "en-US" (inglese, Stati Uniti), `DateTime.Now.ToLongDateString()` visualizza "Thursday, February 18, 2016". Se invece `CurrentCulture` è impostato su "es-ES" (spagnolo, Spagna) l'output sarà "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="acb13-185">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="acb13-186">File di risorse</span><span class="sxs-lookup"><span data-stu-id="acb13-186">Resource files</span></span>

<span data-ttu-id="acb13-187">Un file di risorse è un meccanismo utile per la separazione delle stringhe localizzabili dal codice.</span><span class="sxs-lookup"><span data-stu-id="acb13-187">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="acb13-188">Le stringhe tradotte per la lingua non predefinita sono isolate nei file di risorse *resx* .</span><span class="sxs-lookup"><span data-stu-id="acb13-188">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="acb13-189">È possibile ad esempio che si voglia creare un file di risorse spagnolo denominato *Welcome.es.resx* contenente le stringhe tradotte.</span><span class="sxs-lookup"><span data-stu-id="acb13-189">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="acb13-190">"es" è il codice di lingua per lo spagnolo.</span><span class="sxs-lookup"><span data-stu-id="acb13-190">"es" is the language code for Spanish.</span></span> <span data-ttu-id="acb13-191">Per creare questo file di risorse in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="acb13-191">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="acb13-192">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella che conterrà il file di risorse > **Aggiungi** > **Nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="acb13-192">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menu contestuale annidato: in Esplora soluzioni viene visualizzato un menu contestuale per le risorse.](localization/_static/newi.png)

2. <span data-ttu-id="acb13-195">Nella casella **Cerca modelli installati** immettere "risorse" e assegnare un nome al file.</span><span class="sxs-lookup"><span data-stu-id="acb13-195">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Finestra di dialogo Aggiungi nuovo elemento](localization/_static/res.png)

3. <span data-ttu-id="acb13-197">Immettere il valore della chiave (stringa nativa) nella colonna **Nome** e la stringa tradotta nella colonna **Valore**.</span><span class="sxs-lookup"><span data-stu-id="acb13-197">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![File Welcome.es.resx (file di risorse Welcome per lo spagnolo) con la parola Hello nella colonna Nome e la parola Hola nella colonna Valore](localization/_static/hola.png)

    <span data-ttu-id="acb13-199">Visual Studio visualizza il file *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-199">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Esplora soluzioni con il file di risorse Welcome per lo spagnolo (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="acb13-201">Denominazione dei file di risorse</span><span class="sxs-lookup"><span data-stu-id="acb13-201">Resource file naming</span></span>

<span data-ttu-id="acb13-202">Le risorse sono denominate con il nome completo del tipo della relativa classe meno il nome dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="acb13-202">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="acb13-203">Ad esempio, una risorsa francese di un progetto il cui assembly principale è `LocalizationWebsite.Web.dll` per la classe `LocalizationWebsite.Web.Startup` viene denominata *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-203">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="acb13-204">Una risorsa per la classe `LocalizationWebsite.Web.Controllers.HomeController` viene denominata *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-204">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acb13-205">Se lo spazio dei nomi della classe di destinazione non corrisponde al nome dell'assembly è necessario specificare il nome completo del tipo.</span><span class="sxs-lookup"><span data-stu-id="acb13-205">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="acb13-206">Ad esempio, nel progetto di esempio una risorsa per il tipo `ExtraNamespace.Tools` verrebbe denominata *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-206">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="acb13-207">Poiché nel progetto di esempio il metodo `ConfigureServices` imposta `ResourcesPath` su "Resources", il percorso relativo del progetto per il file di risorse francese del controller principale è *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-207">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acb13-208">In alternativa, è possibile usare le cartelle per organizzare i file di risorse.</span><span class="sxs-lookup"><span data-stu-id="acb13-208">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="acb13-209">Per il controller principale, il percorso sarà *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-209">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="acb13-210">Se non si usa l'opzione `ResourcesPath`, il file con estensione *resx* viene memorizzato nella directory di base del progetto.</span><span class="sxs-lookup"><span data-stu-id="acb13-210">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="acb13-211">Il file di risorse per `HomeController` sarà denominato *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-211">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acb13-212">La scelta di usare la convenzione di denominazione con il punto o il percorso dipende da come si vuole organizzare i file di risorse.</span><span class="sxs-lookup"><span data-stu-id="acb13-212">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="acb13-213">Nome risorsa</span><span class="sxs-lookup"><span data-stu-id="acb13-213">Resource name</span></span> | <span data-ttu-id="acb13-214">Denominazione con il punto o il percorso</span><span class="sxs-lookup"><span data-stu-id="acb13-214">Dot or path naming</span></span> |
| ---
<span data-ttu-id="acb13-215">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-215">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-216">'Blazor'</span></span>
- <span data-ttu-id="acb13-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-217">'Identity'</span></span>
- <span data-ttu-id="acb13-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-219">'Razor'</span></span>
- <span data-ttu-id="acb13-220">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-221">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-221">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-222">'Blazor'</span></span>
- <span data-ttu-id="acb13-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-223">'Identity'</span></span>
- <span data-ttu-id="acb13-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-225">'Razor'</span></span>
- <span data-ttu-id="acb13-226">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-227">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-227">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-228">'Blazor'</span></span>
- <span data-ttu-id="acb13-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-229">'Identity'</span></span>
- <span data-ttu-id="acb13-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-231">'Razor'</span></span>
- <span data-ttu-id="acb13-232">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-233">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-233">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-234">'Blazor'</span></span>
- <span data-ttu-id="acb13-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-235">'Identity'</span></span>
- <span data-ttu-id="acb13-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-237">'Razor'</span></span>
- <span data-ttu-id="acb13-238">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-238">'SignalR' uid:</span></span> 

<span data-ttu-id="acb13-239">------   | Titolo---: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-240">'Blazor'</span></span>
- <span data-ttu-id="acb13-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-241">'Identity'</span></span>
- <span data-ttu-id="acb13-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-243">'Razor'</span></span>
- <span data-ttu-id="acb13-244">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-245">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-245">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-246">'Blazor'</span></span>
- <span data-ttu-id="acb13-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-247">'Identity'</span></span>
- <span data-ttu-id="acb13-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-249">'Razor'</span></span>
- <span data-ttu-id="acb13-250">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-251">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-251">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-252">'Blazor'</span></span>
- <span data-ttu-id="acb13-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-253">'Identity'</span></span>
- <span data-ttu-id="acb13-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-255">'Razor'</span></span>
- <span data-ttu-id="acb13-256">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-257">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-257">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-258">'Blazor'</span></span>
- <span data-ttu-id="acb13-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-259">'Identity'</span></span>
- <span data-ttu-id="acb13-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-261">'Razor'</span></span>
- <span data-ttu-id="acb13-262">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-262">'SignalR' uid:</span></span> 

<span data-ttu-id="acb13-263">------- | | Risorse/Controllers. HomeController. fr. resx | Punto | | Risorse/controller/HomeController. fr. resx | Percorso | |    |     |</span><span class="sxs-lookup"><span data-stu-id="acb13-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="acb13-264">I file di risorse che usano `@inject IViewLocalizer` nelle Razor viste seguono un modello simile.</span><span class="sxs-lookup"><span data-stu-id="acb13-264">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="acb13-265">Il file di risorse per una visualizzazione può essere denominato usando la denominazione con il punto o con il percorso.</span><span class="sxs-lookup"><span data-stu-id="acb13-265">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="acb13-266">Visualizza file di risorse riproduce il percorso del file di visualizzazione associato.</span><span class="sxs-lookup"><span data-stu-id="acb13-266"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="acb13-267">Se `ResourcesPath` viene impostato su "Resources", il file di risorse francese associato alla visualizzazione *Views/Home/About.cshtml* sarà uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="acb13-267">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="acb13-268">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="acb13-268">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="acb13-269">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="acb13-269">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="acb13-270">Se non si usa l'opzione `ResourcesPath`, il file con estensione *resx* per una visualizzazione viene inserito nella stessa cartella della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-270">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="acb13-271">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="acb13-271">RootNamespaceAttribute</span></span> 

<span data-ttu-id="acb13-272">L'attributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) specifica lo spazio dei nomi radice di un assembly quando tale spazio dei nomi è diverso dal nome dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="acb13-272">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="acb13-273">Questo problema può verificarsi quando il nome di un progetto non è un identificatore .NET valido.</span><span class="sxs-lookup"><span data-stu-id="acb13-273">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="acb13-274">Ad esempio `my-project-name.csproj` , utilizzerà lo spazio dei nomi radice `my_project_name` e il nome dell'assembly `my-project-name` che genera questo errore.</span><span class="sxs-lookup"><span data-stu-id="acb13-274">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="acb13-275">Se lo spazio dei nomi radice di un assembly è diverso dal nome dell'assembly:</span><span class="sxs-lookup"><span data-stu-id="acb13-275">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="acb13-276">Per impostazione predefinita, la localizzazione non funziona.</span><span class="sxs-lookup"><span data-stu-id="acb13-276">Localization does not work by default.</span></span>
* <span data-ttu-id="acb13-277">Le risorse vengono cercate all'interno dell'assembly in un modo che impedisce la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-277">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="acb13-278">`RootNamespace` è un valore necessario in fase di compilazione che non è disponibile per il processo in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="acb13-278">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="acb13-279">Se l'attributo `RootNamespace` è diverso da `AssemblyName`, includere quanto segue in *AssemblyInfo.cs*, sostituendo i valori dei parametri con quelli effettivi:</span><span class="sxs-lookup"><span data-stu-id="acb13-279">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="acb13-280">Il codice precedente consente di risolvere correttamente i file resx.</span><span class="sxs-lookup"><span data-stu-id="acb13-280">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="acb13-281">Comportamento di fallback delle impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="acb13-281">Culture fallback behavior</span></span>

<span data-ttu-id="acb13-282">Durante la ricerca di una risorsa, la localizzazione esegue un "fallback delle impostazioni cultura".</span><span class="sxs-lookup"><span data-stu-id="acb13-282">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="acb13-283">A partire dalle impostazioni cultura richieste, se queste non vengono trovate, il codice adotta le impostazioni cultura padre di quelle richieste.</span><span class="sxs-lookup"><span data-stu-id="acb13-283">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="acb13-284">Tenere presente che la proprietà [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) rappresenta le impostazioni cultura padre.</span><span class="sxs-lookup"><span data-stu-id="acb13-284">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="acb13-285">Ciò comporta in genere (ma non sempre) la rimozione della notazione nazionale dal codice ISO.</span><span class="sxs-lookup"><span data-stu-id="acb13-285">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="acb13-286">Ad esempio la versione della lingua spagnola parlata in Messico è "es-MX".</span><span class="sxs-lookup"><span data-stu-id="acb13-286">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="acb13-287">L'elemento padre è "es", ovvero spagnolo non specifico per nessun paese.</span><span class="sxs-lookup"><span data-stu-id="acb13-287">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="acb13-288">Si supponga che il sito riceva una richiesta di una risorsa "Welcome" che usa le impostazioni cultura "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="acb13-288">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="acb13-289">Il sistema di localizzazione cerca le risorse seguenti nell'ordine elencato e seleziona la prima corrispondenza:</span><span class="sxs-lookup"><span data-stu-id="acb13-289">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="acb13-290">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="acb13-290">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="acb13-291">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acb13-291">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="acb13-292">*Welcome.resx* (se `NeutralResourcesLanguage` è "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="acb13-292">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="acb13-293">Ad esempio, se si rimuove l'indicatore delle impostazioni cultura ".fr" e le impostazioni cultura sono impostate sul francese, viene letto il file di risorse predefinito e vengono localizzate le stringhe.</span><span class="sxs-lookup"><span data-stu-id="acb13-293">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="acb13-294">Lo strumento di gestione delle risorse imposta una risorsa predefinita o di fallback per i casi in cui nessun elemento soddisfa le impostazioni cultura richieste.</span><span class="sxs-lookup"><span data-stu-id="acb13-294">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="acb13-295">Per restituire solo la chiave in caso di mancanza di una risorsa per le impostazioni cultura richieste è necessario che non sia specificato un file di risorse predefinito.</span><span class="sxs-lookup"><span data-stu-id="acb13-295">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="acb13-296">Generare file di risorse con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acb13-296">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="acb13-297">Se si crea un file di risorse in Visual Studio senza le impostazioni cultura nel nome del file (ad esempio, *Welcome.resx*), Visual Studio crea una classe C# con una proprietà per ogni stringa.</span><span class="sxs-lookup"><span data-stu-id="acb13-297">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="acb13-298">In genere questo non è il risultato desiderato quando si usa ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="acb13-298">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="acb13-299">Solitamente non è presente un file di risorse con estensione *resx* predefinito (un file con estensione *resx* senza nome delle impostazioni cultura).</span><span class="sxs-lookup"><span data-stu-id="acb13-299">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="acb13-300">Si consiglia di creare il file con estensione *resx* con un nome delle impostazioni cultura, ad esempio *Welcome.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-300">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="acb13-301">Quando si crea un file con estensione *resx* con un nome delle impostazioni cultura, Visual Studio non genera il file di classe.</span><span class="sxs-lookup"><span data-stu-id="acb13-301">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="acb13-302">Aggiungere altre impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="acb13-302">Add other cultures</span></span>

<span data-ttu-id="acb13-303">Ogni combinazione di lingua e impostazioni cultura (diversa dalla lingua predefinita) richiede un file di risorse univoco.</span><span class="sxs-lookup"><span data-stu-id="acb13-303">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="acb13-304">Creare file di risorse per impostazioni cultura e locali diverse creando nuovi file di risorse in cui i codici di lingua ISO sono inclusi nel nome file, ad esempio **en-us**, **fr-ca** e **en-gb**.</span><span class="sxs-lookup"><span data-stu-id="acb13-304">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="acb13-305">I codici ISO vengono inseriti tra il nome file e l'estensione *resx* come in *Welcome.es-MX.resx* (spagnolo/Messico).</span><span class="sxs-lookup"><span data-stu-id="acb13-305">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="acb13-306">Implementare una strategia per la selezione della lingua o delle impostazioni cultura per ogni richiesta</span><span class="sxs-lookup"><span data-stu-id="acb13-306">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="acb13-307">Configurare la localizzazione</span><span class="sxs-lookup"><span data-stu-id="acb13-307">Configure localization</span></span>

<span data-ttu-id="acb13-308">La localizzazione è configurata nel metodo `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="acb13-308">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="acb13-309">`AddLocalization` aggiunge i servizi di localizzazione al contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="acb13-309">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="acb13-310">Il codice precedente imposta anche il percorso delle risorse su "Resources".</span><span class="sxs-lookup"><span data-stu-id="acb13-310">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="acb13-311">`AddViewLocalization` aggiunge il supporto per i file di visualizzazione localizzati.</span><span class="sxs-lookup"><span data-stu-id="acb13-311">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="acb13-312">Nell'esempio la localizzazione delle visualizzazioni è basata sul suffisso dei file di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-312">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="acb13-313">Ad esempio "fr" nel file *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="acb13-313">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="acb13-314">`AddDataAnnotationsLocalization` aggiunge il supporto per i messaggi di convalida `DataAnnotations` localizzati tramite le astrazioni `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="acb13-314">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="acb13-315">Middleware di localizzazione</span><span class="sxs-lookup"><span data-stu-id="acb13-315">Localization middleware</span></span>

<span data-ttu-id="acb13-316">Le impostazioni cultura correnti in un richiesta sono impostate nel [middleware](xref:fundamentals/middleware/index) di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-316">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="acb13-317">Il middleware di localizzazione viene abilitato nel metodo `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="acb13-317">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="acb13-318">Il middleware di localizzazione deve essere configurato prima di qualsiasi middleware che potrebbe controllare le impostazioni cultura della richiesta (ad esempio, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="acb13-318">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="acb13-319">`UseRequestLocalization` inizializza un oggetto `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="acb13-319">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="acb13-320">In ogni richiesta l'elenco di `RequestCultureProvider` in `RequestLocalizationOptions` è enumerato e viene usato il primo provider in grado di determinare le impostazioni cultura della richiesta.</span><span class="sxs-lookup"><span data-stu-id="acb13-320">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="acb13-321">I provider predefiniti provengono dalla classe `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="acb13-321">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="acb13-322">L'elenco predefinito passa dal più specifico al meno specifico.</span><span class="sxs-lookup"><span data-stu-id="acb13-322">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="acb13-323">Di seguito in questo articolo viene descritto come modificare l'ordine e aggiungere un provider delle impostazioni cultura personalizzato.</span><span class="sxs-lookup"><span data-stu-id="acb13-323">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="acb13-324">Se nessuno dei provider è in grado di determinare le impostazioni cultura della richiesta, viene usato `DefaultRequestCulture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-324">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="acb13-325">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="acb13-325">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="acb13-326">Alcune app usano una stringa di query per specificare le [impostazioni cultura e le impostazioni cultura dell'interfaccia utente](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="acb13-326">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="acb13-327">Per le app che usano il cookie o l'approccio dell'intestazione Accept-Language, è utile aggiungere una stringa di query all'URL per eseguire il debug e il test del codice.</span><span class="sxs-lookup"><span data-stu-id="acb13-327">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="acb13-328">Per impostazione predefinita, `QueryStringRequestCultureProvider` viene registrato come primo provider di localizzazione nell'elenco `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="acb13-328">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="acb13-329">Passare i parametri della stringa di query `culture` e `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-329">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="acb13-330">L'esempio seguente specifica le impostazioni cultura specifiche (lingua e area) per spagnolo/Messico:</span><span class="sxs-lookup"><span data-stu-id="acb13-330">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="acb13-331">Se si passa uno solo dei due parametri (`culture` o `ui-culture`), il provider della stringa di query imposterà entrambi i valori usando il parametro passato.</span><span class="sxs-lookup"><span data-stu-id="acb13-331">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="acb13-332">Ad esempio, se si specificano solo le impostazioni cultura verranno impostati entrambi i parametri `Culture` e `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="acb13-332">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="acb13-333">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="acb13-333">CookieRequestCultureProvider</span></span>

<span data-ttu-id="acb13-334">Le app di produzione offrono spesso la possibilità di specificare le impostazioni cultura con il cookie delle impostazioni cultura di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="acb13-334">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="acb13-335">Usare il metodo `MakeCookieValue` per creare un cookie.</span><span class="sxs-lookup"><span data-stu-id="acb13-335">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="acb13-336">`CookieRequestCultureProvider` `DefaultCookieName` Restituisce il nome del cookie predefinito utilizzato per tenere traccia delle informazioni sulle impostazioni cultura preferite dell'utente.</span><span class="sxs-lookup"><span data-stu-id="acb13-336">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="acb13-337">Il nome del cookie predefinito è `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-337">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="acb13-338">Il formato del cookie è `c=%LANGCODE%|uic=%LANGCODE%`, dove `c` è `Culture` e `uic` è `UICulture`, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="acb13-338">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="acb13-339">Se si specificano solo le impostazioni cultura o le impostazioni cultura dell'interfaccia utente, le impostazioni specificate verranno usate per entrambi i tipi di impostazioni.</span><span class="sxs-lookup"><span data-stu-id="acb13-339">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="acb13-340">Intestazione HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="acb13-340">The Accept-Language HTTP header</span></span>

<span data-ttu-id="acb13-341">L'[intestazione Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) può essere impostata nella maggior parte dei browser ed è stata originariamente progettata per specificare la lingua dell'utente.</span><span class="sxs-lookup"><span data-stu-id="acb13-341">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="acb13-342">Questa impostazione indica la lingua impostata per l'invio da parte del browser o che il browser ha ereditato dal sistema operativo sottostante.</span><span class="sxs-lookup"><span data-stu-id="acb13-342">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="acb13-343">L'intestazione HTTP Accept-Language di una richiesta del browser non è un metodo infallibile per individuare la lingua preferita dell'utente (vedere [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php) (Impostazione delle preferenze di lingua in un browser)).</span><span class="sxs-lookup"><span data-stu-id="acb13-343">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="acb13-344">Un'app di produzione deve consentire all'utente di personalizzare le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="acb13-344">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="acb13-345">Impostare l'intestazione HTTP Accept-Language in IE</span><span class="sxs-lookup"><span data-stu-id="acb13-345">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="acb13-346">Dall'icona a forma di ingranaggio, toccare **Opzioni Internet**.</span><span class="sxs-lookup"><span data-stu-id="acb13-346">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="acb13-347">Toccare **Lingue**.</span><span class="sxs-lookup"><span data-stu-id="acb13-347">Tap **Languages**.</span></span>

    ![Opzioni Internet](localization/_static/lang.png)

3. <span data-ttu-id="acb13-349">Toccare **Imposta preferenze lingua**.</span><span class="sxs-lookup"><span data-stu-id="acb13-349">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="acb13-350">Toccare **Aggiungi una lingua**.</span><span class="sxs-lookup"><span data-stu-id="acb13-350">Tap **Add a language**.</span></span>

5. <span data-ttu-id="acb13-351">Aggiungere la lingua.</span><span class="sxs-lookup"><span data-stu-id="acb13-351">Add the language.</span></span>

6. <span data-ttu-id="acb13-352">Toccare la lingua e quindi toccare **Sposta su**.</span><span class="sxs-lookup"><span data-stu-id="acb13-352">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="acb13-353">Usare un provider personalizzato</span><span class="sxs-lookup"><span data-stu-id="acb13-353">Use a custom provider</span></span>

<span data-ttu-id="acb13-354">Si supponga di voler consentire agli utenti di memorizzare lingua e impostazioni cultura nei database.</span><span class="sxs-lookup"><span data-stu-id="acb13-354">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="acb13-355">È possibile creare un provider per la ricerca di questi valori per l'utente.</span><span class="sxs-lookup"><span data-stu-id="acb13-355">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="acb13-356">Il codice seguente illustra come aggiungere un provider personalizzato:</span><span class="sxs-lookup"><span data-stu-id="acb13-356">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="acb13-357">Usare `RequestLocalizationOptions` per aggiungere o rimuovere i provider di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-357">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="acb13-358">Specificare le impostazioni cultura a livello di codice</span><span class="sxs-lookup"><span data-stu-id="acb13-358">Set the culture programmatically</span></span>

<span data-ttu-id="acb13-359">Questo progetto di esempio **Localization.StarterWeb** in [GitHub](https://github.com/aspnet/entropy) contiene l'interfaccia utente per impostare `Culture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-359">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="acb13-360">Il file *Views/Shared/_SelectLanguagePartial.cshtml* consente di selezionare le impostazioni cultura dall'elenco delle impostazioni cultura supportate:</span><span class="sxs-lookup"><span data-stu-id="acb13-360">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="acb13-361">Il file *Views/Shared/_SelectLanguagePartial.cshtml* viene aggiunto alla sezione `footer` del file di layout in modo che sia disponibile in tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="acb13-361">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="acb13-362">Il metodo `SetLanguage` imposta il cookie delle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="acb13-362">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="acb13-363">Non è possibile collegare *_SelectLanguagePartial.cshtml* al codice di esempio per questo progetto.</span><span class="sxs-lookup"><span data-stu-id="acb13-363">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="acb13-364">Il progetto **Localization. StarterWeb** in [GitHub](https://github.com/aspnet/entropy) dispone di codice per il flusso `RequestLocalizationOptions` di a un oggetto Razor parziale tramite il contenitore di [inserimento delle dipendenze](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="acb13-364">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="acb13-365">Associazione di modelli dati della route e stringhe di query</span><span class="sxs-lookup"><span data-stu-id="acb13-365">Model binding route data and query strings</span></span>

<span data-ttu-id="acb13-366">Vedere [comportamento di globalizzazione delle stringhe di query e dati della route di associazione di modelli](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="acb13-366">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="acb13-367">Termini relativi alla globalizzazione e alla localizzazione</span><span class="sxs-lookup"><span data-stu-id="acb13-367">Globalization and localization terms</span></span>

<span data-ttu-id="acb13-368">Il processo di localizzazione dell'app richiede anche una conoscenza di base dei set di caratteri comunemente usati nello sviluppo del software moderno e dei problemi associati a essi.</span><span class="sxs-lookup"><span data-stu-id="acb13-368">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="acb13-369">Sebbene tutti i computer memorizzano il testo come numeri (codici), sistemi diversi memorizzano lo stesso testo usando numeri diversi.</span><span class="sxs-lookup"><span data-stu-id="acb13-369">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="acb13-370">Il processo di localizzazione si riferisce alla traduzione dell'interfaccia utente dell'app per impostazioni cultura o locali specifiche.</span><span class="sxs-lookup"><span data-stu-id="acb13-370">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="acb13-371">La [localizzabilità](/dotnet/standard/globalization-localization/localizability-review) è un processo intermedio che verifica che un'app globalizzata sia pronta per la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-371">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="acb13-372">Il formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) per il nome delle impostazioni cultura è `<languagecode2>-<country/regioncode2>`, dove `<languagecode2>` è il codice della lingua e `<country/regioncode2>` è il codice della cultura secondaria.</span><span class="sxs-lookup"><span data-stu-id="acb13-372">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="acb13-373">Ad esempio, `es-CL` per spagnolo (Cile), `en-US` per inglese (Stati Uniti) e `en-AU` per inglese (Australia).</span><span class="sxs-lookup"><span data-stu-id="acb13-373">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="acb13-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) è la combinazione di un codice di cultura in lettere minuscole di due lettere ISO 639 associato a una lingua e un codice di cultura secondaria in lettere maiuscole di due lettere ISO 3166 associato a un paese o un'area.</span><span class="sxs-lookup"><span data-stu-id="acb13-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="acb13-375">Vedere [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx) (Nome delle impostazioni cultura delle lingue).</span><span class="sxs-lookup"><span data-stu-id="acb13-375">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="acb13-376">L'internazionalizzazione è spesso abbreviata con "I18N".</span><span class="sxs-lookup"><span data-stu-id="acb13-376">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="acb13-377">L'abbreviazione include la prima e l'ultima lettera e il numero di lettere che intercorrono tra di loro nel termine inglese "Internationalization". 18 è il numero di lettere tra la prima "I" e l'ultima "N".</span><span class="sxs-lookup"><span data-stu-id="acb13-377">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="acb13-378">Lo stesso vale per globalizzazione (G11N) e localizzazione (L10N).</span><span class="sxs-lookup"><span data-stu-id="acb13-378">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="acb13-379">Termini:</span><span class="sxs-lookup"><span data-stu-id="acb13-379">Terms:</span></span>

* <span data-ttu-id="acb13-380">Globalizzazione (G11N): processo che consente a un'app di supportare lingue e aree diverse.</span><span class="sxs-lookup"><span data-stu-id="acb13-380">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="acb13-381">Localizzazione (L10N): processo di personalizzazione di un'app per una determinata lingua e area.</span><span class="sxs-lookup"><span data-stu-id="acb13-381">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="acb13-382">Internazionalizzazione (I18N): descrive la globalizzazione e la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-382">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="acb13-383">Impostazioni cultura: si tratta di una lingua e, facoltativamente, di un'area.</span><span class="sxs-lookup"><span data-stu-id="acb13-383">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="acb13-384">Impostazioni cultura neutre: cultura con una lingua specificata ma senza area.</span><span class="sxs-lookup"><span data-stu-id="acb13-384">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="acb13-385">(ad esempio "en", "es")</span><span class="sxs-lookup"><span data-stu-id="acb13-385">(for example "en", "es")</span></span>
* <span data-ttu-id="acb13-386">Impostazioni cultura specifiche: cultura con una lingua e un'area specificate.</span><span class="sxs-lookup"><span data-stu-id="acb13-386">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="acb13-387">(ad esempio "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="acb13-387">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="acb13-388">Impostazioni padre: impostazioni cultura neutre con impostazioni cultura specifiche.</span><span class="sxs-lookup"><span data-stu-id="acb13-388">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="acb13-389">(ad esempio, "en" rappresenta le impostazioni cultura padre di "en-US" e "en-GB")</span><span class="sxs-lookup"><span data-stu-id="acb13-389">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="acb13-390">Impostazioni locali: le impostazioni locali corrispondono alle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="acb13-390">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="acb13-391">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="acb13-391">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="acb13-392">[Progetto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usato nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="acb13-392">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="acb13-393">Globalizzazione e localizzazione di applicazioni .NET</span><span class="sxs-lookup"><span data-stu-id="acb13-393">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="acb13-394">Risorse nei file con estensione resx</span><span class="sxs-lookup"><span data-stu-id="acb13-394">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="acb13-395">Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="acb13-395">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="acb13-396">Localizzazione e generics</span><span class="sxs-lookup"><span data-stu-id="acb13-396">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="acb13-397">[Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="acb13-397">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="acb13-398">Un sito Web multilingue consente al sito di raggiungere un pubblico più ampio.</span><span class="sxs-lookup"><span data-stu-id="acb13-398">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="acb13-399">AP.NET Core offre servizi e middleware per la localizzazione in diverse lingue e culture.</span><span class="sxs-lookup"><span data-stu-id="acb13-399">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="acb13-400">L'internazionalizzazione implica la [globalizzazione](/dotnet/api/system.globalization) e la [localizzazione](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="acb13-400">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="acb13-401">La globalizzazione è il processo di progettazione di app che supportano impostazioni cultura diverse.</span><span class="sxs-lookup"><span data-stu-id="acb13-401">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="acb13-402">La globalizzazione aggiunge supporto per l'input, la visualizzazione e l'output di una specifica serie di script della lingua legati a determinate aree geografiche.</span><span class="sxs-lookup"><span data-stu-id="acb13-402">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="acb13-403">La localizzazione è il processo di adattamento di un'app globalizzata, già elaborata per la localizzazione, a particolari impostazioni cultura e impostazioni locali.</span><span class="sxs-lookup"><span data-stu-id="acb13-403">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="acb13-404">Per altre informazioni, vedere **Termini relativi alla globalizzazione e alla localizzazione** nella parte finale di questo documento.</span><span class="sxs-lookup"><span data-stu-id="acb13-404">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="acb13-405">La localizzazione dell'app comporta quanto segue:</span><span class="sxs-lookup"><span data-stu-id="acb13-405">App localization involves the following:</span></span>

1. <span data-ttu-id="acb13-406">Rendere localizzabile il contenuto dell'app</span><span class="sxs-lookup"><span data-stu-id="acb13-406">Make the app's content localizable</span></span>
1. <span data-ttu-id="acb13-407">Fornire le risorse localizzate per le lingue e le impostazioni cultura supportate</span><span class="sxs-lookup"><span data-stu-id="acb13-407">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="acb13-408">Implementare una strategia per la selezione della lingua o delle impostazioni cultura per ogni richiesta</span><span class="sxs-lookup"><span data-stu-id="acb13-408">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="acb13-409">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="acb13-409">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="acb13-410">Rendere localizzabile il contenuto dell'app</span><span class="sxs-lookup"><span data-stu-id="acb13-410">Make the app's content localizable</span></span>

<span data-ttu-id="acb13-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer ' non richiede l'archiviazione delle stringhe di lingua predefinite in un file di risorse.</span><span class="sxs-lookup"><span data-stu-id="acb13-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="acb13-412">È possibile sviluppare un'app per la localizzazione senza creare file di risorse nelle prime fasi di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="acb13-412">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="acb13-413">Il codice seguente illustra come eseguire il wrapping della stringa "About Title" per la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-413">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="acb13-414">Nel codice precedente, l' `IStringLocalizer<T>` implementazione deriva dall' [inserimento delle dipendenze](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="acb13-414">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="acb13-415">Se non viene trovato il valore localizzato di "About Title", viene restituita la chiave dell'indicizzatore, ovvero la stringa "About Title".</span><span class="sxs-lookup"><span data-stu-id="acb13-415">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="acb13-416">È possibile lasciare le stringhe letterali della lingua predefinita nell'app ed eseguirne il wrapping nel localizzatore per potersi concentrare sullo sviluppo dell'app.</span><span class="sxs-lookup"><span data-stu-id="acb13-416">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="acb13-417">Sviluppare l'app con la lingua predefinita e prepararla per la fase di localizzazione senza prima creare un file di risorse predefinito.</span><span class="sxs-lookup"><span data-stu-id="acb13-417">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="acb13-418">In alternativa, è possibile usare l'approccio tradizionale e fornire una chiave per recuperare la stringa della lingua predefinita.</span><span class="sxs-lookup"><span data-stu-id="acb13-418">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="acb13-419">Per molti sviluppatori il nuovo flusso di lavoro che prevede di non avere un file con estensione *resx* della lingua predefinita e di eseguire semplicemente il wrapping dei valori letterali di stringa consente di ridurre il sovraccarico della localizzazione di un'app.</span><span class="sxs-lookup"><span data-stu-id="acb13-419">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="acb13-420">Altri sviluppatori preferiscono il flusso di lavoro tradizionale poiché semplifica l'uso di valori letterali di stringa più lunghi e l'aggiornamento delle stringhe localizzate.</span><span class="sxs-lookup"><span data-stu-id="acb13-420">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="acb13-421">Usare l'implementazione `IHtmlLocalizer<T>` per le risorse che contengono HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-421">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="acb13-422">L'HTML di `IHtmlLocalizer` codifica gli argomenti formattati nella stringa di risorsa, ma non codifica in HTML la stringa di risorsa.</span><span class="sxs-lookup"><span data-stu-id="acb13-422">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="acb13-423">Nell'esempio evidenziato di seguito, solo il valore del parametro `name` è codificato in HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-423">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="acb13-424">**Nota:** in genere si vuole localizzare solo testo e non il codice HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-424">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="acb13-425">Al livello più basso, è possibile ottenere `IStringLocalizerFactory` dall'[inserimento delle dipendenze](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="acb13-425">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="acb13-426">Il codice precedente illustra ognuno dei due metodi di creazione della factory.</span><span class="sxs-lookup"><span data-stu-id="acb13-426">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="acb13-427">È possibile suddividere le stringhe localizzate per controller o area oppure usare un unico contenitore.</span><span class="sxs-lookup"><span data-stu-id="acb13-427">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="acb13-428">Nell'app di esempio, viene usata una classe fittizia denominata `SharedResource` per le risorse condivise.</span><span class="sxs-lookup"><span data-stu-id="acb13-428">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="acb13-429">Alcuni sviluppatori usano la classe `Startup` per contenere le stringhe globali o condivise.</span><span class="sxs-lookup"><span data-stu-id="acb13-429">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="acb13-430">Nell'esempio seguente vengono usati i localizzatori `InfoController` e `SharedResource`:</span><span class="sxs-lookup"><span data-stu-id="acb13-430">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="acb13-431">Localizzazione delle visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="acb13-431">View localization</span></span>

<span data-ttu-id="acb13-432">Il servizio `IViewLocalizer` fornisce le stringhe localizzate per una [visualizzazione](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="acb13-432">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="acb13-433">La classe `ViewLocalizer` implementa questa interfaccia e individua la posizione della risorsa dal percorso del file della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-433">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="acb13-434">Il codice seguente illustra come usare l'implementazione predefinita di `IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="acb13-434">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="acb13-435">L'implementazione predefinita di `IViewLocalizer` individua il file di risorse in base al nome file della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-435">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="acb13-436">Non è disponibile alcuna opzione per l'uso di un file di risorse condivise globali.</span><span class="sxs-lookup"><span data-stu-id="acb13-436">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="acb13-437">`ViewLocalizer`implementa il localizzatore usando `IHtmlLocalizer` , quindi Razor non codifica la stringa localizzata in HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-437">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="acb13-438">È possibile parametrizzare le stringhe di risorsa e `IViewLocalizer` codificherà in HTML i parametri ma non la stringa di risorsa.</span><span class="sxs-lookup"><span data-stu-id="acb13-438">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="acb13-439">Si consideri il Razor markup seguente:</span><span class="sxs-lookup"><span data-stu-id="acb13-439">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="acb13-440">Un file di risorse francese può contenere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="acb13-440">A French resource file could contain the following:</span></span>

| <span data-ttu-id="acb13-441">Chiave</span><span class="sxs-lookup"><span data-stu-id="acb13-441">Key</span></span> | <span data-ttu-id="acb13-442">valore</span><span class="sxs-lookup"><span data-stu-id="acb13-442">Value</span></span> |
| ----- | ---
<span data-ttu-id="acb13-443">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-443">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-444">'Blazor'</span></span>
- <span data-ttu-id="acb13-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-445">'Identity'</span></span>
- <span data-ttu-id="acb13-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-447">'Razor'</span></span>
- <span data-ttu-id="acb13-448">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-448">'SignalR' uid:</span></span> 

<span data-ttu-id="acb13-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="acb13-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="acb13-450">La visualizzazione di cui è stato eseguito il rendering conterrà il markup HTML del file di risorse.</span><span class="sxs-lookup"><span data-stu-id="acb13-450">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="acb13-451">**Nota:** in genere si vuole localizzare solo testo e non il codice HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-451">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="acb13-452">Per usare un file di risorse condivise in una visualizzazione, inserire `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="acb13-452">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="acb13-453">Localizzazione di DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="acb13-453">DataAnnotations localization</span></span>

<span data-ttu-id="acb13-454">I messaggi di errore DataAnnotations vengono localizzati con `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="acb13-454">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="acb13-455">Usando l'opzione `ResourcesPath = "Resources"` è possibile memorizzare i messaggi di errore in `RegisterViewModel` in uno dei percorsi seguenti:</span><span class="sxs-lookup"><span data-stu-id="acb13-455">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="acb13-456">*Risorse/ViewModels. account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="acb13-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="acb13-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acb13-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="acb13-458">In ASP.NET Core MVC 1.1.0 e versioni successive, gli attributi non di convalida vengono localizzati.</span><span class="sxs-lookup"><span data-stu-id="acb13-458">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="acb13-459">ASP.NET Core MVC 1.0 **non** ricerca le stringhe localizzate per gli attributi non di convalida.</span><span class="sxs-lookup"><span data-stu-id="acb13-459">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="acb13-460">Uso di un'unica stringa di risorsa per più classi</span><span class="sxs-lookup"><span data-stu-id="acb13-460">Using one resource string for multiple classes</span></span>

<span data-ttu-id="acb13-461">Il codice seguente illustra come usare una sola stringa di risorsa per gli attributi di convalida con più classi:</span><span class="sxs-lookup"><span data-stu-id="acb13-461">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="acb13-462">Nel codice precedente `SharedResource` è la classe corrispondente al file con estensione resx in cui sono memorizzati i messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="acb13-462">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="acb13-463">Con questo approccio, DataAnnotations userà solo `SharedResource` anziché la risorsa per ogni classe.</span><span class="sxs-lookup"><span data-stu-id="acb13-463">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="acb13-464">Fornire le risorse localizzate per le lingue e le impostazioni cultura supportate</span><span class="sxs-lookup"><span data-stu-id="acb13-464">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="acb13-465">SupportedCultures e SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="acb13-465">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="acb13-466">ASP.NET Core consente di specificare due valori di impostazioni cultura, `SupportedCultures` e `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="acb13-466">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="acb13-467">L'oggetto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) per `SupportedCultures` determina i risultati delle funzioni dipendenti dalle impostazioni cultura, ad esempio date, ore, numeri e formattazione delle valute.</span><span class="sxs-lookup"><span data-stu-id="acb13-467">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="acb13-468">`SupportedCultures` determina anche l'ordinamento del testo, le convenzioni di maiuscole e minuscole e i confronti di stringhe.</span><span class="sxs-lookup"><span data-stu-id="acb13-468">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="acb13-469">Per altre informazioni su come il server ottiene le impostazioni cultura, vedere [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="acb13-469">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="acb13-470">`SupportedUICultures`Determina quali stringhe tradotte (dai file *resx* ) vengono cercate da [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="acb13-470">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="acb13-471">`ResourceManager` esegue la ricerca nelle stringhe specifiche delle impostazioni cultura determinate da `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-471">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="acb13-472">Ogni thread in .NET include oggetti `CurrentCulture` e `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-472">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="acb13-473">ASP.NET Core controlla questi valori quando viene eseguito il rendering delle funzioni dipendenti dalle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="acb13-473">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="acb13-474">Ad esempio, se le impostazioni cultura del thread corrente sono impostate su "en-US" (inglese, Stati Uniti), `DateTime.Now.ToLongDateString()` visualizza "Thursday, February 18, 2016". Se invece `CurrentCulture` è impostato su "es-ES" (spagnolo, Spagna) l'output sarà "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="acb13-474">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="acb13-475">File di risorse</span><span class="sxs-lookup"><span data-stu-id="acb13-475">Resource files</span></span>

<span data-ttu-id="acb13-476">Un file di risorse è un meccanismo utile per la separazione delle stringhe localizzabili dal codice.</span><span class="sxs-lookup"><span data-stu-id="acb13-476">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="acb13-477">Le stringhe tradotte per la lingua non predefinita sono isolate nei file di risorse *resx* .</span><span class="sxs-lookup"><span data-stu-id="acb13-477">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="acb13-478">È possibile ad esempio che si voglia creare un file di risorse spagnolo denominato *Welcome.es.resx* contenente le stringhe tradotte.</span><span class="sxs-lookup"><span data-stu-id="acb13-478">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="acb13-479">"es" è il codice di lingua per lo spagnolo.</span><span class="sxs-lookup"><span data-stu-id="acb13-479">"es" is the language code for Spanish.</span></span> <span data-ttu-id="acb13-480">Per creare questo file di risorse in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="acb13-480">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="acb13-481">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella che conterrà il file di risorse > **Aggiungi** > **Nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="acb13-481">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menu contestuale annidato: in Esplora soluzioni viene visualizzato un menu contestuale per le risorse.](localization/_static/newi.png)

2. <span data-ttu-id="acb13-484">Nella casella **Cerca modelli installati** immettere "risorse" e assegnare un nome al file.</span><span class="sxs-lookup"><span data-stu-id="acb13-484">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Finestra di dialogo Aggiungi nuovo elemento](localization/_static/res.png)

3. <span data-ttu-id="acb13-486">Immettere il valore della chiave (stringa nativa) nella colonna **Nome** e la stringa tradotta nella colonna **Valore**.</span><span class="sxs-lookup"><span data-stu-id="acb13-486">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![File Welcome.es.resx (file di risorse Welcome per lo spagnolo) con la parola Hello nella colonna Nome e la parola Hola nella colonna Valore](localization/_static/hola.png)

    <span data-ttu-id="acb13-488">Visual Studio visualizza il file *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-488">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Esplora soluzioni con il file di risorse Welcome per lo spagnolo (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="acb13-490">Denominazione dei file di risorse</span><span class="sxs-lookup"><span data-stu-id="acb13-490">Resource file naming</span></span>

<span data-ttu-id="acb13-491">Le risorse sono denominate con il nome completo del tipo della relativa classe meno il nome dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="acb13-491">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="acb13-492">Ad esempio, una risorsa francese di un progetto il cui assembly principale è `LocalizationWebsite.Web.dll` per la classe `LocalizationWebsite.Web.Startup` viene denominata *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-492">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="acb13-493">Una risorsa per la classe `LocalizationWebsite.Web.Controllers.HomeController` viene denominata *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-493">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acb13-494">Se lo spazio dei nomi della classe di destinazione non corrisponde al nome dell'assembly è necessario specificare il nome completo del tipo.</span><span class="sxs-lookup"><span data-stu-id="acb13-494">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="acb13-495">Ad esempio, nel progetto di esempio una risorsa per il tipo `ExtraNamespace.Tools` verrebbe denominata *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-495">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="acb13-496">Poiché nel progetto di esempio il metodo `ConfigureServices` imposta `ResourcesPath` su "Resources", il percorso relativo del progetto per il file di risorse francese del controller principale è *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-496">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acb13-497">In alternativa, è possibile usare le cartelle per organizzare i file di risorse.</span><span class="sxs-lookup"><span data-stu-id="acb13-497">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="acb13-498">Per il controller principale, il percorso sarà *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-498">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="acb13-499">Se non si usa l'opzione `ResourcesPath`, il file con estensione *resx* viene memorizzato nella directory di base del progetto.</span><span class="sxs-lookup"><span data-stu-id="acb13-499">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="acb13-500">Il file di risorse per `HomeController` sarà denominato *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-500">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acb13-501">La scelta di usare la convenzione di denominazione con il punto o il percorso dipende da come si vuole organizzare i file di risorse.</span><span class="sxs-lookup"><span data-stu-id="acb13-501">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="acb13-502">Nome risorsa</span><span class="sxs-lookup"><span data-stu-id="acb13-502">Resource name</span></span> | <span data-ttu-id="acb13-503">Denominazione con il punto o il percorso</span><span class="sxs-lookup"><span data-stu-id="acb13-503">Dot or path naming</span></span> |
| ---
<span data-ttu-id="acb13-504">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-504">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-505">'Blazor'</span></span>
- <span data-ttu-id="acb13-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-506">'Identity'</span></span>
- <span data-ttu-id="acb13-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-508">'Razor'</span></span>
- <span data-ttu-id="acb13-509">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-510">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-510">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-511">'Blazor'</span></span>
- <span data-ttu-id="acb13-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-512">'Identity'</span></span>
- <span data-ttu-id="acb13-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-514">'Razor'</span></span>
- <span data-ttu-id="acb13-515">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-516">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-516">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-517">'Blazor'</span></span>
- <span data-ttu-id="acb13-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-518">'Identity'</span></span>
- <span data-ttu-id="acb13-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-520">'Razor'</span></span>
- <span data-ttu-id="acb13-521">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-522">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-522">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-523">'Blazor'</span></span>
- <span data-ttu-id="acb13-524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-524">'Identity'</span></span>
- <span data-ttu-id="acb13-525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-525">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-526">'Razor'</span></span>
- <span data-ttu-id="acb13-527">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-527">'SignalR' uid:</span></span> 

<span data-ttu-id="acb13-528">------   | Titolo---: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-529">'Blazor'</span></span>
- <span data-ttu-id="acb13-530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-530">'Identity'</span></span>
- <span data-ttu-id="acb13-531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-531">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-532">'Razor'</span></span>
- <span data-ttu-id="acb13-533">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-533">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-534">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-534">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-535">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-535">'Blazor'</span></span>
- <span data-ttu-id="acb13-536">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-536">'Identity'</span></span>
- <span data-ttu-id="acb13-537">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-537">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-538">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-538">'Razor'</span></span>
- <span data-ttu-id="acb13-539">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-539">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-540">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-540">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-541">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-541">'Blazor'</span></span>
- <span data-ttu-id="acb13-542">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-542">'Identity'</span></span>
- <span data-ttu-id="acb13-543">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-543">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-544">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-544">'Razor'</span></span>
- <span data-ttu-id="acb13-545">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-545">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-546">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-546">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-547">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-547">'Blazor'</span></span>
- <span data-ttu-id="acb13-548">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-548">'Identity'</span></span>
- <span data-ttu-id="acb13-549">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-549">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-550">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-550">'Razor'</span></span>
- <span data-ttu-id="acb13-551">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-551">'SignalR' uid:</span></span> 

<span data-ttu-id="acb13-552">------- | | Risorse/Controllers. HomeController. fr. resx | Punto | | Risorse/controller/HomeController. fr. resx | Percorso | |    |     |</span><span class="sxs-lookup"><span data-stu-id="acb13-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="acb13-553">I file di risorse che usano `@inject IViewLocalizer` nelle Razor viste seguono un modello simile.</span><span class="sxs-lookup"><span data-stu-id="acb13-553">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="acb13-554">Il file di risorse per una visualizzazione può essere denominato usando la denominazione con il punto o con il percorso.</span><span class="sxs-lookup"><span data-stu-id="acb13-554">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="acb13-555">Visualizza file di risorse riproduce il percorso del file di visualizzazione associato.</span><span class="sxs-lookup"><span data-stu-id="acb13-555"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="acb13-556">Se `ResourcesPath` viene impostato su "Resources", il file di risorse francese associato alla visualizzazione *Views/Home/About.cshtml* sarà uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="acb13-556">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="acb13-557">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="acb13-557">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="acb13-558">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="acb13-558">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="acb13-559">Se non si usa l'opzione `ResourcesPath`, il file con estensione *resx* per una visualizzazione viene inserito nella stessa cartella della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-559">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="acb13-560">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="acb13-560">RootNamespaceAttribute</span></span> 

<span data-ttu-id="acb13-561">L'attributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) specifica lo spazio dei nomi radice di un assembly quando tale spazio dei nomi è diverso dal nome dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="acb13-561">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="acb13-562">Questo problema può verificarsi quando il nome di un progetto non è un identificatore .NET valido.</span><span class="sxs-lookup"><span data-stu-id="acb13-562">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="acb13-563">Ad esempio `my-project-name.csproj` , utilizzerà lo spazio dei nomi radice `my_project_name` e il nome dell'assembly `my-project-name` che genera questo errore.</span><span class="sxs-lookup"><span data-stu-id="acb13-563">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="acb13-564">Se lo spazio dei nomi radice di un assembly è diverso dal nome dell'assembly:</span><span class="sxs-lookup"><span data-stu-id="acb13-564">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="acb13-565">Per impostazione predefinita, la localizzazione non funziona.</span><span class="sxs-lookup"><span data-stu-id="acb13-565">Localization does not work by default.</span></span>
* <span data-ttu-id="acb13-566">Le risorse vengono cercate all'interno dell'assembly in un modo che impedisce la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-566">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="acb13-567">`RootNamespace` è un valore necessario in fase di compilazione che non è disponibile per il processo in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="acb13-567">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="acb13-568">Se l'attributo `RootNamespace` è diverso da `AssemblyName`, includere quanto segue in *AssemblyInfo.cs*, sostituendo i valori dei parametri con quelli effettivi:</span><span class="sxs-lookup"><span data-stu-id="acb13-568">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="acb13-569">Il codice precedente consente di risolvere correttamente i file resx.</span><span class="sxs-lookup"><span data-stu-id="acb13-569">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="acb13-570">Comportamento di fallback delle impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="acb13-570">Culture fallback behavior</span></span>

<span data-ttu-id="acb13-571">Durante la ricerca di una risorsa, la localizzazione esegue un "fallback delle impostazioni cultura".</span><span class="sxs-lookup"><span data-stu-id="acb13-571">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="acb13-572">A partire dalle impostazioni cultura richieste, se queste non vengono trovate, il codice adotta le impostazioni cultura padre di quelle richieste.</span><span class="sxs-lookup"><span data-stu-id="acb13-572">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="acb13-573">Tenere presente che la proprietà [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) rappresenta le impostazioni cultura padre.</span><span class="sxs-lookup"><span data-stu-id="acb13-573">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="acb13-574">Ciò comporta in genere (ma non sempre) la rimozione della notazione nazionale dal codice ISO.</span><span class="sxs-lookup"><span data-stu-id="acb13-574">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="acb13-575">Ad esempio la versione della lingua spagnola parlata in Messico è "es-MX".</span><span class="sxs-lookup"><span data-stu-id="acb13-575">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="acb13-576">L'elemento padre è "es", ovvero spagnolo non specifico per nessun paese.</span><span class="sxs-lookup"><span data-stu-id="acb13-576">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="acb13-577">Si supponga che il sito riceva una richiesta di una risorsa "Welcome" che usa le impostazioni cultura "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="acb13-577">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="acb13-578">Il sistema di localizzazione cerca le risorse seguenti nell'ordine elencato e seleziona la prima corrispondenza:</span><span class="sxs-lookup"><span data-stu-id="acb13-578">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="acb13-579">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="acb13-579">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="acb13-580">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acb13-580">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="acb13-581">*Welcome.resx* (se `NeutralResourcesLanguage` è "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="acb13-581">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="acb13-582">Ad esempio, se si rimuove l'indicatore delle impostazioni cultura ".fr" e le impostazioni cultura sono impostate sul francese, viene letto il file di risorse predefinito e vengono localizzate le stringhe.</span><span class="sxs-lookup"><span data-stu-id="acb13-582">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="acb13-583">Lo strumento di gestione delle risorse imposta una risorsa predefinita o di fallback per i casi in cui nessun elemento soddisfa le impostazioni cultura richieste.</span><span class="sxs-lookup"><span data-stu-id="acb13-583">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="acb13-584">Per restituire solo la chiave in caso di mancanza di una risorsa per le impostazioni cultura richieste è necessario che non sia specificato un file di risorse predefinito.</span><span class="sxs-lookup"><span data-stu-id="acb13-584">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="acb13-585">Generare file di risorse con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acb13-585">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="acb13-586">Se si crea un file di risorse in Visual Studio senza le impostazioni cultura nel nome del file (ad esempio, *Welcome.resx*), Visual Studio crea una classe C# con una proprietà per ogni stringa.</span><span class="sxs-lookup"><span data-stu-id="acb13-586">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="acb13-587">In genere questo non è il risultato desiderato quando si usa ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="acb13-587">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="acb13-588">Solitamente non è presente un file di risorse con estensione *resx* predefinito (un file con estensione *resx* senza nome delle impostazioni cultura).</span><span class="sxs-lookup"><span data-stu-id="acb13-588">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="acb13-589">Si consiglia di creare il file con estensione *resx* con un nome delle impostazioni cultura, ad esempio *Welcome.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-589">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="acb13-590">Quando si crea un file con estensione *resx* con un nome delle impostazioni cultura, Visual Studio non genera il file di classe.</span><span class="sxs-lookup"><span data-stu-id="acb13-590">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="acb13-591">Aggiungere altre impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="acb13-591">Add other cultures</span></span>

<span data-ttu-id="acb13-592">Ogni combinazione di lingua e impostazioni cultura (diversa dalla lingua predefinita) richiede un file di risorse univoco.</span><span class="sxs-lookup"><span data-stu-id="acb13-592">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="acb13-593">Creare file di risorse per impostazioni cultura e locali diverse creando nuovi file di risorse in cui i codici di lingua ISO sono inclusi nel nome file, ad esempio **en-us**, **fr-ca** e **en-gb**.</span><span class="sxs-lookup"><span data-stu-id="acb13-593">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="acb13-594">I codici ISO vengono inseriti tra il nome file e l'estensione *resx* come in *Welcome.es-MX.resx* (spagnolo/Messico).</span><span class="sxs-lookup"><span data-stu-id="acb13-594">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="acb13-595">Implementare una strategia per la selezione della lingua o delle impostazioni cultura per ogni richiesta</span><span class="sxs-lookup"><span data-stu-id="acb13-595">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="acb13-596">Configurare la localizzazione</span><span class="sxs-lookup"><span data-stu-id="acb13-596">Configure localization</span></span>

<span data-ttu-id="acb13-597">La localizzazione è configurata nel metodo `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="acb13-597">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="acb13-598">`AddLocalization` aggiunge i servizi di localizzazione al contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="acb13-598">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="acb13-599">Il codice precedente imposta anche il percorso delle risorse su "Resources".</span><span class="sxs-lookup"><span data-stu-id="acb13-599">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="acb13-600">`AddViewLocalization` aggiunge il supporto per i file di visualizzazione localizzati.</span><span class="sxs-lookup"><span data-stu-id="acb13-600">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="acb13-601">Nell'esempio la localizzazione delle visualizzazioni è basata sul suffisso dei file di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-601">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="acb13-602">Ad esempio "fr" nel file *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="acb13-602">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="acb13-603">`AddDataAnnotationsLocalization` aggiunge il supporto per i messaggi di convalida `DataAnnotations` localizzati tramite le astrazioni `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="acb13-603">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="acb13-604">Middleware di localizzazione</span><span class="sxs-lookup"><span data-stu-id="acb13-604">Localization middleware</span></span>

<span data-ttu-id="acb13-605">Le impostazioni cultura correnti in un richiesta sono impostate nel [middleware](xref:fundamentals/middleware/index) di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-605">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="acb13-606">Il middleware di localizzazione viene abilitato nel metodo `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="acb13-606">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="acb13-607">Il middleware di localizzazione deve essere configurato prima di qualsiasi middleware che potrebbe controllare le impostazioni cultura della richiesta (ad esempio, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="acb13-607">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="acb13-608">`UseRequestLocalization` inizializza un oggetto `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="acb13-608">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="acb13-609">In ogni richiesta l'elenco di `RequestCultureProvider` in `RequestLocalizationOptions` è enumerato e viene usato il primo provider in grado di determinare le impostazioni cultura della richiesta.</span><span class="sxs-lookup"><span data-stu-id="acb13-609">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="acb13-610">I provider predefiniti provengono dalla classe `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="acb13-610">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="acb13-611">L'elenco predefinito passa dal più specifico al meno specifico.</span><span class="sxs-lookup"><span data-stu-id="acb13-611">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="acb13-612">Di seguito in questo articolo viene descritto come modificare l'ordine e aggiungere un provider delle impostazioni cultura personalizzato.</span><span class="sxs-lookup"><span data-stu-id="acb13-612">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="acb13-613">Se nessuno dei provider è in grado di determinare le impostazioni cultura della richiesta, viene usato `DefaultRequestCulture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-613">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="acb13-614">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="acb13-614">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="acb13-615">Alcune app usano una stringa di query per specificare le [impostazioni cultura e le impostazioni cultura dell'interfaccia utente](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="acb13-615">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="acb13-616">Per le app che usano il cookie o l'approccio dell'intestazione Accept-Language, è utile aggiungere una stringa di query all'URL per eseguire il debug e il test del codice.</span><span class="sxs-lookup"><span data-stu-id="acb13-616">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="acb13-617">Per impostazione predefinita, `QueryStringRequestCultureProvider` viene registrato come primo provider di localizzazione nell'elenco `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="acb13-617">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="acb13-618">Passare i parametri della stringa di query `culture` e `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-618">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="acb13-619">L'esempio seguente specifica le impostazioni cultura specifiche (lingua e area) per spagnolo/Messico:</span><span class="sxs-lookup"><span data-stu-id="acb13-619">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="acb13-620">Se si passa uno solo dei due parametri (`culture` o `ui-culture`), il provider della stringa di query imposterà entrambi i valori usando il parametro passato.</span><span class="sxs-lookup"><span data-stu-id="acb13-620">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="acb13-621">Ad esempio, se si specificano solo le impostazioni cultura verranno impostati entrambi i parametri `Culture` e `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="acb13-621">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="acb13-622">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="acb13-622">CookieRequestCultureProvider</span></span>

<span data-ttu-id="acb13-623">Le app di produzione offrono spesso la possibilità di specificare le impostazioni cultura con il cookie delle impostazioni cultura di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="acb13-623">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="acb13-624">Usare il metodo `MakeCookieValue` per creare un cookie.</span><span class="sxs-lookup"><span data-stu-id="acb13-624">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="acb13-625">`CookieRequestCultureProvider` `DefaultCookieName` Restituisce il nome del cookie predefinito utilizzato per tenere traccia delle informazioni sulle impostazioni cultura preferite dell'utente.</span><span class="sxs-lookup"><span data-stu-id="acb13-625">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="acb13-626">Il nome del cookie predefinito è `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-626">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="acb13-627">Il formato del cookie è `c=%LANGCODE%|uic=%LANGCODE%`, dove `c` è `Culture` e `uic` è `UICulture`, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="acb13-627">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="acb13-628">Se si specificano solo le impostazioni cultura o le impostazioni cultura dell'interfaccia utente, le impostazioni specificate verranno usate per entrambi i tipi di impostazioni.</span><span class="sxs-lookup"><span data-stu-id="acb13-628">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="acb13-629">Intestazione HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="acb13-629">The Accept-Language HTTP header</span></span>

<span data-ttu-id="acb13-630">L'[intestazione Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) può essere impostata nella maggior parte dei browser ed è stata originariamente progettata per specificare la lingua dell'utente.</span><span class="sxs-lookup"><span data-stu-id="acb13-630">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="acb13-631">Questa impostazione indica la lingua impostata per l'invio da parte del browser o che il browser ha ereditato dal sistema operativo sottostante.</span><span class="sxs-lookup"><span data-stu-id="acb13-631">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="acb13-632">L'intestazione HTTP Accept-Language di una richiesta del browser non è un metodo infallibile per individuare la lingua preferita dell'utente (vedere [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php) (Impostazione delle preferenze di lingua in un browser)).</span><span class="sxs-lookup"><span data-stu-id="acb13-632">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="acb13-633">Un'app di produzione deve consentire all'utente di personalizzare le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="acb13-633">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="acb13-634">Impostare l'intestazione HTTP Accept-Language in IE</span><span class="sxs-lookup"><span data-stu-id="acb13-634">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="acb13-635">Dall'icona a forma di ingranaggio, toccare **Opzioni Internet**.</span><span class="sxs-lookup"><span data-stu-id="acb13-635">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="acb13-636">Toccare **Lingue**.</span><span class="sxs-lookup"><span data-stu-id="acb13-636">Tap **Languages**.</span></span>

    ![Opzioni Internet](localization/_static/lang.png)

3. <span data-ttu-id="acb13-638">Toccare **Imposta preferenze lingua**.</span><span class="sxs-lookup"><span data-stu-id="acb13-638">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="acb13-639">Toccare **Aggiungi una lingua**.</span><span class="sxs-lookup"><span data-stu-id="acb13-639">Tap **Add a language**.</span></span>

5. <span data-ttu-id="acb13-640">Aggiungere la lingua.</span><span class="sxs-lookup"><span data-stu-id="acb13-640">Add the language.</span></span>

6. <span data-ttu-id="acb13-641">Toccare la lingua e quindi toccare **Sposta su**.</span><span class="sxs-lookup"><span data-stu-id="acb13-641">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="acb13-642">Usare un provider personalizzato</span><span class="sxs-lookup"><span data-stu-id="acb13-642">Use a custom provider</span></span>

<span data-ttu-id="acb13-643">Si supponga di voler consentire agli utenti di memorizzare lingua e impostazioni cultura nei database.</span><span class="sxs-lookup"><span data-stu-id="acb13-643">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="acb13-644">È possibile creare un provider per la ricerca di questi valori per l'utente.</span><span class="sxs-lookup"><span data-stu-id="acb13-644">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="acb13-645">Il codice seguente illustra come aggiungere un provider personalizzato:</span><span class="sxs-lookup"><span data-stu-id="acb13-645">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="acb13-646">Usare `RequestLocalizationOptions` per aggiungere o rimuovere i provider di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-646">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="acb13-647">Specificare le impostazioni cultura a livello di codice</span><span class="sxs-lookup"><span data-stu-id="acb13-647">Set the culture programmatically</span></span>

<span data-ttu-id="acb13-648">Questo progetto di esempio **Localization.StarterWeb** in [GitHub](https://github.com/aspnet/entropy) contiene l'interfaccia utente per impostare `Culture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-648">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="acb13-649">Il file *Views/Shared/_SelectLanguagePartial.cshtml* consente di selezionare le impostazioni cultura dall'elenco delle impostazioni cultura supportate:</span><span class="sxs-lookup"><span data-stu-id="acb13-649">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="acb13-650">Il file *Views/Shared/_SelectLanguagePartial.cshtml* viene aggiunto alla sezione `footer` del file di layout in modo che sia disponibile in tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="acb13-650">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="acb13-651">Il metodo `SetLanguage` imposta il cookie delle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="acb13-651">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="acb13-652">Non è possibile collegare *_SelectLanguagePartial.cshtml* al codice di esempio per questo progetto.</span><span class="sxs-lookup"><span data-stu-id="acb13-652">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="acb13-653">Il progetto **Localization. StarterWeb** in [GitHub](https://github.com/aspnet/entropy) dispone di codice per il flusso `RequestLocalizationOptions` di a un oggetto Razor parziale tramite il contenitore di [inserimento delle dipendenze](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="acb13-653">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="acb13-654">Associazione di modelli dati della route e stringhe di query</span><span class="sxs-lookup"><span data-stu-id="acb13-654">Model binding route data and query strings</span></span>

<span data-ttu-id="acb13-655">Vedere [comportamento di globalizzazione delle stringhe di query e dati della route di associazione di modelli](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="acb13-655">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="acb13-656">Termini relativi alla globalizzazione e alla localizzazione</span><span class="sxs-lookup"><span data-stu-id="acb13-656">Globalization and localization terms</span></span>

<span data-ttu-id="acb13-657">Il processo di localizzazione dell'app richiede anche una conoscenza di base dei set di caratteri comunemente usati nello sviluppo del software moderno e dei problemi associati a essi.</span><span class="sxs-lookup"><span data-stu-id="acb13-657">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="acb13-658">Sebbene tutti i computer memorizzano il testo come numeri (codici), sistemi diversi memorizzano lo stesso testo usando numeri diversi.</span><span class="sxs-lookup"><span data-stu-id="acb13-658">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="acb13-659">Il processo di localizzazione si riferisce alla traduzione dell'interfaccia utente dell'app per impostazioni cultura o locali specifiche.</span><span class="sxs-lookup"><span data-stu-id="acb13-659">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="acb13-660">La [localizzabilità](/dotnet/standard/globalization-localization/localizability-review) è un processo intermedio che verifica che un'app globalizzata sia pronta per la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-660">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="acb13-661">Il formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) per il nome delle impostazioni cultura è `<languagecode2>-<country/regioncode2>`, dove `<languagecode2>` è il codice della lingua e `<country/regioncode2>` è il codice della cultura secondaria.</span><span class="sxs-lookup"><span data-stu-id="acb13-661">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="acb13-662">Ad esempio, `es-CL` per spagnolo (Cile), `en-US` per inglese (Stati Uniti) e `en-AU` per inglese (Australia).</span><span class="sxs-lookup"><span data-stu-id="acb13-662">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="acb13-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) è la combinazione di un codice di cultura in lettere minuscole di due lettere ISO 639 associato a una lingua e un codice di cultura secondaria in lettere maiuscole di due lettere ISO 3166 associato a un paese o un'area.</span><span class="sxs-lookup"><span data-stu-id="acb13-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="acb13-664">Vedere [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx) (Nome delle impostazioni cultura delle lingue).</span><span class="sxs-lookup"><span data-stu-id="acb13-664">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="acb13-665">L'internazionalizzazione è spesso abbreviata con "I18N".</span><span class="sxs-lookup"><span data-stu-id="acb13-665">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="acb13-666">L'abbreviazione include la prima e l'ultima lettera e il numero di lettere che intercorrono tra di loro nel termine inglese "Internationalization". 18 è il numero di lettere tra la prima "I" e l'ultima "N".</span><span class="sxs-lookup"><span data-stu-id="acb13-666">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="acb13-667">Lo stesso vale per globalizzazione (G11N) e localizzazione (L10N).</span><span class="sxs-lookup"><span data-stu-id="acb13-667">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="acb13-668">Termini:</span><span class="sxs-lookup"><span data-stu-id="acb13-668">Terms:</span></span>

* <span data-ttu-id="acb13-669">Globalizzazione (G11N): processo che consente a un'app di supportare lingue e aree diverse.</span><span class="sxs-lookup"><span data-stu-id="acb13-669">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="acb13-670">Localizzazione (L10N): processo di personalizzazione di un'app per una determinata lingua e area.</span><span class="sxs-lookup"><span data-stu-id="acb13-670">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="acb13-671">Internazionalizzazione (I18N): descrive la globalizzazione e la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-671">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="acb13-672">Impostazioni cultura: si tratta di una lingua e, facoltativamente, di un'area.</span><span class="sxs-lookup"><span data-stu-id="acb13-672">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="acb13-673">Impostazioni cultura neutre: cultura con una lingua specificata ma senza area.</span><span class="sxs-lookup"><span data-stu-id="acb13-673">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="acb13-674">(ad esempio "en", "es")</span><span class="sxs-lookup"><span data-stu-id="acb13-674">(for example "en", "es")</span></span>
* <span data-ttu-id="acb13-675">Impostazioni cultura specifiche: cultura con una lingua e un'area specificate.</span><span class="sxs-lookup"><span data-stu-id="acb13-675">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="acb13-676">(ad esempio "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="acb13-676">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="acb13-677">Impostazioni padre: impostazioni cultura neutre con impostazioni cultura specifiche.</span><span class="sxs-lookup"><span data-stu-id="acb13-677">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="acb13-678">(ad esempio, "en" rappresenta le impostazioni cultura padre di "en-US" e "en-GB")</span><span class="sxs-lookup"><span data-stu-id="acb13-678">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="acb13-679">Impostazioni locali: le impostazioni locali corrispondono alle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="acb13-679">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="acb13-680">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="acb13-680">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="acb13-681">[Progetto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usato nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="acb13-681">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="acb13-682">Globalizzazione e localizzazione di applicazioni .NET</span><span class="sxs-lookup"><span data-stu-id="acb13-682">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="acb13-683">Risorse nei file con estensione resx</span><span class="sxs-lookup"><span data-stu-id="acb13-683">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="acb13-684">Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="acb13-684">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="acb13-685">Localizzazione e generics</span><span class="sxs-lookup"><span data-stu-id="acb13-685">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="acb13-686">[Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="acb13-686">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="acb13-687">Un sito Web multilingue consente al sito di raggiungere un pubblico più ampio.</span><span class="sxs-lookup"><span data-stu-id="acb13-687">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="acb13-688">AP.NET Core offre servizi e middleware per la localizzazione in diverse lingue e culture.</span><span class="sxs-lookup"><span data-stu-id="acb13-688">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="acb13-689">L'internazionalizzazione implica la [globalizzazione](/dotnet/api/system.globalization) e la [localizzazione](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="acb13-689">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="acb13-690">La globalizzazione è il processo di progettazione di app che supportano impostazioni cultura diverse.</span><span class="sxs-lookup"><span data-stu-id="acb13-690">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="acb13-691">La globalizzazione aggiunge supporto per l'input, la visualizzazione e l'output di una specifica serie di script della lingua legati a determinate aree geografiche.</span><span class="sxs-lookup"><span data-stu-id="acb13-691">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="acb13-692">La localizzazione è il processo di adattamento di un'app globalizzata, già elaborata per la localizzazione, a particolari impostazioni cultura e impostazioni locali.</span><span class="sxs-lookup"><span data-stu-id="acb13-692">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="acb13-693">Per altre informazioni, vedere **Termini relativi alla globalizzazione e alla localizzazione** nella parte finale di questo documento.</span><span class="sxs-lookup"><span data-stu-id="acb13-693">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="acb13-694">La localizzazione dell'app comporta quanto segue:</span><span class="sxs-lookup"><span data-stu-id="acb13-694">App localization involves the following:</span></span>

1. <span data-ttu-id="acb13-695">Rendere localizzabile il contenuto dell'app</span><span class="sxs-lookup"><span data-stu-id="acb13-695">Make the app's content localizable</span></span>
1. <span data-ttu-id="acb13-696">Fornire le risorse localizzate per le lingue e le impostazioni cultura supportate</span><span class="sxs-lookup"><span data-stu-id="acb13-696">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="acb13-697">Implementare una strategia per la selezione della lingua o delle impostazioni cultura per ogni richiesta</span><span class="sxs-lookup"><span data-stu-id="acb13-697">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="acb13-698">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="acb13-698">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="acb13-699">Rendere localizzabile il contenuto dell'app</span><span class="sxs-lookup"><span data-stu-id="acb13-699">Make the app's content localizable</span></span>

<span data-ttu-id="acb13-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer ' non richiede l'archiviazione delle stringhe di lingua predefinite in un file di risorse.</span><span class="sxs-lookup"><span data-stu-id="acb13-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="acb13-701">È possibile sviluppare un'app per la localizzazione senza creare file di risorse nelle prime fasi di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="acb13-701">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="acb13-702">Il codice seguente illustra come eseguire il wrapping della stringa "About Title" per la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-702">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="acb13-703">Nel codice precedente, l' `IStringLocalizer<T>` implementazione deriva dall' [inserimento delle dipendenze](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="acb13-703">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="acb13-704">Se non viene trovato il valore localizzato di "About Title", viene restituita la chiave dell'indicizzatore, ovvero la stringa "About Title".</span><span class="sxs-lookup"><span data-stu-id="acb13-704">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="acb13-705">È possibile lasciare le stringhe letterali della lingua predefinita nell'app ed eseguirne il wrapping nel localizzatore per potersi concentrare sullo sviluppo dell'app.</span><span class="sxs-lookup"><span data-stu-id="acb13-705">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="acb13-706">Sviluppare l'app con la lingua predefinita e prepararla per la fase di localizzazione senza prima creare un file di risorse predefinito.</span><span class="sxs-lookup"><span data-stu-id="acb13-706">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="acb13-707">In alternativa, è possibile usare l'approccio tradizionale e fornire una chiave per recuperare la stringa della lingua predefinita.</span><span class="sxs-lookup"><span data-stu-id="acb13-707">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="acb13-708">Per molti sviluppatori il nuovo flusso di lavoro che prevede di non avere un file con estensione *resx* della lingua predefinita e di eseguire semplicemente il wrapping dei valori letterali di stringa consente di ridurre il sovraccarico della localizzazione di un'app.</span><span class="sxs-lookup"><span data-stu-id="acb13-708">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="acb13-709">Altri sviluppatori preferiscono il flusso di lavoro tradizionale poiché semplifica l'uso di valori letterali di stringa più lunghi e l'aggiornamento delle stringhe localizzate.</span><span class="sxs-lookup"><span data-stu-id="acb13-709">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="acb13-710">Usare l'implementazione `IHtmlLocalizer<T>` per le risorse che contengono HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-710">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="acb13-711">L'HTML di `IHtmlLocalizer` codifica gli argomenti formattati nella stringa di risorsa, ma non codifica in HTML la stringa di risorsa.</span><span class="sxs-lookup"><span data-stu-id="acb13-711">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="acb13-712">Nell'esempio evidenziato di seguito, solo il valore del parametro `name` è codificato in HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-712">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="acb13-713">**Nota:** in genere si vuole localizzare solo testo e non il codice HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-713">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="acb13-714">Al livello più basso, è possibile ottenere `IStringLocalizerFactory` dall'[inserimento delle dipendenze](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="acb13-714">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="acb13-715">Il codice precedente illustra ognuno dei due metodi di creazione della factory.</span><span class="sxs-lookup"><span data-stu-id="acb13-715">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="acb13-716">È possibile suddividere le stringhe localizzate per controller o area oppure usare un unico contenitore.</span><span class="sxs-lookup"><span data-stu-id="acb13-716">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="acb13-717">Nell'app di esempio, viene usata una classe fittizia denominata `SharedResource` per le risorse condivise.</span><span class="sxs-lookup"><span data-stu-id="acb13-717">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="acb13-718">Alcuni sviluppatori usano la classe `Startup` per contenere le stringhe globali o condivise.</span><span class="sxs-lookup"><span data-stu-id="acb13-718">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="acb13-719">Nell'esempio seguente vengono usati i localizzatori `InfoController` e `SharedResource`:</span><span class="sxs-lookup"><span data-stu-id="acb13-719">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="acb13-720">Localizzazione delle visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="acb13-720">View localization</span></span>

<span data-ttu-id="acb13-721">Il servizio `IViewLocalizer` fornisce le stringhe localizzate per una [visualizzazione](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="acb13-721">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="acb13-722">La classe `ViewLocalizer` implementa questa interfaccia e individua la posizione della risorsa dal percorso del file della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-722">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="acb13-723">Il codice seguente illustra come usare l'implementazione predefinita di `IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="acb13-723">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="acb13-724">L'implementazione predefinita di `IViewLocalizer` individua il file di risorse in base al nome file della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-724">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="acb13-725">Non è disponibile alcuna opzione per l'uso di un file di risorse condivise globali.</span><span class="sxs-lookup"><span data-stu-id="acb13-725">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="acb13-726">`ViewLocalizer`implementa il localizzatore usando `IHtmlLocalizer` , quindi Razor non codifica la stringa localizzata in HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-726">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="acb13-727">È possibile parametrizzare le stringhe di risorsa e `IViewLocalizer` codificherà in HTML i parametri ma non la stringa di risorsa.</span><span class="sxs-lookup"><span data-stu-id="acb13-727">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="acb13-728">Si consideri il Razor markup seguente:</span><span class="sxs-lookup"><span data-stu-id="acb13-728">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="acb13-729">Un file di risorse francese può contenere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="acb13-729">A French resource file could contain the following:</span></span>

| <span data-ttu-id="acb13-730">Chiave</span><span class="sxs-lookup"><span data-stu-id="acb13-730">Key</span></span> | <span data-ttu-id="acb13-731">valore</span><span class="sxs-lookup"><span data-stu-id="acb13-731">Value</span></span> |
| ----- | ---
<span data-ttu-id="acb13-732">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-732">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-733">'Blazor'</span></span>
- <span data-ttu-id="acb13-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-734">'Identity'</span></span>
- <span data-ttu-id="acb13-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-736">'Razor'</span></span>
- <span data-ttu-id="acb13-737">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-737">'SignalR' uid:</span></span> 

<span data-ttu-id="acb13-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="acb13-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="acb13-739">La visualizzazione di cui è stato eseguito il rendering conterrà il markup HTML del file di risorse.</span><span class="sxs-lookup"><span data-stu-id="acb13-739">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="acb13-740">**Nota:** in genere si vuole localizzare solo testo e non il codice HTML.</span><span class="sxs-lookup"><span data-stu-id="acb13-740">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="acb13-741">Per usare un file di risorse condivise in una visualizzazione, inserire `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="acb13-741">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="acb13-742">Localizzazione di DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="acb13-742">DataAnnotations localization</span></span>

<span data-ttu-id="acb13-743">I messaggi di errore DataAnnotations vengono localizzati con `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="acb13-743">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="acb13-744">Usando l'opzione `ResourcesPath = "Resources"` è possibile memorizzare i messaggi di errore in `RegisterViewModel` in uno dei percorsi seguenti:</span><span class="sxs-lookup"><span data-stu-id="acb13-744">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="acb13-745">*Risorse/ViewModels. account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="acb13-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="acb13-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acb13-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="acb13-747">In ASP.NET Core MVC 1.1.0 e versioni successive, gli attributi non di convalida vengono localizzati.</span><span class="sxs-lookup"><span data-stu-id="acb13-747">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="acb13-748">ASP.NET Core MVC 1.0 **non** ricerca le stringhe localizzate per gli attributi non di convalida.</span><span class="sxs-lookup"><span data-stu-id="acb13-748">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="acb13-749">Uso di un'unica stringa di risorsa per più classi</span><span class="sxs-lookup"><span data-stu-id="acb13-749">Using one resource string for multiple classes</span></span>

<span data-ttu-id="acb13-750">Il codice seguente illustra come usare una sola stringa di risorsa per gli attributi di convalida con più classi:</span><span class="sxs-lookup"><span data-stu-id="acb13-750">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="acb13-751">Nel codice precedente `SharedResource` è la classe corrispondente al file con estensione resx in cui sono memorizzati i messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="acb13-751">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="acb13-752">Con questo approccio, DataAnnotations userà solo `SharedResource` anziché la risorsa per ogni classe.</span><span class="sxs-lookup"><span data-stu-id="acb13-752">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="acb13-753">Fornire le risorse localizzate per le lingue e le impostazioni cultura supportate</span><span class="sxs-lookup"><span data-stu-id="acb13-753">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="acb13-754">SupportedCultures e SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="acb13-754">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="acb13-755">ASP.NET Core consente di specificare due valori di impostazioni cultura, `SupportedCultures` e `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="acb13-755">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="acb13-756">L'oggetto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) per `SupportedCultures` determina i risultati delle funzioni dipendenti dalle impostazioni cultura, ad esempio date, ore, numeri e formattazione delle valute.</span><span class="sxs-lookup"><span data-stu-id="acb13-756">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="acb13-757">`SupportedCultures` determina anche l'ordinamento del testo, le convenzioni di maiuscole e minuscole e i confronti di stringhe.</span><span class="sxs-lookup"><span data-stu-id="acb13-757">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="acb13-758">Per altre informazioni su come il server ottiene le impostazioni cultura, vedere [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="acb13-758">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="acb13-759">`SupportedUICultures`Determina quali stringhe tradotte (dai file *resx* ) vengono cercate da [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="acb13-759">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="acb13-760">`ResourceManager` esegue la ricerca nelle stringhe specifiche delle impostazioni cultura determinate da `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-760">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="acb13-761">Ogni thread in .NET include oggetti `CurrentCulture` e `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-761">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="acb13-762">ASP.NET Core controlla questi valori quando viene eseguito il rendering delle funzioni dipendenti dalle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="acb13-762">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="acb13-763">Ad esempio, se le impostazioni cultura del thread corrente sono impostate su "en-US" (inglese, Stati Uniti), `DateTime.Now.ToLongDateString()` visualizza "Thursday, February 18, 2016". Se invece `CurrentCulture` è impostato su "es-ES" (spagnolo, Spagna) l'output sarà "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="acb13-763">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="acb13-764">File di risorse</span><span class="sxs-lookup"><span data-stu-id="acb13-764">Resource files</span></span>

<span data-ttu-id="acb13-765">Un file di risorse è un meccanismo utile per la separazione delle stringhe localizzabili dal codice.</span><span class="sxs-lookup"><span data-stu-id="acb13-765">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="acb13-766">Le stringhe tradotte per la lingua non predefinita sono isolate nei file di risorse *resx* .</span><span class="sxs-lookup"><span data-stu-id="acb13-766">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="acb13-767">È possibile ad esempio che si voglia creare un file di risorse spagnolo denominato *Welcome.es.resx* contenente le stringhe tradotte.</span><span class="sxs-lookup"><span data-stu-id="acb13-767">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="acb13-768">"es" è il codice di lingua per lo spagnolo.</span><span class="sxs-lookup"><span data-stu-id="acb13-768">"es" is the language code for Spanish.</span></span> <span data-ttu-id="acb13-769">Per creare questo file di risorse in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="acb13-769">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="acb13-770">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella che conterrà il file di risorse > **Aggiungi** > **Nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="acb13-770">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menu contestuale annidato: in Esplora soluzioni viene visualizzato un menu contestuale per le risorse.](localization/_static/newi.png)

2. <span data-ttu-id="acb13-773">Nella casella **Cerca modelli installati** immettere "risorse" e assegnare un nome al file.</span><span class="sxs-lookup"><span data-stu-id="acb13-773">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Finestra di dialogo Aggiungi nuovo elemento](localization/_static/res.png)

3. <span data-ttu-id="acb13-775">Immettere il valore della chiave (stringa nativa) nella colonna **Nome** e la stringa tradotta nella colonna **Valore**.</span><span class="sxs-lookup"><span data-stu-id="acb13-775">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![File Welcome.es.resx (file di risorse Welcome per lo spagnolo) con la parola Hello nella colonna Nome e la parola Hola nella colonna Valore](localization/_static/hola.png)

    <span data-ttu-id="acb13-777">Visual Studio visualizza il file *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-777">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Esplora soluzioni con il file di risorse Welcome per lo spagnolo (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="acb13-779">Denominazione dei file di risorse</span><span class="sxs-lookup"><span data-stu-id="acb13-779">Resource file naming</span></span>

<span data-ttu-id="acb13-780">Le risorse sono denominate con il nome completo del tipo della relativa classe meno il nome dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="acb13-780">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="acb13-781">Ad esempio, una risorsa francese di un progetto il cui assembly principale è `LocalizationWebsite.Web.dll` per la classe `LocalizationWebsite.Web.Startup` viene denominata *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-781">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="acb13-782">Una risorsa per la classe `LocalizationWebsite.Web.Controllers.HomeController` viene denominata *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-782">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acb13-783">Se lo spazio dei nomi della classe di destinazione non corrisponde al nome dell'assembly è necessario specificare il nome completo del tipo.</span><span class="sxs-lookup"><span data-stu-id="acb13-783">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="acb13-784">Ad esempio, nel progetto di esempio una risorsa per il tipo `ExtraNamespace.Tools` verrebbe denominata *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-784">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="acb13-785">Poiché nel progetto di esempio il metodo `ConfigureServices` imposta `ResourcesPath` su "Resources", il percorso relativo del progetto per il file di risorse francese del controller principale è *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-785">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acb13-786">In alternativa, è possibile usare le cartelle per organizzare i file di risorse.</span><span class="sxs-lookup"><span data-stu-id="acb13-786">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="acb13-787">Per il controller principale, il percorso sarà *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-787">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="acb13-788">Se non si usa l'opzione `ResourcesPath`, il file con estensione *resx* viene memorizzato nella directory di base del progetto.</span><span class="sxs-lookup"><span data-stu-id="acb13-788">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="acb13-789">Il file di risorse per `HomeController` sarà denominato *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-789">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acb13-790">La scelta di usare la convenzione di denominazione con il punto o il percorso dipende da come si vuole organizzare i file di risorse.</span><span class="sxs-lookup"><span data-stu-id="acb13-790">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="acb13-791">Nome risorsa</span><span class="sxs-lookup"><span data-stu-id="acb13-791">Resource name</span></span> | <span data-ttu-id="acb13-792">Denominazione con il punto o il percorso</span><span class="sxs-lookup"><span data-stu-id="acb13-792">Dot or path naming</span></span> |
| ---
<span data-ttu-id="acb13-793">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-793">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-794">'Blazor'</span></span>
- <span data-ttu-id="acb13-795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-795">'Identity'</span></span>
- <span data-ttu-id="acb13-796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-796">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-797">'Razor'</span></span>
- <span data-ttu-id="acb13-798">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-799">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-799">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-800">'Blazor'</span></span>
- <span data-ttu-id="acb13-801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-801">'Identity'</span></span>
- <span data-ttu-id="acb13-802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-802">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-803">'Razor'</span></span>
- <span data-ttu-id="acb13-804">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-805">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-805">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-806">'Blazor'</span></span>
- <span data-ttu-id="acb13-807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-807">'Identity'</span></span>
- <span data-ttu-id="acb13-808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-808">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-809">'Razor'</span></span>
- <span data-ttu-id="acb13-810">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-811">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-811">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-812">'Blazor'</span></span>
- <span data-ttu-id="acb13-813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-813">'Identity'</span></span>
- <span data-ttu-id="acb13-814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-814">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-815">'Razor'</span></span>
- <span data-ttu-id="acb13-816">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-816">'SignalR' uid:</span></span> 

<span data-ttu-id="acb13-817">------   | Titolo---: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-818">'Blazor'</span></span>
- <span data-ttu-id="acb13-819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-819">'Identity'</span></span>
- <span data-ttu-id="acb13-820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-820">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-821">'Razor'</span></span>
- <span data-ttu-id="acb13-822">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-823">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-823">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-824">'Blazor'</span></span>
- <span data-ttu-id="acb13-825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-825">'Identity'</span></span>
- <span data-ttu-id="acb13-826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-826">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-827">'Razor'</span></span>
- <span data-ttu-id="acb13-828">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-829">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-829">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-830">'Blazor'</span></span>
- <span data-ttu-id="acb13-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-831">'Identity'</span></span>
- <span data-ttu-id="acb13-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-833">'Razor'</span></span>
- <span data-ttu-id="acb13-834">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acb13-835">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="acb13-835">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acb13-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acb13-836">'Blazor'</span></span>
- <span data-ttu-id="acb13-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acb13-837">'Identity'</span></span>
- <span data-ttu-id="acb13-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acb13-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="acb13-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acb13-839">'Razor'</span></span>
- <span data-ttu-id="acb13-840">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="acb13-840">'SignalR' uid:</span></span> 

<span data-ttu-id="acb13-841">------- | | Risorse/Controllers. HomeController. fr. resx | Punto | | Risorse/controller/HomeController. fr. resx | Percorso | |    |     |</span><span class="sxs-lookup"><span data-stu-id="acb13-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="acb13-842">I file di risorse che usano `@inject IViewLocalizer` nelle Razor viste seguono un modello simile.</span><span class="sxs-lookup"><span data-stu-id="acb13-842">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="acb13-843">Il file di risorse per una visualizzazione può essere denominato usando la denominazione con il punto o con il percorso.</span><span class="sxs-lookup"><span data-stu-id="acb13-843">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="acb13-844">Visualizza file di risorse riproduce il percorso del file di visualizzazione associato.</span><span class="sxs-lookup"><span data-stu-id="acb13-844"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="acb13-845">Se `ResourcesPath` viene impostato su "Resources", il file di risorse francese associato alla visualizzazione *Views/Home/About.cshtml* sarà uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="acb13-845">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="acb13-846">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="acb13-846">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="acb13-847">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="acb13-847">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="acb13-848">Se non si usa l'opzione `ResourcesPath`, il file con estensione *resx* per una visualizzazione viene inserito nella stessa cartella della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-848">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="acb13-849">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="acb13-849">RootNamespaceAttribute</span></span> 

<span data-ttu-id="acb13-850">L'attributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) specifica lo spazio dei nomi radice di un assembly quando tale spazio dei nomi è diverso dal nome dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="acb13-850">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="acb13-851">Questo problema può verificarsi quando il nome di un progetto non è un identificatore .NET valido.</span><span class="sxs-lookup"><span data-stu-id="acb13-851">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="acb13-852">Ad esempio `my-project-name.csproj` , utilizzerà lo spazio dei nomi radice `my_project_name` e il nome dell'assembly `my-project-name` che genera questo errore.</span><span class="sxs-lookup"><span data-stu-id="acb13-852">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="acb13-853">Se lo spazio dei nomi radice di un assembly è diverso dal nome dell'assembly:</span><span class="sxs-lookup"><span data-stu-id="acb13-853">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="acb13-854">Per impostazione predefinita, la localizzazione non funziona.</span><span class="sxs-lookup"><span data-stu-id="acb13-854">Localization does not work by default.</span></span>
* <span data-ttu-id="acb13-855">Le risorse vengono cercate all'interno dell'assembly in un modo che impedisce la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-855">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="acb13-856">`RootNamespace` è un valore necessario in fase di compilazione che non è disponibile per il processo in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="acb13-856">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="acb13-857">Se l'attributo `RootNamespace` è diverso da `AssemblyName`, includere quanto segue in *AssemblyInfo.cs*, sostituendo i valori dei parametri con quelli effettivi:</span><span class="sxs-lookup"><span data-stu-id="acb13-857">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="acb13-858">Il codice precedente consente di risolvere correttamente i file resx.</span><span class="sxs-lookup"><span data-stu-id="acb13-858">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="acb13-859">Comportamento di fallback delle impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="acb13-859">Culture fallback behavior</span></span>

<span data-ttu-id="acb13-860">Durante la ricerca di una risorsa, la localizzazione esegue un "fallback delle impostazioni cultura".</span><span class="sxs-lookup"><span data-stu-id="acb13-860">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="acb13-861">A partire dalle impostazioni cultura richieste, se queste non vengono trovate, il codice adotta le impostazioni cultura padre di quelle richieste.</span><span class="sxs-lookup"><span data-stu-id="acb13-861">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="acb13-862">Tenere presente che la proprietà [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) rappresenta le impostazioni cultura padre.</span><span class="sxs-lookup"><span data-stu-id="acb13-862">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="acb13-863">Ciò comporta in genere (ma non sempre) la rimozione della notazione nazionale dal codice ISO.</span><span class="sxs-lookup"><span data-stu-id="acb13-863">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="acb13-864">Ad esempio la versione della lingua spagnola parlata in Messico è "es-MX".</span><span class="sxs-lookup"><span data-stu-id="acb13-864">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="acb13-865">L'elemento padre è "es", ovvero spagnolo non specifico per nessun paese.</span><span class="sxs-lookup"><span data-stu-id="acb13-865">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="acb13-866">Si supponga che il sito riceva una richiesta di una risorsa "Welcome" che usa le impostazioni cultura "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="acb13-866">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="acb13-867">Il sistema di localizzazione cerca le risorse seguenti nell'ordine elencato e seleziona la prima corrispondenza:</span><span class="sxs-lookup"><span data-stu-id="acb13-867">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="acb13-868">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="acb13-868">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="acb13-869">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acb13-869">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="acb13-870">*Welcome.resx* (se `NeutralResourcesLanguage` è "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="acb13-870">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="acb13-871">Ad esempio, se si rimuove l'indicatore delle impostazioni cultura ".fr" e le impostazioni cultura sono impostate sul francese, viene letto il file di risorse predefinito e vengono localizzate le stringhe.</span><span class="sxs-lookup"><span data-stu-id="acb13-871">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="acb13-872">Lo strumento di gestione delle risorse imposta una risorsa predefinita o di fallback per i casi in cui nessun elemento soddisfa le impostazioni cultura richieste.</span><span class="sxs-lookup"><span data-stu-id="acb13-872">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="acb13-873">Per restituire solo la chiave in caso di mancanza di una risorsa per le impostazioni cultura richieste è necessario che non sia specificato un file di risorse predefinito.</span><span class="sxs-lookup"><span data-stu-id="acb13-873">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="acb13-874">Generare file di risorse con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="acb13-874">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="acb13-875">Se si crea un file di risorse in Visual Studio senza le impostazioni cultura nel nome del file (ad esempio, *Welcome.resx*), Visual Studio crea una classe C# con una proprietà per ogni stringa.</span><span class="sxs-lookup"><span data-stu-id="acb13-875">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="acb13-876">In genere questo non è il risultato desiderato quando si usa ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="acb13-876">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="acb13-877">Solitamente non è presente un file di risorse con estensione *resx* predefinito (un file con estensione *resx* senza nome delle impostazioni cultura).</span><span class="sxs-lookup"><span data-stu-id="acb13-877">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="acb13-878">Si consiglia di creare il file con estensione *resx* con un nome delle impostazioni cultura, ad esempio *Welcome.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="acb13-878">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="acb13-879">Quando si crea un file con estensione *resx* con un nome delle impostazioni cultura, Visual Studio non genera il file di classe.</span><span class="sxs-lookup"><span data-stu-id="acb13-879">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="acb13-880">Aggiungere altre impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="acb13-880">Add other cultures</span></span>

<span data-ttu-id="acb13-881">Ogni combinazione di lingua e impostazioni cultura (diversa dalla lingua predefinita) richiede un file di risorse univoco.</span><span class="sxs-lookup"><span data-stu-id="acb13-881">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="acb13-882">Creare file di risorse per impostazioni cultura e locali diverse creando nuovi file di risorse in cui i codici di lingua ISO sono inclusi nel nome file, ad esempio **en-us**, **fr-ca** e **en-gb**.</span><span class="sxs-lookup"><span data-stu-id="acb13-882">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="acb13-883">I codici ISO vengono inseriti tra il nome file e l'estensione *resx* come in *Welcome.es-MX.resx* (spagnolo/Messico).</span><span class="sxs-lookup"><span data-stu-id="acb13-883">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="acb13-884">Implementare una strategia per la selezione della lingua o delle impostazioni cultura per ogni richiesta</span><span class="sxs-lookup"><span data-stu-id="acb13-884">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="acb13-885">Configurare la localizzazione</span><span class="sxs-lookup"><span data-stu-id="acb13-885">Configure localization</span></span>

<span data-ttu-id="acb13-886">La localizzazione è configurata nel metodo `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="acb13-886">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="acb13-887">`AddLocalization` aggiunge i servizi di localizzazione al contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="acb13-887">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="acb13-888">Il codice precedente imposta anche il percorso delle risorse su "Resources".</span><span class="sxs-lookup"><span data-stu-id="acb13-888">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="acb13-889">`AddViewLocalization` aggiunge il supporto per i file di visualizzazione localizzati.</span><span class="sxs-lookup"><span data-stu-id="acb13-889">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="acb13-890">Nell'esempio la localizzazione delle visualizzazioni è basata sul suffisso dei file di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-890">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="acb13-891">Ad esempio "fr" nel file *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="acb13-891">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="acb13-892">`AddDataAnnotationsLocalization` aggiunge il supporto per i messaggi di convalida `DataAnnotations` localizzati tramite le astrazioni `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="acb13-892">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="acb13-893">Middleware di localizzazione</span><span class="sxs-lookup"><span data-stu-id="acb13-893">Localization middleware</span></span>

<span data-ttu-id="acb13-894">Le impostazioni cultura correnti in un richiesta sono impostate nel [middleware](xref:fundamentals/middleware/index) di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-894">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="acb13-895">Il middleware di localizzazione viene abilitato nel metodo `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="acb13-895">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="acb13-896">Il middleware di localizzazione deve essere configurato prima di qualsiasi middleware che potrebbe controllare le impostazioni cultura della richiesta (ad esempio, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="acb13-896">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="acb13-897">`UseRequestLocalization` inizializza un oggetto `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="acb13-897">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="acb13-898">In ogni richiesta l'elenco di `RequestCultureProvider` in `RequestLocalizationOptions` è enumerato e viene usato il primo provider in grado di determinare le impostazioni cultura della richiesta.</span><span class="sxs-lookup"><span data-stu-id="acb13-898">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="acb13-899">I provider predefiniti provengono dalla classe `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="acb13-899">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="acb13-900">L'elenco predefinito passa dal più specifico al meno specifico.</span><span class="sxs-lookup"><span data-stu-id="acb13-900">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="acb13-901">Di seguito in questo articolo viene descritto come modificare l'ordine e aggiungere un provider delle impostazioni cultura personalizzato.</span><span class="sxs-lookup"><span data-stu-id="acb13-901">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="acb13-902">Se nessuno dei provider è in grado di determinare le impostazioni cultura della richiesta, viene usato `DefaultRequestCulture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-902">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="acb13-903">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="acb13-903">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="acb13-904">Alcune app usano una stringa di query per specificare le [impostazioni cultura e le impostazioni cultura dell'interfaccia utente](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="acb13-904">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="acb13-905">Per le app che usano il cookie o l'approccio dell'intestazione Accept-Language, è utile aggiungere una stringa di query all'URL per eseguire il debug e il test del codice.</span><span class="sxs-lookup"><span data-stu-id="acb13-905">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="acb13-906">Per impostazione predefinita, `QueryStringRequestCultureProvider` viene registrato come primo provider di localizzazione nell'elenco `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="acb13-906">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="acb13-907">Passare i parametri della stringa di query `culture` e `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-907">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="acb13-908">L'esempio seguente specifica le impostazioni cultura specifiche (lingua e area) per spagnolo/Messico:</span><span class="sxs-lookup"><span data-stu-id="acb13-908">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="acb13-909">Se si passa uno solo dei due parametri (`culture` o `ui-culture`), il provider della stringa di query imposterà entrambi i valori usando il parametro passato.</span><span class="sxs-lookup"><span data-stu-id="acb13-909">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="acb13-910">Ad esempio, se si specificano solo le impostazioni cultura verranno impostati entrambi i parametri `Culture` e `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="acb13-910">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="acb13-911">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="acb13-911">CookieRequestCultureProvider</span></span>

<span data-ttu-id="acb13-912">Le app di produzione offrono spesso la possibilità di specificare le impostazioni cultura con il cookie delle impostazioni cultura di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="acb13-912">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="acb13-913">Usare il metodo `MakeCookieValue` per creare un cookie.</span><span class="sxs-lookup"><span data-stu-id="acb13-913">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="acb13-914">`CookieRequestCultureProvider` `DefaultCookieName` Restituisce il nome del cookie predefinito utilizzato per tenere traccia delle informazioni sulle impostazioni cultura preferite dell'utente.</span><span class="sxs-lookup"><span data-stu-id="acb13-914">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="acb13-915">Il nome del cookie predefinito è `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-915">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="acb13-916">Il formato del cookie è `c=%LANGCODE%|uic=%LANGCODE%`, dove `c` è `Culture` e `uic` è `UICulture`, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="acb13-916">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="acb13-917">Se si specificano solo le impostazioni cultura o le impostazioni cultura dell'interfaccia utente, le impostazioni specificate verranno usate per entrambi i tipi di impostazioni.</span><span class="sxs-lookup"><span data-stu-id="acb13-917">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="acb13-918">Intestazione HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="acb13-918">The Accept-Language HTTP header</span></span>

<span data-ttu-id="acb13-919">L'[intestazione Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) può essere impostata nella maggior parte dei browser ed è stata originariamente progettata per specificare la lingua dell'utente.</span><span class="sxs-lookup"><span data-stu-id="acb13-919">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="acb13-920">Questa impostazione indica la lingua impostata per l'invio da parte del browser o che il browser ha ereditato dal sistema operativo sottostante.</span><span class="sxs-lookup"><span data-stu-id="acb13-920">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="acb13-921">L'intestazione HTTP Accept-Language di una richiesta del browser non è un metodo infallibile per individuare la lingua preferita dell'utente (vedere [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php) (Impostazione delle preferenze di lingua in un browser)).</span><span class="sxs-lookup"><span data-stu-id="acb13-921">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="acb13-922">Un'app di produzione deve consentire all'utente di personalizzare le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="acb13-922">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="acb13-923">Impostare l'intestazione HTTP Accept-Language in IE</span><span class="sxs-lookup"><span data-stu-id="acb13-923">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="acb13-924">Dall'icona a forma di ingranaggio, toccare **Opzioni Internet**.</span><span class="sxs-lookup"><span data-stu-id="acb13-924">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="acb13-925">Toccare **Lingue**.</span><span class="sxs-lookup"><span data-stu-id="acb13-925">Tap **Languages**.</span></span>

    ![Opzioni Internet](localization/_static/lang.png)

3. <span data-ttu-id="acb13-927">Toccare **Imposta preferenze lingua**.</span><span class="sxs-lookup"><span data-stu-id="acb13-927">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="acb13-928">Toccare **Aggiungi una lingua**.</span><span class="sxs-lookup"><span data-stu-id="acb13-928">Tap **Add a language**.</span></span>

5. <span data-ttu-id="acb13-929">Aggiungere la lingua.</span><span class="sxs-lookup"><span data-stu-id="acb13-929">Add the language.</span></span>

6. <span data-ttu-id="acb13-930">Toccare la lingua e quindi toccare **Sposta su**.</span><span class="sxs-lookup"><span data-stu-id="acb13-930">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="acb13-931">Intestazione HTTP Content-Language</span><span class="sxs-lookup"><span data-stu-id="acb13-931">The Content-Language HTTP header</span></span>

<span data-ttu-id="acb13-932">Intestazione dell'entità [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :</span><span class="sxs-lookup"><span data-stu-id="acb13-932">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="acb13-933">Viene usato per descrivere le lingue destinate ai destinatari.</span><span class="sxs-lookup"><span data-stu-id="acb13-933">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="acb13-934">Consente a un utente di distinguere in base alla lingua preferita degli utenti.</span><span class="sxs-lookup"><span data-stu-id="acb13-934">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="acb13-935">Le intestazioni delle entità vengono usate sia nelle richieste HTTP sia nelle risposte.</span><span class="sxs-lookup"><span data-stu-id="acb13-935">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="acb13-936">L' `Content-Language` intestazione può essere aggiunta impostando la proprietà `ApplyCurrentCultureToResponseHeaders` .</span><span class="sxs-lookup"><span data-stu-id="acb13-936">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="acb13-937">Aggiunta dell' `Content-Language` intestazione:</span><span class="sxs-lookup"><span data-stu-id="acb13-937">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="acb13-938">Consente a RequestLocalizationMiddleware di impostare l' `Content-Language` intestazione con `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="acb13-938">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="acb13-939">Elimina la necessità di impostare l'intestazione della risposta in `Content-Language` modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="acb13-939">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="acb13-940">Usare un provider personalizzato</span><span class="sxs-lookup"><span data-stu-id="acb13-940">Use a custom provider</span></span>

<span data-ttu-id="acb13-941">Si supponga di voler consentire agli utenti di memorizzare lingua e impostazioni cultura nei database.</span><span class="sxs-lookup"><span data-stu-id="acb13-941">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="acb13-942">È possibile creare un provider per la ricerca di questi valori per l'utente.</span><span class="sxs-lookup"><span data-stu-id="acb13-942">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="acb13-943">Il codice seguente illustra come aggiungere un provider personalizzato:</span><span class="sxs-lookup"><span data-stu-id="acb13-943">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="acb13-944">Usare `RequestLocalizationOptions` per aggiungere o rimuovere i provider di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-944">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="acb13-945">Specificare le impostazioni cultura a livello di codice</span><span class="sxs-lookup"><span data-stu-id="acb13-945">Set the culture programmatically</span></span>

<span data-ttu-id="acb13-946">Questo progetto di esempio **Localization.StarterWeb** in [GitHub](https://github.com/aspnet/entropy) contiene l'interfaccia utente per impostare `Culture`.</span><span class="sxs-lookup"><span data-stu-id="acb13-946">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="acb13-947">Il file *Views/Shared/_SelectLanguagePartial.cshtml* consente di selezionare le impostazioni cultura dall'elenco delle impostazioni cultura supportate:</span><span class="sxs-lookup"><span data-stu-id="acb13-947">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="acb13-948">Il file *Views/Shared/_SelectLanguagePartial.cshtml* viene aggiunto alla sezione `footer` del file di layout in modo che sia disponibile in tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="acb13-948">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="acb13-949">Il metodo `SetLanguage` imposta il cookie delle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="acb13-949">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="acb13-950">Non è possibile collegare *_SelectLanguagePartial.cshtml* al codice di esempio per questo progetto.</span><span class="sxs-lookup"><span data-stu-id="acb13-950">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="acb13-951">Il progetto **Localization. StarterWeb** in [GitHub](https://github.com/aspnet/entropy) dispone di codice per il flusso `RequestLocalizationOptions` di a un oggetto Razor parziale tramite il contenitore di [inserimento delle dipendenze](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="acb13-951">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="acb13-952">Associazione di modelli dati della route e stringhe di query</span><span class="sxs-lookup"><span data-stu-id="acb13-952">Model binding route data and query strings</span></span>

<span data-ttu-id="acb13-953">Vedere [comportamento di globalizzazione delle stringhe di query e dati della route di associazione di modelli](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="acb13-953">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="acb13-954">Termini relativi alla globalizzazione e alla localizzazione</span><span class="sxs-lookup"><span data-stu-id="acb13-954">Globalization and localization terms</span></span>

<span data-ttu-id="acb13-955">Il processo di localizzazione dell'app richiede anche una conoscenza di base dei set di caratteri comunemente usati nello sviluppo del software moderno e dei problemi associati a essi.</span><span class="sxs-lookup"><span data-stu-id="acb13-955">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="acb13-956">Sebbene tutti i computer memorizzano il testo come numeri (codici), sistemi diversi memorizzano lo stesso testo usando numeri diversi.</span><span class="sxs-lookup"><span data-stu-id="acb13-956">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="acb13-957">Il processo di localizzazione si riferisce alla traduzione dell'interfaccia utente dell'app per impostazioni cultura o locali specifiche.</span><span class="sxs-lookup"><span data-stu-id="acb13-957">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="acb13-958">La [localizzabilità](/dotnet/standard/globalization-localization/localizability-review) è un processo intermedio che verifica che un'app globalizzata sia pronta per la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-958">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="acb13-959">Il formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) per il nome delle impostazioni cultura è `<languagecode2>-<country/regioncode2>`, dove `<languagecode2>` è il codice della lingua e `<country/regioncode2>` è il codice della cultura secondaria.</span><span class="sxs-lookup"><span data-stu-id="acb13-959">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="acb13-960">Ad esempio, `es-CL` per spagnolo (Cile), `en-US` per inglese (Stati Uniti) e `en-AU` per inglese (Australia).</span><span class="sxs-lookup"><span data-stu-id="acb13-960">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="acb13-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) è la combinazione di un codice di cultura in lettere minuscole di due lettere ISO 639 associato a una lingua e un codice di cultura secondaria in lettere maiuscole di due lettere ISO 3166 associato a un paese o un'area.</span><span class="sxs-lookup"><span data-stu-id="acb13-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="acb13-962">Vedere [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx) (Nome delle impostazioni cultura delle lingue).</span><span class="sxs-lookup"><span data-stu-id="acb13-962">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="acb13-963">L'internazionalizzazione è spesso abbreviata con "I18N".</span><span class="sxs-lookup"><span data-stu-id="acb13-963">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="acb13-964">L'abbreviazione include la prima e l'ultima lettera e il numero di lettere che intercorrono tra di loro nel termine inglese "Internationalization". 18 è il numero di lettere tra la prima "I" e l'ultima "N".</span><span class="sxs-lookup"><span data-stu-id="acb13-964">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="acb13-965">Lo stesso vale per globalizzazione (G11N) e localizzazione (L10N).</span><span class="sxs-lookup"><span data-stu-id="acb13-965">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="acb13-966">Termini:</span><span class="sxs-lookup"><span data-stu-id="acb13-966">Terms:</span></span>

* <span data-ttu-id="acb13-967">Globalizzazione (G11N): processo che consente a un'app di supportare lingue e aree diverse.</span><span class="sxs-lookup"><span data-stu-id="acb13-967">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="acb13-968">Localizzazione (L10N): processo di personalizzazione di un'app per una determinata lingua e area.</span><span class="sxs-lookup"><span data-stu-id="acb13-968">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="acb13-969">Internazionalizzazione (I18N): descrive la globalizzazione e la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="acb13-969">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="acb13-970">Impostazioni cultura: si tratta di una lingua e, facoltativamente, di un'area.</span><span class="sxs-lookup"><span data-stu-id="acb13-970">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="acb13-971">Impostazioni cultura neutre: cultura con una lingua specificata ma senza area.</span><span class="sxs-lookup"><span data-stu-id="acb13-971">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="acb13-972">(ad esempio "en", "es")</span><span class="sxs-lookup"><span data-stu-id="acb13-972">(for example "en", "es")</span></span>
* <span data-ttu-id="acb13-973">Impostazioni cultura specifiche: cultura con una lingua e un'area specificate.</span><span class="sxs-lookup"><span data-stu-id="acb13-973">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="acb13-974">(ad esempio "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="acb13-974">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="acb13-975">Impostazioni padre: impostazioni cultura neutre con impostazioni cultura specifiche.</span><span class="sxs-lookup"><span data-stu-id="acb13-975">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="acb13-976">(ad esempio, "en" rappresenta le impostazioni cultura padre di "en-US" e "en-GB")</span><span class="sxs-lookup"><span data-stu-id="acb13-976">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="acb13-977">Impostazioni locali: le impostazioni locali corrispondono alle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="acb13-977">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="acb13-978">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="acb13-978">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="acb13-979">[Progetto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usato nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="acb13-979">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="acb13-980">Globalizzazione e localizzazione di applicazioni .NET</span><span class="sxs-lookup"><span data-stu-id="acb13-980">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="acb13-981">Risorse nei file con estensione resx</span><span class="sxs-lookup"><span data-stu-id="acb13-981">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="acb13-982">Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="acb13-982">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="acb13-983">Localizzazione e generics</span><span class="sxs-lookup"><span data-stu-id="acb13-983">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
