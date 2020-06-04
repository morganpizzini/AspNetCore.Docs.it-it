---
<span data-ttu-id="761b1-101">title: configurare la localizzazione di oggetti portabili in ASP.NET Core Author: sebastienros Description: questo articolo introduce i file oggetto portabili e descrive i passaggi per usarli in un'applicazione ASP.NET Core con il Framework Orchard core.</span><span class="sxs-lookup"><span data-stu-id="761b1-101">title: Configure portable object localization in ASP.NET Core author: sebastienros description: This article introduces Portable Object files and outlines steps for using them in an ASP.NET Core application with the Orchard Core framework.</span></span>
<span data-ttu-id="761b1-102">ms. Author: scaddie ms. Date: 09/26/2017 no-loc:</span><span class="sxs-lookup"><span data-stu-id="761b1-102">ms.author: scaddie ms.date: 09/26/2017 no-loc:</span></span>
- <span data-ttu-id="761b1-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="761b1-103">'Blazor'</span></span>
- <span data-ttu-id="761b1-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="761b1-104">'Identity'</span></span>
- <span data-ttu-id="761b1-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="761b1-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="761b1-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="761b1-106">'Razor'</span></span>
- <span data-ttu-id="761b1-107">' SignalR ' UID: Nozioni fondamentali/portabile-oggetto-localizzazione</span><span class="sxs-lookup"><span data-stu-id="761b1-107">'SignalR' uid: fundamentals/portable-object-localization</span></span>

---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="761b1-108">Configurare la localizzazione degli oggetti portabili in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="761b1-108">Configure portable object localization in ASP.NET Core</span></span>

