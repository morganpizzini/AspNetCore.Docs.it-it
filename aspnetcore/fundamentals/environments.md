---
title: Usare più ambienti in ASP.NET Core
author: rick-anderson
description: Informazioni su come controllare il comportamento di app ASP.NET Core in più ambienti.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330634"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="13d19-103">Usare più ambienti in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="13d19-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="13d19-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="13d19-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="13d19-105">ASP.NET Core configura il comportamento dell'app in base all'ambiente di runtime e tramite una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="13d19-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="13d19-106">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="13d19-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="13d19-107">Ambienti</span><span class="sxs-lookup"><span data-stu-id="13d19-107">Environments</span></span>

<span data-ttu-id="13d19-108">Per determinare l'ambiente di runtime, ASP.NET Core legge le variabili di ambiente seguenti:</span><span class="sxs-lookup"><span data-stu-id="13d19-108">To determine the runtime environment, ASP.NET Core reads from the following environment variables:</span></span>

1. [<span data-ttu-id="13d19-109">DOTNET_ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="13d19-109">DOTNET_ENVIRONMENT</span></span>](xref:fundamentals/configuration/index#default-host-configuration)
1. <span data-ttu-id="13d19-110">`ASPNETCORE_ENVIRONMENT`Quando <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="13d19-110">`ASPNETCORE_ENVIRONMENT` when <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> is called.</span></span> <span data-ttu-id="13d19-111">La chiamata predefinita per i modelli di app Web ASP.NET Core `ConfigureWebHostDefaults` .</span><span class="sxs-lookup"><span data-stu-id="13d19-111">The default ASP.NET Core web app templates call `ConfigureWebHostDefaults`.</span></span> <span data-ttu-id="13d19-112">Il `ASPNETCORE_ENVIRONMENT` valore esegue l'override di `DOTNET_ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="13d19-112">The `ASPNETCORE_ENVIRONMENT` value overrides `DOTNET_ENVIRONMENT`.</span></span>

<span data-ttu-id="13d19-113">`IHostEnvironment.EnvironmentName`può essere impostato su qualsiasi valore, ma i valori seguenti vengono forniti dal Framework:</span><span class="sxs-lookup"><span data-stu-id="13d19-113">`IHostEnvironment.EnvironmentName` can be set to any value, but the following values are provided by the framework:</span></span>

* <span data-ttu-id="13d19-114"><xref:Microsoft.Extensions.Hosting.Environments.Development>: Il [launchSettings.jsnei](#lsj) set di file nel `ASPNETCORE_ENVIRONMENT` `Development` computer locale.</span><span class="sxs-lookup"><span data-stu-id="13d19-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> : The [launchSettings.json](#lsj) file sets `ASPNETCORE_ENVIRONMENT` to `Development` on the local machine.</span></span>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="13d19-115"><xref:Microsoft.Extensions.Hosting.Environments.Production>: Valore predefinito se `DOTNET_ENVIRONMENT` e `ASPNETCORE_ENVIRONMENT` non sono stati impostati.</span><span class="sxs-lookup"><span data-stu-id="13d19-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> : The default if `DOTNET_ENVIRONMENT` and `ASPNETCORE_ENVIRONMENT` have not been set.</span></span>

<span data-ttu-id="13d19-116">Il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="13d19-116">The following code:</span></span>

* <span data-ttu-id="13d19-117">Chiama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` è impostato su `Development`.</span><span class="sxs-lookup"><span data-stu-id="13d19-117">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="13d19-118">Chiama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando il valore di `ASPNETCORE_ENVIRONMENT` è impostato su `Staging` , `Production` o `Staging_2` .</span><span class="sxs-lookup"><span data-stu-id="13d19-118">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set to `Staging`, `Production`, or  `Staging_2`.</span></span>
* <span data-ttu-id="13d19-119">Inserisce <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="13d19-119">Injects <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="13d19-120">Questo approccio è utile quando l'app richiede solo la regolazione `Startup.Configure` per alcuni ambienti con differenze minime di codice per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="13d19-120">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>
* <span data-ttu-id="13d19-121">È simile al codice generato dai modelli di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="13d19-121">Is similar to the code generated by the ASP.NET Core templates.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="13d19-122">L' [Helper tag di ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa il valore di [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) per includere o escludere il markup nell'elemento:</span><span class="sxs-lookup"><span data-stu-id="13d19-122">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="13d19-123">La [pagina about](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) del [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) include il markup precedente e visualizza il valore di `IWebHostEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="13d19-123">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) includes the preceding markup and displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

<span data-ttu-id="13d19-124">In Windows e macOS, le variabili di ambiente e i valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="13d19-124">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="13d19-125">Le variabili e i valori di ambiente Linux fanno **distinzione tra maiuscole** e minuscole per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="13d19-125">Linux environment variables and values are **case-sensitive** by default.</span></span>

### <a name="create-environmentssample"></a><span data-ttu-id="13d19-126">Crea EnvironmentsSample</span><span class="sxs-lookup"><span data-stu-id="13d19-126">Create EnvironmentsSample</span></span>

<span data-ttu-id="13d19-127">Il [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) usato in questo documento si basa su un Razor progetto di pagine denominato *EnvironmentsSample*.</span><span class="sxs-lookup"><span data-stu-id="13d19-127">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) used in this document is based on a Razor Pages project named *EnvironmentsSample*.</span></span>

<span data-ttu-id="13d19-128">Il codice seguente crea ed esegue un'app Web denominata *EnvironmentsSample*:</span><span class="sxs-lookup"><span data-stu-id="13d19-128">The following code creates and runs a web app named *EnvironmentsSample*:</span></span>

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

<span data-ttu-id="13d19-129">Quando viene eseguita l'app, viene visualizzato il seguente output:</span><span class="sxs-lookup"><span data-stu-id="13d19-129">When the app runs, it displays some of the following output:</span></span>

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a><span data-ttu-id="13d19-130">Sviluppo e launchSettings.js</span><span class="sxs-lookup"><span data-stu-id="13d19-130">Development and launchSettings.json</span></span>

