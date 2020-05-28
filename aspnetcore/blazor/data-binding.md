---
<span data-ttu-id="8028f-101">title:' ASP.NET Core Blazor Data Binding ' Author: Description:' informazioni sulle funzionalità di data binding per i componenti e gli elementi DOM nelle Blazor app '.</span><span class="sxs-lookup"><span data-stu-id="8028f-101">title: 'ASP.NET Core Blazor data binding' author: description: 'Learn about data binding features for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="8028f-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8028f-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8028f-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8028f-103">'Blazor'</span></span>
- <span data-ttu-id="8028f-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8028f-104">'Identity'</span></span>
- <span data-ttu-id="8028f-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8028f-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="8028f-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8028f-106">'Razor'</span></span>
- <span data-ttu-id="8028f-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8028f-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="8028f-108">ASP.NET Core Blazor Data Binding</span><span class="sxs-lookup"><span data-stu-id="8028f-108">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="8028f-109">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8028f-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="8028f-110">i componenti di forniscono funzionalità di data binding tramite un attributo dell'elemento HTML denominato [`@bind`](xref:mvc/views/razor#bind) con un campo, una proprietà o un Razor valore di espressione.</span><span class="sxs-lookup"><span data-stu-id="8028f-110"> components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="8028f-111">Nell'esempio seguente la proprietà viene associata `CurrentValue` al valore della casella di testo:</span><span class="sxs-lookup"><span data-stu-id="8028f-111">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="8028f-112">Quando la casella di testo perde lo stato attivo, viene aggiornato il valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="8028f-112">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="8028f-113">La casella di testo viene aggiornata nell'interfaccia utente solo quando viene eseguito il rendering del componente, non in risposta alla modifica del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="8028f-113">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="8028f-114">Poiché i componenti eseguono il rendering dopo l'esecuzione del codice del gestore eventi, gli aggiornamenti delle proprietà vengono in *genere* riflessi nell'interfaccia utente immediatamente dopo l'attivazione di un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="8028f-114">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="8028f-115">L'utilizzo [`@bind`](xref:mvc/views/razor#bind) di con la `CurrentValue` Proprietà ( `<input @bind="CurrentValue" />` ) equivale essenzialmente a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="8028f-115">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="8028f-116">Quando viene eseguito il rendering del componente, l'oggetto `value` dell'elemento di input deriva dalla `CurrentValue` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="8028f-116">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="8028f-117">Quando l'utente digita nella casella di testo e modifica lo stato attivo dell'elemento, `onchange` viene generato l'evento e la `CurrentValue` proprietà viene impostata sul valore modificato.</span><span class="sxs-lookup"><span data-stu-id="8028f-117">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="8028f-118">In realtà, la generazione del codice è più complessa perché [`@bind`](xref:mvc/views/razor#bind) gestisce i casi in cui vengono eseguite le conversioni dei tipi.</span><span class="sxs-lookup"><span data-stu-id="8028f-118">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="8028f-119">In linea di principio, [`@bind`](xref:mvc/views/razor#bind) associa il valore corrente di un'espressione a un `value` attributo e gestisce le modifiche utilizzando il gestore registrato.</span><span class="sxs-lookup"><span data-stu-id="8028f-119">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="8028f-120">Associare una proprietà o un campo ad altri eventi includendo anche un `@bind:event` attributo con un `event` parametro.</span><span class="sxs-lookup"><span data-stu-id="8028f-120">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="8028f-121">Nell'esempio seguente viene associata la `CurrentValue` proprietà nell' `oninput` evento:</span><span class="sxs-lookup"><span data-stu-id="8028f-121">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="8028f-122">Diversamente da `onchange` , che viene attivato quando l'elemento perde lo stato attivo, `oninput` viene attivato quando viene modificato il valore della casella di testo.</span><span class="sxs-lookup"><span data-stu-id="8028f-122">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="8028f-123">Utilizzare `@bind-{ATTRIBUTE}` con la `@bind-{ATTRIBUTE}:event` sintassi per associare attributi di elementi diversi da `value` .</span><span class="sxs-lookup"><span data-stu-id="8028f-123">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="8028f-124">Nell'esempio seguente, lo stile del paragrafo viene aggiornato quando cambia il `paragraphStyle` valore:</span><span class="sxs-lookup"><span data-stu-id="8028f-124">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="8028f-125">L'associazione di attributi distingue tra maiuscole e minuscole:</span><span class="sxs-lookup"><span data-stu-id="8028f-125">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="8028f-126">`@bind`è valido.</span><span class="sxs-lookup"><span data-stu-id="8028f-126">`@bind` is valid.</span></span>
* <span data-ttu-id="8028f-127">`@Bind`e `@BIND` non sono validi.</span><span class="sxs-lookup"><span data-stu-id="8028f-127">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="8028f-128">Valori non analizzabili</span><span class="sxs-lookup"><span data-stu-id="8028f-128">Unparsable values</span></span>

