---
title: ASP.NET Core Blazor moduli e convalida
author: guardrex
description: Informazioni su come usare i moduli e gli scenari di convalida di campi in Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: f31a1f1d8942c9d9654dc26e946c022cf21ed9d1
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059864"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="d8a88-103">ASP.NET Core Blazor moduli e convalida</span><span class="sxs-lookup"><span data-stu-id="d8a88-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="d8a88-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d8a88-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d8a88-105">I moduli e la convalida sono supportati nell' Blazor utilizzo delle [annotazioni dei dati](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="d8a88-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="d8a88-106">Il `ExampleModel` tipo seguente definisce la logica di convalida usando le annotazioni dei dati:</span><span class="sxs-lookup"><span data-stu-id="d8a88-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="d8a88-107">Un modulo viene definito usando il <xref:Microsoft.AspNetCore.Components.Forms.EditForm> componente.</span><span class="sxs-lookup"><span data-stu-id="d8a88-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="d8a88-108">Il modulo seguente illustra elementi, componenti e codice tipici Razor :</span><span class="sxs-lookup"><span data-stu-id="d8a88-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="d8a88-109">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="d8a88-109">In the preceding example:</span></span>

* <span data-ttu-id="d8a88-110">Il modulo convalida l'input dell'utente nel `name` campo usando la convalida definita nel `ExampleModel` tipo.</span><span class="sxs-lookup"><span data-stu-id="d8a88-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="d8a88-111">Il modello viene creato nel blocco del componente `@code` e viene mantenuto in un campo privato ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="d8a88-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="d8a88-112">Il campo viene assegnato all' `Model` attributo dell' `<EditForm>` elemento.</span><span class="sxs-lookup"><span data-stu-id="d8a88-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="d8a88-113">Le <xref:Microsoft.AspNetCore.Components.Forms.InputText> associazioni del componente `@bind-Value` :</span><span class="sxs-lookup"><span data-stu-id="d8a88-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="d8a88-114">Proprietà del modello ( `exampleModel.Name` ) della <xref:Microsoft.AspNetCore.Components.Forms.InputText> proprietà del componente `Value` .</span><span class="sxs-lookup"><span data-stu-id="d8a88-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="d8a88-115">Per ulteriori informazioni sull'associazione di proprietà, vedere <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="d8a88-115">For more information on property binding, see <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="d8a88-116">Delegato dell'evento di modifica alla <xref:Microsoft.AspNetCore.Components.Forms.InputText> proprietà del componente `ValueChanged` .</span><span class="sxs-lookup"><span data-stu-id="d8a88-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="d8a88-117">Il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente connette il supporto di convalida usando le annotazioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="d8a88-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="d8a88-118">Il <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente riepiloga i messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="d8a88-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="d8a88-119">`HandleValidSubmit`viene attivato quando il form viene inviato correttamente (supera la convalida).</span><span class="sxs-lookup"><span data-stu-id="d8a88-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="d8a88-120">È disponibile un set di componenti di input predefiniti per la ricezione e la convalida dell'input dell'utente.</span><span class="sxs-lookup"><span data-stu-id="d8a88-120">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="d8a88-121">Gli input vengono convalidati quando vengono modificati e quando viene inviato un modulo.</span><span class="sxs-lookup"><span data-stu-id="d8a88-121">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="d8a88-122">I componenti di input disponibili sono illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="d8a88-122">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="d8a88-123">Componente di input</span><span class="sxs-lookup"><span data-stu-id="d8a88-123">Input component</span></span> | <span data-ttu-id="d8a88-124">Con rendering come&hellip;</span><span class="sxs-lookup"><span data-stu-id="d8a88-124">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

