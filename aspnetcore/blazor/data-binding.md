---
title: ASP.NET Core Blazor data binding
author: guardrex
description: Informazioni sulle funzionalità di data binding per i componenti e gli Blazor elementi DOM nelle app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: 73e73869d58e4a22e9dbee059f69fd15605ce2ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767551"
---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="5b996-103">ASP.NET Core Blazor data binding</span><span class="sxs-lookup"><span data-stu-id="5b996-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="5b996-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5b996-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="5b996-105">i componenti di forniscono funzionalità di data binding tramite un attributo [`@bind`](xref:mvc/views/razor#bind) dell'elemento HTML denominato con un campo Razor , una proprietà o un valore di espressione.</span><span class="sxs-lookup"><span data-stu-id="5b996-105"> components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="5b996-106">Nell'esempio seguente la `CurrentValue` proprietà viene associata al valore della casella di testo:</span><span class="sxs-lookup"><span data-stu-id="5b996-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="5b996-107">Quando la casella di testo perde lo stato attivo, viene aggiornato il valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="5b996-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="5b996-108">La casella di testo viene aggiornata nell'interfaccia utente solo quando viene eseguito il rendering del componente, non in risposta alla modifica del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="5b996-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="5b996-109">Poiché i componenti eseguono il rendering dopo l'esecuzione del codice del gestore eventi, gli aggiornamenti delle proprietà vengono in *genere* riflessi nell'interfaccia utente immediatamente dopo l'attivazione di un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="5b996-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="5b996-110">L' `@bind` utilizzo di `CurrentValue` con la`<input @bind="CurrentValue" />`proprietà () equivale essenzialmente a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="5b996-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="5b996-111">Quando viene eseguito il rendering del componente `value` , l'oggetto dell'elemento di input `CurrentValue` deriva dalla proprietà.</span><span class="sxs-lookup"><span data-stu-id="5b996-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="5b996-112">Quando l'utente digita nella casella di testo e modifica lo stato attivo dell' `onchange` elemento, viene generato l' `CurrentValue` evento e la proprietà viene impostata sul valore modificato.</span><span class="sxs-lookup"><span data-stu-id="5b996-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="5b996-113">In realtà, la generazione del codice è più complessa `@bind` perché gestisce i casi in cui vengono eseguite le conversioni dei tipi.</span><span class="sxs-lookup"><span data-stu-id="5b996-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="5b996-114">In linea di `@bind` principio, associa il valore corrente di un'espressione `value` a un attributo e gestisce le modifiche utilizzando il gestore registrato.</span><span class="sxs-lookup"><span data-stu-id="5b996-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="5b996-115">Associare una proprietà o un campo ad altri eventi includendo anche `@bind:event` un attributo con `event` un parametro.</span><span class="sxs-lookup"><span data-stu-id="5b996-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="5b996-116">Nell'esempio seguente viene associata la `CurrentValue` proprietà nell' `oninput` evento:</span><span class="sxs-lookup"><span data-stu-id="5b996-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="5b996-117">Diversamente `onchange`da, che viene attivato quando l'elemento perde lo `oninput` stato attivo, viene attivato quando viene modificato il valore della casella di testo.</span><span class="sxs-lookup"><span data-stu-id="5b996-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="5b996-118">Utilizzare `@bind-{ATTRIBUTE}` con `@bind-{ATTRIBUTE}:event` la sintassi per associare attributi di elementi `value`diversi da.</span><span class="sxs-lookup"><span data-stu-id="5b996-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="5b996-119">Nell'esempio seguente, lo stile del paragrafo viene aggiornato quando cambia il `_paragraphStyle` valore:</span><span class="sxs-lookup"><span data-stu-id="5b996-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

<span data-ttu-id="5b996-120">L'associazione di attributi distingue tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="5b996-120">Attribute binding is case sensitive.</span></span> <span data-ttu-id="5b996-121">Ad esempio, `@bind` è valido e `@Bind` non è valido.</span><span class="sxs-lookup"><span data-stu-id="5b996-121">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="5b996-122">Valori non analizzabili</span><span class="sxs-lookup"><span data-stu-id="5b996-122">Unparsable values</span></span>

<span data-ttu-id="5b996-123">Quando un utente fornisce un valore non analizzabile a un elemento associato a un oggetto DataBound, il valore non analizzabile viene automaticamente ripristinato al valore precedente quando viene attivato l'evento bind.</span><span class="sxs-lookup"><span data-stu-id="5b996-123">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="5b996-124">Si consideri lo scenario seguente:</span><span class="sxs-lookup"><span data-stu-id="5b996-124">Consider the following scenario:</span></span>

* <span data-ttu-id="5b996-125">Un `<input>` elemento è associato a un `int` tipo con un valore iniziale di `123`:</span><span class="sxs-lookup"><span data-stu-id="5b996-125">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="5b996-126">L'utente aggiorna il valore dell'elemento a `123.45` nella pagina e modifica lo stato attivo dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="5b996-126">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="5b996-127">Nello scenario precedente, il valore dell'elemento viene ripristinato in `123`.</span><span class="sxs-lookup"><span data-stu-id="5b996-127">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="5b996-128">Quando il valore `123.45` viene rifiutato a favore del valore originale di `123`, l'utente riconosce che il relativo valore non è stato accettato.</span><span class="sxs-lookup"><span data-stu-id="5b996-128">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="5b996-129">Per impostazione predefinita, l'associazione si applica all' `onchange` evento dell'`@bind="{PROPERTY OR FIELD}"`elemento ().</span><span class="sxs-lookup"><span data-stu-id="5b996-129">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="5b996-130">Utilizzare `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` per attivare l'associazione in un evento diverso.</span><span class="sxs-lookup"><span data-stu-id="5b996-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="5b996-131">Per l' `oninput` evento (`@bind:event="oninput"`), la riversione viene eseguita dopo qualsiasi sequenza di tasti che introduce un valore non analizzabile.</span><span class="sxs-lookup"><span data-stu-id="5b996-131">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="5b996-132">Quando la destinazione è `oninput` l'evento con `int`un tipo associato a, a un utente viene impedito `.` di digitare un carattere.</span><span class="sxs-lookup"><span data-stu-id="5b996-132">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="5b996-133">Un `.` carattere viene immediatamente rimosso, quindi l'utente riceve il feedback immediato che sono consentiti solo numeri interi.</span><span class="sxs-lookup"><span data-stu-id="5b996-133">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="5b996-134">Esistono scenari in cui il ripristino del valore dell' `oninput` evento non è ideale, ad esempio quando l'utente deve essere autorizzato a cancellare un `<input>` valore non analizzabile.</span><span class="sxs-lookup"><span data-stu-id="5b996-134">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="5b996-135">Le alternative includono:</span><span class="sxs-lookup"><span data-stu-id="5b996-135">Alternatives include:</span></span>

* <span data-ttu-id="5b996-136">Non usare l' `oninput` evento.</span><span class="sxs-lookup"><span data-stu-id="5b996-136">Don't use the `oninput` event.</span></span> <span data-ttu-id="5b996-137">Usare l'evento `onchange` predefinito (specificare `@bind="{PROPERTY OR FIELD}"`solo), in cui un valore non valido non viene ripristinato fino a quando l'elemento non perde lo stato attivo.</span><span class="sxs-lookup"><span data-stu-id="5b996-137">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="5b996-138">Eseguire l'associazione a un tipo nullable, `int?` ad `string`esempio o, e fornire la logica personalizzata per gestire le voci non valide.</span><span class="sxs-lookup"><span data-stu-id="5b996-138">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="5b996-139">Utilizzare un [componente di convalida del modulo](xref:blazor/forms-validation), `InputNumber` ad `InputDate`esempio o.</span><span class="sxs-lookup"><span data-stu-id="5b996-139">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="5b996-140">I componenti di convalida dei moduli includono il supporto predefinito per la gestione di input non validi.</span><span class="sxs-lookup"><span data-stu-id="5b996-140">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="5b996-141">Componenti di convalida dei moduli:</span><span class="sxs-lookup"><span data-stu-id="5b996-141">Form validation components:</span></span>
  * <span data-ttu-id="5b996-142">Consente all'utente di fornire un input non valido e di ricevere errori di `EditContext`convalida nell'oggetto associato.</span><span class="sxs-lookup"><span data-stu-id="5b996-142">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="5b996-143">Visualizzare gli errori di convalida nell'interfaccia utente senza interferire con l'utente che immette dati Web Form aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="5b996-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="5b996-144">Stringhe di formato</span><span class="sxs-lookup"><span data-stu-id="5b996-144">Format strings</span></span>

<span data-ttu-id="5b996-145">Il data binding funziona <xref:System.DateTime> con stringhe di [`@bind:format`](xref:mvc/views/razor#bind)formato usando.</span><span class="sxs-lookup"><span data-stu-id="5b996-145">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="5b996-146">Altre espressioni di formato, ad esempio i formati di valuta o numerici, non sono disponibili in questo momento.</span><span class="sxs-lookup"><span data-stu-id="5b996-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="5b996-147">Nel codice precedente, il valore `<input>` predefinito del tipo di campo`type`dell'elemento () `text`è.</span><span class="sxs-lookup"><span data-stu-id="5b996-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="5b996-148">`@bind:format`è supportato per l'associazione dei tipi .NET seguenti:</span><span class="sxs-lookup"><span data-stu-id="5b996-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="5b996-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="5b996-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="5b996-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="5b996-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="5b996-151">L' `@bind:format` attributo specifica il formato di data da applicare all' `value` oggetto dell' `<input>` elemento.</span><span class="sxs-lookup"><span data-stu-id="5b996-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="5b996-152">Il formato viene usato anche per analizzare il valore quando si `onchange` verifica un evento.</span><span class="sxs-lookup"><span data-stu-id="5b996-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="5b996-153">Non è consigliabile specificare un `date` formato per il tipo di Blazor campo perché include il supporto incorporato per formattare le date.</span><span class="sxs-lookup"><span data-stu-id="5b996-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="5b996-154">A prescindere dalla raccomandazione, utilizzare solo il `yyyy-MM-dd` formato data per il corretto funzionamento dell'associazione se viene fornito un formato con `date` il tipo di campo:</span><span class="sxs-lookup"><span data-stu-id="5b996-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="5b996-155">Associazione da padre a figlio con parametri dei componenti</span><span class="sxs-lookup"><span data-stu-id="5b996-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="5b996-156">Il binding riconosce i parametri del `@bind-{PROPERTY}` componente, dove può associare un valore di proprietà da un componente padre a un componente figlio.</span><span class="sxs-lookup"><span data-stu-id="5b996-156">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="5b996-157">Il binding da un figlio a un padre viene trattato nell' [associazione da figlio a padre con la sezione di binding concatenata](#child-to-parent-binding-with-chained-bind) .</span><span class="sxs-lookup"><span data-stu-id="5b996-157">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="5b996-158">Il componente figlio seguente (`ChildComponent`) ha un `Year` parametro del componente `YearChanged` e un callback:</span><span class="sxs-lookup"><span data-stu-id="5b996-158">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="5b996-159">`EventCallback<T>`è illustrato in <xref:blazor/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="5b996-159">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="5b996-160">Il componente padre seguente utilizza:</span><span class="sxs-lookup"><span data-stu-id="5b996-160">The following parent component uses:</span></span>

* <span data-ttu-id="5b996-161">`ChildComponent`e associa il `ParentYear` parametro dall'elemento padre al `Year` parametro nel componente figlio.</span><span class="sxs-lookup"><span data-stu-id="5b996-161">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="5b996-162">L' `onclick` evento viene usato per attivare il `ChangeTheYear` metodo.</span><span class="sxs-lookup"><span data-stu-id="5b996-162">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="5b996-163">Per altre informazioni, vedere <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="5b996-163">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="5b996-164">Il caricamento `ParentComponent` di produce il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="5b996-164">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="5b996-165">Se il valore `ParentYear` della proprietà viene modificato selezionando il pulsante in `ParentComponent`, viene aggiornata la `Year` proprietà dell'oggetto `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="5b996-165">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="5b996-166">Viene eseguito il rendering `Year` del nuovo valore di nell'interfaccia utente `ParentComponent` quando viene eseguito il rendering dell'oggetto:</span><span class="sxs-lookup"><span data-stu-id="5b996-166">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="5b996-167">Il `Year` parametro è associabile perché contiene un evento `YearChanged` complementare che corrisponde al tipo del `Year` parametro.</span><span class="sxs-lookup"><span data-stu-id="5b996-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="5b996-168">Per convenzione, `<ChildComponent @bind-Year="ParentYear" />` equivale essenzialmente alla scrittura:</span><span class="sxs-lookup"><span data-stu-id="5b996-168">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="5b996-169">In generale, una proprietà può essere associata a un gestore eventi corrispondente includendo un `@bind-{PROPRETY}:event` attributo.</span><span class="sxs-lookup"><span data-stu-id="5b996-169">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="5b996-170">Ad esempio, la proprietà `MyProp` può essere associata a `MyEventHandler` utilizzando i due attributi seguenti:</span><span class="sxs-lookup"><span data-stu-id="5b996-170">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="5b996-171">Associazione da figlio a padre con binding concatenato</span><span class="sxs-lookup"><span data-stu-id="5b996-171">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="5b996-172">Uno scenario comune consiste nel concatenare un parametro associato a dati a un elemento Page nell'output del componente.</span><span class="sxs-lookup"><span data-stu-id="5b996-172">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="5b996-173">Questo scenario è denominato *Binding concatenato* perché si verificano simultaneamente più livelli di associazione.</span><span class="sxs-lookup"><span data-stu-id="5b996-173">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="5b996-174">Un binding concatenato non può essere `@bind` implementato con la sintassi nell'elemento della pagina.</span><span class="sxs-lookup"><span data-stu-id="5b996-174">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="5b996-175">Il gestore eventi e il valore devono essere specificati separatamente.</span><span class="sxs-lookup"><span data-stu-id="5b996-175">The event handler and value must be specified separately.</span></span> <span data-ttu-id="5b996-176">Un componente padre, tuttavia, può utilizzare `@bind` la sintassi con il parametro del componente.</span><span class="sxs-lookup"><span data-stu-id="5b996-176">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="5b996-177">Il componente `PasswordField` seguente (*PasswordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="5b996-177">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="5b996-178">Imposta il `<input>` valore di un elemento su `Password` una proprietà.</span><span class="sxs-lookup"><span data-stu-id="5b996-178">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="5b996-179">Espone le modifiche della `Password` proprietà a un componente padre con un [EventCallback](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="5b996-179">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="5b996-180">Usa l' `onclick` evento viene usato per attivare il `ToggleShowPassword` metodo.</span><span class="sxs-lookup"><span data-stu-id="5b996-180">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="5b996-181">Per altre informazioni, vedere <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="5b996-181">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="5b996-182">Il `PasswordField` componente viene usato in un altro componente:</span><span class="sxs-lookup"><span data-stu-id="5b996-182">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="5b996-183">Per eseguire controlli o intercettare gli errori sulla password nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="5b996-183">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="5b996-184">Creare un campo di supporto per `Password` (`_password` nell'esempio di codice seguente).</span><span class="sxs-lookup"><span data-stu-id="5b996-184">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="5b996-185">Eseguire i controlli o gli errori di trap `Password` nel Setter.</span><span class="sxs-lookup"><span data-stu-id="5b996-185">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="5b996-186">Nell'esempio seguente viene fornito un feedback immediato all'utente se viene utilizzato uno spazio nel valore della password:</span><span class="sxs-lookup"><span data-stu-id="5b996-186">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="5b996-187">Pulsanti di opzione</span><span class="sxs-lookup"><span data-stu-id="5b996-187">Radio buttons</span></span>

<span data-ttu-id="5b996-188">Per informazioni sull'associazione ai pulsanti di opzione in un modulo, <xref:blazor/forms-validation#work-with-radio-buttons>vedere.</span><span class="sxs-lookup"><span data-stu-id="5b996-188">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
