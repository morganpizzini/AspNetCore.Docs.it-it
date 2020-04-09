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
# <a name="aspnet-core-opno-locblazor-data-binding"></a>Associazione Blazor dati di ASP.NET Core

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

I componenti Razor forniscono funzionalità di [`@bind`](xref:mvc/views/razor#bind) associazione dati tramite un attributo dell'elemento HTML denominato con un campo, una proprietà o un valore dell'espressione Razor.

L'esempio seguente `CurrentValue` associa la proprietà al valore della casella di testo:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Quando la casella di testo perde lo stato attivo, il valore della proprietà viene aggiornato.

La casella di testo viene aggiornata nell'interfaccia utente solo quando viene eseguito il rendering del componente, non in risposta alla modifica del valore della proprietà. Poiché i componenti eseguono il rendering di se stessi dopo l'esecuzione del codice del gestore eventi, gli aggiornamenti delle proprietà vengono *in genere* riflessi nell'interfaccia utente immediatamente dopo l'attivazione di un gestore eventi.

`@bind` L'utilizzo `CurrentValue` con`<input @bind="CurrentValue" />`la proprietà ( ) è essenzialmente equivalente a quanto segue:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Quando viene eseguito `value` il rendering del componente, l'elemento di input proviene dalla `CurrentValue` proprietà. Quando l'utente digita nella casella di `onchange` testo e modifica `CurrentValue` lo stato attivo dell'elemento, l'evento viene generato e la proprietà viene impostata sul valore modificato. In realtà, la generazione di `@bind` codice è più complessa perché gestisce i casi in cui vengono eseguite le conversioni dei tipi. In linea `@bind` di principio, associa il `value` valore corrente di un'espressione a un attributo e gestisce le modifiche utilizzando il gestore registrato.

Associare una proprietà o un campo `@bind:event` in `event` altri eventi includendo anche un attributo con un parametro. Nell'esempio seguente `CurrentValue` la proprietà `oninput` dell'evento viene associata:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

A `onchange`differenza di , che viene `oninput` generato quando l'elemento perde lo stato attivo, viene generato quando il valore della casella di testo cambia.

Utilizzare `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` con la sintassi `value`per associare attributi di elemento diversi da . Nell'esempio seguente, lo stile del paragrafo viene aggiornato quando il `_paragraphStyle` valore cambia:

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

Per l'associazione di attributi viene fatta distinzione tra maiuscole e minuscole. Ad esempio, `@bind` è `@Bind` valido e non è valido.

## <a name="unparsable-values"></a>Valori impercilabili

Quando un utente fornisce un valore non analizzabile a un elemento associato a dati, il valore non analizzabile viene automaticamente ripristinato al valore precedente quando viene attivato l'evento di associazione.

Si consideri lo scenario seguente:

* Un `<input>` elemento è `int` associato a un `123`tipo con un valore iniziale di :

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* L'utente aggiorna il valore `123.45` dell'elemento nella pagina e modifica lo stato attivo dell'elemento.

Nello scenario precedente, il valore dell'elemento `123`viene ripristinato su . Quando il `123.45` valore viene rifiutato a `123`favore del valore originale di , l'utente riconosce che il valore non è stato accettato.

Per impostazione predefinita, l'associazione si applica all'evento dell'elemento `onchange` (`@bind="{PROPERTY OR FIELD}"`). Utilizzare `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` per attivare l'associazione su un evento diverso. Per `oninput` l'evento (`@bind:event="oninput"`), la reversione si verifica dopo qualsiasi sequenza di tasti che introduce un valore non analizzabile. Quando si `oninput` ha come `int`destinazione l'evento con un `.` tipo associato a -, a un utente viene impedito di digitare un carattere. Un `.` carattere viene immediatamente rimosso, in modo che l'utente riceve un feedback immediato che solo i numeri interi sono consentiti. Esistono scenari in cui il `oninput` ripristino del valore nell'evento non è ideale, ad esempio `<input>` quando l'utente deve essere autorizzato a cancellare un valore non analizzabile. Le alternative includono:

* Non usare l'evento. `oninput` Utilizzare l'evento `onchange` predefinito `@bind="{PROPERTY OR FIELD}"`(specificare solo ) in cui non viene ripristinato un valore non valido finché l'elemento non perde lo stato attivo.
* Eseguire l'associazione a `int?` un `string`tipo nullable, ad esempio o , e fornire logica personalizzata per gestire le voci non valide.
* Utilizzare un componente di `InputNumber` convalida `InputDate` [del form,](xref:blazor/forms-validation)ad esempio o . I componenti di convalida dei moduli dispongono del supporto incorporato per gestire input non validi. Componenti di convalida dei moduli:
  * Consentire all'utente di fornire input non `EditContext`valido e ricevere errori di convalida nell'oggetto associato.
  * Visualizzare gli errori di convalida nell'interfaccia utente senza interferire con l'utente che immette dati aggiuntivi del modulo Web.

## <a name="format-strings"></a>Formattare le stringhe

L'associazione <xref:System.DateTime> dati [`@bind:format`](xref:mvc/views/razor#bind)funziona con le stringhe di formato utilizzando . Al momento non sono disponibili altre espressioni di formato, ad esempio i formati di valuta o numerici.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Nel codice precedente, `<input>` il tipo di`type`campo dell'elemento ( ) viene impostato su `text`. `@bind:format`è supportato per l'associazione dei seguenti tipi .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

L'attributo `@bind:format` specifica il formato `value` della `<input>` data da applicare all'elemento. Il formato viene utilizzato anche per `onchange` analizzare il valore quando si verifica un evento.

La specifica di `date` un formato per il Blazor tipo di campo non è consigliata perché dispone del supporto incorporato per formattare le date. Nonostante la raccomandazione, utilizzare `yyyy-MM-dd` il formato della data per il corretto `date` funzionamento dell'associazione solo se viene fornito un formato con il tipo di campo:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Associazione padre-figlio con i parametri del componente

L'associazione riconosce `@bind-{PROPERTY}` i parametri del componente, in cui è possibile associare il valore di una proprietà da un componente padre a un componente figlio. L'associazione da un elemento figlio a un elemento padre è descritta nella sezione Associazione da [figlio a padre con associazione concatenata.](#child-to-parent-binding-with-chained-bind)

Il componente figlio`ChildComponent`seguente `Year` ( ) `YearChanged` ha un parametro del componente e un callback:

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

`EventCallback<T>`è spiegato <xref:blazor/event-handling#eventcallback>in .

Viene utilizzato il seguente componente padre:

* `ChildComponent`e associa `ParentYear` il parametro dal `Year` padre al parametro nel componente figlio.
* L'evento `onclick` viene utilizzato `ChangeTheYear` per attivare il metodo. Per altre informazioni, vedere <xref:blazor/event-handling>.

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

Il `ParentComponent` caricamento del prodotto produce il markup seguente:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Se il valore `ParentYear` della proprietà viene modificato `ParentComponent`selezionando `Year` il pulsante nella proprietà , la proprietà dell'oggetto `ChildComponent` viene aggiornata. Il nuovo `Year` valore di viene eseguito il rendering nell'interfaccia utente quando viene eseguito nuovamente il rendering dell'oggetto: `ParentComponent`

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Il `Year` parametro è associabile `YearChanged` perché dispone di un `Year` evento complementare che corrisponde al tipo del parametro.

Per convenzione, `<ChildComponent @bind-Year="ParentYear" />` è essenzialmente equivalente alla scrittura:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

In generale, una proprietà può essere associata `@bind-{PROPRETY}:event` a un gestore eventi corrispondente includendo un attributo. Ad esempio, `MyProp` la proprietà `MyEventHandler` può essere associata all'utilizzo dei due attributi seguenti:For example, the property can be bound to using the following two attributes:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Associazione da figlio a padre con associazione concatenata

Uno scenario comune è il concatenamento di un parametro con associazione a dati a un elemento di pagina nell'output del componente. Questo scenario è denominato *associazione concatenata* perché più livelli di associazione si verificano contemporaneamente.

Un'associazione concatenata `@bind` non può essere implementata con la sintassi nell'elemento della pagina. Il gestore eventi e il valore devono essere specificati separatamente. Un componente padre, tuttavia, può utilizzare `@bind` la sintassi con il parametro del componente.

Il `PasswordField` componente seguente (*PasswordField.razor*):

* Imposta `<input>` il valore di `Password` un elemento su una proprietà.
* Espone le modifiche `Password` della proprietà a un componente padre con [un oggetto EventCallback](xref:blazor/event-handling#eventcallback).
* Utilizza `onclick` l'evento viene `ToggleShowPassword` utilizzato per attivare il metodo. Per altre informazioni, vedere <xref:blazor/event-handling>.

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

Il `PasswordField` componente viene utilizzato in un altro componente:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

Per eseguire controlli o intercettare gli errori sulla password nell'esempio precedente:

* Creare un campo `Password` di`_password` backup per ( nel codice di esempio seguente).
* Eseguire i controlli o `Password` gli errori di intrappolare nel setter.

L'esempio seguente fornisce un feedback immediato all'utente se viene utilizzato uno spazio nel valore della password:The following example provides immediate feedback to the user if a space is used in the password's value:

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

Per informazioni sull'associazione ai <xref:blazor/forms-validation#work-with-radio-buttons>pulsanti di opzione in un modulo, vedere .
