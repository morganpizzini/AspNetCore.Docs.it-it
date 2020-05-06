---
title: Globalizzazione e localizzazione in ASP.NET Core
author: rick-anderson
description: Informazioni su come ASP.NET Core offre servizi e middleware per la localizzazione di contenuti in diverse lingue e culture.
ms.author: riande
ms.date: 11/30/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization
ms.openlocfilehash: 1111c0c57baf5bfd52b26b553179338100b9ba79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776565"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="7b652-103">Globalizzazione e localizzazione in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b652-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="7b652-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="7b652-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="7b652-105">La creazione di un sito Web multilingue con ASP.NET Core consente al sito di raggiungere un pubblico più ampio.</span><span class="sxs-lookup"><span data-stu-id="7b652-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="7b652-106">AP.NET Core offre servizi e middleware per la localizzazione in diverse lingue e culture.</span><span class="sxs-lookup"><span data-stu-id="7b652-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="7b652-107">L'internazionalizzazione implica la [globalizzazione](/dotnet/api/system.globalization) e la [localizzazione](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="7b652-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="7b652-108">La globalizzazione è il processo di progettazione di app che supportano impostazioni cultura diverse.</span><span class="sxs-lookup"><span data-stu-id="7b652-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="7b652-109">La globalizzazione aggiunge supporto per l'input, la visualizzazione e l'output di una specifica serie di script della lingua legati a determinate aree geografiche.</span><span class="sxs-lookup"><span data-stu-id="7b652-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="7b652-110">La localizzazione è il processo di adattamento di un'app globalizzata, già elaborata per la localizzazione, a particolari impostazioni cultura e impostazioni locali.</span><span class="sxs-lookup"><span data-stu-id="7b652-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="7b652-111">Per altre informazioni, vedere **Termini relativi alla globalizzazione e alla localizzazione** nella parte finale di questo documento.</span><span class="sxs-lookup"><span data-stu-id="7b652-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="7b652-112">La localizzazione dell'app comporta quanto segue:</span><span class="sxs-lookup"><span data-stu-id="7b652-112">App localization involves the following:</span></span>

1. <span data-ttu-id="7b652-113">Rendere localizzabile il contenuto dell'app</span><span class="sxs-lookup"><span data-stu-id="7b652-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="7b652-114">Fornire le risorse localizzate per le lingue e le impostazioni cultura supportate</span><span class="sxs-lookup"><span data-stu-id="7b652-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="7b652-115">Implementare una strategia per la selezione della lingua o delle impostazioni cultura per ogni richiesta</span><span class="sxs-lookup"><span data-stu-id="7b652-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="7b652-116">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7b652-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="7b652-117">Rendere localizzabile il contenuto dell'app</span><span class="sxs-lookup"><span data-stu-id="7b652-117">Make the app's content localizable</span></span>

