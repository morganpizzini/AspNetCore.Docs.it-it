---
title: Introduzione a ASP.NET CoreBlazor
author: guardrex
description: Esplora ASP.NET BlazorCore, un modo per creare un'interfaccia utente Web interattiva sul lato client con .NET in un'app ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 03/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/index
ms.openlocfilehash: 6d2e95cd2ec92f97a97cb558fb39e4540450c766
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405949"
---
# <a name="introduction-to-aspnet-core-opno-locblazor"></a><span data-ttu-id="41a5a-103">Introduzione a ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="41a5a-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="41a5a-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="41a5a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="41a5a-105">*Benvenuti Blazora !*</span><span class="sxs-lookup"><span data-stu-id="41a5a-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="41a5a-106">è un framework per la creazione di un'interfaccia utente Web interattiva sul lato client con .NET:Is a framework for building interactive client-side web UI with .NET:</span><span class="sxs-lookup"><span data-stu-id="41a5a-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="41a5a-107">Creare interfacce utente interattive avanzate con C# invece di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="41a5a-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="41a5a-108">Condividere la logica dell'app scritta in .NET sul lato client e sul lato server.</span><span class="sxs-lookup"><span data-stu-id="41a5a-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="41a5a-109">Eseguire il rendering dell'interfaccia utente come HTML e CSS per un ampio supporto dei browser, inclusi i browser per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="41a5a-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="41a5a-110">Integrazione con le moderne piattaforme di hosting, come [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="41a5a-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="41a5a-111">L'uso di .NET per lo sviluppo Web lato client offre i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="41a5a-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="41a5a-112">scrivere codice in C# invece che in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="41a5a-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="41a5a-113">Permette di sfruttare l'ecosistema .NET esistente di librerie .NET.</span><span class="sxs-lookup"><span data-stu-id="41a5a-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="41a5a-114">Permette di condividere la logica dell'app tra server e client.</span><span class="sxs-lookup"><span data-stu-id="41a5a-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="41a5a-115">Permette di ottenere le prestazioni, l'affidabilità e la sicurezza di .NET.</span><span class="sxs-lookup"><span data-stu-id="41a5a-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="41a5a-116">produttività con Visual Studio in Windows, Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="41a5a-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="41a5a-117">basato su un set comune di linguaggi, framework e strumenti che sono stabili, ricchi di funzionalità e facili da usare.</span><span class="sxs-lookup"><span data-stu-id="41a5a-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="41a5a-118">Componenti</span><span class="sxs-lookup"><span data-stu-id="41a5a-118">Components</span></span>

