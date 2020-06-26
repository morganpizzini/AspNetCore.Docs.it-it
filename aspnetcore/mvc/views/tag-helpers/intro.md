---
title: Helper tag in ASP.NET Core
author: rick-anderson
description: Informazioni sugli helper tag e su come usarli in ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 36d8c3dd42ac9d0acdb93bba4bc39ab18ad5c52f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400895"
---
# <a name="tag-helpers-in-aspnet-core"></a><span data-ttu-id="1054b-103">Helper tag in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1054b-103">Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="1054b-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1054b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="1054b-105">Descrizione di helper tag</span><span class="sxs-lookup"><span data-stu-id="1054b-105">What are Tag Helpers</span></span>

<span data-ttu-id="1054b-106">Gli helper tag consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.</span><span class="sxs-lookup"><span data-stu-id="1054b-106">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="1054b-107">Ad esempio, l'elemento predefinito `ImageTagHelper` può aggiungere un numero di versione al nome dell'immagine.</span><span class="sxs-lookup"><span data-stu-id="1054b-107">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="1054b-108">Quando l'immagine viene modificata, il server genera una nuova versione univoca per l'immagine, in modo da garantire che i client ricevano sempre l'immagine corrente (anziché un'immagine non aggiornata memorizzata nella cache).</span><span class="sxs-lookup"><span data-stu-id="1054b-108">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="1054b-109">Esistono molti helper tag predefiniti per le attività comuni, ad esempio la creazione di moduli e collegamenti, il caricamento di asset e così via, e altri ancora sono disponibili nei repository GitHub pubblici e come pacchetti NuGet.</span><span class="sxs-lookup"><span data-stu-id="1054b-109">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="1054b-110">Gli helper tag vengono creati in C# e hanno come destinazione gli elementi HTML in base al nome di elemento, nome di attributo o tag padre.</span><span class="sxs-lookup"><span data-stu-id="1054b-110">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="1054b-111">Ad esempio, l'elemento predefinito `LabelTagHelper` può avere come destinazione l'elemento HTML `<label>` quando vengono applicati gli attributi di `LabelTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="1054b-111">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="1054b-112">Se si ha familiarità con gli [helper HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), gli helper Tag riducono le transizioni esplicite tra HTML e C# nelle Razor visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="1054b-112">If you're familiar with [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in Razor views.</span></span> <span data-ttu-id="1054b-113">In molti casi, gli helper HTML offrono un approccio alternativo a un helper tag specifico, ma è importante tenere presente che gli helper tag non sostituiscono gli helper HTML e che non esiste un helper tag per ogni helper HTML.</span><span class="sxs-lookup"><span data-stu-id="1054b-113">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="1054b-114">Nella sezione [Helper tag e helper HTML a confronto](#tag-helpers-compared-to-html-helpers) vengono illustrate le differenze in modo più dettagliato.</span><span class="sxs-lookup"><span data-stu-id="1054b-114">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="1054b-115">Vantaggi degli helper tag</span><span class="sxs-lookup"><span data-stu-id="1054b-115">What Tag Helpers provide</span></span>

<span data-ttu-id="1054b-116">**Esperienza di sviluppo intuitiva** Nella maggior parte dei casi, il Razor markup che usa gli helper tag è simile a HTML standard.</span><span class="sxs-lookup"><span data-stu-id="1054b-116">**An HTML-friendly development experience** For the most part, Razor markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="1054b-117">I progettisti front-end che hanno dimestichezza con HTML/CSS/JavaScript possono essere modificati Razor senza conoscere la Razor sintassi C#.</span><span class="sxs-lookup"><span data-stu-id="1054b-117">Front-end designers conversant with HTML/CSS/JavaScript can edit Razor without learning C# Razor syntax.</span></span>

<span data-ttu-id="1054b-118">**Un ambiente IntelliSense avanzato per la creazione di HTML e Razor markup** è in contrasto acuto con gli helper HTML, l'approccio precedente alla creazione lato server del markup nelle Razor visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="1054b-118">**A rich IntelliSense environment for creating HTML and Razor markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in Razor views.</span></span> <span data-ttu-id="1054b-119">Nella sezione [Helper tag e helper HTML a confronto](#tag-helpers-compared-to-html-helpers) vengono illustrate le differenze in modo più dettagliato.</span><span class="sxs-lookup"><span data-stu-id="1054b-119">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="1054b-120">Nella sezione [Supporto IntelliSense per gli helper tag](#intellisense-support-for-tag-helpers) viene illustrato l'ambiente IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="1054b-120">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="1054b-121">Anche gli sviluppatori esperti con la Razor sintassi c# sono più produttivi usando gli helper Tag rispetto alla scrittura di Razor markup c#.</span><span class="sxs-lookup"><span data-stu-id="1054b-121">Even developers experienced with Razor C# syntax are more productive using Tag Helpers than writing C# Razor markup.</span></span>

