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
# <a name="tag-helpers-in-aspnet-core"></a>Helper tag in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-are-tag-helpers"></a>Descrizione di helper tag

Gli helper tag consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file. Ad esempio, l'elemento predefinito `ImageTagHelper` può aggiungere un numero di versione al nome dell'immagine. Quando l'immagine viene modificata, il server genera una nuova versione univoca per l'immagine, in modo da garantire che i client ricevano sempre l'immagine corrente (anziché un'immagine non aggiornata memorizzata nella cache). Esistono molti helper tag predefiniti per le attività comuni, ad esempio la creazione di moduli e collegamenti, il caricamento di asset e così via, e altri ancora sono disponibili nei repository GitHub pubblici e come pacchetti NuGet. Gli helper tag vengono creati in C# e hanno come destinazione gli elementi HTML in base al nome di elemento, nome di attributo o tag padre. Ad esempio, l'elemento predefinito `LabelTagHelper` può avere come destinazione l'elemento HTML `<label>` quando vengono applicati gli attributi di `LabelTagHelper`. Se si ha familiarità con gli [helper HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), gli helper Tag riducono le transizioni esplicite tra HTML e C# nelle Razor visualizzazioni. In molti casi, gli helper HTML offrono un approccio alternativo a un helper tag specifico, ma è importante tenere presente che gli helper tag non sostituiscono gli helper HTML e che non esiste un helper tag per ogni helper HTML. Nella sezione [Helper tag e helper HTML a confronto](#tag-helpers-compared-to-html-helpers) vengono illustrate le differenze in modo più dettagliato.

## <a name="what-tag-helpers-provide"></a>Vantaggi degli helper tag

**Esperienza di sviluppo intuitiva** Nella maggior parte dei casi, il Razor markup che usa gli helper tag è simile a HTML standard. I progettisti front-end che hanno dimestichezza con HTML/CSS/JavaScript possono essere modificati Razor senza conoscere la Razor sintassi C#.

**Un ambiente IntelliSense avanzato per la creazione di HTML e Razor markup** è in contrasto acuto con gli helper HTML, l'approccio precedente alla creazione lato server del markup nelle Razor visualizzazioni. Nella sezione [Helper tag e helper HTML a confronto](#tag-helpers-compared-to-html-helpers) vengono illustrate le differenze in modo più dettagliato. Nella sezione [Supporto IntelliSense per gli helper tag](#intellisense-support-for-tag-helpers) viene illustrato l'ambiente IntelliSense. Anche gli sviluppatori esperti con la Razor sintassi c# sono più produttivi usando gli helper Tag rispetto alla scrittura di Razor markup c#.

**Possibilità di aumentare la produttività e la capacità di produrre codice più solido, affidabile e gestibile usando le informazioni disponibili solo nel server**: storicamente il mantra a proposito dell'aggiornamento delle immagini, ad esempio, era quello di cambiare il nome dell'immagine quando l'immagine veniva modificata. Per motivi di prestazioni, le immagini devono essere memorizzate nella cache e, a meno che il nome dell'immagine non venga cambiato, si rischia che i client ricevano una copia non aggiornata. Dopo che un'immagine era stata modificata, il nome doveva essere sempre cambiato e ogni riferimento all'immagine nell'app Web doveva essere aggiornato. Non solo questa operazione richiede molto lavoro, ma è anche soggetta a errori (è possibile perdere un riferimento, immettere accidentalmente la stringa sbagliata e così via). `ImageTagHelper`Questa operazione può essere eseguita automaticamente. `ImageTagHelper` è in grado di aggiungere un numero di versione al nome dell'immagine in modo che quando l'immagine viene modificata il server generi automaticamente una nuova versione univoca dell'immagine. I client riceveranno sicuramente l'immagine corrente. Grazie all'uso di `ImageTagHelper`, efficienza e risparmio di energie sono essenzialmente gratuiti.