<span data-ttu-id="7b652-118">Introdotte in ASP.NET Core, `IStringLocalizer` e `IStringLocalizer<T>` sono state progettate per migliorare la produttività durante lo sviluppo di app localizzate.</span><span class="sxs-lookup"><span data-stu-id="7b652-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="7b652-119">`IStringLocalizer` usa [ResourceManager](/dotnet/api/system.resources.resourcemanager) e [ResourceReader](/dotnet/api/system.resources.resourcereader) per fornire le risorse specifiche delle impostazioni cultura durante il runtime.</span><span class="sxs-lookup"><span data-stu-id="7b652-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="7b652-120">L'interfaccia semplice include un indicizzatore e `IEnumerable` per la restituzione di stringhe localizzate.</span><span class="sxs-lookup"><span data-stu-id="7b652-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="7b652-121">`IStringLocalizer` non richiede di memorizzare le stringhe della lingua predefinita in un file di risorse.</span><span class="sxs-lookup"><span data-stu-id="7b652-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="7b652-122">È possibile sviluppare un'app per la localizzazione senza creare file di risorse nelle prime fasi di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="7b652-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="7b652-123">Il codice seguente illustra come eseguire il wrapping della stringa "About Title" per la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="7b652-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="7b652-124">Nel codice l'implementazione `IStringLocalizer<T>` proviene dall'[inserimento delle dipendenze](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="7b652-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="7b652-125">Se non viene trovato il valore localizzato di "About Title", viene restituita la chiave dell'indicizzatore, ovvero la stringa "About Title".</span><span class="sxs-lookup"><span data-stu-id="7b652-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="7b652-126">È possibile lasciare le stringhe letterali della lingua predefinita nell'app ed eseguirne il wrapping nel localizzatore per potersi concentrare sullo sviluppo dell'app.</span><span class="sxs-lookup"><span data-stu-id="7b652-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="7b652-127">Sviluppare l'app con la lingua predefinita e prepararla per la fase di localizzazione senza prima creare un file di risorse predefinito.</span><span class="sxs-lookup"><span data-stu-id="7b652-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="7b652-128">In alternativa, è possibile usare l'approccio tradizionale e fornire una chiave per recuperare la stringa della lingua predefinita.</span><span class="sxs-lookup"><span data-stu-id="7b652-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="7b652-129">Per molti sviluppatori il nuovo flusso di lavoro che prevede di non avere un file con estensione *resx* della lingua predefinita e di eseguire semplicemente il wrapping dei valori letterali di stringa consente di ridurre il sovraccarico della localizzazione di un'app.</span><span class="sxs-lookup"><span data-stu-id="7b652-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="7b652-130">Altri sviluppatori preferiscono il flusso di lavoro tradizionale poiché semplifica l'uso di valori letterali di stringa più lunghi e l'aggiornamento delle stringhe localizzate.</span><span class="sxs-lookup"><span data-stu-id="7b652-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="7b652-131">Usare l'implementazione `IHtmlLocalizer<T>` per le risorse che contengono HTML.</span><span class="sxs-lookup"><span data-stu-id="7b652-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="7b652-132">L'HTML di `IHtmlLocalizer` codifica gli argomenti formattati nella stringa di risorsa, ma non codifica in HTML la stringa di risorsa.</span><span class="sxs-lookup"><span data-stu-id="7b652-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="7b652-133">Nell'esempio evidenziato di seguito, solo il valore del parametro `name` è codificato in HTML.</span><span class="sxs-lookup"><span data-stu-id="7b652-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="7b652-134">**Nota:** in genere si vuole localizzare solo testo e non il codice HTML.</span><span class="sxs-lookup"><span data-stu-id="7b652-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="7b652-135">Al livello più basso, è possibile ottenere `IStringLocalizerFactory` dall'[inserimento delle dipendenze](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="7b652-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="7b652-136">Il codice precedente illustra ognuno dei due metodi di creazione della factory.</span><span class="sxs-lookup"><span data-stu-id="7b652-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="7b652-137">È possibile suddividere le stringhe localizzate per controller o area oppure usare un unico contenitore.</span><span class="sxs-lookup"><span data-stu-id="7b652-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="7b652-138">Nell'app di esempio, viene usata una classe fittizia denominata `SharedResource` per le risorse condivise.</span><span class="sxs-lookup"><span data-stu-id="7b652-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="7b652-139">Alcuni sviluppatori usano la classe `Startup` per contenere le stringhe globali o condivise.</span><span class="sxs-lookup"><span data-stu-id="7b652-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="7b652-140">Nell'esempio seguente vengono usati i localizzatori `InfoController` e `SharedResource`:</span><span class="sxs-lookup"><span data-stu-id="7b652-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="7b652-141">Localizzazione delle visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="7b652-141">View localization</span></span>

<span data-ttu-id="7b652-142">Il servizio `IViewLocalizer` fornisce le stringhe localizzate per una [visualizzazione](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="7b652-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="7b652-143">La classe `ViewLocalizer` implementa questa interfaccia e individua la posizione della risorsa dal percorso del file della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="7b652-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="7b652-144">Il codice seguente illustra come usare l'implementazione predefinita di `IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="7b652-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="7b652-145">L'implementazione predefinita di `IViewLocalizer` individua il file di risorse in base al nome file della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="7b652-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="7b652-146">Non è disponibile alcuna opzione per l'uso di un file di risorse condivise globali.</span><span class="sxs-lookup"><span data-stu-id="7b652-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="7b652-147">Poiché `ViewLocalizer` implementa il localizzatore usando `IHtmlLocalizer`, Razor non codifica in HTML la stringa localizzata.</span><span class="sxs-lookup"><span data-stu-id="7b652-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="7b652-148">È possibile parametrizzare le stringhe di risorsa e `IViewLocalizer` codificherà in HTML i parametri ma non la stringa di risorsa.</span><span class="sxs-lookup"><span data-stu-id="7b652-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="7b652-149">Si consideri il markup Razor seguente:</span><span class="sxs-lookup"><span data-stu-id="7b652-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="7b652-150">Un file di risorse francese può contenere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="7b652-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="7b652-151">Chiave</span><span class="sxs-lookup"><span data-stu-id="7b652-151">Key</span></span> | <span data-ttu-id="7b652-152">Valore</span><span class="sxs-lookup"><span data-stu-id="7b652-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="7b652-153">La visualizzazione di cui è stato eseguito il rendering conterrà il markup HTML del file di risorse.</span><span class="sxs-lookup"><span data-stu-id="7b652-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="7b652-154">**Nota:** in genere si vuole localizzare solo testo e non il codice HTML.</span><span class="sxs-lookup"><span data-stu-id="7b652-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="7b652-155">Per usare un file di risorse condivise in una visualizzazione, inserire `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="7b652-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="7b652-156">Localizzazione di DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="7b652-156">DataAnnotations localization</span></span>

<span data-ttu-id="7b652-157">I messaggi di errore DataAnnotations vengono localizzati con `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="7b652-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="7b652-158">Usando l'opzione `ResourcesPath = "Resources"` è possibile memorizzare i messaggi di errore in `RegisterViewModel` in uno dei percorsi seguenti:</span><span class="sxs-lookup"><span data-stu-id="7b652-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="7b652-159">*Risorse/ViewModels. account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="7b652-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="7b652-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="7b652-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="7b652-161">In ASP.NET Core MVC 1.1.0 e versioni successive, gli attributi non di convalida vengono localizzati.</span><span class="sxs-lookup"><span data-stu-id="7b652-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="7b652-162">ASP.NET Core MVC 1.0 **non** ricerca le stringhe localizzate per gli attributi non di convalida.</span><span class="sxs-lookup"><span data-stu-id="7b652-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="7b652-163">Uso di un'unica stringa di risorsa per più classi</span><span class="sxs-lookup"><span data-stu-id="7b652-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="7b652-164">Il codice seguente illustra come usare una sola stringa di risorsa per gli attributi di convalida con più classi:</span><span class="sxs-lookup"><span data-stu-id="7b652-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="7b652-165">Nel codice precedente `SharedResource` è la classe corrispondente al file con estensione resx in cui sono memorizzati i messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="7b652-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="7b652-166">Con questo approccio, DataAnnotations userà solo `SharedResource` anziché la risorsa per ogni classe.</span><span class="sxs-lookup"><span data-stu-id="7b652-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="7b652-167">Fornire le risorse localizzate per le lingue e le impostazioni cultura supportate</span><span class="sxs-lookup"><span data-stu-id="7b652-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="7b652-168">SupportedCultures e SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="7b652-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="7b652-169">ASP.NET Core consente di specificare due valori di impostazioni cultura, `SupportedCultures` e `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="7b652-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="7b652-170">L'oggetto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) per `SupportedCultures` determina i risultati delle funzioni dipendenti dalle impostazioni cultura, ad esempio date, ore, numeri e formattazione delle valute.</span><span class="sxs-lookup"><span data-stu-id="7b652-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="7b652-171">`SupportedCultures` determina anche l'ordinamento del testo, le convenzioni di maiuscole e minuscole e i confronti di stringhe.</span><span class="sxs-lookup"><span data-stu-id="7b652-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="7b652-172">Per altre informazioni su come il server ottiene le impostazioni cultura, vedere [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="7b652-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="7b652-173">Determina quali stringhe tradotte (dai file *resx* ) vengono cercate da [ResourceManager.](/dotnet/api/system.resources.resourcemanager) `SupportedUICultures`</span><span class="sxs-lookup"><span data-stu-id="7b652-173">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="7b652-174">`ResourceManager` esegue la ricerca nelle stringhe specifiche delle impostazioni cultura determinate da `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="7b652-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="7b652-175">Ogni thread in .NET include oggetti `CurrentCulture` e `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="7b652-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="7b652-176">ASP.NET Core controlla questi valori quando viene eseguito il rendering delle funzioni dipendenti dalle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="7b652-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="7b652-177">Ad esempio, se le impostazioni cultura del thread corrente sono impostate su "en-US" (inglese, Stati Uniti), `DateTime.Now.ToLongDateString()` visualizza "Thursday, February 18, 2016". Se invece `CurrentCulture` è impostato su "es-ES" (spagnolo, Spagna) l'output sarà "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="7b652-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="7b652-178">File di risorse</span><span class="sxs-lookup"><span data-stu-id="7b652-178">Resource files</span></span>

<span data-ttu-id="7b652-179">Un file di risorse è un meccanismo utile per la separazione delle stringhe localizzabili dal codice.</span><span class="sxs-lookup"><span data-stu-id="7b652-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="7b652-180">Le stringhe tradotte per la lingua non predefinita sono isolate nei file di risorse *resx* .</span><span class="sxs-lookup"><span data-stu-id="7b652-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="7b652-181">È possibile ad esempio che si voglia creare un file di risorse spagnolo denominato *Welcome.es.resx* contenente le stringhe tradotte.</span><span class="sxs-lookup"><span data-stu-id="7b652-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="7b652-182">"es" è il codice di lingua per lo spagnolo.</span><span class="sxs-lookup"><span data-stu-id="7b652-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="7b652-183">Per creare questo file di risorse in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="7b652-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="7b652-184">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella che conterrà il file di risorse > **Aggiungi** > **Nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="7b652-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menu contestuale annidato: in Esplora soluzioni viene visualizzato un menu contestuale per le risorse.](localization/_static/newi.png)

2. <span data-ttu-id="7b652-187">Nella casella **Cerca modelli installati** immettere "risorse" e assegnare un nome al file.</span><span class="sxs-lookup"><span data-stu-id="7b652-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Finestra di dialogo Aggiungi nuovo elemento](localization/_static/res.png)

3. <span data-ttu-id="7b652-189">Immettere il valore della chiave (stringa nativa) nella colonna **Nome** e la stringa tradotta nella colonna **Valore**.</span><span class="sxs-lookup"><span data-stu-id="7b652-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![File Welcome.es.resx (file di risorse Welcome per lo spagnolo) con la parola Hello nella colonna Nome e la parola Hola nella colonna Valore](localization/_static/hola.png)

    <span data-ttu-id="7b652-191">Visual Studio visualizza il file *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="7b652-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Esplora soluzioni con il file di risorse Welcome per lo spagnolo (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="7b652-193">Denominazione dei file di risorse</span><span class="sxs-lookup"><span data-stu-id="7b652-193">Resource file naming</span></span>

<span data-ttu-id="7b652-194">Le risorse sono denominate con il nome completo del tipo della relativa classe meno il nome dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="7b652-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="7b652-195">Ad esempio, una risorsa francese di un progetto il cui assembly principale è `LocalizationWebsite.Web.dll` per la classe `LocalizationWebsite.Web.Startup` viene denominata *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="7b652-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="7b652-196">Una risorsa per la classe `LocalizationWebsite.Web.Controllers.HomeController` viene denominata *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="7b652-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="7b652-197">Se lo spazio dei nomi della classe di destinazione non corrisponde al nome dell'assembly è necessario specificare il nome completo del tipo.</span><span class="sxs-lookup"><span data-stu-id="7b652-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="7b652-198">Ad esempio, nel progetto di esempio una risorsa per il tipo `ExtraNamespace.Tools` verrebbe denominata *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="7b652-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="7b652-199">Poiché nel progetto di esempio il metodo `ConfigureServices` imposta `ResourcesPath` su "Resources", il percorso relativo del progetto per il file di risorse francese del controller principale è *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="7b652-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="7b652-200">In alternativa, è possibile usare le cartelle per organizzare i file di risorse.</span><span class="sxs-lookup"><span data-stu-id="7b652-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="7b652-201">Per il controller principale, il percorso sarà *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="7b652-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="7b652-202">Se non si usa l'opzione `ResourcesPath`, il file con estensione *resx* viene memorizzato nella directory di base del progetto.</span><span class="sxs-lookup"><span data-stu-id="7b652-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="7b652-203">Il file di risorse per `HomeController` sarà denominato *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="7b652-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="7b652-204">La scelta di usare la convenzione di denominazione con il punto o il percorso dipende da come si vuole organizzare i file di risorse.</span><span class="sxs-lookup"><span data-stu-id="7b652-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="7b652-205">Nome risorsa</span><span class="sxs-lookup"><span data-stu-id="7b652-205">Resource name</span></span> | <span data-ttu-id="7b652-206">Denominazione con il punto o il percorso</span><span class="sxs-lookup"><span data-stu-id="7b652-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="7b652-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="7b652-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="7b652-208">Punto</span><span class="sxs-lookup"><span data-stu-id="7b652-208">Dot</span></span>  |
| <span data-ttu-id="7b652-209">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="7b652-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="7b652-210">Path</span><span class="sxs-lookup"><span data-stu-id="7b652-210">Path</span></span> |
|    |     |

<span data-ttu-id="7b652-211">I file di `@inject IViewLocalizer` risorse Razor che usano nelle viste seguono un modello simile.</span><span class="sxs-lookup"><span data-stu-id="7b652-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="7b652-212">Il file di risorse per una visualizzazione può essere denominato usando la denominazione con il punto o con il percorso.</span><span class="sxs-lookup"><span data-stu-id="7b652-212">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="7b652-213">Visualizza file di risorse riproduce il percorso del file di visualizzazione associato.</span><span class="sxs-lookup"><span data-stu-id="7b652-213"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="7b652-214">Se `ResourcesPath` viene impostato su "Resources", il file di risorse francese associato alla visualizzazione *Views/Home/About.cshtml* sarà uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="7b652-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="7b652-215">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="7b652-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="7b652-216">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="7b652-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="7b652-217">Se non si usa l'opzione `ResourcesPath`, il file con estensione *resx* per una visualizzazione viene inserito nella stessa cartella della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="7b652-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="7b652-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="7b652-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="7b652-219">L'attributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) specifica lo spazio dei nomi radice di un assembly quando tale spazio dei nomi è diverso dal nome dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="7b652-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="7b652-220">Questo problema può verificarsi quando il nome di un progetto non è un identificatore .NET valido.</span><span class="sxs-lookup"><span data-stu-id="7b652-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="7b652-221">Ad esempio `my-project-name.csproj` , utilizzerà lo spazio `my_project_name` dei nomi radice e `my-project-name` il nome dell'assembly che genera questo errore.</span><span class="sxs-lookup"><span data-stu-id="7b652-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="7b652-222">Se lo spazio dei nomi radice di un assembly è diverso dal nome dell'assembly:</span><span class="sxs-lookup"><span data-stu-id="7b652-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="7b652-223">Per impostazione predefinita, la localizzazione non funziona.</span><span class="sxs-lookup"><span data-stu-id="7b652-223">Localization does not work by default.</span></span>
* <span data-ttu-id="7b652-224">Le risorse vengono cercate all'interno dell'assembly in un modo che impedisce la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="7b652-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="7b652-225">`RootNamespace` è un valore necessario in fase di compilazione che non è disponibile per il processo in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="7b652-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="7b652-226">Se l'attributo `RootNamespace` è diverso da `AssemblyName`, includere quanto segue in *AssemblyInfo.cs*, sostituendo i valori dei parametri con quelli effettivi:</span><span class="sxs-lookup"><span data-stu-id="7b652-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="7b652-227">Il codice precedente consente di risolvere correttamente i file resx.</span><span class="sxs-lookup"><span data-stu-id="7b652-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="7b652-228">Comportamento di fallback delle impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="7b652-228">Culture fallback behavior</span></span>

