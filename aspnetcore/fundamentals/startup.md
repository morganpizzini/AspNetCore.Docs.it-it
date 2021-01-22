---
title: Avvio dell'app in ASP.NET Core
author: rick-anderson
description: Informazioni su come la classe Startup in ASP.NET Core configura i servizi e la pipeline delle richieste dell'app.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/startup
ms.openlocfilehash: e2825476c5fe36571b365ac5ee3c57ff4db61b87
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658677"
---
# <a name="app-startup-in-aspnet-core"></a>Avvio dell'app in ASP.NET Core

Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com)

La classe `Startup` configura i servizi e la pipeline delle richieste dell'app.

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a>Classe Startup

Le app ASP.NET Core usano una classe `Startup` denominata `Startup` per convenzione. La classe `Startup`:

* Include facoltativamente un metodo <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> per configurare i *servizi* dell'app. Un servizio è un componente riutilizzabile che fornisce la funzionalità delle app. I servizi  vengono registrati `ConfigureServices` e utilizzati nell'app tramite l' [inserimento DI dipendenze (di)](xref:fundamentals/dependency-injection) o <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .
* Include un metodo <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> per creare la pipeline di elaborazione delle richieste dell'app.

`ConfigureServices` e `Configure` sono chiamate dal runtime di ASP.NET Core all'avvio dell'app:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

L'esempio precedente è per le [ Razor pagine](xref:razor-pages/index). la versione di MVC è simile.


La classe `Startup` viene specificata al momento della compilazione dell'[host](xref:fundamentals/index#host) dell'app. La `Startup` classe viene in genere specificata chiamando il metodo [WebHostBuilderExtensions. \<TStartup> UseStartup](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) nel generatore host:

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

L'host fornisce i servizi disponibili al costruttore della classe `Startup`. L'app aggiunge servizi aggiuntivi tramite `ConfigureServices`. I servizi dell'host e dell'app saranno disponibili in `Configure` e nell'app.

`Startup`Quando si utilizza l' [host generico](xref:fundamentals/host/generic-host) () è possibile inserire nel costruttore solo i tipi di servizio seguenti <xref:Microsoft.Extensions.Hosting.IHostBuilder> :

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

La maggior parte dei servizi non è disponibile fino a quando non viene chiamato il metodo `Configure`.

### <a name="multiple-startup"></a>Avvio multiplo