<span data-ttu-id="1054b-122">**Possibilità di aumentare la produttività e la capacità di produrre codice più solido, affidabile e gestibile usando le informazioni disponibili solo nel server**: storicamente il mantra a proposito dell'aggiornamento delle immagini, ad esempio, era quello di cambiare il nome dell'immagine quando l'immagine veniva modificata.</span><span class="sxs-lookup"><span data-stu-id="1054b-122">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="1054b-123">Per motivi di prestazioni, le immagini devono essere memorizzate nella cache e, a meno che il nome dell'immagine non venga cambiato, si rischia che i client ricevano una copia non aggiornata.</span><span class="sxs-lookup"><span data-stu-id="1054b-123">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="1054b-124">Dopo che un'immagine era stata modificata, il nome doveva essere sempre cambiato e ogni riferimento all'immagine nell'app Web doveva essere aggiornato.</span><span class="sxs-lookup"><span data-stu-id="1054b-124">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="1054b-125">Non solo questa operazione richiede molto lavoro, ma è anche soggetta a errori (è possibile perdere un riferimento, immettere accidentalmente la stringa sbagliata e così via). `ImageTagHelper`Questa operazione può essere eseguita automaticamente.</span><span class="sxs-lookup"><span data-stu-id="1054b-125">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="1054b-126">`ImageTagHelper` è in grado di aggiungere un numero di versione al nome dell'immagine in modo che quando l'immagine viene modificata il server generi automaticamente una nuova versione univoca dell'immagine.</span><span class="sxs-lookup"><span data-stu-id="1054b-126">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="1054b-127">I client riceveranno sicuramente l'immagine corrente.</span><span class="sxs-lookup"><span data-stu-id="1054b-127">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="1054b-128">Grazie all'uso di `ImageTagHelper`, efficienza e risparmio di energie sono essenzialmente gratuiti.</span><span class="sxs-lookup"><span data-stu-id="1054b-128">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="1054b-129">La maggior parte degli helper tag predefiniti usano come destinazione elementi HTML standard e specificano attributi sul lato server per l'elemento.</span><span class="sxs-lookup"><span data-stu-id="1054b-129">Most built-in Tag Helpers target standard HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="1054b-130">Ad esempio, l'elemento `<input>` usato in molte visualizzazioni nella cartella *Views/Account* (Visualizzazioni/Account) contiene l'attributo `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="1054b-130">For example, the `<input>` element used in many views in the *Views/Account* folder contains the `asp-for` attribute.</span></span> <span data-ttu-id="1054b-131">Questo attributo consente di estrarre il nome della proprietà del modello specificato nel codice HTML visualizzabile.</span><span class="sxs-lookup"><span data-stu-id="1054b-131">This attribute extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="1054b-132">Si consideri una Razor vista con il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="1054b-132">Consider a Razor view with the following model:</span></span>

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

<span data-ttu-id="1054b-133">Il Razor markup seguente:</span><span class="sxs-lookup"><span data-stu-id="1054b-133">The following Razor markup:</span></span>

```cshtml
<label asp-for="Movie.Title"></label>
```

<span data-ttu-id="1054b-134">Viene generato il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="1054b-134">Generates the following HTML:</span></span>

```html
<label for="Movie_Title">Title</label>
```

<span data-ttu-id="1054b-135">L'attributo `asp-for` viene reso disponibile dalla proprietà `For` in [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="1054b-135">The `asp-for` attribute is made available by the `For` property in the [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span></span> <span data-ttu-id="1054b-136">Per altre informazioni, vedere [Creare helper tag](xref:mvc/views/tag-helpers/authoring).</span><span class="sxs-lookup"><span data-stu-id="1054b-136">See [Author Tag Helpers](xref:mvc/views/tag-helpers/authoring) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="1054b-137">Gestione dell'ambito dell'helper tag</span><span class="sxs-lookup"><span data-stu-id="1054b-137">Managing Tag Helper scope</span></span>