<span data-ttu-id="8028f-129">Quando un utente fornisce un valore non analizzabile a un elemento associato a un oggetto DataBound, il valore non analizzabile viene automaticamente ripristinato al valore precedente quando viene attivato l'evento bind.</span><span class="sxs-lookup"><span data-stu-id="8028f-129">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="8028f-130">Si consideri lo scenario seguente:</span><span class="sxs-lookup"><span data-stu-id="8028f-130">Consider the following scenario:</span></span>

* <span data-ttu-id="8028f-131">Un `<input>` elemento è associato a un `int` tipo con un valore iniziale di `123` :</span><span class="sxs-lookup"><span data-stu-id="8028f-131">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="8028f-132">L'utente aggiorna il valore dell'elemento a `123.45` nella pagina e modifica lo stato attivo dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="8028f-132">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="8028f-133">Nello scenario precedente, il valore dell'elemento viene ripristinato in `123` .</span><span class="sxs-lookup"><span data-stu-id="8028f-133">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="8028f-134">Quando il valore `123.45` viene rifiutato a favore del valore originale di `123` , l'utente riconosce che il relativo valore non è stato accettato.</span><span class="sxs-lookup"><span data-stu-id="8028f-134">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="8028f-135">Per impostazione predefinita, l'associazione si applica all' `onchange` evento dell'elemento ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="8028f-135">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="8028f-136">Utilizzare `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` per attivare l'associazione in un evento diverso.</span><span class="sxs-lookup"><span data-stu-id="8028f-136">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="8028f-137">Per l' `oninput` evento ( `@bind:event="oninput"` ), la riversione viene eseguita dopo qualsiasi sequenza di tasti che introduce un valore non analizzabile.</span><span class="sxs-lookup"><span data-stu-id="8028f-137">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="8028f-138">Quando la destinazione `oninput` è l'evento con un `int` tipo associato a, a un utente viene impedito di digitare un `.` carattere.</span><span class="sxs-lookup"><span data-stu-id="8028f-138">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="8028f-139">Un `.` carattere viene immediatamente rimosso, quindi l'utente riceve il feedback immediato che sono consentiti solo numeri interi.</span><span class="sxs-lookup"><span data-stu-id="8028f-139">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="8028f-140">Esistono scenari in cui il ripristino del valore dell' `oninput` evento non è ideale, ad esempio quando l'utente deve essere autorizzato a cancellare un valore non analizzabile `<input>` .</span><span class="sxs-lookup"><span data-stu-id="8028f-140">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="8028f-141">Le alternative includono:</span><span class="sxs-lookup"><span data-stu-id="8028f-141">Alternatives include:</span></span>

* <span data-ttu-id="8028f-142">Non usare l' `oninput` evento.</span><span class="sxs-lookup"><span data-stu-id="8028f-142">Don't use the `oninput` event.</span></span> <span data-ttu-id="8028f-143">Usare l' `onchange` evento predefinito (specificare solo `@bind="{PROPERTY OR FIELD}"` ), in cui un valore non valido non viene ripristinato fino a quando l'elemento non perde lo stato attivo.</span><span class="sxs-lookup"><span data-stu-id="8028f-143">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="8028f-144">Eseguire l'associazione a un tipo nullable, ad esempio `int?` o `string` , e fornire la logica personalizzata per gestire le voci non valide.</span><span class="sxs-lookup"><span data-stu-id="8028f-144">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="8028f-145">Utilizzare un [componente di convalida del modulo](xref:blazor/forms-validation), ad esempio <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> o <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="8028f-145">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="8028f-146">I componenti di convalida dei moduli includono il supporto predefinito per la gestione di input non validi.</span><span class="sxs-lookup"><span data-stu-id="8028f-146">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="8028f-147">Componenti di convalida dei moduli:</span><span class="sxs-lookup"><span data-stu-id="8028f-147">Form validation components:</span></span>
  * <span data-ttu-id="8028f-148">Consente all'utente di fornire un input non valido e di ricevere errori di convalida nell'oggetto associato <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="8028f-148">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="8028f-149">Visualizzare gli errori di convalida nell'interfaccia utente senza interferire con l'utente che immette dati Web Form aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="8028f-149">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="8028f-150">Stringhe di formato</span><span class="sxs-lookup"><span data-stu-id="8028f-150">Format strings</span></span>