<span data-ttu-id="d8a88-125">Tutti i componenti di input, tra cui <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , supportano attributi arbitrari.</span><span class="sxs-lookup"><span data-stu-id="d8a88-125">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="d8a88-126">Qualsiasi attributo che non corrisponde a un parametro component viene aggiunto all'elemento HTML sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="d8a88-126">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="d8a88-127">I componenti di input forniscono il comportamento predefinito per la convalida in base alla modifica e alla modifica della classe CSS in modo da riflettere lo stato del campo.</span><span class="sxs-lookup"><span data-stu-id="d8a88-127">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="d8a88-128">Alcuni componenti includono una logica di analisi utile.</span><span class="sxs-lookup"><span data-stu-id="d8a88-128">Some components include useful parsing logic.</span></span> <span data-ttu-id="d8a88-129">E, ad esempio, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> gestire i valori non analizzabili con la registrazione normale come errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="d8a88-129">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="d8a88-130">I tipi che possono accettare valori null supportano anche il supporto di valori null del campo di destinazione, ad esempio `int?` .</span><span class="sxs-lookup"><span data-stu-id="d8a88-130">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="d8a88-131">Il `Starship` tipo seguente definisce la logica di convalida usando un set di proprietà e annotazioni di dati più ampio rispetto a quello precedente `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="d8a88-131">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="d8a88-132">Nell'esempio precedente `Description` è facoltativo perché non sono presenti annotazioni di dati.</span><span class="sxs-lookup"><span data-stu-id="d8a88-132">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="d8a88-133">Il form seguente convalida l'input dell'utente utilizzando la convalida definita nel `Starship` modello:</span><span class="sxs-lookup"><span data-stu-id="d8a88-133">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="d8a88-134"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Crea un oggetto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> come [valore](xref:blazor/components/cascading-values-and-parameters) di propagazione che tiene traccia dei metadati relativi al processo di modifica, inclusi i campi modificati e i messaggi di convalida correnti.</span><span class="sxs-lookup"><span data-stu-id="d8a88-134">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="d8a88-135"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Fornisce inoltre eventi pratici per invii validi e non validi ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> , <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> ).</span><span class="sxs-lookup"><span data-stu-id="d8a88-135">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="d8a88-136">In alternativa, usare <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> per attivare la convalida e verificare i valori dei campi con il codice di convalida personalizzato.</span><span class="sxs-lookup"><span data-stu-id="d8a88-136">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="d8a88-137">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="d8a88-137">In the following example:</span></span>

* <span data-ttu-id="d8a88-138">Il `HandleSubmit` metodo viene eseguito quando **`Submit`** si seleziona il pulsante.</span><span class="sxs-lookup"><span data-stu-id="d8a88-138">The `HandleSubmit` method runs when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="d8a88-139">Il modulo viene convalidato usando il modulo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="d8a88-139">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="d8a88-140">Il form viene ulteriormente convalidato passando al <xref:Microsoft.AspNetCore.Components.Forms.EditContext> `ServerValidate` metodo che chiama un endpoint dell'API Web nel server (*non illustrato*).</span><span class="sxs-lookup"><span data-stu-id="d8a88-140">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="d8a88-141">Il codice aggiuntivo viene eseguito a seconda del risultato della convalida lato client e lato server verificando `isValid` .</span><span class="sxs-lookup"><span data-stu-id="d8a88-141">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="d8a88-142">InputText basato sull'evento di input</span><span class="sxs-lookup"><span data-stu-id="d8a88-142">InputText based on the input event</span></span>

<span data-ttu-id="d8a88-143">Utilizzare il <xref:Microsoft.AspNetCore.Components.Forms.InputText> componente per creare un componente personalizzato che utilizza l' `input` evento anziché l' `change` evento.</span><span class="sxs-lookup"><span data-stu-id="d8a88-143">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="d8a88-144">Nell'esempio seguente il `CustomInputText` componente eredita il componente del Framework `InputText` e imposta l'associazione di eventi ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) sull' `oninput` evento.</span><span class="sxs-lookup"><span data-stu-id="d8a88-144">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="d8a88-145">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="d8a88-145">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

<span data-ttu-id="d8a88-146">Il `CustomInputText` componente può essere usato ovunque <xref:Microsoft.AspNetCore.Components.Forms.InputText> :</span><span class="sxs-lookup"><span data-stu-id="d8a88-146">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="d8a88-147">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="d8a88-147">`Pages/TestForm.razor`:</span></span>

