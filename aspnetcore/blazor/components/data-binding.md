---
title: ASP.NET Core Blazor Data Binding
author: guardrex
description: Informazioni sulle funzionalità di data binding per i componenti e gli elementi DOM nelle Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/data-binding
ms.openlocfilehash: 3b41aedcbd0d2c22b20d8fa3a21b8af97d1fbb2c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628560"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a>ASP.NET Core Blazor Data Binding

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Razor i componenti di forniscono funzionalità di data binding tramite un attributo dell'elemento HTML denominato [`@bind`](xref:mvc/views/razor#bind) con un campo, una proprietà o un Razor valore di espressione.

Nell'esempio seguente viene associato un `<input>` elemento al `currentValue` campo e un `<input>` elemento alla `CurrentValue` proprietà:

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

Quando uno degli elementi perde lo stato attivo, viene aggiornato il campo o la proprietà associata.

La casella di testo viene aggiornata nell'interfaccia utente solo quando viene eseguito il rendering del componente, non in risposta alla modifica del valore della proprietà o del campo. Poiché i componenti eseguono il rendering autonomamente dopo l'esecuzione del codice del gestore eventi, gli aggiornamenti dei campi e delle proprietà vengono in *genere* riflessi nell'interfaccia utente immediatamente dopo l'attivazione di un gestore eventi.

L'utilizzo [`@bind`](xref:mvc/views/razor#bind) di con la `CurrentValue` Proprietà ( `<input @bind="CurrentValue" />` ) equivale essenzialmente a quanto segue:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

Quando viene eseguito il rendering del componente, l'oggetto `value` dell'elemento di input deriva dalla `CurrentValue` Proprietà. Quando l'utente digita nella casella di testo e modifica lo stato attivo dell'elemento, `onchange` viene generato l'evento e la `CurrentValue` proprietà viene impostata sul valore modificato. In realtà, la generazione del codice è più complessa rispetto a [`@bind`](xref:mvc/views/razor#bind) quando vengono gestiti i casi in cui vengono eseguite le conversioni dei tipi. In linea di principio, [`@bind`](xref:mvc/views/razor#bind) associa il valore corrente di un'espressione a un `value` attributo e gestisce le modifiche utilizzando il gestore registrato.

Associare una proprietà o un campo ad altri eventi includendo anche un `@bind:event` attributo con un `event` parametro. Nell'esempio seguente viene associata la `CurrentValue` proprietà nell' `oninput` evento:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Diversamente da `onchange` , che viene attivato quando l'elemento perde lo stato attivo, `oninput` viene attivato quando viene modificato il valore della casella di testo.

Utilizzare `@bind-{ATTRIBUTE}` con la `@bind-{ATTRIBUTE}:event` sintassi per associare attributi di elementi diversi da `value` . Nell'esempio seguente:

* Lo stile del paragrafo è **rosso** quando il componente viene caricato ( `style="color:red"` ).
* L'utente modifica il valore della casella di testo in modo da riflettere uno stile di colore CSS diverso e modifica lo stato attivo dell'elemento della pagina. Ad esempio, l'utente modifica il valore della casella di testo in `color:blue` e preme il tasto <kbd>Tab</kbd> sulla tastiera.
* Quando viene modificato lo stato attivo dell'elemento:
  * Il valore di `paragraphStyle` viene assegnato dal `<input>` valore dell'elemento.
  * Lo stile del paragrafo viene aggiornato in modo da riflettere il nuovo stile in `paragraphStyle` . Se lo stile viene aggiornato a `color:blue` , il colore del testo diventa **blu**.