<span data-ttu-id="13d19-131">L'ambiente di sviluppo può abilitare funzionalità che non devono essere esposte nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="13d19-131">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="13d19-132">Ad esempio i modelli ASP.NET Core abilitano la [pagina delle eccezioni per gli sviluppatori](xref:fundamentals/error-handling#developer-exception-page) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="13d19-132">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="13d19-133">L'ambiente di sviluppo computer locale può essere impostato nel file *Properties\launchSettings.json* del progetto.</span><span class="sxs-lookup"><span data-stu-id="13d19-133">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="13d19-134">I valori di ambiente in *launchSettings.json* sostituiscono i valori impostati nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="13d19-134">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="13d19-135">Il *launchSettings.jsnel* file:</span><span class="sxs-lookup"><span data-stu-id="13d19-135">The *launchSettings.json* file:</span></span>

* <span data-ttu-id="13d19-136">Viene usato solo nel computer di sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="13d19-136">Is only used on the local development machine.</span></span>
* <span data-ttu-id="13d19-137">Non è distribuito.</span><span class="sxs-lookup"><span data-stu-id="13d19-137">Is not deployed.</span></span>
* <span data-ttu-id="13d19-138">contiene le impostazioni del profilo.</span><span class="sxs-lookup"><span data-stu-id="13d19-138">contains profile settings.</span></span>

<span data-ttu-id="13d19-139">Il codice JSON seguente mostra il *launchSettings.jssu* file per un ASP.NET Core progetto Web denominato *EnvironmentsSample* creato con Visual Studio o `dotnet new` :</span><span class="sxs-lookup"><span data-stu-id="13d19-139">The following JSON shows the *launchSettings.json* file for an ASP.NET Core web projected named *EnvironmentsSample* created with Visual Studio or `dotnet new`:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

<span data-ttu-id="13d19-140">Il markup precedente contiene due profili:</span><span class="sxs-lookup"><span data-stu-id="13d19-140">The preceding markup contains two profiles:</span></span>