<span data-ttu-id="7b652-229">Durante la ricerca di una risorsa, la localizzazione esegue un "fallback delle impostazioni cultura".</span><span class="sxs-lookup"><span data-stu-id="7b652-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="7b652-230">A partire dalle impostazioni cultura richieste, se queste non vengono trovate, il codice adotta le impostazioni cultura padre di quelle richieste.</span><span class="sxs-lookup"><span data-stu-id="7b652-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="7b652-231">Tenere presente che la proprietà [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) rappresenta le impostazioni cultura padre.</span><span class="sxs-lookup"><span data-stu-id="7b652-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="7b652-232">Ciò comporta in genere (ma non sempre) la rimozione della notazione nazionale dal codice ISO.</span><span class="sxs-lookup"><span data-stu-id="7b652-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="7b652-233">Ad esempio la versione della lingua spagnola parlata in Messico è "es-MX".</span><span class="sxs-lookup"><span data-stu-id="7b652-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="7b652-234">L'elemento padre è "es", ovvero spagnolo non specifico per nessun paese.</span><span class="sxs-lookup"><span data-stu-id="7b652-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="7b652-235">Si supponga che il sito riceva una richiesta di una risorsa "Welcome" che usa le impostazioni cultura "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="7b652-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="7b652-236">Il sistema di localizzazione cerca le risorse seguenti nell'ordine elencato e seleziona la prima corrispondenza:</span><span class="sxs-lookup"><span data-stu-id="7b652-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="7b652-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="7b652-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="7b652-238">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="7b652-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="7b652-239">*Welcome.resx* (se `NeutralResourcesLanguage` è "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="7b652-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="7b652-240">Ad esempio, se si rimuove l'indicatore delle impostazioni cultura ".fr" e le impostazioni cultura sono impostate sul francese, viene letto il file di risorse predefinito e vengono localizzate le stringhe.</span><span class="sxs-lookup"><span data-stu-id="7b652-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="7b652-241">Lo strumento di gestione delle risorse imposta una risorsa predefinita o di fallback per i casi in cui nessun elemento soddisfa le impostazioni cultura richieste.</span><span class="sxs-lookup"><span data-stu-id="7b652-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="7b652-242">Per restituire solo la chiave in caso di mancanza di una risorsa per le impostazioni cultura richieste è necessario che non sia specificato un file di risorse predefinito.</span><span class="sxs-lookup"><span data-stu-id="7b652-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="7b652-243">Generare file di risorse con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b652-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="7b652-244">Se si crea un file di risorse in Visual Studio senza le impostazioni cultura nel nome del file (ad esempio, *Welcome.resx*), Visual Studio crea una classe C# con una proprietà per ogni stringa.</span><span class="sxs-lookup"><span data-stu-id="7b652-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="7b652-245">In genere questo non è il risultato desiderato quando si usa ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7b652-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="7b652-246">Solitamente non è presente un file di risorse con estensione *resx* predefinito (un file con estensione *resx* senza nome delle impostazioni cultura).</span><span class="sxs-lookup"><span data-stu-id="7b652-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="7b652-247">Si consiglia di creare il file con estensione *resx* con un nome delle impostazioni cultura, ad esempio *Welcome.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="7b652-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="7b652-248">Quando si crea un file con estensione *resx* con un nome delle impostazioni cultura, Visual Studio non genera il file di classe.</span><span class="sxs-lookup"><span data-stu-id="7b652-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="7b652-249">Aggiungere altre impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="7b652-249">Add other cultures</span></span>