La maggior parte degli helper tag predefiniti usano come destinazione elementi HTML standard e specificano attributi sul lato server per l'elemento. Ad esempio, l'elemento `<input>` usato in molte visualizzazioni nella cartella *Views/Account* (Visualizzazioni/Account) contiene l'attributo `asp-for`. Questo attributo consente di estrarre il nome della proprietà del modello specificato nel codice HTML visualizzabile. Si consideri una Razor vista con il modello seguente:

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

Il Razor markup seguente:

```cshtml
<label asp-for="Movie.Title"></label>
```

Viene generato il codice HTML seguente:

```html
<label for="Movie_Title">Title</label>
```

L'attributo `asp-for` viene reso disponibile dalla proprietà `For` in [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0). Per altre informazioni, vedere [Creare helper tag](xref:mvc/views/tag-helpers/authoring).

## <a name="managing-tag-helper-scope"></a>Gestione dell'ambito dell'helper tag

L'ambito degli helper tag è controllato da una combinazione di `@addTagHelper`, `@removeTagHelper` e del carattere di esclusione "!".

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a>`@addTagHelper` rende disponibili gli helper tag

Se si crea una nuova app Web ASP.NET Core denominata *AuthoringTagHelpers*, al progetto verrà aggiunto il file *Views/_ViewImports.cshtml* seguente:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

La direttiva `@addTagHelper` rende gli helper tag disponibili per la visualizzazione. In questo caso, il file della visualizzazione è *Pages/_ViewImports.cshtml* che per impostazione predefinita viene ereditato da tutti i file nella cartella *Pages* e nelle sottocartelle, rendendo disponibili gli helper tag. Il codice precedente usa la sintassi con caratteri jolly (" \* ") per specificare che tutti gli helper tag nell'assembly specificato (*Microsoft. AspNetCore. Mvc. TagHelpers*) saranno disponibili per ogni file di visualizzazione nella directory *views* o nella sottodirectory. Il primo parametro dopo `@addTagHelper` specifica gli helper tag da caricare (viene usato "\*" per tutti gli helper tag) e il secondo parametro "Microsoft.AspNetCore.Mvc.TagHelpers" specifica l'assembly contenente gli helper tag. *Microsoft.AspNetCore.Mvc.TagHelpers* è l'assembly per gli helper tag predefiniti di ASP.NET Core.

Per esporre tutti gli helper tag di questo progetto, che crea un assembly denominato *AuthoringTagHelpers*, usare il codice seguente:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

Se il progetto contiene un `EmailTagHelper` con lo spazio dei nomi predefinito (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), è possibile fornire il nome completo dell'helper tag:

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

Per aggiungere un helper tag a una visualizzazione usando un nome completo, aggiungere prima il nome completo (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) e quindi il nome dell'assembly (*AuthoringTagHelpers*). La maggior parte degli sviluppatori preferisce usare la sintassi con caratteri jolly "\*". La sintassi con caratteri jolly consente di inserire il carattere jolly "\*" come suffisso di un nome completo. Le direttive seguenti, ad esempio, aggiungeranno `EmailTagHelper`:

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

Come indicato in precedenza, l'aggiunta della `@addTagHelper` direttiva al file *views/_ViewImports. cshtml* rende l'helper tag disponibile a tutti i file di visualizzazione nella directory *views* e nelle sottodirectory. È possibile usare la direttiva `@addTagHelper` in file di visualizzazione specifici se si vuole acconsentire esplicitamente a esporre l'helper tag solo a queste visualizzazioni.

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a>`@removeTagHelper` rimuove gli helper tag

`@removeTagHelper` contiene gli stessi due parametri di `@addTagHelper` e rimuove un helper tag che era stato precedentemente aggiunto. Se ad esempio si applica `@removeTagHelper` a una visualizzazione specifica, l'helper tag specificato viene rimosso dalla visualizzazione. L'uso di `@removeTagHelper` in un file *Views/Folder/_ViewImports.cshtml* comporta la rimozione dell'helper tag specificato da tutte le visualizzazioni in *Folder*.

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a>Controllo dell'ambito dell'helper tag con il file *_ViewImports.cshtml*

