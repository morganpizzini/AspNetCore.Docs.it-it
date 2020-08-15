---
title: ASP.NET Core Blazor Server con Entity Framework Core (EFCore)
author: JeremyLikness
description: Linee guida per l'uso di EF Core nelle Blazor Server app.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 2ea702aa73a2981afc223e5c1700d6ec2dc62df4
ms.sourcegitcommit: 503b348e9046fcd969de85898394a1ea8274ec38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227771"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a>ASP.NET Core Blazor Server con Entity Framework Core (EFCore)

Da: [Jeremy Likness](https://github.com/JeremyLikness)

:::moniker range=">= aspnetcore-5.0"

Blazor Server è un Framework di app con stato. L'app mantiene una connessione continuativa al server e lo stato dell'utente viene mantenuto nella memoria del server in un *circuito*. Un esempio di stato utente è costituito dai dati contenuti nelle istanze del servizio [di inserimento delle dipendenze](xref:fundamentals/dependency-injection) che hanno come ambito il circuito. Il modello di applicazione univoco Blazor Server fornito da richiede un approccio speciale per utilizzare Entity Framework Core. 

> [!NOTE]
> Questo articolo illustra EF Core nelle Blazor Server app. Blazor WebAssembly le app vengono eseguite in una sandbox di webassembly che impedisce la maggior parte delle connessioni di database dirette. L'esecuzione di EF Core in Blazor WebAssembly esula dall'ambito di questo articolo.

## <a name="sample-app"></a>App di esempio

L'app di esempio è stata creata come riferimento per le Blazor Server app che usano EF core. L'app di esempio include una griglia con operazioni di ordinamento e filtro, eliminazione, aggiunta e aggiornamento. Nell'esempio viene illustrato l'utilizzo di EF Core per gestire la concorrenza ottimistica.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([procedura per il download](xref:index#how-to-download-a-sample))

Nell'esempio viene utilizzato un database [SQLite](https://www.sqlite.org/index.html) locale in modo che possa essere utilizzato su qualsiasi piattaforma. Nell'esempio viene inoltre configurata la registrazione del database per visualizzare le query SQL generate. Questa configurazione è configurata in `appsettings.Development.json` :

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

I componenti Grid, Add e View utilizzano il modello "context-per-Operation", in cui viene creato un contesto per ogni operazione. Il componente Edit usa il modello "context-per-Component", in cui viene creato un contesto per ogni componente.

## <a name="database-access"></a>Accesso al database

EF Core si basa su un <xref:Microsoft.EntityFrameworkCore.DbContext> come mezzo per [configurare l'accesso al database](/ef/core/miscellaneous/configuring-dbcontext) e fungere da [_unità di lavoro_](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core fornisce l' <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> estensione per le app ASP.NET Core che registra il contesto come servizio con _ambito_ per impostazione predefinita. Nelle Blazor Server app, le registrazioni del servizio con ambito possono essere problematiche perché l'istanza è condivisa tra i componenti all'interno del circuito dell'utente. <xref:Microsoft.EntityFrameworkCore.DbContext> non è thread-safe e non è progettato per l'uso simultaneo. Le durate esistenti non sono appropriate per i motivi seguenti:

* Il **singleton** condivide lo stato tra tutti gli utenti dell'app e comporta un uso simultaneo non appropriato.
* Con **ambito** (impostazione predefinita) si presenta un problema simile tra i componenti per lo stesso utente.
* I risultati **temporanei** in una nuova istanza per ogni richiesta; Tuttavia, poiché i componenti possono essere di lunga durata, questo risultato è un contesto di durata più lunga di quanto possa essere previsto.

Le indicazioni seguenti sono progettate per offrire un approccio coerente all'uso di EF Core nelle Blazor Server app. 

* Per impostazione predefinita, è consigliabile usare un contesto per ogni operazione. Il contesto è progettato per la creazione di un'istanza di overhead veloce e bassa:

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Usare un flag per evitare più operazioni simultanee:

  ```csharp
  if (Loading)
  {
      return;
  }
  try 
  {
      Loading = true;
      // operations go here
  }
  finally 
  {
      Loading = false;
  }
  ```

* Per le operazioni più longeve che sfruttano i vantaggi del [rilevamento delle modifiche](/ef/core/querying/tracking) EF core o del controllo della concorrenza, [Definire](/ef/core/saving/concurrency)l' [ambito del contesto per la durata del componente](#scope-to-the-component-lifetime).

### <a name="new-dbcontext-instances"></a>Nuove istanze di DbContext

Il modo più rapido per creare una nuova <xref:Microsoft.EntityFrameworkCore.DbContext> istanza consiste nell'usare `new` per creare una nuova istanza. Esistono tuttavia diversi scenari che possono richiedere la risoluzione di dipendenze aggiuntive. Ad esempio, è possibile usare [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) per configurare il contesto. 

La soluzione consigliata per creare una nuova <xref:Microsoft.EntityFrameworkCore.DbContext> con dipendenze consiste nell'usare una factory. EF Core 5,0 o versione successiva fornisce una factory predefinita per la creazione di nuovi contesti.

Nell'esempio seguente viene configurato [SQLite](https://www.sqlite.org/index.html) e viene abilitata la registrazione dei dati. Il codice usa un metodo di estensione per configurare la factory di database per e fornire opzioni predefinite:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

La factory viene inserita in componenti e usata per creare nuove istanze. Ad esempio: in `Pages/Index.razor`:

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a>Ambito per la durata del componente

È possibile creare un <xref:Microsoft.EntityFrameworkCore.DbContext> esistente per la durata di un componente. In questo modo, è possibile usarlo come [unità di lavoro](https://martinfowler.com/eaaCatalog/unitOfWork.html) e sfruttare le funzionalità predefinite, ad esempio il rilevamento delle modifiche e la risoluzione della concorrenza.
È possibile usare la factory per creare un contesto e tenerne traccia per la durata del componente. Innanzitutto, implementare <xref:System.IDisposable> e inserire la factory come illustrato in `Pages/EditContact.razor` :

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

L'app di esempio garantisce che il contatto venga eliminato quando il componente viene eliminato:

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

Infine, `OnInitializedAsync` viene eseguito l'override di per creare un nuovo contesto. Nell'app di esempio `OnInitializedAsync` carica il contatto nello stesso metodo:

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

Blazor Server è un Framework di app con stato. L'app mantiene una connessione continuativa al server e lo stato dell'utente viene mantenuto nella memoria del server in un *circuito*. Un esempio di stato utente è costituito dai dati contenuti nelle istanze del servizio [di inserimento delle dipendenze](xref:fundamentals/dependency-injection) che hanno come ambito il circuito. Il modello di applicazione univoco Blazor Server fornito da richiede un approccio speciale per utilizzare Entity Framework Core. 

> [!NOTE]
> Questo articolo illustra EF Core nelle Blazor Server app. Blazor WebAssembly le app vengono eseguite in una sandbox di webassembly che impedisce la maggior parte delle connessioni di database dirette. L'esecuzione di EF Core in Blazor WebAssembly esula dall'ambito di questo articolo.

## <a name="sample-app"></a>App di esempio

L'app di esempio è stata creata come riferimento per le Blazor Server app che usano EF core. L'app di esempio include una griglia con operazioni di ordinamento e filtro, eliminazione, aggiunta e aggiornamento. Nell'esempio viene illustrato l'utilizzo di EF Core per gestire la concorrenza ottimistica.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([procedura per il download](xref:index#how-to-download-a-sample))

Nell'esempio viene utilizzato un database [SQLite](https://www.sqlite.org/index.html) locale in modo che possa essere utilizzato su qualsiasi piattaforma. Nell'esempio viene inoltre configurata la registrazione del database per visualizzare le query SQL generate. Questa configurazione è configurata in `appsettings.Development.json` :

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

I componenti Grid, Add e View utilizzano il modello "context-per-Operation", in cui viene creato un contesto per ogni operazione. Il componente Edit usa il modello "context-per-Component", in cui viene creato un contesto per ogni componente.

## <a name="database-access"></a>Accesso al database

EF Core si basa su un <xref:Microsoft.EntityFrameworkCore.DbContext> come mezzo per [configurare l'accesso al database](/ef/core/miscellaneous/configuring-dbcontext) e fungere da [_unità di lavoro_](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core fornisce l' <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> estensione per le app ASP.NET Core che registra il contesto come servizio con _ambito_ per impostazione predefinita. Nelle Blazor Server app questo può essere problematico perché l'istanza è condivisa tra i componenti all'interno del circuito dell'utente. <xref:Microsoft.EntityFrameworkCore.DbContext> non è thread-safe e non è progettato per l'uso simultaneo. Le durate esistenti non sono appropriate per i motivi seguenti:

* Il **singleton** condivide lo stato tra tutti gli utenti dell'app e comporta un uso simultaneo non appropriato.
* Con **ambito** (impostazione predefinita) si presenta un problema simile tra i componenti per lo stesso utente.
* I risultati **temporanei** in una nuova istanza per ogni richiesta; Tuttavia, poiché i componenti possono essere di lunga durata, questo risultato è un contesto di durata più lunga di quanto possa essere previsto.

## <a name="database-access"></a>Accesso al database

Le indicazioni seguenti sono progettate per offrire un approccio coerente all'uso di EF Core nelle Blazor Server app. 

* Per impostazione predefinita, è consigliabile usare un contesto per ogni operazione. Il contesto è progettato per la creazione di un'istanza di overhead veloce e bassa:

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Usare un flag per evitare più operazioni simultanee:

  ```csharp
  if (Loading)
  {
      return;
  }
  try 
  {
      Loading = true;
      // operations go here
  }
  finally 
  {
      Loading = false;
  }
  ```

* Per le operazioni più longeve che sfruttano i vantaggi del [rilevamento delle modifiche](/ef/core/querying/tracking) EF core o del controllo della concorrenza, [Definire](/ef/core/saving/concurrency)l' [ambito del contesto per la durata del componente](#scope-to-the-component-lifetime).

### <a name="create-new-dbcontext-instances"></a>Crea nuove istanze di DbContext

Il modo più rapido per creare una nuova <xref:Microsoft.EntityFrameworkCore.DbContext> istanza consiste nell'usare `new` per creare una nuova istanza. Esistono tuttavia diversi scenari che possono richiedere la risoluzione di dipendenze aggiuntive. Ad esempio, è possibile usare [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) per configurare il contesto. 

La soluzione consigliata per creare una nuova <xref:Microsoft.EntityFrameworkCore.DbContext> con dipendenze consiste nell'usare una factory. L'app di esempio implementa la propria factory in `Data/DbContextFactory.cs` . 

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

Nell'esempio seguente viene configurato [SQLite](https://www.sqlite.org/index.html) e viene abilitata la registrazione dei dati. Il codice usa un metodo di estensione per configurare la factory di database per e fornire opzioni predefinite:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

La factory viene inserita in componenti e usata per creare nuove istanze. Ad esempio: in `Pages/Index.razor`:

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a>Ambito per la durata del componente

È possibile creare un <xref:Microsoft.EntityFrameworkCore.DbContext> esistente per la durata di un componente. In questo modo, è possibile usarlo come [unità di lavoro](https://martinfowler.com/eaaCatalog/unitOfWork.html) e sfruttare le funzionalità predefinite, ad esempio il rilevamento delle modifiche e la risoluzione della concorrenza.
È possibile usare la factory per creare un contesto e tenerne traccia per la durata del componente. Innanzitutto, implementare <xref:System.IDisposable> e inserire la factory come illustrato in `Pages/EditContact.razor` :

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

L'app di esempio garantisce che il contatto venga eliminato quando il componente viene eliminato:

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

Infine, `OnInitializedAsync` viene eseguito l'override di per creare un nuovo contesto. Nell'app di esempio `OnInitializedAsync` carica il contatto nello stesso metodo:

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

> [Documentazione di EF Core](/ef/)