<span data-ttu-id="7b652-250">Ogni combinazione di lingua e impostazioni cultura (diversa dalla lingua predefinita) richiede un file di risorse univoco.</span><span class="sxs-lookup"><span data-stu-id="7b652-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="7b652-251">Creare file di risorse per impostazioni cultura e locali diverse creando nuovi file di risorse in cui i codici di lingua ISO sono inclusi nel nome file, ad esempio **en-us**, **fr-ca** e **en-gb**.</span><span class="sxs-lookup"><span data-stu-id="7b652-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="7b652-252">I codici ISO vengono inseriti tra il nome file e l'estensione *resx* come in *Welcome.es-MX.resx* (spagnolo/Messico).</span><span class="sxs-lookup"><span data-stu-id="7b652-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="7b652-253">Implementare una strategia per la selezione della lingua o delle impostazioni cultura per ogni richiesta</span><span class="sxs-lookup"><span data-stu-id="7b652-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="7b652-254">Configurare la localizzazione</span><span class="sxs-lookup"><span data-stu-id="7b652-254">Configure localization</span></span>

<span data-ttu-id="7b652-255">La localizzazione è configurata nel metodo `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7b652-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="7b652-256">`AddLocalization` aggiunge i servizi di localizzazione al contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="7b652-256">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="7b652-257">Il codice precedente imposta anche il percorso delle risorse su "Resources".</span><span class="sxs-lookup"><span data-stu-id="7b652-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="7b652-258">`AddViewLocalization` aggiunge il supporto per i file di visualizzazione localizzati.</span><span class="sxs-lookup"><span data-stu-id="7b652-258">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="7b652-259">Nell'esempio la localizzazione delle visualizzazioni è basata sul suffisso dei file di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="7b652-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="7b652-260">Ad esempio "fr" nel file *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7b652-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="7b652-261">`AddDataAnnotationsLocalization` aggiunge il supporto per i messaggi di convalida `DataAnnotations` localizzati tramite le astrazioni `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="7b652-261">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="7b652-262">Middleware di localizzazione</span><span class="sxs-lookup"><span data-stu-id="7b652-262">Localization middleware</span></span>