È possibile aggiungere un file *_ViewImports.cshtml* a una cartella di visualizzazione qualsiasi e il motore di visualizzazione applicherà le direttive di questo file e del file *Views/_ViewImports.cshtml*. L'aggiunta di un file *Views/Home/_ViewImports.cshtml* vuoto per le visualizzazioni *Home* non comporta modifiche perché il file *_ViewImports.cshtml* è additivo. Le direttive `@addTagHelper` aggiunte al file *Views/Home/_ViewImports.cshtml* (non presenti nel file *Views/_ViewImports.cshtml* predefinito) espongono questi helper tag alle visualizzazioni solo nella cartella *Home*.

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a>Esclusione di singoli elementi

È possibile disabilitare un helper tag a livello di elemento con il carattere di esclusione ("!") dell'helper tag. La convalida `Email` viene ad esempio disabilitata in `<span>` con il carattere di esclusione dell'helper tag:

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

È necessario applicare il carattere di esclusione dell'helper tag ai tag di apertura e chiusura. L'editor di Visual Studio aggiunge automaticamente il carattere di esclusione al tag di chiusura se ne è stato aggiunto uno al tag di apertura. Dopo che il carattere di esclusione è stato aggiunto, l'elemento e gli attributi dell'helper tag non verranno più visualizzati in un tipo di carattere distintivo.

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a>Uso di `@tagHelperPrefix` per rendere esplicito l'uso dell'helper tag

La direttiva `@tagHelperPrefix` consente di specificare una stringa di prefisso del tag per abilitare il supporto dell'helper tag e renderne esplicito l'uso. È possibile, ad esempio, aggiungere il markup seguente al file *Views/_ViewImports.cshtml*:

```cshtml
@tagHelperPrefix th:
```

Nell'immagine di codice seguente, il prefisso dell'helper tag è impostato su `th:`. Pertanto, solo gli elementi che usano il prefisso `th:` supportano gli helper tag. Gli elementi abilitati per gli helper tag hanno un tipo di carattere distintivo. Gli elementi `<label>` e `<input>` includono il prefisso dell'helper tag e sono abilitati per gli helper tag, mentre l'elemento `<span>` non lo è.

![image](intro/_static/thp.png)

Le stesse regole di gerarchia che si applicano a `@addTagHelper` sono valide anche per `@tagHelperPrefix`.

## <a name="self-closing-tag-helpers"></a>Helper tag di chiusura automatico

