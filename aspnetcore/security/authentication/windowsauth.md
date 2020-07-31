---
title: Configurare l'autenticazione di Windows in ASP.NET Core
author: scottaddie
description: Informazioni su come configurare l'autenticazione di Windows in ASP.NET Core per IIS e HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330684"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Configurare l'autenticazione di Windows in ASP.NET Core

Di [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

L'autenticazione di Windows (nota anche come Negotiate, Kerberos o NTLM) può essere configurata per ASP.NET Core app ospitate con [IIS](xref:host-and-deploy/iis/index), [gheppio](xref:fundamentals/servers/kestrel)o [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

L'autenticazione di Windows (nota anche come Negotiate, Kerberos o NTLM) può essere configurata per ASP.NET Core app ospitate con [IIS](xref:host-and-deploy/iis/index) o [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

L'autenticazione di Windows si basa sul sistema operativo per autenticare gli utenti delle app ASP.NET Core. È possibile usare l'autenticazione di Windows quando il server viene eseguito in una rete aziendale usando Active Directory identità di dominio o account di Windows per identificare gli utenti. L'autenticazione di Windows è più adatta agli ambienti Intranet in cui gli utenti, le app client e i server Web appartengono allo stesso dominio Windows.

> [!NOTE]
> L'autenticazione di Windows non è supportata con HTTP/2. Le richieste di autenticazione possono essere inviate in risposta HTTP/2, ma il client deve eseguire il downgrade a HTTP/1.1 prima dell'autenticazione.

## <a name="proxy-and-load-balancer-scenarios"></a>Scenari di proxy e bilanciamento del carico

L'autenticazione di Windows è uno scenario con stato utilizzato principalmente in una rete Intranet, in cui un proxy o un servizio di bilanciamento del carico non gestisce in genere il traffico tra client e server. Se si usa un proxy o un servizio di bilanciamento del carico, l'autenticazione di Windows funziona solo se il proxy o il servizio di bilanciamento del carico:

* Gestisce l'autenticazione.
* Passa le informazioni di autenticazione utente all'app, ad esempio in un'intestazione di richiesta, che agisce sulle informazioni di autenticazione.

Un'alternativa all'autenticazione di Windows negli ambienti in cui vengono usati proxy e servizi di bilanciamento del carico è Active Directory servizi federati (ADFS) con OpenID Connect (OIDC).

## <a name="iisiis-express"></a>IIS/IIS Express

Aggiungere i servizi di autenticazione richiamando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> spazio dei nomi) in `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>Impostazioni di avvio (debugger)

La configurazione per le impostazioni di avvio influiscono solo sulle *Proprietà/launchSettings.jsnel* file per IIS Express e non configura IIS per l'autenticazione di Windows. La configurazione del server è illustrata nella sezione [IIS](#iis) .

Il modello di **applicazione Web** disponibile tramite Visual Studio o il interfaccia della riga di comando di .NET Core può essere configurato per supportare l'autenticazione di Windows, che aggiorna automaticamente le *Proprietà/launchSettings.jssul* file.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Nuovo progetto**

1. Creare un nuovo progetto.
1. Selezionare **Applicazione Web ASP.NET Core**. Selezionare **Avanti**.
1. Specificare un nome nel campo **nome progetto** . Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto. Selezionare **Crea**.
1. Selezionare **modifica** in **autenticazione**.
1. Nella finestra **Cambia autenticazione** selezionare autenticazione di **Windows**. Seleziona **OK**.
1. Selezionare **applicazione Web**.
1. Selezionare **Crea**.

Eseguire l'app. Il nome utente viene visualizzato nell'interfaccia utente dell'app di cui è stato eseguito il rendering.

**Progetto esistente**

Le proprietà del progetto abilitano l'autenticazione di Windows e disabilitano l'autenticazione anonima:

1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Proprietà**.
1. Selezionare la scheda **Debug**.
1. Deselezionare la casella di controllo **Abilita autenticazione anonima**.
1. Selezionare la casella di controllo **Abilita autenticazione di Windows**.
1. Salvare e chiudere la pagina delle proprietà.

In alternativa, le proprietà possono essere configurate nel `iisSettings` nodo del *launchSettings.js* nel file:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

**Nuovo progetto**

Eseguire il comando [DotNet New](/dotnet/core/tools/dotnet-new) con l' `webapp` argomento (ASP.NET Core app Web) e `--auth Windows` Switch:

```dotnetcli
dotnet new webapp --auth Windows
```

**Progetto esistente**

Aggiornare il `iisSettings` nodo del *launchSettings.jsnel* file:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

Quando si modifica un progetto esistente, verificare che il file di progetto includa un riferimento al pacchetto per il [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) **o** il pacchetto NuGet [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .

### <a name="iis"></a>IIS

IIS usa il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) per ospitare ASP.NET Core app. L'autenticazione di Windows è configurata per IIS tramite il file di *web.config* . Le sezioni seguenti mostrano come:

* Fornire un file di *web.config* locale che attiva l'autenticazione di Windows nel server quando viene distribuita l'app.
* Utilizzare Gestione IIS per configurare il file di *web.config* di un'app ASP.NET Core che è già stata distribuita nel server.

Se non è già stato fatto, abilitare IIS per ospitare ASP.NET Core app. Per altre informazioni, vedere <xref:host-and-deploy/iis/index>.

Abilitare il servizio ruolo IIS per l'autenticazione di Windows. Per ulteriori informazioni, vedere [abilitare l'autenticazione di Windows nei servizi ruolo IIS (vedere il passaggio 2)](xref:host-and-deploy/iis/index#iis-configuration).

Il [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) è configurato per l'autenticazione automatica delle richieste per impostazione predefinita. Per ulteriori informazioni, vedere [Host ASP.NET Core in Windows con IIS: opzioni IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

Il modulo ASP.NET Core è configurato per l'invio del token di autenticazione di Windows all'app per impostazione predefinita. Per altre informazioni, vedere [riferimento alla configurazione del modulo ASP.NET Core: attributi dell'elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

Usare **uno** degli approcci seguenti:

* **Prima di pubblicare e distribuire il progetto,** aggiungere il seguente file di *web.config* alla radice del progetto:

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  Quando il progetto viene pubblicato dal .NET Core SDK (senza la `<IsTransformWebConfigDisabled>` proprietà impostata su `true` nel file di progetto), il file di *web.config* pubblicato include la `<location><system.webServer><security><authentication>` sezione. Per ulteriori informazioni sulla `<IsTransformWebConfigDisabled>` proprietà, vedere <xref:host-and-deploy/iis/index#webconfig-file> .

* **Dopo la pubblicazione e la distribuzione del progetto,** eseguire la configurazione lato server con gestione IIS:

  1. In Gestione IIS selezionare il sito IIS nel nodo **siti** della barra laterale **connessioni** .
  1. Fare doppio clic su **autenticazione** nell'area **IIS** .
  1. Selezionare **autenticazione anonima**. Selezionare **Disabilita** nella barra laterale **azioni** .
  1. Selezionare **Autenticazione di Windows**. Selezionare **Abilita** nella barra laterale **azioni** .

  Quando vengono eseguite queste azioni, gestione IIS modifica il file di *web.config* dell'app. `<system.webServer><security><authentication>`Viene aggiunto un nodo con le impostazioni aggiornate per `anonymousAuthentication` e `windowsAuthentication` :

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  La `<system.webServer>` sezione aggiunta al file di *web.config* da Gestione IIS si trova al di fuori della `<location>` sezione dell'app aggiunta dall'.NET Core SDK quando viene pubblicata l'app. Poiché la sezione viene aggiunta all'esterno del `<location>` nodo, le impostazioni vengono ereditate dalle app [secondarie](xref:host-and-deploy/iis/index#sub-applications) all'app corrente. Per impedire l'ereditarietà, spostare la `<security>` sezione aggiunta all'interno della `<location><system.webServer>` sezione specificata dal .NET Core SDK.

  Quando si usa Gestione IIS per aggiungere la configurazione di IIS, questo influisca solo sul file di *web.config* dell'app nel server. Una distribuzione successiva dell'app potrebbe sovrascrivere le impostazioni nel server se la copia del server *web.config* viene sostituita dal file *web.config* del progetto. Per gestire le impostazioni, usare **uno** degli approcci seguenti:

  * Utilizzare Gestione IIS per reimpostare le impostazioni nel file di *web.config* dopo la sovrascrittura del file durante la distribuzione.
  * Aggiungere un *file diweb.config* all'app localmente con le impostazioni.

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

Il pacchetto NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) può essere usato con [gheppio](xref:fundamentals/servers/kestrel) per supportare l'autenticazione di Windows tramite Negotiate e Kerberos in Windows, Linux e MacOS.

> [!WARNING]
> Le credenziali possono essere rese permanente tra le richieste in una connessione. *L'autenticazione Negotiate non deve essere usata con i proxy, a meno che il proxy non mantenga un'affinità di connessione 1:1 (una connessione permanente) con gheppio.*

> [!NOTE]
> Il gestore Negotiate rileva se il server sottostante supporta l'autenticazione di Windows in modalità nativa e se è abilitato. Se il server supporta l'autenticazione di Windows ma è disabilitato, viene generato un errore in cui viene chiesto di abilitare l'implementazione del server. Quando l'autenticazione di Windows è abilitata nel server, il gestore Negotiate li trasmette in modo trasparente.

Aggiungere i servizi di autenticazione richiamando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> e <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices` :

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

