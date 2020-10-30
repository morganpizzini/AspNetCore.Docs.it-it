---
title: Hosting out-of-process con IIS e ASP.NET Core
author: rick-anderson
description: Informazioni sull'hosting out-of-process con IIS e il modulo ASP.NET Core.
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060417"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a>Hosting out-of-process con IIS e ASP.NET Core 

Poiché le app ASP.NET Core vengono eseguite in un processo separato dal processo di lavoro IIS, il modulo ASP.NET Core gestisce la gestione dei processi. Il modulo avvia il processo per l'app ASP.NET Core quando arriva la prima richiesta e riavvia l'app se viene arrestata o si arresta in modo anomalo. Si tratta essenzialmente dello stesso comportamento delle app eseguite in-process e gestite dal [servizio Attivazione processo Windows](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Il diagramma seguente illustra la relazione tra IIS, il modulo ASP.NET Core e un'app ospitata out-of-process:

![Modulo ASP.NET Core nello scenario di hosting out-of-process](index/_static/ancm-outofprocess.png)

1. Le richieste arrivano dal Web al driver HTTP.sys in modalità kernel.
1. Il driver instrada le richieste a IIS sulla porta configurata del sito Web. La porta configurata è generalmente 80 (HTTP) o 443 (HTTPS).
1. Il modulo Invia le richieste a gheppio su una porta casuale per l'app. La porta casuale non è 80 o 443.

<!-- make this a bullet list -->
Il modulo ASP.NET Core specifica la porta tramite una variabile di ambiente all'avvio. L' <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> estensione configura il server per l'ascolto `http://localhost:{PORT}` . Vengono eseguiti controlli aggiuntivi e le richieste che non provengono dal modulo vengono rifiutate. Il modulo non supporta l'invio HTTPS. Le richieste vengono inviate tramite HTTP anche se ricevute da IIS su HTTPS.

Quando il gheppio preleva la richiesta dal modulo, la richiesta viene trasmessa alla pipeline del middleware ASP.NET Core. La pipeline middleware gestisce la richiesta e la passa come istanza di `HttpContext` alla logica dell'app. Il middleware aggiunto dall'integrazione di IIS aggiorna lo schema, l'IP remoto e il percorso di base all'account per l'inoltro della richiesta a Kestrel. La risposta dell'app viene passata di nuovo a IIS, che la inoltra al client HTTP che ha avviato la richiesta.

Per indicazioni sulla configurazione del modulo ASP.NET Core, vedere <xref:host-and-deploy/aspnet-core-module>.

Per altre informazioni sull'hosting, vedere [Hosting in ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Configurazione dell'applicazione

### <a name="enable-the-iisintegration-components"></a>Abilitare i componenti IISIntegration

Quando si compila un host in `CreateHostBuilder` ( `Program.cs` ), chiamare <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> per abilitare l'integrazione con IIS:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Per altre informazioni su `CreateDefaultBuilder`, vedere <xref:fundamentals/host/generic-host#default-builder-settings>.


**Modello di hosting out-of-process**

Per configurare le opzioni di IIS includere una configurazione del servizio per <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>. Nell'esempio seguente si impedisce all'app di popolare `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opzione                         | Predefinito | Impostazione |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Se `true`, il [middleware di integrazione IIS](#enable-the-iisintegration-components) imposta `HttpContext.User` autenticato tramite l'[autenticazione di Windows](xref:security/authentication/windowsauth). Se `false`, il middleware fornisce solo un'identità per `HttpContext.User` e risponde solo alle richieste esplicite di `AuthenticationScheme`. Per il funzionamento di `AutomaticAuthentication` l’autenticazione di Windows deve essere abilitata in IIS. Per altre informazioni, vedere l'argomento [Autenticazione di Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Imposta il nome visualizzato dagli utenti nelle pagine di accesso. |
| `ForwardClientCertificate`     | `true`  | Se è `true` ed è presente l’intestazione della richiesta `MS-ASPNETCORE-CLIENTCERT`, `HttpContext.Connection.ClientCertificate` viene popolato. |


### <a name="proxy-server-and-load-balancer-scenarios"></a>Scenari con server proxy e servizi di bilanciamento del carico

Il [middleware di integrazione IIS](#enable-the-iisintegration-components) e il modulo ASP.NET Core sono configurati per l'invio di:

* Schema (HTTP/HTTPS).
* Indirizzo IP remoto da cui ha avuto origine la richiesta.

Il [middleware di integrazione di IIS](#enable-the-iisintegration-components) configura il middleware delle intestazioni con inoltri.

Potrebbero essere necessari interventi di configurazione aggiuntivi per le app ospitate dietro ulteriori server proxy e servizi di bilanciamento del carico. Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](xref:host-and-deploy/proxy-load-balancer).


### <a name="out-of-process-hosting-model"></a>Modello di hosting out-of-process

Per configurare un'app per l'hosting out-of-process, impostare il valore della `<AspNetCoreHostingModel>` proprietà su `OutOfProcess` nel file di progetto ( `.csproj` ):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

L'hosting in-process è impostato su `InProcess` , che corrisponde al valore predefinito.

Il valore di non `<AspNetCoreHostingModel>` fa distinzione tra maiuscole e minuscole, pertanto `inprocess` e `outofprocess` sono valori validi.

Viene usato il server [Kestrel](xref:fundamentals/servers/kestrel) al posto di un server HTTP di IIS (`IISHttpServer`).

Per out-of-process, le [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) chiamate <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> a:

* Configurare la porta e il percorso di base su cui il server deve eseguire l'ascolto in caso di esecuzione dietro il modulo ASP.NET Core.
* Configurare l'host per l'acquisizione degli errori di avvio.

### <a name="process-name"></a>Nome del processo

`Process.GetCurrentProcess().ProcessName` dichiara `w3wp`/`iisexpress` (In-Process) o `dotnet` (out-of-process).

Molti moduli nativi, ad esempio l'autenticazione di Windows, rimangono attivi. Per altre informazioni sui moduli IIS attivi con il modulo ASP.NET Core, vedere <xref:host-and-deploy/iis/modules>.

Il modulo ASP.NET Core può anche:

* Impostare variabili di ambiente per il processo di lavoro.
* Registrare output stdout in una risorsa di archiviazione di file per la risoluzione di problemi di avvio.
* Inoltrare token di autenticazione di Windows.
