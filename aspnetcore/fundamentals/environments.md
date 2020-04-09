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
# <a name="use-multiple-environments-in-aspnet-core"></a>Usare più ambienti in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core configura il comportamento dell'app in base all'ambiente di runtime e tramite una variabile di ambiente.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ambienti

ASP.NET Core legge la `ASPNETCORE_ENVIRONMENT` variabile di ambiente all'avvio dell'app e archivia il valore in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`può essere impostato su qualsiasi valore, ma il framework ha fornito tre valori:

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production> (impostazione predefinita)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Il codice precedente:

* Chiama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` è impostato su `Development`.
* Chiama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando il valore di `ASPNETCORE_ENVIRONMENT` è uno dei seguenti:

  * `Staging`
  * `Production`
  * `Staging_2`

L'[helper per tag di ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa il valore di `IHostingEnvironment.EnvironmentName` per includere o escludere il markup nell'elemento:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

In Windows e macOS le variabili di ambiente e i relativi valori non applicano la distinzione tra maiuscole e minuscole. In Linux le variabili di ambiente e i relativi valori **applicano la distinzione tra maiuscole e minuscole** per impostazione predefinita.

### <a name="development"></a>Sviluppo

L'ambiente di sviluppo può abilitare funzionalità che non devono essere esposte nell'ambiente di produzione. Ad esempio i modelli ASP.NET Core abilitano la [pagina delle eccezioni per gli sviluppatori](xref:fundamentals/error-handling#developer-exception-page) nell'ambiente di sviluppo.

L'ambiente di sviluppo computer locale può essere impostato nel file *Properties\launchSettings.json* del progetto. I valori di ambiente in *launchSettings.json* sostituiscono i valori impostati nell'ambiente di sistema.

Il codice JSON seguente visualizza tre profili di un file *launchSettings.json*:

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
> La proprietà `applicationUrl` in *launchSettings.json* può specificare un elenco di URL di server. Usare un punto e virgola tra gli URL dell'elenco:
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

Quando l'app viene avviata con [dotnet run](/dotnet/core/tools/dotnet-run) viene usato il primo profilo con `"commandName": "Project"`. Il valore di `commandName` specifica il server Web da avviare. `commandName` può avere uno dei valori seguenti:

* `IISExpress`
* `IIS`
* `Project` (che avvia Kestrel)

Quando un'app viene avviata con [dotnet run](/dotnet/core/tools/dotnet-run):

* *launchSettings.json*, se disponibile, viene letto. Le impostazioni `environmentVariables` in *launchSettings.json* sostituiscono le variabili di ambiente.
* Viene visualizzato l'ambiente host.

L'output seguente visualizza un'app avviata con [dotnet run](/dotnet/core/tools/dotnet-run):

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

La scheda **Debug** nelle proprietà di progetto di Visual Studio visualizza una GUI per la modifica del file *launchSettings.json*:

![Proprietà progetto - Impostazione delle variabili di ambiente](environments/_static/project-properties-debug.png)

È possibile che le modifiche apportate ai profili di progetto abbiano effetto solo dopo il riavvio del server Web. Perché Kestrel rilevi le modifiche apportate al suo ambiente, deve essere riavviato.

> [!WARNING]
> Evitare di archiviare segreti in *launchSettings.json*. Per l'archiviazione di segreti per lo sviluppo locale, usare lo [strumento Secret Manager](xref:security/app-secrets).

Quando si usa [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*. Nell'esempio seguente l'ambiente viene impostato su `Development`:

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

Un file *.vscode/launch.json* del progetto non viene letto quando si avvia l'app con `dotnet run` come accade per il file *Properties/launchSettings.json*. Quando si avvia un'app in un ambiente di sviluppo che non ha un file *launchSettings.json*, impostare una variabile di ambiente o un argomento della riga di comando sul comando `dotnet run`.

### <a name="production"></a>Produzione

È necessario che l'ambiente di produzione sia configurato per ottimizzare la sicurezza, le prestazioni e l'affidabilità dell'app. Alcune impostazioni comuni che differiscono dallo sviluppo includono:

* Memorizzazione nella cache
* Risorse lato client in bundle, minimizzate e potenzialmente offerte da una rete CDN.
* Pagine di errore di diagnostica disabilitate.
* Pagine di errore descrittive abilitate.
* Registrazione e monitoraggio della produzione abilitati. Ad esempio, [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Impostare l'ambiente

È spesso utile impostare un ambiente specifico per il test con una variabile di ambiente o un'impostazione della piattaforma. Se l'ambiente non viene impostato, il valore predefinito è `Production`, che disabilita la maggior parte delle funzionalità di debug. Il metodo per l'impostazione dell'ambiente dipende dal sistema operativo.

Quando l'host viene compilato, l'ultima impostazione di ambiente letta dall'app determina l'ambiente dell'app. L'ambiente dell'app non può essere modificato mentre l'app è in esecuzione.

### <a name="environment-variable-or-platform-setting"></a>Impostazione della variabile di ambiente o della piattaforma

#### <a name="azure-app-service"></a>Servizio app di Azure

Per impostare l'ambiente in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/), attenersi alla procedura seguente:

1. Selezionare l'app dal pannello **Servizi app**.
1. Nel gruppo Impostazioni selezionare il pannello **Configurazione.In** the **Settings** group, select the Configuration blade.
1. Nella scheda **Impostazioni applicazione** selezionare **Nuova impostazione applicazione**.
1. Nella finestra **di impostazione Aggiungi/Modifica applicazione** specificare `ASPNETCORE_ENVIRONMENT` il **Nome**. Per **Valore**, specificare l'ambiente, `Staging`ad esempio ).
1. Selezionare la casella di controllo **Impostazione slot di distribuzione** se si desidera che l'impostazione dell'ambiente rimanga con lo slot corrente quando gli slot di distribuzione vengono scambiati. Per altre informazioni, vedere [Configurare gli ambienti](/azure/app-service/web-sites-staged-publishing) di gestione temporanea nel servizio app di Azure nella documentazione di Azure.For more information, see Set up staging environments in Azure App Service in the Azure documentation.
1. Selezionare **OK** per chiudere la finestra **di impostazione Aggiungi/Modifica applicazione.**
1. Selezionare Salva nella parte superiore del pannello **Configurazione.Select** **Save** at the top of the Configuration blade.

Servizio app di Azure riavvia automaticamente l'app quando un'impostazione dell'app (una variabile di ambiente) viene aggiunta, modificata o eliminata nel portale di Azure.

#### <a name="windows"></a>Windows

Per impostare la variabile `ASPNETCORE_ENVIRONMENT` per la sessione corrente, se l'app viene avviata tramite [dotnet run](/dotnet/core/tools/dotnet-run), vengono usati i comandi seguenti:

**Prompt**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Questi comandi hanno effetto solo per la finestra corrente. Quando la finestra viene chiusa, l'impostazione `ASPNETCORE_ENVIRONMENT` viene ripristinata sull'impostazione predefinita o sul valore del computer.

Per impostare il valore a livello globale in Windows, usare uno degli approcci seguenti:

* Scegliere **Pannello di controllo** > **Sistema** > **Impostazioni di sistema avanzate** e aggiungere o modificare il valore `ASPNETCORE_ENVIRONMENT`:

  ![Proprietà di sistema avanzate](environments/_static/systemsetting_environment.png)

  ![Variabile di ambiente ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Aprire un prompt dei comandi di amministrazione e usare il comando `setx` o aprire un prompt dei comandi di PowerShell di amministrazione e usare `[Environment]::SetEnvironmentVariable`:

  **Prompt**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  L'opzione `/M` indica di impostare la variabile di ambiente a livello del sistema. Se non viene usata l'opzione `/M`, la variabile di ambiente viene impostata per l'account utente.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  Il valore dell'opzione `Machine` indica di impostare la variabile di ambiente a livello del sistema. Se non viene usata l'opzione `User`, la variabile di ambiente viene impostata per l'account utente.

Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` è impostata a livello globale, viene applicata per `dotnet run` in tutte le finestre di comando aperte dopo l'impostazione del valore.

**web.config (informazioni in lingua lingua inlingua**

Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` con *web.config*, vedere la sezione *Impostazione delle variabili di ambiente* di <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**File di progetto o profilo di pubblicazione**

**Per le distribuzioni di Windows IIS:** Includere `<EnvironmentName>` la proprietà nel profilo di [pubblicazione (pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o nel file di progetto. Questo approccio imposta l'ambiente in *web.config* quando viene pubblicato il progetto:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Pool di applicazioni IIS singoli**

Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` per un'app in esecuzione in un pool di applicazioni isolato (supportato in IIS 10.0 o versioni successive), vedere la sezione *AppCmd.exe* dell'argomento [Variabili di ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` viene impostata per un pool di app, il suo valore esegue l'override di un'impostazione a livello di sistema.

> [!IMPORTANT]
> Durante l'hosting di un'app in IIS, quando si aggiunge o si modifica la variabile di ambiente `ASPNETCORE_ENVIRONMENT`, usare uno degli approcci seguenti per fare in modo che il nuovo valore venga selezionato dalle app:
>
> * Eseguire `net stop was /y` seguito da `net start w3svc` da un prompt dei comandi.
> * Riavviare il server.

#### <a name="macos"></a>macOS

L'impostazione dell'ambiente corrente per macOS può essere eseguita in linea durante l'esecuzione dell'app:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

In alternativa, impostare l'ambiente tramite `export` prima di eseguire l'app:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Le variabili di ambiente a livello computer sono impostate nel file con estensione *bashrc* o *bash_profile*. Modificare il file usando un qualsiasi editor di testo. Aggiungere l'istruzione seguente:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Per le distribuzioni di Linux, usare il comando `export` al prompt dei comandi per le impostazioni delle variabili basate sulla sessione e il file *bash_profile* per le impostazioni di ambiente a livello di computer.

### <a name="set-the-environment-in-code"></a>Impostare l'ambiente nel codiceSet the environment in code

Chiama <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> durante la creazione dell'host. Vedere <xref:fundamentals/host/generic-host#environmentname>.


### <a name="configuration-by-environment"></a>Configurazione per ambiente

Per caricare la configurazione dall'ambiente, sono consigliabili:

* *file appsettings* (*appsettings. Ambiente: .json*). Vedere <xref:fundamentals/configuration/index#json-configuration-provider>.
* Variabili di ambiente (impostate in ogni sistema in cui è ospitata l'app). Controllare <xref:fundamentals/host/generic-host#environmentname> e <xref:security/app-secrets#environment-variables>.
* Secret Manager (solo nell'ambiente di sviluppo). Vedere <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Classe Startup e metodi basati sull'ambiente

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a>Inserire IWebHostEnvironment in Startup.Configure

Iniettare <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup.Configure`. Questo approccio è utile quando `Startup.Configure` l'app richiede la regolazione solo per alcuni ambienti con differenze di codice minime per ogni ambiente.

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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Inserimento di IWebHostEnvironment nella classe StartupInject IWebHostEnvironment into the Startup class

Iniettare <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> nel `Startup` costruttore. Questo approccio è utile quando `Startup` l'app richiede la configurazione solo per alcuni ambienti con differenze di codice minime per ogni ambiente.

Nell'esempio seguente:

* L'ambiente è `_env` tenuto sul campo.
* `_env`viene utilizzato `ConfigureServices` `Configure` in e per applicare la configurazione di avvio in base all'ambiente dell'app.

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
### <a name="startup-class-conventions"></a>Convenzioni delle classi di avvio

Quando viene avviata un'app ASP.NET Core, la [classe Startup](xref:fundamentals/startup) avvia l'app. L'app può `Startup` definire classi separate per `StartupDevelopment`ambienti diversi, ad esempio ). La `Startup` classe appropriata viene selezionata in fase di esecuzione. La classe il cui suffisso di nome corrisponde all'ambiente corrente ha la priorità. Se non viene trovata una classe `Startup{EnvironmentName}` corrispondente, viene usata la classe `Startup`. Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.

Per implementare le classi `Startup` basate sull'ambiente, creare una classe `Startup{EnvironmentName}` per ogni ambiente in uso e una classe `Startup` di fallback:

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

Usare l'overload [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) che accetta un nome di assembly:

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

### <a name="startup-method-conventions"></a>Convenzioni dei metodi di avvio

[Configurare](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) supporta le `Configure<EnvironmentName>` versioni `Configure<EnvironmentName>Services`specifiche dell'ambiente del modulo e . Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core configura il comportamento dell'app in base all'ambiente di runtime e tramite una variabile di ambiente.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ambienti

ASP.NET Core legge la variabile di ambiente `ASPNETCORE_ENVIRONMENT` all'avvio dell'app e ne archivia il valore in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`può essere impostato su qualsiasi valore, ma il framework ha fornito tre valori:

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (impostazione predefinita)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Il codice precedente:

* Chiama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` è impostato su `Development`.
* Chiama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando il valore di `ASPNETCORE_ENVIRONMENT` è uno dei seguenti:

  * `Staging`
  * `Production`
  * `Staging_2`

L'[helper per tag di ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa il valore di `IHostingEnvironment.EnvironmentName` per includere o escludere il markup nell'elemento:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

In Windows e macOS le variabili di ambiente e i relativi valori non applicano la distinzione tra maiuscole e minuscole. In Linux le variabili di ambiente e i relativi valori **applicano la distinzione tra maiuscole e minuscole** per impostazione predefinita.

### <a name="development"></a>Sviluppo

L'ambiente di sviluppo può abilitare funzionalità che non devono essere esposte nell'ambiente di produzione. Ad esempio i modelli ASP.NET Core abilitano la [pagina delle eccezioni per gli sviluppatori](xref:fundamentals/error-handling#developer-exception-page) nell'ambiente di sviluppo.

L'ambiente di sviluppo computer locale può essere impostato nel file *Properties\launchSettings.json* del progetto. I valori di ambiente in *launchSettings.json* sostituiscono i valori impostati nell'ambiente di sistema.

Il codice JSON seguente visualizza tre profili di un file *launchSettings.json*:

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
> La proprietà `applicationUrl` in *launchSettings.json* può specificare un elenco di URL di server. Usare un punto e virgola tra gli URL dell'elenco:
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

Quando l'app viene avviata con [dotnet run](/dotnet/core/tools/dotnet-run) viene usato il primo profilo con `"commandName": "Project"`. Il valore di `commandName` specifica il server Web da avviare. `commandName` può avere uno dei valori seguenti:

* `IISExpress`
* `IIS`
* `Project` (che avvia Kestrel)

Quando un'app viene avviata con [dotnet run](/dotnet/core/tools/dotnet-run):

* *launchSettings.json*, se disponibile, viene letto. Le impostazioni `environmentVariables` in *launchSettings.json* sostituiscono le variabili di ambiente.
* Viene visualizzato l'ambiente host.

L'output seguente visualizza un'app avviata con [dotnet run](/dotnet/core/tools/dotnet-run):

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

La scheda **Debug** nelle proprietà di progetto di Visual Studio visualizza una GUI per la modifica del file *launchSettings.json*:

![Proprietà progetto - Impostazione delle variabili di ambiente](environments/_static/project-properties-debug.png)

È possibile che le modifiche apportate ai profili di progetto abbiano effetto solo dopo il riavvio del server Web. Perché Kestrel rilevi le modifiche apportate al suo ambiente, deve essere riavviato.

> [!WARNING]
> Evitare di archiviare segreti in *launchSettings.json*. Per l'archiviazione di segreti per lo sviluppo locale, usare lo [strumento Secret Manager](xref:security/app-secrets).

Quando si usa [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*. Nell'esempio seguente l'ambiente viene impostato su `Development`:

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

Un file *.vscode/launch.json* del progetto non viene letto quando si avvia l'app con `dotnet run` come accade per il file *Properties/launchSettings.json*. Quando si avvia un'app in un ambiente di sviluppo che non ha un file *launchSettings.json*, impostare una variabile di ambiente o un argomento della riga di comando sul comando `dotnet run`.

### <a name="production"></a>Produzione

È necessario che l'ambiente di produzione sia configurato per ottimizzare la sicurezza, le prestazioni e l'affidabilità dell'app. Alcune impostazioni comuni che differiscono dallo sviluppo includono:

* Memorizzazione nella cache
* Risorse lato client in bundle, minimizzate e potenzialmente offerte da una rete CDN.
* Pagine di errore di diagnostica disabilitate.
* Pagine di errore descrittive abilitate.
* Registrazione e monitoraggio della produzione abilitati. Ad esempio, [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Impostare l'ambiente

È spesso utile impostare un ambiente specifico per il test con una variabile di ambiente o un'impostazione della piattaforma. Se l'ambiente non viene impostato, il valore predefinito è `Production`, che disabilita la maggior parte delle funzionalità di debug. Il metodo per l'impostazione dell'ambiente dipende dal sistema operativo.

Quando l'host viene compilato, l'ultima impostazione di ambiente letta dall'app determina l'ambiente dell'app. L'ambiente dell'app non può essere modificato mentre l'app è in esecuzione.

### <a name="environment-variable-or-platform-setting"></a>Impostazione della variabile di ambiente o della piattaforma

#### <a name="azure-app-service"></a>Servizio app di Azure

Per impostare l'ambiente in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/), attenersi alla procedura seguente:

1. Selezionare l'app dal pannello **Servizi app**.
1. Nel gruppo Impostazioni selezionare il pannello **Configurazione.In** the **Settings** group, select the Configuration blade.
1. Nella scheda **Impostazioni applicazione** selezionare **Nuova impostazione applicazione**.
1. Nella finestra **di impostazione Aggiungi/Modifica applicazione** specificare `ASPNETCORE_ENVIRONMENT` il **Nome**. Per **Valore**, specificare l'ambiente, `Staging`ad esempio ).
1. Selezionare la casella di controllo **Impostazione slot di distribuzione** se si desidera che l'impostazione dell'ambiente rimanga con lo slot corrente quando gli slot di distribuzione vengono scambiati. Per altre informazioni, vedere [Configurare gli ambienti](/azure/app-service/web-sites-staged-publishing) di gestione temporanea nel servizio app di Azure nella documentazione di Azure.For more information, see Set up staging environments in Azure App Service in the Azure documentation.
1. Selezionare **OK** per chiudere la finestra **di impostazione Aggiungi/Modifica applicazione.**
1. Selezionare Salva nella parte superiore del pannello **Configurazione.Select** **Save** at the top of the Configuration blade.

Servizio app di Azure riavvia automaticamente l'app quando un'impostazione dell'app (una variabile di ambiente) viene aggiunta, modificata o eliminata nel portale di Azure.

#### <a name="windows"></a>Windows

Per impostare la variabile `ASPNETCORE_ENVIRONMENT` per la sessione corrente, se l'app viene avviata tramite [dotnet run](/dotnet/core/tools/dotnet-run), vengono usati i comandi seguenti:

**Prompt**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Questi comandi hanno effetto solo per la finestra corrente. Quando la finestra viene chiusa, l'impostazione `ASPNETCORE_ENVIRONMENT` viene ripristinata sull'impostazione predefinita o sul valore del computer.

Per impostare il valore a livello globale in Windows, usare uno degli approcci seguenti:

* Scegliere **Pannello di controllo** > **Sistema** > **Impostazioni di sistema avanzate** e aggiungere o modificare il valore `ASPNETCORE_ENVIRONMENT`:

  ![Proprietà di sistema avanzate](environments/_static/systemsetting_environment.png)

  ![Variabile di ambiente ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Aprire un prompt dei comandi di amministrazione e usare il comando `setx` o aprire un prompt dei comandi di PowerShell di amministrazione e usare `[Environment]::SetEnvironmentVariable`:

  **Prompt**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  L'opzione `/M` indica di impostare la variabile di ambiente a livello del sistema. Se non viene usata l'opzione `/M`, la variabile di ambiente viene impostata per l'account utente.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  Il valore dell'opzione `Machine` indica di impostare la variabile di ambiente a livello del sistema. Se non viene usata l'opzione `User`, la variabile di ambiente viene impostata per l'account utente.

Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` è impostata a livello globale, viene applicata per `dotnet run` in tutte le finestre di comando aperte dopo l'impostazione del valore.

**web.config (informazioni in lingua lingua inlingua**

Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` con *web.config*, vedere la sezione *Impostazione delle variabili di ambiente* di <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**File di progetto o profilo di pubblicazione**

**Per le distribuzioni di Windows IIS:** Includere `<EnvironmentName>` la proprietà nel profilo di [pubblicazione (pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o nel file di progetto. Questo approccio imposta l'ambiente in *web.config* quando viene pubblicato il progetto:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Pool di applicazioni IIS singoli**

Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` per un'app in esecuzione in un pool di applicazioni isolato (supportato in IIS 10.0 o versioni successive), vedere la sezione *AppCmd.exe* dell'argomento [Variabili di ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` viene impostata per un pool di app, il suo valore esegue l'override di un'impostazione a livello di sistema.

> [!IMPORTANT]
> Durante l'hosting di un'app in IIS, quando si aggiunge o si modifica la variabile di ambiente `ASPNETCORE_ENVIRONMENT`, usare uno degli approcci seguenti per fare in modo che il nuovo valore venga selezionato dalle app:
>
> * Eseguire `net stop was /y` seguito da `net start w3svc` da un prompt dei comandi.
> * Riavviare il server.

#### <a name="macos"></a>macOS

L'impostazione dell'ambiente corrente per macOS può essere eseguita in linea durante l'esecuzione dell'app:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

In alternativa, impostare l'ambiente tramite `export` prima di eseguire l'app:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Le variabili di ambiente a livello computer sono impostate nel file con estensione *bashrc* o *bash_profile*. Modificare il file usando un qualsiasi editor di testo. Aggiungere l'istruzione seguente:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Per le distribuzioni di Linux, usare il comando `export` al prompt dei comandi per le impostazioni delle variabili basate sulla sessione e il file *bash_profile* per le impostazioni di ambiente a livello di computer.

### <a name="set-the-environment-in-code"></a>Impostare l'ambiente nel codiceSet the environment in code

Chiama <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> durante la creazione dell'host. Vedere <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Configurazione per ambiente

Per caricare la configurazione dall'ambiente, sono consigliabili:

* *file appsettings* (*appsettings. Ambiente: .json*). Vedere <xref:fundamentals/configuration/index#json-configuration-provider>.
* Variabili di ambiente (impostate in ogni sistema in cui è ospitata l'app). Controllare <xref:fundamentals/host/web-host#environment> e <xref:security/app-secrets#environment-variables>.
* Secret Manager (solo nell'ambiente di sviluppo). Vedere <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Classe Startup e metodi basati sull'ambiente

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Inserire IHostingEnvironment in Startup.ConfigureInject IHostingEnvironment into Startup.Configure

Iniettare <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> in `Startup.Configure`. Questo approccio è utile quando `Startup.Configure` l'app richiede solo la configurazione solo per pochi ambienti con differenze di codice minime per ogni ambiente.

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>Inserimento di IHostingEnvironment nella classe StartupInject IHostingEnvironment into the Startup class

Inserirlo <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> nel `Startup` costruttore e assegnare `Startup` il servizio a un campo da utilizzare in tutta la classe. Questo approccio è utile quando l'app richiede la configurazione dell'avvio solo per alcuni ambienti con differenze di codice minime per ogni ambiente.

Nell'esempio seguente:

* L'ambiente è `_env` tenuto sul campo.
* `_env`viene utilizzato `ConfigureServices` `Configure` in e per applicare la configurazione di avvio in base all'ambiente dell'app.

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

### <a name="startup-class-conventions"></a>Convenzioni delle classi di avvio

Quando viene avviata un'app ASP.NET Core, la [classe Startup](xref:fundamentals/startup) avvia l'app. L'app può `Startup` definire classi separate per `StartupDevelopment`ambienti diversi, ad esempio ). La `Startup` classe appropriata viene selezionata in fase di esecuzione. La classe il cui suffisso di nome corrisponde all'ambiente corrente ha la priorità. Se non viene trovata una classe `Startup{EnvironmentName}` corrispondente, viene usata la classe `Startup`. Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.

Per implementare le classi `Startup` basate sull'ambiente, creare una classe `Startup{EnvironmentName}` per ogni ambiente in uso e una classe `Startup` di fallback:

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

Usare l'overload [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) che accetta un nome di assembly:

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

### <a name="startup-method-conventions"></a>Convenzioni dei metodi di avvio

[Configurare](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) supporta le `Configure<EnvironmentName>` versioni `Configure<EnvironmentName>Services`specifiche dell'ambiente del modulo e . Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