```razor
@page  "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="d8a88-148">Pulsanti di opzione</span><span class="sxs-lookup"><span data-stu-id="d8a88-148">Radio buttons</span></span>

<span data-ttu-id="d8a88-149">Quando si utilizzano pulsanti di opzione in un modulo, data binding viene gestito in modo diverso rispetto ad altri elementi perché i pulsanti di opzione vengono valutati come un gruppo.</span><span class="sxs-lookup"><span data-stu-id="d8a88-149">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="d8a88-150">Il valore di ogni pulsante di opzione è fisso, ma il valore del gruppo di pulsanti di opzione è il valore del pulsante di opzione selezionato.</span><span class="sxs-lookup"><span data-stu-id="d8a88-150">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="d8a88-151">L'esempio seguente illustra come:</span><span class="sxs-lookup"><span data-stu-id="d8a88-151">The following example shows how to:</span></span>

* <span data-ttu-id="d8a88-152">Gestire data binding per un gruppo di pulsanti di opzione.</span><span class="sxs-lookup"><span data-stu-id="d8a88-152">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="d8a88-153">Supportare la convalida usando un `InputRadio` componente personalizzato.</span><span class="sxs-lookup"><span data-stu-id="d8a88-153">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="d8a88-154">Nell'esempio seguente <xref:Microsoft.AspNetCore.Components.Forms.EditForm> viene utilizzato il `InputRadio` componente precedente per ottenere e convalidare una classificazione dall'utente:</span><span class="sxs-lookup"><span data-stu-id="d8a88-154">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="d8a88-155">Associazione `<select>` di opzioni di elementi a valori di oggetti C# `null`</span><span class="sxs-lookup"><span data-stu-id="d8a88-155">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="d8a88-156">Non esiste un modo sensato per rappresentare il valore dell'opzione di un `<select>` elemento come valore dell'oggetto C# `null` , perché:</span><span class="sxs-lookup"><span data-stu-id="d8a88-156">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="d8a88-157">Gli attributi HTML non possono avere `null` valori.</span><span class="sxs-lookup"><span data-stu-id="d8a88-157">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="d8a88-158">L'equivalente più vicino a `null` in HTML è l'assenza dell'attributo HTML dell' `value` `<option>` elemento.</span><span class="sxs-lookup"><span data-stu-id="d8a88-158">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="d8a88-159">Quando si seleziona un oggetto `<option>` senza `value` attributo, il browser considera il valore come *contenuto di testo* dell' `<option>` elemento.</span><span class="sxs-lookup"><span data-stu-id="d8a88-159">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="d8a88-160">Il Blazor Framework non tenta di evitare il comportamento predefinito perché comporterebbe:</span><span class="sxs-lookup"><span data-stu-id="d8a88-160">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="d8a88-161">Creazione di una catena di soluzioni alternative per casi speciali nel Framework.</span><span class="sxs-lookup"><span data-stu-id="d8a88-161">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="d8a88-162">Modifiche di rilievo al comportamento del Framework corrente.</span><span class="sxs-lookup"><span data-stu-id="d8a88-162">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d8a88-163">L'equivalente più plausibile `null` in HTML è una *stringa vuota* `value` .</span><span class="sxs-lookup"><span data-stu-id="d8a88-163">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="d8a88-164">Il Blazor Framework gestisce le `null` conversioni di stringa vuote per l'associazione bidirezionale a un `<select>` valore di.</span><span class="sxs-lookup"><span data-stu-id="d8a88-164">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d8a88-165">Il Blazor Framework non gestisce automaticamente le `null` conversioni di stringa vuote quando si tenta l'associazione bidirezionale al `<select>` valore di un oggetto.</span><span class="sxs-lookup"><span data-stu-id="d8a88-165">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="d8a88-166">Per ulteriori informazioni, vedere [la pagina relativa alla correzione dell'associazione `<select>` a un valore null (DotNet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="d8a88-166">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="d8a88-167">Supporto della convalida</span><span class="sxs-lookup"><span data-stu-id="d8a88-167">Validation support</span></span>

<span data-ttu-id="d8a88-168">Il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente connette il supporto di convalida usando le annotazioni dei dati a a cascata <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="d8a88-168">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="d8a88-169">L'abilitazione del supporto per la convalida usando le annotazioni dei dati richiede questo movimento esplicito.</span><span class="sxs-lookup"><span data-stu-id="d8a88-169">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="d8a88-170">Per usare un sistema di convalida diverso rispetto alle annotazioni dei dati, sostituire <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> con un'implementazione personalizzata.</span><span class="sxs-lookup"><span data-stu-id="d8a88-170">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="d8a88-171">L'implementazione del ASP.NET Core è disponibile per l'ispezione nell'origine riferimento: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) .</span><span class="sxs-lookup"><span data-stu-id="d8a88-171">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="d8a88-172">I collegamenti precedenti a origine riferimento forniscono il codice dal ramo del repository `master` , che rappresenta lo sviluppo corrente dell'unità di prodotto per la prossima versione di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8a88-172">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="d8a88-173">Per selezionare il ramo per una versione diversa, usare il selettore di ramo GitHub (ad esempio `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="d8a88-173">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

Blazor<span data-ttu-id="d8a88-174">esegue due tipi di convalida:</span><span class="sxs-lookup"><span data-stu-id="d8a88-174"> performs two types of validation:</span></span>

