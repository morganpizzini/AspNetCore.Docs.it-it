---
<span data-ttu-id="5970c-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5970c-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5970c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5970c-102">'Blazor'</span></span>
- <span data-ttu-id="5970c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5970c-103">'Identity'</span></span>
- <span data-ttu-id="5970c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5970c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5970c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5970c-105">'Razor'</span></span>
- <span data-ttu-id="5970c-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="5970c-106">'SignalR' uid:</span></span> 

---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="5970c-107">Rilevare le modifiche apportate con i token di modifica in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5970c-107">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5970c-108">Un *token di modifica* è un blocco predefinito di uso generico e di basso livello che viene usato per il rilevamento delle modifiche di stato.</span><span class="sxs-lookup"><span data-stu-id="5970c-108">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="5970c-109">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5970c-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="5970c-110">Interfaccia IChangeToken</span><span class="sxs-lookup"><span data-stu-id="5970c-110">IChangeToken interface</span></span>

<span data-ttu-id="5970c-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propaga le notifiche relative a una modifica apportata.</span><span class="sxs-lookup"><span data-stu-id="5970c-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="5970c-112">`IChangeToken` risiede nello spazio dei nomi <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="5970c-112">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="5970c-113">Il pacchetto NuGet [Microsoft. Extensions. Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) viene fornito in modo implicito alle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5970c-113">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="5970c-114">`IChangeToken` ha due proprietà:</span><span class="sxs-lookup"><span data-stu-id="5970c-114">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="5970c-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica se il token genera callback in modo proattivo.</span><span class="sxs-lookup"><span data-stu-id="5970c-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="5970c-116">Se `ActiveChangedCallbacks` è impostato su `false`, non viene mai chiamato alcun callback e l'app deve eseguire il polling di `HasChanged` per ottenere le modifiche.</span><span class="sxs-lookup"><span data-stu-id="5970c-116">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="5970c-117">È anche possibile che un token non venga mai annullato se non vengono apportate modifiche o se il listener di modifica sottostante viene eliminato o disabilitato.</span><span class="sxs-lookup"><span data-stu-id="5970c-117">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="5970c-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> riceve un valore che indica se è stata apportata una modifica.</span><span class="sxs-lookup"><span data-stu-id="5970c-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="5970c-119">L' `IChangeToken` interfaccia include il metodo [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , che registra un callback che viene richiamato quando il token viene modificato.</span><span class="sxs-lookup"><span data-stu-id="5970c-119">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="5970c-120">Prima che il callback venga richiamato è necessario impostare `HasChanged`.</span><span class="sxs-lookup"><span data-stu-id="5970c-120">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="5970c-121">Classe ChangeToken</span><span class="sxs-lookup"><span data-stu-id="5970c-121">ChangeToken class</span></span>

<span data-ttu-id="5970c-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> è una classe statica usata per propagare le notifiche relative a una modifica che è stata apportata.</span><span class="sxs-lookup"><span data-stu-id="5970c-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="5970c-123">`ChangeToken` risiede nello spazio dei nomi <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="5970c-123">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="5970c-124">Il pacchetto NuGet [Microsoft. Extensions. Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) viene fornito in modo implicito alle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5970c-124">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="5970c-125">Il metodo [token. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra un oggetto `Action` da chiamare ogni volta che il token cambia:</span><span class="sxs-lookup"><span data-stu-id="5970c-125">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="5970c-126">`Func<IChangeToken>` produce il token.</span><span class="sxs-lookup"><span data-stu-id="5970c-126">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="5970c-127">`Action` viene chiamato alla modifica del token.</span><span class="sxs-lookup"><span data-stu-id="5970c-127">`Action` is called when the token changes.</span></span>