Blazor<span data-ttu-id="41a5a-119">le app sono basate sui *componenti*.</span><span class="sxs-lookup"><span data-stu-id="41a5a-119"> apps are based on *components*.</span></span> <span data-ttu-id="41a5a-120">Un componente Blazor in è un elemento dell'interfaccia utente, ad esempio una pagina, una finestra di dialogo o un form di immissione dati.</span><span class="sxs-lookup"><span data-stu-id="41a5a-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="41a5a-121">I componenti sono classi .NET compilati in assembly .NET che:</span><span class="sxs-lookup"><span data-stu-id="41a5a-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="41a5a-122">Definiscono la logica di rendering dell'interfaccia utente flessibile.</span><span class="sxs-lookup"><span data-stu-id="41a5a-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="41a5a-123">Gestiscono gli eventi utente.</span><span class="sxs-lookup"><span data-stu-id="41a5a-123">Handle user events.</span></span>
* <span data-ttu-id="41a5a-124">Possono essere annidati e riutilizzati.</span><span class="sxs-lookup"><span data-stu-id="41a5a-124">Can be nested and reused.</span></span>
* <span data-ttu-id="41a5a-125">Possono essere condivisi e distribuiti come [librerie di classi Razor](xref:razor-pages/ui-class) oppure [pacchetti NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="41a5a-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="41a5a-126">La classe dei componenti viene in genere scritta sotto forma di pagina di markup di [Razor](xref:mvc/views/razor) con un'estensione di file *razor*.</span><span class="sxs-lookup"><span data-stu-id="41a5a-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="41a5a-127">I Blazor componenti in sono formalmente indicati come *componenti Razor*.</span><span class="sxs-lookup"><span data-stu-id="41a5a-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="41a5a-128">Razor è una sintassi per la combinazione di markup HTML con codice C# progettata per la produttività degli sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="41a5a-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="41a5a-129">Razor permette di passare tra markup HTML e C# nello stesso file con supporto per [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="41a5a-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="41a5a-130">Anche Razor Pages ed MVC usano Razor.</span><span class="sxs-lookup"><span data-stu-id="41a5a-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="41a5a-131">Diversamente da Razor Pages ed MVC, che sono basati su un modello di richiesta/risposta, i componenti vengono usati in modo specifico per la logica e la composizione dell'interfaccia utente lato client.</span><span class="sxs-lookup"><span data-stu-id="41a5a-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="41a5a-132">Il markup Razor seguente mostra un componente (*Dialog.razor*), che può essere annidato all'interno di un altro componente:</span><span class="sxs-lookup"><span data-stu-id="41a5a-132">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="41a5a-133">Il contenuto del corpo della finestra di dialogo (`ChildContent`) e il titolo (`Title`) vengono forniti dal componente che usa questo componente nella propria interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="41a5a-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="41a5a-134">`OnYes` è un metodo C# attivato dall'evento `onclick` del pulsante.</span><span class="sxs-lookup"><span data-stu-id="41a5a-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="41a5a-135">utilizza tag HTML naturali per la composizione dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="41a5a-135"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="41a5a-136">Gli elementi HTML specificano i componenti e gli attributi di un tag passano valori alle proprietà del componente.</span><span class="sxs-lookup"><span data-stu-id="41a5a-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="41a5a-137">Nell'esempio seguente il componente `Index` usa il componente `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="41a5a-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="41a5a-138">`ChildContent` e `Title` vengono impostati dagli attributi e dal contenuto dell'elemento `<Dialog>`.</span><span class="sxs-lookup"><span data-stu-id="41a5a-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="41a5a-139">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="41a5a-139">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="41a5a-140">Il rendering della finestra di dialogo viene eseguito quando viene effettuato l'accesso all'elemento padre (*Index.razor*) in un browser:</span><span class="sxs-lookup"><span data-stu-id="41a5a-140">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Rendering del componente Dialog nel browser](index/_static/dialog.png)

<span data-ttu-id="41a5a-142">Quando questo componente viene usato nell'app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) e [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) accelera lo sviluppo con il completamento di sintassi e parametri.</span><span class="sxs-lookup"><span data-stu-id="41a5a-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="41a5a-143">Il rendering dei componenti viene eseguito in una rappresentazione in memoria del modello DOM (Document Object Model) del browser denominata *albero di rendering*, usato per aggiornare l'interfaccia utente in modo flessibile ed efficiente.</span><span class="sxs-lookup"><span data-stu-id="41a5a-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="41a5a-144">Assembly Web</span><span class="sxs-lookup"><span data-stu-id="41a5a-144"> WebAssembly</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="41a5a-145">WebAssembly è un framework di app a pagina singola per la creazione di app Web interattive sul lato client con .NET.</span><span class="sxs-lookup"><span data-stu-id="41a5a-145"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="41a5a-146">WebAssembly utilizza standard Web aperti senza plugin o transpilation di codice e funziona in tutti i browser web moderni, inclusi i browser per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="41a5a-146"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="41a5a-147">L'esecuzione di codice .NET all'interno di Web browser è resa possibile da [WebAssembly](https://webassembly.org) (tecnologia nota anche con l'abbreviazione *wasm*).</span><span class="sxs-lookup"><span data-stu-id="41a5a-147">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="41a5a-148">WebAssembly è un formato bytecode compatto ottimizzato per il download veloce e la velocità massima di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="41a5a-148">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="41a5a-149">WebAssembly è un standard Web aperto ed è supportato nei Web browser senza plug-in.</span><span class="sxs-lookup"><span data-stu-id="41a5a-149">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="41a5a-150">Il codice WebAssembly può accedere a tutte le funzionalità del browser tramite l'*interoperabilità JavaScript* (*JavaScript interop*).</span><span class="sxs-lookup"><span data-stu-id="41a5a-150">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="41a5a-151">Il codice .NET eseguito tramite WebAssembly nel browser viene eseguito nella sandbox JavaScript del browser con le misure di sicurezza offerte dalla sandbox per la protezione da azioni dannose nel computer client.</span><span class="sxs-lookup"><span data-stu-id="41a5a-151">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="41a5a-152">![BlazorWebAssembly esegue codice .NET nel browser con WebAssembly.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="41a5a-152">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="41a5a-153">Quando Blazor un'app WebAssembly viene compilata ed eseguita in un browser:</span><span class="sxs-lookup"><span data-stu-id="41a5a-153">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="41a5a-154">I file di codice C# e i file Razor vengono compilati in assembly .NET.</span><span class="sxs-lookup"><span data-stu-id="41a5a-154">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="41a5a-155">Gli assembly e il runtime .NET vengono scaricati nel browser.</span><span class="sxs-lookup"><span data-stu-id="41a5a-155">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="41a5a-156">WebAssembly esegue il bootstrap del runtime di .NET e configura il runtime per caricare gli assembly per l'app.</span><span class="sxs-lookup"><span data-stu-id="41a5a-156"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="41a5a-157">Il Blazor runtime WebAssembly utilizza l'interoperabilità JavaScript per gestire la manipolazione DOM e le chiamate API del browser.</span><span class="sxs-lookup"><span data-stu-id="41a5a-157">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="41a5a-158">La dimensione dell'app pubblicata, ovvero la *dimensione del payload*, è un fattore cruciale per le prestazioni ai fini dell'usabilità dell'app.</span><span class="sxs-lookup"><span data-stu-id="41a5a-158">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="41a5a-159">Un'app di grandi dimensioni impiega relativamente molto tempo a essere scaricata in un browser, influendo negativamente sull'esperienza utente.</span><span class="sxs-lookup"><span data-stu-id="41a5a-159">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="41a5a-160">WebAssembly ottimizza le dimensioni del payload per ridurre i tempi di download:</span><span class="sxs-lookup"><span data-stu-id="41a5a-160"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="41a5a-161">Il codice non usato viene rimosso dall'app quando questa viene pubblicata dal [linker del linguaggio intermedio](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="41a5a-161">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="41a5a-162">Le risposte HTTP vengono compresse.</span><span class="sxs-lookup"><span data-stu-id="41a5a-162">HTTP responses are compressed.</span></span>
* <span data-ttu-id="41a5a-163">Il runtime e gli assembly .NET vengono memorizzati nella cache nel browser.</span><span class="sxs-lookup"><span data-stu-id="41a5a-163">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="41a5a-164">Server</span><span class="sxs-lookup"><span data-stu-id="41a5a-164"> Server</span></span>

Blazor<span data-ttu-id="41a5a-165">disaccoppia la logica di rendering dei componenti dalla modalità di applicazione degli aggiornamenti dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="41a5a-165"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="41a5a-166">Server fornisce il supporto per l'hosting di componenti Razor nel server in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41a5a-166"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="41a5a-167">Gli aggiornamenti dell'interfaccia [SignalR](xref:signalr/introduction) utente vengono gestiti tramite una connessione.</span><span class="sxs-lookup"><span data-stu-id="41a5a-167">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="41a5a-168">Il runtime gestisce l'invio di eventi dell'interfaccia utente dal browser al server e quindi applica gli aggiornamenti dell'interfaccia utente inviati dal server di nuovo al browser dopo l'esecuzione dei componenti.</span><span class="sxs-lookup"><span data-stu-id="41a5a-168">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="41a5a-169">La connessione Blazor utilizzata da Server per comunicare con il browser viene utilizzata anche per gestire le chiamate di interoperabilità JavaScript.</span><span class="sxs-lookup"><span data-stu-id="41a5a-169">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="41a5a-170">![BlazorServer esegue il codice .NET sul server e interagisce con SignalR il Document Object Model sul client tramite una connessione](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="41a5a-170">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="41a5a-171">Interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="41a5a-171">JavaScript interop</span></span>

<span data-ttu-id="41a5a-172">Per le app che richiedono librerie JavaScript di terze parti e l'accesso alle API del browser, i componenti supportano l'interoperabilità con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="41a5a-172">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="41a5a-173">I componenti sono in grado di usare qualsiasi libreria o API supportata da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="41a5a-173">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="41a5a-174">Il codice C# può effettuare chiamate nel codice JavaScript e vice versa.</span><span class="sxs-lookup"><span data-stu-id="41a5a-174">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="41a5a-175">Per altre informazioni, vedere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="41a5a-175">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="41a5a-176">Condivisione del codice e .NET Standard</span><span class="sxs-lookup"><span data-stu-id="41a5a-176">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="41a5a-177">implementa [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="41a5a-177"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="41a5a-178">.NET Standard è una specifica formale delle API .NET comuni tra le implementazioni di .NET.</span><span class="sxs-lookup"><span data-stu-id="41a5a-178">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="41a5a-179">Le librerie di classi .NET Standard possono Blazoressere condivise tra diverse piattaforme .NET, ad esempio .NET Framework, .NET Core, Xamarin, Mono e Unity.</span><span class="sxs-lookup"><span data-stu-id="41a5a-179">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="41a5a-180">Le API non valide all'interno di un Web browser (ad esempio per l'accesso al file system, l'apertura di un socket e la gestione dei thread) generano un'eccezione <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="41a5a-180">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41a5a-181">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="41a5a-181">Additional resources</span></span>

* [<span data-ttu-id="41a5a-182">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="41a5a-182">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="41a5a-183">Guida di C</span><span class="sxs-lookup"><span data-stu-id="41a5a-183">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="41a5a-184">Html</span><span class="sxs-lookup"><span data-stu-id="41a5a-184">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="41a5a-185">[Impressionanti Blazor ](https://github.com/AdrienTorris/awesome-blazor) collegamenti alla community</span><span class="sxs-lookup"><span data-stu-id="41a5a-185">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
