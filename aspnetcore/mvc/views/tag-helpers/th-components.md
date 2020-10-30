---
title: Componenti helper tag in ASP.NET Core
author: scottaddie
description: Informazioni sui componenti helper tag e su come usarli in ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 15bddd8ce18546bef7ee7e6ec2e32e369d0858a3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060560"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="65c6c-103">Componenti helper tag in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65c6c-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="65c6c-104">Di [Scott Addie](https://twitter.com/Scott_Addie) e [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="65c6c-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="65c6c-105">Un componente helper tag è un helper tag che consente di modificare o aggiungere elementi HTML da codice lato server in modo condizionale.</span><span class="sxs-lookup"><span data-stu-id="65c6c-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="65c6c-106">Questa funzionalità è disponibile in ASP.NET Core 2.0 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="65c6c-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="65c6c-107">ASP.NET Core include due componenti helper tag predefiniti: `head` e `body`.</span><span class="sxs-lookup"><span data-stu-id="65c6c-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="65c6c-108">Si trovano nello <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers> spazio dei nomi e possono essere usati sia in MVC che in :::no-loc(Razor)::: pagine.</span><span class="sxs-lookup"><span data-stu-id="65c6c-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers> namespace and can be used in both MVC and :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="65c6c-109">I componenti helper tag non richiedono la registrazione con l'app in *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="65c6c-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml* .</span></span>

<span data-ttu-id="65c6c-110">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="65c6c-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="65c6c-111">Casi d'uso</span><span class="sxs-lookup"><span data-stu-id="65c6c-111">Use cases</span></span>

<span data-ttu-id="65c6c-112">Due casi d'uso comuni dei componenti helper tag sono:</span><span class="sxs-lookup"><span data-stu-id="65c6c-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="65c6c-113">Inserimento di un `<link>` nell'elemento `<head>`.</span><span class="sxs-lookup"><span data-stu-id="65c6c-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="65c6c-114">Inserimento di un `<script>` nell'elemento `<body>`.</span><span class="sxs-lookup"><span data-stu-id="65c6c-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="65c6c-115">Le sezioni seguenti descrivono questi casi d'uso.</span><span class="sxs-lookup"><span data-stu-id="65c6c-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="65c6c-116">Inserire in un elemento HTML head</span><span class="sxs-lookup"><span data-stu-id="65c6c-116">Inject into HTML head element</span></span>

<span data-ttu-id="65c6c-117">All'interno dell'elemento HTML `<head>` i file CSS vengono generalmente importati con l'elemento HTML `<link>`.</span><span class="sxs-lookup"><span data-stu-id="65c6c-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="65c6c-118">Il codice seguente inserisce un elemento `<link>` nell'elemento `<head>` usando il componente helper tag `head`:</span><span class="sxs-lookup"><span data-stu-id="65c6c-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="65c6c-119">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="65c6c-119">In the preceding code:</span></span>

* <span data-ttu-id="65c6c-120">`AddressStyleTagHelperComponent` implementa <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperComponent>.</span><span class="sxs-lookup"><span data-stu-id="65c6c-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="65c6c-121">L'astrazione:</span><span class="sxs-lookup"><span data-stu-id="65c6c-121">The abstraction:</span></span>
  * <span data-ttu-id="65c6c-122">Consente l'inizializzazione della classe con un <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperContext>.</span><span class="sxs-lookup"><span data-stu-id="65c6c-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="65c6c-123">Abilita l'uso dei componenti helper tag per l'aggiunta o la modifica di elementi HTML.</span><span class="sxs-lookup"><span data-stu-id="65c6c-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="65c6c-124">La proprietà <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperComponent.Order*> definisce l'ordine in cui viene eseguito il rendering dei componenti.</span><span class="sxs-lookup"><span data-stu-id="65c6c-124">The <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="65c6c-125">`Order` è necessaria in presenza di più utilizzi dei componenti helper tag in un'app.</span><span class="sxs-lookup"><span data-stu-id="65c6c-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="65c6c-126"><xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperComponent.ProcessAsync*> confronta il valore della proprietà <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperContext.TagName*> del contesto di esecuzione con `head`.</span><span class="sxs-lookup"><span data-stu-id="65c6c-126"><xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="65c6c-127">Se il confronto restituisce true, il contenuto del campo `_style` viene inserito nell'elemento HTML `<head>`.</span><span class="sxs-lookup"><span data-stu-id="65c6c-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="65c6c-128">Inserire in un elemento HTML body</span><span class="sxs-lookup"><span data-stu-id="65c6c-128">Inject into HTML body element</span></span>

