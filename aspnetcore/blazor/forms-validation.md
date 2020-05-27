---
<span data-ttu-id="5feac-101">title: "ASP.NET Core Blazor Forms and Validation" Author: Description: "informazioni su come usare i moduli e gli scenari di convalida dei campi in" Blazor .</span><span class="sxs-lookup"><span data-stu-id="5feac-101">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-103">'Blazor'</span></span>
- <span data-ttu-id="5feac-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-104">'Identity'</span></span>
- <span data-ttu-id="5feac-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-106">'Razor'</span></span>
- <span data-ttu-id="5feac-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="5feac-108">ASP.NET Core Blazor moduli e convalida</span><span class="sxs-lookup"><span data-stu-id="5feac-108">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="5feac-109">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5feac-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5feac-110">I moduli e la convalida sono supportati nell' Blazor utilizzo delle [annotazioni dei dati](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="5feac-110">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="5feac-111">Il `ExampleModel` tipo seguente definisce la logica di convalida usando le annotazioni dei dati:</span><span class="sxs-lookup"><span data-stu-id="5feac-111">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="5feac-112">Un modulo viene definito usando il <xref:Microsoft.AspNetCore.Components.Forms.EditForm> componente.</span><span class="sxs-lookup"><span data-stu-id="5feac-112">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="5feac-113">Il modulo seguente illustra elementi, componenti e codice tipici Razor :</span><span class="sxs-lookup"><span data-stu-id="5feac-113">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="5feac-114">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="5feac-114">In the preceding example:</span></span>