<span data-ttu-id="1054b-138">L'ambito degli helper tag è controllato da una combinazione di `@addTagHelper`, `@removeTagHelper` e del carattere di esclusione "!".</span><span class="sxs-lookup"><span data-stu-id="1054b-138">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="1054b-139">`@addTagHelper` rende disponibili gli helper tag</span><span class="sxs-lookup"><span data-stu-id="1054b-139">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="1054b-140">Se si crea una nuova app Web ASP.NET Core denominata *AuthoringTagHelpers*, al progetto verrà aggiunto il file *Views/_ViewImports.cshtml* seguente:</span><span class="sxs-lookup"><span data-stu-id="1054b-140">If you create a new ASP.NET Core web app named *AuthoringTagHelpers*, the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="1054b-141">La direttiva `@addTagHelper` rende gli helper tag disponibili per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="1054b-141">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="1054b-142">In questo caso, il file della visualizzazione è *Pages/_ViewImports.cshtml* che per impostazione predefinita viene ereditato da tutti i file nella cartella *Pages* e nelle sottocartelle, rendendo disponibili gli helper tag.</span><span class="sxs-lookup"><span data-stu-id="1054b-142">In this case, the view file is *Pages/_ViewImports.cshtml*, which by default is inherited by all files in the *Pages* folder and subfolders; making Tag Helpers available.</span></span> <span data-ttu-id="1054b-143">Il codice precedente usa la sintassi con caratteri jolly (" \* ") per specificare che tutti gli helper tag nell'assembly specificato (*Microsoft. AspNetCore. Mvc. TagHelpers*) saranno disponibili per ogni file di visualizzazione nella directory *views* o nella sottodirectory.</span><span class="sxs-lookup"><span data-stu-id="1054b-143">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly (*Microsoft.AspNetCore.Mvc.TagHelpers*) will be available to every view file in the *Views* directory or subdirectory.</span></span> <span data-ttu-id="1054b-144">Il primo parametro dopo `@addTagHelper` specifica gli helper tag da caricare (viene usato "\*" per tutti gli helper tag) e il secondo parametro "Microsoft.AspNetCore.Mvc.TagHelpers" specifica l'assembly contenente gli helper tag.</span><span class="sxs-lookup"><span data-stu-id="1054b-144">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="1054b-145">*Microsoft.AspNetCore.Mvc.TagHelpers* è l'assembly per gli helper tag predefiniti di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1054b-145">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="1054b-146">Per esporre tutti gli helper tag di questo progetto, che crea un assembly denominato *AuthoringTagHelpers*, usare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1054b-146">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers*), you would use the following:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="1054b-147">Se il progetto contiene un `EmailTagHelper` con lo spazio dei nomi predefinito (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), è possibile fornire il nome completo dell'helper tag:</span><span class="sxs-lookup"><span data-stu-id="1054b-147">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="1054b-148">Per aggiungere un helper tag a una visualizzazione usando un nome completo, aggiungere prima il nome completo (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) e quindi il nome dell'assembly (*AuthoringTagHelpers*).</span><span class="sxs-lookup"><span data-stu-id="1054b-148">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name (*AuthoringTagHelpers*).</span></span> <span data-ttu-id="1054b-149">La maggior parte degli sviluppatori preferisce usare la sintassi con caratteri jolly "\*".</span><span class="sxs-lookup"><span data-stu-id="1054b-149">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="1054b-150">La sintassi con caratteri jolly consente di inserire il carattere jolly "\*" come suffisso di un nome completo.</span><span class="sxs-lookup"><span data-stu-id="1054b-150">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="1054b-151">Le direttive seguenti, ad esempio, aggiungeranno `EmailTagHelper`:</span><span class="sxs-lookup"><span data-stu-id="1054b-151">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="1054b-152">Come indicato in precedenza, l'aggiunta della `@addTagHelper` direttiva al file *views/_ViewImports. cshtml* rende l'helper tag disponibile a tutti i file di visualizzazione nella directory *views* e nelle sottodirectory.</span><span class="sxs-lookup"><span data-stu-id="1054b-152">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and subdirectories.</span></span> <span data-ttu-id="1054b-153">È possibile usare la direttiva `@addTagHelper` in file di visualizzazione specifici se si vuole acconsentire esplicitamente a esporre l'helper tag solo a queste visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="1054b-153">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="1054b-154">`@removeTagHelper` rimuove gli helper tag</span><span class="sxs-lookup"><span data-stu-id="1054b-154">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="1054b-155">`@removeTagHelper` contiene gli stessi due parametri di `@addTagHelper` e rimuove un helper tag che era stato precedentemente aggiunto.</span><span class="sxs-lookup"><span data-stu-id="1054b-155">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="1054b-156">Se ad esempio si applica `@removeTagHelper` a una visualizzazione specifica, l'helper tag specificato viene rimosso dalla visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="1054b-156">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="1054b-157">L'uso di `@removeTagHelper` in un file *Views/Folder/_ViewImports.cshtml* comporta la rimozione dell'helper tag specificato da tutte le visualizzazioni in *Folder*.</span><span class="sxs-lookup"><span data-stu-id="1054b-157">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder*.</span></span>

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a><span data-ttu-id="1054b-158">Controllo dell'ambito dell'helper tag con il file *_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1054b-158">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="1054b-159">È possibile aggiungere un file *_ViewImports.cshtml* a una cartella di visualizzazione qualsiasi e il motore di visualizzazione applicherà le direttive di questo file e del file *Views/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1054b-159">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="1054b-160">L'aggiunta di un file *Views/Home/_ViewImports.cshtml* vuoto per le visualizzazioni *Home* non comporta modifiche perché il file *_ViewImports.cshtml* è additivo.</span><span class="sxs-lookup"><span data-stu-id="1054b-160">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="1054b-161">Le direttive `@addTagHelper` aggiunte al file *Views/Home/_ViewImports.cshtml* (non presenti nel file *Views/_ViewImports.cshtml* predefinito) espongono questi helper tag alle visualizzazioni solo nella cartella *Home*.</span><span class="sxs-lookup"><span data-stu-id="1054b-161">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="1054b-162">Esclusione di singoli elementi</span><span class="sxs-lookup"><span data-stu-id="1054b-162">Opting out of individual elements</span></span>