* <span data-ttu-id="d8a88-175">La *convalida dei campi* viene eseguita quando l'utente esce da un campo.</span><span class="sxs-lookup"><span data-stu-id="d8a88-175">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="d8a88-176">Durante la convalida dei campi, il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente associa tutti i risultati della convalida segnalati al campo.</span><span class="sxs-lookup"><span data-stu-id="d8a88-176">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="d8a88-177">La *convalida del modello* viene eseguita quando l'utente invia il modulo.</span><span class="sxs-lookup"><span data-stu-id="d8a88-177">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="d8a88-178">Durante la convalida del modello, il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente tenta di determinare il campo in base al nome del membro segnalato dal risultato della convalida.</span><span class="sxs-lookup"><span data-stu-id="d8a88-178">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="d8a88-179">I risultati della convalida che non sono associati a un singolo membro sono associati al modello anziché a un campo.</span><span class="sxs-lookup"><span data-stu-id="d8a88-179">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="d8a88-180">Componenti del messaggio di convalida e di riepilogo della convalida</span><span class="sxs-lookup"><span data-stu-id="d8a88-180">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="d8a88-181">Il <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente riepiloga tutti i messaggi di convalida, che è simile all' [Helper tag del riepilogo di convalida](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="d8a88-181">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="d8a88-182">Messaggi di convalida dell'output per un modello specifico con il `Model` parametro:</span><span class="sxs-lookup"><span data-stu-id="d8a88-182">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="d8a88-183">Il <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> componente Visualizza i messaggi di convalida per un campo specifico, che è simile all' [Helper tag del messaggio di convalida](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="d8a88-183">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="d8a88-184">Specificare il campo per la convalida con l' `For` attributo e un'espressione lambda che denomina la proprietà del modello:</span><span class="sxs-lookup"><span data-stu-id="d8a88-184">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="d8a88-185">I <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componenti e supportano attributi arbitrari.</span><span class="sxs-lookup"><span data-stu-id="d8a88-185">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="d8a88-186">Qualsiasi attributo che non corrisponde a un parametro component viene aggiunto all' `<div>` elemento generato o `<ul>` .</span><span class="sxs-lookup"><span data-stu-id="d8a88-186">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="d8a88-187">Controllare lo stile dei messaggi di convalida nel foglio di stile dell'app ( `wwwroot/css/app.css` o `wwwroot/css/site.css` ).</span><span class="sxs-lookup"><span data-stu-id="d8a88-187">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="d8a88-188">La `validation-message` classe predefinita imposta il colore del testo dei messaggi di convalida su rosso:</span><span class="sxs-lookup"><span data-stu-id="d8a88-188">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="d8a88-189">Attributi di convalida personalizzati</span><span class="sxs-lookup"><span data-stu-id="d8a88-189">Custom validation attributes</span></span>

<span data-ttu-id="d8a88-190">Per assicurarsi che un risultato di convalida venga associato correttamente a un campo quando si usa un [attributo di convalida personalizzato](xref:mvc/models/validation#custom-attributes), passare il contesto di convalida <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> durante la creazione di <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="d8a88-190">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
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

> [!NOTE]
> <span data-ttu-id="d8a88-191"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> è `null`.</span><span class="sxs-lookup"><span data-stu-id="d8a88-191"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="d8a88-192">L'inserimento di servizi per la convalida nel `IsValid` metodo non è supportato.</span><span class="sxs-lookup"><span data-stu-id="d8a88-192">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="d8a88-193">pacchetto di convalida delle annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="d8a88-193"> data annotations validation package</span></span>

<span data-ttu-id="d8a88-194">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)È un pacchetto che colma i gap dell'esperienza di convalida usando il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente.</span><span class="sxs-lookup"><span data-stu-id="d8a88-194">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="d8a88-195">Il pacchetto è attualmente *sperimentale*.</span><span class="sxs-lookup"><span data-stu-id="d8a88-195">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="d8a88-196">Attributo [CompareProperty]</span><span class="sxs-lookup"><span data-stu-id="d8a88-196">[CompareProperty] attribute</span></span>

