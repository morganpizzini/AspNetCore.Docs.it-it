---
<span data-ttu-id="53497-101">title:' ASP.NET Core Blazor componenti basati su modelli ' autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="53497-101">title: 'ASP.NET Core Blazor templated components' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="53497-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="53497-102">'Blazor'</span></span>
- <span data-ttu-id="53497-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="53497-103">'Identity'</span></span>
- <span data-ttu-id="53497-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="53497-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="53497-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="53497-105">'Razor'</span></span>
- <span data-ttu-id="53497-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="53497-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="53497-107">ASP.NET Core Blazor componenti basati su modelli</span><span class="sxs-lookup"><span data-stu-id="53497-107">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="53497-108">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="53497-108">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="53497-109">I componenti basati su modelli sono componenti che accettano uno o più modelli di interfaccia utente come parametri, che possono quindi essere usati come parte della logica di rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="53497-109">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="53497-110">I componenti basati su modelli consentono di creare componenti di livello superiore più riutilizzabili rispetto ai componenti normali.</span><span class="sxs-lookup"><span data-stu-id="53497-110">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="53497-111">Di seguito sono riportati alcuni esempi:</span><span class="sxs-lookup"><span data-stu-id="53497-111">A couple of examples include:</span></span>

* <span data-ttu-id="53497-112">Componente della tabella che consente a un utente di specificare i modelli per l'intestazione, le righe e il piè di pagina della tabella.</span><span class="sxs-lookup"><span data-stu-id="53497-112">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="53497-113">Componente di elenco che consente a un utente di specificare un modello per il rendering degli elementi in un elenco.</span><span class="sxs-lookup"><span data-stu-id="53497-113">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="53497-114">Parametri del modello</span><span class="sxs-lookup"><span data-stu-id="53497-114">Template parameters</span></span>

<span data-ttu-id="53497-115">Un componente basato su modelli viene definito specificando uno o più parametri del componente di tipo <xref:Microsoft.AspNetCore.Components.RenderFragment> o <xref:Microsoft.AspNetCore.Components.RenderFragment%601> .</span><span class="sxs-lookup"><span data-stu-id="53497-115">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="53497-116">Un frammento di rendering rappresenta un segmento di interfaccia utente di cui eseguire il rendering.</span><span class="sxs-lookup"><span data-stu-id="53497-116">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="53497-117"><xref:Microsoft.AspNetCore.Components.RenderFragment%601>accetta un parametro di tipo che può essere specificato quando viene richiamato il frammento di rendering.</span><span class="sxs-lookup"><span data-stu-id="53497-117"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="53497-118">`TableTemplate`componente</span><span class="sxs-lookup"><span data-stu-id="53497-118">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="53497-119">Quando si usa un componente basato su modelli, i parametri del modello possono essere specificati usando gli elementi figlio che corrispondono ai nomi dei parametri ( `TableHeader` e `RowTemplate` nell'esempio seguente):</span><span class="sxs-lookup"><span data-stu-id="53497-119">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

> [!NOTE]
> <span data-ttu-id="53497-120">I vincoli di tipo generico saranno supportati in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="53497-120">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="53497-121">Per ulteriori informazioni, vedere [Consenti vincoli di tipo generico (DotNet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="53497-121">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="53497-122">Parametri di contesto del modello</span><span class="sxs-lookup"><span data-stu-id="53497-122">Template context parameters</span></span>

<span data-ttu-id="53497-123">Gli argomenti del componente di tipo <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passati come elementi hanno un parametro implicito denominato `context` (ad esempio, nell'esempio di codice precedente, `@context.PetId` ), ma è possibile modificare il nome del parametro usando l' `Context` attributo nell'elemento figlio.</span><span class="sxs-lookup"><span data-stu-id="53497-123">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="53497-124">Nell'esempio seguente, l' `RowTemplate` attributo dell'elemento `Context` specifica il `pet` parametro:</span><span class="sxs-lookup"><span data-stu-id="53497-124">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="53497-125">In alternativa, è possibile specificare l' `Context` attributo sull'elemento Component.</span><span class="sxs-lookup"><span data-stu-id="53497-125">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="53497-126">L' `Context` attributo specificato si applica a tutti i parametri di modello specificati.</span><span class="sxs-lookup"><span data-stu-id="53497-126">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="53497-127">Questa operazione può essere utile quando si desidera specificare il nome del parametro di contenuto per il contenuto figlio implicito (senza alcun elemento figlio di wrapping).</span><span class="sxs-lookup"><span data-stu-id="53497-127">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="53497-128">Nell'esempio seguente l' `Context` attributo viene visualizzato nell' `TableTemplate` elemento e si applica a tutti i parametri del modello:</span><span class="sxs-lookup"><span data-stu-id="53497-128">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="53497-129">Componenti tipizzati in modo generico</span><span class="sxs-lookup"><span data-stu-id="53497-129">Generic-typed components</span></span>

<span data-ttu-id="53497-130">I componenti basati su modelli spesso sono tipizzati in modo generico.</span><span class="sxs-lookup"><span data-stu-id="53497-130">Templated components are often generically typed.</span></span> <span data-ttu-id="53497-131">È ad esempio `ListViewTemplate` possibile utilizzare un componente generico per eseguire il rendering `IEnumerable<T>` dei valori.</span><span class="sxs-lookup"><span data-stu-id="53497-131">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="53497-132">Per definire un componente generico, usare la [`@typeparam`](xref:mvc/views/razor#typeparam) direttiva per specificare i parametri di tipo:</span><span class="sxs-lookup"><span data-stu-id="53497-132">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="53497-133">Quando si usano componenti tipizzati generici, il parametro di tipo viene dedotto, se possibile:</span><span class="sxs-lookup"><span data-stu-id="53497-133">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="53497-134">In caso contrario, il parametro di tipo deve essere specificato in modo esplicito utilizzando un attributo che corrisponde al nome del parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="53497-134">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="53497-135">Nell'esempio seguente viene `TItem="Pet"` specificato il tipo:</span><span class="sxs-lookup"><span data-stu-id="53497-135">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