<span data-ttu-id="1054b-163">È possibile disabilitare un helper tag a livello di elemento con il carattere di esclusione ("!") dell'helper tag.</span><span class="sxs-lookup"><span data-stu-id="1054b-163">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="1054b-164">La convalida `Email` viene ad esempio disabilitata in `<span>` con il carattere di esclusione dell'helper tag:</span><span class="sxs-lookup"><span data-stu-id="1054b-164">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="1054b-165">È necessario applicare il carattere di esclusione dell'helper tag ai tag di apertura e chiusura.</span><span class="sxs-lookup"><span data-stu-id="1054b-165">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="1054b-166">L'editor di Visual Studio aggiunge automaticamente il carattere di esclusione al tag di chiusura se ne è stato aggiunto uno al tag di apertura.</span><span class="sxs-lookup"><span data-stu-id="1054b-166">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="1054b-167">Dopo che il carattere di esclusione è stato aggiunto, l'elemento e gli attributi dell'helper tag non verranno più visualizzati in un tipo di carattere distintivo.</span><span class="sxs-lookup"><span data-stu-id="1054b-167">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="1054b-168">Uso di `@tagHelperPrefix` per rendere esplicito l'uso dell'helper tag</span><span class="sxs-lookup"><span data-stu-id="1054b-168">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="1054b-169">La direttiva `@tagHelperPrefix` consente di specificare una stringa di prefisso del tag per abilitare il supporto dell'helper tag e renderne esplicito l'uso.</span><span class="sxs-lookup"><span data-stu-id="1054b-169">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="1054b-170">È possibile, ad esempio, aggiungere il markup seguente al file *Views/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1054b-170">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```cshtml
@tagHelperPrefix th:
```

<span data-ttu-id="1054b-171">Nell'immagine di codice seguente, il prefisso dell'helper tag è impostato su `th:`. Pertanto, solo gli elementi che usano il prefisso `th:` supportano gli helper tag. Gli elementi abilitati per gli helper tag hanno un tipo di carattere distintivo.</span><span class="sxs-lookup"><span data-stu-id="1054b-171">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="1054b-172">Gli elementi `<label>` e `<input>` includono il prefisso dell'helper tag e sono abilitati per gli helper tag, mentre l'elemento `<span>` non lo è.</span><span class="sxs-lookup"><span data-stu-id="1054b-172">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element doesn't.</span></span>

![image](intro/_static/thp.png)

<span data-ttu-id="1054b-174">Le stesse regole di gerarchia che si applicano a `@addTagHelper` sono valide anche per `@tagHelperPrefix`.</span><span class="sxs-lookup"><span data-stu-id="1054b-174">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="self-closing-tag-helpers"></a><span data-ttu-id="1054b-175">Helper tag di chiusura automatico</span><span class="sxs-lookup"><span data-stu-id="1054b-175">Self-closing Tag Helpers</span></span>

<span data-ttu-id="1054b-176">Molti helper tag non possono essere usati come tag di chiusura automatici.</span><span class="sxs-lookup"><span data-stu-id="1054b-176">Many Tag Helpers can't be used as self-closing tags.</span></span> <span data-ttu-id="1054b-177">Alcuni helper tag sono progettati per essere tag di chiusura automatici.</span><span class="sxs-lookup"><span data-stu-id="1054b-177">Some Tag Helpers are designed to be self-closing tags.</span></span> <span data-ttu-id="1054b-178">Se si usa un helper tag non progettato per la chiusura automatica, l'output sottoposto a rendering viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="1054b-178">Using a Tag Helper that was not designed to be self-closing suppresses the rendered output.</span></span> <span data-ttu-id="1054b-179">La chiusura automatica di un helper tag genera un tag di chiusura automatico nell'output sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="1054b-179">Self-closing a Tag Helper results in a self-closing tag in the rendered output.</span></span> <span data-ttu-id="1054b-180">Per altre informazioni, vedere [questa nota](xref:mvc/views/tag-helpers/authoring#self-closing) in [Creare helper tag in ASP.NET Core](xref:mvc/views/tag-helpers/authoring).</span><span class="sxs-lookup"><span data-stu-id="1054b-180">For more information, see [this note](xref:mvc/views/tag-helpers/authoring#self-closing) in [Authoring Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span></span>

## <a name="c-in-tag-helpers-attributedeclaration"></a><span data-ttu-id="1054b-181">C# nell'attributo/dichiarazione degli helper Tag</span><span class="sxs-lookup"><span data-stu-id="1054b-181">C# in Tag Helpers attribute/declaration</span></span> 

<span data-ttu-id="1054b-182">Gli helper tag non consentono C# nell'area di dichiarazione dell'attributo o tag dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="1054b-182">Tag Helpers do not allow C# in the element's attribute or tag declaration area.</span></span> <span data-ttu-id="1054b-183">Ad esempio, il codice seguente non è valido:</span><span class="sxs-lookup"><span data-stu-id="1054b-183">For example, the following code is not valid:</span></span>

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

<span data-ttu-id="1054b-184">Il codice precedente può essere scritto come segue:</span><span class="sxs-lookup"><span data-stu-id="1054b-184">The preceding code can be written as:</span></span>

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="1054b-185">Supporto IntelliSense per gli helper tag</span><span class="sxs-lookup"><span data-stu-id="1054b-185">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="1054b-186">Quando si crea un nuovo ASP.NET Core app Web in Visual Studio, viene aggiunto il pacchetto NuGet " Razor Microsoft. AspNetCore. Strumenti ".</span><span class="sxs-lookup"><span data-stu-id="1054b-186">When you create a new ASP.NET Core web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.Razor.Tools".</span></span> <span data-ttu-id="1054b-187">ovvero il pacchetto che aggiunge gli strumenti dell'helper tag.</span><span class="sxs-lookup"><span data-stu-id="1054b-187">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="1054b-188">Si supponga di scrivere un elemento `<label>` HTML.</span><span class="sxs-lookup"><span data-stu-id="1054b-188">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="1054b-189">Non appena si inizia a digitare `<l` nell'editor di Visual Studio, IntelliSense visualizza gli elementi corrispondenti:</span><span class="sxs-lookup"><span data-stu-id="1054b-189">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image](intro/_static/label.png)

<span data-ttu-id="1054b-191">Oltre alla guida HTML verrà visualizzata anche l'icona (il "@" symbol with "<>" sotto di essa).</span><span class="sxs-lookup"><span data-stu-id="1054b-191">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![image](intro/_static/tagSym.png)

<span data-ttu-id="1054b-193">che identifica l'elemento come destinazione degli helper tag.</span><span class="sxs-lookup"><span data-stu-id="1054b-193">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="1054b-194">Gli elementi HTML puri (ad esempio, `fieldset`) visualizzano l'icona "<>".</span><span class="sxs-lookup"><span data-stu-id="1054b-194">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="1054b-195">Un tag `<label>` HTML puro visualizza il tag HTML (con il tema colori di Visual Studio predefinito) con un tipo di carattere marrone, gli attributi in rosso e i valori degli attributi in blu.</span><span class="sxs-lookup"><span data-stu-id="1054b-195">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![image](intro/_static/LableHtmlTag.png)

<span data-ttu-id="1054b-197">Dopo avere immesso `<label`, IntelliSense elenca gli attributi HTML/CSS disponibili e gli attributi di destinazione dell'helper tag:</span><span class="sxs-lookup"><span data-stu-id="1054b-197">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![image](intro/_static/labelattr.png)

<span data-ttu-id="1054b-199">Il completamento istruzioni di IntelliSense consente di usare il tasto TAB per completare l'istruzione con il valore selezionato:</span><span class="sxs-lookup"><span data-stu-id="1054b-199">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![image](intro/_static/stmtcomplete.png)

<span data-ttu-id="1054b-201">Non appena un attributo dell'helper tag viene immesso, i tipi di carattere del tag e dell'attributo cambiano.</span><span class="sxs-lookup"><span data-stu-id="1054b-201">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="1054b-202">Con il tema colori predefinito "Blu" o "Chiaro" di Visual Studio, il tipo di carattere è in grassetto viola.</span><span class="sxs-lookup"><span data-stu-id="1054b-202">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="1054b-203">Se si usa il tema colori "Scuro", il tipo di carattere è in grassetto verde acqua.</span><span class="sxs-lookup"><span data-stu-id="1054b-203">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="1054b-204">Nelle immagini acquisite per questo documento è stato usato il tema predefinito.</span><span class="sxs-lookup"><span data-stu-id="1054b-204">The images in this document were taken using the default theme.</span></span>

![image](intro/_static/labelaspfor2.png)

<span data-ttu-id="1054b-206">È possibile usare il tasto di scelta rapida *Completa parola* di Visual Studio (CTRL+BARRA SPAZIATRICE è la combinazione [predefinita](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) all'interno delle virgolette doppie ("") per passare a C#, proprio come se ci si trovasse in una classe C#.</span><span class="sxs-lookup"><span data-stu-id="1054b-206">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="1054b-207">IntelliSense visualizza tutti i metodi e le proprietà nel modello di pagina.</span><span class="sxs-lookup"><span data-stu-id="1054b-207">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="1054b-208">I metodi e le proprietà sono disponibili perché il tipo di proprietà è `ModelExpression`.</span><span class="sxs-lookup"><span data-stu-id="1054b-208">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="1054b-209">Nell'immagine seguente viene modificata la visualizzazione `Register`, quindi è disponibile il modello `RegisterViewModel`.</span><span class="sxs-lookup"><span data-stu-id="1054b-209">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![image](intro/_static/intellemail.png)

<span data-ttu-id="1054b-211">IntelliSense elenca le proprietà e i metodi disponibili per il modello nella pagina.</span><span class="sxs-lookup"><span data-stu-id="1054b-211">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="1054b-212">L'ambiente avanzato di IntelliSense consente di selezionare la classe CSS:</span><span class="sxs-lookup"><span data-stu-id="1054b-212">The rich IntelliSense environment helps you select the CSS class:</span></span>

![image](intro/_static/iclass.png)

![image](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="1054b-215">Helper tag e helper HTML a confronto</span><span class="sxs-lookup"><span data-stu-id="1054b-215">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="1054b-216">Gli helper tag si connettono agli elementi HTML nelle Razor viste, mentre gli [helper HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) vengono richiamati come metodi intercalati con HTML nelle Razor visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="1054b-216">Tag Helpers attach to HTML elements in Razor views, while [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in Razor views.</span></span> <span data-ttu-id="1054b-217">Si consideri il Razor markup seguente che crea un'etichetta HTML con la classe CSS "Caption":</span><span class="sxs-lookup"><span data-stu-id="1054b-217">Consider the following Razor markup, which creates an HTML label with the CSS class "caption":</span></span>

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="1054b-218">Il simbolo at ( `@` ) indica che Razor si tratta dell'inizio del codice.</span><span class="sxs-lookup"><span data-stu-id="1054b-218">The at (`@`) symbol tells Razor this is the start of code.</span></span> <span data-ttu-id="1054b-219">I due parametri successivi ("FirstName" e "First Name:") sono stringhe, quindi [IntelliSense](/visualstudio/ide/using-intellisense) non risulta utile.</span><span class="sxs-lookup"><span data-stu-id="1054b-219">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="1054b-220">L'ultimo argomento:</span><span class="sxs-lookup"><span data-stu-id="1054b-220">The last argument:</span></span>

```cshtml
new {@class="caption"}
```

<span data-ttu-id="1054b-221">è un oggetto anonimo usato per rappresentare gli attributi.</span><span class="sxs-lookup"><span data-stu-id="1054b-221">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="1054b-222">Poiché `class` è una parola chiave riservata in C#, usare il simbolo `@` per imporre a C# di interpretare `@class=` come un simbolo (nome di proprietà).</span><span class="sxs-lookup"><span data-stu-id="1054b-222">Because `class` is a reserved keyword in C#, you use the `@` symbol to force C# to interpret `@class=` as a symbol (property name).</span></span> <span data-ttu-id="1054b-223">A una finestra di progettazione front-end (qualcuno che conosce HTML/CSS/JavaScript e altre tecnologie client ma non ha familiarità con C# e Razor ), la maggior parte della linea è straniera.</span><span class="sxs-lookup"><span data-stu-id="1054b-223">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and Razor), most of the line is foreign.</span></span> <span data-ttu-id="1054b-224">L'intera riga deve essere creata senza alcun aiuto da parte di IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="1054b-224">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="1054b-225">Usando `LabelTagHelper`, è possibile scrivere lo stesso markup nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="1054b-225">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

<span data-ttu-id="1054b-226">Con la versione helper tag, non appena si digita `<l` nell'editor di Visual Studio, IntelliSense visualizza gli elementi corrispondenti:</span><span class="sxs-lookup"><span data-stu-id="1054b-226">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image](intro/_static/label.png)

<span data-ttu-id="1054b-228">IntelliSense aiuta a scrivere l'intera riga.</span><span class="sxs-lookup"><span data-stu-id="1054b-228">IntelliSense helps you write the entire line.</span></span>

<span data-ttu-id="1054b-229">Nell'immagine di codice seguente viene illustrata la parte del form della vista *views/account/Register. cshtml* Razor generata dal modello MVC ASP.NET 4.5. x incluso in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1054b-229">The following code image shows the Form portion of the *Views/Account/Register.cshtml* Razor view generated from the ASP.NET 4.5.x MVC template included with Visual Studio.</span></span>

![image](intro/_static/regCS.png)

<span data-ttu-id="1054b-231">Nell'editor di Visual Studio il codice C# viene visualizzato con uno sfondo grigio.</span><span class="sxs-lookup"><span data-stu-id="1054b-231">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="1054b-232">Ad esempio, l'helper HTML `AntiForgeryToken`:</span><span class="sxs-lookup"><span data-stu-id="1054b-232">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```cshtml
@Html.AntiForgeryToken()
```

<span data-ttu-id="1054b-233">viene visualizzato con uno sfondo grigio.</span><span class="sxs-lookup"><span data-stu-id="1054b-233">is displayed with a grey background.</span></span> <span data-ttu-id="1054b-234">La maggior parte del markup nella visualizzazione Register è C#.</span><span class="sxs-lookup"><span data-stu-id="1054b-234">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="1054b-235">Confrontarlo con l'approccio equivalente usando gli helper tag:</span><span class="sxs-lookup"><span data-stu-id="1054b-235">Compare that to the equivalent approach using Tag Helpers:</span></span>

![image](intro/_static/regTH.png)

<span data-ttu-id="1054b-237">Rispetto all'approccio con gli helper HTML, il markup è molto più chiaro e facile da leggere, modificare e gestire.</span><span class="sxs-lookup"><span data-stu-id="1054b-237">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="1054b-238">Il codice C# è ridotto al numero minimo di elementi che il server deve conoscere.</span><span class="sxs-lookup"><span data-stu-id="1054b-238">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="1054b-239">Nell'editor di Visual Studio il markup di destinazione di un helper tag viene visualizzato in un tipo di carattere distintivo.</span><span class="sxs-lookup"><span data-stu-id="1054b-239">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="1054b-240">Si osservi il gruppo *Email*:</span><span class="sxs-lookup"><span data-stu-id="1054b-240">Consider the *Email* group:</span></span>

[!code-csharp[](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="1054b-241">Ogni attributo "asp-" ha il valore "Email", ma "Email" non è una stringa.</span><span class="sxs-lookup"><span data-stu-id="1054b-241">Each of the "asp-" attributes has a value of "Email", but "Email" isn't a string.</span></span> <span data-ttu-id="1054b-242">In questo contesto "Email" è la proprietà di espressione del modello C# per `RegisterViewModel`.</span><span class="sxs-lookup"><span data-stu-id="1054b-242">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="1054b-243">L'editor di Visual Studio agevola la scrittura di **tutto** il markup nell'approccio con l'helper tag del modulo di registrazione, mentre Visual Studio non offre alcun aiuto per la maggior parte del codice nell'approccio con gli helper HTML.</span><span class="sxs-lookup"><span data-stu-id="1054b-243">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="1054b-244">Nella sezione [Supporto IntelliSense per gli helper tag](#intellisense-support-for-tag-helpers) viene illustrato in modo dettagliato l'uso degli helper tag nell'editor di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1054b-244">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="1054b-245">Helper tag e controlli server Web a confronto</span><span class="sxs-lookup"><span data-stu-id="1054b-245">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="1054b-246">Gli helper tag non sono i proprietari dell'elemento a cui sono associati, ma partecipano semplicemente al rendering dell'elemento e del contenuto.</span><span class="sxs-lookup"><span data-stu-id="1054b-246">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="1054b-247">I [controlli server Web](https://msdn.microsoft.com/library/7698y1f0.aspx) di ASP.NET vengono dichiarati e richiamati in una pagina.</span><span class="sxs-lookup"><span data-stu-id="1054b-247">ASP.NET [Web Server controls](https://msdn.microsoft.com/library/7698y1f0.aspx) are declared and invoked on a page.</span></span>

* <span data-ttu-id="1054b-248">I [controlli server Web](https://msdn.microsoft.com/library/zsyt68f1.aspx) hanno un ciclo di vita non semplice che può rendere difficili le attività di sviluppo e debug.</span><span class="sxs-lookup"><span data-stu-id="1054b-248">[Web Server controls](https://msdn.microsoft.com/library/zsyt68f1.aspx) have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="1054b-249">I controlli server Web consentono di aggiungere funzionalità agli elementi DOM (Document Object Model) del client tramite un controllo client.</span><span class="sxs-lookup"><span data-stu-id="1054b-249">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="1054b-250">Gli helper tag non includono DOM.</span><span class="sxs-lookup"><span data-stu-id="1054b-250">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="1054b-251">I controlli server Web includono il rilevamento automatico del browser.</span><span class="sxs-lookup"><span data-stu-id="1054b-251">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="1054b-252">Gli helper tag non hanno conoscenza del browser.</span><span class="sxs-lookup"><span data-stu-id="1054b-252">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="1054b-253">Più helper tag possono agire sullo stesso elemento, vedere [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) (Evitare i conflitti degli helper tag), mentre non è possibile in genere comporre i controlli server Web.</span><span class="sxs-lookup"><span data-stu-id="1054b-253">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="1054b-254">Gli helper tag possono modificare il tag e il contenuto degli elementi HTML che rientrano nel proprio ambito, ma non modificano altro in modo diretto in una pagina.</span><span class="sxs-lookup"><span data-stu-id="1054b-254">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="1054b-255">I controlli server Web hanno un ambito meno specifico e possono eseguire azioni che influiscono su altre parti della pagina, provocando effetti collaterali imprevisti.</span><span class="sxs-lookup"><span data-stu-id="1054b-255">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="1054b-256">I controlli server Web usano i convertitori di tipi per convertire le stringhe in oggetti.</span><span class="sxs-lookup"><span data-stu-id="1054b-256">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="1054b-257">Gli helper tag consentono di lavorare in modo nativo in C#, quindi la conversione dei tipi non è necessaria.</span><span class="sxs-lookup"><span data-stu-id="1054b-257">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="1054b-258">I controlli server Web usano [System.ComponentModel](/dotnet/api/system.componentmodel) per implementare il comportamento di componenti e controlli in fase di esecuzione e in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="1054b-258">Web Server controls use [System.ComponentModel](/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="1054b-259">`System.ComponentModel` include le classi e le interfacce di base per l'implementazione di attributi e convertitori, l'associazione a origini dati e le licenze per i componenti.</span><span class="sxs-lookup"><span data-stu-id="1054b-259">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="1054b-260">Gli helper tag invece derivano in genere da `TagHelper` e la classe di base `TagHelper` espone solo due metodi, `Process` e `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="1054b-260">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="1054b-261">Personalizzazione del tipo di carattere dell'elemento helper tag</span><span class="sxs-lookup"><span data-stu-id="1054b-261">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="1054b-262">È possibile personalizzare il tipo di carattere e la colorazione da **strumenti**  >  **Opzioni**  >  **ambiente**  >  **tipi di carattere e colori**:</span><span class="sxs-lookup"><span data-stu-id="1054b-262">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors**:</span></span>

![image](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a><span data-ttu-id="1054b-264">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1054b-264">Additional resources</span></span>

* [<span data-ttu-id="1054b-265">Creare helper tag</span><span class="sxs-lookup"><span data-stu-id="1054b-265">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="1054b-266">Uso dei moduli</span><span class="sxs-lookup"><span data-stu-id="1054b-266">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* <span data-ttu-id="1054b-267">[TagHelperSamples in GitHub](https://github.com/dpaquette/TagHelperSamples) contiene esempi di helper tag per l'uso di [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="1054b-267">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](https://getbootstrap.com/).</span></span>
