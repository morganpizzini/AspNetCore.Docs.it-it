---
title: BlazorInserimento di dipendenze ASP.NET Core
author: guardrex
description: Informazioni su come le Blazor app possono inserire i servizi nei componenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 0e99e2e3e2dafae0c35d2cfe6903bf4f511f5dc1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402884"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>BlazorInserimento di dipendenze ASP.NET Core

Di [Rainer Stropek](https://www.timecockpit.com) e [Mike entusiasmanti](https://github.com/mjrousos)

Blazorsupporta l' [inserimento di dipendenze](xref:fundamentals/dependency-injection). Le app possono usare i servizi predefiniti inserendoli in componenti. Le app possono anche definire e registrare servizi personalizzati e renderli disponibili nell'app tramite DI.

DI è una tecnica per accedere ai servizi configurati in una posizione centrale. Questa operazione può essere utile nelle Blazor app per:

* Condividere una singola istanza di una classe di servizio in molti componenti, noti come un servizio *singleton* .
* Separare i componenti da classi di servizi concrete usando astrazioni di riferimento. Si consideri ad esempio un'interfaccia `IDataAccess` per l'accesso ai dati nell'app. L'interfaccia viene implementata da una `DataAccess` classe concreta e registrata come servizio nel contenitore del servizio dell'app. Quando un componente usa il per ricevere un' `IDataAccess` implementazione di, il componente non è associato al tipo concreto. L'implementazione può essere scambiata, ad esempio per un'implementazione fittizia negli unit test.

## <a name="default-services"></a>Servizi predefiniti

I servizi predefiniti vengono aggiunti automaticamente alla raccolta di servizi dell'app.

| Servizio | Durata | Descrizione |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Temporaneo | Fornisce metodi per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata da un URI.<br><br>L'istanza di <xref:System.Net.Http.HttpClient> in un' Blazor WebAssembly applicazione utilizza il browser per gestire il traffico HTTP in background.<br><br>Blazor Serverper impostazione predefinita, le app non includono un <xref:System.Net.Http.HttpClient> configurato come servizio. Fornire un <xref:System.Net.Http.HttpClient> a un' Blazor Server app.<br><br>Per altre informazioni, vedere <xref:blazor/call-web-api>. |
| <xref:Microsoft.JSInterop.IJSRuntime> | Singleton ( Blazor WebAssembly )<br>Con ambito ( Blazor Server ) | Rappresenta un'istanza di un runtime JavaScript in cui vengono inviate le chiamate a JavaScript. Per altre informazioni, vedere <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton ( Blazor WebAssembly )<br>Con ambito ( Blazor Server ) | Contiene gli helper per lavorare con gli URI e lo stato di navigazione. Per ulteriori informazioni, vedere [URI e Helper dello stato di navigazione](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Un provider di servizi personalizzato non fornisce automaticamente i servizi predefiniti elencati nella tabella. Se si usa un provider di servizi personalizzato e si richiede uno dei servizi indicati nella tabella, aggiungere i servizi necessari al nuovo provider di servizi.

## <a name="add-services-to-an-app"></a>Aggiungere servizi a un'app

### Blazor WebAssembly

Configurare i servizi per la raccolta di servizi dell'app nel `Main` metodo di `Program.cs` . Nell'esempio seguente l' `MyDependency` implementazione è registrata per `IMyDependency` :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

Una volta compilato l'host, è possibile accedere ai servizi dall'ambito radice prima DI eseguire il rendering di tutti i componenti. Questa operazione può essere utile per eseguire la logica di inizializzazione prima del rendering del contenuto:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

L'host fornisce anche un'istanza di configurazione centrale per l'app. Basandosi sull'esempio precedente, l'URL del servizio meteo viene passato da un'origine di configurazione predefinita (ad esempio, `appsettings.json` ) a `InitializeWeatherAsync` :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

Dopo aver creato una nuova app, esaminare il `Startup.ConfigureServices` Metodo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Al <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> metodo viene passato un oggetto <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , che è un elenco di oggetti del descrittore del servizio ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ). I servizi vengono aggiunti fornendo descrittori del servizio alla raccolta di servizi. Nell'esempio seguente viene illustrato il concetto con l' `IDataAccess` interfaccia e la relativa implementazione concreta `DataAccess` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Durata del servizio

I servizi possono essere configurati con le durate mostrate nella tabella seguente.