<span data-ttu-id="761b1-109">[Sébastien Ros](https://github.com/sebastienros) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="761b1-109">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="761b1-110">Questo articolo descrive i passaggi per l'uso dei file degli oggetti portabili (Portable Object, PO) in un'applicazione ASP.NET Core con il framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="761b1-110">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="761b1-111">**Nota:** Orchard Core non è un prodotto Microsoft.</span><span class="sxs-lookup"><span data-stu-id="761b1-111">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="761b1-112">Di conseguenza, Microsoft non fornisce alcun supporto per questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="761b1-112">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="761b1-113">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="761b1-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="761b1-114">Che cos'è un file PO?</span><span class="sxs-lookup"><span data-stu-id="761b1-114">What is a PO file?</span></span>

<span data-ttu-id="761b1-115">I file PO sono distribuiti come file di testo contenenti le stringhe tradotte per una determinata lingua.</span><span class="sxs-lookup"><span data-stu-id="761b1-115">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="761b1-116">Alcuni dei vantaggi offerti dall'uso dei file PO al posto dei file *.resx* sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="761b1-116">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="761b1-117">Pluralizzazione del supporto dei file PO; i file *.resx* non supportano la pluralizzazione.</span><span class="sxs-lookup"><span data-stu-id="761b1-117">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="761b1-118">I file PO non vengono compilati come i file *.resx*.</span><span class="sxs-lookup"><span data-stu-id="761b1-118">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="761b1-119">Di conseguenza, non sono richiesti strumenti e passaggi di compilazione specifici.</span><span class="sxs-lookup"><span data-stu-id="761b1-119">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="761b1-120">I file PO funzionano perfettamente con gli strumenti di modifica online di collaborazione.</span><span class="sxs-lookup"><span data-stu-id="761b1-120">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="761b1-121">Esempio</span><span class="sxs-lookup"><span data-stu-id="761b1-121">Example</span></span>

<span data-ttu-id="761b1-122">Di seguito è riportato un file PO di esempio contenente le traduzioni di due stringhe in francese, inclusa la stringa con la forma plurale:</span><span class="sxs-lookup"><span data-stu-id="761b1-122">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="761b1-123">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="761b1-123">*fr.po*</span></span>

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

<span data-ttu-id="761b1-124">Questo esempio usa la sintassi seguente:</span><span class="sxs-lookup"><span data-stu-id="761b1-124">This example uses the following syntax:</span></span>

- <span data-ttu-id="761b1-125">`#:`: commento che indica il contesto della stringa da tradurre.</span><span class="sxs-lookup"><span data-stu-id="761b1-125">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="761b1-126">La stessa stringa può essere tradotta in modo diverso a seconda della posizione in cui viene usata.</span><span class="sxs-lookup"><span data-stu-id="761b1-126">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="761b1-127">`msgid`: stringa non tradotta.</span><span class="sxs-lookup"><span data-stu-id="761b1-127">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="761b1-128">`msgstr`: stringa tradotta.</span><span class="sxs-lookup"><span data-stu-id="761b1-128">`msgstr`: The translated string.</span></span>

<span data-ttu-id="761b1-129">Se è supportata la pluralizzazione, è possibile definire più voci.</span><span class="sxs-lookup"><span data-stu-id="761b1-129">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="761b1-130">`msgid_plural`: stringa al plurale non tradotta.</span><span class="sxs-lookup"><span data-stu-id="761b1-130">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="761b1-131">`msgstr[0]`: stringa tradotta per il caso 0.</span><span class="sxs-lookup"><span data-stu-id="761b1-131">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="761b1-132">`msgstr[N]`: stringa tradotta per il caso N.</span><span class="sxs-lookup"><span data-stu-id="761b1-132">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="761b1-133">La specifica dei file PO è disponibile [qui](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="761b1-133">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="761b1-134">Configurazione del supporto dei file PO in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="761b1-134">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="761b1-135">Questo esempio è basato su un'applicazione ASP.NET Core MVC generata da un modello di progetto Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="761b1-135">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="761b1-136">Riferimento al pacchetto</span><span class="sxs-lookup"><span data-stu-id="761b1-136">Referencing the package</span></span>

<span data-ttu-id="761b1-137">Aggiungere un riferimento al pacchetto NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="761b1-137">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="761b1-138">È disponibile in [MyGet](https://www.myget.org/) nell'origine pacchetto seguente: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="761b1-138">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="761b1-139">Il file *.csproj* contiene ora una riga simile alla seguente (il numero di versione può variare):</span><span class="sxs-lookup"><span data-stu-id="761b1-139">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="761b1-140">Registrazione del servizio</span><span class="sxs-lookup"><span data-stu-id="761b1-140">Registering the service</span></span>

<span data-ttu-id="761b1-141">Aggiungere i servizi necessari al metodo `ConfigureServices` di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="761b1-141">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="761b1-142">Aggiungere il middleware necessario al metodo `Configure` di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="761b1-142">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="761b1-143">Aggiungere il codice seguente alla propria Razor visualizzazione scelta.</span><span class="sxs-lookup"><span data-stu-id="761b1-143">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="761b1-144">In questo esempio viene usata *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="761b1-144">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="761b1-145">Viene inserita un'istanza `IViewLocalizer` che viene usata per tradurre il testo "Hello world!".</span><span class="sxs-lookup"><span data-stu-id="761b1-145">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="761b1-146">Creazione di un file PO</span><span class="sxs-lookup"><span data-stu-id="761b1-146">Creating a PO file</span></span>

<span data-ttu-id="761b1-147">Creare un file denominato \* \<culture code> . po\* nella cartella radice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="761b1-147">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="761b1-148">In questo esempio il nome file è *fr.po* poiché viene usata la lingua francese:</span><span class="sxs-lookup"><span data-stu-id="761b1-148">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="761b1-149">Il file memorizza la stringa da tradurre e la stringa tradotta in francese.</span><span class="sxs-lookup"><span data-stu-id="761b1-149">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="761b1-150">Le traduzioni vengono ripristinate alla relativa impostazione cultura, se necessario.</span><span class="sxs-lookup"><span data-stu-id="761b1-150">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="761b1-151">In questo esempio viene usato il file *fr.po* se le impostazioni cultura richieste sono `fr-FR` o `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="761b1-151">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="761b1-152">Test dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="761b1-152">Testing the application</span></span>

<span data-ttu-id="761b1-153">Eseguire l'applicazione e passare all'URL `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="761b1-153">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="761b1-154">Il testo **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="761b1-154">The text **Hello world!**</span></span> <span data-ttu-id="761b1-155">vengono visualizzati i puntini di sospensione (...).</span><span class="sxs-lookup"><span data-stu-id="761b1-155">is displayed.</span></span>

<span data-ttu-id="761b1-156">Passare all'URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="761b1-156">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="761b1-157">Il testo **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="761b1-157">The text **Bonjour le monde!**</span></span> <span data-ttu-id="761b1-158">vengono visualizzati i puntini di sospensione (...).</span><span class="sxs-lookup"><span data-stu-id="761b1-158">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="761b1-159">Pluralizzazione</span><span class="sxs-lookup"><span data-stu-id="761b1-159">Pluralization</span></span>

<span data-ttu-id="761b1-160">I file PO supportano le forme di pluralizzazione, utili quando è necessario tradurre la stessa stringa in modo diverso in base a una cardinalità.</span><span class="sxs-lookup"><span data-stu-id="761b1-160">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="761b1-161">Questa attività risulta complessa poiché ogni lingua definisce regole personalizzate per la selezione della stringa da usare in base alla cardinalità.</span><span class="sxs-lookup"><span data-stu-id="761b1-161">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="761b1-162">Il pacchetto di localizzazione Orchard offre un'API per richiamare automaticamente le diverse forme plurali.</span><span class="sxs-lookup"><span data-stu-id="761b1-162">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="761b1-163">Creazione di file PO di pluralizzazione</span><span class="sxs-lookup"><span data-stu-id="761b1-163">Creating pluralization PO files</span></span>

<span data-ttu-id="761b1-164">Aggiungere il contenuto seguente al file *fr.po* indicato in precedenza:</span><span class="sxs-lookup"><span data-stu-id="761b1-164">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="761b1-165">Vedere [Che cos'è un file PO?](#what-is-a-po-file) per una descrizione di ogni voce di questo esempio.</span><span class="sxs-lookup"><span data-stu-id="761b1-165">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="761b1-166">Aggiunta di una lingua che usa forme di pluralizzazione diverse</span><span class="sxs-lookup"><span data-stu-id="761b1-166">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="761b1-167">Nell'esempio precedente sono state usate stringhe in inglese e in francese.</span><span class="sxs-lookup"><span data-stu-id="761b1-167">English and French strings were used in the previous example.</span></span> <span data-ttu-id="761b1-168">Inglese e francese hanno solo due forme di pluralizzazione e condividono le stesse regole di formato, ovvero una cardinalità viene mappata alla prima forma plurale.</span><span class="sxs-lookup"><span data-stu-id="761b1-168">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="761b1-169">Un'eventuale altra cardinalità viene mappata alla seconda forma plurale.</span><span class="sxs-lookup"><span data-stu-id="761b1-169">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="761b1-170">Non tutte le lingue condividono le stesse regole.</span><span class="sxs-lookup"><span data-stu-id="761b1-170">Not all languages share the same rules.</span></span> <span data-ttu-id="761b1-171">La lingua ceca, ad esempio, ha tre forme plurali.</span><span class="sxs-lookup"><span data-stu-id="761b1-171">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="761b1-172">Creare il file `cs.po` come descritto di seguito e notare che la pluralizzazione richiede tre traduzioni diverse:</span><span class="sxs-lookup"><span data-stu-id="761b1-172">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="761b1-173">Per accettare le localizzazioni ceche, aggiungere `"cs"` all'elenco delle impostazioni cultura supportate nel metodo `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="761b1-173">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="761b1-174">Modificare il file *Views/Home/About.cshtml* per eseguire il rendering delle stringhe plurali localizzate per più cardinalità:</span><span class="sxs-lookup"><span data-stu-id="761b1-174">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="761b1-175">**Nota:** in uno scenario reale potrebbe essere usata una variabile per rappresentare il conteggio.</span><span class="sxs-lookup"><span data-stu-id="761b1-175">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="761b1-176">In questo caso, viene ripetuto lo stesso codice con tre valori diversi per esporre un caso molto specifico.</span><span class="sxs-lookup"><span data-stu-id="761b1-176">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="761b1-177">Per le diverse impostazioni cultura, si ottiene quanto segue:</span><span class="sxs-lookup"><span data-stu-id="761b1-177">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="761b1-178">Per `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="761b1-178">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="761b1-179">Per `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="761b1-179">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="761b1-180">Per `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="761b1-180">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="761b1-181">Si noti che per le impostazioni cultura per la lingua ceca, le tre traduzioni sono diverse.</span><span class="sxs-lookup"><span data-stu-id="761b1-181">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="761b1-182">Le impostazioni cultura per la lingua inglese e francese condividono la stessa costruzione per le ultime due stringhe tradotte.</span><span class="sxs-lookup"><span data-stu-id="761b1-182">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="761b1-183">Attività avanzate</span><span class="sxs-lookup"><span data-stu-id="761b1-183">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="761b1-184">Contestualizzazione delle stringhe</span><span class="sxs-lookup"><span data-stu-id="761b1-184">Contextualizing strings</span></span>

<span data-ttu-id="761b1-185">Le applicazioni spesso contengono le stringhe da tradurre in posizioni diverse.</span><span class="sxs-lookup"><span data-stu-id="761b1-185">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="761b1-186">La stessa stringa può avere una traduzione diversa in determinate posizioni all'interno di un'app ( Razor viste o file di classe).</span><span class="sxs-lookup"><span data-stu-id="761b1-186">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="761b1-187">Un file PO supporta la nozione di contesto del file, che è possibile usare per categorizzare la stringa rappresentata.</span><span class="sxs-lookup"><span data-stu-id="761b1-187">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="761b1-188">Usando un contesto di file è possibile tradurre una stringa in modo diverso a seconda del contesto o della mancanza del contesto.</span><span class="sxs-lookup"><span data-stu-id="761b1-188">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="761b1-189">I servizi di localizzazione PO usano il nome della classe completa o la visualizzazione usata durante la traduzione di una stringa.</span><span class="sxs-lookup"><span data-stu-id="761b1-189">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="761b1-190">Questa operazione viene eseguita impostando il valore nella voce `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="761b1-190">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="761b1-191">Si supponga di eseguire un'aggiunta minore all'esempio *fr.po* precedente.</span><span class="sxs-lookup"><span data-stu-id="761b1-191">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="761b1-192">Una Razor visualizzazione che si trova in *views/Home/About. cshtml* può essere definita come contesto del file impostando il `msgctxt` valore della voce riservata:</span><span class="sxs-lookup"><span data-stu-id="761b1-192">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="761b1-193">Con `msgctxt` impostato in questo modo, la traduzione del testo si verifica quando si passa a `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="761b1-193">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="761b1-194">La traduzione non si verifica quando si passa a `/Home/Contact?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="761b1-194">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="761b1-195">Quando nessuna voce corrisponde al contesto di file specificato, il meccanismo di fallback di Orchard Core cerca un file PO appropriato senza contesto.</span><span class="sxs-lookup"><span data-stu-id="761b1-195">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="761b1-196">Presupponendo che non sia stato definito alcun contesto di file specifico per *Views/Home/Contact.cshtml*, il passaggio a `/Home/Contact?culture=fr-FR` carica un file PO come:</span><span class="sxs-lookup"><span data-stu-id="761b1-196">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="761b1-197">Modifica della posizione dei file PO</span><span class="sxs-lookup"><span data-stu-id="761b1-197">Changing the location of PO files</span></span>

<span data-ttu-id="761b1-198">La posizione predefinita dei file PO può essere modificata in `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="761b1-198">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="761b1-199">In questo esempio i file PO vengono caricati dalla cartella *Localization*.</span><span class="sxs-lookup"><span data-stu-id="761b1-199">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="761b1-200">Implementazione di una logica personalizzata per la ricerca dei file di localizzazione</span><span class="sxs-lookup"><span data-stu-id="761b1-200">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="761b1-201">Quando è necessaria una logica più complessa per individuare i file PO, l'interfaccia `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` può essere implementata e registrata come servizio.</span><span class="sxs-lookup"><span data-stu-id="761b1-201">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="761b1-202">Ciò è utile quando i file PO possono essere memorizzati in posizioni diverse o quando i file devono essere cercati all'interno di una gerarchia di cartelle.</span><span class="sxs-lookup"><span data-stu-id="761b1-202">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="761b1-203">Uso di una lingua pluralizzata predefinita diversa</span><span class="sxs-lookup"><span data-stu-id="761b1-203">Using a different default pluralized language</span></span>

<span data-ttu-id="761b1-204">Il pacchetto include un metodo di estensione `Plural` specifico di due forme plurali.</span><span class="sxs-lookup"><span data-stu-id="761b1-204">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="761b1-205">Per le lingue che richiedono ulteriori forme plurali, creare un metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="761b1-205">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="761b1-206">Con un metodo di estensione, non sarà necessario specificare un file di localizzazione per la lingua predefinita &mdash; le stringhe originali sono già disponibili direttamente nel codice.</span><span class="sxs-lookup"><span data-stu-id="761b1-206">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="761b1-207">È possibile usare l'overload `Plural(int count, string[] pluralForms, params object[] arguments)` più generico che accetta una matrice di stringhe di traduzioni.</span><span class="sxs-lookup"><span data-stu-id="761b1-207">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>
