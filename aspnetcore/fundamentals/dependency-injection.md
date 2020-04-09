---
title: Inserimento delle dipendenze in ASP.NET Core
author: rick-anderson
description: Informazioni su come ASP.NET Core implementa l'inserimento delle dipendenze e su come usare questa funzionalità.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: 943ea30c2e4887638f69b6dcdb7e323bcee40240
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405983"
---
# <a name="dependency-injection-in-aspnet-core"></a>Inserimento delle dipendenze in ASP.NET Core

Di [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core supporta il modello progettuale software per l'inserimento delle dipendenze, ovvero una tecnica per ottenere l'[inversione del controllo (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra classi e relative dipendenze.

Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Panoramica dell'inserimento delle dipendenze

Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto. Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe. La classe `MyDependency` è una dipendenza della classe `IndexModel`:

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

La classe crea e dipende direttamente dall'istanza `MyDependency`. Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:

* Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.
* Se `MyDependency` ha dipendenze, devono essere configurate dalla classe. In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.
* È difficile eseguire unit test di questa implementazione. L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.

L'inserimento delle dipendenze consente di risolvere questi problemi tramite:

* L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.
* La registrazione della dipendenza in un contenitore di servizi. ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>. I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.
* L'*inserimento* del servizio nel costruttore della classe in cui viene usato. Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.

Nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` richiede <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore. Non è insolito usare l'inserimento delle dipendenze in modo concatenato. Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze. Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto. Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.

`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi. `IMyDependency` viene registrato in `Startup.ConfigureServices`. `ILogger<TCategoryName>`viene registrato dall'infrastruttura di astrazione di registrazione, pertanto è un [servizio fornito dal framework](#framework-provided-services) registrato per impostazione predefinita dal framework.

Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`. La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta. Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi. Ad esempio, `services.AddMvc()` aggiunge i servizi richiesti da Razor Pages e MVC. È consigliabile che le app seguano questa convenzione. Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.

Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato. Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.

Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Servizi inseriti nell'avvio

Solo i seguenti tipi di servizio `Startup` possono essere inseriti<xref:Microsoft.Extensions.Hosting.IHostBuilder>nel costruttore quando si utilizza l'host generico ( ):

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

I servizi possono `Startup.Configure`essere iniettati in:

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Per altre informazioni, vedere <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Servizi forniti dal framework

Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET MVC. Inizialmente, `IServiceCollection` i `ConfigureServices` servizi forniti a hanno definito dal framework a seconda di [come è stato configurato l'host.](xref:fundamentals/index#host) Non è raro che un'app basata su un modello ASP.NET Core disponga di centinaia di servizi registrati dal framework. Nella tabella seguente è elencato un piccolo esempio di servizi registrati nel framework.

| Tipo di servizio | Durata |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Temporaneo |
| `IHostApplicationLifetime` | Singleton |
| `IWebHostEnvironment` | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Temporaneo |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Temporaneo |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Temporaneo |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Registrare servizi aggiuntivi con metodi di estensioneRegister additional services with extension methods

Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio. Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>usando i metodi di estensione [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

Per altre informazioni, vedere la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> nella documentazione delle API.

## <a name="service-lifetimes"></a>Durate dei servizi

Scegliere una durata appropriata per ogni servizio registrato. I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:

### <a name="transient"></a>Temporaneo

I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi. Questa impostazione di durata è consigliata per i servizi semplici senza stati.

### <a name="scoped"></a>Con ambito

I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).

> [!WARNING]
> Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`. Evitare l'inserimento tramite il costruttore, che impone al servizio il comportamento singleton. Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

I servizi con durata singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) vengono creati la prima volta che vengono richiesti (o quando si esegue `Startup.ConfigureServices` e viene specificata un'istanza con la registrazione del servizio). Tutte le richieste successive usano la stessa istanza. Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio. Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.

> [!WARNING]
> Può essere pericoloso risolvere un servizio con ambito da un singleton. Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.

## <a name="service-registration-methods"></a>Metodi di registrazione del servizio

I metodi di estensione per la registrazione del servizio offrono overload utili in scenari specifici.

| Metodo | Automatico<br>object<br>eliminazione | Multipli<br>implementazioni | Pass args |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Esempio:<br>`services.AddSingleton<IMyDep, MyDep>();` | Sì | Sì | No |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Esempi:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Sì | Sì | Sì |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Esempio:<br>`services.AddSingleton<MyDep>();` | Sì | No | No |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Esempi:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Sì | Sì |
| `AddSingleton(new {IMPLEMENTATION})`<br>Esempi:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | No | No | Sì |

Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services). Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).

I metodi `TryAdd{LIFETIME}` registrano il servizio solo se esiste già un'implementazione registrata.

Nell'esempio seguente, la prima riga registra `MyDependency` per `IMyDependency`. La seconda riga non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Per altre informazioni, vedere:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

I metodi [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*. Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`. Durante la registrazione di servizi, lo sviluppatore desidera aggiungere un'istanza solo se non ne è già stata aggiunta una dello stesso tipo. In genere, questo metodo viene utilizzato dagli autori di librerie per evitare di registrare due copie di un'istanza nel contenitore.

Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`. La seconda riga registra `MyDep` per `IMyDep2`. La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Comportamento dell'inserimento del costruttore

I servizi possono essere risolti con due meccanismi:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze. `ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.

I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.

Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities`, l'inserimento del costruttore richiede un costruttore *pubblico*.

Quando i servizi vengono risolti da `ActivatorUtilities`, l'inserimento del costruttore richiede l'esistenza di un solo costruttore applicabile. Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.

## <a name="entity-framework-contexts"></a>Contesti di Entity Framework

I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client. La durata predefinita è con ambito se non viene specificata una durata con un overload [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) quando si registra il contesto del database. I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.

## <a name="lifetime-and-registration-options"></a>Opzioni di durata e di registrazione

Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`. A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Le interfacce vengono implementate nella classe `Operation`. Il costruttore `Operation` genera un GUID se non ne viene fornito uno:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`. Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.

* Se i servizi temporanei vengono creati quando vengono richiesti dal contenitore, `OperationId` del servizio `IOperationTransient` è diverso da `OperationId` di `OperationService`. `OperationService` riceve una nuova istanza della classe `IOperationTransient`. La nuova istanza restituisce un `OperationId` diverso.
* Se i servizi con ambito vengono creati per ogni richiesta client, `OperationId` del servizio `IOperationScoped` corrisponde a `OperationService` all'interno di una richiesta client. In tutte le richieste client entrambi i servizi condividono un valore `OperationId` diverso.
* Se i servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste client e tutti i servizi, `OperationId` rimane costante in tutte le richieste di servizio.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`. È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).

L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste. L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`. La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

L'output seguente mostra i risultati delle due richieste:

**Prima richiesta:**

Operazioni del controller:

Transient: d233e165-f417-469b-a866-1cf1935d2518  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Operazioni di `OperationService`:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

**Seconda richiesta:**

Operazioni del controller:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Operazioni di `OperationService`:

Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:

* Gli oggetti *temporanei* sono sempre diversi. Il valore `OperationId` temporaneo per la prima e la seconda richiesta client è diverso per entrambe le operazioni `OperationService` e in tutte le richieste client. Viene fornita una nuova istanza per ogni richiesta di servizio e richiesta client.
* Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta client, ma variano nelle diverse richieste client.
* Gli oggetti *Singleton* sono gli stessi per ogni `Operation` oggetto e `Startup.ConfigureServices`ogni richiesta, indipendentemente dal fatto che un'istanza venga fornita in .

## <a name="call-services-from-main"></a>Chiamare i servizi da main

Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app. Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione. L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a>Convalida dell'ambito

Quando l'app è in esecuzione nell'ambiente di sviluppo e chiama CreateDefaultBuilder per compilare l'host, il provider di servizi predefinito esegue controlli per verificare che:When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:

* I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.
* I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.

Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>. La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.

I servizi con ambito vengono eliminati dal contenitore che li ha creati. Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server. La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.

Per altre informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Servizi di richiesta

I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app. Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.

In generale, l'app non deve usare queste proprietà direttamente. Richiedere invece i tipi necessari alle classi tramite i costruttori di classe e consentire al framework di inserire le dipendenze. Si ottengono classi più facili da testare.

> [!NOTE]
> È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.

## <a name="design-services-for-dependency-injection"></a>Progettare i servizi per l'inserimento di dipendenze

Le procedure consigliate prevedono di:

* Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.
* Evitare classi e membri statici con stato. Progetta invece le app per usare i servizi singleton, evitando di creare uno stato globale.
* Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi. La creazione diretta di istanze associa il codice a una determinata implementazione.
* Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.

Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola). Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe. Tenere presente che le classi del modello di pagina Razor Pages e le classi del controller MVC devono essere incentrate sulle problematiche dell'interfaccia utente. Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Eliminazione dei servizi

Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati. Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.

Nell'esempio seguente i servizi vengono creati dal contenitore dei servizi ed eliminati automaticamente:In the following example, the services are created by the service container and disposed automatically:

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

Nell'esempio seguente:

* Le istanze del servizio non vengono create dal contenitore del servizio.
* Le durate del servizio previste non sono note dal framework.
* Il framework non elimina automaticamente i servizi.
* Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino all'arresto dell'app.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

Per una descrizione delle opzioni di eliminazione dei servizi, vedere [Quattro modi per eliminare IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).

## <a name="default-service-container-replacement"></a>Sostituzione del contenitore di servizi predefinito

Il contenitore di servizi predefinito è progettato per soddisfare le esigenze del framework e della maggior parte delle app consumer. È consigliabile usare il contenitore predefinito, a meno che non sia necessaria una funzionalità specifica non supportata dal contenitore incorporato, ad esempio:We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:

* Inserimento di proprietà
* Inserimento in base al nome
* Contenitori figlio
* Gestione della durata personalizzata
* Supporto di `Func<T>` per l'inizializzazione differita
* Registrazione basata su convenzioni

I seguenti contenitori di terze parti possono essere usati con le app ASP.NET Core:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Laura](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Scatola di lavoro](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Thread safety

Creare servizi singleton thread-safe. Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.

Non è necessario che il metodo factory del singolo servizio, ad esempio il secondo argomento per [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), sia thread-safe. Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.

## <a name="recommendations"></a>Consigli

* La risoluzione basata sui servizi `async/await` e `Task` non è supportata. Dato che C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.

* Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio. Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio. La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options). Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti. È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.

