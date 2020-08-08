---
title: Configurare la localizzazione degli oggetti portabili in ASP.NET Core
author: sebastienros
description: Questo articolo presenta i file degli oggetti portabili e descrive i passaggi per l'uso dei file in un'applicazione ASP.NET Core con il framework Orchard Core.
ms.author: scaddie
ms.date: 09/26/2017
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 1c60f98ce08c5282986f688487339d378f28aaf7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016804"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="7276e-103">Configurare la localizzazione degli oggetti portabili in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7276e-103">Configure portable object localization in ASP.NET Core</span></span>

<span data-ttu-id="7276e-104">[Sébastien Ros](https://github.com/sebastienros) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="7276e-104">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="7276e-105">Questo articolo descrive i passaggi per l'uso dei file degli oggetti portabili (Portable Object, PO) in un'applicazione ASP.NET Core con il framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="7276e-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="7276e-106">**Nota:** Orchard Core non è un prodotto Microsoft.</span><span class="sxs-lookup"><span data-stu-id="7276e-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="7276e-107">Di conseguenza, Microsoft non fornisce alcun supporto per questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="7276e-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="7276e-108">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7276e-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="7276e-109">Che cos'è un file PO?</span><span class="sxs-lookup"><span data-stu-id="7276e-109">What is a PO file?</span></span>

<span data-ttu-id="7276e-110">I file PO sono distribuiti come file di testo contenenti le stringhe tradotte per una determinata lingua.</span><span class="sxs-lookup"><span data-stu-id="7276e-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="7276e-111">Alcuni dei vantaggi offerti dall'uso dei file PO al posto dei file *.resx* sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="7276e-111">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="7276e-112">Pluralizzazione del supporto dei file PO; i file *.resx* non supportano la pluralizzazione.</span><span class="sxs-lookup"><span data-stu-id="7276e-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="7276e-113">I file PO non vengono compilati come i file *.resx*.</span><span class="sxs-lookup"><span data-stu-id="7276e-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="7276e-114">Di conseguenza, non sono richiesti strumenti e passaggi di compilazione specifici.</span><span class="sxs-lookup"><span data-stu-id="7276e-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="7276e-115">I file PO funzionano perfettamente con gli strumenti di modifica online di collaborazione.</span><span class="sxs-lookup"><span data-stu-id="7276e-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="7276e-116">Esempio</span><span class="sxs-lookup"><span data-stu-id="7276e-116">Example</span></span>

<span data-ttu-id="7276e-117">Di seguito è riportato un file PO di esempio contenente le traduzioni di due stringhe in francese, inclusa la stringa con la forma plurale:</span><span class="sxs-lookup"><span data-stu-id="7276e-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="7276e-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="7276e-118">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="7276e-119">Questo esempio usa la sintassi seguente:</span><span class="sxs-lookup"><span data-stu-id="7276e-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="7276e-120">`#:`: commento che indica il contesto della stringa da tradurre.</span><span class="sxs-lookup"><span data-stu-id="7276e-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="7276e-121">La stessa stringa può essere tradotta in modo diverso a seconda della posizione in cui viene usata.</span><span class="sxs-lookup"><span data-stu-id="7276e-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="7276e-122">`msgid`: stringa non tradotta.</span><span class="sxs-lookup"><span data-stu-id="7276e-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="7276e-123">`msgstr`: stringa tradotta.</span><span class="sxs-lookup"><span data-stu-id="7276e-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="7276e-124">Se è supportata la pluralizzazione, è possibile definire più voci.</span><span class="sxs-lookup"><span data-stu-id="7276e-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="7276e-125">`msgid_plural`: stringa al plurale non tradotta.</span><span class="sxs-lookup"><span data-stu-id="7276e-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="7276e-126">`msgstr[0]`: stringa tradotta per il caso 0.</span><span class="sxs-lookup"><span data-stu-id="7276e-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="7276e-127">`msgstr[N]`: stringa tradotta per il caso N.</span><span class="sxs-lookup"><span data-stu-id="7276e-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="7276e-128">La specifica dei file PO è disponibile [qui](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="7276e-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="7276e-129">Configurazione del supporto dei file PO in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7276e-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="7276e-130">Questo esempio è basato su un'applicazione ASP.NET Core MVC generata da un modello di progetto Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="7276e-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="7276e-131">Riferimento al pacchetto</span><span class="sxs-lookup"><span data-stu-id="7276e-131">Referencing the package</span></span>

<span data-ttu-id="7276e-132">Aggiungere un riferimento al pacchetto NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="7276e-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="7276e-133">È disponibile in [MyGet](https://www.myget.org/) nell'origine pacchetto seguente: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="7276e-133">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="7276e-134">Il file *.csproj* contiene ora una riga simile alla seguente (il numero di versione può variare):</span><span class="sxs-lookup"><span data-stu-id="7276e-134">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="7276e-135">Registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="7276e-135">Registering the service</span></span>

<span data-ttu-id="7276e-136">Aggiungere i servizi necessari al metodo `ConfigureServices` di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7276e-136">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="7276e-137">Aggiungere il middleware necessario al metodo `Configure` di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7276e-137">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="7276e-138">Aggiungere il codice seguente alla propria Razor visualizzazione scelta.</span><span class="sxs-lookup"><span data-stu-id="7276e-138">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="7276e-139">In questo esempio viene usata *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7276e-139">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="7276e-140">Viene inserita un'istanza `IViewLocalizer` che viene usata per tradurre il testo "Hello world!".</span><span class="sxs-lookup"><span data-stu-id="7276e-140">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="7276e-141">Creazione di un file PO</span><span class="sxs-lookup"><span data-stu-id="7276e-141">Creating a PO file</span></span>

<span data-ttu-id="7276e-142">Creare un file denominato \* \<culture code> . po\* nella cartella radice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="7276e-142">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="7276e-143">In questo esempio il nome file è *fr.po* poiché viene usata la lingua francese:</span><span class="sxs-lookup"><span data-stu-id="7276e-143">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="7276e-144">Il file memorizza la stringa da tradurre e la stringa tradotta in francese.</span><span class="sxs-lookup"><span data-stu-id="7276e-144">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="7276e-145">Le traduzioni vengono ripristinate alla relativa impostazione cultura, se necessario.</span><span class="sxs-lookup"><span data-stu-id="7276e-145">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="7276e-146">In questo esempio viene usato il file *fr.po* se le impostazioni cultura richieste sono `fr-FR` o `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="7276e-146">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="7276e-147">Test dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="7276e-147">Testing the application</span></span>

<span data-ttu-id="7276e-148">Eseguire l'applicazione e passare all'URL `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="7276e-148">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="7276e-149">Il testo **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="7276e-149">The text **Hello world!**</span></span> <span data-ttu-id="7276e-150">vengono visualizzati i puntini di sospensione (...).</span><span class="sxs-lookup"><span data-stu-id="7276e-150">is displayed.</span></span>

<span data-ttu-id="7276e-151">Passare all'URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="7276e-151">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="7276e-152">Il testo **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="7276e-152">The text **Bonjour le monde!**</span></span> <span data-ttu-id="7276e-153">vengono visualizzati i puntini di sospensione (...).</span><span class="sxs-lookup"><span data-stu-id="7276e-153">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="7276e-154">Pluralizzazione</span><span class="sxs-lookup"><span data-stu-id="7276e-154">Pluralization</span></span>

<span data-ttu-id="7276e-155">I file PO supportano le forme di pluralizzazione, utili quando è necessario tradurre la stessa stringa in modo diverso in base a una cardinalità.</span><span class="sxs-lookup"><span data-stu-id="7276e-155">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="7276e-156">Questa attività risulta complessa poiché ogni lingua definisce regole personalizzate per la selezione della stringa da usare in base alla cardinalità.</span><span class="sxs-lookup"><span data-stu-id="7276e-156">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="7276e-157">Il pacchetto di localizzazione Orchard offre un'API per richiamare automaticamente le diverse forme plurali.</span><span class="sxs-lookup"><span data-stu-id="7276e-157">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="7276e-158">Creazione di file PO di pluralizzazione</span><span class="sxs-lookup"><span data-stu-id="7276e-158">Creating pluralization PO files</span></span>

<span data-ttu-id="7276e-159">Aggiungere il contenuto seguente al file *fr.po* indicato in precedenza:</span><span class="sxs-lookup"><span data-stu-id="7276e-159">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="7276e-160">Vedere [Che cos'è un file PO?](#what-is-a-po-file) per una descrizione di ogni voce di questo esempio.</span><span class="sxs-lookup"><span data-stu-id="7276e-160">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="7276e-161">Aggiunta di una lingua che usa forme di pluralizzazione diverse</span><span class="sxs-lookup"><span data-stu-id="7276e-161">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="7276e-162">Nell'esempio precedente sono state usate stringhe in inglese e in francese.</span><span class="sxs-lookup"><span data-stu-id="7276e-162">English and French strings were used in the previous example.</span></span> <span data-ttu-id="7276e-163">Inglese e francese hanno solo due forme di pluralizzazione e condividono le stesse regole di formato, ovvero una cardinalità viene mappata alla prima forma plurale.</span><span class="sxs-lookup"><span data-stu-id="7276e-163">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="7276e-164">Un'eventuale altra cardinalità viene mappata alla seconda forma plurale.</span><span class="sxs-lookup"><span data-stu-id="7276e-164">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="7276e-165">Non tutte le lingue condividono le stesse regole.</span><span class="sxs-lookup"><span data-stu-id="7276e-165">Not all languages share the same rules.</span></span> <span data-ttu-id="7276e-166">La lingua ceca, ad esempio, ha tre forme plurali.</span><span class="sxs-lookup"><span data-stu-id="7276e-166">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="7276e-167">Creare il file `cs.po` come descritto di seguito e notare che la pluralizzazione richiede tre traduzioni diverse:</span><span class="sxs-lookup"><span data-stu-id="7276e-167">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="7276e-168">Per accettare le localizzazioni ceche, aggiungere `"cs"` all'elenco delle impostazioni cultura supportate nel metodo `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7276e-168">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="7276e-169">Modificare il file *Views/Home/About.cshtml* per eseguire il rendering delle stringhe plurali localizzate per più cardinalità:</span><span class="sxs-lookup"><span data-stu-id="7276e-169">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="7276e-170">**Nota:** in uno scenario reale potrebbe essere usata una variabile per rappresentare il conteggio.</span><span class="sxs-lookup"><span data-stu-id="7276e-170">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="7276e-171">In questo caso, viene ripetuto lo stesso codice con tre valori diversi per esporre un caso molto specifico.</span><span class="sxs-lookup"><span data-stu-id="7276e-171">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="7276e-172">Per le diverse impostazioni cultura, si ottiene quanto segue:</span><span class="sxs-lookup"><span data-stu-id="7276e-172">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="7276e-173">Per `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="7276e-173">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="7276e-174">Per `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="7276e-174">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="7276e-175">Per `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="7276e-175">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="7276e-176">Si noti che per le impostazioni cultura per la lingua ceca, le tre traduzioni sono diverse.</span><span class="sxs-lookup"><span data-stu-id="7276e-176">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="7276e-177">Le impostazioni cultura per la lingua inglese e francese condividono la stessa costruzione per le ultime due stringhe tradotte.</span><span class="sxs-lookup"><span data-stu-id="7276e-177">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="7276e-178">Attività avanzate</span><span class="sxs-lookup"><span data-stu-id="7276e-178">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="7276e-179">Contestualizzazione delle stringhe</span><span class="sxs-lookup"><span data-stu-id="7276e-179">Contextualizing strings</span></span>

<span data-ttu-id="7276e-180">Le applicazioni spesso contengono le stringhe da tradurre in posizioni diverse.</span><span class="sxs-lookup"><span data-stu-id="7276e-180">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="7276e-181">La stessa stringa può avere una traduzione diversa in determinate posizioni all'interno di un'app ( Razor viste o file di classe).</span><span class="sxs-lookup"><span data-stu-id="7276e-181">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="7276e-182">Un file PO supporta la nozione di contesto del file, che è possibile usare per categorizzare la stringa rappresentata.</span><span class="sxs-lookup"><span data-stu-id="7276e-182">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="7276e-183">Usando un contesto di file è possibile tradurre una stringa in modo diverso a seconda del contesto o della mancanza del contesto.</span><span class="sxs-lookup"><span data-stu-id="7276e-183">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="7276e-184">I servizi di localizzazione PO usano il nome della classe completa o la visualizzazione usata durante la traduzione di una stringa.</span><span class="sxs-lookup"><span data-stu-id="7276e-184">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="7276e-185">Questa operazione viene eseguita impostando il valore nella voce `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="7276e-185">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="7276e-186">Si supponga di eseguire un'aggiunta minore all'esempio *fr.po* precedente.</span><span class="sxs-lookup"><span data-stu-id="7276e-186">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="7276e-187">Una Razor visualizzazione che si trova in *views/Home/About. cshtml* può essere definita come contesto del file impostando il `msgctxt` valore della voce riservata:</span><span class="sxs-lookup"><span data-stu-id="7276e-187">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="7276e-188">Con `msgctxt` impostato in questo modo, la traduzione del testo si verifica quando si passa a `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="7276e-188">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="7276e-189">La traduzione non si verifica quando si passa a `/Home/Contact?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="7276e-189">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="7276e-190">Quando nessuna voce corrisponde al contesto di file specificato, il meccanismo di fallback di Orchard Core cerca un file PO appropriato senza contesto.</span><span class="sxs-lookup"><span data-stu-id="7276e-190">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="7276e-191">Presupponendo che non sia stato definito alcun contesto di file specifico per *Views/Home/Contact.cshtml*, il passaggio a `/Home/Contact?culture=fr-FR` carica un file PO come:</span><span class="sxs-lookup"><span data-stu-id="7276e-191">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="7276e-192">Modifica della posizione dei file PO</span><span class="sxs-lookup"><span data-stu-id="7276e-192">Changing the location of PO files</span></span>

<span data-ttu-id="7276e-193">La posizione predefinita dei file PO può essere modificata in `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7276e-193">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="7276e-194">In questo esempio i file PO vengono caricati dalla cartella *Localization*.</span><span class="sxs-lookup"><span data-stu-id="7276e-194">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="7276e-195">Implementazione di una logica personalizzata per la ricerca dei file di localizzazione</span><span class="sxs-lookup"><span data-stu-id="7276e-195">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="7276e-196">Quando è necessaria una logica più complessa per individuare i file PO, l'interfaccia `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` può essere implementata e registrata come servizio.</span><span class="sxs-lookup"><span data-stu-id="7276e-196">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="7276e-197">Ciò è utile quando i file PO possono essere memorizzati in posizioni diverse o quando i file devono essere cercati all'interno di una gerarchia di cartelle.</span><span class="sxs-lookup"><span data-stu-id="7276e-197">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="7276e-198">Uso di una lingua pluralizzata predefinita diversa</span><span class="sxs-lookup"><span data-stu-id="7276e-198">Using a different default pluralized language</span></span>

<span data-ttu-id="7276e-199">Il pacchetto include un metodo di estensione `Plural` specifico di due forme plurali.</span><span class="sxs-lookup"><span data-stu-id="7276e-199">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="7276e-200">Per le lingue che richiedono ulteriori forme plurali, creare un metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="7276e-200">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="7276e-201">Con un metodo di estensione, non sarà necessario specificare un file di localizzazione per la lingua predefinita &mdash; le stringhe originali sono già disponibili direttamente nel codice.</span><span class="sxs-lookup"><span data-stu-id="7276e-201">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="7276e-202">È possibile usare l'overload `Plural(int count, string[] pluralForms, params object[] arguments)` più generico che accetta una matrice di stringhe di traduzioni.</span><span class="sxs-lookup"><span data-stu-id="7276e-202">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>