<span data-ttu-id="8028f-151">Il data binding funziona con <xref:System.DateTime> stringhe di formato usando `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="8028f-151">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="8028f-152">Altre espressioni di formato, ad esempio i formati di valuta o numerici, non sono disponibili in questo momento.</span><span class="sxs-lookup"><span data-stu-id="8028f-152">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="8028f-153">Nel codice precedente, il `<input>` valore predefinito del tipo di campo dell'elemento ( `type` ) è `text` .</span><span class="sxs-lookup"><span data-stu-id="8028f-153">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="8028f-154">`@bind:format`è supportato per l'associazione dei tipi .NET seguenti:</span><span class="sxs-lookup"><span data-stu-id="8028f-154">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="8028f-155"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="8028f-155"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="8028f-156"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="8028f-156"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="8028f-157">L' `@bind:format` attributo specifica il formato di data da applicare all'oggetto `value` dell' `<input>` elemento.</span><span class="sxs-lookup"><span data-stu-id="8028f-157">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="8028f-158">Il formato viene usato anche per analizzare il valore quando `onchange` si verifica un evento.</span><span class="sxs-lookup"><span data-stu-id="8028f-158">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="8028f-159">Non è consigliabile specificare un formato per il `date` tipo di campo perché Blazor include il supporto incorporato per formattare le date.</span><span class="sxs-lookup"><span data-stu-id="8028f-159">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="8028f-160">A prescindere dalla raccomandazione, utilizzare solo il `yyyy-MM-dd` formato data per il corretto funzionamento dell'associazione se viene fornito un formato con il `date` tipo di campo:</span><span class="sxs-lookup"><span data-stu-id="8028f-160">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="8028f-161">Associazione da padre a figlio con parametri dei componenti</span><span class="sxs-lookup"><span data-stu-id="8028f-161">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="8028f-162">Il binding riconosce i parametri del componente, dove `@bind-{PROPERTY}` può associare un valore di proprietà da un componente padre a un componente figlio.</span><span class="sxs-lookup"><span data-stu-id="8028f-162">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="8028f-163">Il binding da un figlio a un padre viene trattato nell' [associazione da figlio a padre con la sezione di binding concatenata](#child-to-parent-binding-with-chained-bind) .</span><span class="sxs-lookup"><span data-stu-id="8028f-163">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="8028f-164">Il componente figlio seguente ( `ChildComponent` ) ha un `Year` parametro del componente e un `YearChanged` callback:</span><span class="sxs-lookup"><span data-stu-id="8028f-164">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="8028f-165"><xref:Microsoft.AspNetCore.Components.EventCallback%601>è illustrato in <xref:blazor/event-handling#eventcallback> .</span><span class="sxs-lookup"><span data-stu-id="8028f-165"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="8028f-166">Il componente padre seguente utilizza:</span><span class="sxs-lookup"><span data-stu-id="8028f-166">The following parent component uses:</span></span>

* <span data-ttu-id="8028f-167">`ChildComponent`e associa il `ParentYear` parametro dall'elemento padre al `Year` parametro nel componente figlio.</span><span class="sxs-lookup"><span data-stu-id="8028f-167">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="8028f-168">L' `onclick` evento viene usato per attivare il `ChangeTheYear` metodo.</span><span class="sxs-lookup"><span data-stu-id="8028f-168">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="8028f-169">Per altre informazioni, vedere <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="8028f-169">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="8028f-170">Il caricamento di `ParentComponent` produce il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="8028f-170">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="8028f-171">Se il valore della `ParentYear` proprietà viene modificato selezionando il pulsante in `ParentComponent` , `Year` viene aggiornata la proprietà dell'oggetto `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="8028f-171">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="8028f-172">Viene eseguito il rendering del nuovo valore di `Year` nell'interfaccia utente quando viene eseguito il rendering dell'oggetto `ParentComponent` :</span><span class="sxs-lookup"><span data-stu-id="8028f-172">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="8028f-173">Il `Year` parametro è associabile perché contiene un evento complementare `YearChanged` che corrisponde al tipo del `Year` parametro.</span><span class="sxs-lookup"><span data-stu-id="8028f-173">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="8028f-174">Per convenzione, `<ChildComponent @bind-Year="ParentYear" />` equivale essenzialmente alla scrittura:</span><span class="sxs-lookup"><span data-stu-id="8028f-174">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="8028f-175">In generale, una proprietà può essere associata a un gestore eventi corrispondente includendo un `@bind-{PROPRETY}:event` attributo.</span><span class="sxs-lookup"><span data-stu-id="8028f-175">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="8028f-176">Ad esempio, la proprietà `MyProp` può essere associata a `MyEventHandler` utilizzando i due attributi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8028f-176">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="8028f-177">Associazione da figlio a padre con binding concatenato</span><span class="sxs-lookup"><span data-stu-id="8028f-177">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="8028f-178">Uno scenario comune consiste nel concatenare un parametro associato a dati a un elemento Page nell'output del componente.</span><span class="sxs-lookup"><span data-stu-id="8028f-178">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="8028f-179">Questo scenario è denominato *Binding concatenato* perché si verificano simultaneamente più livelli di associazione.</span><span class="sxs-lookup"><span data-stu-id="8028f-179">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="8028f-180">Un binding concatenato non può essere implementato con [`@bind`](xref:mvc/views/razor#bind) la sintassi nell'elemento della pagina.</span><span class="sxs-lookup"><span data-stu-id="8028f-180">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="8028f-181">Il gestore eventi e il valore devono essere specificati separatamente.</span><span class="sxs-lookup"><span data-stu-id="8028f-181">The event handler and value must be specified separately.</span></span> <span data-ttu-id="8028f-182">Un componente padre, tuttavia, può utilizzare [`@bind`](xref:mvc/views/razor#bind) la sintassi con il parametro del componente.</span><span class="sxs-lookup"><span data-stu-id="8028f-182">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="8028f-183">Il `PasswordField` componente seguente (*PasswordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="8028f-183">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="8028f-184">Imposta `<input>` il valore di un elemento su una `Password` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="8028f-184">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="8028f-185">Espone le modifiche della `Password` proprietà a un componente padre con un [EventCallback](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="8028f-185">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="8028f-186">Usa l' `onclick` evento viene usato per attivare il `ToggleShowPassword` metodo.</span><span class="sxs-lookup"><span data-stu-id="8028f-186">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="8028f-187">Per altre informazioni, vedere <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="8028f-187">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

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
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="8028f-188">Il `PasswordField` componente viene usato in un altro componente:</span><span class="sxs-lookup"><span data-stu-id="8028f-188">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="8028f-189">Per eseguire controlli o intercettare gli errori sulla password nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="8028f-189">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="8028f-190">Creare un campo di supporto per `Password` ( `password` nell'esempio di codice seguente).</span><span class="sxs-lookup"><span data-stu-id="8028f-190">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="8028f-191">Eseguire i controlli o gli errori di trap nel `Password` Setter.</span><span class="sxs-lookup"><span data-stu-id="8028f-191">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="8028f-192">Nell'esempio seguente viene fornito un feedback immediato all'utente se viene utilizzato uno spazio nel valore della password:</span><span class="sxs-lookup"><span data-stu-id="8028f-192">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
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
        showPassword = !showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="8028f-193">Pulsanti di opzione</span><span class="sxs-lookup"><span data-stu-id="8028f-193">Radio buttons</span></span>

<span data-ttu-id="8028f-194">Per informazioni sull'associazione ai pulsanti di opzione in un modulo, vedere <xref:blazor/forms-validation#work-with-radio-buttons> .</span><span class="sxs-lookup"><span data-stu-id="8028f-194">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
