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
ms.openlocfilehash: 2ce2467ccda04b584a6bc04d1c6d9c66bcd659f2
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504255"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="7b4b6-103">ASP.NET Core Blazor Server con Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="7b4b6-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="7b4b6-104">Da: [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="7b4b6-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7b4b6-105">Blazor Server è un Framework di app con stato.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="7b4b6-106">L'app mantiene una connessione continuativa al server e lo stato dell'utente viene mantenuto nella memoria del server in un *circuito*.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="7b4b6-107">Un esempio di stato utente è costituito dai dati contenuti nelle istanze del servizio [di inserimento delle dipendenze](xref:fundamentals/dependency-injection) che hanno come ambito il circuito.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="7b4b6-108">Il modello di applicazione univoco Blazor Server fornito da richiede un approccio speciale per utilizzare Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="7b4b6-109">Questo articolo illustra EF Core nelle Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="7b4b6-110">Blazor WebAssembly le app vengono eseguite in una sandbox di webassembly che impedisce la maggior parte delle connessioni di database dirette.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="7b4b6-111">L'esecuzione di EF Core in Blazor WebAssembly esula dall'ambito di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="7b4b6-112">App di esempio</span><span class="sxs-lookup"><span data-stu-id="7b4b6-112">Sample app</span></span>

<span data-ttu-id="7b4b6-113">L'app di esempio è stata creata come riferimento per le Blazor Server app che usano EF core.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="7b4b6-114">L'app di esempio include una griglia con operazioni di ordinamento e filtro, eliminazione, aggiunta e aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="7b4b6-115">Nell'esempio viene illustrato l'utilizzo di EF Core per gestire la concorrenza ottimistica.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="7b4b6-116">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7b4b6-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7b4b6-117">Nell'esempio viene utilizzato un database [SQLite](https://www.sqlite.org/index.html) locale in modo che possa essere utilizzato su qualsiasi piattaforma.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="7b4b6-118">Nell'esempio viene inoltre configurata la registrazione del database per visualizzare le query SQL generate.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="7b4b6-119">Questa configurazione è configurata in `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="7b4b6-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="7b4b6-120">I componenti Grid, Add e View utilizzano il modello "context-per-Operation", in cui viene creato un contesto per ogni operazione.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="7b4b6-121">Il componente Edit usa il modello "context-per-Component", in cui viene creato un contesto per ogni componente.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="7b4b6-122">Per alcuni esempi di codice in questo argomento sono necessari gli spazi dei nomi e i servizi che non vengono visualizzati.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="7b4b6-123">Per esaminare il codice completamente funzionante, incluse le [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) direttive e obbligatorie per gli Razor esempi, vedere l' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="7b4b6-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="7b4b6-124">Accesso al database</span><span class="sxs-lookup"><span data-stu-id="7b4b6-124">Database access</span></span>

<span data-ttu-id="7b4b6-125">EF Core si basa su un <xref:Microsoft.EntityFrameworkCore.DbContext> come mezzo per [configurare l'accesso al database](/ef/core/miscellaneous/configuring-dbcontext) e fungere da [*unità di lavoro*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="7b4b6-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="7b4b6-126">EF Core fornisce l' <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> estensione per le app ASP.NET Core che registra il contesto come servizio con *ambito* per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="7b4b6-127">Nelle Blazor Server app, le registrazioni del servizio con ambito possono essere problematiche perché l'istanza è condivisa tra i componenti all'interno del circuito dell'utente.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="7b4b6-128"><xref:Microsoft.EntityFrameworkCore.DbContext> non è thread-safe e non è progettato per l'uso simultaneo.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="7b4b6-129">Le durate esistenti non sono appropriate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="7b4b6-130">Il **singleton** condivide lo stato tra tutti gli utenti dell'app e comporta un uso simultaneo non appropriato.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="7b4b6-131">Con **ambito** (impostazione predefinita) si presenta un problema simile tra i componenti per lo stesso utente.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="7b4b6-132">I risultati **temporanei** in una nuova istanza per ogni richiesta; Tuttavia, poiché i componenti possono essere di lunga durata, questo risultato è un contesto di durata più lunga di quanto possa essere previsto.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="7b4b6-133">Le indicazioni seguenti sono progettate per offrire un approccio coerente all'uso di EF Core nelle Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="7b4b6-134">Per impostazione predefinita, è consigliabile usare un contesto per ogni operazione.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="7b4b6-135">Il contesto è progettato per la creazione di un'istanza di overhead veloce e bassa:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="7b4b6-136">Usare un flag per evitare più operazioni simultanee:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-136">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="7b4b6-137">Posizionare le operazioni dopo la `Loading = true;` riga nel `try` blocco.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="7b4b6-138">Per le operazioni più longeve che sfruttano i vantaggi del [rilevamento delle modifiche](/ef/core/querying/tracking) EF core o del controllo della concorrenza, [Definire](/ef/core/saving/concurrency)l' [ambito del contesto per la durata del componente](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="7b4b6-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="new-dbcontext-instances"></a><span data-ttu-id="7b4b6-139">Nuove istanze di DbContext</span><span class="sxs-lookup"><span data-stu-id="7b4b6-139">New DbContext instances</span></span>

<span data-ttu-id="7b4b6-140">Il modo più rapido per creare una nuova <xref:Microsoft.EntityFrameworkCore.DbContext> istanza consiste nell'usare `new` per creare una nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="7b4b6-141">Esistono tuttavia diversi scenari che possono richiedere la risoluzione di dipendenze aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="7b4b6-142">Ad esempio, è possibile usare [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) per configurare il contesto.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="7b4b6-143">La soluzione consigliata per creare una nuova <xref:Microsoft.EntityFrameworkCore.DbContext> con dipendenze consiste nell'usare una factory.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="7b4b6-144">EF Core 5,0 o versione successiva fornisce una factory predefinita per la creazione di nuovi contesti.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="7b4b6-145">Nell'esempio seguente viene configurato [SQLite](https://www.sqlite.org/index.html) e viene abilitata la registrazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="7b4b6-146">Il codice usa un metodo di estensione per configurare la factory di database per e fornire opzioni predefinite:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-146">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="7b4b6-147">La factory viene inserita in componenti e usata per creare nuove istanze.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="7b4b6-148">Ad esempio: in `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="7b4b6-149">Ambito per la durata del componente</span><span class="sxs-lookup"><span data-stu-id="7b4b6-149">Scope to the component lifetime</span></span>

<span data-ttu-id="7b4b6-150">È possibile creare un <xref:Microsoft.EntityFrameworkCore.DbContext> esistente per la durata di un componente.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-150">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="7b4b6-151">In questo modo, è possibile usarlo come [unità di lavoro](https://martinfowler.com/eaaCatalog/unitOfWork.html) e sfruttare le funzionalità predefinite, ad esempio il rilevamento delle modifiche e la risoluzione della concorrenza.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-151">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="7b4b6-152">È possibile usare la factory per creare un contesto e tenerne traccia per la durata del componente.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-152">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="7b4b6-153">Innanzitutto, implementare <xref:System.IDisposable> e inserire la factory come illustrato in `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="7b4b6-153">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="7b4b6-154">L'app di esempio garantisce che il contatto venga eliminato quando il componente viene eliminato:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-154">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="7b4b6-155">Infine, [`OnInitializedAsync`](xref:blazor/components/lifecycle) viene eseguito l'override di per creare un nuovo contesto.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-155">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="7b4b6-156">Nell'app di esempio [`OnInitializedAsync`](xref:blazor/components/lifecycle) carica il contatto nello stesso metodo:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-156">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="7b4b6-157">Blazor Server è un Framework di app con stato.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-157">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="7b4b6-158">L'app mantiene una connessione continuativa al server e lo stato dell'utente viene mantenuto nella memoria del server in un *circuito*.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-158">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="7b4b6-159">Un esempio di stato utente è costituito dai dati contenuti nelle istanze del servizio [di inserimento delle dipendenze](xref:fundamentals/dependency-injection) che hanno come ambito il circuito.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-159">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="7b4b6-160">Il modello di applicazione univoco Blazor Server fornito da richiede un approccio speciale per utilizzare Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-160">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="7b4b6-161">Questo articolo illustra EF Core nelle Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-161">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="7b4b6-162">Blazor WebAssembly le app vengono eseguite in una sandbox di webassembly che impedisce la maggior parte delle connessioni di database dirette.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-162">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="7b4b6-163">L'esecuzione di EF Core in Blazor WebAssembly esula dall'ambito di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-163">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="7b4b6-164">App di esempio</span><span class="sxs-lookup"><span data-stu-id="7b4b6-164">Sample app</span></span>

<span data-ttu-id="7b4b6-165">L'app di esempio è stata creata come riferimento per le Blazor Server app che usano EF core.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-165">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="7b4b6-166">L'app di esempio include una griglia con operazioni di ordinamento e filtro, eliminazione, aggiunta e aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-166">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="7b4b6-167">Nell'esempio viene illustrato l'utilizzo di EF Core per gestire la concorrenza ottimistica.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-167">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="7b4b6-168">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7b4b6-168">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7b4b6-169">Nell'esempio viene utilizzato un database [SQLite](https://www.sqlite.org/index.html) locale in modo che possa essere utilizzato su qualsiasi piattaforma.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-169">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="7b4b6-170">Nell'esempio viene inoltre configurata la registrazione del database per visualizzare le query SQL generate.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-170">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="7b4b6-171">Questa configurazione è configurata in `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="7b4b6-171">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="7b4b6-172">I componenti Grid, Add e View utilizzano il modello "context-per-Operation", in cui viene creato un contesto per ogni operazione.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-172">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="7b4b6-173">Il componente Edit usa il modello "context-per-Component", in cui viene creato un contesto per ogni componente.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-173">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="7b4b6-174">Per alcuni esempi di codice in questo argomento sono necessari gli spazi dei nomi e i servizi che non vengono visualizzati.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-174">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="7b4b6-175">Per esaminare il codice completamente funzionante, incluse le [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) direttive e obbligatorie per gli Razor esempi, vedere l' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="7b4b6-175">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="7b4b6-176">Accesso al database</span><span class="sxs-lookup"><span data-stu-id="7b4b6-176">Database access</span></span>

<span data-ttu-id="7b4b6-177">EF Core si basa su un <xref:Microsoft.EntityFrameworkCore.DbContext> come mezzo per [configurare l'accesso al database](/ef/core/miscellaneous/configuring-dbcontext) e fungere da [*unità di lavoro*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="7b4b6-177">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="7b4b6-178">EF Core fornisce l' <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> estensione per le app ASP.NET Core che registra il contesto come servizio con *ambito* per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-178">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="7b4b6-179">Nelle Blazor Server app questo può essere problematico perché l'istanza è condivisa tra i componenti all'interno del circuito dell'utente.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-179">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="7b4b6-180"><xref:Microsoft.EntityFrameworkCore.DbContext> non è thread-safe e non è progettato per l'uso simultaneo.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-180"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="7b4b6-181">Le durate esistenti non sono appropriate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-181">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="7b4b6-182">Il **singleton** condivide lo stato tra tutti gli utenti dell'app e comporta un uso simultaneo non appropriato.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-182">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="7b4b6-183">Con **ambito** (impostazione predefinita) si presenta un problema simile tra i componenti per lo stesso utente.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-183">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="7b4b6-184">I risultati **temporanei** in una nuova istanza per ogni richiesta; Tuttavia, poiché i componenti possono essere di lunga durata, questo risultato è un contesto di durata più lunga di quanto possa essere previsto.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-184">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

## <a name="database-access"></a><span data-ttu-id="7b4b6-185">Accesso al database</span><span class="sxs-lookup"><span data-stu-id="7b4b6-185">Database access</span></span>

<span data-ttu-id="7b4b6-186">Le indicazioni seguenti sono progettate per offrire un approccio coerente all'uso di EF Core nelle Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-186">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="7b4b6-187">Per impostazione predefinita, è consigliabile usare un contesto per ogni operazione.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-187">By default, consider using one context per operation.</span></span> <span data-ttu-id="7b4b6-188">Il contesto è progettato per la creazione di un'istanza di overhead veloce e bassa:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-188">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="7b4b6-189">Usare un flag per evitare più operazioni simultanee:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-189">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="7b4b6-190">Posizionare le operazioni dopo la `Loading = true;` riga nel `try` blocco.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-190">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="7b4b6-191">Per le operazioni più longeve che sfruttano i vantaggi del [rilevamento delle modifiche](/ef/core/querying/tracking) EF core o del controllo della concorrenza, [Definire](/ef/core/saving/concurrency)l' [ambito del contesto per la durata del componente](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="7b4b6-191">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="create-new-dbcontext-instances"></a><span data-ttu-id="7b4b6-192">Crea nuove istanze di DbContext</span><span class="sxs-lookup"><span data-stu-id="7b4b6-192">Create new DbContext instances</span></span>

<span data-ttu-id="7b4b6-193">Il modo più rapido per creare una nuova <xref:Microsoft.EntityFrameworkCore.DbContext> istanza consiste nell'usare `new` per creare una nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-193">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="7b4b6-194">Esistono tuttavia diversi scenari che possono richiedere la risoluzione di dipendenze aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-194">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="7b4b6-195">Ad esempio, è possibile usare [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) per configurare il contesto.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-195">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="7b4b6-196">La soluzione consigliata per creare una nuova <xref:Microsoft.EntityFrameworkCore.DbContext> con dipendenze consiste nell'usare una factory.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-196">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="7b4b6-197">L'app di esempio implementa la propria factory in `Data/DbContextFactory.cs` .</span><span class="sxs-lookup"><span data-stu-id="7b4b6-197">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="7b4b6-198">Nella Factory precedente, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> soddisfa tutte le dipendenze tramite il provider di servizi.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-198">In the preceding factory, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>

<span data-ttu-id="7b4b6-199">Nell'esempio seguente viene configurato [SQLite](https://www.sqlite.org/index.html) e viene abilitata la registrazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-199">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="7b4b6-200">Il codice usa un metodo di estensione per configurare la factory di database per e fornire opzioni predefinite:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-200">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="7b4b6-201">La factory viene inserita in componenti e usata per creare nuove istanze.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-201">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="7b4b6-202">Ad esempio: in `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-202">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="7b4b6-203">Ambito per la durata del componente</span><span class="sxs-lookup"><span data-stu-id="7b4b6-203">Scope to the component lifetime</span></span>

<span data-ttu-id="7b4b6-204">È possibile creare un <xref:Microsoft.EntityFrameworkCore.DbContext> esistente per la durata di un componente.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-204">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="7b4b6-205">In questo modo, è possibile usarlo come [unità di lavoro](https://martinfowler.com/eaaCatalog/unitOfWork.html) e sfruttare le funzionalità predefinite, ad esempio il rilevamento delle modifiche e la risoluzione della concorrenza.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-205">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="7b4b6-206">È possibile usare la factory per creare un contesto e tenerne traccia per la durata del componente.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-206">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="7b4b6-207">Innanzitutto, implementare <xref:System.IDisposable> e inserire la factory come illustrato in `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="7b4b6-207">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="7b4b6-208">L'app di esempio garantisce che il contatto venga eliminato quando il componente viene eliminato:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-208">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="7b4b6-209">Infine, [`OnInitializedAsync`](xref:blazor/components/lifecycle) viene eseguito l'override di per creare un nuovo contesto.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-209">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="7b4b6-210">Nell'app di esempio [`OnInitializedAsync`](xref:blazor/components/lifecycle) carica il contatto nello stesso metodo:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-210">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="7b4b6-211">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="7b4b6-211">In the preceding example:</span></span>

* <span data-ttu-id="7b4b6-212">Quando `Busy` è impostato su `true` , possono iniziare le operazioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-212">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="7b4b6-213">Quando `Busy` viene impostato di nuovo su `false` , le operazioni asincrone devono essere completate.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-213">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="7b4b6-214">Inserire la logica di gestione degli errori aggiuntiva in un `catch` blocco.</span><span class="sxs-lookup"><span data-stu-id="7b4b6-214">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7b4b6-215">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7b4b6-215">Additional resources</span></span>

> [<span data-ttu-id="7b4b6-216">Documentazione di EF Core</span><span class="sxs-lookup"><span data-stu-id="7b4b6-216">EF Core documentation</span></span>](/ef/)
