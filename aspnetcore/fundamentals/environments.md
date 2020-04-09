---
title: Usare più ambienti in ASP.NET Core
author: rick-anderson
description: Informazioni su come controllare il comportamento di app ASP.NET Core in più ambienti.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656219"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="944eb-103">Usare più ambienti in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="944eb-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="944eb-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="944eb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="944eb-105">ASP.NET Core configura il comportamento dell'app in base all'ambiente di runtime e tramite una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="944eb-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="944eb-106">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="944eb-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="944eb-107">Ambienti</span><span class="sxs-lookup"><span data-stu-id="944eb-107">Environments</span></span>

<span data-ttu-id="944eb-108">ASP.NET Core legge la `ASPNETCORE_ENVIRONMENT` variabile di ambiente all'avvio dell'app e archivia il valore in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="944eb-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="944eb-109">`ASPNETCORE_ENVIRONMENT`può essere impostato su qualsiasi valore, ma il framework ha fornito tre valori:</span><span class="sxs-lookup"><span data-stu-id="944eb-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="944eb-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="944eb-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="944eb-111">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="944eb-111">The preceding code:</span></span>

* <span data-ttu-id="944eb-112">Chiama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` è impostato su `Development`.</span><span class="sxs-lookup"><span data-stu-id="944eb-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="944eb-113">Chiama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando il valore di `ASPNETCORE_ENVIRONMENT` è uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="944eb-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="944eb-114">L'[helper per tag di ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa il valore di `IHostingEnvironment.EnvironmentName` per includere o escludere il markup nell'elemento:</span><span class="sxs-lookup"><span data-stu-id="944eb-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="944eb-115">In Windows e macOS le variabili di ambiente e i relativi valori non applicano la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="944eb-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="944eb-116">In Linux le variabili di ambiente e i relativi valori **applicano la distinzione tra maiuscole e minuscole** per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="944eb-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="944eb-117">Sviluppo</span><span class="sxs-lookup"><span data-stu-id="944eb-117">Development</span></span>

<span data-ttu-id="944eb-118">L'ambiente di sviluppo può abilitare funzionalità che non devono essere esposte nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="944eb-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="944eb-119">Ad esempio i modelli ASP.NET Core abilitano la [pagina delle eccezioni per gli sviluppatori](xref:fundamentals/error-handling#developer-exception-page) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="944eb-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="944eb-120">L'ambiente di sviluppo computer locale può essere impostato nel file *Properties\launchSettings.json* del progetto.</span><span class="sxs-lookup"><span data-stu-id="944eb-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="944eb-121">I valori di ambiente in *launchSettings.json* sostituiscono i valori impostati nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="944eb-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="944eb-122">Il codice JSON seguente visualizza tre profili di un file *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="944eb-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="944eb-123">La proprietà `applicationUrl` in *launchSettings.json* può specificare un elenco di URL di server.</span><span class="sxs-lookup"><span data-stu-id="944eb-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="944eb-124">Usare un punto e virgola tra gli URL dell'elenco:</span><span class="sxs-lookup"><span data-stu-id="944eb-124">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="944eb-125">Quando l'app viene avviata con [dotnet run](/dotnet/core/tools/dotnet-run) viene usato il primo profilo con `"commandName": "Project"`.</span><span class="sxs-lookup"><span data-stu-id="944eb-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="944eb-126">Il valore di `commandName` specifica il server Web da avviare.</span><span class="sxs-lookup"><span data-stu-id="944eb-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="944eb-127">`commandName` può avere uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="944eb-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="944eb-128">`Project` (che avvia Kestrel)</span><span class="sxs-lookup"><span data-stu-id="944eb-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="944eb-129">Quando un'app viene avviata con [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="944eb-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="944eb-130">*launchSettings.json*, se disponibile, viene letto.</span><span class="sxs-lookup"><span data-stu-id="944eb-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="944eb-131">Le impostazioni `environmentVariables` in *launchSettings.json* sostituiscono le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="944eb-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="944eb-132">Viene visualizzato l'ambiente host.</span><span class="sxs-lookup"><span data-stu-id="944eb-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="944eb-133">L'output seguente visualizza un'app avviata con [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="944eb-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="944eb-134">La scheda **Debug** nelle proprietà di progetto di Visual Studio visualizza una GUI per la modifica del file *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="944eb-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Proprietà progetto - Impostazione delle variabili di ambiente](environments/_static/project-properties-debug.png)

<span data-ttu-id="944eb-136">È possibile che le modifiche apportate ai profili di progetto abbiano effetto solo dopo il riavvio del server Web.</span><span class="sxs-lookup"><span data-stu-id="944eb-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="944eb-137">Perché Kestrel rilevi le modifiche apportate al suo ambiente, deve essere riavviato.</span><span class="sxs-lookup"><span data-stu-id="944eb-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="944eb-138">Evitare di archiviare segreti in *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="944eb-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="944eb-139">Per l'archiviazione di segreti per lo sviluppo locale, usare lo [strumento Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="944eb-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="944eb-140">Quando si usa [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="944eb-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="944eb-141">Nell'esempio seguente l'ambiente viene impostato su `Development`:</span><span class="sxs-lookup"><span data-stu-id="944eb-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="944eb-142">Un file *.vscode/launch.json* del progetto non viene letto quando si avvia l'app con `dotnet run` come accade per il file *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="944eb-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="944eb-143">Quando si avvia un'app in un ambiente di sviluppo che non ha un file *launchSettings.json*, impostare una variabile di ambiente o un argomento della riga di comando sul comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="944eb-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="944eb-144">Produzione</span><span class="sxs-lookup"><span data-stu-id="944eb-144">Production</span></span>

<span data-ttu-id="944eb-145">È necessario che l'ambiente di produzione sia configurato per ottimizzare la sicurezza, le prestazioni e l'affidabilità dell'app.</span><span class="sxs-lookup"><span data-stu-id="944eb-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="944eb-146">Alcune impostazioni comuni che differiscono dallo sviluppo includono:</span><span class="sxs-lookup"><span data-stu-id="944eb-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="944eb-147">Memorizzazione nella cache</span><span class="sxs-lookup"><span data-stu-id="944eb-147">Caching.</span></span>
* <span data-ttu-id="944eb-148">Risorse lato client in bundle, minimizzate e potenzialmente offerte da una rete CDN.</span><span class="sxs-lookup"><span data-stu-id="944eb-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="944eb-149">Pagine di errore di diagnostica disabilitate.</span><span class="sxs-lookup"><span data-stu-id="944eb-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="944eb-150">Pagine di errore descrittive abilitate.</span><span class="sxs-lookup"><span data-stu-id="944eb-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="944eb-151">Registrazione e monitoraggio della produzione abilitati.</span><span class="sxs-lookup"><span data-stu-id="944eb-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="944eb-152">Ad esempio, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="944eb-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="944eb-153">Impostare l'ambiente</span><span class="sxs-lookup"><span data-stu-id="944eb-153">Set the environment</span></span>

<span data-ttu-id="944eb-154">È spesso utile impostare un ambiente specifico per il test con una variabile di ambiente o un'impostazione della piattaforma.</span><span class="sxs-lookup"><span data-stu-id="944eb-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="944eb-155">Se l'ambiente non viene impostato, il valore predefinito è `Production`, che disabilita la maggior parte delle funzionalità di debug.</span><span class="sxs-lookup"><span data-stu-id="944eb-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="944eb-156">Il metodo per l'impostazione dell'ambiente dipende dal sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="944eb-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="944eb-157">Quando l'host viene compilato, l'ultima impostazione di ambiente letta dall'app determina l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="944eb-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="944eb-158">L'ambiente dell'app non può essere modificato mentre l'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="944eb-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="944eb-159">Impostazione della variabile di ambiente o della piattaforma</span><span class="sxs-lookup"><span data-stu-id="944eb-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="944eb-160">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="944eb-160">Azure App Service</span></span>

<span data-ttu-id="944eb-161">Per impostare l'ambiente in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/), attenersi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="944eb-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="944eb-162">Selezionare l'app dal pannello **Servizi app**.</span><span class="sxs-lookup"><span data-stu-id="944eb-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="944eb-163">Nel gruppo Impostazioni selezionare il pannello **Configurazione.In** the **Settings** group, select the Configuration blade.</span><span class="sxs-lookup"><span data-stu-id="944eb-163">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="944eb-164">Nella scheda **Impostazioni applicazione** selezionare **Nuova impostazione applicazione**.</span><span class="sxs-lookup"><span data-stu-id="944eb-164">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="944eb-165">Nella finestra **di impostazione Aggiungi/Modifica applicazione** specificare `ASPNETCORE_ENVIRONMENT` il **Nome**.</span><span class="sxs-lookup"><span data-stu-id="944eb-165">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="944eb-166">Per **Valore**, specificare l'ambiente, `Staging`ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="944eb-166">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="944eb-167">Selezionare la casella di controllo **Impostazione slot di distribuzione** se si desidera che l'impostazione dell'ambiente rimanga con lo slot corrente quando gli slot di distribuzione vengono scambiati.</span><span class="sxs-lookup"><span data-stu-id="944eb-167">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="944eb-168">Per altre informazioni, vedere [Configurare gli ambienti](/azure/app-service/web-sites-staged-publishing) di gestione temporanea nel servizio app di Azure nella documentazione di Azure.For more information, see Set up staging environments in Azure App Service in the Azure documentation.</span><span class="sxs-lookup"><span data-stu-id="944eb-168">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="944eb-169">Selezionare **OK** per chiudere la finestra **di impostazione Aggiungi/Modifica applicazione.**</span><span class="sxs-lookup"><span data-stu-id="944eb-169">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="944eb-170">Selezionare Salva nella parte superiore del pannello **Configurazione.Select** **Save** at the top of the Configuration blade.</span><span class="sxs-lookup"><span data-stu-id="944eb-170">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="944eb-171">Servizio app di Azure riavvia automaticamente l'app quando un'impostazione dell'app (una variabile di ambiente) viene aggiunta, modificata o eliminata nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="944eb-171">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="944eb-172">Windows</span><span class="sxs-lookup"><span data-stu-id="944eb-172">Windows</span></span>

<span data-ttu-id="944eb-173">Per impostare la variabile `ASPNETCORE_ENVIRONMENT` per la sessione corrente, se l'app viene avviata tramite [dotnet run](/dotnet/core/tools/dotnet-run), vengono usati i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="944eb-173">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="944eb-174">**Prompt**</span><span class="sxs-lookup"><span data-stu-id="944eb-174">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="944eb-175">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="944eb-175">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="944eb-176">Questi comandi hanno effetto solo per la finestra corrente.</span><span class="sxs-lookup"><span data-stu-id="944eb-176">These commands only take effect for the current window.</span></span> <span data-ttu-id="944eb-177">Quando la finestra viene chiusa, l'impostazione `ASPNETCORE_ENVIRONMENT` viene ripristinata sull'impostazione predefinita o sul valore del computer.</span><span class="sxs-lookup"><span data-stu-id="944eb-177">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="944eb-178">Per impostare il valore a livello globale in Windows, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="944eb-178">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="944eb-179">Scegliere **Pannello di controllo** > **Sistema** > **Impostazioni di sistema avanzate** e aggiungere o modificare il valore `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="944eb-179">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Proprietà di sistema avanzate](environments/_static/systemsetting_environment.png)

  ![Variabile di ambiente ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="944eb-182">Aprire un prompt dei comandi di amministrazione e usare il comando `setx` o aprire un prompt dei comandi di PowerShell di amministrazione e usare `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="944eb-182">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="944eb-183">**Prompt**</span><span class="sxs-lookup"><span data-stu-id="944eb-183">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="944eb-184">L'opzione `/M` indica di impostare la variabile di ambiente a livello del sistema.</span><span class="sxs-lookup"><span data-stu-id="944eb-184">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="944eb-185">Se non viene usata l'opzione `/M`, la variabile di ambiente viene impostata per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="944eb-185">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="944eb-186">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="944eb-186">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="944eb-187">Il valore dell'opzione `Machine` indica di impostare la variabile di ambiente a livello del sistema.</span><span class="sxs-lookup"><span data-stu-id="944eb-187">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="944eb-188">Se non viene usata l'opzione `User`, la variabile di ambiente viene impostata per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="944eb-188">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="944eb-189">Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` è impostata a livello globale, viene applicata per `dotnet run` in tutte le finestre di comando aperte dopo l'impostazione del valore.</span><span class="sxs-lookup"><span data-stu-id="944eb-189">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="944eb-190">**web.config (informazioni in lingua lingua inlingua**</span><span class="sxs-lookup"><span data-stu-id="944eb-190">**web.config**</span></span>

<span data-ttu-id="944eb-191">Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` con *web.config*, vedere la sezione *Impostazione delle variabili di ambiente* di <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="944eb-191">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="944eb-192">**File di progetto o profilo di pubblicazione**</span><span class="sxs-lookup"><span data-stu-id="944eb-192">**Project file or publish profile**</span></span>

<span data-ttu-id="944eb-193">**Per le distribuzioni di Windows IIS:** Includere `<EnvironmentName>` la proprietà nel profilo di [pubblicazione (pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="944eb-193">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="944eb-194">Questo approccio imposta l'ambiente in *web.config* quando viene pubblicato il progetto:</span><span class="sxs-lookup"><span data-stu-id="944eb-194">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="944eb-195">**Pool di applicazioni IIS singoli**</span><span class="sxs-lookup"><span data-stu-id="944eb-195">**Per IIS Application Pool**</span></span>

<span data-ttu-id="944eb-196">Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` per un'app in esecuzione in un pool di applicazioni isolato (supportato in IIS 10.0 o versioni successive), vedere la sezione *AppCmd.exe* dell'argomento [Variabili di ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="944eb-196">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="944eb-197">Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` viene impostata per un pool di app, il suo valore esegue l'override di un'impostazione a livello di sistema.</span><span class="sxs-lookup"><span data-stu-id="944eb-197">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="944eb-198">Durante l'hosting di un'app in IIS, quando si aggiunge o si modifica la variabile di ambiente `ASPNETCORE_ENVIRONMENT`, usare uno degli approcci seguenti per fare in modo che il nuovo valore venga selezionato dalle app:</span><span class="sxs-lookup"><span data-stu-id="944eb-198">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="944eb-199">Eseguire `net stop was /y` seguito da `net start w3svc` da un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="944eb-199">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="944eb-200">Riavviare il server.</span><span class="sxs-lookup"><span data-stu-id="944eb-200">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="944eb-201">macOS</span><span class="sxs-lookup"><span data-stu-id="944eb-201">macOS</span></span>

<span data-ttu-id="944eb-202">L'impostazione dell'ambiente corrente per macOS può essere eseguita in linea durante l'esecuzione dell'app:</span><span class="sxs-lookup"><span data-stu-id="944eb-202">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="944eb-203">In alternativa, impostare l'ambiente tramite `export` prima di eseguire l'app:</span><span class="sxs-lookup"><span data-stu-id="944eb-203">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="944eb-204">Le variabili di ambiente a livello computer sono impostate nel file con estensione *bashrc* o *bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="944eb-204">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="944eb-205">Modificare il file usando un qualsiasi editor di testo.</span><span class="sxs-lookup"><span data-stu-id="944eb-205">Edit the file using any text editor.</span></span> <span data-ttu-id="944eb-206">Aggiungere l'istruzione seguente:</span><span class="sxs-lookup"><span data-stu-id="944eb-206">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="944eb-207">Linux</span><span class="sxs-lookup"><span data-stu-id="944eb-207">Linux</span></span>

<span data-ttu-id="944eb-208">Per le distribuzioni di Linux, usare il comando `export` al prompt dei comandi per le impostazioni delle variabili basate sulla sessione e il file *bash_profile* per le impostazioni di ambiente a livello di computer.</span><span class="sxs-lookup"><span data-stu-id="944eb-208">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="944eb-209">Impostare l'ambiente nel codiceSet the environment in code</span><span class="sxs-lookup"><span data-stu-id="944eb-209">Set the environment in code</span></span>

<span data-ttu-id="944eb-210">Chiama <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> durante la creazione dell'host.</span><span class="sxs-lookup"><span data-stu-id="944eb-210">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="944eb-211">Vedere <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="944eb-211">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="944eb-212">Configurazione per ambiente</span><span class="sxs-lookup"><span data-stu-id="944eb-212">Configuration by environment</span></span>

<span data-ttu-id="944eb-213">Per caricare la configurazione dall'ambiente, sono consigliabili:</span><span class="sxs-lookup"><span data-stu-id="944eb-213">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="944eb-214">*file appsettings* (*appsettings. Ambiente: .json*).</span><span class="sxs-lookup"><span data-stu-id="944eb-214">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="944eb-215">Vedere <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="944eb-215">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="944eb-216">Variabili di ambiente (impostate in ogni sistema in cui è ospitata l'app).</span><span class="sxs-lookup"><span data-stu-id="944eb-216">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="944eb-217">Controllare <xref:fundamentals/host/generic-host#environmentname> e <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="944eb-217">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="944eb-218">Secret Manager (solo nell'ambiente di sviluppo).</span><span class="sxs-lookup"><span data-stu-id="944eb-218">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="944eb-219">Vedere <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="944eb-219">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="944eb-220">Classe Startup e metodi basati sull'ambiente</span><span class="sxs-lookup"><span data-stu-id="944eb-220">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="944eb-221">Inserire IWebHostEnvironment in Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="944eb-221">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="944eb-222">Iniettare <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="944eb-222">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="944eb-223">Questo approccio è utile quando `Startup.Configure` l'app richiede la regolazione solo per alcuni ambienti con differenze di codice minime per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="944eb-223">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="944eb-224">Inserimento di IWebHostEnvironment nella classe StartupInject IWebHostEnvironment into the Startup class</span><span class="sxs-lookup"><span data-stu-id="944eb-224">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="944eb-225">Iniettare <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> nel `Startup` costruttore.</span><span class="sxs-lookup"><span data-stu-id="944eb-225">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="944eb-226">Questo approccio è utile quando `Startup` l'app richiede la configurazione solo per alcuni ambienti con differenze di codice minime per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="944eb-226">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="944eb-227">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="944eb-227">In the following example:</span></span>

* <span data-ttu-id="944eb-228">L'ambiente è `_env` tenuto sul campo.</span><span class="sxs-lookup"><span data-stu-id="944eb-228">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="944eb-229">`_env`viene utilizzato `ConfigureServices` `Configure` in e per applicare la configurazione di avvio in base all'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="944eb-229">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
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
### <a name="startup-class-conventions"></a><span data-ttu-id="944eb-230">Convenzioni delle classi di avvio</span><span class="sxs-lookup"><span data-stu-id="944eb-230">Startup class conventions</span></span>

<span data-ttu-id="944eb-231">Quando viene avviata un'app ASP.NET Core, la [classe Startup](xref:fundamentals/startup) avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="944eb-231">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="944eb-232">L'app può `Startup` definire classi separate per `StartupDevelopment`ambienti diversi, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="944eb-232">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="944eb-233">La `Startup` classe appropriata viene selezionata in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="944eb-233">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="944eb-234">La classe il cui suffisso di nome corrisponde all'ambiente corrente ha la priorità.</span><span class="sxs-lookup"><span data-stu-id="944eb-234">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="944eb-235">Se non viene trovata una classe `Startup{EnvironmentName}` corrispondente, viene usata la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="944eb-235">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="944eb-236">Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="944eb-236">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="944eb-237">Per implementare le classi `Startup` basate sull'ambiente, creare una classe `Startup{EnvironmentName}` per ogni ambiente in uso e una classe `Startup` di fallback:</span><span class="sxs-lookup"><span data-stu-id="944eb-237">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="944eb-238">Usare l'overload [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) che accetta un nome di assembly:</span><span class="sxs-lookup"><span data-stu-id="944eb-238">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="944eb-239">Convenzioni dei metodi di avvio</span><span class="sxs-lookup"><span data-stu-id="944eb-239">Startup method conventions</span></span>

<span data-ttu-id="944eb-240">[Configurare](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) supporta le `Configure<EnvironmentName>` versioni `Configure<EnvironmentName>Services`specifiche dell'ambiente del modulo e .</span><span class="sxs-lookup"><span data-stu-id="944eb-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="944eb-241">Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="944eb-241">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="944eb-242">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="944eb-242">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="944eb-243">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="944eb-243">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="944eb-244">ASP.NET Core configura il comportamento dell'app in base all'ambiente di runtime e tramite una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="944eb-244">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="944eb-245">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="944eb-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="944eb-246">Ambienti</span><span class="sxs-lookup"><span data-stu-id="944eb-246">Environments</span></span>

<span data-ttu-id="944eb-247">ASP.NET Core legge la variabile di ambiente `ASPNETCORE_ENVIRONMENT` all'avvio dell'app e ne archivia il valore in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="944eb-247">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="944eb-248">`ASPNETCORE_ENVIRONMENT`può essere impostato su qualsiasi valore, ma il framework ha fornito tre valori:</span><span class="sxs-lookup"><span data-stu-id="944eb-248">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="944eb-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="944eb-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="944eb-250">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="944eb-250">The preceding code:</span></span>

* <span data-ttu-id="944eb-251">Chiama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` è impostato su `Development`.</span><span class="sxs-lookup"><span data-stu-id="944eb-251">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="944eb-252">Chiama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando il valore di `ASPNETCORE_ENVIRONMENT` è uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="944eb-252">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="944eb-253">L'[helper per tag di ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa il valore di `IHostingEnvironment.EnvironmentName` per includere o escludere il markup nell'elemento:</span><span class="sxs-lookup"><span data-stu-id="944eb-253">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="944eb-254">In Windows e macOS le variabili di ambiente e i relativi valori non applicano la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="944eb-254">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="944eb-255">In Linux le variabili di ambiente e i relativi valori **applicano la distinzione tra maiuscole e minuscole** per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="944eb-255">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="944eb-256">Sviluppo</span><span class="sxs-lookup"><span data-stu-id="944eb-256">Development</span></span>

<span data-ttu-id="944eb-257">L'ambiente di sviluppo può abilitare funzionalità che non devono essere esposte nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="944eb-257">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="944eb-258">Ad esempio i modelli ASP.NET Core abilitano la [pagina delle eccezioni per gli sviluppatori](xref:fundamentals/error-handling#developer-exception-page) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="944eb-258">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="944eb-259">L'ambiente di sviluppo computer locale può essere impostato nel file *Properties\launchSettings.json* del progetto.</span><span class="sxs-lookup"><span data-stu-id="944eb-259">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="944eb-260">I valori di ambiente in *launchSettings.json* sostituiscono i valori impostati nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="944eb-260">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="944eb-261">Il codice JSON seguente visualizza tre profili di un file *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="944eb-261">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="944eb-262">La proprietà `applicationUrl` in *launchSettings.json* può specificare un elenco di URL di server.</span><span class="sxs-lookup"><span data-stu-id="944eb-262">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="944eb-263">Usare un punto e virgola tra gli URL dell'elenco:</span><span class="sxs-lookup"><span data-stu-id="944eb-263">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="944eb-264">Quando l'app viene avviata con [dotnet run](/dotnet/core/tools/dotnet-run) viene usato il primo profilo con `"commandName": "Project"`.</span><span class="sxs-lookup"><span data-stu-id="944eb-264">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="944eb-265">Il valore di `commandName` specifica il server Web da avviare.</span><span class="sxs-lookup"><span data-stu-id="944eb-265">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="944eb-266">`commandName` può avere uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="944eb-266">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="944eb-267">`Project` (che avvia Kestrel)</span><span class="sxs-lookup"><span data-stu-id="944eb-267">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="944eb-268">Quando un'app viene avviata con [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="944eb-268">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="944eb-269">*launchSettings.json*, se disponibile, viene letto.</span><span class="sxs-lookup"><span data-stu-id="944eb-269">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="944eb-270">Le impostazioni `environmentVariables` in *launchSettings.json* sostituiscono le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="944eb-270">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="944eb-271">Viene visualizzato l'ambiente host.</span><span class="sxs-lookup"><span data-stu-id="944eb-271">The hosting environment is displayed.</span></span>

<span data-ttu-id="944eb-272">L'output seguente visualizza un'app avviata con [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="944eb-272">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="944eb-273">La scheda **Debug** nelle proprietà di progetto di Visual Studio visualizza una GUI per la modifica del file *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="944eb-273">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Proprietà progetto - Impostazione delle variabili di ambiente](environments/_static/project-properties-debug.png)

<span data-ttu-id="944eb-275">È possibile che le modifiche apportate ai profili di progetto abbiano effetto solo dopo il riavvio del server Web.</span><span class="sxs-lookup"><span data-stu-id="944eb-275">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="944eb-276">Perché Kestrel rilevi le modifiche apportate al suo ambiente, deve essere riavviato.</span><span class="sxs-lookup"><span data-stu-id="944eb-276">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="944eb-277">Evitare di archiviare segreti in *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="944eb-277">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="944eb-278">Per l'archiviazione di segreti per lo sviluppo locale, usare lo [strumento Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="944eb-278">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="944eb-279">Quando si usa [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="944eb-279">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="944eb-280">Nell'esempio seguente l'ambiente viene impostato su `Development`:</span><span class="sxs-lookup"><span data-stu-id="944eb-280">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="944eb-281">Un file *.vscode/launch.json* del progetto non viene letto quando si avvia l'app con `dotnet run` come accade per il file *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="944eb-281">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="944eb-282">Quando si avvia un'app in un ambiente di sviluppo che non ha un file *launchSettings.json*, impostare una variabile di ambiente o un argomento della riga di comando sul comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="944eb-282">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="944eb-283">Produzione</span><span class="sxs-lookup"><span data-stu-id="944eb-283">Production</span></span>

<span data-ttu-id="944eb-284">È necessario che l'ambiente di produzione sia configurato per ottimizzare la sicurezza, le prestazioni e l'affidabilità dell'app.</span><span class="sxs-lookup"><span data-stu-id="944eb-284">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="944eb-285">Alcune impostazioni comuni che differiscono dallo sviluppo includono:</span><span class="sxs-lookup"><span data-stu-id="944eb-285">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="944eb-286">Memorizzazione nella cache</span><span class="sxs-lookup"><span data-stu-id="944eb-286">Caching.</span></span>
* <span data-ttu-id="944eb-287">Risorse lato client in bundle, minimizzate e potenzialmente offerte da una rete CDN.</span><span class="sxs-lookup"><span data-stu-id="944eb-287">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="944eb-288">Pagine di errore di diagnostica disabilitate.</span><span class="sxs-lookup"><span data-stu-id="944eb-288">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="944eb-289">Pagine di errore descrittive abilitate.</span><span class="sxs-lookup"><span data-stu-id="944eb-289">Friendly error pages enabled.</span></span>
* <span data-ttu-id="944eb-290">Registrazione e monitoraggio della produzione abilitati.</span><span class="sxs-lookup"><span data-stu-id="944eb-290">Production logging and monitoring enabled.</span></span> <span data-ttu-id="944eb-291">Ad esempio, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="944eb-291">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="944eb-292">Impostare l'ambiente</span><span class="sxs-lookup"><span data-stu-id="944eb-292">Set the environment</span></span>

<span data-ttu-id="944eb-293">È spesso utile impostare un ambiente specifico per il test con una variabile di ambiente o un'impostazione della piattaforma.</span><span class="sxs-lookup"><span data-stu-id="944eb-293">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="944eb-294">Se l'ambiente non viene impostato, il valore predefinito è `Production`, che disabilita la maggior parte delle funzionalità di debug.</span><span class="sxs-lookup"><span data-stu-id="944eb-294">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="944eb-295">Il metodo per l'impostazione dell'ambiente dipende dal sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="944eb-295">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="944eb-296">Quando l'host viene compilato, l'ultima impostazione di ambiente letta dall'app determina l'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="944eb-296">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="944eb-297">L'ambiente dell'app non può essere modificato mentre l'app è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="944eb-297">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="944eb-298">Impostazione della variabile di ambiente o della piattaforma</span><span class="sxs-lookup"><span data-stu-id="944eb-298">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="944eb-299">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="944eb-299">Azure App Service</span></span>

<span data-ttu-id="944eb-300">Per impostare l'ambiente in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/), attenersi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="944eb-300">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="944eb-301">Selezionare l'app dal pannello **Servizi app**.</span><span class="sxs-lookup"><span data-stu-id="944eb-301">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="944eb-302">Nel gruppo Impostazioni selezionare il pannello **Configurazione.In** the **Settings** group, select the Configuration blade.</span><span class="sxs-lookup"><span data-stu-id="944eb-302">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="944eb-303">Nella scheda **Impostazioni applicazione** selezionare **Nuova impostazione applicazione**.</span><span class="sxs-lookup"><span data-stu-id="944eb-303">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="944eb-304">Nella finestra **di impostazione Aggiungi/Modifica applicazione** specificare `ASPNETCORE_ENVIRONMENT` il **Nome**.</span><span class="sxs-lookup"><span data-stu-id="944eb-304">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="944eb-305">Per **Valore**, specificare l'ambiente, `Staging`ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="944eb-305">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="944eb-306">Selezionare la casella di controllo **Impostazione slot di distribuzione** se si desidera che l'impostazione dell'ambiente rimanga con lo slot corrente quando gli slot di distribuzione vengono scambiati.</span><span class="sxs-lookup"><span data-stu-id="944eb-306">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="944eb-307">Per altre informazioni, vedere [Configurare gli ambienti](/azure/app-service/web-sites-staged-publishing) di gestione temporanea nel servizio app di Azure nella documentazione di Azure.For more information, see Set up staging environments in Azure App Service in the Azure documentation.</span><span class="sxs-lookup"><span data-stu-id="944eb-307">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="944eb-308">Selezionare **OK** per chiudere la finestra **di impostazione Aggiungi/Modifica applicazione.**</span><span class="sxs-lookup"><span data-stu-id="944eb-308">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="944eb-309">Selezionare Salva nella parte superiore del pannello **Configurazione.Select** **Save** at the top of the Configuration blade.</span><span class="sxs-lookup"><span data-stu-id="944eb-309">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="944eb-310">Servizio app di Azure riavvia automaticamente l'app quando un'impostazione dell'app (una variabile di ambiente) viene aggiunta, modificata o eliminata nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="944eb-310">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="944eb-311">Windows</span><span class="sxs-lookup"><span data-stu-id="944eb-311">Windows</span></span>

<span data-ttu-id="944eb-312">Per impostare la variabile `ASPNETCORE_ENVIRONMENT` per la sessione corrente, se l'app viene avviata tramite [dotnet run](/dotnet/core/tools/dotnet-run), vengono usati i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="944eb-312">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="944eb-313">**Prompt**</span><span class="sxs-lookup"><span data-stu-id="944eb-313">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="944eb-314">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="944eb-314">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="944eb-315">Questi comandi hanno effetto solo per la finestra corrente.</span><span class="sxs-lookup"><span data-stu-id="944eb-315">These commands only take effect for the current window.</span></span> <span data-ttu-id="944eb-316">Quando la finestra viene chiusa, l'impostazione `ASPNETCORE_ENVIRONMENT` viene ripristinata sull'impostazione predefinita o sul valore del computer.</span><span class="sxs-lookup"><span data-stu-id="944eb-316">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="944eb-317">Per impostare il valore a livello globale in Windows, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="944eb-317">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="944eb-318">Scegliere **Pannello di controllo** > **Sistema** > **Impostazioni di sistema avanzate** e aggiungere o modificare il valore `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="944eb-318">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Proprietà di sistema avanzate](environments/_static/systemsetting_environment.png)

  ![Variabile di ambiente ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="944eb-321">Aprire un prompt dei comandi di amministrazione e usare il comando `setx` o aprire un prompt dei comandi di PowerShell di amministrazione e usare `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="944eb-321">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="944eb-322">**Prompt**</span><span class="sxs-lookup"><span data-stu-id="944eb-322">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="944eb-323">L'opzione `/M` indica di impostare la variabile di ambiente a livello del sistema.</span><span class="sxs-lookup"><span data-stu-id="944eb-323">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="944eb-324">Se non viene usata l'opzione `/M`, la variabile di ambiente viene impostata per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="944eb-324">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="944eb-325">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="944eb-325">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="944eb-326">Il valore dell'opzione `Machine` indica di impostare la variabile di ambiente a livello del sistema.</span><span class="sxs-lookup"><span data-stu-id="944eb-326">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="944eb-327">Se non viene usata l'opzione `User`, la variabile di ambiente viene impostata per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="944eb-327">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="944eb-328">Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` è impostata a livello globale, viene applicata per `dotnet run` in tutte le finestre di comando aperte dopo l'impostazione del valore.</span><span class="sxs-lookup"><span data-stu-id="944eb-328">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="944eb-329">**web.config (informazioni in lingua lingua inlingua**</span><span class="sxs-lookup"><span data-stu-id="944eb-329">**web.config**</span></span>

<span data-ttu-id="944eb-330">Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` con *web.config*, vedere la sezione *Impostazione delle variabili di ambiente* di <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="944eb-330">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="944eb-331">**File di progetto o profilo di pubblicazione**</span><span class="sxs-lookup"><span data-stu-id="944eb-331">**Project file or publish profile**</span></span>

<span data-ttu-id="944eb-332">**Per le distribuzioni di Windows IIS:** Includere `<EnvironmentName>` la proprietà nel profilo di [pubblicazione (pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="944eb-332">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="944eb-333">Questo approccio imposta l'ambiente in *web.config* quando viene pubblicato il progetto:</span><span class="sxs-lookup"><span data-stu-id="944eb-333">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="944eb-334">**Pool di applicazioni IIS singoli**</span><span class="sxs-lookup"><span data-stu-id="944eb-334">**Per IIS Application Pool**</span></span>

<span data-ttu-id="944eb-335">Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` per un'app in esecuzione in un pool di applicazioni isolato (supportato in IIS 10.0 o versioni successive), vedere la sezione *AppCmd.exe* dell'argomento [Variabili di ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="944eb-335">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="944eb-336">Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` viene impostata per un pool di app, il suo valore esegue l'override di un'impostazione a livello di sistema.</span><span class="sxs-lookup"><span data-stu-id="944eb-336">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="944eb-337">Durante l'hosting di un'app in IIS, quando si aggiunge o si modifica la variabile di ambiente `ASPNETCORE_ENVIRONMENT`, usare uno degli approcci seguenti per fare in modo che il nuovo valore venga selezionato dalle app:</span><span class="sxs-lookup"><span data-stu-id="944eb-337">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="944eb-338">Eseguire `net stop was /y` seguito da `net start w3svc` da un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="944eb-338">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="944eb-339">Riavviare il server.</span><span class="sxs-lookup"><span data-stu-id="944eb-339">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="944eb-340">macOS</span><span class="sxs-lookup"><span data-stu-id="944eb-340">macOS</span></span>

<span data-ttu-id="944eb-341">L'impostazione dell'ambiente corrente per macOS può essere eseguita in linea durante l'esecuzione dell'app:</span><span class="sxs-lookup"><span data-stu-id="944eb-341">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="944eb-342">In alternativa, impostare l'ambiente tramite `export` prima di eseguire l'app:</span><span class="sxs-lookup"><span data-stu-id="944eb-342">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="944eb-343">Le variabili di ambiente a livello computer sono impostate nel file con estensione *bashrc* o *bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="944eb-343">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="944eb-344">Modificare il file usando un qualsiasi editor di testo.</span><span class="sxs-lookup"><span data-stu-id="944eb-344">Edit the file using any text editor.</span></span> <span data-ttu-id="944eb-345">Aggiungere l'istruzione seguente:</span><span class="sxs-lookup"><span data-stu-id="944eb-345">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="944eb-346">Linux</span><span class="sxs-lookup"><span data-stu-id="944eb-346">Linux</span></span>

<span data-ttu-id="944eb-347">Per le distribuzioni di Linux, usare il comando `export` al prompt dei comandi per le impostazioni delle variabili basate sulla sessione e il file *bash_profile* per le impostazioni di ambiente a livello di computer.</span><span class="sxs-lookup"><span data-stu-id="944eb-347">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="944eb-348">Impostare l'ambiente nel codiceSet the environment in code</span><span class="sxs-lookup"><span data-stu-id="944eb-348">Set the environment in code</span></span>

<span data-ttu-id="944eb-349">Chiama <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> durante la creazione dell'host.</span><span class="sxs-lookup"><span data-stu-id="944eb-349">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="944eb-350">Vedere <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="944eb-350">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="944eb-351">Configurazione per ambiente</span><span class="sxs-lookup"><span data-stu-id="944eb-351">Configuration by environment</span></span>

<span data-ttu-id="944eb-352">Per caricare la configurazione dall'ambiente, sono consigliabili:</span><span class="sxs-lookup"><span data-stu-id="944eb-352">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="944eb-353">*file appsettings* (*appsettings. Ambiente: .json*).</span><span class="sxs-lookup"><span data-stu-id="944eb-353">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="944eb-354">Vedere <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="944eb-354">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="944eb-355">Variabili di ambiente (impostate in ogni sistema in cui è ospitata l'app).</span><span class="sxs-lookup"><span data-stu-id="944eb-355">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="944eb-356">Controllare <xref:fundamentals/host/web-host#environment> e <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="944eb-356">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="944eb-357">Secret Manager (solo nell'ambiente di sviluppo).</span><span class="sxs-lookup"><span data-stu-id="944eb-357">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="944eb-358">Vedere <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="944eb-358">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="944eb-359">Classe Startup e metodi basati sull'ambiente</span><span class="sxs-lookup"><span data-stu-id="944eb-359">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="944eb-360">Inserire IHostingEnvironment in Startup.ConfigureInject IHostingEnvironment into Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="944eb-360">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="944eb-361">Iniettare <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="944eb-361">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="944eb-362">Questo approccio è utile quando `Startup.Configure` l'app richiede solo la configurazione solo per pochi ambienti con differenze di codice minime per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="944eb-362">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="944eb-363">Inserimento di IHostingEnvironment nella classe StartupInject IHostingEnvironment into the Startup class</span><span class="sxs-lookup"><span data-stu-id="944eb-363">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="944eb-364">Inserirlo <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> nel `Startup` costruttore e assegnare `Startup` il servizio a un campo da utilizzare in tutta la classe.</span><span class="sxs-lookup"><span data-stu-id="944eb-364">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="944eb-365">Questo approccio è utile quando l'app richiede la configurazione dell'avvio solo per alcuni ambienti con differenze di codice minime per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="944eb-365">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="944eb-366">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="944eb-366">In the following example:</span></span>

* <span data-ttu-id="944eb-367">L'ambiente è `_env` tenuto sul campo.</span><span class="sxs-lookup"><span data-stu-id="944eb-367">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="944eb-368">`_env`viene utilizzato `ConfigureServices` `Configure` in e per applicare la configurazione di avvio in base all'ambiente dell'app.</span><span class="sxs-lookup"><span data-stu-id="944eb-368">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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

### <a name="startup-class-conventions"></a><span data-ttu-id="944eb-369">Convenzioni delle classi di avvio</span><span class="sxs-lookup"><span data-stu-id="944eb-369">Startup class conventions</span></span>

<span data-ttu-id="944eb-370">Quando viene avviata un'app ASP.NET Core, la [classe Startup](xref:fundamentals/startup) avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="944eb-370">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="944eb-371">L'app può `Startup` definire classi separate per `StartupDevelopment`ambienti diversi, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="944eb-371">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="944eb-372">La `Startup` classe appropriata viene selezionata in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="944eb-372">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="944eb-373">La classe il cui suffisso di nome corrisponde all'ambiente corrente ha la priorità.</span><span class="sxs-lookup"><span data-stu-id="944eb-373">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="944eb-374">Se non viene trovata una classe `Startup{EnvironmentName}` corrispondente, viene usata la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="944eb-374">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="944eb-375">Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="944eb-375">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="944eb-376">Per implementare le classi `Startup` basate sull'ambiente, creare una classe `Startup{EnvironmentName}` per ogni ambiente in uso e una classe `Startup` di fallback:</span><span class="sxs-lookup"><span data-stu-id="944eb-376">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="944eb-377">Usare l'overload [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) che accetta un nome di assembly:</span><span class="sxs-lookup"><span data-stu-id="944eb-377">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="944eb-378">Convenzioni dei metodi di avvio</span><span class="sxs-lookup"><span data-stu-id="944eb-378">Startup method conventions</span></span>

<span data-ttu-id="944eb-379">[Configurare](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) supporta le `Configure<EnvironmentName>` versioni `Configure<EnvironmentName>Services`specifiche dell'ambiente del modulo e .</span><span class="sxs-lookup"><span data-stu-id="944eb-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="944eb-380">Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.</span><span class="sxs-lookup"><span data-stu-id="944eb-380">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="944eb-381">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="944eb-381">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
