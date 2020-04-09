---
title: ASP.NET Blazor core e convalida
author: guardrex
description: Informazioni su come utilizzare moduli Blazore scenari di convalida dei campi in .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 0359a9337860d9b8ce0b81d8833a034a898b05a5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218960"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET i moduli e la convalida di Core Blazor

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

I moduli e la convalida sono supportati in Blazor utilizzando [le annotazioni dei dati.](xref:mvc/models/validation)

Il `ExampleModel` tipo seguente definisce la logica di convalida utilizzando le annotazioni dei dati:The following type defines validation logic using data annotations:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Un modulo viene `EditForm` definito utilizzando il componente. Nel formato seguente vengono illustrati gli elementi, i componenti e il codice Razor tipici:

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

Nell'esempio precedente:

* Il modulo convalida l'input dell'utente `name` nel `ExampleModel` campo utilizzando la convalida definita nel tipo. Il modello viene creato nel `@code` blocco del componente e`_exampleModel`conservato in un campo privato ( ). Il campo viene `Model` assegnato all'attributo dell'elemento. `<EditForm>`
* Il `InputText` componente `@bind-Value` è associato:
  * Proprietà del`_exampleModel.Name`modello ( `InputText` ) `Value` alla proprietà del componente.
  * Delegato dell'evento `InputText` di modifica `ValueChanged` alla proprietà del componente.
* Il `DataAnnotationsValidator` componente associa la convalida supporta l'utilizzo delle annotazioni dei dati.
* Il `ValidationSummary` componente riepiloga i messaggi di convalida.
* `HandleValidSubmit`viene attivato quando il modulo viene inviato correttamente (supera la convalida).

È disponibile un set di componenti di input incorporati per ricevere e convalidare l'input dell'utente. Gli input vengono convalidati quando vengono modificati e quando viene inviato un modulo. I componenti di input disponibili sono riportati nella tabella seguente.

| Componente di ingresso | Rendering come&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Tutti i componenti di `EditForm`input, inclusi , supportano attributi arbitrari. Qualsiasi attributo che non corrisponde a un parametro del componente viene aggiunto all'elemento HTML di cui è stato eseguito il rendering.

I componenti di input forniscono il comportamento predefinito per la convalida in seguito alla modifica e alla modifica della relativa classe CSS in modo da riflettere lo stato del campo. Alcuni componenti includono un'utile logica di analisi. Ad `InputDate` esempio, `InputNumber` e gestire i valori non analizzabili normalmente registrandoli come errori di convalida. I tipi che possono accettare valori null supportano anche `int?`il supporto di valori Null del campo di destinazione, ad esempio .

Il `Starship` tipo seguente definisce la logica di convalida utilizzando un `ExampleModel`set più ampio di proprietà e annotazioni dei dati rispetto al precedente:

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

Nell'esempio precedente, `Description` è facoltativo perché non sono presenti annotazioni dei dati.

Il modulo seguente convalida l'input dell'utente utilizzando la convalida definita nel `Starship` modello:

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
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
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
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
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