<span data-ttu-id="7b652-263">Le impostazioni cultura correnti in un richiesta sono impostate nel [middleware](xref:fundamentals/middleware/index) di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="7b652-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="7b652-264">Il middleware di localizzazione viene abilitato nel metodo `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="7b652-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="7b652-265">Il middleware di localizzazione deve essere configurato prima di qualsiasi middleware che potrebbe controllare le impostazioni cultura della richiesta (ad esempio, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="7b652-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="7b652-266">`UseRequestLocalization` inizializza un oggetto `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="7b652-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="7b652-267">In ogni richiesta l'elenco di `RequestCultureProvider` in `RequestLocalizationOptions` è enumerato e viene usato il primo provider in grado di determinare le impostazioni cultura della richiesta.</span><span class="sxs-lookup"><span data-stu-id="7b652-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="7b652-268">I provider predefiniti provengono dalla classe `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="7b652-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="7b652-269">L'elenco predefinito passa dal più specifico al meno specifico.</span><span class="sxs-lookup"><span data-stu-id="7b652-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="7b652-270">Di seguito in questo articolo viene descritto come modificare l'ordine e aggiungere un provider delle impostazioni cultura personalizzato.</span><span class="sxs-lookup"><span data-stu-id="7b652-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="7b652-271">Se nessuno dei provider è in grado di determinare le impostazioni cultura della richiesta, viene usato `DefaultRequestCulture`.</span><span class="sxs-lookup"><span data-stu-id="7b652-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="7b652-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="7b652-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="7b652-273">Alcune app usano una stringa di query per specificare le [impostazioni cultura e le impostazioni cultura dell'interfaccia utente](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="7b652-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="7b652-274">Per le app che usano il cookie o l'approccio dell'intestazione Accept-Language, è utile aggiungere una stringa di query all'URL per eseguire il debug e il test del codice.</span><span class="sxs-lookup"><span data-stu-id="7b652-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="7b652-275">Per impostazione predefinita, `QueryStringRequestCultureProvider` viene registrato come primo provider di localizzazione nell'elenco `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="7b652-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="7b652-276">Passare i parametri della stringa di query `culture` e `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="7b652-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="7b652-277">L'esempio seguente specifica le impostazioni cultura specifiche (lingua e area) per spagnolo/Messico:</span><span class="sxs-lookup"><span data-stu-id="7b652-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="7b652-278">Se si passa uno solo dei due parametri (`culture` o `ui-culture`), il provider della stringa di query imposterà entrambi i valori usando il parametro passato.</span><span class="sxs-lookup"><span data-stu-id="7b652-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="7b652-279">Ad esempio, se si specificano solo le impostazioni cultura verranno impostati entrambi i parametri `Culture` e `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="7b652-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="7b652-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="7b652-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="7b652-281">Le app di produzione offrono spesso la possibilità di specificare le impostazioni cultura con il cookie delle impostazioni cultura di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7b652-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="7b652-282">Usare il metodo `MakeCookieValue` per creare un cookie.</span><span class="sxs-lookup"><span data-stu-id="7b652-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="7b652-283">`CookieRequestCultureProvider` Restituisce il nome del cookie predefinito utilizzato per tenere traccia delle informazioni sulle impostazioni cultura preferite dell' `DefaultCookieName` utente.</span><span class="sxs-lookup"><span data-stu-id="7b652-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="7b652-284">Il nome del cookie predefinito è `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="7b652-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="7b652-285">Il formato del cookie è `c=%LANGCODE%|uic=%LANGCODE%`, dove `c` è `Culture` e `uic` è `UICulture`, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7b652-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="7b652-286">Se si specificano solo le impostazioni cultura o le impostazioni cultura dell'interfaccia utente, le impostazioni specificate verranno usate per entrambi i tipi di impostazioni.</span><span class="sxs-lookup"><span data-stu-id="7b652-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="7b652-287">Intestazione HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="7b652-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="7b652-288">L'[intestazione Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) può essere impostata nella maggior parte dei browser ed è stata originariamente progettata per specificare la lingua dell'utente.</span><span class="sxs-lookup"><span data-stu-id="7b652-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="7b652-289">Questa impostazione indica la lingua impostata per l'invio da parte del browser o che il browser ha ereditato dal sistema operativo sottostante.</span><span class="sxs-lookup"><span data-stu-id="7b652-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="7b652-290">L'intestazione HTTP Accept-Language di una richiesta del browser non è un metodo infallibile per individuare la lingua preferita dell'utente (vedere [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php) (Impostazione delle preferenze di lingua in un browser)).</span><span class="sxs-lookup"><span data-stu-id="7b652-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="7b652-291">Un'app di produzione deve consentire all'utente di personalizzare le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="7b652-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="7b652-292">Impostare l'intestazione HTTP Accept-Language in IE</span><span class="sxs-lookup"><span data-stu-id="7b652-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="7b652-293">Dall'icona a forma di ingranaggio, toccare **Opzioni Internet**.</span><span class="sxs-lookup"><span data-stu-id="7b652-293">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="7b652-294">Toccare **Lingue**.</span><span class="sxs-lookup"><span data-stu-id="7b652-294">Tap **Languages**.</span></span>

    ![Opzioni Internet](localization/_static/lang.png)

3. <span data-ttu-id="7b652-296">Toccare **Imposta preferenze lingua**.</span><span class="sxs-lookup"><span data-stu-id="7b652-296">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="7b652-297">Toccare **Aggiungi una lingua**.</span><span class="sxs-lookup"><span data-stu-id="7b652-297">Tap **Add a language**.</span></span>

5. <span data-ttu-id="7b652-298">Aggiungere la lingua.</span><span class="sxs-lookup"><span data-stu-id="7b652-298">Add the language.</span></span>

6. <span data-ttu-id="7b652-299">Toccare la lingua e quindi toccare **Sposta su**.</span><span class="sxs-lookup"><span data-stu-id="7b652-299">Tap the language, then tap **Move Up**.</span></span>

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a><span data-ttu-id="7b652-300">Intestazione HTTP Content-Language</span><span class="sxs-lookup"><span data-stu-id="7b652-300">The Content-Language HTTP header</span></span>

<span data-ttu-id="7b652-301">Intestazione dell'entità [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :</span><span class="sxs-lookup"><span data-stu-id="7b652-301">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="7b652-302">Viene usato per descrivere le lingue destinate ai destinatari.</span><span class="sxs-lookup"><span data-stu-id="7b652-302">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="7b652-303">Consente a un utente di distinguere in base alla lingua preferita degli utenti.</span><span class="sxs-lookup"><span data-stu-id="7b652-303">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="7b652-304">Le intestazioni delle entità vengono usate sia nelle richieste HTTP sia nelle risposte.</span><span class="sxs-lookup"><span data-stu-id="7b652-304">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="7b652-305">L' `Content-Language` intestazione può essere aggiunta impostando la proprietà `ApplyCurrentCultureToResponseHeaders`.</span><span class="sxs-lookup"><span data-stu-id="7b652-305">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="7b652-306">Aggiunta dell' `Content-Language` intestazione:</span><span class="sxs-lookup"><span data-stu-id="7b652-306">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="7b652-307">Consente a RequestLocalizationMiddleware di impostare l' `Content-Language` intestazione con `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="7b652-307">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="7b652-308">Elimina la necessità di impostare l'intestazione `Content-Language` della risposta in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="7b652-308">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a><span data-ttu-id="7b652-309">Usare un provider personalizzato</span><span class="sxs-lookup"><span data-stu-id="7b652-309">Use a custom provider</span></span>

<span data-ttu-id="7b652-310">Si supponga di voler consentire agli utenti di memorizzare lingua e impostazioni cultura nei database.</span><span class="sxs-lookup"><span data-stu-id="7b652-310">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="7b652-311">È possibile creare un provider per la ricerca di questi valori per l'utente.</span><span class="sxs-lookup"><span data-stu-id="7b652-311">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="7b652-312">Il codice seguente illustra come aggiungere un provider personalizzato:</span><span class="sxs-lookup"><span data-stu-id="7b652-312">The following code shows how to add a custom provider:</span></span>

::: moniker range="< aspnetcore-3.0"
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
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
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
::: moniker-end

<span data-ttu-id="7b652-313">Usare `RequestLocalizationOptions` per aggiungere o rimuovere i provider di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="7b652-313">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="7b652-314">Specificare le impostazioni cultura a livello di codice</span><span class="sxs-lookup"><span data-stu-id="7b652-314">Set the culture programmatically</span></span>

<span data-ttu-id="7b652-315">Questo progetto di esempio **Localization.StarterWeb** in [GitHub](https://github.com/aspnet/entropy) contiene l'interfaccia utente per impostare `Culture`.</span><span class="sxs-lookup"><span data-stu-id="7b652-315">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="7b652-316">Il file *Views/Shared/_SelectLanguagePartial.cshtml* consente di selezionare le impostazioni cultura dall'elenco delle impostazioni cultura supportate:</span><span class="sxs-lookup"><span data-stu-id="7b652-316">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="7b652-317">Il file *Views/Shared/_SelectLanguagePartial.cshtml* viene aggiunto alla sezione `footer` del file di layout in modo che sia disponibile in tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="7b652-317">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="7b652-318">Il metodo `SetLanguage` imposta il cookie delle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="7b652-318">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="7b652-319">Non è possibile collegare *_SelectLanguagePartial.cshtml* al codice di esempio per questo progetto.</span><span class="sxs-lookup"><span data-stu-id="7b652-319">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="7b652-320">Il progetto **Localization. StarterWeb** in [GitHub](https://github.com/aspnet/entropy) dispone di codice per il `RequestLocalizationOptions` flusso di Razor a un oggetto parziale tramite il contenitore di [inserimento delle dipendenze](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="7b652-320">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="7b652-321">Associazione di modelli dati della route e stringhe di query</span><span class="sxs-lookup"><span data-stu-id="7b652-321">Model binding route data and query strings</span></span>

<span data-ttu-id="7b652-322">Vedere [comportamento di globalizzazione delle stringhe di query e dati della route di associazione di modelli](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="7b652-322">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="7b652-323">Termini relativi alla globalizzazione e alla localizzazione</span><span class="sxs-lookup"><span data-stu-id="7b652-323">Globalization and localization terms</span></span>

<span data-ttu-id="7b652-324">Il processo di localizzazione dell'app richiede anche una conoscenza di base dei set di caratteri comunemente usati nello sviluppo del software moderno e dei problemi associati a essi.</span><span class="sxs-lookup"><span data-stu-id="7b652-324">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="7b652-325">Sebbene tutti i computer memorizzano il testo come numeri (codici), sistemi diversi memorizzano lo stesso testo usando numeri diversi.</span><span class="sxs-lookup"><span data-stu-id="7b652-325">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="7b652-326">Il processo di localizzazione si riferisce alla traduzione dell'interfaccia utente dell'app per impostazioni cultura o locali specifiche.</span><span class="sxs-lookup"><span data-stu-id="7b652-326">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="7b652-327">La [localizzabilità](/dotnet/standard/globalization-localization/localizability-review) è un processo intermedio che verifica che un'app globalizzata sia pronta per la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="7b652-327">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="7b652-328">Il formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) per il nome delle impostazioni cultura è `<languagecode2>-<country/regioncode2>`, dove `<languagecode2>` è il codice della lingua e `<country/regioncode2>` è il codice della cultura secondaria.</span><span class="sxs-lookup"><span data-stu-id="7b652-328">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="7b652-329">Ad esempio, `es-CL` per spagnolo (Cile), `en-US` per inglese (Stati Uniti) e `en-AU` per inglese (Australia).</span><span class="sxs-lookup"><span data-stu-id="7b652-329">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="7b652-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) è la combinazione di un codice di cultura in lettere minuscole di due lettere ISO 639 associato a una lingua e un codice di cultura secondaria in lettere maiuscole di due lettere ISO 3166 associato a un paese o un'area.</span><span class="sxs-lookup"><span data-stu-id="7b652-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="7b652-331">Vedere [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx) (Nome delle impostazioni cultura delle lingue).</span><span class="sxs-lookup"><span data-stu-id="7b652-331">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="7b652-332">L'internazionalizzazione è spesso abbreviata con "I18N".</span><span class="sxs-lookup"><span data-stu-id="7b652-332">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="7b652-333">L'abbreviazione include la prima e l'ultima lettera e il numero di lettere che intercorrono tra di loro nel termine inglese "Internationalization". 18 è il numero di lettere tra la prima "I" e l'ultima "N".</span><span class="sxs-lookup"><span data-stu-id="7b652-333">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="7b652-334">Lo stesso vale per globalizzazione (G11N) e localizzazione (L10N).</span><span class="sxs-lookup"><span data-stu-id="7b652-334">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="7b652-335">Termini:</span><span class="sxs-lookup"><span data-stu-id="7b652-335">Terms:</span></span>

* <span data-ttu-id="7b652-336">Globalizzazione (G11N): processo che consente a un'app di supportare lingue e aree diverse.</span><span class="sxs-lookup"><span data-stu-id="7b652-336">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="7b652-337">Localizzazione (L10N): processo di personalizzazione di un'app per una determinata lingua e area.</span><span class="sxs-lookup"><span data-stu-id="7b652-337">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="7b652-338">Internazionalizzazione (I18N): descrive la globalizzazione e la localizzazione.</span><span class="sxs-lookup"><span data-stu-id="7b652-338">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="7b652-339">Impostazioni cultura: si tratta di una lingua e, facoltativamente, di un'area.</span><span class="sxs-lookup"><span data-stu-id="7b652-339">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="7b652-340">Impostazioni cultura neutre: cultura con una lingua specificata ma senza area.</span><span class="sxs-lookup"><span data-stu-id="7b652-340">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="7b652-341">(ad esempio "en", "es")</span><span class="sxs-lookup"><span data-stu-id="7b652-341">(for example "en", "es")</span></span>
* <span data-ttu-id="7b652-342">Impostazioni cultura specifiche: cultura con una lingua e un'area specificate.</span><span class="sxs-lookup"><span data-stu-id="7b652-342">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="7b652-343">(ad esempio "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="7b652-343">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="7b652-344">Impostazioni padre: impostazioni cultura neutre con impostazioni cultura specifiche.</span><span class="sxs-lookup"><span data-stu-id="7b652-344">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="7b652-345">(ad esempio, "en" rappresenta le impostazioni cultura padre di "en-US" e "en-GB")</span><span class="sxs-lookup"><span data-stu-id="7b652-345">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="7b652-346">Impostazioni locali: le impostazioni locali corrispondono alle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="7b652-346">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7b652-347">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7b652-347">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="7b652-348">[Progetto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usato nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="7b652-348">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="7b652-349">Globalizzazione e localizzazione di applicazioni .NET</span><span class="sxs-lookup"><span data-stu-id="7b652-349">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="7b652-350">Risorse nei file con estensione resx</span><span class="sxs-lookup"><span data-stu-id="7b652-350">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="7b652-351">Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="7b652-351">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="7b652-352">Localizzazione e generics</span><span class="sxs-lookup"><span data-stu-id="7b652-352">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)
