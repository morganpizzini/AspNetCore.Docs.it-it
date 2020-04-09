---
title: ASP.NET'iniezione di dipendenza da Core Blazor
author: guardrex
description: Scopri Blazor come le app possono inserire servizi nei componenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658074"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>iniezione di dipendenza di ASP.NET Core Blazor

Di [Rainer Stropek](https://www.timecockpit.com) e [Mike Rousos](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor supporta l'iniezione di [dipendenza (DI)](xref:fundamentals/dependency-injection). Le app possono usare i servizi incorporati inserendoli nei componenti. Le app possono anche definire e registrare servizi personalizzati e renderli disponibili in tutta l'app tramite DI.

DI è una tecnica per l'accesso ai servizi configurati in una posizione centrale. Questo può essere utile nelle applicazioni Blazor per:

* Condividere una singola istanza di una classe di servizio tra più componenti, noto come servizio *singleton.*
* Separare i componenti da classi di servizio concrete usando astrazioni di riferimento. Si consideri ad `IDataAccess` esempio un'interfaccia per l'accesso ai dati nell'app. L'interfaccia viene implementata da una classe concreta `DataAccess` e registrata come servizio nel contenitore dei servizi dell'app. Quando un componente utilizza `IDataAccess` DI per ricevere un'implementazione, il componente non viene associato al tipo concreto. L'implementazione può essere scambiata, ad esempio per un'implementazione fittizia negli unit test.

## <a name="default-services"></a>Servizi predefiniti

I servizi predefiniti vengono aggiunti automaticamente alla raccolta di servizi dell'app.

| Service | Durata | Descrizione |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | Fornisce metodi per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata da un URI.<br><br>L'istanza `HttpClient` di in un'app Blazor WebAssembly utilizza il browser per la gestione del traffico HTTP in background.<br><br>Le app del server Blazor non includono una `HttpClient` configurazione come servizio per impostazione predefinita. Fornire `HttpClient` un'app Blazor Server.Provide an to a Blazor Server app.<br><br>Per altre informazioni, vedere <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (Blazor WebAssembly)<br>Ambito (server Blazor) | Rappresenta un'istanza di un runtime JavaScript in cui vengono inviate chiamate JavaScript. Per altre informazioni, vedere <xref:blazor/call-javascript-from-dotnet>. |
| `NavigationManager` | Singleton (Blazor WebAssembly)<br>Ambito (server Blazor) | Contiene helper per l'utilizzo con URI e stato di spostamento. Per ulteriori informazioni, vedere [URI e helper dello stato](xref:blazor/routing#uri-and-navigation-state-helpers)di navigazione . |

Un provider di servizi personalizzato non fornisce automaticamente i servizi predefiniti elencati nella tabella. Se si utilizza un provider di servizi personalizzato e si richiede uno dei servizi illustrati nella tabella, aggiungere i servizi necessari al nuovo provider di servizi.

## <a name="add-services-to-an-app"></a>Aggiungere servizi a un'app

### <a name="blazor-webassembly"></a>WebAssembly Blazor

Configurare i servizi per la `Main` raccolta di servizi dell'app nel metodo di *Program.cs*. Nell'esempio seguente, `MyDependency` l'implementazione viene registrata per `IMyDependency`:

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

Una volta compilato l'host, è possibile accedere ai servizi dall'ambito DI radice prima del rendering di qualsiasi componente. Ciò può essere utile per l'esecuzione della logica di inizializzazione prima del rendering del contenuto:This can be useful for running initialization logic before rendering content:

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

L'host fornisce anche un'istanza di configurazione centrale per l'app. Basandosi sull'esempio precedente, l'URL del servizio meteo viene passato da un'origine `InitializeWeatherAsync`di configurazione predefinita (ad esempio, *appsettings.json*) a :

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

Dopo aver creato una `Startup.ConfigureServices` nuova app, esamina il metodo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Al `ConfigureServices` metodo viene <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>passato un oggetto , che<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>è un elenco di oggetti descrittore del servizio ( ). I servizi vengono aggiunti fornendo i descrittori dei servizi alla raccolta di servizi. Nell'esempio seguente viene `IDataAccess` illustrato il concetto con l'interfaccia e la relativa implementazione `DataAccess`concreta :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Durata del servizio

I servizi possono essere configurati con le durate illustrate nella tabella seguente.

| Durata | Descrizione |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | BlazorLe app WebAssembly non hanno attualmente un concetto di ambiti DI. `Scoped`I servizi registrati `Singleton` si comportano come servizi. Tuttavia, Blazor il modello di `Scoped` hosting Server supporta la durata. Nelle Blazor app server, l'ambito della registrazione di un servizio con ambito ha come ambito la *connessione.* Per questo motivo, l'utilizzo di servizi con ambito è preferibile per i servizi che devono essere nell'ambito dell'utente corrente, anche se la finalità corrente consiste nell'eseguire il lato client nel browser. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI crea una *singola istanza* del servizio. Tutti i `Singleton` componenti che richiedono un servizio ricevono un'istanza dello stesso servizio. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Ogni volta che un componente `Transient` ottiene un'istanza di un servizio dal contenitore dei servizi, riceve una *nuova istanza* del servizio. |

Il sistema DI si basa sul sistema DI in ASP.NET Core. Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Richiedere un servizio in un componenteRequest a service in a component

Dopo aver aggiunto i servizi alla raccolta di servizi, inserire i servizi nei componenti usando la direttiva inject Razor.After [ \@services](xref:mvc/views/razor#inject) are added to the service collection, inject the services into the components using the inject Razor directive. `@inject`ha due parametri:

* Digitare &ndash; il tipo di servizio da iniettare.
* Proprietà &ndash; Il nome della proprietà che riceve il servizio app inserito. La proprietà non richiede la creazione manuale. Il compilatore crea la proprietà .

Per altre informazioni, vedere <xref:mvc/views/dependency-injection>.

Utilizzare `@inject` più istruzioni per inserire servizi diversi.

Nell'esempio riportato di seguito viene illustrato come usare `@inject`. L'implementazione `Services.IDataAccess` del servizio viene inserita nella proprietà `DataRepository`del componente. Si noti come il `IDataAccess` codice utilizza solo l'astrazione:Note how the code is only using the abstraction:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Internamente, la proprietà`DataRepository`generata `InjectAttribute` ( ) utilizza l'attributo . In genere, questo attributo non viene utilizzato direttamente. Se è necessaria una classe base per i componenti e sono necessarie `InjectAttribute`anche proprietà inserite per la classe base, aggiungere manualmente il metodo :

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Nei componenti derivati dalla `@inject` classe base, la direttiva non è obbligatoria. La `InjectAttribute` classe base è sufficiente:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Usare DI nei serviziUse DI in services

Servizi complessi potrebbero richiedere servizi aggiuntivi. Nell'esempio precedente, `DataAccess` potrebbe `HttpClient` essere necessario il servizio predefinito. `@inject`(o `InjectAttribute`il ) non è disponibile per l'utilizzo nei servizi. È invece necessario *utilizzare l'inserimento del costruttore.* I servizi necessari vengono aggiunti aggiungendo parametri al costruttore del servizio. Quando DI crea il servizio, riconosce i servizi necessari nel costruttore e li fornisce di conseguenza.

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

Prerequisiti per l'inserimento del costruttore:Prerequisites for constructor injection:

* Deve esistere un costruttore i cui argomenti possono essere tutti soddisfatti da DI. Se specificano valori predefiniti, sono consentiti parametri aggiuntivi non coperti da DI.
* Il costruttore applicabile deve essere *public*.
* Deve esistere un costruttore applicabile. In caso di ambiguità, DI genera un'eccezione.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Classi di componenti di base dell'utilità per gestire un ambito DI

Nelle app ASP.NET Core, i servizi con ambito hanno in genere ambito la richiesta corrente. Al termine della richiesta, tutti i servizi con ambito o temporanei vengono eliminati dal sistema DI. Nelle Blazor app server, l'ambito della richiesta dura per la durata della connessione client, il che può comportare servizi temporanei e con ambito molto più lungo del previsto. Nelle Blazor app WebAssembly, i servizi registrati con una durata con ambito vengono considerati singleton, pertanto risiedono più a lungo dei servizi con ambito nelle tipiche app ASP.NET Core.

Un approccio che limita Blazor la durata `OwningComponentBase` di un servizio nelle app è l'uso del tipo. `OwningComponentBase`è un tipo `ComponentBase` astratto derivato da che crea un ambito DI corrispondente alla durata del componente. Utilizzando questo ambito, è possibile utilizzare i servizi DI con una durata con ambito e farli vivere fino a quando il componente. Quando il componente viene eliminato, vengono eliminati anche i servizi del provider di servizi con ambito del componente. Ciò può essere utile per i servizi che:

* Deve essere riutilizzato all'interno di un componente, poiché la durata transitoria è inappropriata.
* Non deve essere condiviso tra i componenti, poiché la durata del singolo è inappropriata.

Sono disponibili `OwningComponentBase` due versioni del tipo:

* `OwningComponentBase`è un figlio astratto `ComponentBase` e usa `ScopedServices` e `IServiceProvider`getta del tipo con una proprietà protetta di tipo . Questo provider può essere utilizzato per risolvere i servizi che hanno come ambito la durata del componente.

  I servizi DI inseriti `@inject` nel `InjectAttribute` `[Inject]`componente utilizzando o il ( ) non vengono creati nell'ambito del componente. Per utilizzare l'ambito del componente, `ScopedServices.GetRequiredService` i `ScopedServices.GetService`servizi devono essere risolti utilizzando o . Tutti i servizi `ScopedServices` risolti tramite il provider hanno le dipendenze fornite dallo stesso ambito.

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

* `OwningComponentBase<T>`deriva da `OwningComponentBase` e aggiunge `Service` una proprietà `T` che restituisce un'istanza di dal provider DI con ambito. Questo tipo è un modo pratico per accedere `IServiceProvider` ai servizi con ambito senza usare un'istanza di quando è presente un servizio primario richiesto dall'app dal contenitore DI usando l'ambito del componente. La `ScopedServices` proprietà è disponibile, in modo che l'app possa ottenere servizi di altri tipi, se necessario.

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Utilizzo di Entity Framework DbContext da DI

Un tipo di servizio comune da recuperare da DI `DbContext` nelle applicazioni web è Entity Framework (EF) oggetti. Registrazione di servizi `IServiceCollection.AddDbContext` di `DbContext` EF utilizzando aggiunge il come un servizio con ambito per impostazione predefinita. La registrazione come servizio con ambito Blazor può causare `DbContext` problemi nelle app perché causa istanze di lunga durata e condivise nell'app. `DbContext`non è thread-safe e non deve essere utilizzato contemporaneamente.

A seconda dell'app, `OwningComponentBase` l'utilizzo `DbContext` di un componente può *risolvere* il problema. Se un componente non `DbContext` utilizza un in parallelo, `OwningComponentBase` derivare `DbContext` il `ScopedServices` componente da e recuperare il da è sufficiente perché garantisce che:If a component doesn't use a in parallel, deriving the component from and retrieving the from is sufficient because it ensures that:

* Componenti separati non condividono un `DbContext`file .
* Le `DbContext` vite durano solo fino a quando il componente dipende da esso.

Se un singolo componente `DbContext` potrebbe utilizzare un contemporaneamente (ad esempio, ogni `OwningComponentBase` volta che un utente seleziona un pulsante), anche l'utilizzo non consente di evitare problemi con le operazioni simultanee di EF. In tal caso, `DbContext` utilizzare un diverso per ogni operazione di EF logico. Utilizzare uno dei seguenti approcci:

* Creare `DbContext` il `DbContextOptions<TContext>` direttamente utilizzando come argomento, che può essere recuperato da DI ed è thread-safe.

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

* Registrare `DbContext` l'oggetto nel contenitore dei servizi con una durata temporanea:Register the in the service container with a transient lifetime:
  * Quando si registra il `ServiceLifetime.Transient`contesto, utilizzare . Il `AddDbContext` metodo di estensione accetta `ServiceLifetime`due parametri facoltativi di tipo . Per utilizzare questo approccio, solo il `contextLifetime` parametro deve essere `ServiceLifetime.Transient`. `optionsLifetime`è possibile mantenere `ServiceLifetime.Scoped`il valore predefinito di .

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Il transitorio `DbContext` può essere inserito `@inject`come di consueto (utilizzando ) in componenti che non eseguirà più operazioni di EF in parallelo. Quelli che possono eseguire più operazioni di `DbContext` FFi contemporaneamente `IServiceProvider.GetRequiredService`possono richiedere oggetti separati per ogni operazione parallela utilizzando .

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
