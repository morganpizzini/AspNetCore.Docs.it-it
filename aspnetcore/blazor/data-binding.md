---
title: Associazione Blazor dati di ASP.NET Core
author: guardrex
description: Informazioni sulle funzionalità di associazione Blazor dati per i componenti e gli elementi DOM nelle app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: a7b3730dad48b5bbb6134dab181051da4e3651b4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320957"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="615da-103">Associazione Blazor dati di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="615da-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="615da-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="615da-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="615da-105">I componenti Razor forniscono funzionalità di [`@bind`](xref:mvc/views/razor#bind) associazione dati tramite un attributo dell'elemento HTML denominato con un campo, una proprietà o un valore dell'espressione Razor.</span><span class="sxs-lookup"><span data-stu-id="615da-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="615da-106">L'esempio seguente `CurrentValue` associa la proprietà al valore della casella di testo:</span><span class="sxs-lookup"><span data-stu-id="615da-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="615da-107">Quando la casella di testo perde lo stato attivo, il valore della proprietà viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="615da-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="615da-108">La casella di testo viene aggiornata nell'interfaccia utente solo quando viene eseguito il rendering del componente, non in risposta alla modifica del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="615da-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="615da-109">Poiché i componenti eseguono il rendering di se stessi dopo l'esecuzione del codice del gestore eventi, gli aggiornamenti delle proprietà vengono *in genere* riflessi nell'interfaccia utente immediatamente dopo l'attivazione di un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="615da-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="615da-110">`@bind` L'utilizzo `CurrentValue` con`<input @bind="CurrentValue" />`la proprietà ( ) è essenzialmente equivalente a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="615da-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="615da-111">Quando viene eseguito `value` il rendering del componente, l'elemento di input proviene dalla `CurrentValue` proprietà.</span><span class="sxs-lookup"><span data-stu-id="615da-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="615da-112">Quando l'utente digita nella casella di `onchange` testo e modifica `CurrentValue` lo stato attivo dell'elemento, l'evento viene generato e la proprietà viene impostata sul valore modificato.</span><span class="sxs-lookup"><span data-stu-id="615da-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="615da-113">In realtà, la generazione di `@bind` codice è più complessa perché gestisce i casi in cui vengono eseguite le conversioni dei tipi.</span><span class="sxs-lookup"><span data-stu-id="615da-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="615da-114">In linea `@bind` di principio, associa il `value` valore corrente di un'espressione a un attributo e gestisce le modifiche utilizzando il gestore registrato.</span><span class="sxs-lookup"><span data-stu-id="615da-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="615da-115">Associare una proprietà o un campo `@bind:event` in `event` altri eventi includendo anche un attributo con un parametro.</span><span class="sxs-lookup"><span data-stu-id="615da-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="615da-116">Nell'esempio seguente `CurrentValue` la proprietà `oninput` dell'evento viene associata:</span><span class="sxs-lookup"><span data-stu-id="615da-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="615da-117">A `onchange`differenza di , che viene `oninput` generato quando l'elemento perde lo stato attivo, viene generato quando il valore della casella di testo cambia.</span><span class="sxs-lookup"><span data-stu-id="615da-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="615da-118">Utilizzare `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` con la sintassi `value`per associare attributi di elemento diversi da .</span><span class="sxs-lookup"><span data-stu-id="615da-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="615da-119">Nell'esempio seguente, lo stile del paragrafo viene aggiornato quando il `_paragraphStyle` valore cambia:</span><span class="sxs-lookup"><span data-stu-id="615da-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

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

<span data-ttu-id="615da-120">Per l'associazione di attributi viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="615da-120">Attribute binding is case sensitive.</span></span> <span data-ttu-id="615da-121">Ad esempio, `@bind` è `@Bind` valido e non è valido.</span><span class="sxs-lookup"><span data-stu-id="615da-121">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="615da-122">Valori impercilabili</span><span class="sxs-lookup"><span data-stu-id="615da-122">Unparsable values</span></span>

<span data-ttu-id="615da-123">Quando un utente fornisce un valore non analizzabile a un elemento associato a dati, il valore non analizzabile viene automaticamente ripristinato al valore precedente quando viene attivato l'evento di associazione.</span><span class="sxs-lookup"><span data-stu-id="615da-123">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="615da-124">Si consideri lo scenario seguente:</span><span class="sxs-lookup"><span data-stu-id="615da-124">Consider the following scenario:</span></span>

* <span data-ttu-id="615da-125">Un `<input>` elemento è `int` associato a un `123`tipo con un valore iniziale di :</span><span class="sxs-lookup"><span data-stu-id="615da-125">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="615da-126">L'utente aggiorna il valore `123.45` dell'elemento nella pagina e modifica lo stato attivo dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="615da-126">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="615da-127">Nello scenario precedente, il valore dell'elemento `123`viene ripristinato su .</span><span class="sxs-lookup"><span data-stu-id="615da-127">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="615da-128">Quando il `123.45` valore viene rifiutato a `123`favore del valore originale di , l'utente riconosce che il valore non è stato accettato.</span><span class="sxs-lookup"><span data-stu-id="615da-128">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="615da-129">Per impostazione predefinita, l'associazione si applica all'evento dell'elemento `onchange` (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="615da-129">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="615da-130">Utilizzare `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` per attivare l'associazione su un evento diverso.</span><span class="sxs-lookup"><span data-stu-id="615da-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="615da-131">Per `oninput` l'evento (`@bind:event="oninput"`), la reversione si verifica dopo qualsiasi sequenza di tasti che introduce un valore non analizzabile.</span><span class="sxs-lookup"><span data-stu-id="615da-131">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="615da-132">Quando si `oninput` ha come `int`destinazione l'evento con un `.` tipo associato a -, a un utente viene impedito di digitare un carattere.</span><span class="sxs-lookup"><span data-stu-id="615da-132">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="615da-133">Un `.` carattere viene immediatamente rimosso, in modo che l'utente riceve un feedback immediato che solo i numeri interi sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="615da-133">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="615da-134">Esistono scenari in cui il `oninput` ripristino del valore nell'evento non è ideale, ad esempio `<input>` quando l'utente deve essere autorizzato a cancellare un valore non analizzabile.</span><span class="sxs-lookup"><span data-stu-id="615da-134">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="615da-135">Le alternative includono:</span><span class="sxs-lookup"><span data-stu-id="615da-135">Alternatives include:</span></span>

* <span data-ttu-id="615da-136">Non usare l'evento. `oninput`</span><span class="sxs-lookup"><span data-stu-id="615da-136">Don't use the `oninput` event.</span></span> <span data-ttu-id="615da-137">Utilizzare l'evento `onchange` predefinito `@bind="{PROPERTY OR FIELD}"`(specificare solo ) in cui non viene ripristinato un valore non valido finché l'elemento non perde lo stato attivo.</span><span class="sxs-lookup"><span data-stu-id="615da-137">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="615da-138">Eseguire l'associazione a `int?` un `string`tipo nullable, ad esempio o , e fornire logica personalizzata per gestire le voci non valide.</span><span class="sxs-lookup"><span data-stu-id="615da-138">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="615da-139">Utilizzare un componente di `InputNumber` convalida `InputDate` [del form,](xref:blazor/forms-validation)ad esempio o .</span><span class="sxs-lookup"><span data-stu-id="615da-139">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="615da-140">I componenti di convalida dei moduli dispongono del supporto incorporato per gestire input non validi.</span><span class="sxs-lookup"><span data-stu-id="615da-140">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="615da-141">Componenti di convalida dei moduli:</span><span class="sxs-lookup"><span data-stu-id="615da-141">Form validation components:</span></span>
  * <span data-ttu-id="615da-142">Consentire all'utente di fornire input non `EditContext`valido e ricevere errori di convalida nell'oggetto associato.</span><span class="sxs-lookup"><span data-stu-id="615da-142">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="615da-143">Visualizzare gli errori di convalida nell'interfaccia utente senza interferire con l'utente che immette dati aggiuntivi del modulo Web.</span><span class="sxs-lookup"><span data-stu-id="615da-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="615da-144">Formattare le stringhe</span><span class="sxs-lookup"><span data-stu-id="615da-144">Format strings</span></span>

<span data-ttu-id="615da-145">L'associazione <xref:System.DateTime> dati [`@bind:format`](xref:mvc/views/razor#bind)funziona con le stringhe di formato utilizzando .</span><span class="sxs-lookup"><span data-stu-id="615da-145">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="615da-146">Al momento non sono disponibili altre espressioni di formato, ad esempio i formati di valuta o numerici.</span><span class="sxs-lookup"><span data-stu-id="615da-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="615da-147">Nel codice precedente, `<input>` il tipo di`type`campo dell'elemento ( ) viene impostato su `text`.</span><span class="sxs-lookup"><span data-stu-id="615da-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="615da-148">`@bind:format`è supportato per l'associazione dei seguenti tipi .NET:</span><span class="sxs-lookup"><span data-stu-id="615da-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="615da-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="615da-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="615da-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="615da-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="615da-151">L'attributo `@bind:format` specifica il formato `value` della `<input>` data da applicare all'elemento.</span><span class="sxs-lookup"><span data-stu-id="615da-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="615da-152">Il formato viene utilizzato anche per `onchange` analizzare il valore quando si verifica un evento.</span><span class="sxs-lookup"><span data-stu-id="615da-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="615da-153">La specifica di `date` un formato per il Blazor tipo di campo non è consigliata perché dispone del supporto incorporato per formattare le date.</span><span class="sxs-lookup"><span data-stu-id="615da-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="615da-154">Nonostante la raccomandazione, utilizzare `yyyy-MM-dd` il formato della data per il corretto `date` funzionamento dell'associazione solo se viene fornito un formato con il tipo di campo:</span><span class="sxs-lookup"><span data-stu-id="615da-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="615da-155">Associazione padre-figlio con i parametri del componente</span><span class="sxs-lookup"><span data-stu-id="615da-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="615da-156">L'associazione riconosce `@bind-{PROPERTY}` i parametri del componente, in cui è possibile associare il valore di una proprietà da un componente padre a un componente figlio.</span><span class="sxs-lookup"><span data-stu-id="615da-156">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="615da-157">L'associazione da un elemento figlio a un elemento padre è descritta nella sezione Associazione da [figlio a padre con associazione concatenata.](#child-to-parent-binding-with-chained-bind)</span><span class="sxs-lookup"><span data-stu-id="615da-157">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="615da-158">Il componente figlio`ChildComponent`seguente `Year` ( ) `YearChanged` ha un parametro del componente e un callback:</span><span class="sxs-lookup"><span data-stu-id="615da-158">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="615da-159">`EventCallback<T>`è spiegato <xref:blazor/event-handling#eventcallback>in .</span><span class="sxs-lookup"><span data-stu-id="615da-159">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="615da-160">Viene utilizzato il seguente componente padre:</span><span class="sxs-lookup"><span data-stu-id="615da-160">The following parent component uses:</span></span>

* <span data-ttu-id="615da-161">`ChildComponent`e associa `ParentYear` il parametro dal `Year` padre al parametro nel componente figlio.</span><span class="sxs-lookup"><span data-stu-id="615da-161">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="615da-162">L'evento `onclick` viene utilizzato `ChangeTheYear` per attivare il metodo.</span><span class="sxs-lookup"><span data-stu-id="615da-162">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="615da-163">Per altre informazioni, vedere <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="615da-163">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="615da-164">Il `ParentComponent` caricamento del prodotto produce il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="615da-164">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="615da-165">Se il valore `ParentYear` della proprietà viene modificato `ParentComponent`selezionando `Year` il pulsante nella proprietà , la proprietà dell'oggetto `ChildComponent` viene aggiornata.</span><span class="sxs-lookup"><span data-stu-id="615da-165">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="615da-166">Il nuovo `Year` valore di viene eseguito il rendering nell'interfaccia utente quando viene eseguito nuovamente il rendering dell'oggetto: `ParentComponent`</span><span class="sxs-lookup"><span data-stu-id="615da-166">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="615da-167">Il `Year` parametro è associabile `YearChanged` perché dispone di un `Year` evento complementare che corrisponde al tipo del parametro.</span><span class="sxs-lookup"><span data-stu-id="615da-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="615da-168">Per convenzione, `<ChildComponent @bind-Year="ParentYear" />` è essenzialmente equivalente alla scrittura:</span><span class="sxs-lookup"><span data-stu-id="615da-168">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="615da-169">In generale, una proprietà può essere associata `@bind-{PROPRETY}:event` a un gestore eventi corrispondente includendo un attributo.</span><span class="sxs-lookup"><span data-stu-id="615da-169">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="615da-170">Ad esempio, `MyProp` la proprietà `MyEventHandler` può essere associata all'utilizzo dei due attributi seguenti:For example, the property can be bound to using the following two attributes:</span><span class="sxs-lookup"><span data-stu-id="615da-170">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="615da-171">Associazione da figlio a padre con associazione concatenata</span><span class="sxs-lookup"><span data-stu-id="615da-171">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="615da-172">Uno scenario comune è il concatenamento di un parametro con associazione a dati a un elemento di pagina nell'output del componente.</span><span class="sxs-lookup"><span data-stu-id="615da-172">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="615da-173">Questo scenario è denominato *associazione concatenata* perché più livelli di associazione si verificano contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="615da-173">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="615da-174">Un'associazione concatenata `@bind` non può essere implementata con la sintassi nell'elemento della pagina.</span><span class="sxs-lookup"><span data-stu-id="615da-174">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="615da-175">Il gestore eventi e il valore devono essere specificati separatamente.</span><span class="sxs-lookup"><span data-stu-id="615da-175">The event handler and value must be specified separately.</span></span> <span data-ttu-id="615da-176">Un componente padre, tuttavia, può utilizzare `@bind` la sintassi con il parametro del componente.</span><span class="sxs-lookup"><span data-stu-id="615da-176">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="615da-177">Il `PasswordField` componente seguente (*PasswordField.razor*):</span><span class="sxs-lookup"><span data-stu-id="615da-177">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="615da-178">Imposta `<input>` il valore di `Password` un elemento su una proprietà.</span><span class="sxs-lookup"><span data-stu-id="615da-178">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="615da-179">Espone le modifiche `Password` della proprietà a un componente padre con [un oggetto EventCallback](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="615da-179">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="615da-180">Utilizza `onclick` l'evento viene `ToggleShowPassword` utilizzato per attivare il metodo.</span><span class="sxs-lookup"><span data-stu-id="615da-180">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="615da-181">Per altre informazioni, vedere <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="615da-181">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="615da-182">Il `PasswordField` componente viene utilizzato in un altro componente:</span><span class="sxs-lookup"><span data-stu-id="615da-182">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="615da-183">Per eseguire controlli o intercettare gli errori sulla password nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="615da-183">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="615da-184">Creare un campo `Password` di`_password` backup per ( nel codice di esempio seguente).</span><span class="sxs-lookup"><span data-stu-id="615da-184">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="615da-185">Eseguire i controlli o `Password` gli errori di intrappolare nel setter.</span><span class="sxs-lookup"><span data-stu-id="615da-185">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="615da-186">L'esempio seguente fornisce un feedback immediato all'utente se viene utilizzato uno spazio nel valore della password:The following example provides immediate feedback to the user if a space is used in the password's value:</span><span class="sxs-lookup"><span data-stu-id="615da-186">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="radio-buttons"></a><span data-ttu-id="615da-187">Pulsanti di opzione</span><span class="sxs-lookup"><span data-stu-id="615da-187">Radio buttons</span></span>

<span data-ttu-id="615da-188">Per informazioni sull'associazione ai <xref:blazor/forms-validation#work-with-radio-buttons>pulsanti di opzione in un modulo, vedere .</span><span class="sxs-lookup"><span data-stu-id="615da-188">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
