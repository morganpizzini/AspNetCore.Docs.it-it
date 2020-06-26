---
title: Parte 8, Razor pagine con EF core in ASP.NET Core concorrenza
author: rick-anderson
description: Parte 8 di Razor pagine e Entity Framework serie di esercitazioni.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/concurrency
ms.openlocfilehash: 597f396237151f49a9ae333973e91d8f4f7c6ff1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401376"
---
# <a name="part-8-razor-pages-with-ef-core-in-aspnet-core---concurrency"></a><span data-ttu-id="03019-103">Parte 8, Razor pagine con EF core in ASP.NET Core concorrenza</span><span class="sxs-lookup"><span data-stu-id="03019-103">Part 8, Razor Pages with EF Core in ASP.NET Core - Concurrency</span></span>

<span data-ttu-id="03019-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) e [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="03019-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="03019-105">Questa esercitazione descrive la gestione dei conflitti quando più utenti aggiornano la stessa entità contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="03019-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="03019-106">Conflitti di concorrenza</span><span class="sxs-lookup"><span data-stu-id="03019-106">Concurrency conflicts</span></span>

<span data-ttu-id="03019-107">Un conflitto di concorrenza si verifica quando:</span><span class="sxs-lookup"><span data-stu-id="03019-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="03019-108">Un utente passa alla pagina di modifica di un'entità.</span><span class="sxs-lookup"><span data-stu-id="03019-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="03019-109">Un altro utente aggiorna la stessa entità prima che la modifica del primo utente venga scritta nel database.</span><span class="sxs-lookup"><span data-stu-id="03019-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="03019-110">Se il rilevamento della concorrenza non è abilitato, chiunque aggiorni il database per ultimo sovrascrive le modifiche apportate dall'altro utente.</span><span class="sxs-lookup"><span data-stu-id="03019-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="03019-111">Se questo rischio è accettabile, il costo della programmazione per la concorrenza potrebbe essere superiore ai vantaggi.</span><span class="sxs-lookup"><span data-stu-id="03019-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="03019-112">Concorrenza pessimistica (blocco)</span><span class="sxs-lookup"><span data-stu-id="03019-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="03019-113">Un modo per impedire i conflitti di concorrenza consiste nell'usare blocchi di database.</span><span class="sxs-lookup"><span data-stu-id="03019-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="03019-114">Questo approccio è denominato concorrenza pessimistica.</span><span class="sxs-lookup"><span data-stu-id="03019-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="03019-115">Prima che l'app legga una riga del database che intende aggiornare, richiede un blocco.</span><span class="sxs-lookup"><span data-stu-id="03019-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="03019-116">Una volta bloccata una riga per l'accesso per gli aggiornamenti, nessun altro utente potrà bloccare la riga fino a quando non viene rilasciato il primo blocco.</span><span class="sxs-lookup"><span data-stu-id="03019-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="03019-117">La gestione dei blocchi presenta svantaggi.</span><span class="sxs-lookup"><span data-stu-id="03019-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="03019-118">La programmazione può essere complessa e può causare problemi di prestazioni con l'aumentare del numero di utenti.</span><span class="sxs-lookup"><span data-stu-id="03019-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="03019-119">Entity Framework Core non offre supporto predefinito per questa modalità e la presente esercitazione non indica come implementarla.</span><span class="sxs-lookup"><span data-stu-id="03019-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="03019-120">Concorrenza ottimistica</span><span class="sxs-lookup"><span data-stu-id="03019-120">Optimistic concurrency</span></span>

<span data-ttu-id="03019-121">La concorrenza ottimistica consente che si verifichino conflitti di concorrenza, quindi attiva le misure necessarie.</span><span class="sxs-lookup"><span data-stu-id="03019-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="03019-122">Ad esempio Jane visita la pagina Department Edit (Modifica - Reparto) e cambia il budget per il reparto English (Inglese) da $ 350.000,00 a $ 0,00.</span><span class="sxs-lookup"><span data-stu-id="03019-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Impostazione del budget su 0](concurrency/_static/change-budget30.png)

<span data-ttu-id="03019-124">Prima che Jane faccia clic su **Salva** John visita la stessa pagina e cambia il valore del campo Start Date (Data inizio) da 9/1/2007 a 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="03019-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Impostazione della data di inizio su 2013](concurrency/_static/change-date30.png)

