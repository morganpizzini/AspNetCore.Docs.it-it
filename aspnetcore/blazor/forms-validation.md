---
title: ASP.NET Core Blazor moduli e convalida
author: guardrex
description: Informazioni su come usare i moduli e gli scenari di convalida di campi in Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/27/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 557ccf45fb88a743158d18046789ed6776efb28c
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84145356"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core Blazor moduli e convalida

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

I moduli e la convalida sono supportati nell' Blazor utilizzo delle [annotazioni dei dati](xref:mvc/models/validation).

Il `ExampleModel` tipo seguente definisce la logica di convalida usando le annotazioni dei dati:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Un modulo viene definito usando il <xref:Microsoft.AspNetCore.Components.Forms.EditForm> componente. Il modulo seguente illustra elementi, componenti e codice tipici Razor :

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

Nell'esempio precedente:

* Il modulo convalida l'input dell'utente nel `name` campo usando la convalida definita nel `ExampleModel` tipo. Il modello viene creato nel blocco del componente `@code` e viene mantenuto in un campo privato ( `exampleModel` ). Il campo viene assegnato all' `Model` attributo dell' `<EditForm>` elemento.
* Le <xref:Microsoft.AspNetCore.Components.Forms.InputText> associazioni del componente `@bind-Value` :
  * Proprietà del modello ( `exampleModel.Name` ) della <xref:Microsoft.AspNetCore.Components.Forms.InputText> proprietà del componente `Value` . Per ulteriori informazioni sull'associazione di proprietà, vedere <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters> .
  * Delegato dell'evento di modifica alla <xref:Microsoft.AspNetCore.Components.Forms.InputText> proprietà del componente `ValueChanged` .
* Il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente connette il supporto di convalida usando le annotazioni dei dati.
* Il <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente riepiloga i messaggi di convalida.
* `HandleValidSubmit`viene attivato quando il form viene inviato correttamente (supera la convalida).

È disponibile un set di componenti di input predefiniti per la ricezione e la convalida dell'input dell'utente. Gli input vengono convalidati quando vengono modificati e quando viene inviato un modulo. I componenti di input disponibili sono illustrati nella tabella seguente.

| Componente di input | Con rendering come&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

Tutti i componenti di input, tra cui <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , supportano attributi arbitrari. Qualsiasi attributo che non corrisponde a un parametro component viene aggiunto all'elemento HTML sottoposto a rendering.

I componenti di input forniscono il comportamento predefinito per la convalida in base alla modifica e alla modifica della classe CSS in modo da riflettere lo stato del campo. Alcuni componenti includono una logica di analisi utile. E, ad esempio, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> gestire i valori non analizzabili con la registrazione normale come errori di convalida. I tipi che possono accettare valori null supportano anche il supporto di valori null del campo di destinazione, ad esempio `int?` .

Il `Starship` tipo seguente definisce la logica di convalida usando un set di proprietà e annotazioni di dati più ampio rispetto a quello precedente `ExampleModel` :

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

Nell'esempio precedente `Description` è facoltativo perché non sono presenti annotazioni di dati.