* <span data-ttu-id="5feac-115">Il modulo convalida l'input dell'utente nel `name` campo usando la convalida definita nel `ExampleModel` tipo.</span><span class="sxs-lookup"><span data-stu-id="5feac-115">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="5feac-116">Il modello viene creato nel blocco del componente `@code` e viene mantenuto in un campo privato ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="5feac-116">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="5feac-117">Il campo viene assegnato all' `Model` attributo dell' `<EditForm>` elemento.</span><span class="sxs-lookup"><span data-stu-id="5feac-117">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="5feac-118">Le <xref:Microsoft.AspNetCore.Components.Forms.InputText> associazioni del componente `@bind-Value` :</span><span class="sxs-lookup"><span data-stu-id="5feac-118">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="5feac-119">Proprietà del modello ( `exampleModel.Name` ) della <xref:Microsoft.AspNetCore.Components.Forms.InputText> proprietà del componente `Value` .</span><span class="sxs-lookup"><span data-stu-id="5feac-119">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="5feac-120">Per ulteriori informazioni sull'associazione di proprietà, vedere <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="5feac-120">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="5feac-121">Delegato dell'evento di modifica alla <xref:Microsoft.AspNetCore.Components.Forms.InputText> proprietà del componente `ValueChanged` .</span><span class="sxs-lookup"><span data-stu-id="5feac-121">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="5feac-122">Il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente connette il supporto di convalida usando le annotazioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="5feac-122">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="5feac-123">Il <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente riepiloga i messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="5feac-123">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="5feac-124">`HandleValidSubmit`viene attivato quando il form viene inviato correttamente (supera la convalida).</span><span class="sxs-lookup"><span data-stu-id="5feac-124">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="5feac-125">È disponibile un set di componenti di input predefiniti per la ricezione e la convalida dell'input dell'utente.</span><span class="sxs-lookup"><span data-stu-id="5feac-125">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="5feac-126">Gli input vengono convalidati quando vengono modificati e quando viene inviato un modulo.</span><span class="sxs-lookup"><span data-stu-id="5feac-126">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="5feac-127">I componenti di input disponibili sono illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="5feac-127">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="5feac-128">Componente di input</span><span class="sxs-lookup"><span data-stu-id="5feac-128">Input component</span></span> | <span data-ttu-id="5feac-129">Con rendering come&hellip;</span><span class="sxs-lookup"><span data-stu-id="5feac-129">Rendered as&hellip;</span></span> |
| ---
<span data-ttu-id="5feac-130">title: "ASP.NET Core Blazor Forms and Validation" Author: Description: "informazioni su come usare i moduli e gli scenari di convalida dei campi in" Blazor .</span><span class="sxs-lookup"><span data-stu-id="5feac-130">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-131">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-132">'Blazor'</span></span>
- <span data-ttu-id="5feac-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-133">'Identity'</span></span>
- <span data-ttu-id="5feac-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-135">'Razor'</span></span>
- <span data-ttu-id="5feac-136">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5feac-137">title: "ASP.NET Core Blazor Forms and Validation" Author: Description: "informazioni su come usare i moduli e gli scenari di convalida dei campi in" Blazor .</span><span class="sxs-lookup"><span data-stu-id="5feac-137">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-138">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-139">'Blazor'</span></span>
- <span data-ttu-id="5feac-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-140">'Identity'</span></span>
- <span data-ttu-id="5feac-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-142">'Razor'</span></span>
- <span data-ttu-id="5feac-143">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5feac-144">title: "ASP.NET Core Blazor Forms and Validation" Author: Description: "informazioni su come usare i moduli e gli scenari di convalida dei campi in" Blazor .</span><span class="sxs-lookup"><span data-stu-id="5feac-144">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-145">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-146">'Blazor'</span></span>
- <span data-ttu-id="5feac-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-147">'Identity'</span></span>
- <span data-ttu-id="5feac-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-149">'Razor'</span></span>
- <span data-ttu-id="5feac-150">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5feac-151">title: "ASP.NET Core Blazor Forms and Validation" Author: Description: "informazioni su come usare i moduli e gli scenari di convalida dei campi in" Blazor .</span><span class="sxs-lookup"><span data-stu-id="5feac-151">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-152">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-153">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-153">'Blazor'</span></span>
- <span data-ttu-id="5feac-154">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-154">'Identity'</span></span>
- <span data-ttu-id="5feac-155">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-155">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-156">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-156">'Razor'</span></span>
- <span data-ttu-id="5feac-157">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-157">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5feac-158">title: "ASP.NET Core Blazor Forms and Validation" Author: Description: "informazioni su come usare i moduli e gli scenari di convalida dei campi in" Blazor .</span><span class="sxs-lookup"><span data-stu-id="5feac-158">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-159">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-160">'Blazor'</span></span>
- <span data-ttu-id="5feac-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-161">'Identity'</span></span>
- <span data-ttu-id="5feac-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-163">'Razor'</span></span>
- <span data-ttu-id="5feac-164">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-164">'SignalR' uid:</span></span> 

