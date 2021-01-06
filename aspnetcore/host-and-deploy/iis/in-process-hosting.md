---
title: Hosting in-process con IIS e ASP.NET Core
author: rick-anderson
description: Informazioni sull'hosting in-process con IIS e il modulo ASP.NET Core.
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058480"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a>Hosting in-process con IIS e ASP.NET Core 

L'hosting in-process esegue un'app ASP.NET Core nello stesso processo del processo di lavoro IIS. L'hosting in-process offre prestazioni migliori rispetto all'hosting out-of-process perché le richieste non vengono inserite in proxy sulla scheda di loopback, un'interfaccia di rete che restituisce il traffico di rete in uscita allo stesso computer.

Il diagramma seguente illustra la relazione tra IIS, il modulo ASP.NET Core e un'app ospitata in-process:

![Modulo ASP.NET Core nello scenario di hosting in-process](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a>Abilita hosting in-process

Dal ASP.NET Core 3,0, l'hosting in-process è stato abilitato per impostazione predefinita per tutte le app distribuite in IIS.

Per configurare in modo esplicito un'app per l'hosting in-process, impostare il valore della `<AspNetCoreHostingModel>` proprietà su `InProcess` nel file di progetto ( `.csproj` ):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a>Architettura generale

Il flusso generale di una richiesta è il seguente:

1. Una richiesta arriva dal Web al driver HTTP.sys in modalità kernel.
1. Il driver instrada la richiesta nativa IIS sulla porta configurata per il sito Web, in genere 80 (HTTP) o 443 (HTTPS).
1. Il modulo ASP.NET Core riceve la richiesta nativa e la passa al server HTTP IIS ( `IISHttpServer` ). Il server HTTP di IIS è un'implementazione di server in-process per IIS che converte la richiesta da nativa a gestita.

Dopo l'elaborazione della richiesta da parte del server HTTP IIS:

1. La richiesta viene inviata alla pipeline del middleware ASP.NET Core.
1. La pipeline middleware gestisce la richiesta e la passa come istanza di `HttpContext` alla logica dell'app.
1. La risposta dell'app viene restituita a IIS tramite il server HTTP di IIS.
1. IIS invia la risposta al client che ha avviato la richiesta.

`CreateDefaultBuilder` aggiunge un' <xref:Microsoft.AspNetCore.Hosting.Server.IServer> istanza di chiamando il <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> metodo per avviare il [CoreCLR](/dotnet/standard/glossary#coreclr) e ospitare l'app all'interno del processo di lavoro IIS ( `w3wp.exe` o `iisexpress.exe` ). I test delle prestazioni indicano che l'hosting di un'app .NET Core in-process offre una velocità effettiva delle richieste significativamente superiore rispetto all'hosting dell'app out-of-process o all'inoltro delle richieste a [Kestrel](xref:fundamentals/servers/kestrel).

Le app pubblicate come un singolo file eseguibile non possono essere caricate dal modello di hosting in-process.

## <a name="application-configuration"></a>Configurazione dell'applicazione

Per configurare le opzioni di IIS includere una configurazione del servizio per <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>. L'esempio seguente disabilita AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Opzione | Predefinito | Impostazione |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | Se `true`, IIS Server imposta l'utente `HttpContext.User` autenticato tramite l'[autenticazione di Windows](xref:security/authentication/windowsauth). Se `false`, il server fornisce solo un'identità per `HttpContext.User` e risponde alle richieste esplicite di `AuthenticationScheme`. Per il funzionamento di `AutomaticAuthentication` l’autenticazione di Windows deve essere abilitata in IIS. Per altre informazioni, vedere [Autenticazione di Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName` | `null` | Imposta il nome visualizzato dagli utenti nelle pagine di accesso. |
| `AllowSynchronousIO` | `false` | Indica se l'i/O sincrono è consentito per `HttpContext.Request` e `HttpContext.Response` . |
| `MaxRequestBodySize` | `30000000` | Ottiene o imposta la dimensione massima del corpo della richiesta per `HttpRequest`. Notare che IIS stesso include il limite `maxAllowedContentLength`, che verrà elaborato prima del set `MaxRequestBodySize` in `IISServerOptions`. La modifica di `MaxRequestBodySize` non influisce su `maxAllowedContentLength`. Per aumentare `maxAllowedContentLength` , aggiungere una voce in `web.config` per impostare `maxAllowedContentLength` su un valore più alto. Per ulteriori informazioni, vedere [Configurazione](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a>Differenze tra l'hosting in-process e out-of-process

In caso di hosting in-process, vengono applicate le caratteristiche seguenti:

* Viene usato un server HTTP di IIS (`IISHttpServer`) al posto del server [Kestrel](xref:fundamentals/servers/kestrel). Per in-process, le [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) chiamate <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> a:

  * Registrare `IISHttpServer`.
  * Configurare la porta e il percorso di base su cui il server deve eseguire l'ascolto in caso di esecuzione dietro il modulo ASP.NET Core.
  * Configurare l'host per l'acquisizione degli errori di avvio.

* L' [ `requestTimeout` attributo](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) non si applica all'hosting in-process.

* Non è supportata la condivisione di un pool di app tra le app. Usare un pool di app per ogni app.

* L'architettura, vale a dire il numero di bit dell'app, e il runtime installato (x64 o x86) devono corrispondere all'architettura del pool di app. Ad esempio, per le app pubblicate per 32 bit (x86) è necessario che siano abilitate 32 bit per i pool di applicazioni IIS. Per ulteriori informazioni, vedere la sezione [creare il sito IIS](xref:tutorials/publish-to-iis#create-the-iis-site) .

* Le disconnessioni del client vengono rilevate. Il [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) token di annullamento viene annullato quando il client si disconnette.

* In caso di hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> non viene chiamato internamente per inizializzare un utente. Pertanto, un'implementazione di <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usate per trasformare le attestazioni dopo ogni autenticazione non viene attivata per impostazione predefinita. Quando si trasformano le attestazioni con un'implementazione di <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chiamare <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> per aggiungere i servizi di autenticazione:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* Le [distribuzioni di pacchetti Web (file singolo)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) non sono supportate.