<span data-ttu-id="d8a88-197"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>Non funziona bene con il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente perché non associa il risultato della convalida a un membro specifico.</span><span class="sxs-lookup"><span data-stu-id="d8a88-197">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="d8a88-198">Ciò può comportare un comportamento incoerente tra la convalida a livello di campo e quando l'intero modello viene convalidato in un invio.</span><span class="sxs-lookup"><span data-stu-id="d8a88-198">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="d8a88-199">Il [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) pacchetto *sperimentale* introduce un attributo di convalida aggiuntivo, `ComparePropertyAttribute` , che aggira queste limitazioni.</span><span class="sxs-lookup"><span data-stu-id="d8a88-199">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="d8a88-200">In un' Blazor applicazione, `[CompareProperty]` è una sostituzione diretta per l' [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="d8a88-200">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="d8a88-201">Modelli annidati, tipi di raccolta e tipi complessi</span><span class="sxs-lookup"><span data-stu-id="d8a88-201">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="d8a88-202">fornisce supporto per la convalida dell'input del form utilizzando le annotazioni dei dati con l'oggetto incorporato <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="d8a88-202"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="d8a88-203">Tuttavia, l'oggetto <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> convalida solo le proprietà di primo livello del modello associate al form che non sono proprietà di tipo raccolta o complesso.</span><span class="sxs-lookup"><span data-stu-id="d8a88-203">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="d8a88-204">Per convalidare l'intero grafico di oggetti del modello associato, incluse le proprietà della raccolta e del tipo complesso, usare l'oggetto `ObjectGraphDataAnnotationsValidator` fornito dal pacchetto *sperimentale* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="d8a88-204">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="d8a88-205">Annotare le proprietà del modello con `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="d8a88-205">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="d8a88-206">Nelle classi di modelli seguenti la `ShipDescription` classe contiene annotazioni di dati aggiuntive da convalidare quando il modello è associato al form:</span><span class="sxs-lookup"><span data-stu-id="d8a88-206">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="d8a88-207">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="d8a88-207">`Starship.cs`:</span></span>

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

<span data-ttu-id="d8a88-208">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="d8a88-208">`ShipDescription.cs`:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="d8a88-209">Abilita il pulsante Invia in base alla convalida del modulo</span><span class="sxs-lookup"><span data-stu-id="d8a88-209">Enable the submit button based on form validation</span></span>

<span data-ttu-id="d8a88-210">Per abilitare e disabilitare il pulsante Invia in base alla convalida del modulo:</span><span class="sxs-lookup"><span data-stu-id="d8a88-210">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="d8a88-211">Utilizzare il modulo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> per assegnare il modello quando il componente viene inizializzato.</span><span class="sxs-lookup"><span data-stu-id="d8a88-211">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="d8a88-212">Convalidare il form nel callback del contesto <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> per abilitare e disabilitare il pulsante Submit.</span><span class="sxs-lookup"><span data-stu-id="d8a88-212">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="d8a88-213">Sganciare il gestore eventi nel `Dispose` metodo.</span><span class="sxs-lookup"><span data-stu-id="d8a88-213">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="d8a88-214">Per altre informazioni, vedere <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="d8a88-214">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

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

<span data-ttu-id="d8a88-215">Nell'esempio precedente, impostare `formInvalid` su `false` if:</span><span class="sxs-lookup"><span data-stu-id="d8a88-215">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="d8a88-216">Il modulo viene precaricato con valori predefiniti validi.</span><span class="sxs-lookup"><span data-stu-id="d8a88-216">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="d8a88-217">Si desidera attivare il pulsante Invia quando il modulo viene caricato.</span><span class="sxs-lookup"><span data-stu-id="d8a88-217">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="d8a88-218">Un effetto collaterale dell'approccio precedente è che un <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente viene popolato con campi non validi dopo che l'utente interagisce con un qualsiasi campo.</span><span class="sxs-lookup"><span data-stu-id="d8a88-218">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="d8a88-219">Questo scenario può essere risolto in uno dei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="d8a88-219">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="d8a88-220">Non usare un <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente nel form.</span><span class="sxs-lookup"><span data-stu-id="d8a88-220">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="d8a88-221">Rendere <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> visibile il componente quando viene selezionato il pulsante Invia, ad esempio in un `HandleValidSubmit` metodo.</span><span class="sxs-lookup"><span data-stu-id="d8a88-221">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="d8a88-222">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="d8a88-222">Troubleshoot</span></span>

> <span data-ttu-id="d8a88-223">InvalidOperationException: EditForm richiede un parametro di modello o un parametro EditContext, ma non entrambi.</span><span class="sxs-lookup"><span data-stu-id="d8a88-223">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="d8a88-224">Verificare che <xref:Microsoft.AspNetCore.Components.Forms.EditForm> disponga di un oggetto <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> o <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="d8a88-224">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="d8a88-225">Quando si assegna un <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> al form, verificare che venga creata un'istanza del tipo di modello, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="d8a88-225">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