<span data-ttu-id="5feac-165">-------- | Titolo---:' ASP.NET Core Blazor Forms and Validation ' Author: Description:' Learn how to use Forms and field validation scenarios in Blazor .'</span><span class="sxs-lookup"><span data-stu-id="5feac-165">-------- | --- title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-166">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-167">'Blazor'</span></span>
- <span data-ttu-id="5feac-168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-168">'Identity'</span></span>
- <span data-ttu-id="5feac-169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-169">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-170">'Razor'</span></span>
- <span data-ttu-id="5feac-171">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5feac-172">title: "ASP.NET Core Blazor Forms and Validation" Author: Description: "informazioni su come usare i moduli e gli scenari di convalida dei campi in" Blazor .</span><span class="sxs-lookup"><span data-stu-id="5feac-172">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-173">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-174">'Blazor'</span></span>
- <span data-ttu-id="5feac-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-175">'Identity'</span></span>
- <span data-ttu-id="5feac-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-177">'Razor'</span></span>
- <span data-ttu-id="5feac-178">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5feac-179">title: "ASP.NET Core Blazor Forms and Validation" Author: Description: "informazioni su come usare i moduli e gli scenari di convalida dei campi in" Blazor .</span><span class="sxs-lookup"><span data-stu-id="5feac-179">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-180">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-181">'Blazor'</span></span>
- <span data-ttu-id="5feac-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-182">'Identity'</span></span>
- <span data-ttu-id="5feac-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-184">'Razor'</span></span>
- <span data-ttu-id="5feac-185">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5feac-186">title: "ASP.NET Core Blazor Forms and Validation" Author: Description: "informazioni su come usare i moduli e gli scenari di convalida dei campi in" Blazor .</span><span class="sxs-lookup"><span data-stu-id="5feac-186">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-187">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-188">'Blazor'</span></span>
- <span data-ttu-id="5feac-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-189">'Identity'</span></span>
- <span data-ttu-id="5feac-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-191">'Razor'</span></span>
- <span data-ttu-id="5feac-192">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5feac-193">title: "ASP.NET Core Blazor Forms and Validation" Author: Description: "informazioni su come usare i moduli e gli scenari di convalida dei campi in" Blazor .</span><span class="sxs-lookup"><span data-stu-id="5feac-193">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-194">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-195">'Blazor'</span></span>
- <span data-ttu-id="5feac-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-196">'Identity'</span></span>
- <span data-ttu-id="5feac-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-198">'Razor'</span></span>
- <span data-ttu-id="5feac-199">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-199">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5feac-200">title: "ASP.NET Core Blazor Forms and Validation" Author: Description: "informazioni su come usare i moduli e gli scenari di convalida dei campi in" Blazor .</span><span class="sxs-lookup"><span data-stu-id="5feac-200">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-201">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-202">'Blazor'</span></span>
- <span data-ttu-id="5feac-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-203">'Identity'</span></span>
- <span data-ttu-id="5feac-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-205">'Razor'</span></span>
- <span data-ttu-id="5feac-206">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5feac-207">title: "ASP.NET Core Blazor Forms and Validation" Author: Description: "informazioni su come usare i moduli e gli scenari di convalida dei campi in" Blazor .</span><span class="sxs-lookup"><span data-stu-id="5feac-207">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="5feac-208">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5feac-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5feac-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5feac-209">'Blazor'</span></span>
- <span data-ttu-id="5feac-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5feac-210">'Identity'</span></span>
- <span data-ttu-id="5feac-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5feac-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="5feac-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5feac-212">'Razor'</span></span>
- <span data-ttu-id="5feac-213">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5feac-213">'SignalR' uid:</span></span> 

<span data-ttu-id="5feac-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span><span class="sxs-lookup"><span data-stu-id="5feac-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span></span>