| Durata | Descrizione |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssemblyAttualmente le app non hanno un concetto di ambiti di. `Scoped`-i servizi registrati si comportano come `Singleton` servizi. Tuttavia, il Blazor Server modello di hosting supporta il `Scoped` ciclo di vita. Nelle Blazor Server app, la registrazione di un servizio con ambito ha come ambito la *connessione*. Per questo motivo, è preferibile usare i servizi con ambito per i servizi che devono avere come ambito l'utente corrente, anche se l'obiettivo corrente è eseguire sul lato client nel browser. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | La creazione di una *singola istanza* del servizio. Tutti i componenti che richiedono un `Singleton` servizio ricevono un'istanza dello stesso servizio. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Ogni volta che un componente ottiene un'istanza di un `Transient` servizio dal contenitore del servizio, riceve una *nuova istanza* del servizio. |

Il sistema DI è basato sul sistema DI ASP.NET Core. Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Richiedere un servizio in un componente

Una volta aggiunti i servizi alla raccolta di servizi, inserire i servizi nei componenti utilizzando la direttiva [ \@ Inject](xref:mvc/views/razor#inject) Razor . [`@inject`](xref:mvc/views/razor#inject)dispone di due parametri:

* Tipo: tipo di servizio da inserire.
* Property: nome della proprietà che riceve il servizio app inserito. La proprietà non richiede la creazione manuale. Il compilatore crea la proprietà.

Per altre informazioni, vedere <xref:mvc/views/dependency-injection>.

Usare più [`@inject`](xref:mvc/views/razor#inject) istruzioni per inserire servizi diversi.

Nell'esempio seguente viene illustrato come utilizzare [`@inject`](xref:mvc/views/razor#inject) . Il servizio che implementa `Services.IDataAccess` viene inserito nella proprietà del componente `DataRepository` . Si noti come il codice usa solo l' `IDataAccess` astrazione:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

Internamente, la proprietà generata ( `DataRepository` ) utilizza l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo. In genere, questo attributo non viene utilizzato direttamente. Se è necessaria una classe base per i componenti e le proprietà inserite sono necessarie anche per la classe base, aggiungere manualmente l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Nei componenti derivati dalla classe di base, la [`@inject`](xref:mvc/views/razor#inject) direttiva non è obbligatoria. La <xref:Microsoft.AspNetCore.Components.InjectAttribute> classe della classe base è sufficiente:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Usare l'inserimento DI dipendenze nei servizi

Servizi complessi potrebbe richiedere servizi aggiuntivi. Nell'esempio precedente, `DataAccess` potrebbe richiedere il <xref:System.Net.Http.HttpClient> servizio predefinito. [`@inject`](xref:mvc/views/razor#inject)(o l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo) non è disponibile per l'uso nei servizi. È necessario usare invece l' *inserimento del costruttore* . I servizi necessari vengono aggiunti aggiungendo parametri al costruttore del servizio. Quando si crea il servizio, vengono riconosciuti i servizi richiesti nel costruttore e forniti DI conseguenza.

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

Prerequisiti per l'inserimento del costruttore:

* È necessario che esista un costruttore i cui argomenti possono essere tutti soddisfatti da DI. Sono consentiti parametri aggiuntivi non analizzati da DI se specificano i valori predefiniti.
* Il costruttore applicabile deve essere `public` .
* È necessario che esista un costruttore applicabile. In caso di ambiguità, viene generata un'eccezione.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Classi di componenti di base dell'utilità per gestire un ambito DI

Nelle app ASP.NET Core, i servizi con ambito hanno in genere come ambito la richiesta corrente. Al termine della richiesta, tutti i servizi con ambito o temporaneo vengono eliminati dal sistema DI. Nelle Blazor Server app, l'ambito della richiesta dura per la durata della connessione client, che può comportare la permanenza di servizi temporanei e con ambito più a lungo del previsto. Nelle Blazor WebAssembly app i servizi registrati con una durata con ambito vengono considerati come singleton, quindi vivono più a lungo rispetto ai servizi con ambito nelle app ASP.NET Core tipiche.

> [!NOTE]
> Per rilevare servizi temporanei monouso in un'app, vedere la sezione [rilevare gli eliminabili temporanei](#detect-transient-disposables) .

Un approccio che limita la durata di un servizio nelle Blazor app è l'uso del <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo. <xref:Microsoft.AspNetCore.Components.OwningComponentBase>è un tipo astratto derivato da <xref:Microsoft.AspNetCore.Components.ComponentBase> che consente di creare un ambito di che corrisponde alla durata del componente. Con questo ambito, è possibile usare i servizi DI i con una durata con ambito e fare in modo che siano attivi fino a quando il componente. Quando il componente viene eliminato definitivamente, vengono eliminati anche i servizi del provider di servizi con ambito. Questa operazione può essere utile per i servizi che:

* È necessario riutilizzarlo all'interno di un componente, perché la durata temporanea non è appropriata.
* Non devono essere condivise tra i componenti, perché la durata singleton non è appropriata.

Sono disponibili due versioni del <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo:

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase>è un elemento figlio astratto e disposable del <xref:Microsoft.AspNetCore.Components.ComponentBase> tipo con una <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> proprietà protetta di tipo <xref:System.IServiceProvider> . Questo provider può essere utilizzato per risolvere i servizi che hanno come ambito la durata del componente.

  I servizi DI inserimento nel componente usando [`@inject`](xref:mvc/views/razor#inject) o l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attributo non vengono creati nell'ambito del componente. Per utilizzare l'ambito del componente, i servizi devono essere risolti utilizzando <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> o <xref:System.IServiceProvider.GetService%2A> . Tutti i servizi risolti utilizzando il <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider hanno le dipendenze fornite dallo stesso ambito.

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>deriva da <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e aggiunge una <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> proprietà che restituisce un'istanza di `T` dal provider dell'ambito di. Questo tipo è un modo pratico per accedere ai servizi con ambito senza usare un'istanza di <xref:System.IServiceProvider> quando è presente un servizio primario richiesto dall'app dal contenitore di inserimento delle dipendenze usando l'ambito del componente. La <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> proprietà è disponibile, in modo che l'app possa ottenere i servizi di altri tipi, se necessario.

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Uso di Entity Framework DbContext da DI

Un tipo di servizio comune da recuperare da un in app Web è Entity Framework oggetti (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> . <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>Per impostazione predefinita, la registrazione dei servizi EF tramite aggiunge <xref:Microsoft.EntityFrameworkCore.DbContext> come servizio con ambito. La registrazione come servizio con ambito può causare problemi nelle Blazor app perché causa <xref:Microsoft.EntityFrameworkCore.DbContext> la lunga durata delle istanze e la condivisione nell'app. <xref:Microsoft.EntityFrameworkCore.DbContext>non è thread-safe e non deve essere usato simultaneamente.

A seconda dell'app, l'uso <xref:Microsoft.AspNetCore.Components.OwningComponentBase> di per limitare l'ambito di un <xref:Microsoft.EntityFrameworkCore.DbContext> a un singolo componente *può* risolvere il problema. Se un componente non utilizza un oggetto <xref:Microsoft.EntityFrameworkCore.DbContext> in parallelo, la derivazione del componente da <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e il recupero <xref:Microsoft.EntityFrameworkCore.DbContext> di da <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> è sufficiente perché garantisce quanto segue:

* I componenti separati non condividono un <xref:Microsoft.EntityFrameworkCore.DbContext> .
* Il <xref:Microsoft.EntityFrameworkCore.DbContext> viene vissuto solo fino a quando il componente dipende da esso.

Se un singolo componente può usare <xref:Microsoft.EntityFrameworkCore.DbContext> simultaneamente, ad esempio ogni volta che un utente seleziona un pulsante, anche l'uso di <xref:Microsoft.AspNetCore.Components.OwningComponentBase> non evita problemi con le operazioni EF simultanee. In tal caso, usare un oggetto diverso <xref:Microsoft.EntityFrameworkCore.DbContext> per ogni operazione EF logica. Usare uno degli approcci seguenti:

* Creare <xref:Microsoft.EntityFrameworkCore.DbContext> direttamente usando <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> come argomento, che può essere recuperato da di ed è thread-safe.

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* Registrare <xref:Microsoft.EntityFrameworkCore.DbContext> nel contenitore del servizio con una durata temporanea:
  * Quando si registra il contesto, usare <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> . Il <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> metodo di estensione accetta due parametri facoltativi di tipo <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> . Per usare questo approccio, solo il `contextLifetime` parametro deve essere <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> . `optionsLifetime`può contenere il valore predefinito di <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Il temporaneo <xref:Microsoft.EntityFrameworkCore.DbContext> può essere inserito normalmente (usando [`@inject`](xref:mvc/views/razor#inject) ) in componenti che non eseguono più operazioni EF in parallelo. Quelli che possono eseguire contemporaneamente più operazioni EF possono richiedere <xref:Microsoft.EntityFrameworkCore.DbContext> oggetti distinti per ogni operazione parallela usando <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="detect-transient-disposables"></a>Rilevare eliminabili temporanei

Gli esempi seguenti illustrano come rilevare servizi temporanei monouso in un'app che deve usare <xref:Microsoft.AspNetCore.Components.OwningComponentBase> . Per ulteriori informazioni, vedere la sezione relativa [alle classi dei componenti di base dell'utilità per gestire una](#utility-base-component-classes-to-manage-a-di-scope) sezione dell'ambito.

### Blazor WebAssembly

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

Nell' `TransientDisposable` esempio seguente viene rilevato ( `Program.cs` ):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

`Program`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

Nell' `TransientDependency` esempio seguente viene rilevato ( `Startup.cs` ):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/dependency-injection>
* [`IDisposable`linee guida per le istanze temporanee e condivise](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
