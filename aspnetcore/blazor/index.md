---
<span data-ttu-id="cdb0c-101">title:' Introduction to ASP.NET Core Blazor ' Author: Description:' Explore ASP.NET Core Blazor , un modo per creare un'interfaccia utente Web interattiva sul lato client con .NET in un'app ASP.NET Core '.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-101">title: 'Introduction to ASP.NET Core Blazor' author: description: 'Explore ASP.NET Core Blazor, a way to build interactive client-side web UI with .NET in an ASP.NET Core app.'</span></span>
<span data-ttu-id="cdb0c-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cdb0c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdb0c-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdb0c-103">'Blazor'</span></span>
- <span data-ttu-id="cdb0c-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdb0c-104">'Identity'</span></span>
- <span data-ttu-id="cdb0c-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdb0c-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdb0c-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdb0c-106">'Razor'</span></span>
- <span data-ttu-id="cdb0c-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cdb0c-107">'SignalR' uid:</span></span> 

---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="cdb0c-108">Introduzione a ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="cdb0c-108">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="cdb0c-109">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cdb0c-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cdb0c-110">*Benvenuti in Blazor .*</span><span class="sxs-lookup"><span data-stu-id="cdb0c-110">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="cdb0c-111">è un Framework per la creazione di un'interfaccia utente Web interattiva sul lato client con .NET:</span><span class="sxs-lookup"><span data-stu-id="cdb0c-111"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="cdb0c-112">Creare interfacce utente interattive avanzate con C# invece di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-112">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="cdb0c-113">Condividere la logica dell'app scritta in .NET sul lato client e sul lato server.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-113">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="cdb0c-114">Eseguire il rendering dell'interfaccia utente come HTML e CSS per un ampio supporto dei browser, inclusi i browser per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-114">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="cdb0c-115">Integrarsi con piattaforme di hosting moderne, ad esempio [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="cdb0c-115">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="cdb0c-116">L'uso di .NET per lo sviluppo Web lato client offre i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="cdb0c-116">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="cdb0c-117">scrivere codice in C# invece che in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-117">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="cdb0c-118">Permette di sfruttare l'ecosistema .NET esistente di librerie .NET.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-118">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="cdb0c-119">Permette di condividere la logica dell'app tra server e client.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-119">Share app logic across server and client.</span></span>
* <span data-ttu-id="cdb0c-120">Permette di ottenere le prestazioni, l'affidabilità e la sicurezza di .NET.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-120">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="cdb0c-121">produttività con Visual Studio in Windows, Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-121">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="cdb0c-122">basato su un set comune di linguaggi, framework e strumenti che sono stabili, ricchi di funzionalità e facili da usare.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-122">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="cdb0c-123">Componenti</span><span class="sxs-lookup"><span data-stu-id="cdb0c-123">Components</span></span>