Aggiungere il middleware di autenticazione chiamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure` :

 ```csharp
app.UseAuthentication();
```

Per ulteriori informazioni sul middleware, vedere <xref:fundamentals/middleware/index> .

Sono consentite richieste anonime. Utilizzare [ASP.NET Core autorizzazione](xref:security/authorization/introduction) per la verifica delle richieste anonime di autenticazione.

### <a name="windows-environment-configuration"></a>Configurazione dell'ambiente Windows

Il componente [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) esegue l'autenticazione in modalità utente. È necessario aggiungere i nomi dell'entità servizio (SPN) all'account utente che esegue il servizio, non all'account del computer. Eseguire `setspn -S HTTP/myservername.mydomain.com myuser` in una shell di comandi amministrativi.

### <a name="linux-and-macos-environment-configuration"></a>Configurazione dell'ambiente Linux e macOS

Le istruzioni per l'aggiunta di un computer Linux o macOS a un dominio Windows sono disponibili nell'articolo [connettere Azure Data Studio all'SQL Server mediante autenticazione di Windows-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) . Le istruzioni creano un account computer per il computer Linux nel dominio. È necessario aggiungere nomi SPN all'account del computer.

> [!NOTE]
> Quando si seguono le istruzioni riportate nell'articolo [connettere Azure Data Studio all'SQL Server mediante autenticazione di Windows-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , sostituire `python-software-properties` con `python3-software-properties` se necessario.

Una volta che il computer Linux o macOS è stato aggiunto al dominio, sono necessari passaggi aggiuntivi per fornire un [file keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) con i nomi SPN:

* Nel controller di dominio aggiungere nuovi SPN del servizio Web all'account del computer:
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* Usare [lo Ktpass](/windows-server/administration/windows-commands/ktpass) per generare un file keytab:
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * Alcuni campi devono essere specificati in lettere maiuscole come indicato.
* Copiare il file keytab nel computer Linux o macOS.
* Selezionare il file keytab tramite una variabile di ambiente:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* Richiama `klist` per visualizzare gli SPN attualmente disponibili per l'utilizzo.

> [!NOTE]
> Un file keytab contiene le credenziali di accesso al dominio e deve essere protetto di conseguenza.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) supporta l'autenticazione di Windows in modalità kernel utilizzando l'autenticazione Negotiate, NTLM o di base.

Aggiungere i servizi di autenticazione richiamando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> spazio dei nomi) in `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Configurare l'host web dell'app per l'uso di HTTP.sys con l'autenticazione di Windows (*Program.cs*). <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>si trova nello <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> spazio dei nomi.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> Per la delega all'autenticazione in modalità kernel, HTTP.sys usa il protocollo di autenticazione Kerberos. L'autenticazione in modalità utente non è supportata con Kerberos e HTTP.sys. È necessario usare l'account del computer per decrittografare il token/ticket Kerberos ottenuto da Active Directory e inoltrato dal client al server per autenticare l'utente. Registrare il nome dell'entità servizio per l'host, non l'utente dell'app.

