---
title: Risolvere i problemi ed eseguire il debug di progetti ASP.NET Core
author: Rick-Anderson
description: Riconoscere e risolvere i problemi di avvisi ed errori con i progetti ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/troubleshoot
ms.openlocfilehash: a05b5f85ee9e399daf35c32dabe0149be38ff6c2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021354"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="3fb33-103">Risolvere i problemi ed eseguire il debug di progetti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fb33-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="3fb33-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3fb33-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3fb33-105">I collegamenti seguenti forniscono indicazioni sulla risoluzione dei problemi:</span><span class="sxs-lookup"><span data-stu-id="3fb33-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="3fb33-106">NDC Conference (Londra, 2018): diagnosi dei problemi nelle applicazioni ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fb33-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="3fb33-107">Blog di ASP.NET: risoluzione dei problemi di prestazioni ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fb33-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="3fb33-108">Avvisi di .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="3fb33-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="3fb33-109">Sono installate entrambe le versioni a 32 bit e 64 bit del .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="3fb33-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="3fb33-110">Nella finestra di dialogo **nuovo progetto** per ASP.NET Core, è possibile che venga visualizzato il seguente avviso:</span><span class="sxs-lookup"><span data-stu-id="3fb33-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="3fb33-111">Sono installate entrambe le versioni a 32 bit e a 64 bit del .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="3fb33-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="3fb33-112">Vengono visualizzati solo i modelli delle versioni a 64 bit installate in ' C: \\ Program Files \\ DotNet \\ SDK \\ '.</span><span class="sxs-lookup"><span data-stu-id="3fb33-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="3fb33-113">Questo avviso viene visualizzato quando vengono installate entrambe le versioni a 32 bit (x86) e 64-bit (x64) del [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="3fb33-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="3fb33-114">Di seguito sono elencati i motivi comuni per cui è possibile installare entrambe le versioni:</span><span class="sxs-lookup"><span data-stu-id="3fb33-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="3fb33-115">Il programma di installazione .NET Core SDK è stato scaricato in origine con un computer a 32 bit, ma è stato copiato e installato in un computer a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="3fb33-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="3fb33-116">Il .NET Core SDK a 32 bit è stato installato da un'altra applicazione.</span><span class="sxs-lookup"><span data-stu-id="3fb33-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="3fb33-117">È stata scaricata e installata la versione errata.</span><span class="sxs-lookup"><span data-stu-id="3fb33-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="3fb33-118">Disinstallare la .NET Core SDK a 32 bit per evitare questo avviso.</span><span class="sxs-lookup"><span data-stu-id="3fb33-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="3fb33-119">Disinstallare da **Pannello di controllo**  >  **programmi e funzionalità**  >  **disinstallare o modificare un programma**.</span><span class="sxs-lookup"><span data-stu-id="3fb33-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="3fb33-120">Se si comprende il motivo per cui si verifica l'avviso e le relative implicazioni, è possibile ignorare l'avviso.</span><span class="sxs-lookup"><span data-stu-id="3fb33-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="3fb33-121">Il .NET Core SDK viene installato in più posizioni</span><span class="sxs-lookup"><span data-stu-id="3fb33-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="3fb33-122">Nella finestra di dialogo **nuovo progetto** per ASP.NET Core, è possibile che venga visualizzato il seguente avviso:</span><span class="sxs-lookup"><span data-stu-id="3fb33-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="3fb33-123">Il .NET Core SDK viene installato in più posizioni.</span><span class="sxs-lookup"><span data-stu-id="3fb33-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="3fb33-124">Vengono visualizzati solo i modelli degli SDK installati in ' C: \\ programmi \\ DotNet \\ SDK \\ '.</span><span class="sxs-lookup"><span data-stu-id="3fb33-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="3fb33-125">Questo messaggio viene visualizzato quando si dispone di almeno un'installazione del .NET Core SDK in una directory al di fuori di \*C: \\ Program Files \\ DotNet \\ SDK \\ \*.</span><span class="sxs-lookup"><span data-stu-id="3fb33-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="3fb33-126">Questa situazione si verifica in genere quando il .NET Core SDK è stato distribuito in un computer tramite copia/incolla anziché il programma di installazione MSI.</span><span class="sxs-lookup"><span data-stu-id="3fb33-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="3fb33-127">Disinstallare tutti i runtime e gli SDK di .NET Core a 32 bit per evitare questo avviso.</span><span class="sxs-lookup"><span data-stu-id="3fb33-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="3fb33-128">Disinstallare da **Pannello di controllo**  >  **programmi e funzionalità**  >  **disinstallare o modificare un programma**.</span><span class="sxs-lookup"><span data-stu-id="3fb33-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="3fb33-129">Se si comprende il motivo per cui si verifica l'avviso e le relative implicazioni, è possibile ignorare l'avviso.</span><span class="sxs-lookup"><span data-stu-id="3fb33-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="3fb33-130">Non sono stati rilevati SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="3fb33-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="3fb33-131">Nella finestra di dialogo **nuovo progetto** di Visual Studio per ASP.NET Core, è possibile che venga visualizzato il seguente avviso:</span><span class="sxs-lookup"><span data-stu-id="3fb33-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="3fb33-132">Non sono stati rilevati SDK .NET Core, assicurarsi che siano inclusi nella variabile di ambiente `PATH` .</span><span class="sxs-lookup"><span data-stu-id="3fb33-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="3fb33-133">Quando si esegue un `dotnet` comando, l'avviso viene visualizzato come segue:</span><span class="sxs-lookup"><span data-stu-id="3fb33-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="3fb33-134">Non è stato possibile trovare gli SDK DotNet installati.</span><span class="sxs-lookup"><span data-stu-id="3fb33-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="3fb33-135">Questi avvisi vengono visualizzati quando la variabile `PATH` di ambiente non punta ad alcun SDK di .NET Core nel computer.</span><span class="sxs-lookup"><span data-stu-id="3fb33-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="3fb33-136">Per risolvere questo problema:</span><span class="sxs-lookup"><span data-stu-id="3fb33-136">To resolve this problem:</span></span>