* Evitare l'accesso statico ai servizi (ad esempio, la tipizzazione statica [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) da usare in altre posizioni).

* Evitare di usare il *modello di localizzatore del servizio*. Ad esempio, non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando è invece possibile usare l'inserimento delle dipendenze:

  **Errato:**

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  **Corretto**:

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* Un'altra variazione del localizzatore del servizio da evitare è inserire una factory che risolve le dipendenze in fase di esecuzione. Queste procedure combinano le strategie di [inversione del controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).

* Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate. Le eccezioni sono rare e principalmente si tratta di casi speciali all'interno del framework stesso.

L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali. Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Explicit Dependencies Principle (Principio delle dipendenze esplicite)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)](https://www.martinfowler.com/articles/injection.html)
* [How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core supporta il modello progettuale software per l'inserimento delle dipendenze, ovvero una tecnica per ottenere l'[inversione del controllo (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra classi e relative dipendenze.

Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.

[Visualizzare o scaricare codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Panoramica dell'inserimento delle dipendenze

Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto. Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe. La classe `MyDependency` è una dipendenza della classe `IndexModel`:

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

La classe crea e dipende direttamente dall'istanza `MyDependency`. Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:

* Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.
* Se `MyDependency` ha dipendenze, devono essere configurate dalla classe. In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.
* È difficile eseguire unit test di questa implementazione. L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.

L'inserimento delle dipendenze consente di risolvere questi problemi tramite:

* L'uso di un'interfaccia o di una classe di base per astrarre l'implementazione delle dipendenze.
* La registrazione della dipendenza in un contenitore di servizi. ASP.NET Core offre il contenitore di servizi predefinito <xref:System.IServiceProvider>. I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.
* L'*inserimento* del servizio nel costruttore della classe in cui viene usato. Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.

Nell'[app di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` richiede <xref:Microsoft.Extensions.Logging.ILogger`1> nel costruttore. Non è insolito usare l'inserimento delle dipendenze in modo concatenato. Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze. Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto. Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.

`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi. `IMyDependency` viene registrato in `Startup.ConfigureServices`. `ILogger<TCategoryName>`viene registrato dall'infrastruttura di astrazione di registrazione, pertanto è un [servizio fornito dal framework](#framework-provided-services) registrato per impostazione predefinita dal framework.

Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`. La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta. Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi. Ad esempio, `services.AddMvc()` aggiunge i servizi richiesti da Razor Pages e MVC. È consigliabile che le app seguano questa convenzione. Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.

Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato. Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.

Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Servizi inseriti nell'avvio

Solo i seguenti tipi di servizio `Startup` possono essere inseriti<xref:Microsoft.Extensions.Hosting.IHostBuilder>nel costruttore quando si utilizza l'host generico ( ):

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

I servizi possono `Startup.Configure`essere iniettati in:

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Per altre informazioni, vedere <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Servizi forniti dal framework

Il `Startup.ConfigureServices` metodo è responsabile della definizione dei servizi utilizzati dall'app, incluse le funzionalità della piattaforma, ad esempio Entity Framework Core e ASP.NET MVC. Inizialmente, `IServiceCollection` i `ConfigureServices` servizi forniti a hanno definito dal framework a seconda di [come è stato configurato l'host.](xref:fundamentals/index#host) Non è raro che un'app basata su un modello ASP.NET Core disponga di centinaia di servizi registrati dal framework. Nella tabella seguente è elencato un piccolo esempio di servizi registrati nel framework.

| Tipo di servizio | Durata |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Temporaneo |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Temporaneo |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Temporaneo |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Temporaneo |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Registrare servizi aggiuntivi con metodi di estensioneRegister additional services with extension methods

Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio. Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>usando i metodi di estensione [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

Per altre informazioni, vedere la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> nella documentazione delle API.

## <a name="service-lifetimes"></a>Durate dei servizi

Scegliere una durata appropriata per ogni servizio registrato. I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:

### <a name="transient"></a>Temporaneo

I servizi con durata temporanea (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) vengono creati ogni volta che vengono richiesti dal contenitore dei servizi. Questa impostazione di durata è consigliata per i servizi semplici senza stati.

### <a name="scoped"></a>Con ambito

I servizi con durata con ambito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) vengono creati una volta per ogni richiesta client (connessione).

> [!WARNING]
> Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`. Evitare l'inserimento tramite il costruttore, che impone al servizio il comportamento singleton. Per altre informazioni, vedere <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

I servizi con durata singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) vengono creati la prima volta che vengono richiesti (o quando si esegue `Startup.ConfigureServices` e viene specificata un'istanza con la registrazione del servizio). Tutte le richieste successive usano la stessa istanza. Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio. Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.

> [!WARNING]
> Può essere pericoloso risolvere un servizio con ambito da un singleton. Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.

## <a name="service-registration-methods"></a>Metodi di registrazione del servizio

I metodi di estensione per la registrazione del servizio offrono overload utili in scenari specifici.

| Metodo | Automatico<br>object<br>eliminazione | Multipli<br>implementazioni | Pass args |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Esempio:<br>`services.AddSingleton<IMyDep, MyDep>();` | Sì | Sì | No |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Esempi:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Sì | Sì | Sì |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Esempio:<br>`services.AddSingleton<MyDep>();` | Sì | No | No |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Esempi:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Sì | Sì |
| `AddSingleton(new {IMPLEMENTATION})`<br>Esempi:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | No | No | Sì |

Per ulteriori informazioni sull'eliminazione dei tipi, vedere la sezione [Eliminazione dei servizi](#disposal-of-services). Uno scenario comune per più implementazioni è costituito dai [tipi di simulazioni per i test](xref:test/integration-tests#inject-mock-services).

I metodi `TryAdd{LIFETIME}` registrano il servizio solo se esiste già un'implementazione registrata.

Nell'esempio seguente, la prima riga registra `MyDependency` per `IMyDependency`. La seconda riga non ha alcun effetto perché `IMyDependency` dispone già di un'implementazione registrata:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Per altre informazioni, vedere:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

I metodi [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrano il servizio solo se non esiste già un'implementazione *dello stesso tipo*. Più servizi vengono risolti tramite `IEnumerable<{SERVICE}>`. Durante la registrazione di servizi, lo sviluppatore desidera aggiungere un'istanza solo se non ne è già stata aggiunta una dello stesso tipo. In genere, questo metodo viene utilizzato dagli autori di librerie per evitare di registrare due copie di un'istanza nel contenitore.

Nell'esempio seguente, la prima riga registra `MyDep` per `IMyDep1`. La seconda riga registra `MyDep` per `IMyDep2`. La seconda riga non ha alcun effetto perché `IMyDep1` dispone già di un'implementazione registrata di `MyDep`:

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Comportamento dell'inserimento del costruttore

I servizi possono essere risolti con due meccanismi:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze. `ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.

I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.

Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities`, l'inserimento del costruttore richiede un costruttore *pubblico*.

Quando i servizi vengono risolti da `ActivatorUtilities`, l'inserimento del costruttore richiede l'esistenza di un solo costruttore applicabile. Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.

## <a name="entity-framework-contexts"></a>Contesti di Entity Framework

I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes) perché le operazioni di database delle app Web hanno di solito come ambito la richiesta client. La durata predefinita è con ambito se non viene specificata una durata con un overload [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) quando si registra il contesto del database. I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.

## <a name="lifetime-and-registration-options"></a>Opzioni di durata e di registrazione

Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`. A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Le interfacce vengono implementate nella classe `Operation`. Il costruttore `Operation` genera un GUID se non ne viene fornito uno:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`. Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.

* Se i servizi temporanei vengono creati quando vengono richiesti dal contenitore, `OperationId` del servizio `IOperationTransient` è diverso da `OperationId` di `OperationService`. `OperationService` riceve una nuova istanza della classe `IOperationTransient`. La nuova istanza restituisce un `OperationId` diverso.
* Se i servizi con ambito vengono creati per ogni richiesta client, `OperationId` del servizio `IOperationScoped` corrisponde a `OperationService` all'interno di una richiesta client. In tutte le richieste client entrambi i servizi condividono un valore `OperationId` diverso.
* Se i servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste client e tutti i servizi, `OperationId` rimane costante in tutte le richieste di servizio.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`. È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).

L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste. L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`. La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

L'output seguente mostra i risultati delle due richieste:

**Prima richiesta:**

Operazioni del controller:

Transient: d233e165-f417-469b-a866-1cf1935d2518  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Operazioni di `OperationService`:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

**Seconda richiesta:**

Operazioni del controller:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Operazioni di `OperationService`:

Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:

* Gli oggetti *temporanei* sono sempre diversi. Il valore `OperationId` temporaneo per la prima e la seconda richiesta client è diverso per entrambe le operazioni `OperationService` e in tutte le richieste client. Viene fornita una nuova istanza per ogni richiesta di servizio e richiesta client.
* Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta client, ma variano nelle diverse richieste client.
* Gli oggetti *Singleton* sono gli stessi per ogni `Operation` oggetto e `Startup.ConfigureServices`ogni richiesta, indipendentemente dal fatto che un'istanza venga fornita in .

## <a name="call-services-from-main"></a>Chiamare i servizi da main

Creare un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>IServiceScope con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) per risolvere un servizio con ambito nell'ambito di applicazione dell'app. Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione. L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a>Convalida dell'ambito

Quando l'app viene eseguita nell'ambiente di sviluppo, il provider di servizi predefinito esegue controlli per verificare che:

* I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.
* I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.

Il provider di servizi radice venga creato con la chiamata di <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>. La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.

I servizi con ambito vengono eliminati dal contenitore che li ha creati. Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server. La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.

Per altre informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Servizi di richiesta

I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app. Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.

In generale, l'app non deve usare queste proprietà direttamente. Richiedere invece i tipi richiesti dalle classi tramite i costruttori di classi e consentire al framework di inserire le dipendenze. Si ottengono classi più facili da testare.

> [!NOTE]
> È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.

## <a name="design-services-for-dependency-injection"></a>Progettare i servizi per l'inserimento di dipendenze

Le procedure consigliate prevedono di:

* Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.
* Evitare classi e membri statici con stato. Progetta invece le app per usare i servizi singleton, evitando di creare uno stato globale.
* Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi. La creazione diretta di istanze associa il codice a una determinata implementazione.
* Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.

Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola). Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe. Tenere presente che le classi del modello di pagina Razor Pages e le classi del controller MVC devono essere incentrate sulle problematiche dell'interfaccia utente. Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Eliminazione dei servizi

Il contenitore chiama <xref:System.IDisposable.Dispose*> per i tipi <xref:System.IDisposable> creati. Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.

Nell'esempio seguente i servizi vengono creati dal contenitore dei servizi ed eliminati automaticamente:In the following example, the services are created by the service container and disposed automatically:

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

Nell'esempio seguente:

* Le istanze del servizio non vengono create dal contenitore del servizio.
* Le durate del servizio previste non sono note dal framework.
* Il framework non elimina automaticamente i servizi.
* Se i servizi non vengono eliminati in modo esplicito nel codice dello sviluppatore, vengono mantenuti fino all'arresto dell'app.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a>Sostituzione del contenitore di servizi predefinito

Il contenitore di servizi predefinito è progettato per soddisfare le esigenze del framework e della maggior parte delle app consumer. È consigliabile usare il contenitore predefinito, a meno che non sia necessaria una funzionalità specifica non supportata dal contenitore incorporato, ad esempio:We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:

* Inserimento di proprietà
* Inserimento in base al nome
* Contenitori figlio
* Gestione della durata personalizzata
* Supporto di `Func<T>` per l'inizializzazione differita
* Registrazione basata su convenzioni

I seguenti contenitori di terze parti possono essere usati con le app ASP.NET Core:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Laura](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Scatola di lavoro](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Thread safety

Creare servizi singleton thread-safe. Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.

Non è necessario che il metodo factory del singolo servizio, ad esempio il secondo argomento per [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), sia thread-safe. Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.

## <a name="recommendations"></a>Consigli

* La risoluzione basata sui servizi `async/await` e `Task` non è supportata. Dato che C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.

* Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio. Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio. La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options). Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti. È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.

* Evitare l'accesso statico ai servizi (ad esempio, la tipizzazione statica [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) da usare in altre posizioni).

* Evitare di utilizzare il modello di *localizzatore*del servizio , che combina le strategie [di inversione del controllo.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)

  * Non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando è possibile usare DI invece:Do't invoke to obtain a service instance when you can use DI instead:

    **Errato:**

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    **Corretto**:

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * Evitare di inserire una factory che <xref:System.IServiceProvider.GetService*>risolve le dipendenze in fase di esecuzione utilizzando .

* Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate. Le eccezioni sono rare e principalmente si tratta di casi speciali all'interno del framework stesso.

L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali. Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Explicit Dependencies Principle (Principio delle dipendenze esplicite)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)](https://www.martinfowler.com/articles/injection.html)
* [How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)

::: moniker-end
