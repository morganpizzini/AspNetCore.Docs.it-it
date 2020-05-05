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
# <a name="aspnet-core-blazor-data-binding"></a>ASP.NET Core Blazor data binding

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Razori componenti di forniscono funzionalità di data binding tramite un attributo [`@bind`](xref:mvc/views/razor#bind) dell'elemento HTML denominato con un campo Razor , una proprietà o un valore di espressione.

Nell'esempio seguente la `CurrentValue` proprietà viene associata al valore della casella di testo:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Quando la casella di testo perde lo stato attivo, viene aggiornato il valore della proprietà.

La casella di testo viene aggiornata nell'interfaccia utente solo quando viene eseguito il rendering del componente, non in risposta alla modifica del valore della proprietà. Poiché i componenti eseguono il rendering dopo l'esecuzione del codice del gestore eventi, gli aggiornamenti delle proprietà vengono in *genere* riflessi nell'interfaccia utente immediatamente dopo l'attivazione di un gestore eventi.

L' `@bind` utilizzo di `CurrentValue` con la`<input @bind="CurrentValue" />`proprietà () equivale essenzialmente a quanto segue:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Quando viene eseguito il rendering del componente `value` , l'oggetto dell'elemento di input `CurrentValue` deriva dalla proprietà. Quando l'utente digita nella casella di testo e modifica lo stato attivo dell' `onchange` elemento, viene generato l' `CurrentValue` evento e la proprietà viene impostata sul valore modificato. In realtà, la generazione del codice è più complessa `@bind` perché gestisce i casi in cui vengono eseguite le conversioni dei tipi. In linea di `@bind` principio, associa il valore corrente di un'espressione `value` a un attributo e gestisce le modifiche utilizzando il gestore registrato.

Associare una proprietà o un campo ad altri eventi includendo anche `@bind:event` un attributo con `event` un parametro. Nell'esempio seguente viene associata la `CurrentValue` proprietà nell' `oninput` evento:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Diversamente `onchange`da, che viene attivato quando l'elemento perde lo `oninput` stato attivo, viene attivato quando viene modificato il valore della casella di testo.

Utilizzare `@bind-{ATTRIBUTE}` con `@bind-{ATTRIBUTE}:event` la sintassi per associare attributi di elementi `value`diversi da. Nell'esempio seguente, lo stile del paragrafo viene aggiornato quando cambia il `_paragraphStyle` valore:

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

L'associazione di attributi distingue tra maiuscole e minuscole. Ad esempio, `@bind` è valido e `@Bind` non è valido.

## <a name="unparsable-values"></a>Valori non analizzabili

Quando un utente fornisce un valore non analizzabile a un elemento associato a un oggetto DataBound, il valore non analizzabile viene automaticamente ripristinato al valore precedente quando viene attivato l'evento bind.

Si consideri lo scenario seguente:

* Un `<input>` elemento è associato a un `int` tipo con un valore iniziale di `123`:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* L'utente aggiorna il valore dell'elemento a `123.45` nella pagina e modifica lo stato attivo dell'elemento.

Nello scenario precedente, il valore dell'elemento viene ripristinato in `123`. Quando il valore `123.45` viene rifiutato a favore del valore originale di `123`, l'utente riconosce che il relativo valore non è stato accettato.

Per impostazione predefinita, l'associazione si applica all' `onchange` evento dell'`@bind="{PROPERTY OR FIELD}"`elemento (). Utilizzare `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` per attivare l'associazione in un evento diverso. Per l' `oninput` evento (`@bind:event="oninput"`), la riversione viene eseguita dopo qualsiasi sequenza di tasti che introduce un valore non analizzabile. Quando la destinazione è `oninput` l'evento con `int`un tipo associato a, a un utente viene impedito `.` di digitare un carattere. Un `.` carattere viene immediatamente rimosso, quindi l'utente riceve il feedback immediato che sono consentiti solo numeri interi. Esistono scenari in cui il ripristino del valore dell' `oninput` evento non è ideale, ad esempio quando l'utente deve essere autorizzato a cancellare un `<input>` valore non analizzabile. Le alternative includono:

* Non usare l' `oninput` evento. Usare l'evento `onchange` predefinito (specificare `@bind="{PROPERTY OR FIELD}"`solo), in cui un valore non valido non viene ripristinato fino a quando l'elemento non perde lo stato attivo.
* Eseguire l'associazione a un tipo nullable, `int?` ad `string`esempio o, e fornire la logica personalizzata per gestire le voci non valide.
* Utilizzare un [componente di convalida del modulo](xref:blazor/forms-validation), `InputNumber` ad `InputDate`esempio o. I componenti di convalida dei moduli includono il supporto predefinito per la gestione di input non validi. Componenti di convalida dei moduli:
  * Consente all'utente di fornire un input non valido e di ricevere errori di `EditContext`convalida nell'oggetto associato.
  * Visualizzare gli errori di convalida nell'interfaccia utente senza interferire con l'utente che immette dati Web Form aggiuntivi.

## <a name="format-strings"></a>Stringhe di formato

Il data binding funziona <xref:System.DateTime> con stringhe di [`@bind:format`](xref:mvc/views/razor#bind)formato usando. Altre espressioni di formato, ad esempio i formati di valuta o numerici, non sono disponibili in questo momento.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Nel codice precedente, il valore `<input>` predefinito del tipo di campo`type`dell'elemento () `text`è. `@bind:format`è supportato per l'associazione dei tipi .NET seguenti:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

L' `@bind:format` attributo specifica il formato di data da applicare all' `value` oggetto dell' `<input>` elemento. Il formato viene usato anche per analizzare il valore quando si `onchange` verifica un evento.

Non è consigliabile specificare un `date` formato per il tipo di Blazor campo perché include il supporto incorporato per formattare le date. A prescindere dalla raccomandazione, utilizzare solo il `yyyy-MM-dd` formato data per il corretto funzionamento dell'associazione se viene fornito un formato con `date` il tipo di campo:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Associazione da padre a figlio con parametri dei componenti

Il binding riconosce i parametri del `@bind-{PROPERTY}` componente, dove può associare un valore di proprietà da un componente padre a un componente figlio. Il binding da un figlio a un padre viene trattato nell' [associazione da figlio a padre con la sezione di binding concatenata](#child-to-parent-binding-with-chained-bind) .

Il componente figlio seguente (`ChildComponent`) ha un `Year` parametro del componente `YearChanged` e un callback:

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

`EventCallback<T>`è illustrato in <xref:blazor/event-handling#eventcallback>.

Il componente padre seguente utilizza:

* `ChildComponent`e associa il `ParentYear` parametro dall'elemento padre al `Year` parametro nel componente figlio.
* L' `onclick` evento viene usato per attivare il `ChangeTheYear` metodo. Per altre informazioni, vedere <xref:blazor/event-handling>.

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

Il caricamento `ParentComponent` di produce il markup seguente:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Se il valore `ParentYear` della proprietà viene modificato selezionando il pulsante in `ParentComponent`, viene aggiornata la `Year` proprietà dell'oggetto `ChildComponent` . Viene eseguito il rendering `Year` del nuovo valore di nell'interfaccia utente `ParentComponent` quando viene eseguito il rendering dell'oggetto:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Il `Year` parametro è associabile perché contiene un evento `YearChanged` complementare che corrisponde al tipo del `Year` parametro.

Per convenzione, `<ChildComponent @bind-Year="ParentYear" />` equivale essenzialmente alla scrittura:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

In generale, una proprietà può essere associata a un gestore eventi corrispondente includendo un `@bind-{PROPRETY}:event` attributo. Ad esempio, la proprietà `MyProp` può essere associata a `MyEventHandler` utilizzando i due attributi seguenti:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Associazione da figlio a padre con binding concatenato

Uno scenario comune consiste nel concatenare un parametro associato a dati a un elemento Page nell'output del componente. Questo scenario è denominato *Binding concatenato* perché si verificano simultaneamente più livelli di associazione.

Un binding concatenato non può essere `@bind` implementato con la sintassi nell'elemento della pagina. Il gestore eventi e il valore devono essere specificati separatamente. Un componente padre, tuttavia, può utilizzare `@bind` la sintassi con il parametro del componente.

Il componente `PasswordField` seguente (*PasswordField. Razor*):

* Imposta il `<input>` valore di un elemento su `Password` una proprietà.
* Espone le modifiche della `Password` proprietà a un componente padre con un [EventCallback](xref:blazor/event-handling#eventcallback).
* Usa l' `onclick` evento viene usato per attivare il `ToggleShowPassword` metodo. Per altre informazioni, vedere <xref:blazor/event-handling>.

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

Il `PasswordField` componente viene usato in un altro componente:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

Per eseguire controlli o intercettare gli errori sulla password nell'esempio precedente:

* Creare un campo di supporto per `Password` (`_password` nell'esempio di codice seguente).
* Eseguire i controlli o gli errori di trap `Password` nel Setter.

Nell'esempio seguente viene fornito un feedback immediato all'utente se viene utilizzato uno spazio nel valore della password:

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

## <a name="radio-buttons"></a>Pulsanti di opzione

Per informazioni sull'associazione ai pulsanti di opzione in un modulo, <xref:blazor/forms-validation#work-with-radio-buttons>vedere.