* <span data-ttu-id="13d19-141">`IIS Express`: Profilo predefinito usato quando si avvia l'app da Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="13d19-141">`IIS Express`: The default profile used when launching the app from Visual Studio.</span></span> <span data-ttu-id="13d19-142">Il `"commandName"` valore della chiave è `"IISExpress"` , pertanto, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) è il server Web.</span><span class="sxs-lookup"><span data-stu-id="13d19-142">The `"commandName"` key has the value `"IISExpress"`, therefore, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) is the web server.</span></span> <span data-ttu-id="13d19-143">È possibile impostare il profilo di avvio sul progetto o su qualsiasi altro profilo incluso.</span><span class="sxs-lookup"><span data-stu-id="13d19-143">You can set the launch profile to the project or any other profile included.</span></span> <span data-ttu-id="13d19-144">Nell'immagine seguente, ad esempio, se si seleziona il nome del progetto viene avviato il [server Web gheppio](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="13d19-144">For example, in the image below, selecting the project name launches the [Kestrel web server](xref:fundamentals/servers/kestrel).</span></span>

  ![Menu di avvio IIS Express](environments/_static/iisx2.png)
* <span data-ttu-id="13d19-146">`EnvironmentsSample`: Il nome del profilo è il nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="13d19-146">`EnvironmentsSample`: The profile name is the project name.</span></span> <span data-ttu-id="13d19-147">Questo profilo viene usato per impostazione predefinita quando si avvia l'app con `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="13d19-147">This profile is used by default when launching the app with `dotnet run`.</span></span>  <span data-ttu-id="13d19-148">Il `"commandName"` valore della chiave è `"Project"` , pertanto, viene avviato il [server Web gheppio](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="13d19-148">The `"commandName"` key has the value `"Project"`, therefore, the [Kestrel web server](xref:fundamentals/servers/kestrel) is launched.</span></span>

<span data-ttu-id="13d19-149">Il valore di `commandName` può specificare il server Web da avviare.</span><span class="sxs-lookup"><span data-stu-id="13d19-149">The value of `commandName` can specify the web server to launch.</span></span> <span data-ttu-id="13d19-150">`commandName` può avere uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="13d19-150">`commandName` can be any one of the following:</span></span>

* <span data-ttu-id="13d19-151">`IISExpress`: Avvia IIS Express.</span><span class="sxs-lookup"><span data-stu-id="13d19-151">`IISExpress` : Launches IIS Express.</span></span>
* <span data-ttu-id="13d19-152">`IIS`: Non è stato avviato alcun server Web.</span><span class="sxs-lookup"><span data-stu-id="13d19-152">`IIS` : No web server launched.</span></span> <span data-ttu-id="13d19-153">È previsto che IIS sia disponibile.</span><span class="sxs-lookup"><span data-stu-id="13d19-153">IIS is expected to be available.</span></span>
* <span data-ttu-id="13d19-154">`Project`: Avvia gheppio.</span><span class="sxs-lookup"><span data-stu-id="13d19-154">`Project` : Launches Kestrel.</span></span>

<span data-ttu-id="13d19-155">La scheda **debug** delle proprietà del progetto di Visual Studio fornisce un'interfaccia utente grafica per modificare il *launchSettings.jssu* file.</span><span class="sxs-lookup"><span data-stu-id="13d19-155">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file.</span></span> <span data-ttu-id="13d19-156">È possibile che le modifiche apportate ai profili di progetto abbiano effetto solo dopo il riavvio del server Web.</span><span class="sxs-lookup"><span data-stu-id="13d19-156">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="13d19-157">Perché Kestrel rilevi le modifiche apportate al suo ambiente, deve essere riavviato.</span><span class="sxs-lookup"><span data-stu-id="13d19-157">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

![Proprietà progetto - Impostazione delle variabili di ambiente](environments/_static/project-properties-debug.png)

<span data-ttu-id="13d19-159">Il *launchSettings.jsseguente nel* file contiene più profili:</span><span class="sxs-lookup"><span data-stu-id="13d19-159">The following *launchSettings.json* file contains multiple profiles:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

<span data-ttu-id="13d19-160">È possibile selezionare i profili:</span><span class="sxs-lookup"><span data-stu-id="13d19-160">Profiles can be selected:</span></span>

* <span data-ttu-id="13d19-161">Dall'interfaccia utente di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="13d19-161">From the Visual Studio UI.</span></span>
* <span data-ttu-id="13d19-162">Utilizzando il [`dotnet run`](/dotnet/core/tools/dotnet-run) comando in una shell dei comandi con l' `--launch-profile` opzione impostata sul nome del profilo.</span><span class="sxs-lookup"><span data-stu-id="13d19-162">Using the [`dotnet run`](/dotnet/core/tools/dotnet-run) command in a command shell with the `--launch-profile` option set to the profile's name.</span></span> <span data-ttu-id="13d19-163">*Questo approccio supporta solo profili gheppio.*</span><span class="sxs-lookup"><span data-stu-id="13d19-163">*This approach only supports Kestrel profiles.*</span></span>

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> <span data-ttu-id="13d19-164">Evitare di archiviare segreti in *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="13d19-164">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="13d19-165">Per l'archiviazione di segreti per lo sviluppo locale, usare lo [strumento Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="13d19-165">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="13d19-166">Quando si usa [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="13d19-166">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="13d19-167">Nell'esempio seguente vengono impostate diverse [variabili di ambiente per i valori di configurazione host](xref:fundamentals/host/web-host#host-configuration-values):</span><span class="sxs-lookup"><span data-stu-id="13d19-167">The following example sets several [Host configuration values environment variables](xref:fundamentals/host/web-host#host-configuration-values):</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

<span data-ttu-id="13d19-168">Il file *con estensione VSCODE/launch.js* viene utilizzato solo da Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="13d19-168">The *.vscode/launch.json* file is only used by Visual Studio Code.</span></span>

### <a name="production"></a><span data-ttu-id="13d19-169">Produzione</span><span class="sxs-lookup"><span data-stu-id="13d19-169">Production</span></span>

<span data-ttu-id="13d19-170">L'ambiente di produzione deve essere configurato per ottimizzare la sicurezza, le prestazioni e l'affidabilità [delle](xref:performance/performance-best-practices)applicazioni.</span><span class="sxs-lookup"><span data-stu-id="13d19-170">The production environment should be configured to maximize security, [performance](xref:performance/performance-best-practices), and application robustness.</span></span> <span data-ttu-id="13d19-171">Alcune impostazioni comuni che differiscono dallo sviluppo includono:</span><span class="sxs-lookup"><span data-stu-id="13d19-171">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="13d19-172">[Memorizzazione nella cache](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="13d19-172">[Caching](xref:performance/caching/memory).</span></span>
* <span data-ttu-id="13d19-173">Risorse lato client in bundle, minimizzate e potenzialmente offerte da una rete CDN.</span><span class="sxs-lookup"><span data-stu-id="13d19-173">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="13d19-174">Pagine di errore di diagnostica disabilitate.</span><span class="sxs-lookup"><span data-stu-id="13d19-174">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="13d19-175">Pagine di errore descrittive abilitate.</span><span class="sxs-lookup"><span data-stu-id="13d19-175">Friendly error pages enabled.</span></span>
* <span data-ttu-id="13d19-176">[Registrazione](xref:fundamentals/logging/index) e monitoraggio di produzione abilitati.</span><span class="sxs-lookup"><span data-stu-id="13d19-176">Production [logging](xref:fundamentals/logging/index) and monitoring enabled.</span></span> <span data-ttu-id="13d19-177">Ad esempio, usando [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="13d19-177">For example, using [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="13d19-178">Impostare l'ambiente</span><span class="sxs-lookup"><span data-stu-id="13d19-178">Set the environment</span></span>

<span data-ttu-id="13d19-179">Spesso è utile impostare un ambiente specifico per il test con una variabile di ambiente o un'impostazione della piattaforma.</span><span class="sxs-lookup"><span data-stu-id="13d19-179">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="13d19-180">Se l'ambiente non viene impostato, il valore predefinito è `Production`, che disabilita la maggior parte delle funzionalità di debug.</span><span class="sxs-lookup"><span data-stu-id="13d19-180">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="13d19-181">Il metodo per l'impostazione dell'ambiente dipende dal sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="13d19-181">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="13d19-182">Quando viene compilato l'host, l'ultima impostazione dell'ambiente letta dall'app determina l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="13d19-182">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="13d19-183">L'ambiente dell'app non può essere modificato mentre l'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="13d19-183">The app's environment can't be changed while the app is running.</span></span>

<span data-ttu-id="13d19-184">Nella [pagina about](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) del [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) viene visualizzato il valore di `IWebHostEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="13d19-184">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="13d19-185">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="13d19-185">Azure App Service</span></span>

<span data-ttu-id="13d19-186">Per impostare l'ambiente in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/), attenersi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="13d19-186">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="13d19-187">Selezionare l'app dal pannello **Servizi app**.</span><span class="sxs-lookup"><span data-stu-id="13d19-187">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="13d19-188">Nel gruppo **Impostazioni** selezionare il pannello **configurazione** .</span><span class="sxs-lookup"><span data-stu-id="13d19-188">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="13d19-189">Nella scheda **Impostazioni applicazione** selezionare **nuova impostazione applicazione**.</span><span class="sxs-lookup"><span data-stu-id="13d19-189">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="13d19-190">Nella finestra **Aggiungi/modifica impostazione applicazione** specificare `ASPNETCORE_ENVIRONMENT` il **nome**.</span><span class="sxs-lookup"><span data-stu-id="13d19-190">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="13d19-191">Per **valore**, specificare l'ambiente (ad esempio, `Staging` ).</span><span class="sxs-lookup"><span data-stu-id="13d19-191">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="13d19-192">Selezionare la casella di controllo **Impostazioni slot di distribuzione** se si desidera che l'impostazione dell'ambiente rimanga con lo slot corrente quando vengono scambiati gli slot di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="13d19-192">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="13d19-193">Per altre informazioni, vedere [configurare gli ambienti di staging nel servizio app Azure](/azure/app-service/web-sites-staged-publishing) nella documentazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="13d19-193">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="13d19-194">Selezionare **OK** per chiudere la finestra **Aggiungi/modifica impostazione applicazione** .</span><span class="sxs-lookup"><span data-stu-id="13d19-194">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="13d19-195">Selezionare **Save (Salva** ) nella parte superiore del pannello **Configuration (configurazione** ).</span><span class="sxs-lookup"><span data-stu-id="13d19-195">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="13d19-196">App Azure servizio riavvia automaticamente l'app dopo l'aggiunta, la modifica o l'eliminazione di un'impostazione dell'app nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="13d19-196">Azure App Service automatically restarts the app after an app setting is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="13d19-197">Windows</span><span class="sxs-lookup"><span data-stu-id="13d19-197">Windows</span></span>

<span data-ttu-id="13d19-198">I valori di ambiente in *launchSettings.jssui* valori di override impostati nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="13d19-198">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="13d19-199">Per impostare la variabile `ASPNETCORE_ENVIRONMENT` per la sessione corrente, se l'app viene avviata tramite [dotnet run](/dotnet/core/tools/dotnet-run), vengono usati i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="13d19-199">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="13d19-200">**Prompt dei comandi**</span><span class="sxs-lookup"><span data-stu-id="13d19-200">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

<span data-ttu-id="13d19-201">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="13d19-201">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

<span data-ttu-id="13d19-202">Il comando precedente imposta `ASPNETCORE_ENVIRONMENT` solo per i processi avviati dalla finestra di comando.</span><span class="sxs-lookup"><span data-stu-id="13d19-202">The preceding command sets `ASPNETCORE_ENVIRONMENT` only for processes launched from that command window.</span></span>

<span data-ttu-id="13d19-203">Per impostare il valore a livello globale in Windows, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="13d19-203">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="13d19-204">Scegliere **Pannello di controllo** > **Sistema** > **Impostazioni di sistema avanzate** e aggiungere o modificare il valore `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="13d19-204">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Proprietà di sistema avanzate](environments/_static/systemsetting_environment.png)

  ![Variabile di ambiente ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="13d19-207">Aprire un prompt dei comandi di amministrazione e usare il comando `setx` o aprire un prompt dei comandi di PowerShell di amministrazione e usare `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="13d19-207">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="13d19-208">**Prompt dei comandi**</span><span class="sxs-lookup"><span data-stu-id="13d19-208">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  <span data-ttu-id="13d19-209">L'opzione `/M` indica di impostare la variabile di ambiente a livello del sistema.</span><span class="sxs-lookup"><span data-stu-id="13d19-209">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="13d19-210">Se non viene usata l'opzione `/M`, la variabile di ambiente viene impostata per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="13d19-210">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="13d19-211">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="13d19-211">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  <span data-ttu-id="13d19-212">Il valore dell'opzione `Machine` indica di impostare la variabile di ambiente a livello del sistema.</span><span class="sxs-lookup"><span data-stu-id="13d19-212">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="13d19-213">Se non viene usata l'opzione `User`, la variabile di ambiente viene impostata per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="13d19-213">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="13d19-214">Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` è impostata a livello globale, viene applicata per `dotnet run` in tutte le finestre di comando aperte dopo l'impostazione del valore.</span><span class="sxs-lookup"><span data-stu-id="13d19-214">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span> <span data-ttu-id="13d19-215">I valori di ambiente in *launchSettings.jssui* valori di override impostati nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="13d19-215">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="13d19-216">**web.config**</span><span class="sxs-lookup"><span data-stu-id="13d19-216">**web.config**</span></span>

<span data-ttu-id="13d19-217">Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` con *web.config*, vedere la sezione *Impostazione delle variabili di ambiente* di <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="13d19-217">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="13d19-218">**File di progetto o profilo di pubblicazione**</span><span class="sxs-lookup"><span data-stu-id="13d19-218">**Project file or publish profile**</span></span>

<span data-ttu-id="13d19-219">**Per le distribuzioni di Windows IIS:** Includere la `<EnvironmentName>` proprietà nel [profilo di pubblicazione (con estensione pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="13d19-219">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="13d19-220">Questo approccio imposta l'ambiente in *web.config* quando viene pubblicato il progetto:</span><span class="sxs-lookup"><span data-stu-id="13d19-220">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="13d19-221">**Pool di applicazioni IIS singoli**</span><span class="sxs-lookup"><span data-stu-id="13d19-221">**Per IIS Application Pool**</span></span>

<span data-ttu-id="13d19-222">Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` per un'app in esecuzione in un pool di applicazioni isolato (supportato in IIS 10.0 o versioni successive), vedere la sezione *AppCmd.exe* dell'argomento [Variabili di ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="13d19-222">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="13d19-223">Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` viene impostata per un pool di app, il suo valore esegue l'override di un'impostazione a livello di sistema.</span><span class="sxs-lookup"><span data-stu-id="13d19-223">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

<span data-ttu-id="13d19-224">Durante l'hosting di un'app in IIS, quando si aggiunge o si modifica la variabile di ambiente `ASPNETCORE_ENVIRONMENT`, usare uno degli approcci seguenti per fare in modo che il nuovo valore venga selezionato dalle app:</span><span class="sxs-lookup"><span data-stu-id="13d19-224">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>

* <span data-ttu-id="13d19-225">Eseguire `net stop was /y` seguito da `net start w3svc` da un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="13d19-225">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
* <span data-ttu-id="13d19-226">Riavviare il server.</span><span class="sxs-lookup"><span data-stu-id="13d19-226">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="13d19-227">macOS</span><span class="sxs-lookup"><span data-stu-id="13d19-227">macOS</span></span>

<span data-ttu-id="13d19-228">L'impostazione dell'ambiente corrente per macOS può essere eseguita in linea durante l'esecuzione dell'app:</span><span class="sxs-lookup"><span data-stu-id="13d19-228">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

<span data-ttu-id="13d19-229">In alternativa, impostare l'ambiente tramite `export` prima di eseguire l'app:</span><span class="sxs-lookup"><span data-stu-id="13d19-229">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

<span data-ttu-id="13d19-230">Le variabili di ambiente a livello computer sono impostate nel file con estensione *bashrc* o *bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="13d19-230">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="13d19-231">Modificare il file usando un qualsiasi editor di testo.</span><span class="sxs-lookup"><span data-stu-id="13d19-231">Edit the file using any text editor.</span></span> <span data-ttu-id="13d19-232">Aggiungere l'istruzione seguente:</span><span class="sxs-lookup"><span data-stu-id="13d19-232">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a><span data-ttu-id="13d19-233">Linux</span><span class="sxs-lookup"><span data-stu-id="13d19-233">Linux</span></span>

<span data-ttu-id="13d19-234">Per le distribuzioni di Linux, usare il `export` comando al prompt dei comandi per le impostazioni delle variabili basate sulla sessione e *bash_profile* file per le impostazioni di ambiente a livello di computer.</span><span class="sxs-lookup"><span data-stu-id="13d19-234">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="13d19-235">Impostazione dell'ambiente nel codice</span><span class="sxs-lookup"><span data-stu-id="13d19-235">Set the environment in code</span></span>

<span data-ttu-id="13d19-236">Chiamare <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> quando si compila l'host.</span><span class="sxs-lookup"><span data-stu-id="13d19-236">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="13d19-237">Vedere <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="13d19-237">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="13d19-238">Configurazione per ambiente</span><span class="sxs-lookup"><span data-stu-id="13d19-238">Configuration by environment</span></span>

<span data-ttu-id="13d19-239">Per caricare la configurazione in base all'ambiente, vedere <xref:fundamentals/configuration/index#json-configuration-provider> .</span><span class="sxs-lookup"><span data-stu-id="13d19-239">To load configuration by environment, see <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="13d19-240">Classe Startup e metodi basati sull'ambiente</span><span class="sxs-lookup"><span data-stu-id="13d19-240">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="13d19-241">Inserire IWebHostEnvironment nella classe Startup</span><span class="sxs-lookup"><span data-stu-id="13d19-241">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="13d19-242">Inserire <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> nel `Startup` costruttore.</span><span class="sxs-lookup"><span data-stu-id="13d19-242">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="13d19-243">Questo approccio è utile quando l'app richiede la configurazione `Startup` solo per alcuni ambienti con differenze minime di codice per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="13d19-243">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="13d19-244">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="13d19-244">In the following example:</span></span>

* <span data-ttu-id="13d19-245">L'ambiente viene mantenuto nel `_env` campo.</span><span class="sxs-lookup"><span data-stu-id="13d19-245">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="13d19-246">`_env`viene usato in `ConfigureServices` e `Configure` per applicare la configurazione di avvio in base all'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="13d19-246">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a><span data-ttu-id="13d19-247">Convenzioni delle classi di avvio</span><span class="sxs-lookup"><span data-stu-id="13d19-247">Startup class conventions</span></span>

<span data-ttu-id="13d19-248">Quando viene avviata un'app ASP.NET Core, la [classe Startup](xref:fundamentals/startup) avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="13d19-248">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="13d19-249">L'app può definire più `Startup` classi per ambienti diversi.</span><span class="sxs-lookup"><span data-stu-id="13d19-249">The app can define multiple `Startup` classes for different environments.</span></span> <span data-ttu-id="13d19-250">La `Startup` classe appropriata viene selezionata in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="13d19-250">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="13d19-251">La classe il cui suffisso di nome corrisponde all'ambiente corrente ha la priorità.</span><span class="sxs-lookup"><span data-stu-id="13d19-251">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="13d19-252">Se non viene trovata una classe `Startup{EnvironmentName}` corrispondente, viene usata la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="13d19-252">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="13d19-253">Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="13d19-253">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span> <span data-ttu-id="13d19-254">Questo approccio non è necessario per le app tipiche.</span><span class="sxs-lookup"><span data-stu-id="13d19-254">Typical apps will not need this approach.</span></span>

<span data-ttu-id="13d19-255">Per implementare classi basate sull'ambiente `Startup` , creare `Startup{EnvironmentName}` classi e una classe di fallback `Startup` :</span><span class="sxs-lookup"><span data-stu-id="13d19-255">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` classes and a fallback `Startup` class:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

<span data-ttu-id="13d19-256">Usare l'overload [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) che accetta un nome di assembly:</span><span class="sxs-lookup"><span data-stu-id="13d19-256">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a><span data-ttu-id="13d19-257">Convenzioni dei metodi di avvio</span><span class="sxs-lookup"><span data-stu-id="13d19-257">Startup method conventions</span></span>

<span data-ttu-id="13d19-258">[Configurare](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) supportare versioni specifiche dell'ambiente del modulo `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services` .</span><span class="sxs-lookup"><span data-stu-id="13d19-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="13d19-259">Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ambiente:</span><span class="sxs-lookup"><span data-stu-id="13d19-259">This approach is useful when the app requires configuring startup for several environments with many code differences per environment:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="13d19-260">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="13d19-260">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="13d19-261">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="13d19-261">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="13d19-262">ASP.NET Core configura il comportamento dell'app in base all'ambiente di runtime e tramite una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="13d19-262">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="13d19-263">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="13d19-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="13d19-264">Ambienti</span><span class="sxs-lookup"><span data-stu-id="13d19-264">Environments</span></span>

<span data-ttu-id="13d19-265">ASP.NET Core legge la variabile di ambiente `ASPNETCORE_ENVIRONMENT` all'avvio dell'app e ne archivia il valore in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="13d19-265">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="13d19-266">`ASPNETCORE_ENVIRONMENT`può essere impostato su qualsiasi valore, ma vengono forniti tre valori dal Framework:</span><span class="sxs-lookup"><span data-stu-id="13d19-266">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="13d19-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="13d19-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="13d19-268">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="13d19-268">The preceding code:</span></span>

* <span data-ttu-id="13d19-269">Chiama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` è impostato su `Development`.</span><span class="sxs-lookup"><span data-stu-id="13d19-269">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="13d19-270">Chiama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando il valore di `ASPNETCORE_ENVIRONMENT` è uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="13d19-270">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="13d19-271">L'[helper per tag di ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa il valore di `IHostingEnvironment.EnvironmentName` per includere o escludere il markup nell'elemento:</span><span class="sxs-lookup"><span data-stu-id="13d19-271">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="13d19-272">In Windows e macOS, le variabili di ambiente e i valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="13d19-272">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="13d19-273">Le variabili e i valori di ambiente Linux fanno distinzione tra maiuscole e minuscole per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="13d19-273">Linux environment variables and values are case-sensitive by default.</span></span>

### <a name="development"></a><span data-ttu-id="13d19-274">Sviluppo</span><span class="sxs-lookup"><span data-stu-id="13d19-274">Development</span></span>

<span data-ttu-id="13d19-275">L'ambiente di sviluppo può abilitare funzionalità che non devono essere esposte nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="13d19-275">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="13d19-276">Ad esempio i modelli ASP.NET Core abilitano la [pagina delle eccezioni per gli sviluppatori](xref:fundamentals/error-handling#developer-exception-page) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="13d19-276">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="13d19-277">L'ambiente di sviluppo computer locale può essere impostato nel file *Properties\launchSettings.json* del progetto.</span><span class="sxs-lookup"><span data-stu-id="13d19-277">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="13d19-278">I valori di ambiente in *launchSettings.json* sostituiscono i valori impostati nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="13d19-278">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="13d19-279">Il codice JSON seguente visualizza tre profili di un file *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="13d19-279">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="13d19-280">La proprietà `applicationUrl` in *launchSettings.json* può specificare un elenco di URL di server.</span><span class="sxs-lookup"><span data-stu-id="13d19-280">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="13d19-281">Usare un punto e virgola tra gli URL dell'elenco:</span><span class="sxs-lookup"><span data-stu-id="13d19-281">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="13d19-282">Quando l'app viene avviata con [dotnet run](/dotnet/core/tools/dotnet-run) viene usato il primo profilo con `"commandName": "Project"`.</span><span class="sxs-lookup"><span data-stu-id="13d19-282">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="13d19-283">Il valore di `commandName` specifica il server Web da avviare.</span><span class="sxs-lookup"><span data-stu-id="13d19-283">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="13d19-284">`commandName` può avere uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="13d19-284">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="13d19-285">`Project` (che avvia Kestrel)</span><span class="sxs-lookup"><span data-stu-id="13d19-285">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="13d19-286">Quando un'app viene avviata con [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="13d19-286">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="13d19-287">*launchSettings.json*, se disponibile, viene letto.</span><span class="sxs-lookup"><span data-stu-id="13d19-287">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="13d19-288">Le impostazioni `environmentVariables` in *launchSettings.json* sostituiscono le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="13d19-288">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="13d19-289">Viene visualizzato l'ambiente host.</span><span class="sxs-lookup"><span data-stu-id="13d19-289">The hosting environment is displayed.</span></span>

<span data-ttu-id="13d19-290">L'output seguente visualizza un'app avviata con [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="13d19-290">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="13d19-291">La scheda **Debug** nelle proprietà di progetto di Visual Studio visualizza una GUI per la modifica del file *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="13d19-291">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Proprietà progetto - Impostazione delle variabili di ambiente](environments/_static/project-properties-debug.png)

<span data-ttu-id="13d19-293">È possibile che le modifiche apportate ai profili di progetto abbiano effetto solo dopo il riavvio del server Web.</span><span class="sxs-lookup"><span data-stu-id="13d19-293">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="13d19-294">Perché Kestrel rilevi le modifiche apportate al suo ambiente, deve essere riavviato.</span><span class="sxs-lookup"><span data-stu-id="13d19-294">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="13d19-295">Evitare di archiviare segreti in *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="13d19-295">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="13d19-296">Per l'archiviazione di segreti per lo sviluppo locale, usare lo [strumento Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="13d19-296">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="13d19-297">Quando si usa [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="13d19-297">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="13d19-298">Nell'esempio seguente l'ambiente viene impostato su `Development`:</span><span class="sxs-lookup"><span data-stu-id="13d19-298">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="13d19-299">Un file *.vscode/launch.json* del progetto non viene letto quando si avvia l'app con `dotnet run` come accade per il file *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="13d19-299">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="13d19-300">Quando si avvia un'app in un ambiente di sviluppo che non ha un file *launchSettings.json*, impostare una variabile di ambiente o un argomento della riga di comando sul comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="13d19-300">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="13d19-301">Produzione</span><span class="sxs-lookup"><span data-stu-id="13d19-301">Production</span></span>

<span data-ttu-id="13d19-302">È necessario che l'ambiente di produzione sia configurato per ottimizzare la sicurezza, le prestazioni e l'affidabilità dell'app.</span><span class="sxs-lookup"><span data-stu-id="13d19-302">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="13d19-303">Alcune impostazioni comuni che differiscono dallo sviluppo includono:</span><span class="sxs-lookup"><span data-stu-id="13d19-303">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="13d19-304">Memorizzazione nella cache</span><span class="sxs-lookup"><span data-stu-id="13d19-304">Caching.</span></span>
* <span data-ttu-id="13d19-305">Risorse lato client in bundle, minimizzate e potenzialmente offerte da una rete CDN.</span><span class="sxs-lookup"><span data-stu-id="13d19-305">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="13d19-306">Pagine di errore di diagnostica disabilitate.</span><span class="sxs-lookup"><span data-stu-id="13d19-306">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="13d19-307">Pagine di errore descrittive abilitate.</span><span class="sxs-lookup"><span data-stu-id="13d19-307">Friendly error pages enabled.</span></span>
* <span data-ttu-id="13d19-308">Registrazione e monitoraggio della produzione abilitati.</span><span class="sxs-lookup"><span data-stu-id="13d19-308">Production logging and monitoring enabled.</span></span> <span data-ttu-id="13d19-309">Ad esempio, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="13d19-309">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="13d19-310">Impostare l'ambiente</span><span class="sxs-lookup"><span data-stu-id="13d19-310">Set the environment</span></span>

<span data-ttu-id="13d19-311">Spesso è utile impostare un ambiente specifico per il test con una variabile di ambiente o un'impostazione della piattaforma.</span><span class="sxs-lookup"><span data-stu-id="13d19-311">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="13d19-312">Se l'ambiente non viene impostato, il valore predefinito è `Production`, che disabilita la maggior parte delle funzionalità di debug.</span><span class="sxs-lookup"><span data-stu-id="13d19-312">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="13d19-313">Il metodo per l'impostazione dell'ambiente dipende dal sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="13d19-313">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="13d19-314">Quando viene compilato l'host, l'ultima impostazione dell'ambiente letta dall'app determina l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="13d19-314">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="13d19-315">L'ambiente dell'app non può essere modificato mentre l'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="13d19-315">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="13d19-316">Impostazione della piattaforma o della variabile di ambiente</span><span class="sxs-lookup"><span data-stu-id="13d19-316">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="13d19-317">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="13d19-317">Azure App Service</span></span>

<span data-ttu-id="13d19-318">Per impostare l'ambiente in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/), attenersi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="13d19-318">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="13d19-319">Selezionare l'app dal pannello **Servizi app**.</span><span class="sxs-lookup"><span data-stu-id="13d19-319">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="13d19-320">Nel gruppo **Impostazioni** selezionare il pannello **configurazione** .</span><span class="sxs-lookup"><span data-stu-id="13d19-320">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="13d19-321">Nella scheda **Impostazioni applicazione** selezionare **nuova impostazione applicazione**.</span><span class="sxs-lookup"><span data-stu-id="13d19-321">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="13d19-322">Nella finestra **Aggiungi/modifica impostazione applicazione** specificare `ASPNETCORE_ENVIRONMENT` il **nome**.</span><span class="sxs-lookup"><span data-stu-id="13d19-322">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="13d19-323">Per **valore**, specificare l'ambiente (ad esempio, `Staging` ).</span><span class="sxs-lookup"><span data-stu-id="13d19-323">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="13d19-324">Selezionare la casella di controllo **Impostazioni slot di distribuzione** se si desidera che l'impostazione dell'ambiente rimanga con lo slot corrente quando vengono scambiati gli slot di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="13d19-324">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="13d19-325">Per altre informazioni, vedere [configurare gli ambienti di staging nel servizio app Azure](/azure/app-service/web-sites-staged-publishing) nella documentazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="13d19-325">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="13d19-326">Selezionare **OK** per chiudere la finestra **Aggiungi/modifica impostazione applicazione** .</span><span class="sxs-lookup"><span data-stu-id="13d19-326">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="13d19-327">Selezionare **Save (Salva** ) nella parte superiore del pannello **Configuration (configurazione** ).</span><span class="sxs-lookup"><span data-stu-id="13d19-327">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="13d19-328">Servizio app di Azure riavvia automaticamente l'app quando un'impostazione dell'app (una variabile di ambiente) viene aggiunta, modificata o eliminata nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="13d19-328">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="13d19-329">Windows</span><span class="sxs-lookup"><span data-stu-id="13d19-329">Windows</span></span>

<span data-ttu-id="13d19-330">Per impostare la variabile `ASPNETCORE_ENVIRONMENT` per la sessione corrente, se l'app viene avviata tramite [dotnet run](/dotnet/core/tools/dotnet-run), vengono usati i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="13d19-330">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="13d19-331">**Prompt dei comandi**</span><span class="sxs-lookup"><span data-stu-id="13d19-331">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="13d19-332">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="13d19-332">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="13d19-333">Questi comandi hanno effetto solo per la finestra corrente.</span><span class="sxs-lookup"><span data-stu-id="13d19-333">These commands only take effect for the current window.</span></span> <span data-ttu-id="13d19-334">Quando la finestra viene chiusa, l'impostazione `ASPNETCORE_ENVIRONMENT` viene ripristinata sull'impostazione predefinita o sul valore del computer.</span><span class="sxs-lookup"><span data-stu-id="13d19-334">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="13d19-335">Per impostare il valore a livello globale in Windows, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="13d19-335">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="13d19-336">Scegliere **Pannello di controllo** > **Sistema** > **Impostazioni di sistema avanzate** e aggiungere o modificare il valore `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="13d19-336">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Proprietà di sistema avanzate](environments/_static/systemsetting_environment.png)

  ![Variabile di ambiente ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="13d19-339">Aprire un prompt dei comandi di amministrazione e usare il comando `setx` o aprire un prompt dei comandi di PowerShell di amministrazione e usare `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="13d19-339">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="13d19-340">**Prompt dei comandi**</span><span class="sxs-lookup"><span data-stu-id="13d19-340">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="13d19-341">L'opzione `/M` indica di impostare la variabile di ambiente a livello del sistema.</span><span class="sxs-lookup"><span data-stu-id="13d19-341">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="13d19-342">Se non viene usata l'opzione `/M`, la variabile di ambiente viene impostata per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="13d19-342">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="13d19-343">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="13d19-343">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="13d19-344">Il valore dell'opzione `Machine` indica di impostare la variabile di ambiente a livello del sistema.</span><span class="sxs-lookup"><span data-stu-id="13d19-344">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="13d19-345">Se non viene usata l'opzione `User`, la variabile di ambiente viene impostata per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="13d19-345">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="13d19-346">Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` è impostata a livello globale, viene applicata per `dotnet run` in tutte le finestre di comando aperte dopo l'impostazione del valore.</span><span class="sxs-lookup"><span data-stu-id="13d19-346">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="13d19-347">**web.config**</span><span class="sxs-lookup"><span data-stu-id="13d19-347">**web.config**</span></span>

<span data-ttu-id="13d19-348">Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` con *web.config*, vedere la sezione *Impostazione delle variabili di ambiente* di <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="13d19-348">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="13d19-349">**File di progetto o profilo di pubblicazione**</span><span class="sxs-lookup"><span data-stu-id="13d19-349">**Project file or publish profile**</span></span>

<span data-ttu-id="13d19-350">**Per le distribuzioni di Windows IIS:** Includere la `<EnvironmentName>` proprietà nel [profilo di pubblicazione (con estensione pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="13d19-350">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="13d19-351">Questo approccio imposta l'ambiente in *web.config* quando viene pubblicato il progetto:</span><span class="sxs-lookup"><span data-stu-id="13d19-351">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="13d19-352">**Pool di applicazioni IIS singoli**</span><span class="sxs-lookup"><span data-stu-id="13d19-352">**Per IIS Application Pool**</span></span>

<span data-ttu-id="13d19-353">Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` per un'app in esecuzione in un pool di applicazioni isolato (supportato in IIS 10.0 o versioni successive), vedere la sezione *AppCmd.exe* dell'argomento [Variabili di ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="13d19-353">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="13d19-354">Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` viene impostata per un pool di app, il suo valore esegue l'override di un'impostazione a livello di sistema.</span><span class="sxs-lookup"><span data-stu-id="13d19-354">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="13d19-355">Durante l'hosting di un'app in IIS, quando si aggiunge o si modifica la variabile di ambiente `ASPNETCORE_ENVIRONMENT`, usare uno degli approcci seguenti per fare in modo che il nuovo valore venga selezionato dalle app:</span><span class="sxs-lookup"><span data-stu-id="13d19-355">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="13d19-356">Eseguire `net stop was /y` seguito da `net start w3svc` da un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="13d19-356">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="13d19-357">Riavviare il server.</span><span class="sxs-lookup"><span data-stu-id="13d19-357">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="13d19-358">macOS</span><span class="sxs-lookup"><span data-stu-id="13d19-358">macOS</span></span>

<span data-ttu-id="13d19-359">L'impostazione dell'ambiente corrente per macOS può essere eseguita in linea durante l'esecuzione dell'app:</span><span class="sxs-lookup"><span data-stu-id="13d19-359">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="13d19-360">In alternativa, impostare l'ambiente tramite `export` prima di eseguire l'app:</span><span class="sxs-lookup"><span data-stu-id="13d19-360">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="13d19-361">Le variabili di ambiente a livello computer sono impostate nel file con estensione *bashrc* o *bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="13d19-361">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="13d19-362">Modificare il file usando un qualsiasi editor di testo.</span><span class="sxs-lookup"><span data-stu-id="13d19-362">Edit the file using any text editor.</span></span> <span data-ttu-id="13d19-363">Aggiungere l'istruzione seguente:</span><span class="sxs-lookup"><span data-stu-id="13d19-363">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="13d19-364">Linux</span><span class="sxs-lookup"><span data-stu-id="13d19-364">Linux</span></span>

<span data-ttu-id="13d19-365">Per le distribuzioni di Linux, usare il `export` comando al prompt dei comandi per le impostazioni delle variabili basate sulla sessione e *bash_profile* file per le impostazioni di ambiente a livello di computer.</span><span class="sxs-lookup"><span data-stu-id="13d19-365">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="13d19-366">Impostazione dell'ambiente nel codice</span><span class="sxs-lookup"><span data-stu-id="13d19-366">Set the environment in code</span></span>

<span data-ttu-id="13d19-367">Chiamare <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> quando si compila l'host.</span><span class="sxs-lookup"><span data-stu-id="13d19-367">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="13d19-368">Vedere <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="13d19-368">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="13d19-369">Configurazione per ambiente</span><span class="sxs-lookup"><span data-stu-id="13d19-369">Configuration by environment</span></span>

<span data-ttu-id="13d19-370">Per caricare la configurazione dall'ambiente, sono consigliabili:</span><span class="sxs-lookup"><span data-stu-id="13d19-370">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="13d19-371">file *appSettings* (*appSettings. { Environment}. JSON*).</span><span class="sxs-lookup"><span data-stu-id="13d19-371">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="13d19-372">Vedere <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="13d19-372">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="13d19-373">Variabili di ambiente (impostate in ogni sistema in cui è ospitata l'app).</span><span class="sxs-lookup"><span data-stu-id="13d19-373">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="13d19-374">Controllare <xref:fundamentals/host/web-host#environment> e <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="13d19-374">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="13d19-375">Secret Manager (solo nell'ambiente di sviluppo).</span><span class="sxs-lookup"><span data-stu-id="13d19-375">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="13d19-376">Vedere <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="13d19-376">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="13d19-377">Classe Startup e metodi basati sull'ambiente</span><span class="sxs-lookup"><span data-stu-id="13d19-377">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="13d19-378">Inserire IHostingEnvironment in Startup.ConfigUre</span><span class="sxs-lookup"><span data-stu-id="13d19-378">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="13d19-379">Inserire <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="13d19-379">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="13d19-380">Questo approccio è utile quando l'app richiede solo la configurazione `Startup.Configure` solo per alcuni ambienti con differenze minime di codice per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="13d19-380">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="13d19-381">Inserire IHostingEnvironment nella classe Startup</span><span class="sxs-lookup"><span data-stu-id="13d19-381">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="13d19-382">Inserire <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> nel `Startup` costruttore e assegnare il servizio a un campo da utilizzare in tutta la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="13d19-382">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="13d19-383">Questo approccio è utile quando l'app richiede la configurazione dell'avvio per solo alcuni ambienti con differenze minime di codice per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="13d19-383">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="13d19-384">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="13d19-384">In the following example:</span></span>

* <span data-ttu-id="13d19-385">L'ambiente viene mantenuto nel `_env` campo.</span><span class="sxs-lookup"><span data-stu-id="13d19-385">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="13d19-386">`_env`viene usato in `ConfigureServices` e `Configure` per applicare la configurazione di avvio in base all'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="13d19-386">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="13d19-387">Convenzioni delle classi di avvio</span><span class="sxs-lookup"><span data-stu-id="13d19-387">Startup class conventions</span></span>

<span data-ttu-id="13d19-388">Quando viene avviata un'app ASP.NET Core, la [classe Startup](xref:fundamentals/startup) avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="13d19-388">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="13d19-389">L'app può definire `Startup` classi separate per ambienti diversi, ad esempio `StartupDevelopment` .</span><span class="sxs-lookup"><span data-stu-id="13d19-389">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="13d19-390">La `Startup` classe appropriata viene selezionata in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="13d19-390">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="13d19-391">La classe il cui suffisso di nome corrisponde all'ambiente corrente ha la priorità.</span><span class="sxs-lookup"><span data-stu-id="13d19-391">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="13d19-392">Se non viene trovata una classe `Startup{EnvironmentName}` corrispondente, viene usata la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="13d19-392">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="13d19-393">Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="13d19-393">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="13d19-394">Per implementare le classi `Startup` basate sull'ambiente, creare una classe `Startup{EnvironmentName}` per ogni ambiente in uso e una classe `Startup` di fallback:</span><span class="sxs-lookup"><span data-stu-id="13d19-394">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="13d19-395">Usare l'overload [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) che accetta un nome di assembly:</span><span class="sxs-lookup"><span data-stu-id="13d19-395">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="13d19-396">Convenzioni dei metodi di avvio</span><span class="sxs-lookup"><span data-stu-id="13d19-396">Startup method conventions</span></span>

<span data-ttu-id="13d19-397">[Configurare](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) supportare versioni specifiche dell'ambiente del modulo `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services` .</span><span class="sxs-lookup"><span data-stu-id="13d19-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="13d19-398">Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="13d19-398">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="13d19-399">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="13d19-399">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
