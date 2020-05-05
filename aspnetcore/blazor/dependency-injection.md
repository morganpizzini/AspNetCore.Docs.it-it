---
title: Inserimento Blazor di dipendenze ASP.NET Core
author: guardrex
description: Scopri in Blazor che modo le app possono inserire i servizi nei componenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 742f3c5ea26fab5e168f162a0e133da05fd74a74
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767116"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>Inserimento delle dipendenze di ASP.NET Core Blazer

Di [Rainer Stropek](https://www.timecockpit.com) e [Mike entusiasmanti](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazer supporta l' [inserimento delle dipendenze (di)](xref:fundamentals/dependency-injection). Le app possono usare i servizi predefiniti inserendoli in componenti. Le app possono anche definire e registrare servizi personalizzati e renderli disponibili nell'app tramite DI.

DI è una tecnica per accedere ai servizi configurati in una posizione centrale. Questa operazione può essere utile nelle app blazer per:

* Condividere una singola istanza di una classe di servizio in molti componenti, noti come un servizio *singleton* .
* Separare i componenti da classi di servizi concrete usando astrazioni di riferimento. Si consideri ad esempio `IDataAccess` un'interfaccia per l'accesso ai dati nell'app. L'interfaccia viene implementata da una `DataAccess` classe concreta e registrata come servizio nel contenitore del servizio dell'app. Quando un componente usa il per ricevere un' `IDataAccess` implementazione di, il componente non è associato al tipo concreto. L'implementazione può essere scambiata, ad esempio per un'implementazione fittizia negli unit test.

## <a name="default-services"></a>Servizi predefiniti

I servizi predefiniti vengono aggiunti automaticamente alla raccolta di servizi dell'app.

| Service | Durata | Description |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Temporaneo | Fornisce metodi per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata da un URI.<br><br>L'istanza di `HttpClient` in un'app Webassembly Blazer usa il browser per gestire il traffico HTTP in background.<br><br>Per impostazione predefinita, le app del `HttpClient` server Blazer non includono un oggetto configurato come servizio. Fornire `HttpClient` a un'app del server blazer.<br><br>Per altre informazioni, vedere <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (webassembly Blazer)<br>Con ambito (server Blazer) | Rappresenta un'istanza di un runtime JavaScript in cui vengono inviate le chiamate a JavaScript. Per altre informazioni, vedere <xref:blazor/call-javascript-from-dotnet>. |
| `NavigationManager` | Singleton (webassembly Blazer)<br>Con ambito (server Blazer) | Contiene gli helper per lavorare con gli URI e lo stato di navigazione. Per ulteriori informazioni, vedere [URI e Helper dello stato di navigazione](xref:blazor/routing#uri-and-navigation-state-helpers). |

Un provider di servizi personalizzato non fornisce automaticamente i servizi predefiniti elencati nella tabella. Se si usa un provider di servizi personalizzato e si richiede uno dei servizi indicati nella tabella, aggiungere i servizi necessari al nuovo provider di servizi.

## <a name="add-services-to-an-app"></a>Aggiungere servizi a un'app

### <a name="blazor-webassembly"></a>WebAssembly Blazor

Configurare i servizi per la raccolta di servizi dell'app `Main` nel metodo di *Program.cs*. Nell'esempio seguente l' `MyDependency` implementazione è registrata per: `IMyDependency`

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

L'host fornisce anche un'istanza di configurazione centrale per l'app. Basandosi sull'esempio precedente, l'URL del servizio meteo viene passato da un'origine di configurazione predefinita, ad esempio *appSettings. JSON*, a `InitializeWeatherAsync`:

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

### <a name="blazor-server"></a>Server Blazor

Dopo aver creato una nuova app, esaminare `Startup.ConfigureServices` il metodo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Al `ConfigureServices` metodo viene passato un <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>oggetto, che è un elenco di oggetti del descrittore del servizio (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). I servizi vengono aggiunti fornendo descrittori del servizio alla raccolta di servizi. Nell'esempio seguente viene illustrato il concetto con `IDataAccess` l'interfaccia e la relativa `DataAccess`implementazione concreta:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Durata del servizio

I servizi possono essere configurati con le durate mostrate nella tabella seguente.

| Durata | Description |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | BlazorLe app webassembly attualmente non dispongono di un concetto di ambiti di. `Scoped`-i servizi registrati si `Singleton` comportano come servizi. Tuttavia, il Blazor modello di hosting del server `Scoped` supporta il ciclo di vita. Nelle Blazor app Server, una registrazione del servizio con ambito ha come ambito la *connessione*. Per questo motivo, è preferibile usare i servizi con ambito per i servizi che devono avere come ambito l'utente corrente, anche se l'obiettivo corrente è eseguire sul lato client nel browser. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | La creazione di una *singola istanza* del servizio. Tutti i componenti che richiedono `Singleton` un servizio ricevono un'istanza dello stesso servizio. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Ogni volta che un componente ottiene un'istanza di `Transient` un servizio dal contenitore del servizio, riceve una *nuova istanza* del servizio. |

Il sistema DI è basato sul sistema DI ASP.NET Core. Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Richiedere un servizio in un componente

Una volta aggiunti i servizi alla raccolta di servizi, inserire i servizi nei componenti utilizzando la [ \@direttiva Inject.](xref:mvc/views/razor#inject) Razor `@inject`dispone di due parametri:

* Digitare &ndash; il tipo di servizio da inserire.
* Proprietà &ndash; nome della proprietà che riceve il servizio app inserito. La proprietà non richiede la creazione manuale. Il compilatore crea la proprietà.

Per altre informazioni, vedere <xref:mvc/views/dependency-injection>.

Usare più `@inject` istruzioni per inserire servizi diversi.

Nell'esempio riportato di seguito viene illustrato come usare `@inject`. Il servizio che `Services.IDataAccess` implementa viene inserito nella proprietà `DataRepository`del componente. Si noti come il codice usa solo l' `IDataAccess` astrazione:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Internamente, la proprietà generata`DataRepository`() utilizza `InjectAttribute` l'attributo. In genere, questo attributo non viene utilizzato direttamente. Se è necessaria una classe base per i componenti e le proprietà inserite sono necessarie anche per la classe base, aggiungere manualmente `InjectAttribute`:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Nei componenti derivati dalla classe di base `@inject` , la direttiva non è obbligatoria. La `InjectAttribute` classe della classe base è sufficiente:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Usare l'inserimento DI dipendenze nei servizi

Servizi complessi potrebbe richiedere servizi aggiuntivi. Nell'esempio precedente, `DataAccess` potrebbe richiedere il `HttpClient` servizio predefinito. `@inject`(o) `InjectAttribute`non è disponibile per l'uso nei servizi. È necessario usare invece l' *inserimento del costruttore* . I servizi necessari vengono aggiunti aggiungendo parametri al costruttore del servizio. Quando si crea il servizio, vengono riconosciuti i servizi richiesti nel costruttore e forniti DI conseguenza.

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
* Il costruttore applicabile deve essere *pubblico*.
* È necessario che esista un costruttore applicabile. In caso di ambiguità, viene generata un'eccezione.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Classi di componenti di base dell'utilità per gestire un ambito DI

Nelle app ASP.NET Core, i servizi con ambito hanno in genere come ambito la richiesta corrente. Al termine della richiesta, tutti i servizi con ambito o temporaneo vengono eliminati dal sistema DI. Nelle Blazor app Server, l'ambito della richiesta dura per la durata della connessione client, che può comportare un tempo di permanenza dei servizi temporanei e con ambito più lungo del previsto. Nelle Blazor app webassembly i servizi registrati con una durata con ambito vengono considerati come singleton, quindi vivono più a lungo rispetto ai servizi con ambito nelle app ASP.NET Core tipiche.

Un approccio che limita la durata di un Blazor servizio nelle app è l' `OwningComponentBase` uso del tipo. `OwningComponentBase`è un tipo astratto derivato da `ComponentBase` che consente di creare un ambito di che corrisponde alla durata del componente. Con questo ambito, è possibile usare i servizi DI i con una durata con ambito e fare in modo che siano attivi fino a quando il componente. Quando il componente viene eliminato definitivamente, vengono eliminati anche i servizi del provider di servizi con ambito. Questa operazione può essere utile per i servizi che:

* È necessario riutilizzarlo all'interno di un componente, perché la durata temporanea non è appropriata.
* Non devono essere condivise tra i componenti, perché la durata singleton non è appropriata.

Sono disponibili due versioni `OwningComponentBase` del tipo:

* `OwningComponentBase`è un elemento figlio astratto e disposable del `ComponentBase` tipo con una proprietà `ScopedServices` protetta di tipo `IServiceProvider`. Questo provider può essere utilizzato per risolvere i servizi che hanno come ambito la durata del componente.

  I servizi di inserimento nel componente tramite `@inject` o `InjectAttribute` (`[Inject]`) non vengono creati nell'ambito del componente. Per utilizzare l'ambito del componente, i servizi devono essere `ScopedServices.GetRequiredService` risolti `ScopedServices.GetService`utilizzando o. Tutti i servizi risolti `ScopedServices` utilizzando il provider hanno le dipendenze fornite dallo stesso ambito.

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

* `OwningComponentBase<T>`deriva da `OwningComponentBase` e aggiunge una proprietà `Service` che restituisce un'istanza di `T` dal provider dell'ambito di. Questo tipo è un modo pratico per accedere ai servizi con ambito senza usare un'istanza `IServiceProvider` di quando è presente un servizio primario richiesto dall'app dal contenitore di inserimento delle dipendenze usando l'ambito del componente. La `ScopedServices` proprietà è disponibile, in modo che l'app possa ottenere i servizi di altri tipi, se necessario.

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

Un tipo di servizio comune da recuperare da un in app Web è Entity Framework oggetti ( `DbContext` EF). Per impostazione predefinita, la `IServiceCollection.AddDbContext` `DbContext` registrazione dei servizi EF tramite aggiunge come servizio con ambito. La registrazione come servizio con ambito può causare problemi nelle Blazor app perché causa `DbContext` la lunga durata delle istanze e la condivisione nell'app. `DbContext`non è thread-safe e non deve essere usato simultaneamente.

A seconda dell'app, l'uso `OwningComponentBase` di per limitare l'ambito di `DbContext` un a un singolo componente *può* risolvere il problema. Se un componente non utilizza un `DbContext` oggetto in parallelo, la derivazione `OwningComponentBase` del componente da e `DbContext` il `ScopedServices` recupero di da è sufficiente perché garantisce quanto segue:

* I componenti separati non condividono `DbContext`un.
* Il `DbContext` viene vissuto solo fino a quando il componente dipende da esso.

Se un singolo componente può usare `DbContext` simultaneamente, ad esempio ogni volta che un utente seleziona un pulsante, anche l'uso `OwningComponentBase` di non evita problemi con le operazioni EF simultanee. In tal caso, usare un oggetto `DbContext` diverso per ogni operazione EF logica. Usare uno degli approcci seguenti:

* Creare `DbContext` direttamente usando `DbContextOptions<TContext>` come argomento, che può essere recuperato da di ed è thread-safe.

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
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

* Registrare `DbContext` nel contenitore del servizio con una durata temporanea:
  * Quando si registra il contesto, usare `ServiceLifetime.Transient`. Il `AddDbContext` metodo di estensione accetta due parametri facoltativi `ServiceLifetime`di tipo. Per usare questo approccio, solo il `contextLifetime` parametro deve essere `ServiceLifetime.Transient`. `optionsLifetime`può contenere il valore predefinito di `ServiceLifetime.Scoped`.

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Il temporaneo `DbContext` può essere inserito normalmente (usando `@inject`) in componenti che non eseguono più operazioni EF in parallelo. Quelli che possono eseguire contemporaneamente più operazioni EF possono richiedere oggetti `DbContext` distinti per ogni operazione parallela `IServiceProvider.GetRequiredService`usando.

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
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

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