<span data-ttu-id="5970c-128">L'overload di [token. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , tstate)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) accetta un `TState` parametro aggiuntivo passato al consumer del token `Action` .</span><span class="sxs-lookup"><span data-stu-id="5970c-128">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="5970c-129">`OnChange` restituisce un oggetto <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="5970c-129">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="5970c-130">La chiamata a <xref:System.IDisposable.Dispose*> interrompe l'ascolto di altre modifiche da parte del token e rilascia le risorse del token.</span><span class="sxs-lookup"><span data-stu-id="5970c-130">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="5970c-131">Esempi di uso di token di modifica in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5970c-131">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="5970c-132">I token di modifica vengono usati nelle aree principali di ASP.NET Core per monitorare le modifiche apportate agli oggetti:</span><span class="sxs-lookup"><span data-stu-id="5970c-132">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="5970c-133">Per monitorare le modifiche ai file, il metodo <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> di <xref:Microsoft.Extensions.FileProviders.IFileProvider> crea un elemento `IChangeToken` per le cartelle o i file specificati da controllare.</span><span class="sxs-lookup"><span data-stu-id="5970c-133">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="5970c-134">È possibile aggiungere token `IChangeToken` alle voci della cache per attivare le eliminazioni dalla cache alla modifica.</span><span class="sxs-lookup"><span data-stu-id="5970c-134">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="5970c-135">Per le modifiche di `TOptions`, l'implementazione <xref:Microsoft.Extensions.Options.OptionsMonitor`1> predefinita di <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> ha un overload che accetta una o più istanze di <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="5970c-135">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="5970c-136">Ogni istanza restituisce un elemento `IChangeToken` per registrare un callback di notifica delle modifiche per il rilevamento delle modifiche apportate alle opzioni.</span><span class="sxs-lookup"><span data-stu-id="5970c-136">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="5970c-137">Monitorare le modifiche di configurazione</span><span class="sxs-lookup"><span data-stu-id="5970c-137">Monitor for configuration changes</span></span>