Il form seguente convalida l'input dell'utente utilizzando la convalida definita nel `Starship` modello:

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>Crea un oggetto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> come [valore](xref:blazor/components#cascading-values-and-parameters) di propagazione che tiene traccia dei metadati relativi al processo di modifica, inclusi i campi modificati e i messaggi di convalida correnti. <xref:Microsoft.AspNetCore.Components.Forms.EditForm>Fornisce inoltre eventi pratici per invii validi e non validi ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> , <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> ). In alternativa, usare <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> per attivare la convalida e verificare i valori dei campi con il codice di convalida personalizzato.

Nell'esempio seguente:

* Il `HandleSubmit` metodo viene eseguito quando viene selezionato il pulsante **Invia** .
* Il modulo viene convalidato usando il modulo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .
* Il form viene ulteriormente convalidato passando al <xref:Microsoft.AspNetCore.Components.Forms.EditContext> `ServerValidate` metodo che chiama un endpoint dell'API Web nel server (*non illustrato*).
* Il codice aggiuntivo viene eseguito a seconda del risultato della convalida lato client e lato server verificando `isValid` .

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a>InputText basato sull'evento di input

Utilizzare il <xref:Microsoft.AspNetCore.Components.Forms.InputText> componente per creare un componente personalizzato che utilizza l' `input` evento anziché l' `change` evento.

Creare un componente con il markup seguente e usare il componente Analogamente a quanto <xref:Microsoft.AspNetCore.Components.Forms.InputText> viene usato:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Usare i pulsanti di opzione

Quando si utilizzano pulsanti di opzione in un modulo, data binding viene gestito in modo diverso rispetto ad altri elementi perché i pulsanti di opzione vengono valutati come un gruppo. Il valore di ogni pulsante di opzione è fisso, ma il valore del gruppo di pulsanti di opzione è il valore del pulsante di opzione selezionato. L'esempio seguente illustra come:

* Gestire data binding per un gruppo di pulsanti di opzione.
* Supportare la convalida usando un `InputRadio` componente personalizzato.

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

Nell'esempio seguente <xref:Microsoft.AspNetCore.Components.Forms.EditForm> viene utilizzato il `InputRadio` componente precedente per ottenere e convalidare una classificazione dall'utente:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a>Supporto della convalida

Il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente connette il supporto di convalida usando le annotazioni dei dati a a cascata <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . L'abilitazione del supporto per la convalida usando le annotazioni dei dati richiede questo movimento esplicito. Per usare un sistema di convalida diverso rispetto alle annotazioni dei dati, sostituire <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> con un'implementazione personalizzata. L'implementazione del ASP.NET Core è disponibile per l'ispezione nell'origine riferimento: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazoresegue due tipi di convalida:

* La *convalida dei campi* viene eseguita quando l'utente esce da un campo. Durante la convalida dei campi, il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente associa tutti i risultati della convalida segnalati al campo.
* La *convalida del modello* viene eseguita quando l'utente invia il modulo. Durante la convalida del modello, il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente tenta di determinare il campo in base al nome del membro segnalato dal risultato della convalida. I risultati della convalida che non sono associati a un singolo membro sono associati al modello anziché a un campo.

### <a name="validation-summary-and-validation-message-components"></a>Componenti del messaggio di convalida e di riepilogo della convalida

Il <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente riepiloga tutti i messaggi di convalida, che è simile all' [Helper tag del riepilogo di convalida](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Messaggi di convalida dell'output per un modello specifico con il `Model` parametro:
  
```razor
<ValidationSummary Model="@starship" />
```

Il <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> componente Visualizza i messaggi di convalida per un campo specifico, che è simile all' [Helper tag del messaggio di convalida](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Specificare il campo per la convalida con l' `For` attributo e un'espressione lambda che denomina la proprietà del modello:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

I <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componenti e supportano attributi arbitrari. Qualsiasi attributo che non corrisponde a un parametro component viene aggiunto all' `<div>` elemento generato o `<ul>` .

### <a name="custom-validation-attributes"></a>Attributi di convalida personalizzati

Per assicurarsi che un risultato di convalida venga associato correttamente a un campo quando si usa un [attributo di convalida personalizzato](xref:mvc/models/validation#custom-attributes), passare il contesto di convalida <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> durante la creazione di <xref:System.ComponentModel.DataAnnotations.ValidationResult> :

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="blazor-data-annotations-validation-package"></a>Blazorpacchetto di convalida delle annotazioni dei dati

[Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) è un pacchetto che colma i gap dell'esperienza di convalida usando il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente. Il pacchetto è attualmente *sperimentale*.

### <a name="compareproperty-attribute"></a>Attributo [CompareProperty]

<xref:System.ComponentModel.DataAnnotations.CompareAttribute>Non funziona bene con il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente perché non associa il risultato della convalida a un membro specifico. Ciò può comportare un comportamento incoerente tra la convalida a livello di campo e quando l'intero modello viene convalidato in un invio. Il pacchetto *sperimentale* [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) introduce un attributo di convalida aggiuntivo, `ComparePropertyAttribute` , che aggira queste limitazioni. In un' Blazor applicazione, `[CompareProperty]` è una sostituzione diretta per l' [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attributo.

### <a name="nested-models-collection-types-and-complex-types"></a>Modelli annidati, tipi di raccolta e tipi complessi

Blazorfornisce supporto per la convalida dell'input del form utilizzando le annotazioni dei dati con l'oggetto incorporato <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . Tuttavia, l'oggetto <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> convalida solo le proprietà di primo livello del modello associate al form che non sono proprietà di tipo raccolta o complesso.

Per convalidare l'intero grafico di oggetti del modello associato, incluse le proprietà della raccolta e del tipo complesso, usare l'oggetto `ObjectGraphDataAnnotationsValidator` fornito dal pacchetto *sperimentale* [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Annotare le proprietà del modello con `[ValidateComplexType]` . Nelle classi di modelli seguenti la `ShipDescription` classe contiene annotazioni di dati aggiuntive da convalidare quando il modello è associato al form:

*Starship.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

*ShipDescription.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>Abilita il pulsante Invia in base alla convalida del modulo

Per abilitare e disabilitare il pulsante Invia in base alla convalida del modulo:

* Utilizzare il modulo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> per assegnare il modello quando il componente viene inizializzato.
* Convalidare il form nel callback del contesto <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> per abilitare e disabilitare il pulsante Submit.
* Sganciare il gestore eventi nel `Dispose` metodo. Per altre informazioni, vedere <xref:blazor/lifecycle#component-disposal-with-idisposable>.

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

Nell'esempio precedente, impostare `formInvalid` su `false` if:

* Il modulo viene precaricato con valori predefiniti validi.
* Si desidera attivare il pulsante Invia quando il modulo viene caricato.

Un effetto collaterale dell'approccio precedente è che un <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente viene popolato con campi non validi dopo che l'utente interagisce con un qualsiasi campo. Questo scenario può essere risolto in uno dei modi seguenti:

* Non usare un <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente nel form.
* Rendere <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> visibile il componente quando viene selezionato il pulsante Invia, ad esempio in un `HandleValidSubmit` metodo.

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a>Risolvere problemi

> InvalidOperationException: EditForm richiede un parametro di modello o un parametro EditContext, ma non entrambi.

Verificare che <xref:Microsoft.AspNetCore.Components.Forms.EditForm> disponga di un oggetto <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> o <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .

Quando si assegna un <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> al form, verificare che venga creata un'istanza del tipo di modello, come illustrato nell'esempio seguente:

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