```razor
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

L'associazione di attributi distingue tra maiuscole e minuscole:

* `@bind` è valido.
* `@Bind` e `@BIND` non sono validi.

## <a name="unparsable-values"></a>Valori non analizzabili

Quando un utente fornisce un valore non analizzabile a un elemento associato a un oggetto DataBound, il valore non analizzabile viene automaticamente ripristinato al valore precedente quando viene attivato l'evento bind.

Si consideri lo scenario seguente:

* Un `<input>` elemento è associato a un `int` tipo con un valore iniziale di `123` :

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* L'utente aggiorna il valore dell'elemento a `123.45` nella pagina e modifica lo stato attivo dell'elemento.

Nello scenario precedente, il valore dell'elemento viene ripristinato in `123` . Quando il valore `123.45` viene rifiutato a favore del valore originale di `123` , l'utente riconosce che il relativo valore non è stato accettato.

Per impostazione predefinita, l'associazione si applica all' `onchange` evento dell'elemento ( `@bind="{PROPERTY OR FIELD}"` ). Utilizzare `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` per attivare l'associazione in un evento diverso. Per l' `oninput` evento ( `@bind:event="oninput"` ), la riversione viene eseguita dopo qualsiasi sequenza di tasti che introduce un valore non analizzabile. Quando la destinazione `oninput` è l'evento con un `int` tipo associato a, a un utente viene impedito di digitare un `.` carattere. Un `.` carattere viene immediatamente rimosso, quindi l'utente riceve il feedback immediato che sono consentiti solo numeri interi. Esistono scenari in cui il ripristino del valore dell' `oninput` evento non è ideale, ad esempio quando l'utente deve essere autorizzato a cancellare un valore non analizzabile `<input>` . Le alternative includono:

* Non usare l' `oninput` evento. Usare l' `onchange` evento predefinito (specificare solo `@bind="{PROPERTY OR FIELD}"` ), in cui un valore non valido non viene ripristinato fino a quando l'elemento non perde lo stato attivo.
* Eseguire l'associazione a un tipo nullable, ad esempio `int?` o, `string` e fornire la logica personalizzata per gestire le voci non valide.
* Utilizzare un [componente di convalida del modulo](xref:blazor/forms-validation), ad esempio <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> o <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> . I componenti di convalida dei moduli includono il supporto predefinito per la gestione di input non validi. Per altre informazioni, vedere <xref:blazor/forms-validation>. Componenti di convalida dei moduli:
  * Consente all'utente di fornire un input non valido e di ricevere errori di convalida nell'oggetto associato <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .
  * Visualizzare gli errori di convalida nell'interfaccia utente senza interferire con l'utente che immette dati Web Form aggiuntivi.

## <a name="format-strings"></a>Stringhe di formato

Il data binding funziona con <xref:System.DateTime> stringhe di formato usando `@bind:format` . Altre espressioni di formato, ad esempio i formati di valuta o numerici, non sono disponibili in questo momento.

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

Nel codice precedente, il `<input>` valore predefinito del tipo di campo dell'elemento ( `type` ) è `text` . `@bind:format` è supportato per l'associazione dei tipi .NET seguenti:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

L' `@bind:format` attributo specifica il formato di data da applicare all'oggetto `value` dell' `<input>` elemento. Il formato viene usato anche per analizzare il valore quando `onchange` si verifica un evento.

Non è consigliabile specificare un formato per il `date` tipo di campo perché Blazor include il supporto incorporato per formattare le date. A prescindere dalla raccomandazione, utilizzare solo il `yyyy-MM-dd` formato data per il binding per funzionare correttamente se viene fornito un formato con il `date` tipo di campo:

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Associazione da padre a figlio con parametri dei componenti

I parametri del componente consentono di associare proprietà e campi di un componente padre con la `@bind-{PROPERTY OR FIELD}` sintassi.

Il `Child` componente seguente ( `Child.razor` ) ha un `Year` parametro del componente e un `YearChanged` callback:

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
        <p>
            <button @onclick="UpdateYear">
                Update Child <code>Year</code> and call 
                <code>YearChanged.InvokeAsync(Year)</code>
            </button>
        </p>
    </div>
</div>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private Task UpdateYear()
    {
        Year = r.Next(10050, 12021);

        return YearChanged.InvokeAsync(Year);
    }
}
```

Il callback ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) deve essere denominato come nome del parametro del componente seguito dal `Changed` suffisso "" ( `{PARAMETER NAME}Changed` ). Nell'esempio precedente, il callback è denominato `YearChanged` . Per altre informazioni su <xref:Microsoft.AspNetCore.Components.EventCallback%601>, vedere <xref:blazor/components/event-handling#eventcallback>.

Nel componente seguente `Parent` ( `Parent.razor` ) il `year` campo è associato al `Year` parametro del componente figlio:

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1978;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

Il `Year` parametro è associabile perché contiene un evento complementare `YearChanged` che corrisponde al tipo del `Year` parametro.

Per convenzione, una proprietà può essere associata a un gestore eventi corrispondente includendo un `@bind-{PROPERTY}:event` attributo assegnato al gestore. `<Child @bind-Year="year" />` equivale alla scrittura:

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Associazione da figlio a padre con binding concatenato

Uno scenario comune consiste nel concatenare un parametro associato a dati a un elemento Page nell'output del componente. Questo scenario è denominato *Binding concatenato* perché si verificano simultaneamente più livelli di associazione.

Un binding concatenato non può essere implementato con [`@bind`](xref:mvc/views/razor#bind) la sintassi nel componente figlio. Il gestore eventi e il valore devono essere specificati separatamente. Un componente padre, tuttavia, può utilizzare [`@bind`](xref:mvc/views/razor#bind) la sintassi con il parametro del componente figlio.

Il `PasswordField` componente seguente ( `PasswordField.razor` ):

* Imposta `<input>` il valore di un elemento su una `Password` Proprietà.
* Espone le modifiche della `Password` proprietà a un componente padre con un oggetto [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .
* Usa l' `onclick` evento per attivare il `ToggleShowPassword` metodo. Per altre informazioni, vedere <xref:blazor/components/event-handling>.

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

Il `PasswordField` componente viene usato in un altro componente:

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Per eseguire controlli o intercettare gli errori sulla password nell'esempio precedente:

* Creare un campo di supporto per `Password` ( `password` nell'esempio di codice seguente).
* Eseguire i controlli o gli errori di trap nel `Password` Setter.

Nell'esempio seguente viene fornito un feedback immediato all'utente se viene utilizzato uno spazio nel valore della password:

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

## <a name="additional-resources"></a>Risorse aggiuntive

* [Associazione ai pulsanti di opzione in un modulo](xref:blazor/forms-validation#radio-buttons)
* [Associazione `<select>` di opzioni di elementi a valori di oggetti C# `null` in un form](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