<span data-ttu-id="65c6c-129">Il componente helper tag `body` può inserire un elemento `<script>` nell'elemento `<body>`.</span><span class="sxs-lookup"><span data-stu-id="65c6c-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="65c6c-130">Questa tecnica è illustrata nel codice riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="65c6c-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="65c6c-131">Per archiviare l'elemento `<script>` viene usato un file HTML separato,</span><span class="sxs-lookup"><span data-stu-id="65c6c-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="65c6c-132">che rende il codice più lineare e gestibile.</span><span class="sxs-lookup"><span data-stu-id="65c6c-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="65c6c-133">Il codice riportato sopra legge il contenuto di *TagHelpers/Templates/AddressToolTipScript.html* e vi accoda l'output dell'helper tag.</span><span class="sxs-lookup"><span data-stu-id="65c6c-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="65c6c-134">Il file *AddressToolTipScript.html* include il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="65c6c-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/:::no-loc(Razor):::PagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="65c6c-135">Il codice precedente associa un [widget tooltip Boostrap](https://getbootstrap.com/docs/3.3/javascript/#tooltips) a qualsiasi elemento `<address>` che include un attributo `printable`.</span><span class="sxs-lookup"><span data-stu-id="65c6c-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="65c6c-136">L'effetto è visibile quando si posiziona il puntatore del mouse sull'elemento.</span><span class="sxs-lookup"><span data-stu-id="65c6c-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="65c6c-137">Registrare un componente</span><span class="sxs-lookup"><span data-stu-id="65c6c-137">Register a Component</span></span>

<span data-ttu-id="65c6c-138">Un componente helper tag deve essere aggiunto alla raccolta di componenti helper tag.</span><span class="sxs-lookup"><span data-stu-id="65c6c-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="65c6c-139">È possibile farlo in tre modi:</span><span class="sxs-lookup"><span data-stu-id="65c6c-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="65c6c-140">Registrazione tramite un contenitore di servizi</span><span class="sxs-lookup"><span data-stu-id="65c6c-140">Registration via services container</span></span>](#registration-via-services-container)
* [<span data-ttu-id="65c6c-141">Registrazione tramite :::no-loc(Razor)::: file</span><span class="sxs-lookup"><span data-stu-id="65c6c-141">Registration via :::no-loc(Razor)::: file</span></span>](#registration-via-razor-file)
* [<span data-ttu-id="65c6c-142">Registrazione tramite un modello di pagina o un controller</span><span class="sxs-lookup"><span data-stu-id="65c6c-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="65c6c-143">Registrazione tramite un contenitore di servizi</span><span class="sxs-lookup"><span data-stu-id="65c6c-143">Registration via services container</span></span>

<span data-ttu-id="65c6c-144">Se la classe di componenti helper tag non è gestita con <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers.ITagHelperComponentManager>, deve essere registrata con il sistema di [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="65c6c-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="65c6c-145">Il codice `Startup.ConfigureServices` seguente registra le classi `AddressStyleTagHelperComponent` e `AddressScriptTagHelperComponent` con una [durata temporanea](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span><span class="sxs-lookup"><span data-stu-id="65c6c-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-no-locrazor-file"></a><span data-ttu-id="65c6c-146">Registrazione tramite :::no-loc(Razor)::: file</span><span class="sxs-lookup"><span data-stu-id="65c6c-146">Registration via :::no-loc(Razor)::: file</span></span>

<span data-ttu-id="65c6c-147">Se il componente helper tag non è registrato con DI, può essere registrato da una :::no-loc(Razor)::: pagina di pagine o da una visualizzazione MVC.</span><span class="sxs-lookup"><span data-stu-id="65c6c-147">If the Tag Helper Component isn't registered with DI, it can be registered from a :::no-loc(Razor)::: Pages page or an MVC view.</span></span> <span data-ttu-id="65c6c-148">Questa tecnica viene usata per controllare il markup inserito e l'ordine di esecuzione dei componenti da un :::no-loc(Razor)::: file.</span><span class="sxs-lookup"><span data-stu-id="65c6c-148">This technique is used for controlling the injected markup and the component execution order from a :::no-loc(Razor)::: file.</span></span>

<span data-ttu-id="65c6c-149">`ITagHelperComponentManager` viene usato per aggiungere componenti helper tag o per rimuoverli dall'app.</span><span class="sxs-lookup"><span data-stu-id="65c6c-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="65c6c-150">Il codice seguente illustra questa tecnica con `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="65c6c-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/:::no-loc(Razor):::PagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="65c6c-151">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="65c6c-151">In the preceding code:</span></span>

* <span data-ttu-id="65c6c-152">La direttiva `@inject` fornisce un'istanza di `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="65c6c-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="65c6c-153">L'istanza viene assegnata a una variabile denominata `manager` per accedere a downstream nel :::no-loc(Razor)::: file.</span><span class="sxs-lookup"><span data-stu-id="65c6c-153">The instance is assigned to a variable named `manager` for access downstream in the :::no-loc(Razor)::: file.</span></span>
* <span data-ttu-id="65c6c-154">Un'istanza di `AddressTagHelperComponent` viene aggiunta alla raccolta di componenti helper tag dell'app.</span><span class="sxs-lookup"><span data-stu-id="65c6c-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="65c6c-155">`AddressTagHelperComponent` viene modificato per contenere un costruttore che accetta i parametri `markup` e `order`:</span><span class="sxs-lookup"><span data-stu-id="65c6c-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="65c6c-156">Il parametro `markup` specificato viene usato in `ProcessAsync` nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="65c6c-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="65c6c-157">Registrazione tramite un modello di pagina o un controller</span><span class="sxs-lookup"><span data-stu-id="65c6c-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="65c6c-158">Se il componente helper tag non è registrato con DI, può essere registrato da un :::no-loc(Razor)::: modello di pagina delle pagine o da un controller MVC.</span><span class="sxs-lookup"><span data-stu-id="65c6c-158">If the Tag Helper Component isn't registered with DI, it can be registered from a :::no-loc(Razor)::: Pages page model or an MVC controller.</span></span> <span data-ttu-id="65c6c-159">Questa tecnica è utile per separare la logica C# dai :::no-loc(Razor)::: file.</span><span class="sxs-lookup"><span data-stu-id="65c6c-159">This technique is useful for separating C# logic from :::no-loc(Razor)::: files.</span></span>

<span data-ttu-id="65c6c-160">Per accedere a un'istanza di `ITagHelperComponentManager` viene usato l'inserimento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="65c6c-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="65c6c-161">Il componente helper tag deve viene aggiunto alla raccolta di componenti helper tag dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="65c6c-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="65c6c-162">Il :::no-loc(Razor)::: modello di pagina delle pagine seguente illustra questa tecnica con `AddressTagHelperComponent` :</span><span class="sxs-lookup"><span data-stu-id="65c6c-162">The following :::no-loc(Razor)::: Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="65c6c-163">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="65c6c-163">In the preceding code:</span></span>

* <span data-ttu-id="65c6c-164">Per accedere a un'istanza di `ITagHelperComponentManager` viene usato l'inserimento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="65c6c-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="65c6c-165">Un'istanza di `AddressTagHelperComponent` viene aggiunta alla raccolta di componenti helper tag dell'app.</span><span class="sxs-lookup"><span data-stu-id="65c6c-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="65c6c-166">Creare un componente</span><span class="sxs-lookup"><span data-stu-id="65c6c-166">Create a Component</span></span>

<span data-ttu-id="65c6c-167">Per creare un componente helper tag personalizzato:</span><span class="sxs-lookup"><span data-stu-id="65c6c-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="65c6c-168">Creare una classe pubblica che deriva da <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers.TagHelperComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="65c6c-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="65c6c-169">Applicare un [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.HtmlTargetElementAttribute) attributo alla classe.</span><span class="sxs-lookup"><span data-stu-id="65c6c-169">Apply an [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="65c6c-170">Specificare il nome dell'elemento HTML di destinazione.</span><span class="sxs-lookup"><span data-stu-id="65c6c-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="65c6c-171">*Facoltativo* : applicare un [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attributo alla classe per disattivare la visualizzazione del tipo in IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="65c6c-171">*Optional* : Apply an [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="65c6c-172">Il codice seguente crea un componente helper tag personalizzato che fa riferimento all'elemento HTML `<address>`:</span><span class="sxs-lookup"><span data-stu-id="65c6c-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/:::no-loc(Razor):::PagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="65c6c-173">Usare il componente helper tag personalizzato `address` per inserire markup HTML nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="65c6c-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

<span data-ttu-id="65c6c-174">Il metodo `ProcessAsync` precedente inserisce l'HTML fornito a <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperContent.SetHtmlContent*> nell'elemento `<address>` corrispondente.</span><span class="sxs-lookup"><span data-stu-id="65c6c-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.:::no-loc(Razor):::.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="65c6c-175">L'inserimento si verifica quando:</span><span class="sxs-lookup"><span data-stu-id="65c6c-175">The injection occurs when:</span></span>

* <span data-ttu-id="65c6c-176">Il valore della proprietà `TagName` del contesto di esecuzione è uguale a `address`.</span><span class="sxs-lookup"><span data-stu-id="65c6c-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="65c6c-177">L'elemento `<address>` corrispondente ha un attributo `printable`.</span><span class="sxs-lookup"><span data-stu-id="65c6c-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="65c6c-178">Ad esempio, l'istruzione `if` restituisce true durante l'elaborazione dell'elemento `<address>` seguente:</span><span class="sxs-lookup"><span data-stu-id="65c6c-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/:::no-loc(Razor):::PagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="65c6c-179">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="65c6c-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