<span data-ttu-id="03019-126">Jane fa prima di tutto clic su **Save** e visualizza l'effetto della modifica, dato che il browser visualizza la pagina Index con zero come importo del budget.</span><span class="sxs-lookup"><span data-stu-id="03019-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="03019-127">John fa clic su **Salva** in una pagina Edit (Modifica) che visualizza ancora un budget pari a $ 350.000,00.</span><span class="sxs-lookup"><span data-stu-id="03019-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="03019-128">Le operazioni successive dipendono da come si decide di gestire i conflitti di concorrenza:</span><span class="sxs-lookup"><span data-stu-id="03019-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="03019-129">È possibile tenere traccia della proprietà che un utente ha modificato e aggiornare solo le colonne corrispondenti nel database.</span><span class="sxs-lookup"><span data-stu-id="03019-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="03019-130">Con questo scenario non si registra la perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="03019-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="03019-131">I due utenti hanno aggiornato proprietà diverse.</span><span class="sxs-lookup"><span data-stu-id="03019-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="03019-132">Quando un utente torna a visualizzare il reparto English (Inglese), visualizza sia le modifiche di Jane sia quelle di John.</span><span class="sxs-lookup"><span data-stu-id="03019-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="03019-133">Questo metodo di aggiornamento riduce il numero di conflitti che possono comportare la perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="03019-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="03019-134">Questo approccio presenta alcuni svantaggi:</span><span class="sxs-lookup"><span data-stu-id="03019-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="03019-135">Non evita la perdita di dati se vengono apportate modifiche concorrenti alla stessa proprietà.</span><span class="sxs-lookup"><span data-stu-id="03019-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="03019-136">Risulta in genere poco pratico in un'app Web.</span><span class="sxs-lookup"><span data-stu-id="03019-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="03019-137">Richiede la manutenzione di un volume importante di codice statico per tenere traccia di tutti i valori recuperati e i nuovi valori.</span><span class="sxs-lookup"><span data-stu-id="03019-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="03019-138">La gestione di grandi quantità di codice statico può ridurre le prestazioni dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="03019-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="03019-139">Può rendere più complesse le app rispetto al rilevamento della concorrenza in un'entità.</span><span class="sxs-lookup"><span data-stu-id="03019-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="03019-140">È possibile consentire che la modifica di John sovrascriva la modifica di Jane.</span><span class="sxs-lookup"><span data-stu-id="03019-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="03019-141">Quando un utente torna a visualizzare il reparto English (Inglese), visualizza 9/1/2013 e il valore $ 350.000,00 recuperato.</span><span class="sxs-lookup"><span data-stu-id="03019-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="03019-142">Questo scenario è detto *Priorità client* o *Last in Wins* (Priorità ultimo accesso).</span><span class="sxs-lookup"><span data-stu-id="03019-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="03019-143">Tutti i valori del client hanno la precedenza sugli elementi presenti nell'archivio dati. Se non si esegue alcuna codifica per la gestione della concorrenza, la WINS client viene eseguita automaticamente.</span><span class="sxs-lookup"><span data-stu-id="03019-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="03019-144">È possibile impedire l'aggiornamento del database con la modifica di John.</span><span class="sxs-lookup"><span data-stu-id="03019-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="03019-145">In genere, l'app:</span><span class="sxs-lookup"><span data-stu-id="03019-145">Typically, the app would:</span></span>

  * <span data-ttu-id="03019-146">Visualizza un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="03019-146">Display an error message.</span></span>
  * <span data-ttu-id="03019-147">Visualizza lo stato corrente dei dati.</span><span class="sxs-lookup"><span data-stu-id="03019-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="03019-148">Consente all'utente di riapplicare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="03019-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="03019-149">Questo scenario è detto *Store Wins* (Priorità archivio).</span><span class="sxs-lookup"><span data-stu-id="03019-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="03019-150">I valori dell'archivio dati hanno la precedenza sui valori inviati dal client. In questa esercitazione viene implementato lo scenario di WINS dello Store.</span><span class="sxs-lookup"><span data-stu-id="03019-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="03019-151">Questo metodo garantisce che nessuna modifica venga sovrascritta senza che un utente riceva un avviso.</span><span class="sxs-lookup"><span data-stu-id="03019-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="03019-152">Rilevamento dei conflitti in EF Core</span><span class="sxs-lookup"><span data-stu-id="03019-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="03019-153">EF Core genera eccezioni `DbConcurrencyException` quando rileva conflitti.</span><span class="sxs-lookup"><span data-stu-id="03019-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="03019-154">Il modello di dati deve essere configurato per abilitare il rilevamento dei conflitti.</span><span class="sxs-lookup"><span data-stu-id="03019-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="03019-155">Di seguito sono elencate alcune opzioni per abilitare il rilevamento dei conflitti:</span><span class="sxs-lookup"><span data-stu-id="03019-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="03019-156">Configurare EF Core in modo che includa i valori originali delle colonne configurate come [token di concorrenza](/ef/core/modeling/concurrency) nella clausola Where dei comandi Update e Delete.</span><span class="sxs-lookup"><span data-stu-id="03019-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="03019-157">Quando viene chiamato `SaveChanges`, la clausola Where cerca i valori originali di tutte le proprietà annotate con l'attributo [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute).</span><span class="sxs-lookup"><span data-stu-id="03019-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="03019-158">L'istruzione Update non troverà una riga da aggiornare se una delle proprietà del token di concorrenza è cambiata dopo la prima lettura della riga.</span><span class="sxs-lookup"><span data-stu-id="03019-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="03019-159">EF Core interpreta questa condizione come conflitto di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="03019-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="03019-160">Per le tabelle di database con molte colonne, questo approccio può risultare in clausole Where molto grandi e richiedere grandi quantità di stato.</span><span class="sxs-lookup"><span data-stu-id="03019-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="03019-161">Pertanto questo approccio è in genere sconsigliato e non è il metodo usato in questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="03019-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="03019-162">Nella tabella del database, includere una colonna di rilevamento che può essere usata per determinare quando è stata modificata una riga.</span><span class="sxs-lookup"><span data-stu-id="03019-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="03019-163">In un database di SQL Server il tipo di dati della colonna di rilevamento è `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="03019-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="03019-164">Il valore `rowversion` è un numero sequenziale che viene incrementato ogni volta che la riga viene aggiornata.</span><span class="sxs-lookup"><span data-stu-id="03019-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="03019-165">In un comando Update o Delete, la clausola Where include il valore originale della colonna di rilevamento (il numero di versione originale della riga).</span><span class="sxs-lookup"><span data-stu-id="03019-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="03019-166">Se la riga da aggiornare è stata modificata da un altro utente, il valore nella colonna `rowversion` è diverso dal valore originale.</span><span class="sxs-lookup"><span data-stu-id="03019-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="03019-167">In tal caso, l'istruzione Update o Delete non riesce a trovare la riga da aggiornare a causa della clausola Where.</span><span class="sxs-lookup"><span data-stu-id="03019-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="03019-168">EF Core genera un'eccezione di concorrenza quando nessuna riga è interessata da un comando Update o Delete.</span><span class="sxs-lookup"><span data-stu-id="03019-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="03019-169">Aggiungere una proprietà di rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="03019-169">Add a tracking property</span></span>