Quando l'app definisce classi `Startup` separate per i diversi ambienti (ad esempio `StartupDevelopment`), la classe `Startup` appropriata viene selezionata durante il runtime. La classe il cui suffisso di nome corrisponde all'ambiente corrente ha la priorità. Se l'app viene eseguita nell'ambiente di sviluppo e include sia una classe `Startup` che una classe `StartupDevelopment`, viene usata la classe `StartupDevelopment`. Per altre informazioni, vedere [Usare più ambienti](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Per altre informazioni sull'host, vedere [L'host](xref:fundamentals/index#host). Per informazioni sulla gestione degli errori durante l'avvio, vedere [Gestione delle eccezioni durante l'avvio](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metodo ConfigureServices

Il metodo <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> è:

* facoltativo.
* Chiamato dall'host prima del metodo `Configure` per configurare i servizi dell'app.
* Dove le [opzioni di configurazione](xref:fundamentals/configuration/index) sono impostate per convenzione.

L'host può configurare alcuni servizi prima che vengano chiamati i metodi `Startup`. Per altre informazioni, vedere [L'host](xref:fundamentals/index#host).

Per le funzionalità che richiedono l'installazione sostanziale, sono disponibili i metodi di estensione `Add{Service}` in <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. Ad esempio, **aggiungere** DbContext, **aggiungere** il valore predefinito Identity , **aggiungere** EntityFrameworkStores e **aggiungere** Razor pagine:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

L'aggiunta dei servizi al contenitore dei servizi li rende disponibili all'interno dell'app e nel metodo `Configure`. I servizi vengono risolti tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection) o da <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

## <a name="the-configure-method"></a>Metodo Configure

Il metodo <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> viene usato per specificare come risponde l'app alle richieste HTTP. La pipeline delle richieste viene configurata aggiungendo i componenti [middleware](xref:fundamentals/middleware/index) a un'istanza <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` è disponibile per il metodo `Configure` ma non viene registrato nel contenitore dei servizi. L'hosting crea `IApplicationBuilder` e lo passa direttamente a `Configure`.

I [modelli ASP.NET Core](/dotnet/core/tools/dotnet-new) configurano la pipeline con il supporto per:

* [Pagina delle eccezioni per sviluppatori](xref:fundamentals/error-handling#developer-exception-page)
* [Gestore di eccezioni](xref:fundamentals/error-handling#exception-handler-page)
* [Protocollo HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Reindirizzamento HTTPS](xref:security/enforcing-ssl)
* [File statici](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) e [ Razor pagine](xref:razor-pages/index)


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

L'esempio precedente è per le [ Razor pagine](xref:razor-pages/index). la versione di MVC è simile.

Ogni metodo di estensione `Use` aggiunge uno o più componenti middleware alla pipeline delle richieste. Ad esempio, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configura il [middleware](xref:fundamentals/middleware/index) per fornire i [file statici](xref:fundamentals/static-files).

Ogni componente del middleware è responsabile della chiamata del componente seguente nella pipeline o del corto circuito della catena, se necessario.

I servizi aggiuntivi, ad esempio `IWebHostEnvironment`, `ILoggerFactory` o qualsiasi elemento definito in `ConfigureServices`, possono essere specificati nella firma del metodo `Configure`. Questi servizi vengono inseriti se sono disponibili.

Per altre informazioni su come usare `IApplicationBuilder` e sull'ordine di elaborazione del middleware, vedere <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Configurare i servizi senza Startup

Per configurare i servizi e la pipeline di elaborazione delle richieste senza usare una classe `Startup`, chiamare i metodi pratici `ConfigureServices` e `Configure` sul generatore di host. Se vengono effettuate più chiamate a `ConfigureServices`, le chiamate vengono aggiunte l'una all'altra. In presenza di più chiamate del metodo `Configure` viene usata l'ultima chiamata di `Configure`.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a>Estendere l'avvio con filtri di avvio

Usare <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> :

* Per configurare middleware all'inizio o alla fine della pipeline di [configurazione](#the-configure-method) middleware di un'app senza una chiamata esplicita a `Use{Middleware}` . `IStartupFilter` viene usato da ASP.NET Core per aggiungere i valori predefiniti all'inizio della pipeline senza dover fare in modo che l'autore dell'app registri in modo esplicito il middleware predefinito. `IStartupFilter` consente a un componente diverso di chiamare per `Use{Middleware}` conto dell'autore dell'app.
* Per creare una pipeline di `Configure` metodi. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) può impostare un middleware da eseguire prima o dopo l'aggiunta del middleware dalle librerie.

`IStartupFilter` implementa <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, che riceve e restituisce un `Action<IApplicationBuilder>`. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> definisce una classe per configurare la pipeline delle richieste di un'app. Per altre informazioni, vedere [Creare una pipeline middleware con IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Ogni `IStartupFilter` può aggiungere uno o più middleware nella pipeline delle richieste. I filtri vengono richiamati nell'ordine in cui sono stati aggiunti al contenitore di servizi. Poiché i filtri possono aggiungere il middleware prima o dopo il passaggio del controllo al filtro successivo, i filtri vengono aggiunti all'inizio o alla fine della pipeline dell'app.

L'esempio seguente dimostra come registrare un middleware con `IStartupFilter`. Il middleware `RequestSetOptionsMiddleware` imposta un valore di opzioni da un parametro di stringa di query:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

`RequestSetOptionsMiddleware` è configurato nella classe `RequestSetOptionsStartupFilter`:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter` è registrato nel contenitore di servizi in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

Quando viene specificato un parametro di stringa di query per `option`, il middleware elabora l'assegnazione del valore prima che il middleware ASP.NET Core esegua il rendering della risposta.

L'ordine di esecuzione del middleware viene impostato in base all'ordine delle registrazioni `IStartupFilter`:

* Più implementazioni `IStartupFilter` possono interagire con gli stessi oggetti. Se l'ordinamento è un fattore importante, ordinare le registrazioni di servizio `IStartupFilter` in un ordine corrispondente a quello di esecuzione dei relativi middleware.
* Le librerie possono aggiungere middleware con una o più implementazioni `IStartupFilter` che viene eseguito prima o dopo altri middleware dell'app registrati con `IStartupFilter`. Per richiamare un middleware `IStartupFilter` prima che un middleware venga aggiunto da `IStartupFilter` di una libreria:

  * Posizionare la registrazione del servizio prima che la libreria venga aggiunta al contenitore di servizi.
  * Per richiamarlo in un secondo momento, posizionare la registrazione dei servizi dopo l'aggiunta della libreria.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Aggiungere elementi di configurazione all'avvio da un assembly esterno

Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app. Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Host](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a>Classe Startup

Le app ASP.NET Core usano una classe `Startup` denominata `Startup` per convenzione. La classe `Startup`:

* Include facoltativamente un metodo <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> per configurare i *servizi* dell'app. Un servizio è un componente riutilizzabile che fornisce la funzionalità delle app. I servizi  vengono registrati `ConfigureServices` e utilizzati nell'app tramite l' [inserimento DI dipendenze (di)](xref:fundamentals/dependency-injection) o <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .
* Include un metodo <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> per creare la pipeline di elaborazione delle richieste dell'app.

`ConfigureServices` e `Configure` sono chiamate dal runtime di ASP.NET Core all'avvio dell'app:

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

La classe `Startup` viene specificata al momento della compilazione dell'[host](xref:fundamentals/index#host) dell'app. La `Startup` classe viene in genere specificata chiamando il metodo [WebHostBuilderExtensions. \<TStartup> UseStartup](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) nel generatore host:

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

L'host fornisce i servizi disponibili al costruttore della classe `Startup`. L'app aggiunge servizi aggiuntivi tramite `ConfigureServices`. I servizi dell'host e dell'app saranno quindi disponibili in `Configure` e nell'app.

Un uso comune dell'[inserimento di dipendenze](xref:fundamentals/dependency-injection) nella classe `Startup` consiste nell'inserire:

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> per configurare i servizi in base all'ambiente.
* <xref:Microsoft.Extensions.Configuration.IConfiguration> per leggere la configurazione.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory> per creare un logger in `Startup.ConfigureServices`.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

La maggior parte dei servizi non è disponibile fino a quando non viene chiamato il metodo `Configure`.

### <a name="multiple-startup"></a>Avvio multiplo

Quando l'app definisce classi `Startup` separate per i diversi ambienti (ad esempio `StartupDevelopment`), la classe `Startup` appropriata viene selezionata durante il runtime. La classe il cui suffisso di nome corrisponde all'ambiente corrente ha la priorità. Se l'app viene eseguita nell'ambiente di sviluppo e include sia una classe `Startup` che una classe `StartupDevelopment`, viene usata la classe `StartupDevelopment`. Per altre informazioni, vedere [Usare più ambienti](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Per altre informazioni sull'host, vedere [L'host](xref:fundamentals/index#host). Per informazioni sulla gestione degli errori durante l'avvio, vedere [Gestione delle eccezioni durante l'avvio](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metodo ConfigureServices

Il metodo <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> è:

* facoltativo.
* Chiamato dall'host prima del metodo `Configure` per configurare i servizi dell'app.
* Dove le [opzioni di configurazione](xref:fundamentals/configuration/index) sono impostate per convenzione.

L'host può configurare alcuni servizi prima che vengano chiamati i metodi `Startup`. Per altre informazioni, vedere [L'host](xref:fundamentals/index#host).

Per le funzionalità che richiedono l'installazione sostanziale, sono disponibili i metodi di estensione `Add{Service}` in <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. Ad esempio, **aggiungere** DbContext, **aggiungere** il valore predefinito Identity , **aggiungere** EntityFrameworkStores e **aggiungere** Razor pagine:

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

L'aggiunta dei servizi al contenitore dei servizi li rende disponibili all'interno dell'app e nel metodo `Configure`. I servizi vengono risolti tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection) o da <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

Vedere [SetCompatibilityVersion](xref:mvc/compatibility-version) per altre informazioni su `SetCompatibilityVersion`.

## <a name="the-configure-method"></a>Metodo Configure

Il metodo <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> viene usato per specificare come risponde l'app alle richieste HTTP. La pipeline delle richieste viene configurata aggiungendo i componenti [middleware](xref:fundamentals/middleware/index) a un'istanza <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` è disponibile per il metodo `Configure` ma non viene registrato nel contenitore dei servizi. L'hosting crea `IApplicationBuilder` e lo passa direttamente a `Configure`.

I [modelli ASP.NET Core](/dotnet/core/tools/dotnet-new) configurano la pipeline con il supporto per:

* [Pagina delle eccezioni per sviluppatori](xref:fundamentals/error-handling#developer-exception-page)
* [Gestore di eccezioni](xref:fundamentals/error-handling#exception-handler-page)
* [Protocollo HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Reindirizzamento HTTPS](xref:security/enforcing-ssl)
* [File statici](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) e [ Razor pagine](xref:razor-pages/index)
* [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Ogni metodo di estensione `Use` aggiunge uno o più componenti middleware alla pipeline delle richieste. Ad esempio, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configura il [middleware](xref:fundamentals/middleware/index) per fornire i [file statici](xref:fundamentals/static-files).

Ogni componente del middleware è responsabile della chiamata del componente seguente nella pipeline o del corto circuito della catena, se necessario.

I servizi aggiuntivi, ad esempio `IHostingEnvironment` e `ILoggerFactory` o qualsiasi elemento definito in `ConfigureServices`, possono essere specificati nella firma del metodo `Configure`. Questi servizi vengono inseriti se sono disponibili.

Per altre informazioni su come usare `IApplicationBuilder` e sull'ordine di elaborazione del middleware, vedere <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Configurare i servizi senza Startup

Per configurare i servizi e la pipeline di elaborazione delle richieste senza usare una classe `Startup`, chiamare i metodi pratici `ConfigureServices` e `Configure` sul generatore di host. Se vengono effettuate più chiamate a `ConfigureServices`, le chiamate vengono aggiunte l'una all'altra. In presenza di più chiamate del metodo `Configure` viene usata l'ultima chiamata di `Configure`.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Estendere l'avvio con filtri di avvio

Usare <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> :

* Per configurare middleware all'inizio o alla fine della pipeline di [configurazione](#the-configure-method) middleware di un'app senza una chiamata esplicita a `Use{Middleware}` . `IStartupFilter` viene usato da ASP.NET Core per aggiungere i valori predefiniti all'inizio della pipeline senza dover fare in modo che l'autore dell'app registri in modo esplicito il middleware predefinito. `IStartupFilter` consente di chiamare un componente diverso `Use{Middleware}` per conto dell'autore dell'app.
* Per creare una pipeline di `Configure` metodi. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) può impostare un middleware da eseguire prima o dopo l'aggiunta del middleware dalle librerie.

`IStartupFilter` implementa <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, che riceve e restituisce un `Action<IApplicationBuilder>`. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> definisce una classe per configurare la pipeline delle richieste di un'app. Per altre informazioni, vedere [Creare una pipeline middleware con IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Ogni `IStartupFilter` può aggiungere uno o più middleware nella pipeline delle richieste. I filtri vengono richiamati nell'ordine in cui sono stati aggiunti al contenitore di servizi. Poiché i filtri possono aggiungere il middleware prima o dopo il passaggio del controllo al filtro successivo, i filtri vengono aggiunti all'inizio o alla fine della pipeline dell'app.

L'esempio seguente dimostra come registrare un middleware con `IStartupFilter`. Il middleware `RequestSetOptionsMiddleware` imposta un valore di opzioni da un parametro di stringa di query:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

`RequestSetOptionsMiddleware` è configurato nella classe `RequestSetOptionsStartupFilter`:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter` è registrato nel contenitore di servizi in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

Quando viene specificato un parametro di stringa di query per `option`, il middleware elabora l'assegnazione del valore prima che il middleware ASP.NET Core esegua il rendering della risposta.

L'ordine di esecuzione del middleware viene impostato in base all'ordine delle registrazioni `IStartupFilter`:

* Più implementazioni `IStartupFilter` possono interagire con gli stessi oggetti. Se l'ordinamento è un fattore importante, ordinare le registrazioni di servizio `IStartupFilter` in un ordine corrispondente a quello di esecuzione dei relativi middleware.
* Le librerie possono aggiungere middleware con una o più implementazioni `IStartupFilter` che viene eseguito prima o dopo altri middleware dell'app registrati con `IStartupFilter`. Per richiamare un middleware `IStartupFilter` prima che un middleware venga aggiunto da `IStartupFilter` di una libreria:

  * Posizionare la registrazione del servizio prima che la libreria venga aggiunta al contenitore di servizi.
  * Per richiamarlo in un secondo momento, posizionare la registrazione dei servizi dopo l'aggiunta della libreria.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Aggiungere elementi di configurazione all'avvio da un assembly esterno

Un'implementazione <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> consente l'aggiunta di miglioramenti a un'app all'avvio, da un assembly esterno alla classe `Startup` dell'app. Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Host](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end
