---
title: informazioni di riferimento sulla sintassi Razor per ASP.NET Core
author: rick-anderson
description: Informazioni sulla Razor sintassi di markup per l'incorporamento di codice basato su server in pagine Web.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: 2831fd2edd029043e9457cd213e32f1a82c2872e
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424415"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="ba2ab-103">Guida di riferimento della sintassi Razor per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ba2ab-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="ba2ab-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)e [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="ba2ab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="ba2ab-105">Razor è una sintassi di markup per l'incorporamento di codice basato su server in pagine Web.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="ba2ab-106">La sintassi Razor è costituita da markup Razor, C# e HTML.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="ba2ab-107">I file contenenti Razor in genere presentano l'estensione *cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="ba2ab-108">Razor è presente anche nei file dei [componenti di Razor](xref:blazor/components) (con estensione *razor*).</span><span class="sxs-lookup"><span data-stu-id="ba2ab-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="ba2ab-109">Rendering di HTML</span><span class="sxs-lookup"><span data-stu-id="ba2ab-109">Rendering HTML</span></span>

<span data-ttu-id="ba2ab-110">Il linguaggio Razor predefinito è HTML.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-110">The default Razor language is HTML.</span></span> <span data-ttu-id="ba2ab-111">Il rendering HTML dal markup Razor non è diverso dal rendering HTML da un file HTML.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="ba2ab-112">Il rendering del markup HTML nei file Razor con estensione *cshtml* viene eseguito dal server senza modifiche.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="ba2ab-113">Sintassi Razor</span><span class="sxs-lookup"><span data-stu-id="ba2ab-113">Razor syntax</span></span>

<span data-ttu-id="ba2ab-114">Razor supporta C# e usa il simbolo `@` per la transizione da HTML a C#.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="ba2ab-115">Razor valuta le espressioni C# e ne esegue il rendering nell'output HTML.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="ba2ab-116">Quando un simbolo `@` è seguito da una [parola chiave riservata Razor](#razor-reserved-keywords), la transizione avviene in un markup specifico per Razor,</span><span class="sxs-lookup"><span data-stu-id="ba2ab-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="ba2ab-117">in caso contrario in C# semplice.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="ba2ab-118">Per usare un simbolo `@` come carattere di escape nel markup Razor, usare un secondo simbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="ba2ab-119">Il rendering del codice viene eseguito in HTML con un solo simbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="ba2ab-120">Gli attributi e il contenuto HTML contenenti indirizzi di posta elettronica non considerano il simbolo `@` come un carattere di transizione.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="ba2ab-121">Gli indirizzi di posta elettronica nell'esempio seguente non vengono modificati dall'analisi Razor:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="ba2ab-122">Espressioni implicite Razor</span><span class="sxs-lookup"><span data-stu-id="ba2ab-122">Implicit Razor expressions</span></span>

<span data-ttu-id="ba2ab-123">Le espressioni implicite Razor iniziano con `@` seguito dal codice C#:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="ba2ab-124">Fatta eccezione per la parola chiave `await` di C#, le espressioni implicite non devono contenere spazi.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="ba2ab-125">Se l'istruzione C# ha una fine chiaramente definita, possono coesistere spazi:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="ba2ab-126">Le espressioni implicite **non possono** contenere generics C#, poiché i caratteri all'interno delle parentesi (`<>`) vengono interpretati come un tag HTML.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="ba2ab-127">Il codice seguente **non** è valido:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="ba2ab-128">Il codice precedente genera un errore del compilatore simile a uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="ba2ab-129">L'elemento "int" non è stato chiuso.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="ba2ab-130">Tutti gli elementi devono essere a chiusura automatica o avere un tag di fine corrispondente.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="ba2ab-131">Non è possibile convertire il gruppo di metodi 'GenericMethod' nel tipo non delegato 'object'.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="ba2ab-132">Si intendeva chiamare il metodo?'</span><span class="sxs-lookup"><span data-stu-id="ba2ab-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="ba2ab-133">Le chiamate di metodo generiche devono essere racchiuse in un'[espressione esplicita Razor](#explicit-razor-expressions) o in un [blocco di codice Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="ba2ab-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="ba2ab-134">Espressioni esplicite Razor</span><span class="sxs-lookup"><span data-stu-id="ba2ab-134">Explicit Razor expressions</span></span>

<span data-ttu-id="ba2ab-135">Le espressioni esplicite Razor sono costituite da un simbolo `@` con parentesi bilanciate.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="ba2ab-136">Per eseguire il rendering dell'ora dell'ultima settimana, viene usato il markup Razor seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="ba2ab-137">Qualsiasi contenuto all'interno delle parentesi `@()` viene valutato e sottoposto a rendering nell'output.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="ba2ab-138">Le espressioni implicite, descritte nella sezione precedente, in genere non possono contenere spazi.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="ba2ab-139">Nel codice seguente, una settimana non viene sottratta dall'ora corrente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="ba2ab-140">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="ba2ab-141">È possibile usare le espressioni esplicite per concatenare testo con un risultato dell'espressione:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="ba2ab-142">Senza l'espressione esplicita, `<p>Age@joe.Age</p>` viene considerato come un indirizzo di posta elettronica e `<p>Age@joe.Age</p>` viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="ba2ab-143">Se viene scritto come espressione esplicita, `<p>Age33</p>` viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="ba2ab-144">È possibile usare le espressioni esplicite per eseguire il rendering dell'output da metodi generici in file con estensione *cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="ba2ab-145">Il markup seguente illustra come correggere l'errore riportato in precedenza, causato dalle parentesi quadre di un oggetto generico C#.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="ba2ab-146">Il codice viene scritto come un'espressione esplicita:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="ba2ab-147">Codifica di espressioni</span><span class="sxs-lookup"><span data-stu-id="ba2ab-147">Expression encoding</span></span>

<span data-ttu-id="ba2ab-148">Le espressioni C# che restituiscono una stringa sono codificate in HTML.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="ba2ab-149">Le espressioni C# che restituiscono `IHtmlContent` vengono sottoposte a rendering direttamente tramite `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="ba2ab-150">Le espressioni C# che non restituiscono `IHtmlContent` vengono convertite in una stringa da `ToString` e codificate prima di essere sottoposte a rendering.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="ba2ab-151">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="ba2ab-152">Il codice HTML viene visualizzato nel browser come:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="ba2ab-153">L'output `HtmlHelper.Raw` non è codificato ma viene sottoposto a rendering come markup HTML.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="ba2ab-154">L'uso di `HtmlHelper.Raw` su input utente non purificato costituisce un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="ba2ab-155">L'input utente potrebbe contenere JavaScript dannoso o altri attacchi.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="ba2ab-156">La purificazione degli input utente è difficile.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="ba2ab-157">Evitare l'uso di `HtmlHelper.Raw` con l'input utente.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="ba2ab-158">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="ba2ab-159">Blocchi di codice Razor</span><span class="sxs-lookup"><span data-stu-id="ba2ab-159">Razor code blocks</span></span>

<span data-ttu-id="ba2ab-160">I blocchi di codice Razor iniziano con `@` e sono racchiusi tra `{}`.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="ba2ab-161">A differenza delle espressioni, il codice C# all'interno di blocchi di codice non viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="ba2ab-162">I blocchi di codice e le espressioni in una visualizzazione condividono lo stesso ambito e vengono definiti in ordine:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="ba2ab-163">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba2ab-164">Nei blocchi di codice dichiarare [funzioni locali](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) con markup da usare come metodi per la creazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="ba2ab-165">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="ba2ab-166">Transizioni implicite</span><span class="sxs-lookup"><span data-stu-id="ba2ab-166">Implicit transitions</span></span>

<span data-ttu-id="ba2ab-167">Il linguaggio predefinito in un blocco di codice è C#, ma la pagina Razor può eseguire la transizione a HTML:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="ba2ab-168">Transizione esplicita delimitata</span><span class="sxs-lookup"><span data-stu-id="ba2ab-168">Explicit delimited transition</span></span>

<span data-ttu-id="ba2ab-169">Per definire una sottosezione di un blocco di codice che deve eseguire il rendering HTML, racchiudere i caratteri per il rendering all'interno del tag Razor `<text>`:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="ba2ab-170">Usare questo approccio per eseguire il rendering di HTML che non è racchiuso tra tag HTML.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="ba2ab-171">Senza un tag HTML o Razor, si verifica un errore di runtime Razor.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="ba2ab-172">Il tag `<text>` è utile per controllare gli spazi vuoti durante il rendering del contenuto:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="ba2ab-173">Solo il contenuto all'interno del tag `<text>` viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="ba2ab-174">Non vengono visualizzati spazi vuoti prima o dopo il tag `<text>` nell'output HTML.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="ba2ab-175">Transizione di riga esplicita</span><span class="sxs-lookup"><span data-stu-id="ba2ab-175">Explicit line transition</span></span>

<span data-ttu-id="ba2ab-176">Per eseguire il rendering del resto di un'intera riga come HTML all'interno di un blocco di codice, usare la `@:` sintassi:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="ba2ab-177">Senza il simbolo `@:` nel codice, viene generato un errore di runtime Razor.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="ba2ab-178">La presenza di caratteri `@` aggiuntivi in un file Razor può causare errori del compilatore nelle istruzioni successive del blocco.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="ba2ab-179">Questi errori del compilatore possono essere difficili da comprendere perché l'errore effettivo si verifica prima dell'errore segnalato.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="ba2ab-180">Questo errore è comune dopo la combinazione di più espressioni implicite/esplicite in un singolo blocco di codice.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="ba2ab-181">Strutture di controllo</span><span class="sxs-lookup"><span data-stu-id="ba2ab-181">Control structures</span></span>

<span data-ttu-id="ba2ab-182">Le strutture di controllo sono un'estensione dei blocchi di codice.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="ba2ab-183">Tutti gli aspetti dei blocchi di codice (transizione al markup, C# inline) sono validi anche per le strutture seguenti:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="ba2ab-184">Istruzioni condizionali \@if, else if, else e \@switch</span><span class="sxs-lookup"><span data-stu-id="ba2ab-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="ba2ab-185">`@if` controlla quando viene eseguito il codice:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="ba2ab-186">`else` e `else if` non richiedono il simbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-186">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="ba2ab-187">Nel markup seguente viene illustrato come usare un'istruzione switch:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-187">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="ba2ab-188">Esecuzione del ciclo \@for, \@foreach, \@while e \@do while</span><span class="sxs-lookup"><span data-stu-id="ba2ab-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="ba2ab-189">È possibile eseguire il rendering di HTML basato su modelli con le istruzioni di controllo ciclo.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="ba2ab-190">Per eseguire il rendering di un elenco di persone:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-190">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="ba2ab-191">Sono supportate le seguenti istruzioni di ciclo:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-191">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="ba2ab-192">\@using composta</span><span class="sxs-lookup"><span data-stu-id="ba2ab-192">Compound \@using</span></span>

<span data-ttu-id="ba2ab-193">In C# viene usata un'istruzione `using` per verificare che un oggetto sia stato eliminato.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="ba2ab-194">In Razor lo stesso meccanismo viene usato per creare gli helper HTML che includono contenuto aggiuntivo.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="ba2ab-195">Nel codice seguente gli helper HTML eseguono il rendering di un tag `<form>` con l'istruzione `@using`:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="ba2ab-196">\@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="ba2ab-196">\@try, catch, finally</span></span>

<span data-ttu-id="ba2ab-197">La gestione delle eccezioni è simile a C#:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="ba2ab-198">\@lock</span><span class="sxs-lookup"><span data-stu-id="ba2ab-198">\@lock</span></span>

<span data-ttu-id="ba2ab-199">Razor è in grado di proteggere le sezioni critiche con le istruzioni di lock:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="ba2ab-200">Commenti</span><span class="sxs-lookup"><span data-stu-id="ba2ab-200">Comments</span></span>

<span data-ttu-id="ba2ab-201">Razor supporta i commenti in C# e HTML:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="ba2ab-202">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="ba2ab-203">I commenti Razor vengono rimossi dal server prima che la pagina Web venga sottoposta a rendering.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="ba2ab-204">Razor usa `@*  *@` per delimitare i commenti.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="ba2ab-205">Il codice seguente è commentato, pertanto il server non esegue il rendering di alcun markup:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="ba2ab-206">Direttive</span><span class="sxs-lookup"><span data-stu-id="ba2ab-206">Directives</span></span>

<span data-ttu-id="ba2ab-207">Le direttive Razor sono rappresentate da espressioni implicite con parole chiave riservate dopo il simbolo `@`.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="ba2ab-208">Una direttiva cambia in genere il modo in cui viene analizzata una visualizzazione o abilita funzionalità diverse.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="ba2ab-209">Comprendere come Razor genera il codice per una visualizzazione facilita la comprensione del funzionamento delle direttive.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="ba2ab-210">Il codice genera una classe simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-210">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="ba2ab-211">La sezione [Ispezionare la classe C# Razor generata per una visualizzazione](#inspect-the-razor-c-class-generated-for-a-view) più avanti in questo articolo illustra come visualizzare la classe generata.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="ba2ab-212">\@attribute</span><span class="sxs-lookup"><span data-stu-id="ba2ab-212">\@attribute</span></span>

<span data-ttu-id="ba2ab-213">La direttiva `@attribute` aggiunge l'attributo specificato alla classe della pagina o della visualizzazione generata.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="ba2ab-214">L'esempio seguente aggiunge l'attributo `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="ba2ab-215">\@code</span><span class="sxs-lookup"><span data-stu-id="ba2ab-215">\@code</span></span>

<span data-ttu-id="ba2ab-216">*Questo scenario si applica solo ai componenti di Razor (con estensione razor).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="ba2ab-217">Il blocco `@code` consente a un [componente di Razor](xref:blazor/components) di aggiungere membri C# (campi, proprietà e metodi) a un componente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="ba2ab-218">Per i componenti Razor, `@code` è un alias di [`@functions`](#functions) e consigliato rispetto a `@functions` .</span><span class="sxs-lookup"><span data-stu-id="ba2ab-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="ba2ab-219">È consentito più di un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="ba2ab-220">\@functions</span><span class="sxs-lookup"><span data-stu-id="ba2ab-220">\@functions</span></span>

<span data-ttu-id="ba2ab-221">La direttiva `@functions` consente di aggiungere membri C# (campi, proprietà e metodi) alla classe generata:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba2ab-222">Nei [componenti di Razor](xref:blazor/components) usare `@code` in `@functions` per aggiungere membri C#.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="ba2ab-223">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="ba2ab-224">Il codice genera il markup HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="ba2ab-225">Il codice seguente è la classe C# Razor generata:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba2ab-226">I metodi `@functions` fungono da metodi per la creazione di modelli quando includono markup:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-226">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="ba2ab-227">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="ba2ab-228">\@implements</span><span class="sxs-lookup"><span data-stu-id="ba2ab-228">\@implements</span></span>

<span data-ttu-id="ba2ab-229">La direttiva `@implements` implementa un'interfaccia per la classe generata.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="ba2ab-230">L'esempio seguente implementa <xref:System.IDisposable?displayProperty=fullName> in modo che sia possibile chiamare il metodo <xref:System.IDisposable.Dispose*>:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### <a name="inherits"></a><span data-ttu-id="ba2ab-231">\@inherits</span><span class="sxs-lookup"><span data-stu-id="ba2ab-231">\@inherits</span></span>

<span data-ttu-id="ba2ab-232">La direttiva `@inherits` offre il controllo completo della classe che viene ereditata dalla visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="ba2ab-233">Il codice seguente è un tipo di pagina Razor personalizzato:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="ba2ab-234">L'elemento `CustomText` viene inserito in una visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="ba2ab-235">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="ba2ab-236">È possibile usare `@model` e `@inherits` nella stessa visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="ba2ab-237">`@inherits` può trovarsi in un file *_ViewImports.cshtml* che viene importato dalla visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="ba2ab-238">Il codice seguente è un esempio di visualizzazione fortemente tipizzata:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="ba2ab-239">Se "rick@contoso.com" viene passato nel modello, la visualizzazione genera il markup HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="ba2ab-240">\@inject</span><span class="sxs-lookup"><span data-stu-id="ba2ab-240">\@inject</span></span>

<span data-ttu-id="ba2ab-241">La direttiva `@inject` consente alla pagina Razor di inserire un servizio dal [contenitore di servizi](xref:fundamentals/dependency-injection) in una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="ba2ab-242">Per altre informazioni, vedere [Inserimento di dipendenze in visualizzazioni](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ba2ab-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="ba2ab-243">\@layout</span><span class="sxs-lookup"><span data-stu-id="ba2ab-243">\@layout</span></span>

<span data-ttu-id="ba2ab-244">*Questo scenario si applica solo ai componenti di Razor (con estensione razor).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="ba2ab-245">La direttiva `@layout` specifica un layout per un componente di Razor.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="ba2ab-246">I componenti di layout vengono usati per evitare la duplicazione e l'incoerenza del codice.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="ba2ab-247">Per altre informazioni, vedere <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="ba2ab-248">\@model</span><span class="sxs-lookup"><span data-stu-id="ba2ab-248">\@model</span></span>

<span data-ttu-id="ba2ab-249">*Questo scenario si applica solo alle viste MVC e a Razor Pages (con estensione cshtml).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="ba2ab-250">La direttiva `@model` specifica il tipo del modello passato a una vista o a una pagina:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="ba2ab-251">In un'app ASP.NET Core MVC o Razor Pages creata con account utente individuali, *Views/Account/Login.cshtml* contiene la dichiarazione di modello seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="ba2ab-252">La classe generata eredita da `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="ba2ab-253">Razor espone una proprietà `Model` per l'accesso al modello passato alla visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="ba2ab-254">La direttiva `@model` specifica il tipo della proprietà `Model`.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="ba2ab-255">La direttiva specifica l'elemento `T` in `RazorPage<T>` che ha generato la classe da cui deriva la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="ba2ab-256">Se la direttiva `@model` non è specificata, la proprietà `Model` è di tipo `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="ba2ab-257">Per ulteriori informazioni, vedere [modelli fortemente tipizzati e la @model parola chiave](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="ba2ab-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="ba2ab-258">\@namespace</span><span class="sxs-lookup"><span data-stu-id="ba2ab-258">\@namespace</span></span>

<span data-ttu-id="ba2ab-259">La direttiva `@namespace`:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="ba2ab-260">Imposta lo spazio dei nomi della classe della pagina Razor generata, della vista MVC o del componente di Razor.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="ba2ab-261">Imposta gli spazi dei nomi derivati dalla radice di pagine, viste o classi di componenti dal file di importazioni più vicino nell'albero di directory, *_ViewImports.cshtml* (viste o pagine) oppure *_Imports.razor* (componenti di Razor).</span><span class="sxs-lookup"><span data-stu-id="ba2ab-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="ba2ab-262">Per l'esempio di Razor Pages illustrato nella tabella seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="ba2ab-263">Ogni pagina importa *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="ba2ab-264">*Pages/_ViewImports.cshtml* contiene `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="ba2ab-265">Ogni pagina ha `Hello.World` come radice dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="ba2ab-266">Pagina</span><span class="sxs-lookup"><span data-stu-id="ba2ab-266">Page</span></span>                                        | <span data-ttu-id="ba2ab-267">Spazio dei nomi</span><span class="sxs-lookup"><span data-stu-id="ba2ab-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="ba2ab-268">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="ba2ab-269">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="ba2ab-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="ba2ab-271">Le relazioni precedenti si applicano ai file di importazione usati con le viste MVC e i componenti di Razor.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="ba2ab-272">Quando più file di importazione hanno una direttiva `@namespace`, per impostare lo spazio dei nomi radice, viene usato il file più vicino alla pagina, alla vista o al componente nell'albero di directory.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="ba2ab-273">Se la cartella *EvenMorePages* nell'esempio precedente ha un file di importazione con `@namespace Another.Planet` (oppure il file *Pages/MorePages/EvenMorePages/Page.cshtml* contiene `@namespace Another.Planet`), il risultato è illustrato nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="ba2ab-274">Pagina</span><span class="sxs-lookup"><span data-stu-id="ba2ab-274">Page</span></span>                                        | <span data-ttu-id="ba2ab-275">Spazio dei nomi</span><span class="sxs-lookup"><span data-stu-id="ba2ab-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="ba2ab-276">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="ba2ab-277">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="ba2ab-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="ba2ab-279">\@page</span><span class="sxs-lookup"><span data-stu-id="ba2ab-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba2ab-280">La direttiva `@page` ha effetti diversi a seconda del tipo del file in cui viene visualizzata.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="ba2ab-281">La direttiva:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-281">The directive:</span></span>

* <span data-ttu-id="ba2ab-282">In un file con estensione *cshtml* indica che il file è una pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="ba2ab-283">Per ulteriori informazioni, vedere [route personalizzate](xref:razor-pages/index#custom-routes) e <xref:razor-pages/index> .</span><span class="sxs-lookup"><span data-stu-id="ba2ab-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="ba2ab-284">Specifica che un componente Razor deve gestire direttamente le richieste.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="ba2ab-285">Per altre informazioni, vedere <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ba2ab-286">La direttiva `@page` nella prima riga di un file con estensione *cshtml* indica che il file è una pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="ba2ab-287">Per altre informazioni, vedere <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="ba2ab-288">\@section</span><span class="sxs-lookup"><span data-stu-id="ba2ab-288">\@section</span></span>

<span data-ttu-id="ba2ab-289">*Questo scenario si applica solo alle viste MVC e a Razor Pages (con estensione cshtml).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="ba2ab-290">La direttiva `@section` viene usata in combinazione con i [layout di MVC e Razor Pages](xref:mvc/views/layout) per abilitare le viste o le pagine per il rendering del contenuto in diverse parti della pagina HTML.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="ba2ab-291">Per altre informazioni, vedere <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="ba2ab-292">\@using</span><span class="sxs-lookup"><span data-stu-id="ba2ab-292">\@using</span></span>

<span data-ttu-id="ba2ab-293">La direttiva `@using` aggiunge la direttiva C# `using` alla visualizzazione generata:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba2ab-294">Nei [componenti Razor](xref:blazor/components) `@using` Controlla anche quali componenti sono inclusi nell'ambito.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="ba2ab-295">Attributi delle direttive</span><span class="sxs-lookup"><span data-stu-id="ba2ab-295">Directive attributes</span></span>

<span data-ttu-id="ba2ab-296">Gli attributi delle direttive Razor sono rappresentati da espressioni implicite con parole chiave riservate che seguono il `@` simbolo.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-296">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="ba2ab-297">Un attributo di direttiva cambia in genere il modo in cui un elemento viene analizzato o Abilita diverse funzionalità.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-297">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### <a name="attributes"></a><span data-ttu-id="ba2ab-298">\@attributes</span><span class="sxs-lookup"><span data-stu-id="ba2ab-298">\@attributes</span></span>

<span data-ttu-id="ba2ab-299">*Questo scenario si applica solo ai componenti di Razor (con estensione razor).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-299">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="ba2ab-300">`@attributes` consente a un componente di eseguire il rendering di attributi non dichiarati.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-300">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="ba2ab-301">Per altre informazioni, vedere <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-301">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="ba2ab-302">\@bind</span><span class="sxs-lookup"><span data-stu-id="ba2ab-302">\@bind</span></span>

<span data-ttu-id="ba2ab-303">*Questo scenario si applica solo ai componenti di Razor (con estensione razor).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="ba2ab-304">Il data binding nei componenti viene eseguito con l'attributo `@bind`.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-304">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="ba2ab-305">Per altre informazioni, vedere <xref:blazor/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-305">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="ba2ab-306">\@in {EVENT}</span><span class="sxs-lookup"><span data-stu-id="ba2ab-306">\@on{EVENT}</span></span>

<span data-ttu-id="ba2ab-307">*Questo scenario si applica solo ai componenti di Razor (con estensione razor).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="ba2ab-308">Razor fornisce funzionalità di gestione degli eventi per i componenti.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-308">Razor provides event handling features for components.</span></span> <span data-ttu-id="ba2ab-309">Per altre informazioni, vedere <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-309">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="ba2ab-310">\@in {EVENT}:p reventDefault</span><span class="sxs-lookup"><span data-stu-id="ba2ab-310">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="ba2ab-311">*Questo scenario si applica solo ai componenti di Razor (con estensione razor).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-311">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="ba2ab-312">Impedisce l'azione predefinita per l'evento.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-312">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="ba2ab-313">\@in {EVENT}: stopPropagation</span><span class="sxs-lookup"><span data-stu-id="ba2ab-313">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="ba2ab-314">*Questo scenario si applica solo ai componenti di Razor (con estensione razor).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-314">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="ba2ab-315">Arresta la propagazione degli eventi per l'evento.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-315">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="ba2ab-316">\@key</span><span class="sxs-lookup"><span data-stu-id="ba2ab-316">\@key</span></span>

<span data-ttu-id="ba2ab-317">*Questo scenario si applica solo ai componenti di Razor (con estensione razor).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-317">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="ba2ab-318">L'attributo della direttiva `@key` fa in modo che l'algoritmo di controllo delle differenze tra componenti garantisca la conservazione degli elementi o dei componenti in base al valore della chiave.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-318">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="ba2ab-319">Per altre informazioni, vedere <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-319">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="ba2ab-320">\@ref</span><span class="sxs-lookup"><span data-stu-id="ba2ab-320">\@ref</span></span>

<span data-ttu-id="ba2ab-321">*Questo scenario si applica solo ai componenti di Razor (con estensione razor).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-321">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="ba2ab-322">I riferimenti ai componenti (`@ref`) consentono di fare riferimento a un'istanza di un componente in modo che sia possibile eseguire comandi su tale istanza.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-322">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="ba2ab-323">Per altre informazioni, vedere <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-323">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="ba2ab-324">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="ba2ab-324">\@typeparam</span></span>

<span data-ttu-id="ba2ab-325">*Questo scenario si applica solo ai componenti di Razor (con estensione razor).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-325">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="ba2ab-326">La `@typeparam` direttiva dichiara un parametro di tipo generico per la classe Component generata.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-326">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="ba2ab-327">Per altre informazioni, vedere <xref:blazor/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-327">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="ba2ab-328">Delegati Razor basati su modelli</span><span class="sxs-lookup"><span data-stu-id="ba2ab-328">Templated Razor delegates</span></span>

<span data-ttu-id="ba2ab-329">I modelli Razor consentono di definire un frammento di codice dell'interfaccia utente con il formato seguente:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-329">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="ba2ab-330">L'esempio seguente illustra come specificare un delegato Razor basato su modelli come <xref:System.Func%602>.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-330">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="ba2ab-331">Per il parametro del metodo incapsulato dal delegato viene specificato il [tipo dinamico](/dotnet/csharp/programming-guide/types/using-type-dynamic).</span><span class="sxs-lookup"><span data-stu-id="ba2ab-331">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="ba2ab-332">Come valore restituito del delegato viene specificato un [tipo di oggetto](/dotnet/csharp/language-reference/keywords/object).</span><span class="sxs-lookup"><span data-stu-id="ba2ab-332">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="ba2ab-333">Il modello viene usato con un oggetto <xref:System.Collections.Generic.List%601> di `Pet` dotato della proprietà `Name`.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-333">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="ba2ab-334">Il rendering del modello viene eseguito con `pets` in un'istruzione `foreach`:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-334">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="ba2ab-335">Output sottoposto a rendering:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-335">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="ba2ab-336">È anche possibile specificare un modello Razor inline come argomento di un metodo.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-336">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="ba2ab-337">Nell'esempio seguente, il metodo `Repeat` riceve un modello Razor.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-337">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="ba2ab-338">Il metodo usa il modello per generare contenuto HTML tramite ripetizioni di elementi (item) ricavati da un elenco:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-338">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="ba2ab-339">Usando l'elenco di animali domestici (pets) dell'esempio precedente, il metodo `Repeat` viene chiamato con:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-339">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="ba2ab-340"><xref:System.Collections.Generic.List%601> di `Pet`.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-340"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="ba2ab-341">Numero di ripetizioni di ogni animale domestico.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-341">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="ba2ab-342">Modello inline da usare per gli elementi elenco di un elenco non ordinato.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-342">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="ba2ab-343">Output sottoposto a rendering:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-343">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="ba2ab-344">Helper tag</span><span class="sxs-lookup"><span data-stu-id="ba2ab-344">Tag Helpers</span></span>

<span data-ttu-id="ba2ab-345">*Questo scenario si applica solo alle viste MVC e a Razor Pages (con estensione cshtml).*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-345">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="ba2ab-346">Esistono tre direttive che riguardano gli [helper tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="ba2ab-346">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="ba2ab-347">Direttiva</span><span class="sxs-lookup"><span data-stu-id="ba2ab-347">Directive</span></span> | <span data-ttu-id="ba2ab-348">Funzione</span><span class="sxs-lookup"><span data-stu-id="ba2ab-348">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="ba2ab-349">Rende gli helper tag disponibili per una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-349">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="ba2ab-350">Rimuove gli helper tag aggiunti in precedenza da una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-350">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="ba2ab-351">Specifica un prefisso del tag per abilitare il supporto dell'helper tag e renderne esplicito l'uso.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-351">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a>Razor<span data-ttu-id="ba2ab-352">Parole chiave riservate</span><span class="sxs-lookup"><span data-stu-id="ba2ab-352"> reserved keywords</span></span>

### <a name="razor-keywords"></a>Razor<span data-ttu-id="ba2ab-353">Parole</span><span class="sxs-lookup"><span data-stu-id="ba2ab-353"> keywords</span></span>

* <span data-ttu-id="ba2ab-354">page (richiede ASP.NET Core 2.1 o versione successiva)</span><span class="sxs-lookup"><span data-stu-id="ba2ab-354">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="ba2ab-355">namespace</span><span class="sxs-lookup"><span data-stu-id="ba2ab-355">namespace</span></span>
* <span data-ttu-id="ba2ab-356">functions</span><span class="sxs-lookup"><span data-stu-id="ba2ab-356">functions</span></span>
* <span data-ttu-id="ba2ab-357">eredita</span><span class="sxs-lookup"><span data-stu-id="ba2ab-357">inherits</span></span>
* <span data-ttu-id="ba2ab-358">model</span><span class="sxs-lookup"><span data-stu-id="ba2ab-358">model</span></span>
* <span data-ttu-id="ba2ab-359">section</span><span class="sxs-lookup"><span data-stu-id="ba2ab-359">section</span></span>
* <span data-ttu-id="ba2ab-360">helper (attualmente non supportata da ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="ba2ab-360">helper (Not currently supported by ASP.NET Core)</span></span>

Razor<span data-ttu-id="ba2ab-361">le parole chiave sono precedute da un carattere di escape `@(Razor Keyword)` (ad esempio, `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="ba2ab-361"> keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="ba2ab-362">RazorParole chiave di C#</span><span class="sxs-lookup"><span data-stu-id="ba2ab-362">C# Razor keywords</span></span>

* <span data-ttu-id="ba2ab-363">case</span><span class="sxs-lookup"><span data-stu-id="ba2ab-363">case</span></span>
* <span data-ttu-id="ba2ab-364">do</span><span class="sxs-lookup"><span data-stu-id="ba2ab-364">do</span></span>
* <span data-ttu-id="ba2ab-365">default</span><span class="sxs-lookup"><span data-stu-id="ba2ab-365">default</span></span>
* <span data-ttu-id="ba2ab-366">per</span><span class="sxs-lookup"><span data-stu-id="ba2ab-366">for</span></span>
* <span data-ttu-id="ba2ab-367">foreach</span><span class="sxs-lookup"><span data-stu-id="ba2ab-367">foreach</span></span>
* <span data-ttu-id="ba2ab-368">if</span><span class="sxs-lookup"><span data-stu-id="ba2ab-368">if</span></span>
* <span data-ttu-id="ba2ab-369">else</span><span class="sxs-lookup"><span data-stu-id="ba2ab-369">else</span></span>
* <span data-ttu-id="ba2ab-370">lock</span><span class="sxs-lookup"><span data-stu-id="ba2ab-370">lock</span></span>
* <span data-ttu-id="ba2ab-371">switch</span><span class="sxs-lookup"><span data-stu-id="ba2ab-371">switch</span></span>
* <span data-ttu-id="ba2ab-372">provare</span><span class="sxs-lookup"><span data-stu-id="ba2ab-372">try</span></span>
* <span data-ttu-id="ba2ab-373">generazione</span><span class="sxs-lookup"><span data-stu-id="ba2ab-373">catch</span></span>
* <span data-ttu-id="ba2ab-374">finally</span><span class="sxs-lookup"><span data-stu-id="ba2ab-374">finally</span></span>
* <span data-ttu-id="ba2ab-375">using</span><span class="sxs-lookup"><span data-stu-id="ba2ab-375">using</span></span>
* <span data-ttu-id="ba2ab-376">while</span><span class="sxs-lookup"><span data-stu-id="ba2ab-376">while</span></span>

<span data-ttu-id="ba2ab-377">RazorLe parole chiave di C# devono essere con doppio carattere di escape `@(@C# Razor Keyword)` (ad esempio, `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="ba2ab-377">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="ba2ab-378">Il primo carattere di `@` escape del Razor parser.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-378">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="ba2ab-379">Il secondo `@` è il carattere di escape del parser C#.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-379">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="ba2ab-380">Parole chiave riservate non usate daRazor</span><span class="sxs-lookup"><span data-stu-id="ba2ab-380">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="ba2ab-381">classe</span><span class="sxs-lookup"><span data-stu-id="ba2ab-381">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="ba2ab-382">Esaminare la Razor classe C# generata per una visualizzazione</span><span class="sxs-lookup"><span data-stu-id="ba2ab-382">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ba2ab-383">Con .NET Core SDK 2,1 o versioni successive, l' [ Razor SDK](xref:razor-pages/sdk) gestisce la compilazione dei Razor file.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-383">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="ba2ab-384">Quando si compila un progetto, l' Razor SDK genera una directory *obj/<build_configuration>/<target_framework_moniker Razor>/* nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-384">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="ba2ab-385">La struttura di directory all'interno della *Razor* directory rispecchia la struttura di directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-385">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="ba2ab-386">Si consideri la struttura di directory seguente in un progetto ASP.NET Core 2,1 Razor pages destinato a .NET Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-386">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="ba2ab-387">**Aree**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-387">**Areas/**</span></span>
  * <span data-ttu-id="ba2ab-388">**Admin**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-388">**Admin/**</span></span>
    * <span data-ttu-id="ba2ab-389">**Pagine**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-389">**Pages/**</span></span>
      * <span data-ttu-id="ba2ab-390">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-390">*Index.cshtml*</span></span>
      * <span data-ttu-id="ba2ab-391">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-391">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="ba2ab-392">**Pagine**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-392">**Pages/**</span></span>
  * <span data-ttu-id="ba2ab-393">**Condiviso**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-393">**Shared/**</span></span>
    * <span data-ttu-id="ba2ab-394">*_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-394">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="ba2ab-395">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-395">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="ba2ab-396">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-396">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="ba2ab-397">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-397">*Index.cshtml*</span></span>
  * <span data-ttu-id="ba2ab-398">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-398">*Index.cshtml.cs*</span></span>

<span data-ttu-id="ba2ab-399">La compilazione del progetto nella configurazione *Debug* produce la seguente directory *obj*:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-399">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="ba2ab-400">**obj**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-400">**obj/**</span></span>
  * <span data-ttu-id="ba2ab-401">**Debug**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-401">**Debug/**</span></span>
    * <span data-ttu-id="ba2ab-402">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-402">**netcoreapp2.1/**</span></span>
      * **Razor/**
        * <span data-ttu-id="ba2ab-403">**Aree**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-403">**Areas/**</span></span>
          * <span data-ttu-id="ba2ab-404">**Admin**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-404">**Admin/**</span></span>
            * <span data-ttu-id="ba2ab-405">**Pagine**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-405">**Pages/**</span></span>
              * <span data-ttu-id="ba2ab-406">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-406">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="ba2ab-407">**Pagine**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-407">**Pages/**</span></span>
          * <span data-ttu-id="ba2ab-408">**Condiviso**</span><span class="sxs-lookup"><span data-stu-id="ba2ab-408">**Shared/**</span></span>
            * <span data-ttu-id="ba2ab-409">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-409">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="ba2ab-410">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-410">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="ba2ab-411">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-411">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="ba2ab-412">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ba2ab-412">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="ba2ab-413">Per visualizzare la classe generata per *pages/index. cshtml*, aprire *obj/debug/netcoreapp 2.1/ Razor /pages/index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-413">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="ba2ab-414">Aggiungere la classe seguente al progetto ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-414">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="ba2ab-415">In `Startup.ConfigureServices` eseguire l'override dell'elemento `RazorTemplateEngine` aggiunto da MVC con la classe `CustomTemplateEngine`:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-415">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="ba2ab-416">Impostare un punto di interruzione per l'istruzione `return csharpDocument;` di `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-416">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="ba2ab-417">Quando l'esecuzione del programma si interrompe nel punto di interruzione, visualizzare il valore di `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-417">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Visualizzazione nel visualizzatore testo del codice generato](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="ba2ab-419">Visualizzazione di ricerche e distinzione tra maiuscole e minuscole</span><span class="sxs-lookup"><span data-stu-id="ba2ab-419">View lookups and case sensitivity</span></span>

<span data-ttu-id="ba2ab-420">Il Razor motore di visualizzazione esegue ricerche con distinzione tra maiuscole e minuscole per le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-420">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="ba2ab-421">La ricerca effettiva è tuttavia determinata dal file system sottostante:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-421">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="ba2ab-422">Origine basata su file:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-422">File based source:</span></span>
  * <span data-ttu-id="ba2ab-423">Nei sistemi operativi con file system che non fanno distinzione tra maiuscole e minuscole (ad esempio, Windows), le ricerche del provider di file fisici non eseguono la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-423">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="ba2ab-424">Ad esempio, `return View("Test")` comporta corrispondenze per */Views/Home/Test.cshtml*, */Views/home/test.cshtml* e qualsiasi altra variante di maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-424">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="ba2ab-425">Nei file system che fanno distinzione tra maiuscole e minuscole (ad esempio Linux, OS x e con `EmbeddedFileProvider`), le ricerche eseguono la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-425">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="ba2ab-426">Ad esempio, `return View("Test")` trova specificamente le corrispondenze per */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-426">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="ba2ab-427">Visualizzazioni precompilate: con ASP.NET Core 2.0 e versioni successive, la ricerca di visualizzazioni precompilate non esegue la distinzione tra maiuscole e minuscole in tutti i sistemi operativi.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-427">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="ba2ab-428">Questo comportamento è identico al comportamento del provider di file fisici in Windows.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-428">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="ba2ab-429">Se due visualizzazioni precompilate differiscono solo nelle lettere maiuscole e minuscole, il risultato di ricerca è non deterministico.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-429">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="ba2ab-430">Gli sviluppatori sono invitati a far corrispondere le maiuscole e minuscole dei nomi di file e directory per quanto riguarda le maiuscole e minuscole di:</span><span class="sxs-lookup"><span data-stu-id="ba2ab-430">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="ba2ab-431">Nomi di area, controller e azione.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-431">Area, controller, and action names.</span></span>
* Razor<span data-ttu-id="ba2ab-432">Pagine.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-432"> Pages.</span></span>

<span data-ttu-id="ba2ab-433">La distinzione tra maiuscole e minuscole assicura che le distribuzioni trovino le proprie visualizzazioni indipendentemente dal file system sottostante.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-433">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ba2ab-434">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ba2ab-434">Additional resources</span></span>

<span data-ttu-id="ba2ab-435">[Introduzione alla programmazione Web ASP.NET con il Razor La sintassi](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) fornisce molti esempi di programmazione con la Razor sintassi.</span><span class="sxs-lookup"><span data-stu-id="ba2ab-435">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