* <span data-ttu-id="3fb33-137">Installare .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="3fb33-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="3fb33-138">Ottenere il programma di installazione più recente da [download di .NET](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="3fb33-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="3fb33-139">Verificare che la `PATH` variabile di ambiente punti al percorso in cui è installato l'SDK ( `C:\Program Files\dotnet\` per a 64 bit/x64 o `C:\Program Files (x86)\dotnet\` per 32 bit/x86).</span><span class="sxs-lookup"><span data-stu-id="3fb33-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="3fb33-140">Il programma di installazione dell'SDK normalmente imposta `PATH` .</span><span class="sxs-lookup"><span data-stu-id="3fb33-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="3fb33-141">Installare sempre gli stessi SDK e Runtime bit nello stesso computer.</span><span class="sxs-lookup"><span data-stu-id="3fb33-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="3fb33-142">SDK mancante dopo l'installazione del bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="3fb33-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="3fb33-143">L'installazione del [bundle di hosting .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifica `PATH` quando installa il runtime di .NET Core in modo che punti alla versione 32 bit (x86) di .NET Core ( `C:\Program Files (x86)\dotnet\` ).</span><span class="sxs-lookup"><span data-stu-id="3fb33-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="3fb33-144">Questo può causare la mancata presenza di SDK quando viene usato il comando .NET Core a 32 bit (x86) `dotnet` ([non sono stati rilevati SDK .NET Core](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="3fb33-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="3fb33-145">Per risolvere il problema, passare `C:\Program Files\dotnet\` a una posizione precedente `C:\Program Files (x86)\dotnet\` in `PATH` .</span><span class="sxs-lookup"><span data-stu-id="3fb33-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="3fb33-146">Ottenere dati da un'app</span><span class="sxs-lookup"><span data-stu-id="3fb33-146">Obtain data from an app</span></span>

<span data-ttu-id="3fb33-147">Se un'app è in grado di rispondere alle richieste, è possibile ottenere i dati seguenti dall'app usando il middleware:</span><span class="sxs-lookup"><span data-stu-id="3fb33-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="3fb33-148">Richiesta: metodo, schema, host, pathbase, percorso, stringa di query, intestazioni</span><span class="sxs-lookup"><span data-stu-id="3fb33-148">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="3fb33-149">Connessione: indirizzo IP remoto, porta remota, indirizzo IP locale, porta locale, certificato client</span><span class="sxs-lookup"><span data-stu-id="3fb33-149">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="3fb33-150">Identity: Nome, nome visualizzato</span><span class="sxs-lookup"><span data-stu-id="3fb33-150">Identity: Name, display name</span></span>
* <span data-ttu-id="3fb33-151">Impostazioni di configurazione</span><span class="sxs-lookup"><span data-stu-id="3fb33-151">Configuration settings</span></span>
* <span data-ttu-id="3fb33-152">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="3fb33-152">Environment variables</span></span>

<span data-ttu-id="3fb33-153">Inserire il codice [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) seguente all'inizio della pipeline di `Startup.Configure` elaborazione delle richieste del metodo.</span><span class="sxs-lookup"><span data-stu-id="3fb33-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="3fb33-154">L'ambiente viene verificato prima dell'esecuzione del middleware per garantire che il codice venga eseguito solo nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3fb33-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="3fb33-155">Per ottenere l'ambiente, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="3fb33-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="3fb33-156">Inserire l'oggetto `IHostingEnvironment` nel `Startup.Configure` metodo e controllare l'ambiente con la variabile locale.</span><span class="sxs-lookup"><span data-stu-id="3fb33-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="3fb33-157">Il codice di esempio seguente illustra questo approccio.</span><span class="sxs-lookup"><span data-stu-id="3fb33-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="3fb33-158">Assegnare l'ambiente a una proprietà nella `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="3fb33-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="3fb33-159">Controllare l'ambiente utilizzando la proprietà (ad esempio, `if (Environment.IsDevelopment())` ).</span><span class="sxs-lookup"><span data-stu-id="3fb33-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="3fb33-160">Eseguire il debug di app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fb33-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="3fb33-161">I collegamenti seguenti forniscono informazioni sul debug di ASP.NET Core app.</span><span class="sxs-lookup"><span data-stu-id="3fb33-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="3fb33-162">Debug di ASP core in Linux</span><span class="sxs-lookup"><span data-stu-id="3fb33-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="3fb33-163">Debug di .NET Core su UNIX tramite SSH</span><span class="sxs-lookup"><span data-stu-id="3fb33-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="3fb33-164">Guida introduttiva: eseguire il debug di ASP.NET con il debugger di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fb33-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="3fb33-165">Per ulteriori informazioni sul debug, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) .</span><span class="sxs-lookup"><span data-stu-id="3fb33-165">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
