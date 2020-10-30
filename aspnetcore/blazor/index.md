---
title: 'Introduzione a ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: "Esplora ASP.NET Core :::no-loc(Blazor)::: , un modo per creare un'interfaccia utente Web interattiva sul lato client con .NET in un'app ASP.NET Core."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: ecdf4f59aca0fe71bbfcfe61a99109127c8b92df
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055712"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a><span data-ttu-id="7c13d-103">Introduzione a ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="7c13d-103">Introduction to ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="7c13d-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7c13d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7c13d-105">*Benvenuti in :::no-loc(Blazor)::: .*</span><span class="sxs-lookup"><span data-stu-id="7c13d-105">*Welcome to :::no-loc(Blazor):::!*</span></span>

<span data-ttu-id="7c13d-106">:::no-loc(Blazor)::: è un Framework per la creazione di un'interfaccia utente Web interattiva sul lato client con [.NET](/dotnet/standard/tour):</span><span class="sxs-lookup"><span data-stu-id="7c13d-106">:::no-loc(Blazor)::: is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="7c13d-107">Creare interfacce utente interattive avanzate usando [C#](/dotnet/csharp/) anziché [JavaScript](https://www.javascript.com).</span><span class="sxs-lookup"><span data-stu-id="7c13d-107">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="7c13d-108">Condividere la logica dell'app scritta in .NET sul lato client e sul lato server.</span><span class="sxs-lookup"><span data-stu-id="7c13d-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="7c13d-109">Eseguire il rendering dell'interfaccia utente come HTML e CSS per un ampio supporto dei browser, inclusi i browser per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="7c13d-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="7c13d-110">Integrarsi con piattaforme di hosting moderne, ad esempio [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="7c13d-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="7c13d-111">L'uso di .NET per lo sviluppo Web lato client offre i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="7c13d-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="7c13d-112">scrivere codice in C# invece che in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7c13d-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="7c13d-113">Sfruttare l'ecosistema .NET esistente delle [librerie .NET](/dotnet/standard/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="7c13d-113">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="7c13d-114">Permette di condividere la logica dell'app tra server e client.</span><span class="sxs-lookup"><span data-stu-id="7c13d-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="7c13d-115">Permette di ottenere le prestazioni, l'affidabilità e la sicurezza di .NET.</span><span class="sxs-lookup"><span data-stu-id="7c13d-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="7c13d-116">È sempre più produttivo con [Visual Studio](https://visualstudio.microsoft.com) in Windows, Linux e MacOS.</span><span class="sxs-lookup"><span data-stu-id="7c13d-116">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="7c13d-117">basato su un set comune di linguaggi, framework e strumenti che sono stabili, ricchi di funzionalità e facili da usare.</span><span class="sxs-lookup"><span data-stu-id="7c13d-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="7c13d-118">Componenti</span><span class="sxs-lookup"><span data-stu-id="7c13d-118">Components</span></span>

<span data-ttu-id="7c13d-119">:::no-loc(Blazor)::: le app sono basate su *componenti* .</span><span class="sxs-lookup"><span data-stu-id="7c13d-119">:::no-loc(Blazor)::: apps are based on *components* .</span></span> <span data-ttu-id="7c13d-120">Un componente :::no-loc(Blazor)::: di è un elemento dell'interfaccia utente, ad esempio una pagina, una finestra di dialogo o un form di immissione dati.</span><span class="sxs-lookup"><span data-stu-id="7c13d-120">A component in :::no-loc(Blazor)::: is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="7c13d-121">I componenti sono classi .NET C# compilate in [assembly .NET](/dotnet/standard/assembly/) che:</span><span class="sxs-lookup"><span data-stu-id="7c13d-121">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="7c13d-122">Definiscono la logica di rendering dell'interfaccia utente flessibile.</span><span class="sxs-lookup"><span data-stu-id="7c13d-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="7c13d-123">Gestiscono gli eventi utente.</span><span class="sxs-lookup"><span data-stu-id="7c13d-123">Handle user events.</span></span>
* <span data-ttu-id="7c13d-124">Possono essere annidati e riutilizzati.</span><span class="sxs-lookup"><span data-stu-id="7c13d-124">Can be nested and reused.</span></span>
* <span data-ttu-id="7c13d-125">Può essere condiviso e distribuito come [ :::no-loc(Razor)::: librerie di classi](xref:razor-pages/ui-class) o [pacchetti NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="7c13d-125">Can be shared and distributed as [:::no-loc(Razor)::: class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="7c13d-126">La classe Component viene in genere scritta sotto forma di [:::no-loc(Razor):::](xref:mvc/views/razor) pagina di markup con `.razor` estensione di file.</span><span class="sxs-lookup"><span data-stu-id="7c13d-126">The component class is usually written in the form of a [:::no-loc(Razor):::](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="7c13d-127">I componenti di :::no-loc(Blazor)::: sono definiti formalmente come *:::no-loc(Razor)::: componenti* di.</span><span class="sxs-lookup"><span data-stu-id="7c13d-127">Components in :::no-loc(Blazor)::: are formally referred to as *:::no-loc(Razor)::: components* .</span></span> <span data-ttu-id="7c13d-128">:::no-loc(Razor)::: è una sintassi per combinare il markup HTML con il codice C# progettato per la produttività degli sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="7c13d-128">:::no-loc(Razor)::: is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="7c13d-129">:::no-loc(Razor)::: consente di passare dal markup HTML al linguaggio C# e viceversa nello stesso file con supporto per la programmazione [IntelliSense](/visualstudio/ide/using-intellisense) in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7c13d-129">:::no-loc(Razor)::: allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="7c13d-130">:::no-loc(Razor)::: Anche le pagine e MVC usano :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="7c13d-130">:::no-loc(Razor)::: Pages and MVC also use :::no-loc(Razor):::.</span></span> <span data-ttu-id="7c13d-131">Diversamente dalle :::no-loc(Razor)::: pagine e da MVC, che sono basate su un modello di richiesta/risposta, i componenti vengono usati in modo specifico per la composizione e la logica dell'interfaccia utente lato client.</span><span class="sxs-lookup"><span data-stu-id="7c13d-131">Unlike :::no-loc(Razor)::: Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="7c13d-132">:::no-loc(Blazor)::: Usa tag HTML naturali per la composizione dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="7c13d-132">:::no-loc(Blazor)::: uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="7c13d-133">Il :::no-loc(Razor)::: markup seguente illustra un componente ( `Dialog.razor` ) che visualizza una finestra di dialogo e elabora un evento quando l'utente seleziona un pulsante:</span><span class="sxs-lookup"><span data-stu-id="7c13d-133">The following :::no-loc(Razor)::: markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

<span data-ttu-id="7c13d-134">Nell'esempio precedente, `OnYes` è un metodo C# attivato dall'evento del pulsante `onclick` .</span><span class="sxs-lookup"><span data-stu-id="7c13d-134">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="7c13d-135">Il testo ( `ChildContent` ) e il titolo () della finestra di dialogo `Title` vengono forniti dal componente seguente che usa questo componente nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="7c13d-135">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="7c13d-136">Il `Dialog` componente è annidato all'interno di un altro componente usando un tag HTML.</span><span class="sxs-lookup"><span data-stu-id="7c13d-136">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="7c13d-137">Nell'esempio seguente, il `Index` componente ( `Pages/Index.razor` ) usa il componente precedente `Dialog` .</span><span class="sxs-lookup"><span data-stu-id="7c13d-137">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="7c13d-138">L'attributo del tag `Title` passa un valore per il titolo alla `Dialog` proprietà del componente `Title` .</span><span class="sxs-lookup"><span data-stu-id="7c13d-138">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="7c13d-139">Il `Dialog` testo () del componente `ChildContent` viene impostato dal contenuto dell' `<Dialog>` elemento.</span><span class="sxs-lookup"><span data-stu-id="7c13d-139">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="7c13d-140">Quando il `Dialog` componente viene aggiunto al `Index` componente, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) accelera lo sviluppo con la sintassi e il completamento dei parametri.</span><span class="sxs-lookup"><span data-stu-id="7c13d-140">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about :::no-loc(Blazor):::?
</Dialog>
```

<span data-ttu-id="7c13d-141">Il rendering della finestra di dialogo viene eseguito quando `Index` si accede al componente in un browser.</span><span class="sxs-lookup"><span data-stu-id="7c13d-141">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="7c13d-142">Quando il pulsante è selezionato dall'utente, la console degli strumenti di sviluppo del browser Mostra il messaggio scritto dal `OnYes` Metodo:</span><span class="sxs-lookup"><span data-stu-id="7c13d-142">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![Componente della finestra di dialogo sottoposto a rendering nel browser annidato all'interno del componente dell'indice.](index/_static/dialog.png)

<span data-ttu-id="7c13d-146">I componenti eseguono il rendering in una rappresentazione in memoria del Document Object Model del browser [(Dom)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) denominato *albero di rendering* , che viene usato per aggiornare l'interfaccia utente in modo flessibile ed efficiente.</span><span class="sxs-lookup"><span data-stu-id="7c13d-146">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree* , which is used to update the UI in a flexible and efficient way.</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="7c13d-147">:::no-loc(Blazor WebAssembly)::: è un [Framework di app a singola pagina (Spa)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) per la creazione di app Web interattive sul lato client con .NET.</span><span class="sxs-lookup"><span data-stu-id="7c13d-147">:::no-loc(Blazor WebAssembly)::: is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="7c13d-148">:::no-loc(Blazor WebAssembly)::: USA gli standard Web aperti senza plug-in o ricompilando il codice in altri linguaggi.</span><span class="sxs-lookup"><span data-stu-id="7c13d-148">:::no-loc(Blazor WebAssembly)::: uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="7c13d-149">:::no-loc(Blazor WebAssembly)::: funziona in tutti i Web browser moderni, inclusi i browser per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="7c13d-149">:::no-loc(Blazor WebAssembly)::: works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="7c13d-150">Il codice .NET in esecuzione all'interno di Web browser è reso possibile dal [webassembly](https://webassembly.org) (abbreviato `wasm` ).</span><span class="sxs-lookup"><span data-stu-id="7c13d-150">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="7c13d-151">WebAssembly è un formato bytecode compatto ottimizzato per il download veloce e la velocità massima di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="7c13d-151">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="7c13d-152">WebAssembly è un standard Web aperto ed è supportato nei Web browser senza plug-in.</span><span class="sxs-lookup"><span data-stu-id="7c13d-152">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="7c13d-153">Il codice webassembly può accedere alle funzionalità complete del browser tramite JavaScript, denominato *interoperabilità JavaScript* , spesso abbreviato in interoperabilità di *JavaScript* o di interoperabilità *JS* .</span><span class="sxs-lookup"><span data-stu-id="7c13d-153">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* , often shortened to *JavaScript interop* or *JS interop* .</span></span> <span data-ttu-id="7c13d-154">Il codice .NET eseguito tramite WebAssembly nel browser viene eseguito nella sandbox JavaScript del browser con le misure di sicurezza offerte dalla sandbox per la protezione da azioni dannose nel computer client.</span><span class="sxs-lookup"><span data-stu-id="7c13d-154">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![::: NO-LOC (webassembly Blazer)::: esegue il codice .NET nel browser con webassembly.](index/_static/blazor-webassembly.png)

<span data-ttu-id="7c13d-156">Quando un' :::no-loc(Blazor WebAssembly)::: app viene compilata ed eseguita in un browser:</span><span class="sxs-lookup"><span data-stu-id="7c13d-156">When a :::no-loc(Blazor WebAssembly)::: app is built and run in a browser:</span></span>

* <span data-ttu-id="7c13d-157">I file e i file di codice C# :::no-loc(Razor)::: vengono compilati in assembly .NET.</span><span class="sxs-lookup"><span data-stu-id="7c13d-157">C# code files and :::no-loc(Razor)::: files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="7c13d-158">Gli assembly e il [Runtime .NET](/dotnet/framework/get-started/overview) vengono scaricati nel browser.</span><span class="sxs-lookup"><span data-stu-id="7c13d-158">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="7c13d-159">:::no-loc(Blazor WebAssembly)::: avvia il Runtime .NET e configura il runtime per caricare gli assembly per l'app.</span><span class="sxs-lookup"><span data-stu-id="7c13d-159">:::no-loc(Blazor WebAssembly)::: bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="7c13d-160">Il :::no-loc(Blazor WebAssembly)::: Runtime usa l'interoperabilità JavaScript per gestire la manipolazione Dom e le chiamate API del browser.</span><span class="sxs-lookup"><span data-stu-id="7c13d-160">The :::no-loc(Blazor WebAssembly)::: runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="7c13d-161">La dimensione dell'app pubblicata, ovvero la *dimensione del payload* , è un fattore cruciale per le prestazioni ai fini dell'usabilità dell'app.</span><span class="sxs-lookup"><span data-stu-id="7c13d-161">The size of the published app, its *payload size* , is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="7c13d-162">Un'app di grandi dimensioni impiega relativamente molto tempo a essere scaricata in un browser, influendo negativamente sull'esperienza utente.</span><span class="sxs-lookup"><span data-stu-id="7c13d-162">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="7c13d-163">:::no-loc(Blazor WebAssembly)::: Ottimizza le dimensioni del payload per ridurre i tempi di download:</span><span class="sxs-lookup"><span data-stu-id="7c13d-163">:::no-loc(Blazor WebAssembly)::: optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="7c13d-164">Il codice inutilizzato viene rimosso dall'app quando viene pubblicato dal [trimmer del linguaggio intermedio (il)](xref:blazor/host-and-deploy/configure-trimmer).</span><span class="sxs-lookup"><span data-stu-id="7c13d-164">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="7c13d-165">Le risposte HTTP vengono compresse.</span><span class="sxs-lookup"><span data-stu-id="7c13d-165">HTTP responses are compressed.</span></span>
* <span data-ttu-id="7c13d-166">Il runtime e gli assembly .NET vengono memorizzati nella cache nel browser.</span><span class="sxs-lookup"><span data-stu-id="7c13d-166">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="7c13d-167">Il codice non usato viene rimosso dall'app quando questa viene pubblicata dal [linker del linguaggio intermedio](xref:blazor/host-and-deploy/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="7c13d-167">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="7c13d-168">Le risposte HTTP vengono compresse.</span><span class="sxs-lookup"><span data-stu-id="7c13d-168">HTTP responses are compressed.</span></span>
* <span data-ttu-id="7c13d-169">Il runtime e gli assembly .NET vengono memorizzati nella cache nel browser.</span><span class="sxs-lookup"><span data-stu-id="7c13d-169">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## :::no-loc(Blazor Server):::

<span data-ttu-id="7c13d-170">:::no-loc(Blazor)::: separa la logica di rendering dei componenti dal modo in cui vengono applicati gli aggiornamenti dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="7c13d-170">:::no-loc(Blazor)::: decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="7c13d-171">*:::no-loc(Blazor Server):::* fornisce supporto per l'hosting di :::no-loc(Razor)::: componenti nel server in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c13d-171">*:::no-loc(Blazor Server):::* provides support for hosting :::no-loc(Razor)::: components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="7c13d-172">Gli aggiornamenti dell'interfaccia utente vengono gestiti tramite una [:::no-loc(SignalR):::](xref:signalr/introduction) connessione.</span><span class="sxs-lookup"><span data-stu-id="7c13d-172">UI updates are handled over a [:::no-loc(SignalR):::](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="7c13d-173">Il runtime gestisce:</span><span class="sxs-lookup"><span data-stu-id="7c13d-173">The runtime handles:</span></span>

* <span data-ttu-id="7c13d-174">Invio di eventi dell'interfaccia utente dal browser al server.</span><span class="sxs-lookup"><span data-stu-id="7c13d-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="7c13d-175">Applicazione degli aggiornamenti dell'interfaccia utente al componente di cui è stato eseguito il rendering che vengono restituiti dal server.</span><span class="sxs-lookup"><span data-stu-id="7c13d-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="7c13d-176">La connessione utilizzata da :::no-loc(Blazor Server)::: per comunicare con il browser viene utilizzata anche per gestire le chiamate di interoperabilità JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7c13d-176">The connection used by :::no-loc(Blazor Server)::: to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![::: NO-LOC (server Blazer)::: esegue il codice .NET sul server e interagisce con il Document Object Model sul client su un oggetto::: NO-LOC (SignalR)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="7c13d-178">Interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="7c13d-178">JavaScript interop</span></span>

<span data-ttu-id="7c13d-179">Per le app che richiedono librerie JavaScript di terze parti e l'accesso alle API del browser, i componenti supportano l'interoperabilità con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7c13d-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="7c13d-180">I componenti sono in grado di usare qualsiasi libreria o API supportata da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7c13d-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="7c13d-181">Il codice c# può [chiamare il codice JavaScript](xref:blazor/call-javascript-from-dotnet)e il codice JavaScript può chiamare il codice [c#](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="7c13d-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="7c13d-182">Condivisione del codice e .NET Standard</span><span class="sxs-lookup"><span data-stu-id="7c13d-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="7c13d-183">:::no-loc(Blazor)::: implementa l' [.NET standard](/dotnet/standard/net-standard), che consente :::no-loc(Blazor)::: ai progetti di fare riferimento a librerie conformi alle specifiche di .NET standard.</span><span class="sxs-lookup"><span data-stu-id="7c13d-183">:::no-loc(Blazor)::: implements the [.NET Standard](/dotnet/standard/net-standard), which enables :::no-loc(Blazor)::: projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="7c13d-184">.NET Standard è una specifica formale delle API .NET comuni tra le implementazioni di .NET.</span><span class="sxs-lookup"><span data-stu-id="7c13d-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="7c13d-185">Le librerie di classi .NET Standard possono essere condivise tra diverse piattaforme .NET, ad esempio :::no-loc(Blazor)::: .NET Framework, .NET Core, Novell, mono e Unity.</span><span class="sxs-lookup"><span data-stu-id="7c13d-185">.NET Standard class libraries can be shared across different .NET platforms, such as :::no-loc(Blazor):::, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="7c13d-186">Le API non valide all'interno di un Web browser (ad esempio per l'accesso al file system, l'apertura di un socket e la gestione dei thread) generano un'eccezione <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="7c13d-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c13d-187">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7c13d-187">Additional resources</span></span>

* [<span data-ttu-id="7c13d-188">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7c13d-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="7c13d-189">Guida a C#</span><span class="sxs-lookup"><span data-stu-id="7c13d-189">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="7c13d-190">HTML</span><span class="sxs-lookup"><span data-stu-id="7c13d-190">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="7c13d-191">[Awesome :::no-loc(Blazor)::: ](https://github.com/AdrienTorris/awesome-blazor) collegamenti della community</span><span class="sxs-lookup"><span data-stu-id="7c13d-191">[Awesome :::no-loc(Blazor):::](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