<span data-ttu-id="5feac-215">Tutti i componenti di input, tra cui <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , supportano attributi arbitrari.</span><span class="sxs-lookup"><span data-stu-id="5feac-215">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="5feac-216">Qualsiasi attributo che non corrisponde a un parametro component viene aggiunto all'elemento HTML sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="5feac-216">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="5feac-217">I componenti di input forniscono il comportamento predefinito per la convalida in base alla modifica e alla modifica della classe CSS in modo da riflettere lo stato del campo.</span><span class="sxs-lookup"><span data-stu-id="5feac-217">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="5feac-218">Alcuni componenti includono una logica di analisi utile.</span><span class="sxs-lookup"><span data-stu-id="5feac-218">Some components include useful parsing logic.</span></span> <span data-ttu-id="5feac-219">E, ad esempio, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> gestire i valori non analizzabili con la registrazione normale come errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="5feac-219">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="5feac-220">I tipi che possono accettare valori null supportano anche il supporto di valori null del campo di destinazione, ad esempio `int?` .</span><span class="sxs-lookup"><span data-stu-id="5feac-220">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="5feac-221">Il `Starship` tipo seguente definisce la logica di convalida usando un set di proprietà e annotazioni di dati più ampio rispetto a quello precedente `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="5feac-221">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="5feac-222">Nell'esempio precedente `Description` è facoltativo perché non sono presenti annotazioni di dati.</span><span class="sxs-lookup"><span data-stu-id="5feac-222">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="5feac-223">Il form seguente convalida l'input dell'utente utilizzando la convalida definita nel `Starship` modello:</span><span class="sxs-lookup"><span data-stu-id="5feac-223">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="5feac-224"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Crea un oggetto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> come [valore](xref:blazor/components#cascading-values-and-parameters) di propagazione che tiene traccia dei metadati relativi al processo di modifica, inclusi i campi modificati e i messaggi di convalida correnti.</span><span class="sxs-lookup"><span data-stu-id="5feac-224">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="5feac-225"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Fornisce inoltre eventi pratici per invii validi e non validi ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> , <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> ).</span><span class="sxs-lookup"><span data-stu-id="5feac-225">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="5feac-226">In alternativa, usare <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> per attivare la convalida e verificare i valori dei campi con il codice di convalida personalizzato.</span><span class="sxs-lookup"><span data-stu-id="5feac-226">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="5feac-227">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="5feac-227">In the following example:</span></span>

* <span data-ttu-id="5feac-228">Il `HandleSubmit` metodo viene eseguito quando viene selezionato il pulsante **Invia** .</span><span class="sxs-lookup"><span data-stu-id="5feac-228">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="5feac-229">Il modulo viene convalidato usando il modulo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="5feac-229">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="5feac-230">Il form viene ulteriormente convalidato passando al <xref:Microsoft.AspNetCore.Components.Forms.EditContext> `ServerValidate` metodo che chiama un endpoint dell'API Web nel server (*non illustrato*).</span><span class="sxs-lookup"><span data-stu-id="5feac-230">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="5feac-231">Il codice aggiuntivo viene eseguito a seconda del risultato della convalida lato client e lato server verificando `isValid` .</span><span class="sxs-lookup"><span data-stu-id="5feac-231">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="5feac-232">InputText basato sull'evento di input</span><span class="sxs-lookup"><span data-stu-id="5feac-232">InputText based on the input event</span></span>

<span data-ttu-id="5feac-233">Utilizzare il <xref:Microsoft.AspNetCore.Components.Forms.InputText> componente per creare un componente personalizzato che utilizza l' `input` evento anziché l' `change` evento.</span><span class="sxs-lookup"><span data-stu-id="5feac-233">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="5feac-234">Creare un componente con il markup seguente e usare il componente Analogamente a quanto <xref:Microsoft.AspNetCore.Components.Forms.InputText> viene usato:</span><span class="sxs-lookup"><span data-stu-id="5feac-234">Create a component with the following markup, and use the component just as <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="5feac-235">Usare i pulsanti di opzione</span><span class="sxs-lookup"><span data-stu-id="5feac-235">Work with radio buttons</span></span>

<span data-ttu-id="5feac-236">Quando si utilizzano pulsanti di opzione in un modulo, data binding viene gestito in modo diverso rispetto ad altri elementi perché i pulsanti di opzione vengono valutati come un gruppo.</span><span class="sxs-lookup"><span data-stu-id="5feac-236">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="5feac-237">Il valore di ogni pulsante di opzione è fisso, ma il valore del gruppo di pulsanti di opzione è il valore del pulsante di opzione selezionato.</span><span class="sxs-lookup"><span data-stu-id="5feac-237">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="5feac-238">L'esempio seguente illustra come:</span><span class="sxs-lookup"><span data-stu-id="5feac-238">The following example shows how to:</span></span>

* <span data-ttu-id="5feac-239">Gestire data binding per un gruppo di pulsanti di opzione.</span><span class="sxs-lookup"><span data-stu-id="5feac-239">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="5feac-240">Supportare la convalida usando un `InputRadio` componente personalizzato.</span><span class="sxs-lookup"><span data-stu-id="5feac-240">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="5feac-241">Nell'esempio seguente <xref:Microsoft.AspNetCore.Components.Forms.EditForm> viene utilizzato il `InputRadio` componente precedente per ottenere e convalidare una classificazione dall'utente:</span><span class="sxs-lookup"><span data-stu-id="5feac-241">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="5feac-242">Supporto della convalida</span><span class="sxs-lookup"><span data-stu-id="5feac-242">Validation support</span></span>

<span data-ttu-id="5feac-243">Il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente connette il supporto di convalida usando le annotazioni dei dati a a cascata <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="5feac-243">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="5feac-244">L'abilitazione del supporto per la convalida usando le annotazioni dei dati richiede questo movimento esplicito.</span><span class="sxs-lookup"><span data-stu-id="5feac-244">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="5feac-245">Per usare un sistema di convalida diverso rispetto alle annotazioni dei dati, sostituire <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> con un'implementazione personalizzata.</span><span class="sxs-lookup"><span data-stu-id="5feac-245">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="5feac-246">L'implementazione del ASP.NET Core è disponibile per l'ispezione nell'origine riferimento: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="5feac-246">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="5feac-247">esegue due tipi di convalida:</span><span class="sxs-lookup"><span data-stu-id="5feac-247"> performs two types of validation:</span></span>

* <span data-ttu-id="5feac-248">La *convalida dei campi* viene eseguita quando l'utente esce da un campo.</span><span class="sxs-lookup"><span data-stu-id="5feac-248">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="5feac-249">Durante la convalida dei campi, il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente associa tutti i risultati della convalida segnalati al campo.</span><span class="sxs-lookup"><span data-stu-id="5feac-249">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="5feac-250">La *convalida del modello* viene eseguita quando l'utente invia il modulo.</span><span class="sxs-lookup"><span data-stu-id="5feac-250">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="5feac-251">Durante la convalida del modello, il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente tenta di determinare il campo in base al nome del membro segnalato dal risultato della convalida.</span><span class="sxs-lookup"><span data-stu-id="5feac-251">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="5feac-252">I risultati della convalida che non sono associati a un singolo membro sono associati al modello anziché a un campo.</span><span class="sxs-lookup"><span data-stu-id="5feac-252">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="5feac-253">Componenti del messaggio di convalida e di riepilogo della convalida</span><span class="sxs-lookup"><span data-stu-id="5feac-253">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="5feac-254">Il <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente riepiloga tutti i messaggi di convalida, che è simile all' [Helper tag del riepilogo di convalida](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="5feac-254">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="5feac-255">Messaggi di convalida dell'output per un modello specifico con il `Model` parametro:</span><span class="sxs-lookup"><span data-stu-id="5feac-255">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="5feac-256">Il <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> componente Visualizza i messaggi di convalida per un campo specifico, che è simile all' [Helper tag del messaggio di convalida](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5feac-256">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="5feac-257">Specificare il campo per la convalida con l' `For` attributo e un'espressione lambda che denomina la proprietà del modello:</span><span class="sxs-lookup"><span data-stu-id="5feac-257">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="5feac-258">I <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componenti e supportano attributi arbitrari.</span><span class="sxs-lookup"><span data-stu-id="5feac-258">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="5feac-259">Qualsiasi attributo che non corrisponde a un parametro component viene aggiunto all' `<div>` elemento generato o `<ul>` .</span><span class="sxs-lookup"><span data-stu-id="5feac-259">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="5feac-260">Attributi di convalida personalizzati</span><span class="sxs-lookup"><span data-stu-id="5feac-260">Custom validation attributes</span></span>

<span data-ttu-id="5feac-261">Per assicurarsi che un risultato di convalida venga associato correttamente a un campo quando si usa un [attributo di convalida personalizzato](xref:mvc/models/validation#custom-attributes), passare il contesto di convalida <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> durante la creazione di <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="5feac-261">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="5feac-262">pacchetto di convalida delle annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="5feac-262"> data annotations validation package</span></span>

<span data-ttu-id="5feac-263">[Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) è un pacchetto che colma i gap dell'esperienza di convalida usando il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente.</span><span class="sxs-lookup"><span data-stu-id="5feac-263">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="5feac-264">Il pacchetto è attualmente *sperimentale*.</span><span class="sxs-lookup"><span data-stu-id="5feac-264">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="5feac-265">Attributo [CompareProperty]</span><span class="sxs-lookup"><span data-stu-id="5feac-265">[CompareProperty] attribute</span></span>

<span data-ttu-id="5feac-266"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>Non funziona bene con il <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente perché non associa il risultato della convalida a un membro specifico.</span><span class="sxs-lookup"><span data-stu-id="5feac-266">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="5feac-267">Ciò può comportare un comportamento incoerente tra la convalida a livello di campo e quando l'intero modello viene convalidato in un invio.</span><span class="sxs-lookup"><span data-stu-id="5feac-267">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="5feac-268">Il pacchetto *sperimentale* [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) introduce un attributo di convalida aggiuntivo, `ComparePropertyAttribute` , che aggira queste limitazioni.</span><span class="sxs-lookup"><span data-stu-id="5feac-268">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="5feac-269">In un' Blazor applicazione, `[CompareProperty]` è una sostituzione diretta per l' [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="5feac-269">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="5feac-270">Modelli annidati, tipi di raccolta e tipi complessi</span><span class="sxs-lookup"><span data-stu-id="5feac-270">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="5feac-271">fornisce supporto per la convalida dell'input del form utilizzando le annotazioni dei dati con l'oggetto incorporato <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="5feac-271"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="5feac-272">Tuttavia, l'oggetto <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> convalida solo le proprietà di primo livello del modello associate al form che non sono proprietà di tipo raccolta o complesso.</span><span class="sxs-lookup"><span data-stu-id="5feac-272">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="5feac-273">Per convalidare l'intero grafico di oggetti del modello associato, incluse le proprietà della raccolta e del tipo complesso, usare l'oggetto `ObjectGraphDataAnnotationsValidator` fornito dal pacchetto *sperimentale* [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="5feac-273">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="5feac-274">Annotare le proprietà del modello con `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="5feac-274">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="5feac-275">Nelle classi di modelli seguenti la `ShipDescription` classe contiene annotazioni di dati aggiuntive da convalidare quando il modello è associato al form:</span><span class="sxs-lookup"><span data-stu-id="5feac-275">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="5feac-276">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="5feac-276">*Starship.cs*:</span></span>

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

<span data-ttu-id="5feac-277">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="5feac-277">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="5feac-278">Abilita il pulsante Invia in base alla convalida del modulo</span><span class="sxs-lookup"><span data-stu-id="5feac-278">Enable the submit button based on form validation</span></span>

<span data-ttu-id="5feac-279">Per abilitare e disabilitare il pulsante Invia in base alla convalida del modulo:</span><span class="sxs-lookup"><span data-stu-id="5feac-279">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="5feac-280">Utilizzare il modulo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> per assegnare il modello quando il componente viene inizializzato.</span><span class="sxs-lookup"><span data-stu-id="5feac-280">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="5feac-281">Convalidare il form nel callback del contesto <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> per abilitare e disabilitare il pulsante Submit.</span><span class="sxs-lookup"><span data-stu-id="5feac-281">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="5feac-282">Sganciare il gestore eventi nel `Dispose` metodo.</span><span class="sxs-lookup"><span data-stu-id="5feac-282">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="5feac-283">Per altre informazioni, vedere <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="5feac-283">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

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

<span data-ttu-id="5feac-284">Nell'esempio precedente, impostare `formInvalid` su `false` if:</span><span class="sxs-lookup"><span data-stu-id="5feac-284">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="5feac-285">Il modulo viene precaricato con valori predefiniti validi.</span><span class="sxs-lookup"><span data-stu-id="5feac-285">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="5feac-286">Si desidera attivare il pulsante Invia quando il modulo viene caricato.</span><span class="sxs-lookup"><span data-stu-id="5feac-286">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="5feac-287">Un effetto collaterale dell'approccio precedente è che un <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente viene popolato con campi non validi dopo che l'utente interagisce con un qualsiasi campo.</span><span class="sxs-lookup"><span data-stu-id="5feac-287">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="5feac-288">Questo scenario può essere risolto in uno dei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="5feac-288">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="5feac-289">Non usare un <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente nel form.</span><span class="sxs-lookup"><span data-stu-id="5feac-289">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="5feac-290">Rendere <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> visibile il componente quando viene selezionato il pulsante Invia, ad esempio in un `HandleValidSubmit` metodo.</span><span class="sxs-lookup"><span data-stu-id="5feac-290">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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
