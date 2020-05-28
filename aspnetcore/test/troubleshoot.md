---
<span data-ttu-id="1b201-101">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1b201-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b201-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b201-102">'Blazor'</span></span>
- <span data-ttu-id="1b201-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b201-103">'Identity'</span></span>
- <span data-ttu-id="1b201-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b201-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b201-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b201-105">'Razor'</span></span>
- <span data-ttu-id="1b201-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="1b201-106">'SignalR' uid:</span></span> 

---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="1b201-107">Risolvere i problemi ed eseguire il debug di progetti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b201-107">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="1b201-108">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1b201-108">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1b201-109">I collegamenti seguenti forniscono indicazioni sulla risoluzione dei problemi:</span><span class="sxs-lookup"><span data-stu-id="1b201-109">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="1b201-110">NDC Conference (Londra, 2018): diagnosi dei problemi nelle applicazioni ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b201-110">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="1b201-111">Blog di ASP.NET: risoluzione dei problemi di prestazioni ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b201-111">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="1b201-112">Avvisi di .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="1b201-112">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="1b201-113">Sono installate entrambe le versioni a 32 bit e 64 bit del .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="1b201-113">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="1b201-114">Nella finestra di dialogo **nuovo progetto** per ASP.NET Core, è possibile che venga visualizzato il seguente avviso:</span><span class="sxs-lookup"><span data-stu-id="1b201-114">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="1b201-115">Sono installate entrambe le versioni a 32 bit e a 64 bit del .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="1b201-115">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="1b201-116">Vengono visualizzati solo i modelli delle versioni a 64 bit installate in ' C: \\ Program Files \\ DotNet \\ SDK \\ '.</span><span class="sxs-lookup"><span data-stu-id="1b201-116">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="1b201-117">Questo avviso viene visualizzato quando vengono installate entrambe le versioni a 32 bit (x86) e 64-bit (x64) del [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="1b201-117">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="1b201-118">Di seguito sono elencati i motivi comuni per cui è possibile installare entrambe le versioni:</span><span class="sxs-lookup"><span data-stu-id="1b201-118">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="1b201-119">Il programma di installazione .NET Core SDK è stato scaricato in origine con un computer a 32 bit, ma è stato copiato e installato in un computer a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="1b201-119">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="1b201-120">Il .NET Core SDK a 32 bit è stato installato da un'altra applicazione.</span><span class="sxs-lookup"><span data-stu-id="1b201-120">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="1b201-121">È stata scaricata e installata la versione errata.</span><span class="sxs-lookup"><span data-stu-id="1b201-121">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="1b201-122">Disinstallare la .NET Core SDK a 32 bit per evitare questo avviso.</span><span class="sxs-lookup"><span data-stu-id="1b201-122">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="1b201-123">Disinstallare da **Pannello di controllo**  >  **programmi e funzionalità**  >  **disinstallare o modificare un programma**.</span><span class="sxs-lookup"><span data-stu-id="1b201-123">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="1b201-124">Se si comprende il motivo per cui si verifica l'avviso e le relative implicazioni, è possibile ignorare l'avviso.</span><span class="sxs-lookup"><span data-stu-id="1b201-124">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="1b201-125">Il .NET Core SDK viene installato in più posizioni</span><span class="sxs-lookup"><span data-stu-id="1b201-125">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="1b201-126">Nella finestra di dialogo **nuovo progetto** per ASP.NET Core, è possibile che venga visualizzato il seguente avviso:</span><span class="sxs-lookup"><span data-stu-id="1b201-126">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="1b201-127">Il .NET Core SDK viene installato in più posizioni.</span><span class="sxs-lookup"><span data-stu-id="1b201-127">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="1b201-128">Vengono visualizzati solo i modelli degli SDK installati in ' C: \\ programmi \\ DotNet \\ SDK \\ '.</span><span class="sxs-lookup"><span data-stu-id="1b201-128">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="1b201-129">Questo messaggio viene visualizzato quando si dispone di almeno un'installazione del .NET Core SDK in una directory al di fuori di \*C: \\ Program Files \\ DotNet \\ SDK \\ \*.</span><span class="sxs-lookup"><span data-stu-id="1b201-129">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="1b201-130">Questa situazione si verifica in genere quando il .NET Core SDK è stato distribuito in un computer tramite copia/incolla anziché il programma di installazione MSI.</span><span class="sxs-lookup"><span data-stu-id="1b201-130">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="1b201-131">Disinstallare tutti i runtime e gli SDK di .NET Core a 32 bit per evitare questo avviso.</span><span class="sxs-lookup"><span data-stu-id="1b201-131">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="1b201-132">Disinstallare da **Pannello di controllo**  >  **programmi e funzionalità**  >  **disinstallare o modificare un programma**.</span><span class="sxs-lookup"><span data-stu-id="1b201-132">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="1b201-133">Se si comprende il motivo per cui si verifica l'avviso e le relative implicazioni, è possibile ignorare l'avviso.</span><span class="sxs-lookup"><span data-stu-id="1b201-133">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="1b201-134">Non sono stati rilevati SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b201-134">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="1b201-135">Nella finestra di dialogo **nuovo progetto** di Visual Studio per ASP.NET Core, è possibile che venga visualizzato il seguente avviso:</span><span class="sxs-lookup"><span data-stu-id="1b201-135">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="1b201-136">Non sono stati rilevati SDK .NET Core, assicurarsi che siano inclusi nella variabile di ambiente `PATH` .</span><span class="sxs-lookup"><span data-stu-id="1b201-136">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="1b201-137">Quando si esegue un `dotnet` comando, l'avviso viene visualizzato come segue:</span><span class="sxs-lookup"><span data-stu-id="1b201-137">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="1b201-138">Non è stato possibile trovare gli SDK DotNet installati.</span><span class="sxs-lookup"><span data-stu-id="1b201-138">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="1b201-139">Questi avvisi vengono visualizzati quando la variabile `PATH` di ambiente non punta ad alcun SDK di .NET Core nel computer.</span><span class="sxs-lookup"><span data-stu-id="1b201-139">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="1b201-140">Per risolvere questo problema:</span><span class="sxs-lookup"><span data-stu-id="1b201-140">To resolve this problem:</span></span>

