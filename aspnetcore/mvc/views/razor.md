---
title: informazioni di riferimento sulla sintassi Razor per ASP.NET Core
author: rick-anderson
description: "Informazioni sulla Razor sintassi di markup per l'incorporamento di codice basato su server in pagine Web."
ms.author: riande
ms.date: 02/12/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/razor
ms.openlocfilehash: c1278b0cd3e58814b1c06dca81efd662c3de0c54
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059195"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="cd8c1-103">Razor informazioni di riferimento sulla sintassi per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd8c1-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="cd8c1-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)e [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="cd8c1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="cd8c1-105">Razor è una sintassi di markup per l'incorporamento di codice basato su server in pagine Web.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="cd8c1-106">La Razor sintassi è costituita da Razor markup, C# e HTML.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="cd8c1-107">I file che contengono Razor in genere hanno un'estensione di file *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cd8c1-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="cd8c1-108">Razorsi trova anche nei file dei [ Razor componenti](xref:blazor/components/index) ( *Razor* ).</span><span class="sxs-lookup"><span data-stu-id="cd8c1-108">Razor is also found in [Razor components](xref:blazor/components/index) files ( *.razor* ).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="cd8c1-109">Rendering di HTML</span><span class="sxs-lookup"><span data-stu-id="cd8c1-109">Rendering HTML</span></span>

<span data-ttu-id="cd8c1-110">La Razor lingua predefinita è HTML.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-110">The default Razor language is HTML.</span></span> <span data-ttu-id="cd8c1-111">Il rendering del codice HTML dal Razor markup non è diverso dal rendering HTML da un file HTML.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="cd8c1-112">Il rendering del markup HTML nei file con *estensione cshtml* Razor viene eseguito senza modifiche al server.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="cd8c1-113">Sintassi Razor</span><span class="sxs-lookup"><span data-stu-id="cd8c1-113">Razor syntax</span></span>

<span data-ttu-id="cd8c1-114">Razor supporta C# e usa il `@` simbolo per eseguire la transizione da HTML a c#.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="cd8c1-115">Razor valuta le espressioni C# e ne esegue il rendering nell'output HTML.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="cd8c1-116">Quando un `@` simbolo è seguito da una [ Razor parola chiave riservata](#razor-reserved-keywords), viene eseguita la transizione a un Razor markup specifico.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="cd8c1-117">in caso contrario in C# semplice.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="cd8c1-118">Per eseguire l'escape di un `@` simbolo nel Razor markup, usare un secondo `@` simbolo:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="cd8c1-119">Il rendering del codice viene eseguito in HTML con un solo simbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="cd8c1-120">Gli attributi e il contenuto HTML contenenti indirizzi di posta elettronica non considerano il simbolo `@` come un carattere di transizione.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="cd8c1-121">Gli indirizzi di posta elettronica nell'esempio seguente non vengono modificati mediante l' Razor analisi:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="cd8c1-122">Espressioni implicite Razor</span><span class="sxs-lookup"><span data-stu-id="cd8c1-122">Implicit Razor expressions</span></span>

<span data-ttu-id="cd8c1-123">RazorLe espressioni implicite iniziano con `@` seguito dal codice C#:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="cd8c1-124">Fatta eccezione per la parola chiave `await` di C#, le espressioni implicite non devono contenere spazi.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="cd8c1-125">Se l'istruzione C# ha una fine chiaramente definita, possono coesistere spazi:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="cd8c1-126">Le espressioni implicite **non possono** contenere generics C#, poiché i caratteri all'interno delle parentesi (`<>`) vengono interpretati come un tag HTML.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="cd8c1-127">Il codice seguente **non** è valido:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="cd8c1-128">Il codice precedente genera un errore del compilatore simile a uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="cd8c1-129">L'elemento "int" non è stato chiuso.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="cd8c1-130">Tutti gli elementi devono essere a chiusura automatica o avere un tag di fine corrispondente.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="cd8c1-131">Non è possibile convertire il gruppo di metodi 'GenericMethod' nel tipo non delegato 'object'.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="cd8c1-132">Si intendeva chiamare il metodo?'</span><span class="sxs-lookup"><span data-stu-id="cd8c1-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="cd8c1-133">Le chiamate al metodo generico devono essere incapsulate in un' [ Razor espressione esplicita](#explicit-razor-expressions) o in un [ Razor blocco di codice](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="cd8c1-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="cd8c1-134">Espressioni esplicite Razor</span><span class="sxs-lookup"><span data-stu-id="cd8c1-134">Explicit Razor expressions</span></span>

<span data-ttu-id="cd8c1-135">Le Razor espressioni esplicite sono costituite da un `@` simbolo con parentesi bilanciate.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="cd8c1-136">Per eseguire il rendering dell'ora dell'ultima settimana, Razor viene usato il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="cd8c1-137">Qualsiasi contenuto all'interno delle parentesi `@()` viene valutato e sottoposto a rendering nell'output.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="cd8c1-138">Le espressioni implicite, descritte nella sezione precedente, in genere non possono contenere spazi.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="cd8c1-139">Nel codice seguente, una settimana non viene sottratta dall'ora corrente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="cd8c1-140">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="cd8c1-141">È possibile usare le espressioni esplicite per concatenare testo con un risultato dell'espressione:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="cd8c1-142">Senza l'espressione esplicita, `<p>Age@joe.Age</p>` viene considerato come un indirizzo di posta elettronica e `<p>Age@joe.Age</p>` viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="cd8c1-143">Se viene scritto come espressione esplicita, `<p>Age33</p>` viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="cd8c1-144">È possibile usare le espressioni esplicite per eseguire il rendering dell'output da metodi generici in file con estensione *cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cd8c1-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="cd8c1-145">Il markup seguente illustra come correggere l'errore riportato in precedenza, causato dalle parentesi quadre di un oggetto generico C#.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="cd8c1-146">Il codice viene scritto come un'espressione esplicita:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="cd8c1-147">Codifica di espressioni</span><span class="sxs-lookup"><span data-stu-id="cd8c1-147">Expression encoding</span></span>

<span data-ttu-id="cd8c1-148">Le espressioni C# che restituiscono una stringa sono codificate in HTML.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="cd8c1-149">Le espressioni C# che restituiscono `IHtmlContent` vengono sottoposte a rendering direttamente tramite `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="cd8c1-150">Le espressioni C# che non restituiscono `IHtmlContent` vengono convertite in una stringa da `ToString` e codificate prima di essere sottoposte a rendering.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="cd8c1-151">Il codice precedente esegue il rendering del codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-151">The preceding code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="cd8c1-152">Il codice HTML viene visualizzato nel browser come testo normale:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-152">The HTML is shown in the browser as plain text:</span></span>

<span data-ttu-id="cd8c1-153">&lt;span &gt; Hello World &lt; /span&gt;</span><span class="sxs-lookup"><span data-stu-id="cd8c1-153">&lt;span&gt;Hello World&lt;/span&gt;</span></span>

<span data-ttu-id="cd8c1-154">L'output `HtmlHelper.Raw` non è codificato ma viene sottoposto a rendering come markup HTML.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-154">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="cd8c1-155">L'uso di `HtmlHelper.Raw` su input utente non purificato costituisce un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-155">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="cd8c1-156">L'input utente potrebbe contenere JavaScript dannoso o altri attacchi.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-156">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="cd8c1-157">La purificazione degli input utente è difficile.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-157">Sanitizing user input is difficult.</span></span> <span data-ttu-id="cd8c1-158">Evitare l'uso di `HtmlHelper.Raw` con l'input utente.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-158">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="cd8c1-159">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-159">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="cd8c1-160">Razor blocchi di codice</span><span class="sxs-lookup"><span data-stu-id="cd8c1-160">Razor code blocks</span></span>

<span data-ttu-id="cd8c1-161">Razor i blocchi di codice iniziano con `@` e sono racchiusi tra `{}` .</span><span class="sxs-lookup"><span data-stu-id="cd8c1-161">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="cd8c1-162">A differenza delle espressioni, il codice C# all'interno di blocchi di codice non viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-162">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="cd8c1-163">I blocchi di codice e le espressioni in una visualizzazione condividono lo stesso ambito e vengono definiti in ordine:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-163">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="cd8c1-164">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-164">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd8c1-165">Nei blocchi di codice dichiarare [funzioni locali](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) con markup da usare come metodi per la creazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-165">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="cd8c1-166">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-166">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="cd8c1-167">Transizioni implicite</span><span class="sxs-lookup"><span data-stu-id="cd8c1-167">Implicit transitions</span></span>

<span data-ttu-id="cd8c1-168">La lingua predefinita in un blocco di codice è C#, ma la pagina è in Razor grado di eseguire la transizione a HTML:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-168">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="cd8c1-169">Transizione esplicita delimitata</span><span class="sxs-lookup"><span data-stu-id="cd8c1-169">Explicit delimited transition</span></span>

<span data-ttu-id="cd8c1-170">Per definire una sottosezione di un blocco di codice che deve eseguire il rendering di HTML, racchiudere i caratteri per il rendering con il Razor `<text>` Tag:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-170">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="cd8c1-171">Usare questo approccio per eseguire il rendering di HTML che non è racchiuso tra tag HTML.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-171">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="cd8c1-172">Senza un tag HTML o Razor , Razor si verifica un errore di Runtime.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-172">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="cd8c1-173">Il tag `<text>` è utile per controllare gli spazi vuoti durante il rendering del contenuto:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-173">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="cd8c1-174">Solo il contenuto all'interno del tag `<text>` viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-174">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="cd8c1-175">Non vengono visualizzati spazi vuoti prima o dopo il tag `<text>` nell'output HTML.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-175">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="cd8c1-176">Transizione di riga esplicita</span><span class="sxs-lookup"><span data-stu-id="cd8c1-176">Explicit line transition</span></span>

<span data-ttu-id="cd8c1-177">Per eseguire il rendering del resto di un'intera riga come HTML all'interno di un blocco di codice, usare la `@:` sintassi:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-177">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="cd8c1-178">Senza l'oggetto `@:` nel codice, Razor viene generato un errore di Runtime.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-178">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="cd8c1-179">`@`I caratteri aggiuntivi in un Razor file possono causare errori del compilatore nelle istruzioni successive nel blocco.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-179">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="cd8c1-180">Questi errori del compilatore possono essere difficili da comprendere perché l'errore effettivo si verifica prima dell'errore segnalato.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-180">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="cd8c1-181">Questo errore è comune dopo la combinazione di più espressioni implicite/esplicite in un singolo blocco di codice.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-181">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="cd8c1-182">Strutture di controllo</span><span class="sxs-lookup"><span data-stu-id="cd8c1-182">Control structures</span></span>

<span data-ttu-id="cd8c1-183">Le strutture di controllo sono un'estensione dei blocchi di codice.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-183">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="cd8c1-184">Tutti gli aspetti dei blocchi di codice (transizione al markup, C# inline) sono validi anche per le strutture seguenti:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-184">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="cd8c1-185">Istruzioni condizionali `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="cd8c1-185">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="cd8c1-186">`@if` controlla quando viene eseguito il codice:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-186">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="cd8c1-187">`else` e `else if` non richiedono il simbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-187">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="cd8c1-188">Nel markup seguente viene illustrato come usare un'istruzione switch:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-188">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="cd8c1-189">Ciclo `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="cd8c1-189">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="cd8c1-190">È possibile eseguire il rendering di HTML basato su modelli con le istruzioni di controllo ciclo.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-190">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="cd8c1-191">Per eseguire il rendering di un elenco di persone:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-191">To render a list of people:</span></span>

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

<span data-ttu-id="cd8c1-192">Sono supportate le seguenti istruzioni di ciclo:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-192">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="cd8c1-193">Istruzione `@using` composita</span><span class="sxs-lookup"><span data-stu-id="cd8c1-193">Compound `@using`</span></span>

<span data-ttu-id="cd8c1-194">In C# viene usata un'istruzione `using` per verificare che un oggetto sia stato eliminato.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-194">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="cd8c1-195">In Razor lo stesso meccanismo viene usato per creare helper HTML che contengono contenuto aggiuntivo.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-195">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="cd8c1-196">Nel codice seguente gli helper HTML eseguono il rendering di un tag `<form>` con l'istruzione `@using`:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-196">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

<span data-ttu-id="cd8c1-197">La gestione delle eccezioni è simile a C#:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="cd8c1-198">Razor offre la possibilità di proteggere le sezioni critiche con le istruzioni lock:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-198">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="cd8c1-199">Commenti</span><span class="sxs-lookup"><span data-stu-id="cd8c1-199">Comments</span></span>

<span data-ttu-id="cd8c1-200">Razor supporta i commenti in C# e HTML:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-200">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="cd8c1-201">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="cd8c1-202">Razor i commenti vengono rimossi dal server prima che venga eseguito il rendering della pagina Web.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-202">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="cd8c1-203">Razor USA `@*  *@` per delimitare i commenti.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-203">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="cd8c1-204">Il codice seguente è commentato, pertanto il server non esegue il rendering di alcun markup:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="cd8c1-205">Direttive</span><span class="sxs-lookup"><span data-stu-id="cd8c1-205">Directives</span></span>

<span data-ttu-id="cd8c1-206">Razor le direttive sono rappresentate da espressioni implicite con parole chiave riservate che seguono il `@` simbolo.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-206">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="cd8c1-207">Una direttiva cambia in genere il modo in cui viene analizzata una visualizzazione o abilita funzionalità diverse.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="cd8c1-208">Comprendere in che modo Razor genera il codice per una vista rende più semplice comprendere il funzionamento delle direttive.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-208">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="cd8c1-209">Il codice genera una classe simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-209">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="cd8c1-210">Più avanti in questo articolo, la sezione [esaminare la Razor classe C# generata per una visualizzazione](#inspect-the-razor-c-class-generated-for-a-view) spiega come visualizzare questa classe generata.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-210">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="cd8c1-211">La direttiva `@attribute` aggiunge l'attributo specificato alla classe della pagina o della visualizzazione generata.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-211">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="cd8c1-212">L'esempio seguente aggiunge l'attributo `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-212">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="cd8c1-213">*Questo scenario si applica solo ai Razor componenti (Razor).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-213">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="cd8c1-214">Il `@code` blocco consente a un [ Razor componente](xref:blazor/components/index) di aggiungere membri C# (campi, proprietà e metodi) a un componente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-214">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="cd8c1-215">Per i Razor componenti di, `@code` è un alias di [`@functions`](#functions) e consigliato rispetto a `@functions` .</span><span class="sxs-lookup"><span data-stu-id="cd8c1-215">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="cd8c1-216">È consentito più di un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-216">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="cd8c1-217">La direttiva `@functions` consente di aggiungere membri C# (campi, proprietà e metodi) alla classe generata:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-217">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd8c1-218">In [ Razor Components](xref:blazor/components/index)usare `@code` over `@functions` per aggiungere membri C#.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-218">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="cd8c1-219">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-219">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="cd8c1-220">Il codice genera il markup HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-220">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="cd8c1-221">Il codice seguente è la Razor classe C# generata:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-221">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd8c1-222">I metodi `@functions` fungono da metodi per la creazione di modelli quando includono markup:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-222">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="cd8c1-223">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-223">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="cd8c1-224">La direttiva `@implements` implementa un'interfaccia per la classe generata.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-224">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="cd8c1-225">L'esempio seguente implementa <xref:System.IDisposable?displayProperty=fullName> in modo che sia possibile chiamare il metodo <xref:System.IDisposable.Dispose*>:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-225">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

### `@inherits`

<span data-ttu-id="cd8c1-226">La direttiva `@inherits` offre il controllo completo della classe che viene ereditata dalla visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-226">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="cd8c1-227">Il codice seguente è un Razor tipo di pagina personalizzato:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-227">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="cd8c1-228">L'elemento `CustomText` viene inserito in una visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-228">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="cd8c1-229">Il codice esegue il rendering dell'HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-229">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="cd8c1-230">È possibile usare `@model` e `@inherits` nella stessa visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-230">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="cd8c1-231">`@inherits` può trovarsi in un file *_ViewImports.cshtml* che viene importato dalla visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-231">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="cd8c1-232">Il codice seguente è un esempio di visualizzazione fortemente tipizzata:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-232">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="cd8c1-233">Se "rick@contoso.com" viene passato nel modello, la visualizzazione genera il markup HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-233">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="cd8c1-234">La `@inject` direttiva consente alla Razor pagina di inserire un servizio dal [contenitore di servizi](xref:fundamentals/dependency-injection) in una vista.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-234">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="cd8c1-235">Per altre informazioni, vedere [Inserimento di dipendenze in visualizzazioni](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cd8c1-235">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="cd8c1-236">*Questo scenario si applica solo ai Razor componenti (Razor).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-236">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="cd8c1-237">La `@layout` direttiva specifica un layout per un Razor componente.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-237">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="cd8c1-238">I componenti di layout vengono usati per evitare la duplicazione e l'incoerenza del codice.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-238">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="cd8c1-239">Per altre informazioni, vedere <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-239">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="cd8c1-240">*Questo scenario si applica solo a viste e Razor pagine MVC (cshtml).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-240">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="cd8c1-241">La direttiva `@model` specifica il tipo del modello passato a una vista o a una pagina:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-241">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="cd8c1-242">In un'app ASP.NET Core MVC o Razor pagine creata con singoli account utente, *views/account/login. cshtml* contiene la dichiarazione del modello seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-242">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="cd8c1-243">La classe generata eredita da `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-243">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="cd8c1-244">Razor espone una `Model` proprietà per l'accesso al modello passato alla visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-244">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="cd8c1-245">La direttiva `@model` specifica il tipo della proprietà `Model`.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-245">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="cd8c1-246">La direttiva specifica l'elemento `T` in `RazorPage<T>` che ha generato la classe da cui deriva la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-246">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="cd8c1-247">Se la direttiva `@model` non è specificata, la proprietà `Model` è di tipo `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-247">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="cd8c1-248">Per ulteriori informazioni, vedere [modelli fortemente tipizzati e la @model parola chiave](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="cd8c1-248">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="cd8c1-249">La direttiva `@namespace`:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-249">The `@namespace` directive:</span></span>

* <span data-ttu-id="cd8c1-250">Imposta lo spazio dei nomi della classe della Razor Pagina generata, della visualizzazione MVC o del Razor componente.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-250">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="cd8c1-251">Imposta gli spazi dei nomi derivati radice di una classe di pagine, viste o componenti dal file di importazione più vicino nell'albero di directory *_ViewImports. cshtml* (viste o pagine) o *_Imports. Razor* ( Razor Components).</span><span class="sxs-lookup"><span data-stu-id="cd8c1-251">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="cd8c1-252">Per l' Razor esempio di pagine illustrato nella tabella seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-252">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="cd8c1-253">Ogni pagina importa *Pages/_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cd8c1-253">Each page imports *Pages/_ViewImports.cshtml* .</span></span>
* <span data-ttu-id="cd8c1-254">*Pages/_ViewImports.cshtml* contiene `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-254">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="cd8c1-255">Ogni pagina ha `Hello.World` come radice dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-255">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="cd8c1-256">Pagina</span><span class="sxs-lookup"><span data-stu-id="cd8c1-256">Page</span></span>                                        | <span data-ttu-id="cd8c1-257">Spazio dei nomi</span><span class="sxs-lookup"><span data-stu-id="cd8c1-257">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="cd8c1-258">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-258">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="cd8c1-259">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-259">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="cd8c1-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="cd8c1-261">Le relazioni precedenti si applicano ai file di importazione utilizzati con i componenti e le visualizzazioni MVC Razor .</span><span class="sxs-lookup"><span data-stu-id="cd8c1-261">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="cd8c1-262">Quando più file di importazione hanno una direttiva `@namespace`, per impostare lo spazio dei nomi radice, viene usato il file più vicino alla pagina, alla vista o al componente nell'albero di directory.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-262">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="cd8c1-263">Se la cartella *EvenMorePages* nell'esempio precedente ha un file di importazione con `@namespace Another.Planet` (oppure il file *Pages/MorePages/EvenMorePages/Page.cshtml* contiene `@namespace Another.Planet`), il risultato è illustrato nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-263">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="cd8c1-264">Pagina</span><span class="sxs-lookup"><span data-stu-id="cd8c1-264">Page</span></span>                                        | <span data-ttu-id="cd8c1-265">Spazio dei nomi</span><span class="sxs-lookup"><span data-stu-id="cd8c1-265">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="cd8c1-266">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-266">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="cd8c1-267">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-267">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="cd8c1-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd8c1-269">La direttiva `@page` ha effetti diversi a seconda del tipo del file in cui viene visualizzata.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-269">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="cd8c1-270">La direttiva:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-270">The directive:</span></span>

* <span data-ttu-id="cd8c1-271">In un file con *estensione cshtml* indica che il file è una Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-271">In a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="cd8c1-272">Per ulteriori informazioni, vedere [route personalizzate](xref:razor-pages/index#custom-routes) e <xref:razor-pages/index> .</span><span class="sxs-lookup"><span data-stu-id="cd8c1-272">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="cd8c1-273">Specifica che un Razor componente deve gestire direttamente le richieste.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-273">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="cd8c1-274">Per altre informazioni, vedere <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-274">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cd8c1-275">La `@page` direttiva nella prima riga di un file con *estensione cshtml* indica che il file è una Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-275">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="cd8c1-276">Per altre informazioni, vedere <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-276">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="cd8c1-277">*Questo scenario si applica solo a viste e Razor pagine MVC (cshtml).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-277">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="cd8c1-278">La `@section` direttiva viene utilizzata insieme ai [layout MVC e Razor pagine](xref:mvc/views/layout) per consentire alle visualizzazioni o alle pagine di eseguire il rendering del contenuto in diverse parti della pagina HTML.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-278">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="cd8c1-279">Per altre informazioni, vedere <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-279">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="cd8c1-280">La direttiva `@using` aggiunge la direttiva C# `using` alla visualizzazione generata:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-280">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd8c1-281">Nei [ Razor componenti](xref:blazor/components/index)di `@using` Controlla anche quali componenti sono inclusi nell'ambito.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-281">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="cd8c1-282">Attributi delle direttive</span><span class="sxs-lookup"><span data-stu-id="cd8c1-282">Directive attributes</span></span>

<span data-ttu-id="cd8c1-283">Razor gli attributi di direttiva sono rappresentati da espressioni implicite con parole chiave riservate che seguono il `@` simbolo.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-283">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="cd8c1-284">Un attributo di direttiva cambia in genere il modo in cui un elemento viene analizzato o Abilita diverse funzionalità.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-284">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="cd8c1-285">*Questo scenario si applica solo ai Razor componenti (Razor).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-285">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="cd8c1-286">`@attributes` consente a un componente di eseguire il rendering di attributi non dichiarati.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-286">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="cd8c1-287">Per altre informazioni, vedere <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-287">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="cd8c1-288">*Questo scenario si applica solo ai Razor componenti (Razor).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-288">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="cd8c1-289">Il data binding nei componenti viene eseguito con l'attributo `@bind`.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-289">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="cd8c1-290">Per altre informazioni, vedere <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-290">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="cd8c1-291">*Questo scenario si applica solo ai Razor componenti (Razor).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-291">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="cd8c1-292">Razor fornisce funzionalità di gestione degli eventi per i componenti di.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-292">Razor provides event handling features for components.</span></span> <span data-ttu-id="cd8c1-293">Per altre informazioni, vedere <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-293">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="cd8c1-294">*Questo scenario si applica solo ai Razor componenti (Razor).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-294">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="cd8c1-295">Impedisce l'azione predefinita per l'evento.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-295">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="cd8c1-296">*Questo scenario si applica solo ai Razor componenti (Razor).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-296">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="cd8c1-297">Arresta la propagazione degli eventi per l'evento.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-297">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="cd8c1-298">*Questo scenario si applica solo ai Razor componenti (Razor).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-298">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="cd8c1-299">L'attributo della direttiva `@key` fa in modo che l'algoritmo di controllo delle differenze tra componenti garantisca la conservazione degli elementi o dei componenti in base al valore della chiave.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-299">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="cd8c1-300">Per altre informazioni, vedere <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-300">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="cd8c1-301">*Questo scenario si applica solo ai Razor componenti (Razor).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="cd8c1-302">I riferimenti ai componenti (`@ref`) consentono di fare riferimento a un'istanza di un componente in modo che sia possibile eseguire comandi su tale istanza.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-302">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="cd8c1-303">Per altre informazioni, vedere <xref:blazor/components/index#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-303">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="cd8c1-304">*Questo scenario si applica solo ai Razor componenti (Razor).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-304">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="cd8c1-305">La `@typeparam` direttiva dichiara un parametro di tipo generico per la classe Component generata.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-305">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="cd8c1-306">Per altre informazioni, vedere <xref:blazor/components/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-306">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="cd8c1-307">Delegati basati su modelli Razor</span><span class="sxs-lookup"><span data-stu-id="cd8c1-307">Templated Razor delegates</span></span>

<span data-ttu-id="cd8c1-308">Razor i modelli consentono di definire un frammento di interfaccia utente con il formato seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-308">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="cd8c1-309">Nell'esempio seguente viene illustrato come specificare un delegato basato su modelli Razor come <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="cd8c1-309">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="cd8c1-310">Per il parametro del metodo incapsulato dal delegato viene specificato il [tipo dinamico](/dotnet/csharp/programming-guide/types/using-type-dynamic).</span><span class="sxs-lookup"><span data-stu-id="cd8c1-310">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="cd8c1-311">Come valore restituito del delegato viene specificato un [tipo di oggetto](/dotnet/csharp/language-reference/keywords/object).</span><span class="sxs-lookup"><span data-stu-id="cd8c1-311">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="cd8c1-312">Il modello viene usato con un oggetto <xref:System.Collections.Generic.List%601> di `Pet` dotato della proprietà `Name`.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-312">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="cd8c1-313">Il rendering del modello viene eseguito con `pets` in un'istruzione `foreach`:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-313">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="cd8c1-314">Output sottoposto a rendering:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-314">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="cd8c1-315">È anche possibile fornire un modello inline Razor come argomento a un metodo.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-315">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="cd8c1-316">Nell'esempio seguente il `Repeat` metodo riceve un Razor modello.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-316">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="cd8c1-317">Il metodo usa il modello per generare contenuto HTML tramite ripetizioni di elementi (item) ricavati da un elenco:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-317">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="cd8c1-318">Usando l'elenco di animali domestici (pets) dell'esempio precedente, il metodo `Repeat` viene chiamato con:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-318">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="cd8c1-319"><xref:System.Collections.Generic.List%601> di `Pet`.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-319"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="cd8c1-320">Numero di ripetizioni di ogni animale domestico.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-320">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="cd8c1-321">Modello inline da usare per gli elementi elenco di un elenco non ordinato.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-321">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="cd8c1-322">Output sottoposto a rendering:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-322">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="cd8c1-323">Helper tag</span><span class="sxs-lookup"><span data-stu-id="cd8c1-323">Tag Helpers</span></span>

<span data-ttu-id="cd8c1-324">*Questo scenario si applica solo a viste e Razor pagine MVC (cshtml).*</span><span class="sxs-lookup"><span data-stu-id="cd8c1-324">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="cd8c1-325">Esistono tre direttive che riguardano gli [helper tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="cd8c1-325">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="cd8c1-326">Direttiva</span><span class="sxs-lookup"><span data-stu-id="cd8c1-326">Directive</span></span> | <span data-ttu-id="cd8c1-327">Funzione</span><span class="sxs-lookup"><span data-stu-id="cd8c1-327">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="cd8c1-328">Rende gli helper tag disponibili per una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-328">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="cd8c1-329">Rimuove gli helper tag aggiunti in precedenza da una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-329">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="cd8c1-330">Specifica un prefisso del tag per abilitare il supporto dell'helper tag e renderne esplicito l'uso.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-330">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="cd8c1-331">Razor Parole chiave riservate</span><span class="sxs-lookup"><span data-stu-id="cd8c1-331">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="cd8c1-332">Razor Parole</span><span class="sxs-lookup"><span data-stu-id="cd8c1-332">Razor keywords</span></span>

* <span data-ttu-id="cd8c1-333">`page` (Richiede ASP.NET Core 2,1 o versione successiva)</span><span class="sxs-lookup"><span data-stu-id="cd8c1-333">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="cd8c1-334">`helper` (Attualmente non supportata da ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="cd8c1-334">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="cd8c1-335">Razor le parole chiave sono precedute da un carattere di escape `@(Razor Keyword)` (ad esempio, `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="cd8c1-335">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="cd8c1-336">RazorParole chiave di C#</span><span class="sxs-lookup"><span data-stu-id="cd8c1-336">C# Razor keywords</span></span>

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

<span data-ttu-id="cd8c1-337">RazorLe parole chiave di C# devono essere con doppio carattere di escape `@(@C# Razor Keyword)` (ad esempio, `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="cd8c1-337">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="cd8c1-338">Il primo carattere di `@` escape del Razor parser.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-338">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="cd8c1-339">Il secondo `@` è il carattere di escape del parser C#.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-339">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="cd8c1-340">Parole chiave riservate non usate da Razor</span><span class="sxs-lookup"><span data-stu-id="cd8c1-340">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="cd8c1-341">Esaminare la Razor classe C# generata per una visualizzazione</span><span class="sxs-lookup"><span data-stu-id="cd8c1-341">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="cd8c1-342">Con .NET Core SDK 2,1 o versioni successive, l' [ Razor SDK](xref:razor-pages/sdk) gestisce la compilazione dei Razor file.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-342">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="cd8c1-343">Quando si compila un progetto, l' Razor SDK genera una directory *obj/<build_configuration>/<target_framework_moniker Razor>/* nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-343">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="cd8c1-344">La struttura di directory all'interno della *Razor* directory rispecchia la struttura di directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-344">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="cd8c1-345">Si consideri la struttura di directory seguente in un progetto ASP.NET Core 2,1 Razor pages destinato a .NET Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-345">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

<span data-ttu-id="cd8c1-346">La compilazione del progetto nella configurazione *Debug* produce la seguente directory *obj* :</span><span class="sxs-lookup"><span data-stu-id="cd8c1-346">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

<span data-ttu-id="cd8c1-347">Per visualizzare la classe generata per *pages/index. cshtml* , aprire *obj/debug/netcoreapp 2.1/ Razor /pages/index.g.cshtml.cs* .</span><span class="sxs-lookup"><span data-stu-id="cd8c1-347">To view the generated class for *Pages/Index.cshtml* , open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs* .</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="cd8c1-348">Aggiungere la classe seguente al progetto ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-348">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="cd8c1-349">In `Startup.ConfigureServices` eseguire l'override dell'elemento `RazorTemplateEngine` aggiunto da MVC con la classe `CustomTemplateEngine`:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-349">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="cd8c1-350">Impostare un punto di interruzione per l'istruzione `return csharpDocument;` di `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-350">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="cd8c1-351">Quando l'esecuzione del programma si interrompe nel punto di interruzione, visualizzare il valore di `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-351">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Visualizzazione nel visualizzatore testo del codice generato](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="cd8c1-353">Visualizzazione di ricerche e distinzione tra maiuscole e minuscole</span><span class="sxs-lookup"><span data-stu-id="cd8c1-353">View lookups and case sensitivity</span></span>

<span data-ttu-id="cd8c1-354">Il Razor motore di visualizzazione esegue ricerche con distinzione tra maiuscole e minuscole per le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-354">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="cd8c1-355">La ricerca effettiva è tuttavia determinata dal file system sottostante:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-355">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="cd8c1-356">Origine basata su file:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-356">File based source:</span></span>
  * <span data-ttu-id="cd8c1-357">Nei sistemi operativi con file system che non fanno distinzione tra maiuscole e minuscole (ad esempio, Windows), le ricerche del provider di file fisici non eseguono la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-357">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="cd8c1-358">Ad esempio, `return View("Test")` comporta corrispondenze per */Views/Home/Test.cshtml* , */Views/home/test.cshtml* e qualsiasi altra variante di maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-358">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml* , */Views/home/test.cshtml* , and any other casing variant.</span></span>
  * <span data-ttu-id="cd8c1-359">Nei file system che fanno distinzione tra maiuscole e minuscole (ad esempio Linux, OS x e con `EmbeddedFileProvider`), le ricerche eseguono la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-359">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="cd8c1-360">Ad esempio, `return View("Test")` trova specificamente le corrispondenze per */Views/Home/Test.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cd8c1-360">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml* .</span></span>
* <span data-ttu-id="cd8c1-361">Visualizzazioni precompilate: con ASP.NET Core 2.0 e versioni successive, la ricerca di visualizzazioni precompilate non esegue la distinzione tra maiuscole e minuscole in tutti i sistemi operativi.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-361">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="cd8c1-362">Questo comportamento è identico al comportamento del provider di file fisici in Windows.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-362">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="cd8c1-363">Se due visualizzazioni precompilate differiscono solo nelle lettere maiuscole e minuscole, il risultato di ricerca è non deterministico.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-363">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="cd8c1-364">Gli sviluppatori sono invitati a far corrispondere le maiuscole e minuscole dei nomi di file e directory per quanto riguarda le maiuscole e minuscole di:</span><span class="sxs-lookup"><span data-stu-id="cd8c1-364">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="cd8c1-365">Nomi di area, controller e azione.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-365">Area, controller, and action names.</span></span>
* <span data-ttu-id="cd8c1-366">Razor Pagine.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-366">Razor Pages.</span></span>

<span data-ttu-id="cd8c1-367">La distinzione tra maiuscole e minuscole assicura che le distribuzioni trovino le proprie visualizzazioni indipendentemente dal file system sottostante.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-367">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd8c1-368">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="cd8c1-368">Additional resources</span></span>

<span data-ttu-id="cd8c1-369">[Introduzione alla programmazione Web ASP.NET con il Razor La sintassi](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) fornisce molti esempi di programmazione con la Razor sintassi.</span><span class="sxs-lookup"><span data-stu-id="cd8c1-369">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
