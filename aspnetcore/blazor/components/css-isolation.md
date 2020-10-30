---
title: :::no-loc(Blazor):::Isolamento ASP.NET Core CSS
author: daveabrock
description: Scopri in che modo l'isolamento CSS ti permette di definire l'ambito di CSS per i tuoi componenti, semplificando i tuoi CSS ed evitando conflitti con altri componenti o librerie.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 628e7dc897912beaae0df792b82958517ac70ca4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056322"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="ff306-103">:::no-loc(Blazor):::Isolamento ASP.NET Core CSS</span><span class="sxs-lookup"><span data-stu-id="ff306-103">ASP.NET Core :::no-loc(Blazor)::: CSS isolation</span></span>

<span data-ttu-id="ff306-104">Di [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="ff306-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="ff306-105">L'isolamento CSS semplifica l'impronta CSS di un'app impedendo le dipendenze sugli stili globali e consente di evitare conflitti di stile tra componenti e librerie.</span><span class="sxs-lookup"><span data-stu-id="ff306-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="ff306-106">Abilita isolamento CSS</span><span class="sxs-lookup"><span data-stu-id="ff306-106">Enable CSS isolation</span></span> 

<span data-ttu-id="ff306-107">Per definire stili specifici del componente, creare un `razor.css` file corrispondente al nome del `.razor` file per il componente.</span><span class="sxs-lookup"><span data-stu-id="ff306-107">To define component-specific styles, create a `razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="ff306-108">Questo `razor.css` file è un *file CSS con ambito* .</span><span class="sxs-lookup"><span data-stu-id="ff306-108">This `razor.css` file is a *scoped CSS file* .</span></span> 

<span data-ttu-id="ff306-109">Per un `MyComponent` componente con un `MyComponent.razor` file, creare un file insieme al componente denominato `MyComponent.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="ff306-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="ff306-110">Il `MyComponent` valore nel `razor.css` nome file **non** fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="ff306-110">The `MyComponent` value in the `razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="ff306-111">Ad esempio, per aggiungere l'isolamento CSS al `Counter` componente nel :::no-loc(Blazor)::: modello di progetto predefinito, aggiungere un nuovo file denominato `Counter.razor.css` insieme al `Counter.razor` file, quindi aggiungere il seguente CSS:</span><span class="sxs-lookup"><span data-stu-id="ff306-111">For example to add CSS isolation to the `Counter` component in the default :::no-loc(Blazor)::: project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="ff306-112">Gli stili definiti in `Counter.razor.css` vengono applicati solo all'output del componente di cui è stato eseguito il rendering `Counter` .</span><span class="sxs-lookup"><span data-stu-id="ff306-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="ff306-113">Qualsiasi `h1` dichiarazione CSS definita altrove nell'app non è in conflitto con gli `Counter` stili.</span><span class="sxs-lookup"><span data-stu-id="ff306-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="ff306-114">Per garantire l'isolamento dello stile quando si verifica la creazione di bundle, `@import` :::no-loc(Razor)::: i blocchi non sono supportati con i file CSS con ambito.</span><span class="sxs-lookup"><span data-stu-id="ff306-114">In order to guarantee style isolation when bundling occurs, `@import` :::no-loc(Razor)::: blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="ff306-115">Aggregazione di isolamento CSS</span><span class="sxs-lookup"><span data-stu-id="ff306-115">CSS isolation bundling</span></span>

<span data-ttu-id="ff306-116">L'isolamento CSS si verifica in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="ff306-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="ff306-117">Durante questo processo, :::no-loc(Blazor)::: riscrive i selettori CSS in modo che corrispondano al markup sottoposto a rendering dal componente.</span><span class="sxs-lookup"><span data-stu-id="ff306-117">During this process, :::no-loc(Blazor)::: rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="ff306-118">Questi stili CSS riscritti sono aggregati e prodotti come asset statico in `{PROJECT NAME}.styles.css` , dove il segnaposto `{PROJECT NAME}` è il nome del pacchetto o del prodotto a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="ff306-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="ff306-119">Per impostazione predefinita, a questi file statici viene fatto riferimento dal percorso radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="ff306-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="ff306-120">Nell'app, fare riferimento al file in bundle controllando il riferimento all'interno del `<head>` tag del codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="ff306-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="ff306-121">All'interno del file in bundle, ogni componente è associato a un identificatore di ambito.</span><span class="sxs-lookup"><span data-stu-id="ff306-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="ff306-122">Per ogni componente con stile, viene aggiunto un attributo HTML con il formato `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="ff306-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="ff306-123">L'identificatore è univoco e diverso per ogni app.</span><span class="sxs-lookup"><span data-stu-id="ff306-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="ff306-124">Nel componente sottoposto a rendering `Counter` :::no-loc(Blazor)::: Accoda un identificatore di ambito all' `h1` elemento:</span><span class="sxs-lookup"><span data-stu-id="ff306-124">In the rendered `Counter` component, :::no-loc(Blazor)::: appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="ff306-125">Il `MyProjectName.styles.css` file usa l'identificatore di ambito per raggruppare una dichiarazione di stile con il relativo componente.</span><span class="sxs-lookup"><span data-stu-id="ff306-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="ff306-126">Nell'esempio seguente viene fornito lo stile per l' `<h1>` elemento precedente:</span><span class="sxs-lookup"><span data-stu-id="ff306-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="ff306-127">In fase di compilazione, viene creato un bundle di progetto con la convenzione `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` , dove il segnaposto `{STATIC WEB ASSETS BASE PATH}` è il percorso di base degli asset Web statici.</span><span class="sxs-lookup"><span data-stu-id="ff306-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="ff306-128">Se vengono usati altri progetti, ad esempio pacchetti NuGet o [ :::no-loc(Razor)::: librerie di classi](xref:blazor/components/class-libraries), il file in bundle:</span><span class="sxs-lookup"><span data-stu-id="ff306-128">If other projects are utilized, such as NuGet packages or [:::no-loc(Razor)::: class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="ff306-129">Fa riferimento agli stili utilizzando le importazioni CSS.</span><span class="sxs-lookup"><span data-stu-id="ff306-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="ff306-130">Non viene pubblicato come asset Web statico dell'app che utilizza gli stili.</span><span class="sxs-lookup"><span data-stu-id="ff306-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="ff306-131">Supporto componenti figlio</span><span class="sxs-lookup"><span data-stu-id="ff306-131">Child component support</span></span>

<span data-ttu-id="ff306-132">Per impostazione predefinita, l'isolamento CSS si applica solo al componente associato al formato `{COMPONENT NAME}.razor.css` , in cui il segnaposto `{COMPONENT NAME}` è in genere il nome del componente.</span><span class="sxs-lookup"><span data-stu-id="ff306-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="ff306-133">Per applicare le modifiche a un componente figlio, usare il `::deep` combinatore con tutti gli elementi discendenti nel file del componente padre `razor.css` .</span><span class="sxs-lookup"><span data-stu-id="ff306-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `razor.css` file.</span></span> <span data-ttu-id="ff306-134">Il `::deep` combinatore Seleziona elementi *discendenti* dell'identificatore di ambito generato da un elemento.</span><span class="sxs-lookup"><span data-stu-id="ff306-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="ff306-135">Nell'esempio seguente viene illustrato un componente padre denominato `Parent` con un componente figlio denominato `Child` .</span><span class="sxs-lookup"><span data-stu-id="ff306-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="ff306-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="ff306-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="ff306-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="ff306-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="ff306-138">Aggiornare la `h1` dichiarazione in `Parent.razor.css` con il `::deep` combinatore per indicare che la `h1` dichiarazione di stile deve essere applicata al componente padre e ai relativi elementi figlio:</span><span class="sxs-lookup"><span data-stu-id="ff306-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="ff306-139">Lo `h1` stile è ora applicabile ai `Parent` `Child` componenti e senza la necessità di creare un file CSS con ambito separato per il componente figlio.</span><span class="sxs-lookup"><span data-stu-id="ff306-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="ff306-140">Il `::deep` combinatore funziona solo con gli elementi discendenti.</span><span class="sxs-lookup"><span data-stu-id="ff306-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="ff306-141">La struttura HTML seguente applica gli `h1` stili ai componenti come previsto:</span><span class="sxs-lookup"><span data-stu-id="ff306-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="ff306-142">In questo scenario ASP.NET Core applica l'identificatore di ambito del componente padre all' `div` elemento, in modo che il browser sappia ereditare gli stili dal componente padre.</span><span class="sxs-lookup"><span data-stu-id="ff306-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="ff306-143">Tuttavia, escludendo l' `div` elemento viene rimossa la relazione discendente e lo stile **non** viene applicato al componente figlio:</span><span class="sxs-lookup"><span data-stu-id="ff306-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="ff306-144">Supporto per il preprocessore CSS</span><span class="sxs-lookup"><span data-stu-id="ff306-144">CSS preprocessor support</span></span>

<span data-ttu-id="ff306-145">I preprocessori CSS sono utili per migliorare lo sviluppo CSS utilizzando funzionalità quali variabili, nidificazione, moduli, mixin ed ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="ff306-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="ff306-146">Sebbene l'isolamento CSS non supporti in modo nativo i preprocessori CSS come Sass o less, l'integrazione dei preprocessori CSS è facile purché la compilazione del preprocessore venga eseguita prima che :::no-loc(Blazor)::: riscriva i selettori CSS durante il processo di compilazione.</span><span class="sxs-lookup"><span data-stu-id="ff306-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before :::no-loc(Blazor)::: rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="ff306-147">Usando Visual Studio, ad esempio, configurare la compilazione del preprocessore esistente come attività **prima della compilazione** in Visual Studio Task Runner Explorer.</span><span class="sxs-lookup"><span data-stu-id="ff306-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="ff306-148">Molti pacchetti NuGet di terze parti, ad esempio [delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), possono compilare i file Sass/scss all'inizio del processo di compilazione prima che si verifichi l'isolamento CSS e non è necessaria alcuna configurazione aggiuntiva aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="ff306-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="ff306-149">Configurazione dell'isolamento CSS</span><span class="sxs-lookup"><span data-stu-id="ff306-149">CSS isolation configuration</span></span>

<span data-ttu-id="ff306-150">L'isolamento CSS è progettato per l'uso predefinito, ma fornisce la configurazione per alcuni scenari avanzati, ad esempio quando sono presenti dipendenze da strumenti o flussi di lavoro esistenti.</span><span class="sxs-lookup"><span data-stu-id="ff306-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="ff306-151">Personalizzare il formato dell'identificatore dell'ambito</span><span class="sxs-lookup"><span data-stu-id="ff306-151">Customize scope identifier format</span></span>

<span data-ttu-id="ff306-152">Per impostazione predefinita, gli identificatori di ambito usano il formato `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="ff306-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="ff306-153">Per personalizzare il formato dell'identificatore di ambito, aggiornare il file di progetto in un modello desiderato:</span><span class="sxs-lookup"><span data-stu-id="ff306-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="ff306-154">Nell'esempio precedente, il CSS generato per `MyComponent.:::no-loc(Razor):::.css` modifica l'identificatore di ambito da `b-<10-character-string>` a `my-custom-scope-identifier` .</span><span class="sxs-lookup"><span data-stu-id="ff306-154">In the preceding example, the CSS generated for `MyComponent.:::no-loc(Razor):::.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="ff306-155">Modificare il percorso di base per gli asset Web statici</span><span class="sxs-lookup"><span data-stu-id="ff306-155">Change base path for static web assets</span></span>

<span data-ttu-id="ff306-156">Il `scoped.styles.css` file viene generato alla radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="ff306-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="ff306-157">Nel file di progetto, utilizzare la `StaticWebAssetBasePath` proprietà per modificare il percorso predefinito.</span><span class="sxs-lookup"><span data-stu-id="ff306-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="ff306-158">L'esempio seguente posiziona il `scoped.styles.css` file e il resto degli asset dell'app nel `_content` percorso:</span><span class="sxs-lookup"><span data-stu-id="ff306-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="ff306-159">Disabilitare la creazione automatica di bundle</span><span class="sxs-lookup"><span data-stu-id="ff306-159">Disable automatic bundling</span></span>

<span data-ttu-id="ff306-160">Per rifiutare esplicitamente la modalità di :::no-loc(Blazor)::: pubblicazione e caricamento dei file con ambito in fase di esecuzione, utilizzare la `DisableScopedCssBundling` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="ff306-160">To opt out of how :::no-loc(Blazor)::: publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="ff306-161">Quando si usa questa proprietà, altri strumenti o processi sono responsabili dell'acquisizione dei file CSS isolati dalla `obj` Directory e della pubblicazione e del caricamento in fase di esecuzione:</span><span class="sxs-lookup"><span data-stu-id="ff306-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```