* <span data-ttu-id="1b201-141">Installare .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="1b201-141">Install the .NET Core SDK.</span></span> <span data-ttu-id="1b201-142">Ottenere il programma di installazione più recente da [download di .NET](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="1b201-142">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="1b201-143">Verificare che la `PATH` variabile di ambiente punti al percorso in cui è installato l'SDK ( `C:\Program Files\dotnet\` per a 64 bit/x64 o `C:\Program Files (x86)\dotnet\` per 32 bit/x86).</span><span class="sxs-lookup"><span data-stu-id="1b201-143">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="1b201-144">Il programma di installazione dell'SDK normalmente imposta `PATH` .</span><span class="sxs-lookup"><span data-stu-id="1b201-144">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="1b201-145">Installare sempre gli stessi SDK e Runtime bit nello stesso computer.</span><span class="sxs-lookup"><span data-stu-id="1b201-145">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="1b201-146">SDK mancante dopo l'installazione del bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b201-146">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="1b201-147">L'installazione del [bundle di hosting .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifica `PATH` quando installa il runtime di .NET Core in modo che punti alla versione 32 bit (x86) di .NET Core ( `C:\Program Files (x86)\dotnet\` ).</span><span class="sxs-lookup"><span data-stu-id="1b201-147">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="1b201-148">Questo può causare la mancata presenza di SDK quando viene usato il comando .NET Core a 32 bit (x86) `dotnet` ([non sono stati rilevati SDK .NET Core](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="1b201-148">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="1b201-149">Per risolvere il problema, passare `C:\Program Files\dotnet\` a una posizione precedente `C:\Program Files (x86)\dotnet\` in `PATH` .</span><span class="sxs-lookup"><span data-stu-id="1b201-149">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="1b201-150">Ottenere dati da un'app</span><span class="sxs-lookup"><span data-stu-id="1b201-150">Obtain data from an app</span></span>

<span data-ttu-id="1b201-151">Se un'app è in grado di rispondere alle richieste, è possibile ottenere i dati seguenti dall'app usando il middleware:</span><span class="sxs-lookup"><span data-stu-id="1b201-151">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="1b201-152">Richiesta: metodo, schema, host, pathbase, percorso, stringa di query, intestazioni</span><span class="sxs-lookup"><span data-stu-id="1b201-152">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="1b201-153">Connessione: indirizzo IP remoto, porta remota, indirizzo IP locale, porta locale, certificato client</span><span class="sxs-lookup"><span data-stu-id="1b201-153">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* Identity: Name, display name
* <span data-ttu-id="1b201-154">Impostazioni di configurazione</span><span class="sxs-lookup"><span data-stu-id="1b201-154">Configuration settings</span></span>
* <span data-ttu-id="1b201-155">Variabili di ambiente</span><span class="sxs-lookup"><span data-stu-id="1b201-155">Environment variables</span></span>

<span data-ttu-id="1b201-156">Inserire il codice [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) seguente all'inizio della pipeline di `Startup.Configure` elaborazione delle richieste del metodo.</span><span class="sxs-lookup"><span data-stu-id="1b201-156">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="1b201-157">L'ambiente viene verificato prima dell'esecuzione del middleware per garantire che il codice venga eseguito solo nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="1b201-157">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="1b201-158">Per ottenere l'ambiente, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="1b201-158">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="1b201-159">Inserire l'oggetto `IHostingEnvironment` nel `Startup.Configure` metodo e controllare l'ambiente con la variabile locale.</span><span class="sxs-lookup"><span data-stu-id="1b201-159">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="1b201-160">Il codice di esempio seguente illustra questo approccio.</span><span class="sxs-lookup"><span data-stu-id="1b201-160">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="1b201-161">Assegnare l'ambiente a una proprietà nella `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="1b201-161">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="1b201-162">Controllare l'ambiente utilizzando la proprietà (ad esempio, `if (Environment.IsDevelopment())` ).</span><span class="sxs-lookup"><span data-stu-id="1b201-162">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="1b201-163">Eseguire il debug di app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b201-163">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="1b201-164">I collegamenti seguenti forniscono informazioni sul debug di ASP.NET Core app.</span><span class="sxs-lookup"><span data-stu-id="1b201-164">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="1b201-165">Debug di ASP core in Linux</span><span class="sxs-lookup"><span data-stu-id="1b201-165">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="1b201-166">Debug di .NET Core su UNIX tramite SSH</span><span class="sxs-lookup"><span data-stu-id="1b201-166">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="1b201-167">Guida introduttiva: eseguire il debug di ASP.NET con il debugger di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b201-167">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="1b201-168">Per ulteriori informazioni sul debug, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) .</span><span class="sxs-lookup"><span data-stu-id="1b201-168">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