<span data-ttu-id="03019-170">In *Models/Department.cs* aggiungere una proprietà di rilevamento denominata RowVersion:</span><span class="sxs-lookup"><span data-stu-id="03019-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="03019-171">L'attributo [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) è quello che identifica la colonna come colonna di rilevamento della concorrenza.</span><span class="sxs-lookup"><span data-stu-id="03019-171">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="03019-172">L'API Fluent è un modo alternativo per specificare la proprietà di rilevamento:</span><span class="sxs-lookup"><span data-stu-id="03019-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="03019-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03019-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03019-174">Per un database di SQL Server l'attributo `[Timestamp]` per una proprietà dell'entità definita come matrice di byte:</span><span class="sxs-lookup"><span data-stu-id="03019-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="03019-175">Causa l'inclusione della colonna nelle clausole Where per Delete e Update.</span><span class="sxs-lookup"><span data-stu-id="03019-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="03019-176">Imposta il tipo di colonna nel database su [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="03019-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="03019-177">Il database genera un numero di versione di riga sequenziale che viene incrementato ogni volta che la riga viene aggiornata.</span><span class="sxs-lookup"><span data-stu-id="03019-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="03019-178">In un comando `Update` o `Delete` la clausola `Where` include il valore della versione della riga recuperato.</span><span class="sxs-lookup"><span data-stu-id="03019-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="03019-179">Se la riga da aggiornare è stata modificata dopo il recupero:</span><span class="sxs-lookup"><span data-stu-id="03019-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="03019-180">Il valore della versione della riga corrente non corrisponde al valore recuperato.</span><span class="sxs-lookup"><span data-stu-id="03019-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="03019-181">I comandi `Update` o `Delete` non trovano una riga perché la clausola `Where` cerca il valore della versione della riga recuperata.</span><span class="sxs-lookup"><span data-stu-id="03019-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="03019-182">Viene generata un'eccezione `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="03019-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="03019-183">Il codice seguente visualizza un frammento di T-SQL generato da EF Core quando viene aggiornato il nome di Department (Reparto):</span><span class="sxs-lookup"><span data-stu-id="03019-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="03019-184">Il codice evidenziato precedente visualizza la clausola `WHERE` contenente `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="03019-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="03019-185">Se il valore `RowVersion` del database non è uguale al parametro `RowVersion` (`@p2`) non viene aggiornata alcuna riga.</span><span class="sxs-lookup"><span data-stu-id="03019-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="03019-186">Il codice evidenziato seguente visualizza la notazione T-SQL che verifica che è stata aggiornata esattamente una riga:</span><span class="sxs-lookup"><span data-stu-id="03019-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="03019-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) Restituisce il numero di righe interessate dall'ultima istruzione.</span><span class="sxs-lookup"><span data-stu-id="03019-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="03019-188">Se non viene aggiornata alcuna riga, EF Core genera `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="03019-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03019-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03019-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="03019-190">Per un database SQLite l'attributo `[Timestamp]` per una proprietà dell'entità definita come matrice di byte:</span><span class="sxs-lookup"><span data-stu-id="03019-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="03019-191">Causa l'inclusione della colonna nelle clausole Where per Delete e Update.</span><span class="sxs-lookup"><span data-stu-id="03019-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="03019-192">Corrisponde a un tipo di colonna BLOB.</span><span class="sxs-lookup"><span data-stu-id="03019-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="03019-193">I trigger di database aggiornano la colonna RowVersion con una nuova matrice di byte casuali ogni volta che viene aggiornata una riga.</span><span class="sxs-lookup"><span data-stu-id="03019-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="03019-194">In un comando `Update` o `Delete` la clausola `Where` include il valore recuperato della colonna RowVersion.</span><span class="sxs-lookup"><span data-stu-id="03019-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="03019-195">Se la riga da aggiornare è stata modificata dopo il recupero:</span><span class="sxs-lookup"><span data-stu-id="03019-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="03019-196">Il valore della versione della riga corrente non corrisponde al valore recuperato.</span><span class="sxs-lookup"><span data-stu-id="03019-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="03019-197">Il comando `Update` o `Delete` non trova una riga perché la clausola `Where` cerca il valore della versione della riga originale.</span><span class="sxs-lookup"><span data-stu-id="03019-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="03019-198">Viene generata un'eccezione `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="03019-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="03019-199">Aggiornare il database</span><span class="sxs-lookup"><span data-stu-id="03019-199">Update the database</span></span>

<span data-ttu-id="03019-200">L'aggiunta della proprietà `RowVersion` cambia il modello di dati e ciò richiede una migrazione.</span><span class="sxs-lookup"><span data-stu-id="03019-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="03019-201">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="03019-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="03019-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03019-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="03019-203">Eseguire il comando seguente nella console di Gestione pacchetti:</span><span class="sxs-lookup"><span data-stu-id="03019-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="03019-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03019-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="03019-205">Eseguire il comando seguente in un terminale:</span><span class="sxs-lookup"><span data-stu-id="03019-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="03019-206">Questo comando:</span><span class="sxs-lookup"><span data-stu-id="03019-206">This command:</span></span>

* <span data-ttu-id="03019-207">Creare il file di migrazione *Migrations/{timestamp}_RowVersion.cs*.</span><span class="sxs-lookup"><span data-stu-id="03019-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="03019-208">Aggiornano il file *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="03019-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="03019-209">L'aggiornamento aggiunge al metodo `BuildModel` il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="03019-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="03019-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03019-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="03019-211">Eseguire il comando seguente nella console di Gestione pacchetti:</span><span class="sxs-lookup"><span data-stu-id="03019-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="03019-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03019-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="03019-213">Aprire il file `Migrations/<timestamp>_RowVersion.cs` e aggiungere il codice evidenziato:</span><span class="sxs-lookup"><span data-stu-id="03019-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="03019-214">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="03019-214">The preceding code:</span></span>

  * <span data-ttu-id="03019-215">Aggiorna le righe esistenti con valori BLOB casuali.</span><span class="sxs-lookup"><span data-stu-id="03019-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="03019-216">Aggiunge trigger di database che impostano la colonna RowVersion su un valore BLOB casuale ogni volta che viene aggiornata una riga.</span><span class="sxs-lookup"><span data-stu-id="03019-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="03019-217">Eseguire il comando seguente in un terminale:</span><span class="sxs-lookup"><span data-stu-id="03019-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="03019-218">Scaffolding delle pagine Department</span><span class="sxs-lookup"><span data-stu-id="03019-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03019-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03019-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="03019-220">Seguire le istruzioni in [Scaffolding delle pagine Student](xref:data/ef-rp/intro#scaffold-student-pages) con le eccezioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="03019-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="03019-221">Creare una cartella *Pages/Departments*.</span><span class="sxs-lookup"><span data-stu-id="03019-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="03019-222">Usare `Department` per la classe del modello.</span><span class="sxs-lookup"><span data-stu-id="03019-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="03019-223">Usare la classe di contesto esistente anziché crearne una nuova.</span><span class="sxs-lookup"><span data-stu-id="03019-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03019-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03019-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="03019-225">Creare una cartella *Pages/Departments*.</span><span class="sxs-lookup"><span data-stu-id="03019-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="03019-226">Eseguire il comando seguente per eseguire lo scaffolding delle pagine Department.</span><span class="sxs-lookup"><span data-stu-id="03019-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="03019-227">**In Windows:**</span><span class="sxs-lookup"><span data-stu-id="03019-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="03019-228">**In Linux o macOS:**</span><span class="sxs-lookup"><span data-stu-id="03019-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="03019-229">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="03019-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="03019-230">Aggiornare la pagina Index</span><span class="sxs-lookup"><span data-stu-id="03019-230">Update the Index page</span></span>

<span data-ttu-id="03019-231">Lo strumento di scaffolding crea una colonna `RowVersion` per la pagina Index, ma questo campo non verrebbe visualizzato in un'app in produzione.</span><span class="sxs-lookup"><span data-stu-id="03019-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="03019-232">In questa esercitazione, l'ultimo byte di `RowVersion` viene visualizzato per illustrare in modo più chiaro come funziona la gestione della concorrenza.</span><span class="sxs-lookup"><span data-stu-id="03019-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="03019-233">L'univocità dell'ultimo byte non è garantita.</span><span class="sxs-lookup"><span data-stu-id="03019-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="03019-234">Aggiornare la pagina *Pages\Departments\Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="03019-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="03019-235">Sostituire Index con Departments.</span><span class="sxs-lookup"><span data-stu-id="03019-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="03019-236">Modificare il codice che contiene `RowVersion` per visualizzare solo l'ultimo byte della matrice di byte.</span><span class="sxs-lookup"><span data-stu-id="03019-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="03019-237">Sostituire FirstMidName con FullName.</span><span class="sxs-lookup"><span data-stu-id="03019-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="03019-238">Il codice seguente mostra la pagina aggiornata:</span><span class="sxs-lookup"><span data-stu-id="03019-238">The following code shows the updated page:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="03019-239">Aggiornare il modello di pagina Edit</span><span class="sxs-lookup"><span data-stu-id="03019-239">Update the Edit page model</span></span>

<span data-ttu-id="03019-240">Aggiornare *Pages\Departments\Edit.cshtml.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="03019-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="03019-241">[OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) viene aggiornato con il valore `rowVersion` dall'entità al momento del recupero nel metodo `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="03019-241">The [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="03019-242">EF Core genera un comando SQL UPDATE con una clausola WHERE contenente il valore `RowVersion` originale.</span><span class="sxs-lookup"><span data-stu-id="03019-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="03019-243">Se il comando UPDATE non ha effetto su nessuna riga (nessuna riga ha il valore originale `RowVersion`), viene generata un'eccezione `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="03019-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="03019-244">Nel codice evidenziato precedente:</span><span class="sxs-lookup"><span data-stu-id="03019-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="03019-245">Il valore in `Department.RowVersion` corrisponde a quello presente nell'entità quando è stato recuperato in origine nella richiesta Get per la pagina Edit.</span><span class="sxs-lookup"><span data-stu-id="03019-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="03019-246">Il valore viene fornito al `OnPost` metodo da un campo nascosto nella pagina in Razor cui viene visualizzata l'entità da modificare.</span><span class="sxs-lookup"><span data-stu-id="03019-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="03019-247">Il valore del campo nascosto viene copiato in `Department.RowVersion` dallo strumento di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="03019-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="03019-248">`OriginalValue` è il valore che verrà usato da EF Core nella clausola Where.</span><span class="sxs-lookup"><span data-stu-id="03019-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="03019-249">Prima dell'esecuzione della riga di codice evidenziata, `OriginalValue` ha il valore che era presente nel database al momento della chiamata di `FirstOrDefaultAsync` in questo metodo, che potrebbe essere diverso da quello visualizzato nella pagina Edit.</span><span class="sxs-lookup"><span data-stu-id="03019-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="03019-250">Il codice evidenziato assicura che EF Core usi il valore `RowVersion` originale dell'entità visualizzata `Department` nella clausola Where dell'istruzione SQL Update.</span><span class="sxs-lookup"><span data-stu-id="03019-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="03019-251">Quando si verifica un errore di concorrenza, il codice evidenziato seguente ottiene i valori client (i valori inseriti in questo metodo) e i valori del database.</span><span class="sxs-lookup"><span data-stu-id="03019-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="03019-252">Il codice seguente aggiunge un messaggio di errore personalizzato per ogni colonna che ha valori del database diversi da quelli inseriti in `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="03019-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="03019-253">Il codice evidenziato seguente imposta il valore `RowVersion` sul nuovo valore recuperato dal database.</span><span class="sxs-lookup"><span data-stu-id="03019-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="03019-254">Quando l'utente fa di nuovo clic su **Salva** vengono rilevati solo gli errori di concorrenza che si verificano dopo l'ultima visualizzazione della pagina Edit (Modifica).</span><span class="sxs-lookup"><span data-stu-id="03019-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="03019-255">L'istruzione `ModelState.Remove` è necessaria perché `ModelState` presenta il valore obsoleto `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="03019-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="03019-256">Nella Razor pagina, il `ModelState` valore di un campo ha la precedenza sui valori delle proprietà del modello quando entrambi sono presenti.</span><span class="sxs-lookup"><span data-stu-id="03019-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-razor-page"></a><span data-ttu-id="03019-257">Aggiornare la Razor pagina</span><span class="sxs-lookup"><span data-stu-id="03019-257">Update the Razor page</span></span>

<span data-ttu-id="03019-258">Aggiornare *Pages/Departments/Edit.cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="03019-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="03019-259">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="03019-259">The preceding code:</span></span>

* <span data-ttu-id="03019-260">Aggiorna la direttiva `page` da `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="03019-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="03019-261">Aggiunge una versione di riga nascosta.</span><span class="sxs-lookup"><span data-stu-id="03019-261">Adds a hidden row version.</span></span> <span data-ttu-id="03019-262">L'aggiunta di `RowVersion` è necessaria per far sì che il postback associ il valore.</span><span class="sxs-lookup"><span data-stu-id="03019-262">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="03019-263">Visualizza l'ultimo byte di `RowVersion` a scopo di debug.</span><span class="sxs-lookup"><span data-stu-id="03019-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="03019-264">Sostituisce `ViewData` con l'elemento `InstructorNameSL` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="03019-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="03019-265">Eseguire il test dei conflitti di concorrenza con la pagina Edit (Modifica)</span><span class="sxs-lookup"><span data-stu-id="03019-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="03019-266">Aprire due istanze di browser con la pagina Edit (Modifica) e il reparto English (Inglese):</span><span class="sxs-lookup"><span data-stu-id="03019-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="03019-267">Eseguire l'app e selezionare Departments (Reparti).</span><span class="sxs-lookup"><span data-stu-id="03019-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="03019-268">Fare clic con il pulsante destro del mouse sul collegamento ipertestuale **Edit** (Modifica) per il reparto English (Inglese) e selezionare **Apri in una nuova scheda**.</span><span class="sxs-lookup"><span data-stu-id="03019-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="03019-269">Nella prima scheda fare clic sul collegamento ipertestuale **Edit** (Modifica) per il reparto English (Inglese).</span><span class="sxs-lookup"><span data-stu-id="03019-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="03019-270">Le due schede del browser visualizzano le stesse informazioni.</span><span class="sxs-lookup"><span data-stu-id="03019-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="03019-271">Modificare il nome nella prima scheda del browser e fare clic su **Salva**.</span><span class="sxs-lookup"><span data-stu-id="03019-271">Change the name in the first browser tab and click **Save**.</span></span>

![Pagina Department Edit (Modifica - Reparto) 1 dopo la modifica](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="03019-273">Il browser visualizza la pagina Index con il valore modificato e l'indicatore rowVersion aggiornato.</span><span class="sxs-lookup"><span data-stu-id="03019-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="03019-274">Si noti l'indicatore rowVersion aggiornato, che è visualizzato sul secondo postback nell'altra scheda.</span><span class="sxs-lookup"><span data-stu-id="03019-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="03019-275">Modificare un altro campo nella seconda scheda del browser.</span><span class="sxs-lookup"><span data-stu-id="03019-275">Change a different field in the second browser tab.</span></span>

![Pagina Department Edit (Modifica - Reparto) 2 dopo la modifica](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="03019-277">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="03019-277">Click **Save**.</span></span> <span data-ttu-id="03019-278">Vengono visualizzati messaggi di errore per tutti i campi che non corrispondono ai valori del database:</span><span class="sxs-lookup"><span data-stu-id="03019-278">You see error messages for all fields that don't match the database values:</span></span>

![Messaggio di errore della pagina Department Edit (Modifica - Reparto)](concurrency/_static/edit-error30.png)

<span data-ttu-id="03019-280">Questa finestra del browser non prevedeva la modifica del campo Name (Nome).</span><span class="sxs-lookup"><span data-stu-id="03019-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="03019-281">Copiare e incollare il valore corrente Languages (Lingue) nel campo Name (Nome).</span><span class="sxs-lookup"><span data-stu-id="03019-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="03019-282">Tabulazione. La convalida lato client rimuove il messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="03019-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="03019-283">Fare clic su **Salva**.</span><span class="sxs-lookup"><span data-stu-id="03019-283">Click **Save** again.</span></span> <span data-ttu-id="03019-284">Il valore immesso nella seconda scheda del browser viene salvato.</span><span class="sxs-lookup"><span data-stu-id="03019-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="03019-285">I valori salvati vengono visualizzati nella pagina Index.</span><span class="sxs-lookup"><span data-stu-id="03019-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="03019-286">Aggiornare la pagina Delete (Elimina)</span><span class="sxs-lookup"><span data-stu-id="03019-286">Update the Delete page</span></span>

<span data-ttu-id="03019-287">Aggiornare *Pages/Departments/Delete.cshtml.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="03019-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="03019-288">La pagina Delete (Elimina) rileva i conflitti di concorrenza quando l'entità è stata modificata dopo il recupero.</span><span class="sxs-lookup"><span data-stu-id="03019-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="03019-289">`Department.RowVersion` è la versione di riga quando l'entità è stata recuperata.</span><span class="sxs-lookup"><span data-stu-id="03019-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="03019-290">Quando EF Core crea il comando SQL DELETE include una clausola WHERE con `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="03019-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="03019-291">Se il comando SQL DELETE non ha effetto su nessuna riga:</span><span class="sxs-lookup"><span data-stu-id="03019-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="03019-292">`RowVersion` nel comando SQL DELETE non corrisponde a `RowVersion` nel database.</span><span class="sxs-lookup"><span data-stu-id="03019-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="03019-293">Viene generata un'eccezione DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="03019-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="03019-294">`OnGetAsync` viene chiamata con `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="03019-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="03019-295">Aggiornare la Razor pagina Elimina</span><span class="sxs-lookup"><span data-stu-id="03019-295">Update the Delete Razor page</span></span>

<span data-ttu-id="03019-296">Aggiornare *Pages/Departments/Delete.cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="03019-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="03019-297">Il codice precedente apporta le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="03019-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="03019-298">Aggiorna la direttiva `page` da `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="03019-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="03019-299">Aggiunge un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="03019-299">Adds an error message.</span></span>
* <span data-ttu-id="03019-300">Sostituisce FirstMidName con FullName nel campo **Administrator** (Amministratore).</span><span class="sxs-lookup"><span data-stu-id="03019-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="03019-301">Modifica `RowVersion` per visualizzare l'ultimo byte.</span><span class="sxs-lookup"><span data-stu-id="03019-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="03019-302">Aggiunge una versione di riga nascosta.</span><span class="sxs-lookup"><span data-stu-id="03019-302">Adds a hidden row version.</span></span> <span data-ttu-id="03019-303">L'aggiunta di `RowVersion` è necessaria per l'associazione del valore.</span><span class="sxs-lookup"><span data-stu-id="03019-303">`RowVersion` must be added so postgit add back binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="03019-304">Testare i conflitti di concorrenza</span><span class="sxs-lookup"><span data-stu-id="03019-304">Test concurrency conflicts</span></span>

<span data-ttu-id="03019-305">Creare un reparto di test.</span><span class="sxs-lookup"><span data-stu-id="03019-305">Create a test department.</span></span>

<span data-ttu-id="03019-306">Aprire due istanze del browser con la pagina Delete (Elimina):</span><span class="sxs-lookup"><span data-stu-id="03019-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="03019-307">Eseguire l'app e selezionare Departments (Reparti).</span><span class="sxs-lookup"><span data-stu-id="03019-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="03019-308">Fare clic con il pulsante destro del mouse sul collegamento ipertestuale **Delete** (Elimina) per il reparto di test e selezionare **Apri in una nuova scheda**.</span><span class="sxs-lookup"><span data-stu-id="03019-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="03019-309">Fare clic sul collegamento ipertestuale \*\*Edit \*\* (Modifica) per il reparto di test.</span><span class="sxs-lookup"><span data-stu-id="03019-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="03019-310">Le due schede del browser visualizzano le stesse informazioni.</span><span class="sxs-lookup"><span data-stu-id="03019-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="03019-311">Modificare il budget nella prima scheda del browser e fare clic su **Salva**.</span><span class="sxs-lookup"><span data-stu-id="03019-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="03019-312">Il browser visualizza la pagina Index con il valore modificato e l'indicatore rowVersion aggiornato.</span><span class="sxs-lookup"><span data-stu-id="03019-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="03019-313">Si noti l'indicatore rowVersion aggiornato, che è visualizzato sul secondo postback nell'altra scheda.</span><span class="sxs-lookup"><span data-stu-id="03019-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="03019-314">Eliminare il reparto di test dalla seconda scheda. Viene visualizzato un errore di concorrenza con i valori correnti del database.</span><span class="sxs-lookup"><span data-stu-id="03019-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="03019-315">Se si fa clic su **Delete** (Elimina) l'entità viene eliminata, salvo se l'elemento `RowVersion` è stato aggiornato.</span><span class="sxs-lookup"><span data-stu-id="03019-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03019-316">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="03019-316">Additional resources</span></span>

* <span data-ttu-id="03019-317">[Concurrency Tokens in EF Core](/ef/core/modeling/concurrency) (Token di concorrenza in EF Core)</span><span class="sxs-lookup"><span data-stu-id="03019-317">[Concurrency Tokens in EF Core](/ef/core/modeling/concurrency)</span></span>
* [<span data-ttu-id="03019-318">Gestione della concorrenza in EF Core</span><span class="sxs-lookup"><span data-stu-id="03019-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="03019-319">Debug dell'origine ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="03019-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="03019-320">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="03019-320">Next steps</span></span>

<span data-ttu-id="03019-321">Questa è l'ultima esercitazione nella serie.</span><span class="sxs-lookup"><span data-stu-id="03019-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="03019-322">Ulteriori argomenti sono trattati nella [versione MVC di questa serie di esercitazioni](xref:data/ef-mvc/index).</span><span class="sxs-lookup"><span data-stu-id="03019-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="03019-323">Esercitazione precedente</span><span class="sxs-lookup"><span data-stu-id="03019-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="03019-324">Questa esercitazione descrive la gestione dei conflitti quando più utenti aggiornano la stessa entità contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="03019-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="03019-325">Se si verificano problemi che non si è in grado di risolvere, [scaricare o visualizzare l'app completa](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="03019-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="03019-326">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="03019-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="03019-327">Conflitti di concorrenza</span><span class="sxs-lookup"><span data-stu-id="03019-327">Concurrency conflicts</span></span>

<span data-ttu-id="03019-328">Un conflitto di concorrenza si verifica quando:</span><span class="sxs-lookup"><span data-stu-id="03019-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="03019-329">Un utente passa alla pagina di modifica di un'entità.</span><span class="sxs-lookup"><span data-stu-id="03019-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="03019-330">Un altro utente aggiorna la stessa entità prima che la modifica del primo utente venga scritta nel database.</span><span class="sxs-lookup"><span data-stu-id="03019-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="03019-331">Se non è abilitato il rilevamento della concorrenza, quando si verificano aggiornamenti concorrenti:</span><span class="sxs-lookup"><span data-stu-id="03019-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="03019-332">L'ultimo aggiornamento è quello valido.</span><span class="sxs-lookup"><span data-stu-id="03019-332">The last update wins.</span></span> <span data-ttu-id="03019-333">In altri termini, nel database vengono salvati i valori dell'ultimo aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="03019-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="03019-334">I dati del primo aggiornamento vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="03019-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="03019-335">Concorrenza ottimistica</span><span class="sxs-lookup"><span data-stu-id="03019-335">Optimistic concurrency</span></span>

<span data-ttu-id="03019-336">La concorrenza ottimistica consente che si verifichino conflitti di concorrenza, quindi attiva le misure necessarie.</span><span class="sxs-lookup"><span data-stu-id="03019-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="03019-337">Ad esempio Jane visita la pagina Department Edit (Modifica - Reparto) e cambia il budget per il reparto English (Inglese) da $ 350.000,00 a $ 0,00.</span><span class="sxs-lookup"><span data-stu-id="03019-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Impostazione del budget su 0](concurrency/_static/change-budget.png)

<span data-ttu-id="03019-339">Prima che Jane faccia clic su **Salva** John visita la stessa pagina e cambia il valore del campo Start Date (Data inizio) da 9/1/2007 a 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="03019-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Impostazione della data di inizio su 2013](concurrency/_static/change-date.png)

<span data-ttu-id="03019-341">Jane fa clic su **Salva** per prima e vede la sua modifica quando il browser torna alla pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="03019-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Budget impostato su zero](concurrency/_static/budget-zero.png)

<span data-ttu-id="03019-343">John fa clic su **Salva** in una pagina Edit (Modifica) che visualizza ancora un budget pari a $ 350.000,00.</span><span class="sxs-lookup"><span data-stu-id="03019-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="03019-344">Le operazioni successive dipendono da come si decide di gestire i conflitti di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="03019-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="03019-345">La concorrenza ottimistica include le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="03019-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="03019-346">È possibile tenere traccia della proprietà che un utente ha modificato e aggiornare solo le colonne corrispondenti nel database.</span><span class="sxs-lookup"><span data-stu-id="03019-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="03019-347">Con questo scenario non si registra la perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="03019-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="03019-348">I due utenti hanno aggiornato proprietà diverse.</span><span class="sxs-lookup"><span data-stu-id="03019-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="03019-349">Quando un utente torna a visualizzare il reparto English (Inglese), visualizza sia le modifiche di Jane sia quelle di John.</span><span class="sxs-lookup"><span data-stu-id="03019-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="03019-350">Questo metodo di aggiornamento riduce il numero di conflitti che possono comportare la perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="03019-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="03019-351">Questo approccio:</span><span class="sxs-lookup"><span data-stu-id="03019-351">This approach:</span></span>
 
  * <span data-ttu-id="03019-352">Non evita la perdita di dati se vengono apportate modifiche concorrenti alla stessa proprietà.</span><span class="sxs-lookup"><span data-stu-id="03019-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="03019-353">Risulta in genere poco pratico in un'app Web.</span><span class="sxs-lookup"><span data-stu-id="03019-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="03019-354">Richiede la manutenzione di un volume importante di codice statico per tenere traccia di tutti i valori recuperati e i nuovi valori.</span><span class="sxs-lookup"><span data-stu-id="03019-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="03019-355">La gestione di grandi quantità di codice statico può ridurre le prestazioni dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="03019-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="03019-356">Può rendere più complesse le app rispetto al rilevamento della concorrenza in un'entità.</span><span class="sxs-lookup"><span data-stu-id="03019-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="03019-357">È possibile consentire che la modifica di John sovrascriva la modifica di Jane.</span><span class="sxs-lookup"><span data-stu-id="03019-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="03019-358">Quando un utente torna a visualizzare il reparto English (Inglese), visualizza 9/1/2013 e il valore $ 350.000,00 recuperato.</span><span class="sxs-lookup"><span data-stu-id="03019-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="03019-359">Questo scenario è detto *Priorità client* o *Last in Wins* (Priorità ultimo accesso).</span><span class="sxs-lookup"><span data-stu-id="03019-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="03019-360">Tutti i valori del client hanno la precedenza sugli elementi presenti nell'archivio dati. Se non si esegue alcuna codifica per la gestione della concorrenza, la WINS client viene eseguita automaticamente.</span><span class="sxs-lookup"><span data-stu-id="03019-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="03019-361">È possibile impedire che la modifica di John venga implementata nel database.</span><span class="sxs-lookup"><span data-stu-id="03019-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="03019-362">In genere, l'app:</span><span class="sxs-lookup"><span data-stu-id="03019-362">Typically, the app would:</span></span>

  * <span data-ttu-id="03019-363">Visualizza un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="03019-363">Display an error message.</span></span>
  * <span data-ttu-id="03019-364">Visualizza lo stato corrente dei dati.</span><span class="sxs-lookup"><span data-stu-id="03019-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="03019-365">Consente all'utente di riapplicare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="03019-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="03019-366">Questo scenario è detto *Store Wins* (Priorità archivio).</span><span class="sxs-lookup"><span data-stu-id="03019-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="03019-367">I valori dell'archivio dati hanno la precedenza sui valori inviati dal client. In questa esercitazione viene implementato lo scenario di WINS dello Store.</span><span class="sxs-lookup"><span data-stu-id="03019-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="03019-368">Questo metodo garantisce che nessuna modifica venga sovrascritta senza che un utente riceva un avviso.</span><span class="sxs-lookup"><span data-stu-id="03019-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="03019-369">Gestione della concorrenza</span><span class="sxs-lookup"><span data-stu-id="03019-369">Handling concurrency</span></span> 

<span data-ttu-id="03019-370">Quando una proprietà è configurata come [token di concorrenza](/ef/core/modeling/concurrency):</span><span class="sxs-lookup"><span data-stu-id="03019-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="03019-371">EF Core verifica che la proprietà non sia stata modificata dopo il suo recupero.</span><span class="sxs-lookup"><span data-stu-id="03019-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="03019-372">La verifica viene eseguita quando si chiama [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) o [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_).</span><span class="sxs-lookup"><span data-stu-id="03019-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="03019-373">Se la proprietà è stata modificata dopo il recupero, viene generata un'eccezione [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="03019-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) is thrown.</span></span> 

<span data-ttu-id="03019-374">Il database e il modello di dati devono essere configurati per supportare la generazione di `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="03019-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="03019-375">Rilevamento dei conflitti di concorrenza per una proprietà</span><span class="sxs-lookup"><span data-stu-id="03019-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="03019-376">È possibile rilevare i conflitti di concorrenza a livello delle proprietà con l'attributo [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="03019-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) attribute.</span></span> <span data-ttu-id="03019-377">L'attributo può essere applicato a più proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="03019-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="03019-378">Per altre informazioni, vedere [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations) (Annotazioni dei dati - ConcurrencyCheck).</span><span class="sxs-lookup"><span data-stu-id="03019-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="03019-379">L'attributo `[ConcurrencyCheck]` non viene usato in questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="03019-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="03019-380">Rilevamento dei conflitti di concorrenza per una riga</span><span class="sxs-lookup"><span data-stu-id="03019-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="03019-381">Per rilevare i conflitti di concorrenza si aggiunge al modello una colonna di rilevamento [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="03019-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="03019-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="03019-382">`rowversion` :</span></span>

* <span data-ttu-id="03019-383">È specifica per SQL Server.</span><span class="sxs-lookup"><span data-stu-id="03019-383">Is SQL Server specific.</span></span> <span data-ttu-id="03019-384">È possibile che altri database non dispongano di una funzionalità simile.</span><span class="sxs-lookup"><span data-stu-id="03019-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="03019-385">Viene usata per determinare che un'entità non è stata modificata dopo il suo recupero dal database.</span><span class="sxs-lookup"><span data-stu-id="03019-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="03019-386">Il database genera un numero `rowversion` sequenziale che viene incrementato ogni volta che la riga viene aggiornata.</span><span class="sxs-lookup"><span data-stu-id="03019-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="03019-387">In un comando `Update` o `Delete` la clausola `Where` include il valore recuperato di `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="03019-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="03019-388">Se la riga che viene aggiornata è stata modificata:</span><span class="sxs-lookup"><span data-stu-id="03019-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="03019-389">`rowversion` non corrisponde al valore recuperato.</span><span class="sxs-lookup"><span data-stu-id="03019-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="03019-390">I comandi `Update` o `Delete` non trovano una riga perché la clausola `Where` include il valore `rowversion` recuperato.</span><span class="sxs-lookup"><span data-stu-id="03019-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="03019-391">Viene generata un'eccezione `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="03019-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="03019-392">In EF Core quando nessuna riga è stata aggiornata da un comando `Update` o `Delete` viene generata un'eccezione di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="03019-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="03019-393">Aggiungere una proprietà di rilevamento all'entità Department</span><span class="sxs-lookup"><span data-stu-id="03019-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="03019-394">In *Models/Department.cs* aggiungere una proprietà di rilevamento denominata RowVersion:</span><span class="sxs-lookup"><span data-stu-id="03019-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="03019-395">L'attributo [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) specifica che questa colonna è inclusa nella clausola `Where` dei comandi `Update` e `Delete`.</span><span class="sxs-lookup"><span data-stu-id="03019-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="03019-396">L'attributo viene chiamato `Timestamp` perché le versioni precedenti di SQL Server usavano un tipo di dati SQL `timestamp` prima che questo fosse sostituito dal tipo SQL `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="03019-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="03019-397">L'API Fluent può anche specificare la proprietà di rilevamento:</span><span class="sxs-lookup"><span data-stu-id="03019-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="03019-398">Il codice seguente visualizza un frammento di T-SQL generato da EF Core quando viene aggiornato il nome di Department (Reparto):</span><span class="sxs-lookup"><span data-stu-id="03019-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="03019-399">Il codice evidenziato precedente visualizza la clausola `WHERE` contenente `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="03019-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="03019-400">Se nel database `RowVersion` non è uguale al parametro `RowVersion` (`@p2`) non viene aggiornata nessuna riga.</span><span class="sxs-lookup"><span data-stu-id="03019-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="03019-401">Il codice evidenziato seguente visualizza la notazione T-SQL che verifica che è stata aggiornata esattamente una riga:</span><span class="sxs-lookup"><span data-stu-id="03019-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="03019-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) Restituisce il numero di righe interessate dall'ultima istruzione.</span><span class="sxs-lookup"><span data-stu-id="03019-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="03019-403">Se non viene aggiornata nessuna riga, EF Core genera `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="03019-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="03019-404">Il codice T-SQL generato da EF Core è visibile nella finestra di output di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="03019-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="03019-405">Aggiornare il database</span><span class="sxs-lookup"><span data-stu-id="03019-405">Update the DB</span></span>

<span data-ttu-id="03019-406">L'aggiunta della proprietà `RowVersion` cambia il modello di database e ciò richiede una migrazione.</span><span class="sxs-lookup"><span data-stu-id="03019-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="03019-407">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="03019-407">Build the project.</span></span> <span data-ttu-id="03019-408">Digitare quanto segue in una finestra di comando:</span><span class="sxs-lookup"><span data-stu-id="03019-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="03019-409">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="03019-409">The preceding commands:</span></span>

* <span data-ttu-id="03019-410">Aggiungono il file di migrazione *Migrations/{timestamp}_RowVersion.cs*.</span><span class="sxs-lookup"><span data-stu-id="03019-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="03019-411">Aggiornano il file *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="03019-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="03019-412">L'aggiornamento aggiunge al metodo `BuildModel` il codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="03019-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="03019-413">Eseguono migrations per aggiornare il database.</span><span class="sxs-lookup"><span data-stu-id="03019-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="03019-414">Scaffolding del modello Departments (Reparti)</span><span class="sxs-lookup"><span data-stu-id="03019-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03019-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03019-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="03019-416">Seguire le istruzioni in [Eseguire lo scaffolding del modello Student (Studente)](xref:data/ef-rp/intro#scaffold-student-pages) e usare `Department` per la classe modello.</span><span class="sxs-lookup"><span data-stu-id="03019-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03019-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03019-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="03019-418">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="03019-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="03019-419">Il comando precedente esegue lo scaffolding del modello `Department`.</span><span class="sxs-lookup"><span data-stu-id="03019-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="03019-420">Aprire il progetto in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="03019-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="03019-421">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="03019-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="03019-422">Aggiornare la pagina Departments Index (Indice reparti)</span><span class="sxs-lookup"><span data-stu-id="03019-422">Update the Departments Index page</span></span>

<span data-ttu-id="03019-423">Il motore di scaffolding crea una colonna `RowVersion` per la pagina Index, ma questo campo non deve essere visualizzato.</span><span class="sxs-lookup"><span data-stu-id="03019-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="03019-424">In questa esercitazione, l'ultimo byte di `RowVersion` viene visualizzato per facilitare la comprensione della concorrenza.</span><span class="sxs-lookup"><span data-stu-id="03019-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="03019-425">L'univocità dell'ultimo byte non è garantita.</span><span class="sxs-lookup"><span data-stu-id="03019-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="03019-426">Un'app reale non visualizza `RowVersion` o l'ultimo byte di `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="03019-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="03019-427">Aggiornare la pagina Index:</span><span class="sxs-lookup"><span data-stu-id="03019-427">Update the Index page:</span></span>

* <span data-ttu-id="03019-428">Sostituire Index con Departments.</span><span class="sxs-lookup"><span data-stu-id="03019-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="03019-429">Sostituire il markup che contiene `RowVersion` con l'ultimo byte di `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="03019-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="03019-430">Sostituire FirstMidName con FullName.</span><span class="sxs-lookup"><span data-stu-id="03019-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="03019-431">Il markup seguente visualizza la pagina aggiornata:</span><span class="sxs-lookup"><span data-stu-id="03019-431">The following markup shows the updated page:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="03019-432">Aggiornare il modello di pagina Edit</span><span class="sxs-lookup"><span data-stu-id="03019-432">Update the Edit page model</span></span>

<span data-ttu-id="03019-433">Aggiornare *Pages\Departments\Edit.cshtml.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="03019-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="03019-434">Per rilevare un problema di concorrenza, [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) viene aggiornato con il valore `rowVersion` dall'entità dalla quale stato recuperato.</span><span class="sxs-lookup"><span data-stu-id="03019-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="03019-435">EF Core genera un comando SQL UPDATE con una clausola WHERE contenente il valore `RowVersion` originale.</span><span class="sxs-lookup"><span data-stu-id="03019-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="03019-436">Se il comando UPDATE non ha effetto su nessuna riga (nessuna riga ha il valore originale `RowVersion`), viene generata un'eccezione `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="03019-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="03019-437">Nel codice precedente, `Department.RowVersion` è il valore al momento del recupero dell'entità.</span><span class="sxs-lookup"><span data-stu-id="03019-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="03019-438">`OriginalValue` è il valore presente nel database quando in questo metodo è stato chiamato `FirstOrDefaultAsync`.</span><span class="sxs-lookup"><span data-stu-id="03019-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="03019-439">Il codice seguente ottiene i valori del client (i valori inseriti in questo metodo) e i valori del database:</span><span class="sxs-lookup"><span data-stu-id="03019-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="03019-440">Il codice seguente aggiunge un messaggio di errore personalizzato per ogni colonna che ha valori del database diversi da quelli inseriti in `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="03019-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="03019-441">Il codice evidenziato seguente imposta il valore `RowVersion` sul nuovo valore recuperato dal database.</span><span class="sxs-lookup"><span data-stu-id="03019-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="03019-442">Quando l'utente fa di nuovo clic su **Salva** vengono rilevati solo gli errori di concorrenza che si verificano dopo l'ultima visualizzazione della pagina Edit (Modifica).</span><span class="sxs-lookup"><span data-stu-id="03019-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="03019-443">L'istruzione `ModelState.Remove` è necessaria perché `ModelState` presenta il valore obsoleto `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="03019-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="03019-444">Nella Razor pagina, il `ModelState` valore di un campo ha la precedenza sui valori delle proprietà del modello quando entrambi sono presenti.</span><span class="sxs-lookup"><span data-stu-id="03019-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="03019-445">Aggiornare la pagina Edit (Modifica)</span><span class="sxs-lookup"><span data-stu-id="03019-445">Update the Edit page</span></span>

<span data-ttu-id="03019-446">Aggiornare *Pages/Departments/Edit.cshtml* con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="03019-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="03019-447">Il markup precedente:</span><span class="sxs-lookup"><span data-stu-id="03019-447">The preceding markup:</span></span>

* <span data-ttu-id="03019-448">Aggiorna la direttiva `page` da `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="03019-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="03019-449">Aggiunge una versione di riga nascosta.</span><span class="sxs-lookup"><span data-stu-id="03019-449">Adds a hidden row version.</span></span> <span data-ttu-id="03019-450">L'aggiunta di `RowVersion` è necessaria per far sì che il postback associ il valore.</span><span class="sxs-lookup"><span data-stu-id="03019-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="03019-451">Visualizza l'ultimo byte di `RowVersion` a scopo di debug.</span><span class="sxs-lookup"><span data-stu-id="03019-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="03019-452">Sostituisce `ViewData` con l'elemento `InstructorNameSL` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="03019-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="03019-453">Eseguire il test dei conflitti di concorrenza con la pagina Edit (Modifica)</span><span class="sxs-lookup"><span data-stu-id="03019-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="03019-454">Aprire due istanze di browser con la pagina Edit (Modifica) e il reparto English (Inglese):</span><span class="sxs-lookup"><span data-stu-id="03019-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="03019-455">Eseguire l'app e selezionare Departments (Reparti).</span><span class="sxs-lookup"><span data-stu-id="03019-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="03019-456">Fare clic con il pulsante destro del mouse sul collegamento ipertestuale **Edit** (Modifica) per il reparto English (Inglese) e selezionare **Apri in una nuova scheda**.</span><span class="sxs-lookup"><span data-stu-id="03019-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="03019-457">Nella prima scheda fare clic sul collegamento ipertestuale **Edit** (Modifica) per il reparto English (Inglese).</span><span class="sxs-lookup"><span data-stu-id="03019-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="03019-458">Le due schede del browser visualizzano le stesse informazioni.</span><span class="sxs-lookup"><span data-stu-id="03019-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="03019-459">Modificare il nome nella prima scheda del browser e fare clic su **Salva**.</span><span class="sxs-lookup"><span data-stu-id="03019-459">Change the name in the first browser tab and click **Save**.</span></span>

![Pagina Department Edit (Modifica - Reparto) 1 dopo la modifica](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="03019-461">Il browser visualizza la pagina Index con il valore modificato e l'indicatore rowVersion aggiornato.</span><span class="sxs-lookup"><span data-stu-id="03019-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="03019-462">Si noti l'indicatore rowVersion aggiornato, che è visualizzato sul secondo postback nell'altra scheda.</span><span class="sxs-lookup"><span data-stu-id="03019-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="03019-463">Modificare un altro campo nella seconda scheda del browser.</span><span class="sxs-lookup"><span data-stu-id="03019-463">Change a different field in the second browser tab.</span></span>

![Pagina Department Edit (Modifica - Reparto) 2 dopo la modifica](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="03019-465">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="03019-465">Click **Save**.</span></span> <span data-ttu-id="03019-466">Vengono visualizzati messaggi di errore per tutti i campi che non corrispondono ai valori del database:</span><span class="sxs-lookup"><span data-stu-id="03019-466">You see error messages for all fields that don't match the DB values:</span></span>

![Messaggio di errore della pagina Department Edit (Modifica - Reparto)](concurrency/_static/edit-error.png)

<span data-ttu-id="03019-468">Questa finestra del browser non prevedeva la modifica del campo Name (Nome).</span><span class="sxs-lookup"><span data-stu-id="03019-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="03019-469">Copiare e incollare il valore corrente Languages (Lingue) nel campo Name (Nome).</span><span class="sxs-lookup"><span data-stu-id="03019-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="03019-470">Tabulazione. La convalida lato client rimuove il messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="03019-470">Tab out. Client-side validation removes the error message.</span></span>

![Messaggio di errore della pagina Department Edit (Modifica - Reparto)](concurrency/_static/cv.png)

<span data-ttu-id="03019-472">Fare clic su **Salva**.</span><span class="sxs-lookup"><span data-stu-id="03019-472">Click **Save** again.</span></span> <span data-ttu-id="03019-473">Il valore immesso nella seconda scheda del browser viene salvato.</span><span class="sxs-lookup"><span data-stu-id="03019-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="03019-474">I valori salvati vengono visualizzati nella pagina Index.</span><span class="sxs-lookup"><span data-stu-id="03019-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="03019-475">Aggiornare la pagina Delete (Elimina)</span><span class="sxs-lookup"><span data-stu-id="03019-475">Update the Delete page</span></span>

<span data-ttu-id="03019-476">Aggiornare il modello di pagina Delete (Elimina) con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="03019-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="03019-477">La pagina Delete (Elimina) rileva i conflitti di concorrenza quando l'entità è stata modificata dopo il recupero.</span><span class="sxs-lookup"><span data-stu-id="03019-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="03019-478">`Department.RowVersion` è la versione di riga quando l'entità è stata recuperata.</span><span class="sxs-lookup"><span data-stu-id="03019-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="03019-479">Quando EF Core crea il comando SQL DELETE include una clausola WHERE con `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="03019-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="03019-480">Se il comando SQL DELETE non ha effetto su nessuna riga:</span><span class="sxs-lookup"><span data-stu-id="03019-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="03019-481">`RowVersion` nel comando SQL DELETE non corrisponde a `RowVersion` nel database.</span><span class="sxs-lookup"><span data-stu-id="03019-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="03019-482">Viene generata un'eccezione DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="03019-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="03019-483">`OnGetAsync` viene chiamata con `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="03019-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="03019-484">Aggiornare la pagina Delete (Elimina)</span><span class="sxs-lookup"><span data-stu-id="03019-484">Update the Delete page</span></span>

<span data-ttu-id="03019-485">Aggiornare *Pages/Departments/Delete.cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="03019-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="03019-486">Il codice precedente apporta le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="03019-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="03019-487">Aggiorna la direttiva `page` da `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="03019-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="03019-488">Aggiunge un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="03019-488">Adds an error message.</span></span>
* <span data-ttu-id="03019-489">Sostituisce FirstMidName con FullName nel campo **Administrator** (Amministratore).</span><span class="sxs-lookup"><span data-stu-id="03019-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="03019-490">Modifica `RowVersion` per visualizzare l'ultimo byte.</span><span class="sxs-lookup"><span data-stu-id="03019-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="03019-491">Aggiunge una versione di riga nascosta.</span><span class="sxs-lookup"><span data-stu-id="03019-491">Adds a hidden row version.</span></span> <span data-ttu-id="03019-492">L'aggiunta di `RowVersion` è necessaria per far sì che il postback associ il valore.</span><span class="sxs-lookup"><span data-stu-id="03019-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="03019-493">Eseguire il test dei conflitti di concorrenza con la pagina Delete (Elimina)</span><span class="sxs-lookup"><span data-stu-id="03019-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="03019-494">Creare un reparto di test.</span><span class="sxs-lookup"><span data-stu-id="03019-494">Create a test department.</span></span>

<span data-ttu-id="03019-495">Aprire due istanze del browser con la pagina Delete (Elimina):</span><span class="sxs-lookup"><span data-stu-id="03019-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="03019-496">Eseguire l'app e selezionare Departments (Reparti).</span><span class="sxs-lookup"><span data-stu-id="03019-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="03019-497">Fare clic con il pulsante destro del mouse sul collegamento ipertestuale **Delete** (Elimina) per il reparto di test e selezionare **Apri in una nuova scheda**.</span><span class="sxs-lookup"><span data-stu-id="03019-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="03019-498">Fare clic sul collegamento ipertestuale \*\*Edit \*\* (Modifica) per il reparto di test.</span><span class="sxs-lookup"><span data-stu-id="03019-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="03019-499">Le due schede del browser visualizzano le stesse informazioni.</span><span class="sxs-lookup"><span data-stu-id="03019-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="03019-500">Modificare il budget nella prima scheda del browser e fare clic su **Salva**.</span><span class="sxs-lookup"><span data-stu-id="03019-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="03019-501">Il browser visualizza la pagina Index con il valore modificato e l'indicatore rowVersion aggiornato.</span><span class="sxs-lookup"><span data-stu-id="03019-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="03019-502">Si noti l'indicatore rowVersion aggiornato, che è visualizzato sul secondo postback nell'altra scheda.</span><span class="sxs-lookup"><span data-stu-id="03019-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="03019-503">Eliminare il reparto di test dalla seconda scheda. Viene visualizzato un errore di concorrenza con i valori correnti del database.</span><span class="sxs-lookup"><span data-stu-id="03019-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="03019-504">Se si fa clic su **Delete** (Elimina) l'entità viene eliminata, salvo se l'elemento `RowVersion` è stato aggiornato.</span><span class="sxs-lookup"><span data-stu-id="03019-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

<span data-ttu-id="03019-505">Per informazioni su come ereditare un modello di dati, vedere [Ereditarietà](xref:data/ef-mvc/inheritance).</span><span class="sxs-lookup"><span data-stu-id="03019-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="03019-506">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="03019-506">Additional resources</span></span>

* <span data-ttu-id="03019-507">[Concurrency Tokens in EF Core](/ef/core/modeling/concurrency) (Token di concorrenza in EF Core)</span><span class="sxs-lookup"><span data-stu-id="03019-507">[Concurrency Tokens in EF Core](/ef/core/modeling/concurrency)</span></span>
* [<span data-ttu-id="03019-508">Gestione della concorrenza in EF Core</span><span class="sxs-lookup"><span data-stu-id="03019-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="03019-509">Versione YouTube dell'esercitazione (Gestione dei conflitti di concorrenza)</span><span class="sxs-lookup"><span data-stu-id="03019-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="03019-510">Versione YouTube dell'esercitazione (parte 2)</span><span class="sxs-lookup"><span data-stu-id="03019-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="03019-511">Versione YouTube dell'esercitazione (parte 3)</span><span class="sxs-lookup"><span data-stu-id="03019-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="03019-512">Indietro</span><span class="sxs-lookup"><span data-stu-id="03019-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

