---
title: File web.config
author: rick-anderson
description: Scoprire che cosa si trova all'interno del file di web.config e come configurare diverse opzioni del modulo di ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: edeef31042547db79fcec98f1236787f78e187a5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057284"
---
# <a name="webconfig-file"></a>File `web.config`

`web.config`È un file letto da IIS e il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) per configurare un'app ospitata con IIS.

## <a name="webconfig-file-location"></a>`web.config` percorso file

Per configurare correttamente il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , il `web.config` file deve essere presente nel percorso [radice del contenuto](xref:fundamentals/index#content-root) (in genere il percorso di base dell'app) dell'app distribuita. Corrisponde al percorso fisico del sito Web fornito a IIS. Il `web.config` file è necessario alla radice dell'app per abilitare la pubblicazione di più app usando distribuzione Web.

I file sensibili sono presenti nel percorso fisico dell'app, ad esempio `{ASSEMBLY}.runtimeconfig.json` , `{ASSEMBLY}.xml` (commenti in formato documentazione XML) e `{ASSEMBLY}.deps.json` , dove il segnaposto `{ASSEMBLY}` è il nome dell'assembly. Quando il `web.config` file è presente e il sito viene avviato normalmente, in IIS questi file riservati non vengono utilizzati se richiesti. Se il `web.config` file è mancante, denominato in modo non corretto o non è in grado di configurare il sito per l'avvio normale, è possibile che in IIS vengano utilizzati pubblicamente file riservati.

**Il `web.config` file deve essere presente nella distribuzione in qualsiasi momento, denominato correttamente e in grado di configurare il sito per l'avvio normale. Non rimuovere mai il `web.config` file da una distribuzione di produzione.**

Se un `web.config` file non è presente nel progetto, il file viene creato con il corretto `processPath` e `arguments` per configurare il modulo ASP.NET Core e spostato nell' [output pubblicato](xref:host-and-deploy/directory-structure).

Se un `web.config` file è presente nel progetto, il file viene trasformato con il corretto `processPath` e `arguments` per configurare il modulo ASP.NET Core e spostato nell'output pubblicato. La trasformazione non modifica le impostazioni di configurazione di IIS incluse nel file.

Il `web.config` file può fornire impostazioni di configurazione di IIS aggiuntive che controllano i moduli IIS attivi. Per informazioni sui moduli IIS in grado di elaborare le richieste con le app di ASP.NET Core, vedere l'argomento [Moduli IIS](xref:host-and-deploy/iis/modules).

La creazione, la trasformazione e la pubblicazione del `web.config` file vengono gestite da una destinazione MSBuild ( `_TransformWebConfig` ) quando viene pubblicato il progetto. Questa destinazione si trova tra le destinazioni Web SDK (`Microsoft.NET.Sdk.Web`). L'SDK è impostato all'inizio del file di progetto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Per impedire a Web SDK di trasformare il `web.config` file, usare la `<IsTransformWebConfigDisabled>` proprietà nel file di progetto:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Quando si disabilita il Web SDK dalla trasformazione del file, `processPath` `arguments` è necessario impostare manualmente lo sviluppatore. Per altre informazioni, vedere <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a>Configurazione del modulo ASP.NET Core con `web.config`

Il modulo ASP.NET Core viene configurato con la `aspNetCore` sezione del `system.webServer` nodo nel file del sito `web.config` .

Il `web.config` file seguente viene pubblicato per una [distribuzione dipendente dal Framework](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura il modulo ASP.NET Core per gestire le richieste del sito:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

`web.config`Per una [distribuzione autonoma](/dotnet/articles/core/deploying/#self-contained-deployments-scd), viene pubblicato il codice seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

La <xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> proprietà è impostata su `false` per indicare che le impostazioni specificate all'interno dell' [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemento non vengono ereditate da app che si trovano in una sottodirectory dell'app.

Quando un'app viene distribuita in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/), il percorso `stdoutLogFile` è impostato su `\\?\%home%\LogFiles\stdout`. Il percorso Salva i log stdout nella `LogFiles` cartella, che è un percorso creato automaticamente dal servizio.

Per informazioni sulla configurazione delle applicazioni secondarie IIS, vedere <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Attributi dell' `aspNetCore` elemento

| Attributo | Descrizione | Predefinito |
| --------- | ----------- | :-----: |
| `arguments` | <p>Attributo stringa facoltativo.</p><p>Argomenti per il file eseguibile specificato in `processPath` .</p> | |
| `disableStartUpErrorPage` | <p>Attributo booleano facoltativo.</p><p>Se è true, la pagina di **errore 502,5-Process** viene eliminata e la tabella codici di stato 502 configurata in ha la `web.config` precedenza.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Attributo booleano facoltativo.</p><p>Se true, il token viene inviato al processo figlio in ascolto `%ASPNETCORE_PORT%` come intestazione ' MS-ASPNETCORE-WINAUTHTOKEN ' per ogni richiesta. È responsabilità del processo chiamare CloseHandle su questo token per ogni richiesta.</p> | `true` |
| `hostingModel` | <p>Attributo stringa facoltativo.</p><p>Specifica il modello di hosting come in-process ( `InProcess` / `inprocess` ) o out-of-process ( `OutOfProcess` / `outofprocess` ).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Attributo Integer facoltativo.</p><p>Specifica il numero di istanze del processo specificato nell' `processPath` impostazione che è possibile creare per ogni app.</p><p>&dagger;Per l'hosting in-process, il valore è limitato a `1`.</p><p>L'impostazione di `processesPerApplication` è sconsigliata. Questo attributo sarà rimosso nelle versioni future.</p> | Valore predefinito: `1`<br>Min: `1`<br>Max: `100`&dagger; |
| `processPath` | <p>Attributo stringa obbligatorio.</p><p>Percorso del file eseguibile che avvia un processo in ascolto delle richieste HTTP. I percorsi relativi sono supportati. Se il percorso inizia con `.`, viene considerato relativo alla radice del sito.</p> | |
| `rapidFailsPerMinute` | <p>Attributo Integer facoltativo.</p><p>Specifica il numero di volte in cui il processo specificato in `processPath` può arrestarsi in modo anomalo al minuto. Se questo limite viene superato, il modulo smette di avviare il processo per la parte restante del minuto.</p><p>Non supportato con l'hosting in-process.</p> | Valore predefinito: `10`<br>Min: `0`<br>Max: `100` |
| `requestTimeout` | <p>Attributo Timespan facoltativo.</p><p>Specifica la durata per cui il modulo ASP.NET Core attende una risposta dal processo in ascolto su %ASPNETCORE_PORT%.</p><p>Nelle versioni del modulo ASP.NET Core fornito con ASP.NET Core 2.1 o versioni successive, `requestTimeout` viene specificato in ore, minuti e secondi.</p><p>Non è applicabile all'hosting in-process. Per l'hosting in-process, il modulo resta in attesa che l'app elabori la richiesta.</p><p>I valori validi per i segmenti della stringa relativi a minuti e secondi sono compresi nell'intervallo tra 0 e 59. L'utilizzo di `60` nel valore per minuti o secondi restituisce un *errore 500 interno del server*.</p> | Valore predefinito: `00:02:00`<br>Min: `00:00:00`<br>Max: `360:00:00` |
| `shutdownTimeLimit` | <p>Attributo Integer facoltativo.</p><p>Durata in secondi durante la quale il modulo attende che l'eseguibile venga arrestato normalmente quando `app_offline.htm` viene rilevato il file.</p> | Valore predefinito: `10`<br>Min: `0`<br>Max: `600` |
| `startupTimeLimit` | <p>Attributo Integer facoltativo.</p><p>Durata in secondi per cui il modulo attende l'avvio di un processo in ascolto sulla porta da parte del file eseguibile. Se questo limite di tempo viene superato, il modulo termina il processo.</p><p>Quando si esegue l'hosting *in-process*: il processo **non** viene riavviato **e non usa** l' `rapidFailsPerMinute` impostazione.</p><p>Quando si esegue l'hosting *out-of-process*: il modulo tenta di riavviare il processo quando riceve una nuova richiesta e continua a provare a riavviare il processo per le successive richieste in ingresso, a meno che l'app non riesca ad avviare il `rapidFailsPerMinute` numero di volte nell'ultimo minuto in sequenza.</p><p>Un valore pari a 0 (zero) **non** è considerato un timeout infinito.</p> | Valore predefinito: `120`<br>Min: `0`<br>Max: `3600` |
| `stdoutLogEnabled` | <p>Attributo booleano facoltativo.</p><p>Se true, `stdout` e `stderr` per il processo specificato in `processPath` vengono reindirizzati al file specificato in `stdoutLogFile` .</p> | `false` |
| `stdoutLogFile` | <p>Attributo stringa facoltativo.</p><p>Specifica il percorso del file relativo o assoluto per `stdout` cui `stderr` vengono registrati e dal processo specificato in `processPath` . I percorsi relativi sono relativi alla radice del sito. Qualsiasi percorso che inizia con `.` è relativo al sito radice e tutti gli altri percorsi vengono trattati come percorsi assoluti. Le eventuali cartelle specificate nel percorso vengono create dal modulo quando viene creato il file di log. L'utilizzo di delimitatori di sottolineatura, timestamp, ID processo ed estensione di file ( `.log` ) viene aggiunto all'ultimo segmento del `stdoutLogFile` percorso. Se `.\logs\stdout` viene fornito come valore, un esempio di log stdout viene salvato come `stdout_20180205194132_1934.log` nella `logs` cartella quando viene salvato il 2/5/2018 alle 19:41:32 con l'ID di processo 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

È possibile specificare le variabili di ambiente per il processo nell'attributo `processPath`. Specificare una variabile di ambiente con l'elemento figlio `<environmentVariable>` di un elemento della raccolta `<environmentVariables>`. Le variabili di ambiente impostate in questa sezione hanno la precedenza sulle variabili di ambiente di sistema.

Nell'esempio seguente vengono impostate due variabili di ambiente in `web.config` . `ASPNETCORE_ENVIRONMENT` configura l'ambiente dell'app su `Development`. Uno sviluppatore può impostare temporaneamente questo valore nel `web.config` file per forzare il caricamento della [pagina delle eccezioni](xref:fundamentals/error-handling) per gli sviluppatori durante il debug di un'eccezione dell'app. `CONFIG_DIR` è un esempio di variabile di ambiente definita dall'utente, in cui lo sviluppatore ha scritto il codice che legge il valore all'avvio in modo da formare un percorso per il caricamento del file di configurazione dell'app.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Un'alternativa all'impostazione dell'ambiente direttamente in `web.config` consiste nell'includere la `<EnvironmentName>` proprietà nel file di progetto o nel [profilo di pubblicazione `.pubxml` ](xref:host-and-deploy/visual-studio-publish-profiles) . Questo approccio imposta l'ambiente in `web.config` quando viene pubblicato il progetto:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` su `Development` solo in server di gestione temporanea e test che non sono accessibili da reti non attendibili, ad esempio Internet.

## <a name="configuration-of-iis-with-webconfig"></a>Configurazione di IIS con `web.config`

La configurazione di IIS è influenzata dalla `<system.webServer>` sezione di `web.config` per gli scenari IIS funzionali per ASP.NET Core app con il modulo di ASP.NET Core. Ad esempio, la configurazione di IIS è funzionale per la compressione dinamica. Se IIS è configurato a livello di server per usare la compressione dinamica, l' `<urlCompression>` elemento nel file dell'app `web.config` può disabilitarlo per un'app ASP.NET Core.

Per altre informazioni, vedere i seguenti argomenti:

* [Riferimento alla configurazione per `<system.webServer>`](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Per impostare le variabili di ambiente per singole app in esecuzione in pool di applicazioni isolate (supportate per IIS 10,0 o versioni successive), vedere la sezione *`AppCmd.exe` comando* dell'argomento [variabili `<environmentVariables>` di ambiente](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) nella documentazione di riferimento di IIS.

## <a name="configuration-sections-of-webconfig"></a>Sezioni di configurazione di `web.config`

Le sezioni di configurazione delle app ASP.NET 4. x in `web.config` non vengono usate dalle app ASP.NET Core per la configurazione:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Le applicazioni ASP.NET Core vengono configurate tramite altri provider di configurazione. Per ulteriori informazioni, vedere [configurazione](xref:fundamentals/configuration/index).

## <a name="transform-webconfig"></a>Trasformare web.config

Se è necessario eseguire la trasformazione `web.config` alla pubblicazione, vedere <xref:host-and-deploy/iis/transform-webconfig> . Potrebbe essere necessario trasformare `web.config` in pubblicazione per impostare le variabili di ambiente in base alla configurazione, al profilo o all'ambiente.

## <a name="additional-resources"></a>Risorse aggiuntive

* [IIS \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