> [!NOTE]
> HTTP.sys non è supportato in nano Server versione 1709 o successiva. Per usare l'autenticazione di Windows e HTTP.sys con nano server, usare un [contenitore Server Core (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/). Per ulteriori informazioni su Server Core, vedere [che cos'è l'opzione di installazione dei componenti di base del server in Windows Server?](/windows-server/administration/server-core/what-is-server-core).

## <a name="authorize-users"></a>Autorizzare gli utenti

Lo stato di configurazione dell'accesso anonimo determina il modo in cui `[Authorize]` gli `[AllowAnonymous]` attributi e vengono usati nell'app. Nelle due sezioni seguenti viene illustrato come gestire gli Stati di configurazione non consentiti e consentiti di accesso anonimo.

### <a name="disallow-anonymous-access"></a>Non consentire l'accesso anonimo

Quando l'autenticazione di Windows è abilitata e l'accesso anonimo è disabilitato, gli `[Authorize]` `[AllowAnonymous]` attributi e non hanno alcun effetto. Se un sito IIS è configurato per non consentire l'accesso anonimo, la richiesta non raggiunge mai l'app. Per questo motivo, l' `[AllowAnonymous]` attributo non è applicabile.

### <a name="allow-anonymous-access"></a>Consenti accesso anonimo