Viene `EditForm` creato `EditContext` un [come valore a catena](xref:blazor/components#cascading-values-and-parameters) che tiene traccia dei metadati relativi al processo di modifica, inclusi i campi modificati e i messaggi di convalida correnti. Fornisce `EditForm` inoltre eventi convenienti per gli`OnValidSubmit` `OnInvalidSubmit`invii validi e non validi ( , ). In alternativa, `OnSubmit` utilizzare per attivare la convalida e controllare i valori dei campi con codice di convalida personalizzato.

Nell'esempio seguente:

* Il `HandleSubmit` metodo viene eseguito quando viene selezionato il pulsante **Invia.**
* Il modulo viene convalidato utilizzando `EditContext`il file .
* Il form viene ulteriormente `EditContext` convalidato `ServerValidate` passando l'oggetto al metodo che chiama un endpoint API Web sul server *(non mostrato*).
* Il codice aggiuntivo viene eseguito in base al risultato `isValid`della convalida lato client e lato server controllando .

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

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

## <a name="inputtext-based-on-the-input-event"></a>InputText in base all'evento di input

Utilizzare `InputText` il componente per creare un `input` componente personalizzato `change` che utilizza l'evento anziché l'evento.

Creare un componente con la marcatura seguente `InputText` e utilizzare il componente come viene utilizzato:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Utilizzare i pulsanti di opzione

Quando si utilizzano i pulsanti di opzione in un form, l'associazione dati viene gestita in modo diverso rispetto ad altri elementi perché i pulsanti di opzione vengono valutati come gruppo. Il valore di ogni pulsante di opzione è fisso, ma il valore del gruppo di pulsanti di opzione è il valore del pulsante di opzione selezionato. L'esempio seguente illustra come:

* Gestire l'associazione dati per un gruppo di pulsanti di opzione.
* Supportare la `InputRadio` convalida utilizzando un componente personalizzato.

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

Di `EditForm` seguito viene `InputRadio` utilizzato il componente precedente per ottenere e convalidare una classificazione dall'utente:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

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

## <a name="validation-support"></a>Supporto di convalida

Il `DataAnnotationsValidator` componente associa il supporto di convalida tramite annotazioni dei dati all'oggetto in cascata. `EditContext` L'abilitazione del supporto per la convalida tramite annotazioni dei dati richiede questo gesto esplicito. Per utilizzare un sistema di convalida diverso `DataAnnotationsValidator` rispetto alle annotazioni dei dati, sostituire il con un'implementazione personalizzata. L'implementazione ASP.NET Core è disponibile per l'ispezione nell'origine di riferimento: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazoresegue due tipi di convalida:

* *La convalida* del campo viene eseguita quando l'utente esce da un campo. Durante la convalida `DataAnnotationsValidator` del campo, il componente associa tutti i risultati della convalida riportata al campo.
* *La convalida* del modello viene eseguita quando l'utente invia il modulo. Durante la convalida `DataAnnotationsValidator` del modello, il componente tenta di determinare il campo in base al nome del membro segnalato dal risultato della convalida. I risultati della convalida non associati a un singolo membro sono associati al modello anziché a un campo.

### <a name="validation-summary-and-validation-message-components"></a>Componenti di Riepilogo di convalida e Messaggio di convalida

Il `ValidationSummary` componente riepiloga tutti i messaggi di convalida, che è simile al [convalida Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Messaggi di convalida dell'output `Model` per un modello specifico con il parametro:
  
```razor
<ValidationSummary Model="@_starship" />
```

Il `ValidationMessage` componente visualizza i messaggi di convalida per un campo specifico, che è simile a [Convalida Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Specificare il campo `For` per la convalida con l'attributo e un'espressione lambda che denomina la proprietà del modello:

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

I `ValidationMessage` `ValidationSummary` componenti e supportano attributi arbitrari. Qualsiasi attributo che non corrisponde a un `<div>` parametro del componente viene aggiunto all'elemento o `<ul>` alla generazione.

### <a name="custom-validation-attributes"></a>Attributi di convalida personalizzati

Per assicurarsi che un risultato di convalida sia associato correttamente a <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> un campo <xref:System.ComponentModel.DataAnnotations.ValidationResult>quando si utilizza un attributo di [convalida personalizzato,](xref:mvc/models/validation#custom-attributes)passare il contesto di convalida durante la creazione del comando :

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazorpacchetto di convalida delle annotazioni dei dati

[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) è un pacchetto che colma le `DataAnnotationsValidator` lacune nell'esperienza di convalida utilizzando il componente. Il pacchetto è attualmente *sperimentale.*

### <a name="compareproperty-attribute"></a>[CompareProperty] (attributo)

Il <xref:System.ComponentModel.DataAnnotations.CompareAttribute> non funziona bene `DataAnnotationsValidator` con il componente perché non associa il risultato della convalida a un membro specifico. Ciò può causare un comportamento incoerente tra la convalida a livello di campo e quando l'intero modello viene convalidato in un invio. Il pacchetto *sperimentale* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) introduce `ComparePropertyAttribute`un attributo di convalida aggiuntivo, , che aggira queste limitazioni. In Blazor un'app, `[CompareProperty]` è una `[Compare]` sostituzione diretta per l'attributo.

### <a name="nested-models-collection-types-and-complex-types"></a>Modelli annidati, tipi di raccolta e tipi complessiNested models, collection types, and complex types

Blazorfornisce supporto per la convalida dell'input del `DataAnnotationsValidator`modulo utilizzando le annotazioni dei dati con l'oggetto incorporato in . Tuttavia, `DataAnnotationsValidator` l'unico convalida le proprietà di primo livello del modello associate al form che non sono proprietà di tipo raccolta o complesso.

Per convalidare l'intero object graph del modello associato, incluse `ObjectGraphDataAnnotationsValidator` le proprietà di tipo complesso e di raccolta, utilizzare il pacchetto di [microsoft.AspNetCore.Components.DataAnnotations.Validation fornito dal pacchetto sperimentale Microsoft.AspNetCore.Components.DataAnnotations.Validation:](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental*

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Annotare le `[ValidateComplexType]`proprietà del modello con . Nelle classi del modello `ShipDescription` seguenti, la classe contiene annotazioni di dati aggiuntive da convalidare quando il modello è associato al form:

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>Abilitare il pulsante di invio in base alla convalida del modulo

Per abilitare e disabilitare il pulsante di invio in base alla convalida del modulo:

* Utilizzare i moduli `EditContext` per assegnare il modello quando il componente viene inizializzato.
* Convalidare il modulo nel `OnFieldChanged` callback del contesto per abilitare e disabilitare il pulsante di invio.
* Annullare l'hook `Dispose` del gestore eventi nel metodo. Per altre informazioni, vedere <xref:blazor/lifecycle#component-disposal-with-idisposable>.

```razor
@implements IDisposable

<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
        _editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        _formInvalid = !_editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        _editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

Nell'esempio precedente, `_formInvalid` impostare su `false` if:

* Il modulo viene precaricato con valori predefiniti validi.
* Si desidera attivare il pulsante di invio al caricamento del modulo.

Un effetto collaterale dell'approccio `ValidationSummary` precedente è che un componente viene popolato con campi non validi dopo che l'utente interagisce con qualsiasi campo. Questo scenario può essere affrontato in uno dei seguenti modi:

* Non utilizzare un `ValidationSummary` componente nel modulo.
* Rendere `ValidationSummary` il componente visibile quando è selezionato il `HandleValidSubmit` pulsante di invio (ad esempio, in un metodo).

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```