Molti helper tag non possono essere usati come tag di chiusura automatici. Alcuni helper tag sono progettati per essere tag di chiusura automatici. Se si usa un helper tag non progettato per la chiusura automatica, l'output sottoposto a rendering viene eliminato. La chiusura automatica di un helper tag genera un tag di chiusura automatico nell'output sottoposto a rendering. Per altre informazioni, vedere [questa nota](xref:mvc/views/tag-helpers/authoring#self-closing) in [Creare helper tag in ASP.NET Core](xref:mvc/views/tag-helpers/authoring).

## <a name="c-in-tag-helpers-attributedeclaration"></a>C# nell'attributo/dichiarazione degli helper Tag 

Gli helper tag non consentono C# nell'area di dichiarazione dell'attributo o tag dell'elemento. Ad esempio, il codice seguente non è valido:

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

Il codice precedente può essere scritto come segue:

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a>Supporto IntelliSense per gli helper tag

Quando si crea un nuovo ASP.NET Core app Web in Visual Studio, viene aggiunto il pacchetto NuGet " Razor Microsoft. AspNetCore. Strumenti ". ovvero il pacchetto che aggiunge gli strumenti dell'helper tag.

Si supponga di scrivere un elemento `<label>` HTML. Non appena si inizia a digitare `<l` nell'editor di Visual Studio, IntelliSense visualizza gli elementi corrispondenti:

![image](intro/_static/label.png)

Oltre alla guida HTML verrà visualizzata anche l'icona (il "@" symbol with "<>" sotto di essa).

![image](intro/_static/tagSym.png)

che identifica l'elemento come destinazione degli helper tag. Gli elementi HTML puri (ad esempio, `fieldset`) visualizzano l'icona "<>".

Un tag `<label>` HTML puro visualizza il tag HTML (con il tema colori di Visual Studio predefinito) con un tipo di carattere marrone, gli attributi in rosso e i valori degli attributi in blu.

![image](intro/_static/LableHtmlTag.png)

Dopo avere immesso `<label`, IntelliSense elenca gli attributi HTML/CSS disponibili e gli attributi di destinazione dell'helper tag:

![image](intro/_static/labelattr.png)

Il completamento istruzioni di IntelliSense consente di usare il tasto TAB per completare l'istruzione con il valore selezionato:

![image](intro/_static/stmtcomplete.png)

Non appena un attributo dell'helper tag viene immesso, i tipi di carattere del tag e dell'attributo cambiano. Con il tema colori predefinito "Blu" o "Chiaro" di Visual Studio, il tipo di carattere è in grassetto viola. Se si usa il tema colori "Scuro", il tipo di carattere è in grassetto verde acqua. Nelle immagini acquisite per questo documento è stato usato il tema predefinito.

![image](intro/_static/labelaspfor2.png)

È possibile usare il tasto di scelta rapida *Completa parola* di Visual Studio (CTRL+BARRA SPAZIATRICE è la combinazione [predefinita](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) all'interno delle virgolette doppie ("") per passare a C#, proprio come se ci si trovasse in una classe C#. IntelliSense visualizza tutti i metodi e le proprietà nel modello di pagina. I metodi e le proprietà sono disponibili perché il tipo di proprietà è `ModelExpression`. Nell'immagine seguente viene modificata la visualizzazione `Register`, quindi è disponibile il modello `RegisterViewModel`.

![image](intro/_static/intellemail.png)

IntelliSense elenca le proprietà e i metodi disponibili per il modello nella pagina. L'ambiente avanzato di IntelliSense consente di selezionare la classe CSS:

![image](intro/_static/iclass.png)

![image](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a>Helper tag e helper HTML a confronto

Gli helper tag si connettono agli elementi HTML nelle Razor viste, mentre gli [helper HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) vengono richiamati come metodi intercalati con HTML nelle Razor visualizzazioni. Si consideri il Razor markup seguente che crea un'etichetta HTML con la classe CSS "Caption":

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

Il simbolo at ( `@` ) indica che Razor si tratta dell'inizio del codice. I due parametri successivi ("FirstName" e "First Name:") sono stringhe, quindi [IntelliSense](/visualstudio/ide/using-intellisense) non risulta utile. L'ultimo argomento:

```cshtml
new {@class="caption"}
```

è un oggetto anonimo usato per rappresentare gli attributi. Poiché `class` è una parola chiave riservata in C#, usare il simbolo `@` per imporre a C# di interpretare `@class=` come un simbolo (nome di proprietà). A una finestra di progettazione front-end (qualcuno che conosce HTML/CSS/JavaScript e altre tecnologie client ma non ha familiarità con C# e Razor ), la maggior parte della linea è straniera. L'intera riga deve essere creata senza alcun aiuto da parte di IntelliSense.

Usando `LabelTagHelper`, è possibile scrivere lo stesso markup nel modo seguente:

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

Con la versione helper tag, non appena si digita `<l` nell'editor di Visual Studio, IntelliSense visualizza gli elementi corrispondenti:

![image](intro/_static/label.png)

IntelliSense aiuta a scrivere l'intera riga.

Nell'immagine di codice seguente viene illustrata la parte del form della vista *views/account/Register. cshtml* Razor generata dal modello MVC ASP.NET 4.5. x incluso in Visual Studio.

![image](intro/_static/regCS.png)

Nell'editor di Visual Studio il codice C# viene visualizzato con uno sfondo grigio. Ad esempio, l'helper HTML `AntiForgeryToken`:

```cshtml
@Html.AntiForgeryToken()
```

viene visualizzato con uno sfondo grigio. La maggior parte del markup nella visualizzazione Register è C#. Confrontarlo con l'approccio equivalente usando gli helper tag:

![image](intro/_static/regTH.png)

Rispetto all'approccio con gli helper HTML, il markup è molto più chiaro e facile da leggere, modificare e gestire. Il codice C# è ridotto al numero minimo di elementi che il server deve conoscere. Nell'editor di Visual Studio il markup di destinazione di un helper tag viene visualizzato in un tipo di carattere distintivo.

Si osservi il gruppo *Email*:

[!code-csharp[](intro/sample/Register.cshtml?range=12-18)]

Ogni attributo "asp-" ha il valore "Email", ma "Email" non è una stringa. In questo contesto "Email" è la proprietà di espressione del modello C# per `RegisterViewModel`.

L'editor di Visual Studio agevola la scrittura di **tutto** il markup nell'approccio con l'helper tag del modulo di registrazione, mentre Visual Studio non offre alcun aiuto per la maggior parte del codice nell'approccio con gli helper HTML. Nella sezione [Supporto IntelliSense per gli helper tag](#intellisense-support-for-tag-helpers) viene illustrato in modo dettagliato l'uso degli helper tag nell'editor di Visual Studio.

## <a name="tag-helpers-compared-to-web-server-controls"></a>Helper tag e controlli server Web a confronto

* Gli helper tag non sono i proprietari dell'elemento a cui sono associati, ma partecipano semplicemente al rendering dell'elemento e del contenuto. I [controlli server Web](https://msdn.microsoft.com/library/7698y1f0.aspx) di ASP.NET vengono dichiarati e richiamati in una pagina.

* I [controlli server Web](https://msdn.microsoft.com/library/zsyt68f1.aspx) hanno un ciclo di vita non semplice che può rendere difficili le attività di sviluppo e debug.

* I controlli server Web consentono di aggiungere funzionalità agli elementi DOM (Document Object Model) del client tramite un controllo client. Gli helper tag non includono DOM.

* I controlli server Web includono il rilevamento automatico del browser. Gli helper tag non hanno conoscenza del browser.

* Più helper tag possono agire sullo stesso elemento, vedere [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) (Evitare i conflitti degli helper tag), mentre non è possibile in genere comporre i controlli server Web.

* Gli helper tag possono modificare il tag e il contenuto degli elementi HTML che rientrano nel proprio ambito, ma non modificano altro in modo diretto in una pagina. I controlli server Web hanno un ambito meno specifico e possono eseguire azioni che influiscono su altre parti della pagina, provocando effetti collaterali imprevisti.

* I controlli server Web usano i convertitori di tipi per convertire le stringhe in oggetti. Gli helper tag consentono di lavorare in modo nativo in C#, quindi la conversione dei tipi non è necessaria.

* I controlli server Web usano [System.ComponentModel](/dotnet/api/system.componentmodel) per implementare il comportamento di componenti e controlli in fase di esecuzione e in fase di progettazione. `System.ComponentModel` include le classi e le interfacce di base per l'implementazione di attributi e convertitori, l'associazione a origini dati e le licenze per i componenti. Gli helper tag invece derivano in genere da `TagHelper` e la classe di base `TagHelper` espone solo due metodi, `Process` e `ProcessAsync`.

## <a name="customizing-the-tag-helper-element-font"></a>Personalizzazione del tipo di carattere dell'elemento helper tag

È possibile personalizzare il tipo di carattere e la colorazione da **strumenti**  >  **Opzioni**  >  **ambiente**  >  **tipi di carattere e colori**:

![image](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Creare helper tag](xref:mvc/views/tag-helpers/authoring)
* [Uso dei moduli](xref:mvc/views/working-with-forms)
* [TagHelperSamples in GitHub](https://github.com/dpaquette/TagHelperSamples) contiene esempi di helper tag per l'uso di [Bootstrap](https://getbootstrap.com/).