Blazor<span data-ttu-id="cdb0c-124">le app sono basate su *componenti*.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-124"> apps are based on *components*.</span></span> <span data-ttu-id="cdb0c-125">Un componente Blazor di è un elemento dell'interfaccia utente, ad esempio una pagina, una finestra di dialogo o un form di immissione dati.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-125">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="cdb0c-126">I componenti sono classi .NET compilati in assembly .NET che:</span><span class="sxs-lookup"><span data-stu-id="cdb0c-126">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="cdb0c-127">Definiscono la logica di rendering dell'interfaccia utente flessibile.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-127">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="cdb0c-128">Gestiscono gli eventi utente.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-128">Handle user events.</span></span>
* <span data-ttu-id="cdb0c-129">Possono essere annidati e riutilizzati.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-129">Can be nested and reused.</span></span>
* <span data-ttu-id="cdb0c-130">Può essere condiviso e distribuito come [ Razor librerie di classi](xref:razor-pages/ui-class) o [pacchetti NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="cdb0c-130">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="cdb0c-131">La classe Component viene in genere scritta sotto forma di [Razor](xref:mvc/views/razor) pagina di markup con estensione *Razor* .</span><span class="sxs-lookup"><span data-stu-id="cdb0c-131">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="cdb0c-132">I componenti di Blazor sono definiti formalmente come \* Razor componenti\*di.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-132">Components in Blazor are formally referred to as *Razor components*.</span></span> Razor<span data-ttu-id="cdb0c-133">è una sintassi per combinare il markup HTML con il codice C# progettato per la produttività degli sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-133"> is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> Razor<span data-ttu-id="cdb0c-134">consente di passare dal markup HTML al linguaggio C# e viceversa nello stesso file con supporto [IntelliSense](/visualstudio/ide/using-intellisense) .</span><span class="sxs-lookup"><span data-stu-id="cdb0c-134"> allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> Razor<span data-ttu-id="cdb0c-135">Anche le pagine e MVC usano Razor .</span><span class="sxs-lookup"><span data-stu-id="cdb0c-135"> Pages and MVC also use Razor.</span></span> <span data-ttu-id="cdb0c-136">Diversamente dalle Razor pagine e da MVC, che sono basate su un modello di richiesta/risposta, i componenti vengono usati in modo specifico per la composizione e la logica dell'interfaccia utente lato client.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-136">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="cdb0c-137">Il Razor markup seguente illustra un componente (*Dialog. Razor*), che può essere annidato all'interno di un altro componente:</span><span class="sxs-lookup"><span data-stu-id="cdb0c-137">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

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

<span data-ttu-id="cdb0c-138">Il contenuto del corpo della finestra di dialogo (`ChildContent`) e il titolo (`Title`) vengono forniti dal componente che usa questo componente nella propria interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-138">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="cdb0c-139">`OnYes` è un metodo C# attivato dall'evento `onclick` del pulsante.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-139">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="cdb0c-140">Usa tag HTML naturali per la composizione dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-140"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="cdb0c-141">Gli elementi HTML specificano i componenti e gli attributi di un tag passano valori alle proprietà del componente.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-141">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="cdb0c-142">Nell'esempio seguente il componente `Index` usa il componente `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-142">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="cdb0c-143">`ChildContent` e `Title` vengono impostati dagli attributi e dal contenuto dell'elemento `<Dialog>`.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-143">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="cdb0c-144">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="cdb0c-144">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="cdb0c-145">Il rendering della finestra di dialogo viene eseguito quando viene effettuato l'accesso all'elemento padre (*Index.razor*) in un browser:</span><span class="sxs-lookup"><span data-stu-id="cdb0c-145">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Rendering del componente Dialog nel browser](index/_static/dialog.png)

<span data-ttu-id="cdb0c-147">Quando questo componente viene usato nell'app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) e [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) accelera lo sviluppo con il completamento di sintassi e parametri.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-147">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="cdb0c-148">Il rendering dei componenti viene eseguito in una rappresentazione in memoria del modello DOM (Document Object Model) del browser denominata *albero di rendering*, usato per aggiornare l'interfaccia utente in modo flessibile ed efficiente.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-148">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="cdb0c-149">Webassembly</span><span class="sxs-lookup"><span data-stu-id="cdb0c-149"> WebAssembly</span></span>

Blazor<span data-ttu-id="cdb0c-150">Webassembly è un Framework di app a singola pagina per la creazione di app Web interattive sul lato client con .NET.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-150"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="cdb0c-151">Webassembly usa gli standard Web aperti senza plug-in o transpilazione di codice e funziona in tutti i Web browser moderni, inclusi i browser per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-151"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="cdb0c-152">L'esecuzione di codice .NET all'interno di Web browser è resa possibile da [WebAssembly](https://webassembly.org) (tecnologia nota anche con l'abbreviazione *wasm*).</span><span class="sxs-lookup"><span data-stu-id="cdb0c-152">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="cdb0c-153">WebAssembly è un formato bytecode compatto ottimizzato per il download veloce e la velocità massima di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-153">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="cdb0c-154">WebAssembly è un standard Web aperto ed è supportato nei Web browser senza plug-in.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-154">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="cdb0c-155">Il codice WebAssembly può accedere a tutte le funzionalità del browser tramite l'*interoperabilità JavaScript* (*JavaScript interop*).</span><span class="sxs-lookup"><span data-stu-id="cdb0c-155">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="cdb0c-156">Il codice .NET eseguito tramite WebAssembly nel browser viene eseguito nella sandbox JavaScript del browser con le misure di sicurezza offerte dalla sandbox per la protezione da azioni dannose nel computer client.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-156">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="cdb0c-157">![BlazorWebassembly esegue il codice .NET nel browser con webassembly.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="cdb0c-157">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="cdb0c-158">Quando un' Blazor app webassembly viene compilata ed eseguita in un browser:</span><span class="sxs-lookup"><span data-stu-id="cdb0c-158">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="cdb0c-159">I file e i file di codice C# Razor vengono compilati in assembly .NET.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-159">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="cdb0c-160">Gli assembly e il runtime .NET vengono scaricati nel browser.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-160">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="cdb0c-161">Webassembly avvia il Runtime .NET e configura il runtime per caricare gli assembly per l'app.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-161"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="cdb0c-162">Il Blazor runtime di webassembly usa l'interoperabilità JavaScript per gestire la manipolazione Dom e le chiamate API del browser.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-162">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="cdb0c-163">La dimensione dell'app pubblicata, ovvero la *dimensione del payload*, è un fattore cruciale per le prestazioni ai fini dell'usabilità dell'app.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-163">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="cdb0c-164">Un'app di grandi dimensioni impiega relativamente molto tempo a essere scaricata in un browser, influendo negativamente sull'esperienza utente.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-164">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="cdb0c-165">Webassembly ottimizza le dimensioni del payload per ridurre i tempi di download:</span><span class="sxs-lookup"><span data-stu-id="cdb0c-165"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="cdb0c-166">Il codice non usato viene rimosso dall'app quando questa viene pubblicata dal [linker del linguaggio intermedio](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="cdb0c-166">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="cdb0c-167">Le risposte HTTP vengono compresse.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-167">HTTP responses are compressed.</span></span>
* <span data-ttu-id="cdb0c-168">Il runtime e gli assembly .NET vengono memorizzati nella cache nel browser.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-168">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="cdb0c-169">Server</span><span class="sxs-lookup"><span data-stu-id="cdb0c-169"> Server</span></span>

Blazor<span data-ttu-id="cdb0c-170">separa la logica di rendering dei componenti dal modo in cui vengono applicati gli aggiornamenti dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-170"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="cdb0c-171">Il server fornisce il supporto per l'hosting di Razor componenti nel server in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-171"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="cdb0c-172">Gli aggiornamenti dell'interfaccia utente vengono gestiti tramite una [SignalR](xref:signalr/introduction) connessione.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-172">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="cdb0c-173">Il runtime gestisce l'invio di eventi dell'interfaccia utente dal browser al server e quindi applica gli aggiornamenti dell'interfaccia utente inviati dal server di nuovo al browser dopo l'esecuzione dei componenti.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-173">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="cdb0c-174">La connessione utilizzata dal Blazor Server per comunicare con il browser viene utilizzata anche per gestire le chiamate di interoperabilità JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-174">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="cdb0c-175">![BlazorIl server esegue il codice .NET sul server e interagisce con il Document Object Model sul client su una SignalR connessione](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="cdb0c-175">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="cdb0c-176">Interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="cdb0c-176">JavaScript interop</span></span>

<span data-ttu-id="cdb0c-177">Per le app che richiedono librerie JavaScript di terze parti e l'accesso alle API del browser, i componenti supportano l'interoperabilità con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-177">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="cdb0c-178">I componenti sono in grado di usare qualsiasi libreria o API supportata da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-178">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="cdb0c-179">Il codice C# può effettuare chiamate nel codice JavaScript e vice versa.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-179">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="cdb0c-180">Per altre informazioni, vedere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="cdb0c-180">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="cdb0c-181">Condivisione del codice e .NET Standard</span><span class="sxs-lookup"><span data-stu-id="cdb0c-181">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="cdb0c-182">implementa [.NET Standard 2,0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="cdb0c-182"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="cdb0c-183">.NET Standard è una specifica formale delle API .NET comuni tra le implementazioni di .NET.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-183">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="cdb0c-184">Le librerie di classi .NET Standard possono essere condivise tra diverse piattaforme .NET, ad esempio Blazor .NET Framework, .NET Core, Novell, mono e Unity.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-184">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="cdb0c-185">Le API non valide all'interno di un Web browser (ad esempio per l'accesso al file system, l'apertura di un socket e la gestione dei thread) generano un'eccezione <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="cdb0c-185">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cdb0c-186">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="cdb0c-186">Additional resources</span></span>

* [<span data-ttu-id="cdb0c-187">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="cdb0c-187">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="cdb0c-188">Guida a C#</span><span class="sxs-lookup"><span data-stu-id="cdb0c-188">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="cdb0c-189">HTML</span><span class="sxs-lookup"><span data-stu-id="cdb0c-189">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="cdb0c-190">[Awesome Blazor ](https://github.com/AdrienTorris/awesome-blazor) collegamenti della community</span><span class="sxs-lookup"><span data-stu-id="cdb0c-190">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
