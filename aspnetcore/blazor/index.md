---
title: Introduzione a ASP.NET CoreBlazor
author: guardrex
description: Esplora ASP.NET Core Blazor , un modo per creare un'interfaccia utente Web interattiva sul lato client con .NET in un'app ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 06/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/index
ms.openlocfilehash: 086629f88ad04faed812bd16683e7d073f3e8ede
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122074"
---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="96453-103">Introduzione a ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="96453-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="96453-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="96453-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="96453-105">*Benvenuti in Blazor .*</span><span class="sxs-lookup"><span data-stu-id="96453-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="96453-106">è un Framework per la creazione di un'interfaccia utente Web interattiva sul lato client con .NET:</span><span class="sxs-lookup"><span data-stu-id="96453-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="96453-107">Creare interfacce utente interattive avanzate con C# invece di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="96453-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="96453-108">Condividere la logica dell'app scritta in .NET sul lato client e sul lato server.</span><span class="sxs-lookup"><span data-stu-id="96453-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="96453-109">Eseguire il rendering dell'interfaccia utente come HTML e CSS per un ampio supporto dei browser, inclusi i browser per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="96453-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="96453-110">Integrarsi con piattaforme di hosting moderne, ad esempio [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="96453-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="96453-111">L'uso di .NET per lo sviluppo Web lato client offre i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="96453-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="96453-112">scrivere codice in C# invece che in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="96453-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="96453-113">Permette di sfruttare l'ecosistema .NET esistente di librerie .NET.</span><span class="sxs-lookup"><span data-stu-id="96453-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="96453-114">Permette di condividere la logica dell'app tra server e client.</span><span class="sxs-lookup"><span data-stu-id="96453-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="96453-115">Permette di ottenere le prestazioni, l'affidabilità e la sicurezza di .NET.</span><span class="sxs-lookup"><span data-stu-id="96453-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="96453-116">produttività con Visual Studio in Windows, Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="96453-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="96453-117">basato su un set comune di linguaggi, framework e strumenti che sono stabili, ricchi di funzionalità e facili da usare.</span><span class="sxs-lookup"><span data-stu-id="96453-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="96453-118">Componenti</span><span class="sxs-lookup"><span data-stu-id="96453-118">Components</span></span>

Blazor<span data-ttu-id="96453-119">le app sono basate su *componenti*.</span><span class="sxs-lookup"><span data-stu-id="96453-119"> apps are based on *components*.</span></span> <span data-ttu-id="96453-120">Un componente Blazor di è un elemento dell'interfaccia utente, ad esempio una pagina, una finestra di dialogo o un form di immissione dati.</span><span class="sxs-lookup"><span data-stu-id="96453-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="96453-121">I componenti sono classi .NET compilati in assembly .NET che:</span><span class="sxs-lookup"><span data-stu-id="96453-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="96453-122">Definiscono la logica di rendering dell'interfaccia utente flessibile.</span><span class="sxs-lookup"><span data-stu-id="96453-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="96453-123">Gestiscono gli eventi utente.</span><span class="sxs-lookup"><span data-stu-id="96453-123">Handle user events.</span></span>
* <span data-ttu-id="96453-124">Possono essere annidati e riutilizzati.</span><span class="sxs-lookup"><span data-stu-id="96453-124">Can be nested and reused.</span></span>
* <span data-ttu-id="96453-125">Può essere condiviso e distribuito come [ Razor librerie di classi](xref:razor-pages/ui-class) o [pacchetti NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="96453-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="96453-126">La classe Component viene in genere scritta sotto forma di [Razor](xref:mvc/views/razor) pagina di markup con estensione *Razor* .</span><span class="sxs-lookup"><span data-stu-id="96453-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="96453-127">I componenti di Blazor sono definiti formalmente come \* Razor componenti\*di.</span><span class="sxs-lookup"><span data-stu-id="96453-127">Components in Blazor are formally referred to as *Razor components*.</span></span> Razor<span data-ttu-id="96453-128">è una sintassi per combinare il markup HTML con il codice C# progettato per la produttività degli sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="96453-128"> is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> Razor<span data-ttu-id="96453-129">consente di passare dal markup HTML al linguaggio C# e viceversa nello stesso file con supporto [IntelliSense](/visualstudio/ide/using-intellisense) .</span><span class="sxs-lookup"><span data-stu-id="96453-129"> allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> Razor<span data-ttu-id="96453-130">Anche le pagine e MVC usano Razor .</span><span class="sxs-lookup"><span data-stu-id="96453-130"> Pages and MVC also use Razor.</span></span> <span data-ttu-id="96453-131">Diversamente dalle Razor pagine e da MVC, che sono basate su un modello di richiesta/risposta, i componenti vengono usati in modo specifico per la composizione e la logica dell'interfaccia utente lato client.</span><span class="sxs-lookup"><span data-stu-id="96453-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="96453-132">Il Razor markup seguente illustra un componente (*Dialog. Razor*), che può essere annidato all'interno di un altro componente:</span><span class="sxs-lookup"><span data-stu-id="96453-132">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

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

<span data-ttu-id="96453-133">Il contenuto del corpo della finestra di dialogo (`ChildContent`) e il titolo (`Title`) vengono forniti dal componente che usa questo componente nella propria interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="96453-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="96453-134">`OnYes` è un metodo C# attivato dall'evento `onclick` del pulsante.</span><span class="sxs-lookup"><span data-stu-id="96453-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="96453-135">Usa tag HTML naturali per la composizione dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="96453-135"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="96453-136">Gli elementi HTML specificano i componenti e gli attributi di un tag passano valori alle proprietà del componente.</span><span class="sxs-lookup"><span data-stu-id="96453-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="96453-137">Nell'esempio seguente il componente `Index` usa il componente `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="96453-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="96453-138">`ChildContent` e `Title` vengono impostati dagli attributi e dal contenuto dell'elemento `<Dialog>`.</span><span class="sxs-lookup"><span data-stu-id="96453-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="96453-139">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="96453-139">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="96453-140">Il rendering della finestra di dialogo viene eseguito quando viene effettuato l'accesso all'elemento padre (*Index.razor*) in un browser:</span><span class="sxs-lookup"><span data-stu-id="96453-140">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Rendering del componente Dialog nel browser](index/_static/dialog.png)

<span data-ttu-id="96453-142">Quando questo componente viene usato nell'app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) e [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) accelera lo sviluppo con il completamento di sintassi e parametri.</span><span class="sxs-lookup"><span data-stu-id="96453-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="96453-143">Il rendering dei componenti viene eseguito in una rappresentazione in memoria del modello DOM (Document Object Model) del browser denominata *albero di rendering*, usato per aggiornare l'interfaccia utente in modo flessibile ed efficiente.</span><span class="sxs-lookup"><span data-stu-id="96453-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="96453-144">Webassembly</span><span class="sxs-lookup"><span data-stu-id="96453-144"> WebAssembly</span></span>

Blazor<span data-ttu-id="96453-145">Webassembly è un Framework di app a singola pagina per la creazione di app Web interattive sul lato client con .NET.</span><span class="sxs-lookup"><span data-stu-id="96453-145"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="96453-146">Webassembly usa gli standard Web aperti senza plug-in o transpilazione di codice e funziona in tutti i Web browser moderni, inclusi i browser per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="96453-146"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="96453-147">L'esecuzione di codice .NET all'interno di Web browser è resa possibile da [WebAssembly](https://webassembly.org) (tecnologia nota anche con l'abbreviazione *wasm*).</span><span class="sxs-lookup"><span data-stu-id="96453-147">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="96453-148">WebAssembly è un formato bytecode compatto ottimizzato per il download veloce e la velocità massima di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="96453-148">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="96453-149">WebAssembly è un standard Web aperto ed è supportato nei Web browser senza plug-in.</span><span class="sxs-lookup"><span data-stu-id="96453-149">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="96453-150">Il codice WebAssembly può accedere a tutte le funzionalità del browser tramite l'*interoperabilità JavaScript* (*JavaScript interop*).</span><span class="sxs-lookup"><span data-stu-id="96453-150">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="96453-151">Il codice .NET eseguito tramite WebAssembly nel browser viene eseguito nella sandbox JavaScript del browser con le misure di sicurezza offerte dalla sandbox per la protezione da azioni dannose nel computer client.</span><span class="sxs-lookup"><span data-stu-id="96453-151">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="96453-152">![BlazorWebassembly esegue il codice .NET nel browser con webassembly.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="96453-152">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="96453-153">Quando un' Blazor app webassembly viene compilata ed eseguita in un browser:</span><span class="sxs-lookup"><span data-stu-id="96453-153">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="96453-154">I file e i file di codice C# Razor vengono compilati in assembly .NET.</span><span class="sxs-lookup"><span data-stu-id="96453-154">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="96453-155">Gli assembly e il runtime .NET vengono scaricati nel browser.</span><span class="sxs-lookup"><span data-stu-id="96453-155">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="96453-156">Webassembly avvia il Runtime .NET e configura il runtime per caricare gli assembly per l'app.</span><span class="sxs-lookup"><span data-stu-id="96453-156"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="96453-157">Il Blazor runtime di webassembly usa l'interoperabilità JavaScript per gestire la manipolazione Dom e le chiamate API del browser.</span><span class="sxs-lookup"><span data-stu-id="96453-157">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="96453-158">La dimensione dell'app pubblicata, ovvero la *dimensione del payload*, è un fattore cruciale per le prestazioni ai fini dell'usabilità dell'app.</span><span class="sxs-lookup"><span data-stu-id="96453-158">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="96453-159">Un'app di grandi dimensioni impiega relativamente molto tempo a essere scaricata in un browser, influendo negativamente sull'esperienza utente.</span><span class="sxs-lookup"><span data-stu-id="96453-159">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="96453-160">Webassembly ottimizza le dimensioni del payload per ridurre i tempi di download:</span><span class="sxs-lookup"><span data-stu-id="96453-160"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="96453-161">Il codice non usato viene rimosso dall'app quando questa viene pubblicata dal [linker del linguaggio intermedio](xref:blazor/host-and-deploy/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="96453-161">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="96453-162">Le risposte HTTP vengono compresse.</span><span class="sxs-lookup"><span data-stu-id="96453-162">HTTP responses are compressed.</span></span>
* <span data-ttu-id="96453-163">Il runtime e gli assembly .NET vengono memorizzati nella cache nel browser.</span><span class="sxs-lookup"><span data-stu-id="96453-163">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="96453-164">Server</span><span class="sxs-lookup"><span data-stu-id="96453-164"> Server</span></span>

Blazor<span data-ttu-id="96453-165">separa la logica di rendering dei componenti dal modo in cui vengono applicati gli aggiornamenti dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="96453-165"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="96453-166">Il server fornisce il supporto per l'hosting di Razor componenti nel server in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="96453-166"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="96453-167">Gli aggiornamenti dell'interfaccia utente vengono gestiti tramite una [SignalR](xref:signalr/introduction) connessione.</span><span class="sxs-lookup"><span data-stu-id="96453-167">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="96453-168">Il runtime gestisce l'invio di eventi dell'interfaccia utente dal browser al server e quindi applica gli aggiornamenti dell'interfaccia utente inviati dal server di nuovo al browser dopo l'esecuzione dei componenti.</span><span class="sxs-lookup"><span data-stu-id="96453-168">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="96453-169">La connessione utilizzata dal Blazor Server per comunicare con il browser viene utilizzata anche per gestire le chiamate di interoperabilità JavaScript.</span><span class="sxs-lookup"><span data-stu-id="96453-169">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="96453-170">![BlazorIl server esegue il codice .NET sul server e interagisce con il Document Object Model sul client su una SignalR connessione](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="96453-170">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="96453-171">Interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="96453-171">JavaScript interop</span></span>

<span data-ttu-id="96453-172">Per le app che richiedono librerie JavaScript di terze parti e l'accesso alle API del browser, i componenti supportano l'interoperabilità con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="96453-172">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="96453-173">I componenti sono in grado di usare qualsiasi libreria o API supportata da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="96453-173">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="96453-174">Il codice C# può effettuare chiamate nel codice JavaScript e vice versa.</span><span class="sxs-lookup"><span data-stu-id="96453-174">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="96453-175">Per altre informazioni, vedere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="96453-175">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="96453-176">Condivisione del codice e .NET Standard</span><span class="sxs-lookup"><span data-stu-id="96453-176">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="96453-177">implementa [.NET Standard 2,1](/dotnet/standard/net-standard), che consente Blazor ai progetti di fare riferimento a librerie conformi alle specifiche .NET standard 2,1 o versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="96453-177"> implements [.NET Standard 2.1](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard 2.1 or earlier specifications.</span></span> <span data-ttu-id="96453-178">.NET Standard è una specifica formale delle API .NET comuni tra le implementazioni di .NET.</span><span class="sxs-lookup"><span data-stu-id="96453-178">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="96453-179">Le librerie di classi .NET Standard possono essere condivise tra diverse piattaforme .NET, ad esempio Blazor .NET Framework, .NET Core, Novell, mono e Unity.</span><span class="sxs-lookup"><span data-stu-id="96453-179">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="96453-180">Le API non valide all'interno di un Web browser (ad esempio per l'accesso al file system, l'apertura di un socket e la gestione dei thread) generano un'eccezione <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="96453-180">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96453-181">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="96453-181">Additional resources</span></span>

* [<span data-ttu-id="96453-182">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="96453-182">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="96453-183">Guida a C#</span><span class="sxs-lookup"><span data-stu-id="96453-183">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="96453-184">HTML</span><span class="sxs-lookup"><span data-stu-id="96453-184">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="96453-185">[Awesome Blazor ](https://github.com/AdrienTorris/awesome-blazor) collegamenti della community</span><span class="sxs-lookup"><span data-stu-id="96453-185">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