Quando è abilitata l'autenticazione di Windows e l'accesso anonimo, utilizzare gli `[Authorize]` `[AllowAnonymous]` attributi e. L' `[Authorize]` attributo consente di proteggere gli endpoint dell'app che richiedono l'autenticazione. L' `[AllowAnonymous]` attributo sostituisce l' `[Authorize]` attributo nelle app che consentono l'accesso anonimo. Per informazioni dettagliate sull'utilizzo degli attributi, vedere <xref:security/authorization/simple> .

> [!NOTE]
> Per impostazione predefinita, gli utenti che non dispongono dell'autorizzazione per accedere a una pagina vengono presentati con una risposta HTTP 403 vuota. Il [middleware StatusCodePages](xref:fundamentals/error-handling#usestatuscodepages) può essere configurato in modo da offrire agli utenti una migliore esperienza di "accesso negato".

## <a name="impersonation"></a>Rappresentazione

ASP.NET Core non implementa la rappresentazione. Le app vengono eseguite con l'identità dell'app per tutte le richieste, usando il pool di applicazioni o l'identità del processo. Se l'app deve eseguire un'azione per conto di un utente, utilizzare [WindowsIdentity. RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in un [middleware inline terminale](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure` . Eseguire una singola azione in questo contesto e quindi chiudere il contesto.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated`non supporta le operazioni asincrone e non deve essere usato per scenari complessi. Ad esempio, il wrapping di intere richieste o catene di middleware non è supportato o consigliato.

::: moniker range=">= aspnetcore-3.0"

Mentre il pacchetto [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) Abilita l'autenticazione in Windows, Linux e MacOS, la rappresentazione è supportata solo in Windows.

::: moniker-end

## <a name="claims-transformations"></a>Trasformazioni delle attestazioni

::: moniker range=">= aspnetcore-3.0"

Quando si ospita con IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> non viene chiamato internamente per inizializzare un utente. Pertanto, un'implementazione di <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usate per trasformare le attestazioni dopo ogni autenticazione non viene attivata per impostazione predefinita. Per ulteriori informazioni e un esempio di codice che attiva le trasformazioni delle attestazioni, vedere <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Quando si esegue l'hosting con la modalità in-process di IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> non viene chiamato internamente per inizializzare un utente. Pertanto, un'implementazione di <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usate per trasformare le attestazioni dopo ogni autenticazione non viene attivata per impostazione predefinita. Per ulteriori informazioni e un esempio di codice che attiva le trasformazioni delle attestazioni durante l'hosting in-process, vedere <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
