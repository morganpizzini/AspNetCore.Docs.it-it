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
# <a name="use-multiple-environments-in-aspnet-core"></a>Usare più ambienti in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)

ASP.NET Core configura il comportamento dell'app in base all'ambiente di runtime e tramite una variabile di ambiente.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ambienti

Per determinare l'ambiente di runtime, ASP.NET Core legge le variabili di ambiente seguenti:

1. [DOTNET_ENVIRONMENT](xref:fundamentals/configuration/index#default-host-configuration)
1. `ASPNETCORE_ENVIRONMENT`Quando <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> viene chiamato il metodo. La chiamata predefinita per i modelli di app Web ASP.NET Core `ConfigureWebHostDefaults` . Il `ASPNETCORE_ENVIRONMENT` valore esegue l'override di `DOTNET_ENVIRONMENT` .

`IHostEnvironment.EnvironmentName`può essere impostato su qualsiasi valore, ma i valori seguenti vengono forniti dal Framework:

* <xref:Microsoft.Extensions.Hosting.Environments.Development>: Il [launchSettings.jsnei](#lsj) set di file nel `ASPNETCORE_ENVIRONMENT` `Development` computer locale.
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production>: Valore predefinito se `DOTNET_ENVIRONMENT` e `ASPNETCORE_ENVIRONMENT` non sono stati impostati.

Il codice seguente:

* Chiama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` è impostato su `Development`.
* Chiama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando il valore di `ASPNETCORE_ENVIRONMENT` è impostato su `Staging` , `Production` o `Staging_2` .
* Inserisce <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup.Configure` . Questo approccio è utile quando l'app richiede solo la regolazione `Startup.Configure` per alcuni ambienti con differenze minime di codice per ogni ambiente.
* È simile al codice generato dai modelli di ASP.NET Core.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

L' [Helper tag di ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa il valore di [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) per includere o escludere il markup nell'elemento:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

La [pagina about](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) del [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) include il markup precedente e visualizza il valore di `IWebHostEnvironment.EnvironmentName` .

In Windows e macOS, le variabili di ambiente e i valori non fanno distinzione tra maiuscole e minuscole. Le variabili e i valori di ambiente Linux fanno **distinzione tra maiuscole** e minuscole per impostazione predefinita

### <a name="create-environmentssample"></a>Crea EnvironmentsSample

Il [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) usato in questo documento si basa su un Razor progetto di pagine denominato *EnvironmentsSample*.

Il codice seguente crea ed esegue un'app Web denominata *EnvironmentsSample*:

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

Quando viene eseguita l'app, viene visualizzato il seguente output:

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

### <a name="development-and-launchsettingsjson"></a>Sviluppo e launchSettings.js

L'ambiente di sviluppo può abilitare funzionalità che non devono essere esposte nell'ambiente di produzione. Ad esempio i modelli ASP.NET Core abilitano la [pagina delle eccezioni per gli sviluppatori](xref:fundamentals/error-handling#developer-exception-page) nell'ambiente di sviluppo.

L'ambiente di sviluppo computer locale può essere impostato nel file *Properties\launchSettings.json* del progetto. I valori di ambiente in *launchSettings.json* sostituiscono i valori impostati nell'ambiente di sistema.

Il *launchSettings.jsnel* file:

* Viene usato solo nel computer di sviluppo locale.
* Non è distribuito.
* contiene le impostazioni del profilo.

Il codice JSON seguente mostra il *launchSettings.jssu* file per un ASP.NET Core progetto Web denominato *EnvironmentsSample* creato con Visual Studio o `dotnet new` :

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

Il markup precedente contiene due profili:

* `IIS Express`: Profilo predefinito usato quando si avvia l'app da Visual Studio. Il `"commandName"` valore della chiave è `"IISExpress"` , pertanto, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) è il server Web. È possibile impostare il profilo di avvio sul progetto o su qualsiasi altro profilo incluso. Nell'immagine seguente, ad esempio, se si seleziona il nome del progetto viene avviato il [server Web gheppio](xref:fundamentals/servers/kestrel).

  ![Menu di avvio IIS Express](environments/_static/iisx2.png)
* `EnvironmentsSample`: Il nome del profilo è il nome del progetto. Questo profilo viene usato per impostazione predefinita quando si avvia l'app con `dotnet run` .  Il `"commandName"` valore della chiave è `"Project"` , pertanto, viene avviato il [server Web gheppio](xref:fundamentals/servers/kestrel) .

Il valore di `commandName` può specificare il server Web da avviare. `commandName` può avere uno dei valori seguenti:

* `IISExpress`: Avvia IIS Express.
* `IIS`: Non è stato avviato alcun server Web. È previsto che IIS sia disponibile.
* `Project`: Avvia gheppio.

La scheda **debug** delle proprietà del progetto di Visual Studio fornisce un'interfaccia utente grafica per modificare il *launchSettings.jssu* file. È possibile che le modifiche apportate ai profili di progetto abbiano effetto solo dopo il riavvio del server Web. Perché Kestrel rilevi le modifiche apportate al suo ambiente, deve essere riavviato.

![Proprietà progetto - Impostazione delle variabili di ambiente](environments/_static/project-properties-debug.png)

Il *launchSettings.jsseguente nel* file contiene più profili:

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

È possibile selezionare i profili:

* Dall'interfaccia utente di Visual Studio.
* Utilizzando il [`dotnet run`](/dotnet/core/tools/dotnet-run) comando in una shell dei comandi con l' `--launch-profile` opzione impostata sul nome del profilo. *Questo approccio supporta solo profili gheppio.*

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> Evitare di archiviare segreti in *launchSettings.json*. Per l'archiviazione di segreti per lo sviluppo locale, usare lo [strumento Secret Manager](xref:security/app-secrets).

Quando si usa [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*. Nell'esempio seguente vengono impostate diverse [variabili di ambiente per i valori di configurazione host](xref:fundamentals/host/web-host#host-configuration-values):

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

Il file *con estensione VSCODE/launch.js* viene utilizzato solo da Visual Studio Code.

### <a name="production"></a>Produzione

L'ambiente di produzione deve essere configurato per ottimizzare la sicurezza, le prestazioni e l'affidabilità [delle](xref:performance/performance-best-practices)applicazioni. Alcune impostazioni comuni che differiscono dallo sviluppo includono:

* [Memorizzazione nella cache](xref:performance/caching/memory).
* Risorse lato client in bundle, minimizzate e potenzialmente offerte da una rete CDN.
* Pagine di errore di diagnostica disabilitate.
* Pagine di errore descrittive abilitate.
* [Registrazione](xref:fundamentals/logging/index) e monitoraggio di produzione abilitati. Ad esempio, usando [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Impostare l'ambiente

Spesso è utile impostare un ambiente specifico per il test con una variabile di ambiente o un'impostazione della piattaforma. Se l'ambiente non viene impostato, il valore predefinito è `Production`, che disabilita la maggior parte delle funzionalità di debug. Il metodo per l'impostazione dell'ambiente dipende dal sistema operativo.

Quando viene compilato l'host, l'ultima impostazione dell'ambiente letta dall'app determina l'ambiente dell'app. L'ambiente dell'app non può essere modificato mentre l'app è in esecuzione.

Nella [pagina about](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) del [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) viene visualizzato il valore di `IWebHostEnvironment.EnvironmentName` .

### <a name="azure-app-service"></a>Servizio app di Azure

Per impostare l'ambiente in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/), attenersi alla procedura seguente:

1. Selezionare l'app dal pannello **Servizi app**.
1. Nel gruppo **Impostazioni** selezionare il pannello **configurazione** .
1. Nella scheda **Impostazioni applicazione** selezionare **nuova impostazione applicazione**.
1. Nella finestra **Aggiungi/modifica impostazione applicazione** specificare `ASPNETCORE_ENVIRONMENT` il **nome**. Per **valore**, specificare l'ambiente (ad esempio, `Staging` ).
1. Selezionare la casella di controllo **Impostazioni slot di distribuzione** se si desidera che l'impostazione dell'ambiente rimanga con lo slot corrente quando vengono scambiati gli slot di distribuzione. Per altre informazioni, vedere [configurare gli ambienti di staging nel servizio app Azure](/azure/app-service/web-sites-staged-publishing) nella documentazione di Azure.
1. Selezionare **OK** per chiudere la finestra **Aggiungi/modifica impostazione applicazione** .
1. Selezionare **Save (Salva** ) nella parte superiore del pannello **Configuration (configurazione** ).

App Azure servizio riavvia automaticamente l'app dopo l'aggiunta, la modifica o l'eliminazione di un'impostazione dell'app nel portale di Azure.

### <a name="windows"></a>Windows

I valori di ambiente in *launchSettings.jssui* valori di override impostati nell'ambiente di sistema.

Per impostare la variabile `ASPNETCORE_ENVIRONMENT` per la sessione corrente, se l'app viene avviata tramite [dotnet run](/dotnet/core/tools/dotnet-run), vengono usati i comandi seguenti:

**Prompt dei comandi**

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

Il comando precedente imposta `ASPNETCORE_ENVIRONMENT` solo per i processi avviati dalla finestra di comando.

Per impostare il valore a livello globale in Windows, usare uno degli approcci seguenti:

* Scegliere **Pannello di controllo** > **Sistema** > **Impostazioni di sistema avanzate** e aggiungere o modificare il valore `ASPNETCORE_ENVIRONMENT`:

  ![Proprietà di sistema avanzate](environments/_static/systemsetting_environment.png)

  ![Variabile di ambiente ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Aprire un prompt dei comandi di amministrazione e usare il comando `setx` o aprire un prompt dei comandi di PowerShell di amministrazione e usare `[Environment]::SetEnvironmentVariable`:

  **Prompt dei comandi**

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  L'opzione `/M` indica di impostare la variabile di ambiente a livello del sistema. Se non viene usata l'opzione `/M`, la variabile di ambiente viene impostata per l'account utente.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  Il valore dell'opzione `Machine` indica di impostare la variabile di ambiente a livello del sistema. Se non viene usata l'opzione `User`, la variabile di ambiente viene impostata per l'account utente.

Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` è impostata a livello globale, viene applicata per `dotnet run` in tutte le finestre di comando aperte dopo l'impostazione del valore. I valori di ambiente in *launchSettings.jssui* valori di override impostati nell'ambiente di sistema.

**web.config**

Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` con *web.config*, vedere la sezione *Impostazione delle variabili di ambiente* di <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**File di progetto o profilo di pubblicazione**

**Per le distribuzioni di Windows IIS:** Includere la `<EnvironmentName>` proprietà nel [profilo di pubblicazione (con estensione pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o nel file di progetto. Questo approccio imposta l'ambiente in *web.config* quando viene pubblicato il progetto:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Pool di applicazioni IIS singoli**

Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` per un'app in esecuzione in un pool di applicazioni isolato (supportato in IIS 10.0 o versioni successive), vedere la sezione *AppCmd.exe* dell'argomento [Variabili di ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` viene impostata per un pool di app, il suo valore esegue l'override di un'impostazione a livello di sistema.

Durante l'hosting di un'app in IIS, quando si aggiunge o si modifica la variabile di ambiente `ASPNETCORE_ENVIRONMENT`, usare uno degli approcci seguenti per fare in modo che il nuovo valore venga selezionato dalle app:

* Eseguire `net stop was /y` seguito da `net start w3svc` da un prompt dei comandi.
* Riavviare il server.

#### <a name="macos"></a>macOS

L'impostazione dell'ambiente corrente per macOS può essere eseguita in linea durante l'esecuzione dell'app:

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

In alternativa, impostare l'ambiente tramite `export` prima di eseguire l'app:

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

Le variabili di ambiente a livello computer sono impostate nel file con estensione *bashrc* o *bash_profile*. Modificare il file usando un qualsiasi editor di testo. Aggiungere l'istruzione seguente:

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a>Linux

Per le distribuzioni di Linux, usare il `export` comando al prompt dei comandi per le impostazioni delle variabili basate sulla sessione e *bash_profile* file per le impostazioni di ambiente a livello di computer.

### <a name="set-the-environment-in-code"></a>Impostazione dell'ambiente nel codice

Chiamare <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> quando si compila l'host. Vedere <xref:fundamentals/host/generic-host#environmentname>.

### <a name="configuration-by-environment"></a>Configurazione per ambiente

Per caricare la configurazione in base all'ambiente, vedere <xref:fundamentals/configuration/index#json-configuration-provider> .

## <a name="environment-based-startup-class-and-methods"></a>Classe Startup e metodi basati sull'ambiente

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Inserire IWebHostEnvironment nella classe Startup

Inserire <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> nel `Startup` costruttore. Questo approccio è utile quando l'app richiede la configurazione `Startup` solo per alcuni ambienti con differenze minime di codice per ogni ambiente.

Nell'esempio seguente:

* L'ambiente viene mantenuto nel `_env` campo.
* `_env`viene usato in `ConfigureServices` e `Configure` per applicare la configurazione di avvio in base all'ambiente dell'app.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a>Convenzioni delle classi di avvio

Quando viene avviata un'app ASP.NET Core, la [classe Startup](xref:fundamentals/startup) avvia l'app. L'app può definire più `Startup` classi per ambienti diversi. La `Startup` classe appropriata viene selezionata in fase di esecuzione. La classe il cui suffisso di nome corrisponde all'ambiente corrente ha la priorità. Se non viene trovata una classe `Startup{EnvironmentName}` corrispondente, viene usata la classe `Startup`. Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente. Questo approccio non è necessario per le app tipiche.

Per implementare classi basate sull'ambiente `Startup` , creare `Startup{EnvironmentName}` classi e una classe di fallback `Startup` :

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

Usare l'overload [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) che accetta un nome di assembly:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a>Convenzioni dei metodi di avvio

[Configurare](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) supportare versioni specifiche dell'ambiente del modulo `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services` . Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ambiente:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core configura il comportamento dell'app in base all'ambiente di runtime e tramite una variabile di ambiente.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ambienti

ASP.NET Core legge la variabile di ambiente `ASPNETCORE_ENVIRONMENT` all'avvio dell'app e ne archivia il valore in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`può essere impostato su qualsiasi valore, ma vengono forniti tre valori dal Framework:

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

In Windows e macOS, le variabili di ambiente e i valori non fanno distinzione tra maiuscole e minuscole. Le variabili e i valori di ambiente Linux fanno distinzione tra maiuscole e minuscole per impostazione predefinita

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

Spesso è utile impostare un ambiente specifico per il test con una variabile di ambiente o un'impostazione della piattaforma. Se l'ambiente non viene impostato, il valore predefinito è `Production`, che disabilita la maggior parte delle funzionalità di debug. Il metodo per l'impostazione dell'ambiente dipende dal sistema operativo.

Quando viene compilato l'host, l'ultima impostazione dell'ambiente letta dall'app determina l'ambiente dell'app. L'ambiente dell'app non può essere modificato mentre l'app è in esecuzione.

### <a name="environment-variable-or-platform-setting"></a>Impostazione della piattaforma o della variabile di ambiente

#### <a name="azure-app-service"></a>Servizio app di Azure

Per impostare l'ambiente in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/), attenersi alla procedura seguente:

1. Selezionare l'app dal pannello **Servizi app**.
1. Nel gruppo **Impostazioni** selezionare il pannello **configurazione** .
1. Nella scheda **Impostazioni applicazione** selezionare **nuova impostazione applicazione**.
1. Nella finestra **Aggiungi/modifica impostazione applicazione** specificare `ASPNETCORE_ENVIRONMENT` il **nome**. Per **valore**, specificare l'ambiente (ad esempio, `Staging` ).
1. Selezionare la casella di controllo **Impostazioni slot di distribuzione** se si desidera che l'impostazione dell'ambiente rimanga con lo slot corrente quando vengono scambiati gli slot di distribuzione. Per altre informazioni, vedere [configurare gli ambienti di staging nel servizio app Azure](/azure/app-service/web-sites-staged-publishing) nella documentazione di Azure.
1. Selezionare **OK** per chiudere la finestra **Aggiungi/modifica impostazione applicazione** .
1. Selezionare **Save (Salva** ) nella parte superiore del pannello **Configuration (configurazione** ).

Servizio app di Azure riavvia automaticamente l'app quando un'impostazione dell'app (una variabile di ambiente) viene aggiunta, modificata o eliminata nel portale di Azure.

#### <a name="windows"></a>Windows

Per impostare la variabile `ASPNETCORE_ENVIRONMENT` per la sessione corrente, se l'app viene avviata tramite [dotnet run](/dotnet/core/tools/dotnet-run), vengono usati i comandi seguenti:

**Prompt dei comandi**

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

  **Prompt dei comandi**

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

**web.config**

Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` con *web.config*, vedere la sezione *Impostazione delle variabili di ambiente* di <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**File di progetto o profilo di pubblicazione**

**Per le distribuzioni di Windows IIS:** Includere la `<EnvironmentName>` proprietà nel [profilo di pubblicazione (con estensione pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o nel file di progetto. Questo approccio imposta l'ambiente in *web.config* quando viene pubblicato il progetto:

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

Per le distribuzioni di Linux, usare il `export` comando al prompt dei comandi per le impostazioni delle variabili basate sulla sessione e *bash_profile* file per le impostazioni di ambiente a livello di computer.

### <a name="set-the-environment-in-code"></a>Impostazione dell'ambiente nel codice

Chiamare <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> quando si compila l'host. Vedere <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Configurazione per ambiente

Per caricare la configurazione dall'ambiente, sono consigliabili:

* file *appSettings* (*appSettings. { Environment}. JSON*). Vedere <xref:fundamentals/configuration/index#json-configuration-provider>.
* Variabili di ambiente (impostate in ogni sistema in cui è ospitata l'app). Controllare <xref:fundamentals/host/web-host#environment> e <xref:security/app-secrets#environment-variables>.
* Secret Manager (solo nell'ambiente di sviluppo). Vedere <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Classe Startup e metodi basati sull'ambiente

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Inserire IHostingEnvironment in Startup.ConfigUre

Inserire <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> in `Startup.Configure` . Questo approccio è utile quando l'app richiede solo la configurazione `Startup.Configure` solo per alcuni ambienti con differenze minime di codice per ogni ambiente.

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>Inserire IHostingEnvironment nella classe Startup

Inserire <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> nel `Startup` costruttore e assegnare il servizio a un campo da utilizzare in tutta la `Startup` classe. Questo approccio è utile quando l'app richiede la configurazione dell'avvio per solo alcuni ambienti con differenze minime di codice per ogni ambiente.

Nell'esempio seguente:

* L'ambiente viene mantenuto nel `_env` campo.
* `_env`viene usato in `ConfigureServices` e `Configure` per applicare la configurazione di avvio in base all'ambiente dell'app.

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

Quando viene avviata un'app ASP.NET Core, la [classe Startup](xref:fundamentals/startup) avvia l'app. L'app può definire `Startup` classi separate per ambienti diversi, ad esempio `StartupDevelopment` . La `Startup` classe appropriata viene selezionata in fase di esecuzione. La classe il cui suffisso di nome corrisponde all'ambiente corrente ha la priorità. Se non viene trovata una classe `Startup{EnvironmentName}` corrispondente, viene usata la classe `Startup`. Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.

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

[Configurare](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) supportare versioni specifiche dell'ambiente del modulo `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services` . Questo approccio è utile quando l'app richiede la configurazione dell'avvio per diversi ambienti con molte differenze di codice per ogni ambiente.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
