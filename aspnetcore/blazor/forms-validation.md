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
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="7f691-103">ASP.NET i moduli e la convalida di Core Blazor</span><span class="sxs-lookup"><span data-stu-id="7f691-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="7f691-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7f691-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7f691-105">I moduli e la convalida sono supportati in Blazor utilizzando [le annotazioni dei dati.](xref:mvc/models/validation)</span><span class="sxs-lookup"><span data-stu-id="7f691-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="7f691-106">Il `ExampleModel` tipo seguente definisce la logica di convalida utilizzando le annotazioni dei dati:The following type defines validation logic using data annotations:</span><span class="sxs-lookup"><span data-stu-id="7f691-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="7f691-107">Un modulo viene `EditForm` definito utilizzando il componente.</span><span class="sxs-lookup"><span data-stu-id="7f691-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="7f691-108">Nel formato seguente vengono illustrati gli elementi, i componenti e il codice Razor tipici:</span><span class="sxs-lookup"><span data-stu-id="7f691-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="7f691-109">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="7f691-109">In the preceding example:</span></span>

* <span data-ttu-id="7f691-110">Il modulo convalida l'input dell'utente `name` nel `ExampleModel` campo utilizzando la convalida definita nel tipo.</span><span class="sxs-lookup"><span data-stu-id="7f691-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="7f691-111">Il modello viene creato nel `@code` blocco del componente e`_exampleModel`conservato in un campo privato ( ).</span><span class="sxs-lookup"><span data-stu-id="7f691-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="7f691-112">Il campo viene `Model` assegnato all'attributo dell'elemento. `<EditForm>`</span><span class="sxs-lookup"><span data-stu-id="7f691-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="7f691-113">Il `InputText` componente `@bind-Value` è associato:</span><span class="sxs-lookup"><span data-stu-id="7f691-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="7f691-114">Proprietà del`_exampleModel.Name`modello ( `InputText` ) `Value` alla proprietà del componente.</span><span class="sxs-lookup"><span data-stu-id="7f691-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="7f691-115">Delegato dell'evento `InputText` di modifica `ValueChanged` alla proprietà del componente.</span><span class="sxs-lookup"><span data-stu-id="7f691-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="7f691-116">Il `DataAnnotationsValidator` componente associa la convalida supporta l'utilizzo delle annotazioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="7f691-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="7f691-117">Il `ValidationSummary` componente riepiloga i messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="7f691-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="7f691-118">`HandleValidSubmit`viene attivato quando il modulo viene inviato correttamente (supera la convalida).</span><span class="sxs-lookup"><span data-stu-id="7f691-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="7f691-119">È disponibile un set di componenti di input incorporati per ricevere e convalidare l'input dell'utente.</span><span class="sxs-lookup"><span data-stu-id="7f691-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="7f691-120">Gli input vengono convalidati quando vengono modificati e quando viene inviato un modulo.</span><span class="sxs-lookup"><span data-stu-id="7f691-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="7f691-121">I componenti di input disponibili sono riportati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="7f691-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="7f691-122">Componente di ingresso</span><span class="sxs-lookup"><span data-stu-id="7f691-122">Input component</span></span> | <span data-ttu-id="7f691-123">Rendering come&hellip;</span><span class="sxs-lookup"><span data-stu-id="7f691-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="7f691-124">Tutti i componenti di `EditForm`input, inclusi , supportano attributi arbitrari.</span><span class="sxs-lookup"><span data-stu-id="7f691-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="7f691-125">Qualsiasi attributo che non corrisponde a un parametro del componente viene aggiunto all'elemento HTML di cui è stato eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="7f691-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="7f691-126">I componenti di input forniscono il comportamento predefinito per la convalida in seguito alla modifica e alla modifica della relativa classe CSS in modo da riflettere lo stato del campo.</span><span class="sxs-lookup"><span data-stu-id="7f691-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="7f691-127">Alcuni componenti includono un'utile logica di analisi.</span><span class="sxs-lookup"><span data-stu-id="7f691-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="7f691-128">Ad `InputDate` esempio, `InputNumber` e gestire i valori non analizzabili normalmente registrandoli come errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="7f691-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="7f691-129">I tipi che possono accettare valori null supportano anche `int?`il supporto di valori Null del campo di destinazione, ad esempio .</span><span class="sxs-lookup"><span data-stu-id="7f691-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="7f691-130">Il `Starship` tipo seguente definisce la logica di convalida utilizzando un `ExampleModel`set più ampio di proprietà e annotazioni dei dati rispetto al precedente:</span><span class="sxs-lookup"><span data-stu-id="7f691-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="7f691-131">Nell'esempio precedente, `Description` è facoltativo perché non sono presenti annotazioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="7f691-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="7f691-132">Il modulo seguente convalida l'input dell'utente utilizzando la convalida definita nel `Starship` modello:</span><span class="sxs-lookup"><span data-stu-id="7f691-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="7f691-133">Viene `EditForm` creato `EditContext` un [come valore a catena](xref:blazor/components#cascading-values-and-parameters) che tiene traccia dei metadati relativi al processo di modifica, inclusi i campi modificati e i messaggi di convalida correnti.</span><span class="sxs-lookup"><span data-stu-id="7f691-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="7f691-134">Fornisce `EditForm` inoltre eventi convenienti per gli`OnValidSubmit` `OnInvalidSubmit`invii validi e non validi ( , ).</span><span class="sxs-lookup"><span data-stu-id="7f691-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="7f691-135">In alternativa, `OnSubmit` utilizzare per attivare la convalida e controllare i valori dei campi con codice di convalida personalizzato.</span><span class="sxs-lookup"><span data-stu-id="7f691-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="7f691-136">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="7f691-136">In the following example:</span></span>

* <span data-ttu-id="7f691-137">Il `HandleSubmit` metodo viene eseguito quando viene selezionato il pulsante **Invia.**</span><span class="sxs-lookup"><span data-stu-id="7f691-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="7f691-138">Il modulo viene convalidato utilizzando `EditContext`il file .</span><span class="sxs-lookup"><span data-stu-id="7f691-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="7f691-139">Il form viene ulteriormente `EditContext` convalidato `ServerValidate` passando l'oggetto al metodo che chiama un endpoint API Web sul server *(non mostrato*).</span><span class="sxs-lookup"><span data-stu-id="7f691-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="7f691-140">Il codice aggiuntivo viene eseguito in base al risultato `isValid`della convalida lato client e lato server controllando .</span><span class="sxs-lookup"><span data-stu-id="7f691-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="7f691-141">InputText in base all'evento di input</span><span class="sxs-lookup"><span data-stu-id="7f691-141">InputText based on the input event</span></span>

<span data-ttu-id="7f691-142">Utilizzare `InputText` il componente per creare un `input` componente personalizzato `change` che utilizza l'evento anziché l'evento.</span><span class="sxs-lookup"><span data-stu-id="7f691-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="7f691-143">Creare un componente con la marcatura seguente `InputText` e utilizzare il componente come viene utilizzato:</span><span class="sxs-lookup"><span data-stu-id="7f691-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="7f691-144">Utilizzare i pulsanti di opzione</span><span class="sxs-lookup"><span data-stu-id="7f691-144">Work with radio buttons</span></span>

<span data-ttu-id="7f691-145">Quando si utilizzano i pulsanti di opzione in un form, l'associazione dati viene gestita in modo diverso rispetto ad altri elementi perché i pulsanti di opzione vengono valutati come gruppo.</span><span class="sxs-lookup"><span data-stu-id="7f691-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="7f691-146">Il valore di ogni pulsante di opzione è fisso, ma il valore del gruppo di pulsanti di opzione è il valore del pulsante di opzione selezionato.</span><span class="sxs-lookup"><span data-stu-id="7f691-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="7f691-147">L'esempio seguente illustra come:</span><span class="sxs-lookup"><span data-stu-id="7f691-147">The following example shows how to:</span></span>

* <span data-ttu-id="7f691-148">Gestire l'associazione dati per un gruppo di pulsanti di opzione.</span><span class="sxs-lookup"><span data-stu-id="7f691-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="7f691-149">Supportare la `InputRadio` convalida utilizzando un componente personalizzato.</span><span class="sxs-lookup"><span data-stu-id="7f691-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="7f691-150">Di `EditForm` seguito viene `InputRadio` utilizzato il componente precedente per ottenere e convalidare una classificazione dall'utente:</span><span class="sxs-lookup"><span data-stu-id="7f691-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="7f691-151">Supporto di convalida</span><span class="sxs-lookup"><span data-stu-id="7f691-151">Validation support</span></span>

<span data-ttu-id="7f691-152">Il `DataAnnotationsValidator` componente associa il supporto di convalida tramite annotazioni dei dati all'oggetto in cascata. `EditContext`</span><span class="sxs-lookup"><span data-stu-id="7f691-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="7f691-153">L'abilitazione del supporto per la convalida tramite annotazioni dei dati richiede questo gesto esplicito.</span><span class="sxs-lookup"><span data-stu-id="7f691-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="7f691-154">Per utilizzare un sistema di convalida diverso `DataAnnotationsValidator` rispetto alle annotazioni dei dati, sostituire il con un'implementazione personalizzata.</span><span class="sxs-lookup"><span data-stu-id="7f691-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="7f691-155">L'implementazione ASP.NET Core è disponibile per l'ispezione nell'origine di riferimento: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="7f691-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="7f691-156">esegue due tipi di convalida:</span><span class="sxs-lookup"><span data-stu-id="7f691-156"> performs two types of validation:</span></span>

* <span data-ttu-id="7f691-157">*La convalida* del campo viene eseguita quando l'utente esce da un campo.</span><span class="sxs-lookup"><span data-stu-id="7f691-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="7f691-158">Durante la convalida `DataAnnotationsValidator` del campo, il componente associa tutti i risultati della convalida riportata al campo.</span><span class="sxs-lookup"><span data-stu-id="7f691-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="7f691-159">*La convalida* del modello viene eseguita quando l'utente invia il modulo.</span><span class="sxs-lookup"><span data-stu-id="7f691-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="7f691-160">Durante la convalida `DataAnnotationsValidator` del modello, il componente tenta di determinare il campo in base al nome del membro segnalato dal risultato della convalida.</span><span class="sxs-lookup"><span data-stu-id="7f691-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="7f691-161">I risultati della convalida non associati a un singolo membro sono associati al modello anziché a un campo.</span><span class="sxs-lookup"><span data-stu-id="7f691-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="7f691-162">Componenti di Riepilogo di convalida e Messaggio di convalida</span><span class="sxs-lookup"><span data-stu-id="7f691-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="7f691-163">Il `ValidationSummary` componente riepiloga tutti i messaggi di convalida, che è simile al [convalida Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="7f691-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="7f691-164">Messaggi di convalida dell'output `Model` per un modello specifico con il parametro:</span><span class="sxs-lookup"><span data-stu-id="7f691-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="7f691-165">Il `ValidationMessage` componente visualizza i messaggi di convalida per un campo specifico, che è simile a [Convalida Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="7f691-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="7f691-166">Specificare il campo `For` per la convalida con l'attributo e un'espressione lambda che denomina la proprietà del modello:</span><span class="sxs-lookup"><span data-stu-id="7f691-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="7f691-167">I `ValidationMessage` `ValidationSummary` componenti e supportano attributi arbitrari.</span><span class="sxs-lookup"><span data-stu-id="7f691-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="7f691-168">Qualsiasi attributo che non corrisponde a un `<div>` parametro del componente viene aggiunto all'elemento o `<ul>` alla generazione.</span><span class="sxs-lookup"><span data-stu-id="7f691-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="7f691-169">Attributi di convalida personalizzati</span><span class="sxs-lookup"><span data-stu-id="7f691-169">Custom validation attributes</span></span>

<span data-ttu-id="7f691-170">Per assicurarsi che un risultato di convalida sia associato correttamente a <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> un campo <xref:System.ComponentModel.DataAnnotations.ValidationResult>quando si utilizza un attributo di [convalida personalizzato,](xref:mvc/models/validation#custom-attributes)passare il contesto di convalida durante la creazione del comando :</span><span class="sxs-lookup"><span data-stu-id="7f691-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="7f691-171">pacchetto di convalida delle annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="7f691-171"> data annotations validation package</span></span>

<span data-ttu-id="7f691-172">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) è un pacchetto che colma le `DataAnnotationsValidator` lacune nell'esperienza di convalida utilizzando il componente.</span><span class="sxs-lookup"><span data-stu-id="7f691-172">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="7f691-173">Il pacchetto è attualmente *sperimentale.*</span><span class="sxs-lookup"><span data-stu-id="7f691-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="7f691-174">[CompareProperty] (attributo)</span><span class="sxs-lookup"><span data-stu-id="7f691-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="7f691-175">Il <xref:System.ComponentModel.DataAnnotations.CompareAttribute> non funziona bene `DataAnnotationsValidator` con il componente perché non associa il risultato della convalida a un membro specifico.</span><span class="sxs-lookup"><span data-stu-id="7f691-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="7f691-176">Ciò può causare un comportamento incoerente tra la convalida a livello di campo e quando l'intero modello viene convalidato in un invio.</span><span class="sxs-lookup"><span data-stu-id="7f691-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="7f691-177">Il pacchetto *sperimentale* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) introduce `ComparePropertyAttribute`un attributo di convalida aggiuntivo, , che aggira queste limitazioni.</span><span class="sxs-lookup"><span data-stu-id="7f691-177">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="7f691-178">In Blazor un'app, `[CompareProperty]` è una `[Compare]` sostituzione diretta per l'attributo.</span><span class="sxs-lookup"><span data-stu-id="7f691-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="7f691-179">Modelli annidati, tipi di raccolta e tipi complessiNested models, collection types, and complex types</span><span class="sxs-lookup"><span data-stu-id="7f691-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="7f691-180">fornisce supporto per la convalida dell'input del `DataAnnotationsValidator`modulo utilizzando le annotazioni dei dati con l'oggetto incorporato in .</span><span class="sxs-lookup"><span data-stu-id="7f691-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="7f691-181">Tuttavia, `DataAnnotationsValidator` l'unico convalida le proprietà di primo livello del modello associate al form che non sono proprietà di tipo raccolta o complesso.</span><span class="sxs-lookup"><span data-stu-id="7f691-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="7f691-182">Per convalidare l'intero object graph del modello associato, incluse `ObjectGraphDataAnnotationsValidator` le proprietà di tipo complesso e di raccolta, utilizzare il pacchetto di [microsoft.AspNetCore.Components.DataAnnotations.Validation fornito dal pacchetto sperimentale Microsoft.AspNetCore.Components.DataAnnotations.Validation:](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental*</span><span class="sxs-lookup"><span data-stu-id="7f691-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="7f691-183">Annotare le `[ValidateComplexType]`proprietà del modello con .</span><span class="sxs-lookup"><span data-stu-id="7f691-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="7f691-184">Nelle classi del modello `ShipDescription` seguenti, la classe contiene annotazioni di dati aggiuntive da convalidare quando il modello è associato al form:</span><span class="sxs-lookup"><span data-stu-id="7f691-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="7f691-185">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="7f691-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="7f691-186">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="7f691-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="7f691-187">Abilitare il pulsante di invio in base alla convalida del modulo</span><span class="sxs-lookup"><span data-stu-id="7f691-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="7f691-188">Per abilitare e disabilitare il pulsante di invio in base alla convalida del modulo:</span><span class="sxs-lookup"><span data-stu-id="7f691-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="7f691-189">Utilizzare i moduli `EditContext` per assegnare il modello quando il componente viene inizializzato.</span><span class="sxs-lookup"><span data-stu-id="7f691-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="7f691-190">Convalidare il modulo nel `OnFieldChanged` callback del contesto per abilitare e disabilitare il pulsante di invio.</span><span class="sxs-lookup"><span data-stu-id="7f691-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="7f691-191">Annullare l'hook `Dispose` del gestore eventi nel metodo.</span><span class="sxs-lookup"><span data-stu-id="7f691-191">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="7f691-192">Per altre informazioni, vedere <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="7f691-192">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

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

<span data-ttu-id="7f691-193">Nell'esempio precedente, `_formInvalid` impostare su `false` if:</span><span class="sxs-lookup"><span data-stu-id="7f691-193">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="7f691-194">Il modulo viene precaricato con valori predefiniti validi.</span><span class="sxs-lookup"><span data-stu-id="7f691-194">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="7f691-195">Si desidera attivare il pulsante di invio al caricamento del modulo.</span><span class="sxs-lookup"><span data-stu-id="7f691-195">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="7f691-196">Un effetto collaterale dell'approccio `ValidationSummary` precedente è che un componente viene popolato con campi non validi dopo che l'utente interagisce con qualsiasi campo.</span><span class="sxs-lookup"><span data-stu-id="7f691-196">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="7f691-197">Questo scenario può essere affrontato in uno dei seguenti modi:</span><span class="sxs-lookup"><span data-stu-id="7f691-197">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="7f691-198">Non utilizzare un `ValidationSummary` componente nel modulo.</span><span class="sxs-lookup"><span data-stu-id="7f691-198">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="7f691-199">Rendere `ValidationSummary` il componente visibile quando è selezionato il `HandleValidSubmit` pulsante di invio (ad esempio, in un metodo).</span><span class="sxs-lookup"><span data-stu-id="7f691-199">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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