<span data-ttu-id="5970c-138">Per impostazione predefinita, i modelli ASP.NET Core usano [file di configurazione JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* e *appsettings.Production.json*) per caricare le impostazioni di configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="5970c-138">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="5970c-139">Questi file vengono configurati usando il metodo di estensione [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) per <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> che accetta un parametro `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="5970c-139">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="5970c-140">`reloadOnChange` indica se la configurazione deve essere ricaricata alla modifica del file.</span><span class="sxs-lookup"><span data-stu-id="5970c-140">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="5970c-141">Questa impostazione viene visualizzata nel metodo pratico <xref:Microsoft.Extensions.Hosting.Host><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="5970c-141">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="5970c-142">La configurazione basata su file è rappresentata da <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="5970c-142">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="5970c-143">`FileConfigurationSource` usa <xref:Microsoft.Extensions.FileProviders.IFileProvider> per monitorare i file.</span><span class="sxs-lookup"><span data-stu-id="5970c-143">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="5970c-144">Per impostazione predefinita, `IFileMonitor` viene offerto da una classe <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> che usa <xref:System.IO.FileSystemWatcher> per monitorare le modifiche apportate al file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="5970c-144">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="5970c-145">L'app di esempio illustra due implementazioni per il monitoraggio delle modifiche alla configurazione.</span><span class="sxs-lookup"><span data-stu-id="5970c-145">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="5970c-146">In caso di modifica di uno qualsiasi dei file \*\* appsettings&mdash;, entrambe le implementazioni di monitoraggio dei file eseguono codice personalizzato e l'app di esempio scrive un messaggio nella console.</span><span class="sxs-lookup"><span data-stu-id="5970c-146">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="5970c-147">L'elemento `FileSystemWatcher` di un file di configurazione può attivare più callback del token per una singola modifica del file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="5970c-147">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="5970c-148">Per assicurarsi che il codice personalizzato venga eseguito solo una volta quando vengono attivati più callback del token, l'implementazione dell'esempio controlla gli hash dei file.</span><span class="sxs-lookup"><span data-stu-id="5970c-148">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="5970c-149">L'esempio usa l'hash di file SHA1.</span><span class="sxs-lookup"><span data-stu-id="5970c-149">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="5970c-150">Viene implementato un nuovo tentativo con un'interruzione temporanea esponenziale.</span><span class="sxs-lookup"><span data-stu-id="5970c-150">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="5970c-151">Il nuovo tentativo è presente perché potrebbe verificarsi un blocco di file che impedisce temporaneamente l'elaborazione di un nuovo hash in un file.</span><span class="sxs-lookup"><span data-stu-id="5970c-151">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="5970c-152">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="5970c-152">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="5970c-153">Semplice token di modifica dell'avvio</span><span class="sxs-lookup"><span data-stu-id="5970c-153">Simple startup change token</span></span>

<span data-ttu-id="5970c-154">Registrare un callback dell'elemento `Action` consumer del token per le notifiche di modifica al token di ricaricamento della configurazione.</span><span class="sxs-lookup"><span data-stu-id="5970c-154">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="5970c-155">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5970c-155">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="5970c-156">`config.GetReloadToken()` fornisce il token.</span><span class="sxs-lookup"><span data-stu-id="5970c-156">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="5970c-157">Il callback è il metodo `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="5970c-157">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="5970c-158">Il valore `state` del callback viene usato per passare `IWebHostEnvironment`, che è utile per specificare il file di configurazione *appsettings* corretto da monitorare (ad esempio, *appsettings.Development.json* nell'ambiente di sviluppo).</span><span class="sxs-lookup"><span data-stu-id="5970c-158">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="5970c-159">Gli hash dei file vengono usati per impedire che l'istruzione `WriteConsole` venga eseguita più volte a causa di più callback del token quando il file di configurazione è stato modificato una sola volta.</span><span class="sxs-lookup"><span data-stu-id="5970c-159">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="5970c-160">Questo sistema rimane in esecuzione finché l'app è in esecuzione e non può essere disabilitato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="5970c-160">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="5970c-161">Monitorare le modifiche di configurazione come servizio</span><span class="sxs-lookup"><span data-stu-id="5970c-161">Monitor configuration changes as a service</span></span>

<span data-ttu-id="5970c-162">L'esempio implementa:</span><span class="sxs-lookup"><span data-stu-id="5970c-162">The sample implements:</span></span>

* <span data-ttu-id="5970c-163">Monitoraggio di base del token di avvio.</span><span class="sxs-lookup"><span data-stu-id="5970c-163">Basic startup token monitoring.</span></span>
* <span data-ttu-id="5970c-164">Monitoraggio come servizio.</span><span class="sxs-lookup"><span data-stu-id="5970c-164">Monitoring as a service.</span></span>
* <span data-ttu-id="5970c-165">Un meccanismo per abilitare e disabilitare il monitoraggio.</span><span class="sxs-lookup"><span data-stu-id="5970c-165">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="5970c-166">L'esempio stabilisce un'interfaccia `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="5970c-166">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="5970c-167">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="5970c-167">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="5970c-168">Il costruttore della classe implementata, `ConfigurationMonitor`, registra un callback per le notifiche di modifica:</span><span class="sxs-lookup"><span data-stu-id="5970c-168">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="5970c-169">`config.GetReloadToken()` fornisce il token.</span><span class="sxs-lookup"><span data-stu-id="5970c-169">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="5970c-170">`InvokeChanged` è il metodo di callback.</span><span class="sxs-lookup"><span data-stu-id="5970c-170">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="5970c-171">Il valore `state` in questa istanza è un riferimento all'istanza `IConfigurationMonitor` usata per accedere allo stato di monitoraggio.</span><span class="sxs-lookup"><span data-stu-id="5970c-171">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="5970c-172">Vengono usate due proprietà:</span><span class="sxs-lookup"><span data-stu-id="5970c-172">Two properties are used:</span></span>

* <span data-ttu-id="5970c-173">`MonitoringEnabled`: Indica se il callback deve eseguire il codice personalizzato.</span><span class="sxs-lookup"><span data-stu-id="5970c-173">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="5970c-174">`CurrentState`: Descrive lo stato di monitoraggio corrente per l'uso nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="5970c-174">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="5970c-175">Il metodo `InvokeChanged` è simile all'approccio precedente, ad eccezione del fatto che:</span><span class="sxs-lookup"><span data-stu-id="5970c-175">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="5970c-176">Non esegue il proprio codice a meno che `MonitoringEnabled` non sia impostato su `true`.</span><span class="sxs-lookup"><span data-stu-id="5970c-176">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="5970c-177">Restituisce l'elemento `state` corrente nell'output `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="5970c-177">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="5970c-178">Un'istanza di `ConfigurationMonitor` viene registrata come servizio in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5970c-178">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="5970c-179">La pagina di indice offre all'utente il controllo sul monitoraggio della configurazione.</span><span class="sxs-lookup"><span data-stu-id="5970c-179">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="5970c-180">L'istanza di `IConfigurationMonitor` viene inserita in `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="5970c-180">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="5970c-181">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5970c-181">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="5970c-182">Il monitoraggio di configurazione (`_monitor`) viene usato per abilitare o disabilitare il monitoraggio e impostare lo stato corrente per commenti e suggerimenti dell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="5970c-182">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="5970c-183">Quando viene attivato `OnPostStartMonitoring`, il monitoraggio è abilitato e lo stato corrente viene cancellato.</span><span class="sxs-lookup"><span data-stu-id="5970c-183">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="5970c-184">Quando viene attivato `OnPostStopMonitoring`, il monitoraggio è disabilitato e lo stato viene impostato in modo da indicare che il monitoraggio non viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="5970c-184">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="5970c-185">I pulsanti nell'interfaccia utente abilitano e disabilitano il monitoraggio.</span><span class="sxs-lookup"><span data-stu-id="5970c-185">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="5970c-186">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5970c-186">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="5970c-187">Monitorare le modifiche al file nella cache</span><span class="sxs-lookup"><span data-stu-id="5970c-187">Monitor cached file changes</span></span>

<span data-ttu-id="5970c-188">È possibile memorizzare il contenuto del file nella cache in memoria usando <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="5970c-188">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="5970c-189">La memorizzazione nella cache in memoria è descritta nell'argomento [Cache in memoria](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="5970c-189">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="5970c-190">Senza eseguire passaggi aggiuntivi, ad esempio l'implementazione descritta di seguito, la cache restituirà dati *non aggiornati* (obsoleti) se i dati di origine vengono modificati.</span><span class="sxs-lookup"><span data-stu-id="5970c-190">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="5970c-191">Ad esempio, se durante il rinnovo di un periodo di [scadenza variabile](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) non si tiene conto dello stato di un file di origine memorizzato nella cache, i dati relativi ai file nella cache risulteranno non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="5970c-191">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="5970c-192">Ogni richiesta di dati rinnoverà il periodo di scadenza variabile, ma il file non verrà mai ricaricato nella cache.</span><span class="sxs-lookup"><span data-stu-id="5970c-192">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="5970c-193">Le funzionalità dell'app che usano il contenuto del file memorizzato nella cache sono soggette alla possibile ricezione di contenuto non aggiornato.</span><span class="sxs-lookup"><span data-stu-id="5970c-193">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="5970c-194">L'uso dei token di modifica in uno scenario di memorizzazione di file nella cache consente di evitare la presenza di contenuto dei file non aggiornato nella cache.</span><span class="sxs-lookup"><span data-stu-id="5970c-194">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="5970c-195">L'app di esempio illustra un'implementazione dell'approccio.</span><span class="sxs-lookup"><span data-stu-id="5970c-195">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="5970c-196">Nell'esempio viene usato `GetFileContent` per:</span><span class="sxs-lookup"><span data-stu-id="5970c-196">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="5970c-197">Restituire il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="5970c-197">Return file content.</span></span>
* <span data-ttu-id="5970c-198">Implementare un algoritmo di nuovi tentativi con interruzione temporanea esponenziale per i casi in cui un blocco di file impedisca temporaneamente la lettura di un file.</span><span class="sxs-lookup"><span data-stu-id="5970c-198">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="5970c-199">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="5970c-199">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="5970c-200">Viene creato un elemento `FileService` per gestire le ricerche nel file memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="5970c-200">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="5970c-201">La chiamata del servizio al metodo `GetFileContent` prova a ottenere il contenuto del file dalla cache in memoria e a restituirlo al chiamante (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="5970c-201">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="5970c-202">Se non è possibile reperire il contenuto memorizzato nella cache usando la chiave della cache, verranno intraprese le azioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="5970c-202">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="5970c-203">Il contenuto del file viene ottenuto usando `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="5970c-203">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="5970c-204">Un token di modifica viene ottenuto dal provider di file con [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="5970c-204">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="5970c-205">Il callback del token viene attivato alla modifica del file.</span><span class="sxs-lookup"><span data-stu-id="5970c-205">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="5970c-206">Il contenuto del file viene memorizzato nella cache con un periodo di [scadenza variabile](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="5970c-206">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="5970c-207">Al token di modifica viene associato [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) per eliminare la voce della cache se il file viene modificato mentre è memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="5970c-207">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="5970c-208">Nell'esempio seguente i file vengono archiviati nella [radice del contenuto](xref:fundamentals/index#content-root)dell'app.</span><span class="sxs-lookup"><span data-stu-id="5970c-208">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="5970c-209">`IWebHostEnvironment.ContentRootFileProvider`viene usato per ottenere un oggetto che <xref:Microsoft.Extensions.FileProviders.IFileProvider> punta all'oggetto dell'app `IWebHostEnvironment.ContentRootPath` .</span><span class="sxs-lookup"><span data-stu-id="5970c-209">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="5970c-210">`filePath` viene ottenuto con [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="5970c-210">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="5970c-211">`FileService` è registrato nel contenitore di servizi insieme al servizio di memorizzazione nella cache.</span><span class="sxs-lookup"><span data-stu-id="5970c-211">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="5970c-212">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5970c-212">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="5970c-213">Il modello di pagina carica il contenuto del file usando il servizio.</span><span class="sxs-lookup"><span data-stu-id="5970c-213">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="5970c-214">Nel metodo `OnGet` della pagina Index (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="5970c-214">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="5970c-215">Classe CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="5970c-215">CompositeChangeToken class</span></span>

<span data-ttu-id="5970c-216">Per rappresentare una o più istanze di `IChangeToken` in un singolo oggetto, usare la classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="5970c-216">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="5970c-217">`HasChanged` nel token composito indica `true` se l'elemento `HasChanged` di qualsiasi token rappresentato è `true`.</span><span class="sxs-lookup"><span data-stu-id="5970c-217">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="5970c-218">`ActiveChangeCallbacks` nel token composito indica `true` se l'elemento `ActiveChangeCallbacks` di qualsiasi token rappresentato è `true`.</span><span class="sxs-lookup"><span data-stu-id="5970c-218">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="5970c-219">Se si verificano più eventi di modifica simultanei, il callback della modifica composita viene richiamato una volta.</span><span class="sxs-lookup"><span data-stu-id="5970c-219">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5970c-220">Un *token di modifica* è un blocco predefinito di uso generico e di basso livello che viene usato per il rilevamento delle modifiche di stato.</span><span class="sxs-lookup"><span data-stu-id="5970c-220">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="5970c-221">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5970c-221">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="5970c-222">Interfaccia IChangeToken</span><span class="sxs-lookup"><span data-stu-id="5970c-222">IChangeToken interface</span></span>

<span data-ttu-id="5970c-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propaga le notifiche relative a una modifica apportata.</span><span class="sxs-lookup"><span data-stu-id="5970c-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="5970c-224">`IChangeToken` risiede nello spazio dei nomi <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="5970c-224">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="5970c-225">Per le app che non usano il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), creare un riferimento al pacchetto per il pacchetto NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="5970c-225">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="5970c-226">`IChangeToken` ha due proprietà:</span><span class="sxs-lookup"><span data-stu-id="5970c-226">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="5970c-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica se il token genera callback in modo proattivo.</span><span class="sxs-lookup"><span data-stu-id="5970c-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="5970c-228">Se `ActiveChangedCallbacks` è impostato su `false`, non viene mai chiamato alcun callback e l'app deve eseguire il polling di `HasChanged` per ottenere le modifiche.</span><span class="sxs-lookup"><span data-stu-id="5970c-228">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="5970c-229">È anche possibile che un token non venga mai annullato se non vengono apportate modifiche o se il listener di modifica sottostante viene eliminato o disabilitato.</span><span class="sxs-lookup"><span data-stu-id="5970c-229">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="5970c-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> riceve un valore che indica se è stata apportata una modifica.</span><span class="sxs-lookup"><span data-stu-id="5970c-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="5970c-231">L' `IChangeToken` interfaccia include il metodo [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , che registra un callback che viene richiamato quando il token viene modificato.</span><span class="sxs-lookup"><span data-stu-id="5970c-231">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="5970c-232">Prima che il callback venga richiamato è necessario impostare `HasChanged`.</span><span class="sxs-lookup"><span data-stu-id="5970c-232">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="5970c-233">Classe ChangeToken</span><span class="sxs-lookup"><span data-stu-id="5970c-233">ChangeToken class</span></span>

<span data-ttu-id="5970c-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> è una classe statica usata per propagare le notifiche relative a una modifica che è stata apportata.</span><span class="sxs-lookup"><span data-stu-id="5970c-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="5970c-235">`ChangeToken` risiede nello spazio dei nomi <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="5970c-235">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="5970c-236">Per le app che non usano il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), creare un riferimento al pacchetto per il pacchetto NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="5970c-236">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="5970c-237">Il metodo [token. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra un oggetto `Action` da chiamare ogni volta che il token cambia:</span><span class="sxs-lookup"><span data-stu-id="5970c-237">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="5970c-238">`Func<IChangeToken>` produce il token.</span><span class="sxs-lookup"><span data-stu-id="5970c-238">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="5970c-239">`Action` viene chiamato alla modifica del token.</span><span class="sxs-lookup"><span data-stu-id="5970c-239">`Action` is called when the token changes.</span></span>

<span data-ttu-id="5970c-240">L'overload di [token. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , tstate)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) accetta un `TState` parametro aggiuntivo passato al consumer del token `Action` .</span><span class="sxs-lookup"><span data-stu-id="5970c-240">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="5970c-241">`OnChange` restituisce un oggetto <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="5970c-241">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="5970c-242">La chiamata a <xref:System.IDisposable.Dispose*> interrompe l'ascolto di altre modifiche da parte del token e rilascia le risorse del token.</span><span class="sxs-lookup"><span data-stu-id="5970c-242">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="5970c-243">Esempi di uso di token di modifica in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5970c-243">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="5970c-244">I token di modifica vengono usati nelle aree principali di ASP.NET Core per monitorare le modifiche apportate agli oggetti:</span><span class="sxs-lookup"><span data-stu-id="5970c-244">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="5970c-245">Per monitorare le modifiche ai file, il metodo <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> di <xref:Microsoft.Extensions.FileProviders.IFileProvider> crea un elemento `IChangeToken` per le cartelle o i file specificati da controllare.</span><span class="sxs-lookup"><span data-stu-id="5970c-245">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="5970c-246">È possibile aggiungere token `IChangeToken` alle voci della cache per attivare le eliminazioni dalla cache alla modifica.</span><span class="sxs-lookup"><span data-stu-id="5970c-246">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="5970c-247">Per le modifiche di `TOptions`, l'implementazione <xref:Microsoft.Extensions.Options.OptionsMonitor`1> predefinita di <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> ha un overload che accetta una o più istanze di <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="5970c-247">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="5970c-248">Ogni istanza restituisce un elemento `IChangeToken` per registrare un callback di notifica delle modifiche per il rilevamento delle modifiche apportate alle opzioni.</span><span class="sxs-lookup"><span data-stu-id="5970c-248">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="5970c-249">Monitorare le modifiche di configurazione</span><span class="sxs-lookup"><span data-stu-id="5970c-249">Monitor for configuration changes</span></span>

<span data-ttu-id="5970c-250">Per impostazione predefinita, i modelli ASP.NET Core usano [file di configurazione JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* e *appsettings.Production.json*) per caricare le impostazioni di configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="5970c-250">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="5970c-251">Questi file vengono configurati usando il metodo di estensione [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) per <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> che accetta un parametro `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="5970c-251">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="5970c-252">`reloadOnChange` indica se la configurazione deve essere ricaricata alla modifica del file.</span><span class="sxs-lookup"><span data-stu-id="5970c-252">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="5970c-253">Questa impostazione viene visualizzata nel metodo pratico <xref:Microsoft.AspNetCore.WebHost><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="5970c-253">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="5970c-254">La configurazione basata su file è rappresentata da <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="5970c-254">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="5970c-255">`FileConfigurationSource` usa <xref:Microsoft.Extensions.FileProviders.IFileProvider> per monitorare i file.</span><span class="sxs-lookup"><span data-stu-id="5970c-255">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="5970c-256">Per impostazione predefinita, `IFileMonitor` viene offerto da una classe <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> che usa <xref:System.IO.FileSystemWatcher> per monitorare le modifiche apportate al file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="5970c-256">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="5970c-257">L'app di esempio illustra due implementazioni per il monitoraggio delle modifiche alla configurazione.</span><span class="sxs-lookup"><span data-stu-id="5970c-257">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="5970c-258">In caso di modifica di uno qualsiasi dei file \*\* appsettings&mdash;, entrambe le implementazioni di monitoraggio dei file eseguono codice personalizzato e l'app di esempio scrive un messaggio nella console.</span><span class="sxs-lookup"><span data-stu-id="5970c-258">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="5970c-259">L'elemento `FileSystemWatcher` di un file di configurazione può attivare più callback del token per una singola modifica del file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="5970c-259">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="5970c-260">Per assicurarsi che il codice personalizzato venga eseguito solo una volta quando vengono attivati più callback del token, l'implementazione dell'esempio controlla gli hash dei file.</span><span class="sxs-lookup"><span data-stu-id="5970c-260">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="5970c-261">L'esempio usa l'hash di file SHA1.</span><span class="sxs-lookup"><span data-stu-id="5970c-261">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="5970c-262">Viene implementato un nuovo tentativo con un'interruzione temporanea esponenziale.</span><span class="sxs-lookup"><span data-stu-id="5970c-262">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="5970c-263">Il nuovo tentativo è presente perché potrebbe verificarsi un blocco di file che impedisce temporaneamente l'elaborazione di un nuovo hash in un file.</span><span class="sxs-lookup"><span data-stu-id="5970c-263">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="5970c-264">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="5970c-264">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="5970c-265">Semplice token di modifica dell'avvio</span><span class="sxs-lookup"><span data-stu-id="5970c-265">Simple startup change token</span></span>

<span data-ttu-id="5970c-266">Registrare un callback dell'elemento `Action` consumer del token per le notifiche di modifica al token di ricaricamento della configurazione.</span><span class="sxs-lookup"><span data-stu-id="5970c-266">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="5970c-267">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5970c-267">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="5970c-268">`config.GetReloadToken()` fornisce il token.</span><span class="sxs-lookup"><span data-stu-id="5970c-268">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="5970c-269">Il callback è il metodo `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="5970c-269">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="5970c-270">Il valore `state` del callback viene usato per passare `IHostingEnvironment`, che è utile per specificare il file di configurazione *appsettings* corretto da monitorare (ad esempio, *appsettings.Development.json* nell'ambiente di sviluppo).</span><span class="sxs-lookup"><span data-stu-id="5970c-270">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="5970c-271">Gli hash dei file vengono usati per impedire che l'istruzione `WriteConsole` venga eseguita più volte a causa di più callback del token quando il file di configurazione è stato modificato una sola volta.</span><span class="sxs-lookup"><span data-stu-id="5970c-271">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="5970c-272">Questo sistema rimane in esecuzione finché l'app è in esecuzione e non può essere disabilitato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="5970c-272">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="5970c-273">Monitorare le modifiche di configurazione come servizio</span><span class="sxs-lookup"><span data-stu-id="5970c-273">Monitor configuration changes as a service</span></span>

<span data-ttu-id="5970c-274">L'esempio implementa:</span><span class="sxs-lookup"><span data-stu-id="5970c-274">The sample implements:</span></span>

* <span data-ttu-id="5970c-275">Monitoraggio di base del token di avvio.</span><span class="sxs-lookup"><span data-stu-id="5970c-275">Basic startup token monitoring.</span></span>
* <span data-ttu-id="5970c-276">Monitoraggio come servizio.</span><span class="sxs-lookup"><span data-stu-id="5970c-276">Monitoring as a service.</span></span>
* <span data-ttu-id="5970c-277">Un meccanismo per abilitare e disabilitare il monitoraggio.</span><span class="sxs-lookup"><span data-stu-id="5970c-277">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="5970c-278">L'esempio stabilisce un'interfaccia `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="5970c-278">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="5970c-279">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="5970c-279">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="5970c-280">Il costruttore della classe implementata, `ConfigurationMonitor`, registra un callback per le notifiche di modifica:</span><span class="sxs-lookup"><span data-stu-id="5970c-280">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="5970c-281">`config.GetReloadToken()` fornisce il token.</span><span class="sxs-lookup"><span data-stu-id="5970c-281">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="5970c-282">`InvokeChanged` è il metodo di callback.</span><span class="sxs-lookup"><span data-stu-id="5970c-282">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="5970c-283">Il valore `state` in questa istanza è un riferimento all'istanza `IConfigurationMonitor` usata per accedere allo stato di monitoraggio.</span><span class="sxs-lookup"><span data-stu-id="5970c-283">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="5970c-284">Vengono usate due proprietà:</span><span class="sxs-lookup"><span data-stu-id="5970c-284">Two properties are used:</span></span>

* <span data-ttu-id="5970c-285">`MonitoringEnabled`: Indica se il callback deve eseguire il codice personalizzato.</span><span class="sxs-lookup"><span data-stu-id="5970c-285">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="5970c-286">`CurrentState`: Descrive lo stato di monitoraggio corrente per l'uso nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="5970c-286">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="5970c-287">Il metodo `InvokeChanged` è simile all'approccio precedente, ad eccezione del fatto che:</span><span class="sxs-lookup"><span data-stu-id="5970c-287">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="5970c-288">Non esegue il proprio codice a meno che `MonitoringEnabled` non sia impostato su `true`.</span><span class="sxs-lookup"><span data-stu-id="5970c-288">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="5970c-289">Restituisce l'elemento `state` corrente nell'output `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="5970c-289">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="5970c-290">Un'istanza di `ConfigurationMonitor` viene registrata come servizio in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5970c-290">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="5970c-291">La pagina di indice offre all'utente il controllo sul monitoraggio della configurazione.</span><span class="sxs-lookup"><span data-stu-id="5970c-291">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="5970c-292">L'istanza di `IConfigurationMonitor` viene inserita in `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="5970c-292">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="5970c-293">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5970c-293">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="5970c-294">Il monitoraggio di configurazione (`_monitor`) viene usato per abilitare o disabilitare il monitoraggio e impostare lo stato corrente per commenti e suggerimenti dell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="5970c-294">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="5970c-295">Quando viene attivato `OnPostStartMonitoring`, il monitoraggio è abilitato e lo stato corrente viene cancellato.</span><span class="sxs-lookup"><span data-stu-id="5970c-295">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="5970c-296">Quando viene attivato `OnPostStopMonitoring`, il monitoraggio è disabilitato e lo stato viene impostato in modo da indicare che il monitoraggio non viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="5970c-296">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="5970c-297">I pulsanti nell'interfaccia utente abilitano e disabilitano il monitoraggio.</span><span class="sxs-lookup"><span data-stu-id="5970c-297">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="5970c-298">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5970c-298">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="5970c-299">Monitorare le modifiche al file nella cache</span><span class="sxs-lookup"><span data-stu-id="5970c-299">Monitor cached file changes</span></span>

<span data-ttu-id="5970c-300">È possibile memorizzare il contenuto del file nella cache in memoria usando <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="5970c-300">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="5970c-301">La memorizzazione nella cache in memoria è descritta nell'argomento [Cache in memoria](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="5970c-301">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="5970c-302">Senza eseguire passaggi aggiuntivi, ad esempio l'implementazione descritta di seguito, la cache restituirà dati *non aggiornati* (obsoleti) se i dati di origine vengono modificati.</span><span class="sxs-lookup"><span data-stu-id="5970c-302">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="5970c-303">Ad esempio, se durante il rinnovo di un periodo di [scadenza variabile](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) non si tiene conto dello stato di un file di origine memorizzato nella cache, i dati relativi ai file nella cache risulteranno non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="5970c-303">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="5970c-304">Ogni richiesta di dati rinnoverà il periodo di scadenza variabile, ma il file non verrà mai ricaricato nella cache.</span><span class="sxs-lookup"><span data-stu-id="5970c-304">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="5970c-305">Le funzionalità dell'app che usano il contenuto del file memorizzato nella cache sono soggette alla possibile ricezione di contenuto non aggiornato.</span><span class="sxs-lookup"><span data-stu-id="5970c-305">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="5970c-306">L'uso dei token di modifica in uno scenario di memorizzazione di file nella cache consente di evitare la presenza di contenuto dei file non aggiornato nella cache.</span><span class="sxs-lookup"><span data-stu-id="5970c-306">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="5970c-307">L'app di esempio illustra un'implementazione dell'approccio.</span><span class="sxs-lookup"><span data-stu-id="5970c-307">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="5970c-308">Nell'esempio viene usato `GetFileContent` per:</span><span class="sxs-lookup"><span data-stu-id="5970c-308">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="5970c-309">Restituire il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="5970c-309">Return file content.</span></span>
* <span data-ttu-id="5970c-310">Implementare un algoritmo di nuovi tentativi con interruzione temporanea esponenziale per i casi in cui un blocco di file impedisca temporaneamente la lettura di un file.</span><span class="sxs-lookup"><span data-stu-id="5970c-310">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="5970c-311">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="5970c-311">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="5970c-312">Viene creato un elemento `FileService` per gestire le ricerche nel file memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="5970c-312">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="5970c-313">La chiamata del servizio al metodo `GetFileContent` prova a ottenere il contenuto del file dalla cache in memoria e a restituirlo al chiamante (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="5970c-313">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="5970c-314">Se non è possibile reperire il contenuto memorizzato nella cache usando la chiave della cache, verranno intraprese le azioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="5970c-314">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="5970c-315">Il contenuto del file viene ottenuto usando `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="5970c-315">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="5970c-316">Un token di modifica viene ottenuto dal provider di file con [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="5970c-316">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="5970c-317">Il callback del token viene attivato alla modifica del file.</span><span class="sxs-lookup"><span data-stu-id="5970c-317">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="5970c-318">Il contenuto del file viene memorizzato nella cache con un periodo di [scadenza variabile](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="5970c-318">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="5970c-319">Al token di modifica viene associato [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) per eliminare la voce della cache se il file viene modificato mentre è memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="5970c-319">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="5970c-320">Nell'esempio seguente i file vengono archiviati nella [radice del contenuto](xref:fundamentals/index#content-root)dell'app.</span><span class="sxs-lookup"><span data-stu-id="5970c-320">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="5970c-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) viene usato per ottenere un <xref:Microsoft.Extensions.FileProviders.IFileProvider> che punta al <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> dell'app.</span><span class="sxs-lookup"><span data-stu-id="5970c-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="5970c-322">`filePath` viene ottenuto con [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="5970c-322">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="5970c-323">`FileService` è registrato nel contenitore di servizi insieme al servizio di memorizzazione nella cache.</span><span class="sxs-lookup"><span data-stu-id="5970c-323">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="5970c-324">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5970c-324">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="5970c-325">Il modello di pagina carica il contenuto del file usando il servizio.</span><span class="sxs-lookup"><span data-stu-id="5970c-325">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="5970c-326">Nel metodo `OnGet` della pagina Index (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="5970c-326">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="5970c-327">Classe CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="5970c-327">CompositeChangeToken class</span></span>

<span data-ttu-id="5970c-328">Per rappresentare una o più istanze di `IChangeToken` in un singolo oggetto, usare la classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="5970c-328">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="5970c-329">`HasChanged` nel token composito indica `true` se l'elemento `HasChanged` di qualsiasi token rappresentato è `true`.</span><span class="sxs-lookup"><span data-stu-id="5970c-329">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="5970c-330">`ActiveChangeCallbacks` nel token composito indica `true` se l'elemento `ActiveChangeCallbacks` di qualsiasi token rappresentato è `true`.</span><span class="sxs-lookup"><span data-stu-id="5970c-330">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="5970c-331">Se si verificano più eventi di modifica simultanei, il callback della modifica composita viene richiamato una volta.</span><span class="sxs-lookup"><span data-stu-id="5970c-331">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5970c-332">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5970c-332">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
