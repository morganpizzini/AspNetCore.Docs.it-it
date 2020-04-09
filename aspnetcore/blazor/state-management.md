---
title: Gestione Blazor dello stato di ASP.NET Core
author: guardrex
description: Informazioni su come Blazor mantenere lo stato nelle app server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218869"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="1d54e-103">Gestione Blazor dello stato di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d54e-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="1d54e-104">Di [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="1d54e-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="1d54e-105">Il server è un framework dell'app con stato.</span><span class="sxs-lookup"><span data-stu-id="1d54e-105"> Server is a stateful app framework.</span></span> <span data-ttu-id="1d54e-106">Nella maggior parte dei criteri, l'app mantiene una connessione continua al server.</span><span class="sxs-lookup"><span data-stu-id="1d54e-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="1d54e-107">Lo stato dell'utente viene mantenuto nella memoria del server in un *circuito*.</span><span class="sxs-lookup"><span data-stu-id="1d54e-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="1d54e-108">Esempi di stato tenuto per il circuito di un utente includono:</span><span class="sxs-lookup"><span data-stu-id="1d54e-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="1d54e-109">Interfaccia utente&mdash;di cui è stato eseguito il rendering la gerarchia delle istanze del componente e l'output di rendering più recente.</span><span class="sxs-lookup"><span data-stu-id="1d54e-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="1d54e-110">I valori di tutti i campi e le proprietà nelle istanze del componente.</span><span class="sxs-lookup"><span data-stu-id="1d54e-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="1d54e-111">I dati contenuti nelle istanze del servizio [di inserimento delle dipendenze (DI)](xref:fundamentals/dependency-injection) con ambito al circuito.</span><span class="sxs-lookup"><span data-stu-id="1d54e-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="1d54e-112">Questo articolo riguarda Blazor la persistenza dello stato nelle app del server.</span><span class="sxs-lookup"><span data-stu-id="1d54e-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="1d54e-113">Le app WebAssembly possono sfruttare la [persistenza dello stato sul lato client nel browser,](#client-side-in-the-browser) ma richiedono soluzioni personalizzate o pacchetti di terze parti oltre l'ambito di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="1d54e-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a>Blazor<span data-ttu-id="1d54e-114">Circuiti</span><span class="sxs-lookup"><span data-stu-id="1d54e-114"> circuits</span></span>

<span data-ttu-id="1d54e-115">Se un utente si verifica Blazor una perdita di connessione di rete temporanea, tenta di riconnettere l'utente al circuito originale in modo che possa continuare a usare l'app.</span><span class="sxs-lookup"><span data-stu-id="1d54e-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="1d54e-116">Tuttavia, la riconnessione di un utente al circuito originale nella memoria del server non è sempre possibile:</span><span class="sxs-lookup"><span data-stu-id="1d54e-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="1d54e-117">Il server non può mantenere un circuito disconnesso per sempre.</span><span class="sxs-lookup"><span data-stu-id="1d54e-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="1d54e-118">Il server deve rilasciare un circuito disconnesso dopo un timeout o quando il server è sotto pressione della memoria.</span><span class="sxs-lookup"><span data-stu-id="1d54e-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="1d54e-119">Negli ambienti di distribuzione multiserver con carico bilanciato, tutte le richieste di elaborazione del server potrebbero non essere disponibili in un determinato momento.</span><span class="sxs-lookup"><span data-stu-id="1d54e-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="1d54e-120">I singoli server potrebbero non riuscire o essere rimossi automaticamente quando non sono più necessari per gestire il volume complessivo delle richieste.</span><span class="sxs-lookup"><span data-stu-id="1d54e-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="1d54e-121">Il server originale potrebbe non essere disponibile quando l'utente tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="1d54e-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="1d54e-122">L'utente potrebbe chiudere e riaprire il browser o ricaricare la pagina, rimuovendo qualsiasi stato contenuto nella memoria del browser.</span><span class="sxs-lookup"><span data-stu-id="1d54e-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="1d54e-123">Ad esempio, i valori impostati tramite chiamate di interoperabilità JavaScript vengono persi.</span><span class="sxs-lookup"><span data-stu-id="1d54e-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="1d54e-124">Quando un utente non può essere riconnesso al circuito originale, riceve un nuovo circuito con uno stato vuoto.</span><span class="sxs-lookup"><span data-stu-id="1d54e-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="1d54e-125">Ciò equivale alla chiusura e alla riapertura di un'app desktop.</span><span class="sxs-lookup"><span data-stu-id="1d54e-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="1d54e-126">Conservare lo stato tra i circuiti</span><span class="sxs-lookup"><span data-stu-id="1d54e-126">Preserve state across circuits</span></span>

<span data-ttu-id="1d54e-127">In alcuni scenari, è auspicabile mantenere lo stato tra i circuiti.</span><span class="sxs-lookup"><span data-stu-id="1d54e-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="1d54e-128">Un'app può conservare dati importanti per un utente se:An app can retain important data for a user if:</span><span class="sxs-lookup"><span data-stu-id="1d54e-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="1d54e-129">Il server Web non è più disponibile.</span><span class="sxs-lookup"><span data-stu-id="1d54e-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="1d54e-130">Il browser dell'utente è costretto ad avviare un nuovo circuito con un nuovo server web.</span><span class="sxs-lookup"><span data-stu-id="1d54e-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="1d54e-131">In generale, la gestione dello stato tra i circuiti si applica agli scenari in cui gli utenti creano attivamente dati, non alla semplice lettura dei dati già esistenti.</span><span class="sxs-lookup"><span data-stu-id="1d54e-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="1d54e-132">Per mantenere lo stato oltre un singolo circuito, *non archiviare semplicemente i dati nella memoria del server.*</span><span class="sxs-lookup"><span data-stu-id="1d54e-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="1d54e-133">L'app deve rendere persistenti i dati in un'altra posizione di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="1d54e-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="1d54e-134">La persistenza dello&mdash;stato non è automatica, è necessario eseguire le operazioni quando si sviluppa l'app per implementare la persistenza dei dati con stato.</span><span class="sxs-lookup"><span data-stu-id="1d54e-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="1d54e-135">La persistenza dei dati è in genere necessaria solo per lo stato di valore elevato che gli utenti hanno speso per creare.</span><span class="sxs-lookup"><span data-stu-id="1d54e-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="1d54e-136">Negli esempi seguenti, lo stato persistente consente di risparmiare tempo o aiuti nelle attività commerciali:</span><span class="sxs-lookup"><span data-stu-id="1d54e-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="1d54e-137">Modulo Web &ndash; multifase È dispendioso in termini di tempo per un utente reinserire i dati per diversi passaggi completati di un processo a più passaggi se il loro stato viene perso.</span><span class="sxs-lookup"><span data-stu-id="1d54e-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="1d54e-138">Un utente perde lo stato in questo scenario se si sposta dal form a più passaggi e tornare al modulo in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="1d54e-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="1d54e-139">Carrello &ndash; della spesa È possibile mantenere qualsiasi componente commercialmente importante di un'app che rappresenta potenziali entrate.</span><span class="sxs-lookup"><span data-stu-id="1d54e-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="1d54e-140">Un utente che perde il proprio stato, e quindi il carrello, può acquistare meno prodotti o servizi quando torna al sito in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="1d54e-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="1d54e-141">In genere non è necessario mantenere lo stato ricreato facilmente, ad esempio il nome utente immesso in una finestra di dialogo di accesso che non è stata inviata.</span><span class="sxs-lookup"><span data-stu-id="1d54e-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1d54e-142">Un'app può solo rendere persistente *lo stato dell'app.*</span><span class="sxs-lookup"><span data-stu-id="1d54e-142">An app can only persist *app state*.</span></span> <span data-ttu-id="1d54e-143">Le api non possono essere rese persistenti, ad esempio le istanze dei componenti e i relativi alberi di rendering.</span><span class="sxs-lookup"><span data-stu-id="1d54e-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="1d54e-144">I componenti e gli alberi di rendering non sono in genere serializzabili.</span><span class="sxs-lookup"><span data-stu-id="1d54e-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="1d54e-145">Per rendere persistente un elemento simile allo stato dell'interfaccia utente, ad esempio i nodi espansi di un controllo TreeView, l'app deve disporre di codice personalizzato per modellare il comportamento come stato serializzabile dell'app.</span><span class="sxs-lookup"><span data-stu-id="1d54e-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="1d54e-146">Dove mantenere lo stato</span><span class="sxs-lookup"><span data-stu-id="1d54e-146">Where to persist state</span></span>

<span data-ttu-id="1d54e-147">Esistono tre posizioni comuni per Blazor la persistenza dello stato in un'app Server.Three common locations exist for persisting state in a Server app.</span><span class="sxs-lookup"><span data-stu-id="1d54e-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="1d54e-148">Ogni approccio è più adatto a scenari diversi e ha diversi avvertimenti:</span><span class="sxs-lookup"><span data-stu-id="1d54e-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="1d54e-149">Lato server in un database</span><span class="sxs-lookup"><span data-stu-id="1d54e-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="1d54e-150">URL</span><span class="sxs-lookup"><span data-stu-id="1d54e-150">URL</span></span>](#url)
* [<span data-ttu-id="1d54e-151">Lato client nel browser</span><span class="sxs-lookup"><span data-stu-id="1d54e-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="1d54e-152">Lato server in un database</span><span class="sxs-lookup"><span data-stu-id="1d54e-152">Server-side in a database</span></span>

<span data-ttu-id="1d54e-153">Per la persistenza permanente dei dati o per tutti i dati che devono estendersi su più utenti o dispositivi, un database lato server indipendente è quasi certamente la scelta migliore.</span><span class="sxs-lookup"><span data-stu-id="1d54e-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="1d54e-154">Le opzioni includono:</span><span class="sxs-lookup"><span data-stu-id="1d54e-154">Options include:</span></span>

* <span data-ttu-id="1d54e-155">Database SQL relazionale</span><span class="sxs-lookup"><span data-stu-id="1d54e-155">Relational SQL database</span></span>
* <span data-ttu-id="1d54e-156">Archivio chiave-valore</span><span class="sxs-lookup"><span data-stu-id="1d54e-156">Key-value store</span></span>
* <span data-ttu-id="1d54e-157">Archivio BLOB</span><span class="sxs-lookup"><span data-stu-id="1d54e-157">Blob store</span></span>
* <span data-ttu-id="1d54e-158">Archivio tabelle</span><span class="sxs-lookup"><span data-stu-id="1d54e-158">Table store</span></span>

<span data-ttu-id="1d54e-159">Dopo aver salvato i dati nel database, un nuovo circuito può essere avviato da un utente in qualsiasi momento.</span><span class="sxs-lookup"><span data-stu-id="1d54e-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="1d54e-160">I dati dell'utente vengono conservati e disponibili in qualsiasi nuovo circuito.</span><span class="sxs-lookup"><span data-stu-id="1d54e-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="1d54e-161">Per altre informazioni sulle opzioni di archiviazione dei dati di Azure, vedere la [documentazione di Archiviazione di Azure](/azure/storage/) e database di Azure.For more information on Azure data storage options, see the Azure Storage Documentation and [Azure Databases.](https://azure.microsoft.com/product-categories/databases/)</span><span class="sxs-lookup"><span data-stu-id="1d54e-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="1d54e-162">URL</span><span class="sxs-lookup"><span data-stu-id="1d54e-162">URL</span></span>

<span data-ttu-id="1d54e-163">Per i dati temporanei che rappresentano lo stato di navigazione, modellare i dati come parte dell'URL.</span><span class="sxs-lookup"><span data-stu-id="1d54e-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="1d54e-164">Di seguito sono riportati alcuni esempi di stato modellato nell'URL:</span><span class="sxs-lookup"><span data-stu-id="1d54e-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="1d54e-165">ID di un'entità visualizzata.</span><span class="sxs-lookup"><span data-stu-id="1d54e-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="1d54e-166">Numero di pagina corrente in una griglia di paging.</span><span class="sxs-lookup"><span data-stu-id="1d54e-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="1d54e-167">Il contenuto della barra degli indirizzi del browser viene mantenuto:</span><span class="sxs-lookup"><span data-stu-id="1d54e-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="1d54e-168">Se l'utente ricarica manualmente la pagina.</span><span class="sxs-lookup"><span data-stu-id="1d54e-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="1d54e-169">Se il server Web&mdash;non è più disponibile, l'utente è costretto a ricaricare la pagina per connettersi a un altro server.</span><span class="sxs-lookup"><span data-stu-id="1d54e-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="1d54e-170">Per informazioni sulla definizione `@page` di <xref:blazor/routing>modelli di URL con la direttiva , vedere .</span><span class="sxs-lookup"><span data-stu-id="1d54e-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="1d54e-171">Lato client nel browser</span><span class="sxs-lookup"><span data-stu-id="1d54e-171">Client-side in the browser</span></span>

<span data-ttu-id="1d54e-172">Per i dati temporanei che l'utente sta creando attivamente, un `localStorage` `sessionStorage` archivio di backup comune è il browser e le raccolte.</span><span class="sxs-lookup"><span data-stu-id="1d54e-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="1d54e-173">L'app non è necessaria per gestire o cancellare lo stato archiviato se il circuito viene abbandonato, il che è un vantaggio rispetto all'archiviazione lato server.</span><span class="sxs-lookup"><span data-stu-id="1d54e-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="1d54e-174">"Lato client" in questa sezione si riferisce agli [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)scenari lato client nel browser, non al modello di hosting WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="1d54e-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="1d54e-175">`localStorage`e `sessionStorage` può essere Blazor utilizzato nelle applicazioni WebAssembly, ma solo scrivendo codice personalizzato o utilizzando un pacchetto di terze parti.</span><span class="sxs-lookup"><span data-stu-id="1d54e-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="1d54e-176">`localStorage`e `sessionStorage` differiscono come segue:</span><span class="sxs-lookup"><span data-stu-id="1d54e-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="1d54e-177">`localStorage`ha come ambito il browser dell'utente.</span><span class="sxs-lookup"><span data-stu-id="1d54e-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="1d54e-178">Se l'utente ricarica la pagina o chiude e riapre il browser, lo stato viene mantenuto.</span><span class="sxs-lookup"><span data-stu-id="1d54e-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="1d54e-179">Se l'utente apre più schede del browser, lo stato viene condiviso tra le schede.</span><span class="sxs-lookup"><span data-stu-id="1d54e-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="1d54e-180">I dati `localStorage` vengono mantenuti fino a quando non vengono cancellati in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="1d54e-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="1d54e-181">`sessionStorage`ha come ambito la scheda del browser dell'utente. Se l'utente ricarica la scheda, lo stato persiste.</span><span class="sxs-lookup"><span data-stu-id="1d54e-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="1d54e-182">Se l'utente chiude la scheda o il browser, lo stato viene perso.</span><span class="sxs-lookup"><span data-stu-id="1d54e-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="1d54e-183">Se l'utente apre più schede del browser, ogni scheda ha la propria versione indipendente dei dati.</span><span class="sxs-lookup"><span data-stu-id="1d54e-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="1d54e-184">Generalmente, `sessionStorage` è più sicuro da usare.</span><span class="sxs-lookup"><span data-stu-id="1d54e-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="1d54e-185">`sessionStorage`evita il rischio che un utente apra più schede e incontri quanto segue:</span><span class="sxs-lookup"><span data-stu-id="1d54e-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="1d54e-186">Bug nell'archiviazione dello stato tra le schede.</span><span class="sxs-lookup"><span data-stu-id="1d54e-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="1d54e-187">Comportamento di confusione quando una scheda sovrascrive lo stato di altre schede.</span><span class="sxs-lookup"><span data-stu-id="1d54e-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="1d54e-188">`localStorage`è la scelta migliore se l'app deve mantenere lo stato durante la chiusura e la riapertura del browser.</span><span class="sxs-lookup"><span data-stu-id="1d54e-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="1d54e-189">Avvertenze per l'utilizzo dell'archiviazione del browser:</span><span class="sxs-lookup"><span data-stu-id="1d54e-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="1d54e-190">Analogamente all'uso di un database sul lato server, il caricamento e il salvataggio dei dati sono asincroni.</span><span class="sxs-lookup"><span data-stu-id="1d54e-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="1d54e-191">A differenza di un database sul lato server, l'archiviazione non è disponibile durante il prerendering perché la pagina richiesta non esiste nel browser durante la fase di prerendering.</span><span class="sxs-lookup"><span data-stu-id="1d54e-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="1d54e-192">L'archiviazione di alcuni kilobyte di Blazor dati è ragionevole per mantenere per le applicazioni Server.</span><span class="sxs-lookup"><span data-stu-id="1d54e-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="1d54e-193">Oltre a pochi kilobyte, è necessario considerare le implicazioni in termini di prestazioni perché i dati vengono caricati e salvati in rete.</span><span class="sxs-lookup"><span data-stu-id="1d54e-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="1d54e-194">Gli utenti possono visualizzare o manomettere i dati.</span><span class="sxs-lookup"><span data-stu-id="1d54e-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="1d54e-195">ASP.NET core [Data Protection](xref:security/data-protection/introduction) può ridurre il rischio.</span><span class="sxs-lookup"><span data-stu-id="1d54e-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="1d54e-196">Soluzioni di archiviazione di browser di terze parti</span><span class="sxs-lookup"><span data-stu-id="1d54e-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="1d54e-197">I pacchetti NuGet di terze parti `localStorage` forniscono API per l'utilizzo con e `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="1d54e-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="1d54e-198">Vale la pena scegliere un pacchetto che utilizza in modo trasparente ASP.NET [protezione dei dati](xref:security/data-protection/introduction)di Core .</span><span class="sxs-lookup"><span data-stu-id="1d54e-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="1d54e-199">ASP.NET Core Data Protection crittografa i dati memorizzati e riduce il rischio potenziale di manomissione dei dati archiviati.</span><span class="sxs-lookup"><span data-stu-id="1d54e-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="1d54e-200">Se i dati serializzati IN JSON vengono archiviati in testo non crittografato, gli utenti possono visualizzare i dati utilizzando gli strumenti di sviluppo del browser e anche modificare i dati archiviati.</span><span class="sxs-lookup"><span data-stu-id="1d54e-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="1d54e-201">La protezione dei dati non è sempre un problema perché i dati potrebbero essere di natura semplice.</span><span class="sxs-lookup"><span data-stu-id="1d54e-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="1d54e-202">Ad esempio, la lettura o la modifica del colore archiviato di un elemento dell'interfaccia utente non è un rischio significativo per la sicurezza dell'utente o dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="1d54e-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="1d54e-203">Evitare di consentire agli utenti di ispezionare o manomettere *i dati sensibili.*</span><span class="sxs-lookup"><span data-stu-id="1d54e-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="1d54e-204">Pacchetto sperimentale Protected Browser Storage</span><span class="sxs-lookup"><span data-stu-id="1d54e-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="1d54e-205">Un esempio di pacchetto NuGet che `localStorage` `sessionStorage` fornisce la protezione [dei dati](xref:security/data-protection/introduction) per ed è [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="1d54e-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="1d54e-206">`Microsoft.AspNetCore.ProtectedBrowserStorage`è un pacchetto sperimentale non supportato non adatto all'uso in produzione in questo momento.</span><span class="sxs-lookup"><span data-stu-id="1d54e-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="1d54e-207">Installazione</span><span class="sxs-lookup"><span data-stu-id="1d54e-207">Installation</span></span>

<span data-ttu-id="1d54e-208">Per installare `Microsoft.AspNetCore.ProtectedBrowserStorage` il pacchetto:</span><span class="sxs-lookup"><span data-stu-id="1d54e-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="1d54e-209">Nel Blazor progetto di app Server aggiungere un riferimento al pacchetto [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="1d54e-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="1d54e-210">Nel codice HTML di primo livello, ad esempio nel file *Pages/_Host.cshtml* `<script>` nel modello di progetto predefinito, aggiungere il tag seguente:</span><span class="sxs-lookup"><span data-stu-id="1d54e-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="1d54e-211">Nel `Startup.ConfigureServices` metodo, `AddProtectedBrowserStorage` chiamare `localStorage` per `sessionStorage` aggiungere e servizi alla raccolta di servizi:In the method, call to add and services to the service collection:</span><span class="sxs-lookup"><span data-stu-id="1d54e-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="1d54e-212">Salvare e caricare i dati all'interno di un componente</span><span class="sxs-lookup"><span data-stu-id="1d54e-212">Save and load data within a component</span></span>

<span data-ttu-id="1d54e-213">In qualsiasi componente che richiede il caricamento [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) o il salvataggio dei dati nell'archivio del browser, utilizzare per inserire un'istanza di uno degli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1d54e-213">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="1d54e-214">La scelta dipende da quale negozio di backup si desidera utilizzare.</span><span class="sxs-lookup"><span data-stu-id="1d54e-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="1d54e-215">Nell'esempio seguente `sessionStorage` viene utilizzato:In the following example, is used:</span><span class="sxs-lookup"><span data-stu-id="1d54e-215">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="1d54e-216">L'istruzione `@using` può essere inserita in un file *_Imports.razor* anziché nel componente.</span><span class="sxs-lookup"><span data-stu-id="1d54e-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="1d54e-217">L'uso del file *_Imports.razor* rende lo spazio dei nomi disponibile per segmenti più grandi dell'app o dell'intera app.</span><span class="sxs-lookup"><span data-stu-id="1d54e-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="1d54e-218">Per rendere `_currentCount` persistente `Counter` il valore nel componente `IncrementCount` del `ProtectedSessionStore.SetAsync`modello di progetto, modificare il metodo in modo che utilizzi:</span><span class="sxs-lookup"><span data-stu-id="1d54e-218">To persist the `_currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

<span data-ttu-id="1d54e-219">Nelle app più grandi e realistiche, l'archiviazione di singoli campi è uno scenario improbabile.</span><span class="sxs-lookup"><span data-stu-id="1d54e-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="1d54e-220">È più probabile che le app archivino interi oggetti modello che includono uno stato complesso.</span><span class="sxs-lookup"><span data-stu-id="1d54e-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="1d54e-221">`ProtectedSessionStore`serializza e deserializza automaticamente i dati JSON.</span><span class="sxs-lookup"><span data-stu-id="1d54e-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="1d54e-222">Nell'esempio di codice `_currentCount` precedente, i `sessionStorage['count']` dati vengono archiviati come nel browser dell'utente.</span><span class="sxs-lookup"><span data-stu-id="1d54e-222">In the preceding code example, the `_currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="1d54e-223">I dati non vengono archiviati in testo non crittografato, ma sono protetti utilizzando ASP.NET [Core's Data Protection](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="1d54e-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="1d54e-224">I dati crittografati possono `sessionStorage['count']` essere visualizzati se vengono valutati nella console per sviluppatori del browser.</span><span class="sxs-lookup"><span data-stu-id="1d54e-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="1d54e-225">Per recuperare `_currentCount` i dati se `Counter` l'utente ritorna al componente in un `ProtectedSessionStore.GetAsync`secondo momento (incluso se si trovano su un circuito completamente nuovo), utilizzare :</span><span class="sxs-lookup"><span data-stu-id="1d54e-225">To recover the `_currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="1d54e-226">Se i parametri del componente includono `ProtectedSessionStore.GetAsync` lo stato `OnParametersSetAsync`di `OnInitializedAsync`navigazione, chiamare e assegnare il risultato in , non .</span><span class="sxs-lookup"><span data-stu-id="1d54e-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="1d54e-227">`OnInitializedAsync`viene chiamato solo una volta quando viene creata un'istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="1d54e-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="1d54e-228">`OnInitializedAsync`non viene chiamato di nuovo in un secondo momento se l'utente passa a un URL diverso pur rimanendo nella stessa pagina.</span><span class="sxs-lookup"><span data-stu-id="1d54e-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="1d54e-229">Per altre informazioni, vedere <xref:blazor/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="1d54e-229">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="1d54e-230">Gli esempi in questa sezione funzionano solo se nel server non è abilitato il prerendering.</span><span class="sxs-lookup"><span data-stu-id="1d54e-230">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="1d54e-231">Con il prerendering abilitato, viene generato un errore simile al:</span><span class="sxs-lookup"><span data-stu-id="1d54e-231">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="1d54e-232">Le chiamate di interoperabilità JavaScript non possono essere emesse in questo momento.</span><span class="sxs-lookup"><span data-stu-id="1d54e-232">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="1d54e-233">Ciò è dovuto al fatto che è in corso il prerendering del componente.</span><span class="sxs-lookup"><span data-stu-id="1d54e-233">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="1d54e-234">Disabilitare il prerendering o aggiungere codice aggiuntivo per lavorare con il prerendering.</span><span class="sxs-lookup"><span data-stu-id="1d54e-234">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="1d54e-235">Per altre informazioni sulla scrittura di codice che funziona con il prerendering, vedere la sezione Gestire il [prerendering.](#handle-prerendering)</span><span class="sxs-lookup"><span data-stu-id="1d54e-235">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="1d54e-236">Gestire lo stato di caricamento</span><span class="sxs-lookup"><span data-stu-id="1d54e-236">Handle the loading state</span></span>

<span data-ttu-id="1d54e-237">Poiché l'archiviazione del browser è asincrona (accessibile tramite una connessione di rete), c'è sempre un periodo di tempo prima che i dati vengano caricati e disponibili per l'utilizzo da parte di un componente.</span><span class="sxs-lookup"><span data-stu-id="1d54e-237">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="1d54e-238">Per ottenere risultati ottimali, è in corso il rendering di un messaggio sullo stato del caricamento durante il caricamento anziché visualizzare dati vuoti o predefiniti.</span><span class="sxs-lookup"><span data-stu-id="1d54e-238">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="1d54e-239">Un approccio consiste nel `null` tenere traccia se i dati sono (ancora in fase di caricamento) o meno.</span><span class="sxs-lookup"><span data-stu-id="1d54e-239">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="1d54e-240">Nel componente `Counter` di default, il `int`conteggio viene mantenuto in un file .</span><span class="sxs-lookup"><span data-stu-id="1d54e-240">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="1d54e-241">Rendere `_currentCount` nullable aggiungendo un`?`punto interrogativo`int`( ) al tipo ( ):</span><span class="sxs-lookup"><span data-stu-id="1d54e-241">Make `_currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? _currentCount;
```

<span data-ttu-id="1d54e-242">Anziché visualizzare incondizionatamente il conteggio e il pulsante **Incremento,** scegliere di visualizzare questi elementi solo se i dati vengono caricati:</span><span class="sxs-lookup"><span data-stu-id="1d54e-242">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="1d54e-243">Gestire il prerendering</span><span class="sxs-lookup"><span data-stu-id="1d54e-243">Handle prerendering</span></span>

<span data-ttu-id="1d54e-244">Durante il prerendering:</span><span class="sxs-lookup"><span data-stu-id="1d54e-244">During prerendering:</span></span>

* <span data-ttu-id="1d54e-245">Non esiste una connessione interattiva al browser dell'utente.</span><span class="sxs-lookup"><span data-stu-id="1d54e-245">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="1d54e-246">Il browser non dispone ancora di una pagina in cui è possibile eseguire codice JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1d54e-246">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="1d54e-247">`localStorage`o `sessionStorage` non sono disponibili durante il prerendering.</span><span class="sxs-lookup"><span data-stu-id="1d54e-247">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="1d54e-248">Se il componente tenta di interagire con l'archiviazione, viene generato un errore simile al:</span><span class="sxs-lookup"><span data-stu-id="1d54e-248">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="1d54e-249">Le chiamate di interoperabilità JavaScript non possono essere emesse in questo momento.</span><span class="sxs-lookup"><span data-stu-id="1d54e-249">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="1d54e-250">Ciò è dovuto al fatto che è in corso il prerendering del componente.</span><span class="sxs-lookup"><span data-stu-id="1d54e-250">This is because the component is being prerendered.</span></span>

<span data-ttu-id="1d54e-251">Un modo per risolvere l'errore consiste nel disabilitare il prerendering.</span><span class="sxs-lookup"><span data-stu-id="1d54e-251">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="1d54e-252">Questa è di solito la scelta migliore se l'applicazione fa un uso pesante di archiviazione basata su browser.</span><span class="sxs-lookup"><span data-stu-id="1d54e-252">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="1d54e-253">Il prerendering aggiunge complessità e non ne trae vantaggio perché l'app non può eseguire il prerendering di qualsiasi contenuto utile fino a quando `localStorage` o `sessionStorage` non sono disponibili.</span><span class="sxs-lookup"><span data-stu-id="1d54e-253">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="1d54e-254">Per disabilitare il prerendering, aprire il file `render-mode` *Pages/_Host.cshtml* e impostare Component Tag [Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) su <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span><span class="sxs-lookup"><span data-stu-id="1d54e-254">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="1d54e-255">Il prerendering può essere utile per `localStorage` altre `sessionStorage`pagine che non utilizzano o .</span><span class="sxs-lookup"><span data-stu-id="1d54e-255">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="1d54e-256">Per mantenere abilitato il prerendering, rinviare l'operazione di caricamento fino a quando il browser non è connesso al circuito.</span><span class="sxs-lookup"><span data-stu-id="1d54e-256">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="1d54e-257">Di seguito è riportato un esempio per l'archiviazione di un valore di contatore:The following is an example for storing a counter value:</span><span class="sxs-lookup"><span data-stu-id="1d54e-257">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="1d54e-258">Eseguire il factoring della conservazione dello stato in un percorso comune</span><span class="sxs-lookup"><span data-stu-id="1d54e-258">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="1d54e-259">Se molti componenti si basano sull'archiviazione basata su browser, la riimplementazione del codice del provider di stato più volte crea la duplicazione del codice.</span><span class="sxs-lookup"><span data-stu-id="1d54e-259">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="1d54e-260">Un'opzione per evitare la duplicazione del codice consiste nel creare un *componente padre* del provider di stato che incapsula la logica del provider di stato.</span><span class="sxs-lookup"><span data-stu-id="1d54e-260">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="1d54e-261">I componenti figlio possono utilizzare dati persistenti indipendentemente dal meccanismo di persistenza dello stato.</span><span class="sxs-lookup"><span data-stu-id="1d54e-261">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="1d54e-262">Nell'esempio seguente `CounterStateProvider` di un componente, i dati del contatore vengono mantenuti:In the following example of a component, counter data is persisted:</span><span class="sxs-lookup"><span data-stu-id="1d54e-262">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="1d54e-263">Il `CounterStateProvider` componente gestisce la fase di caricamento non eseguendo il rendering del contenuto figlio fino al completamento del caricamento.</span><span class="sxs-lookup"><span data-stu-id="1d54e-263">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="1d54e-264">Per utilizzare `CounterStateProvider` il componente, eseguire il wrapping di un'istanza del componente intorno a qualsiasi altro componente che richiede l'accesso allo stato del contatore.</span><span class="sxs-lookup"><span data-stu-id="1d54e-264">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="1d54e-265">Per rendere lo stato accessibile a tutti `CounterStateProvider` i `Router` componenti `App` in un'app, eseguire il wrapping del componente intorno al componente (*App.razor*):</span><span class="sxs-lookup"><span data-stu-id="1d54e-265">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="1d54e-266">I componenti di cui è stato eseguito il wrapping ricevono e possono modificare lo stato del contatore persistente.</span><span class="sxs-lookup"><span data-stu-id="1d54e-266">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="1d54e-267">Il `Counter` componente seguente implementa il modello:The following component implements the pattern:</span><span class="sxs-lookup"><span data-stu-id="1d54e-267">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="1d54e-268">Il componente precedente non è necessario `ProtectedBrowserStorage`per interagire con , né si occupa di una fase di "caricamento".</span><span class="sxs-lookup"><span data-stu-id="1d54e-268">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="1d54e-269">Per gestire il prerendering `CounterStateProvider` come descritto in precedenza, può essere modificato in modo che tutti i componenti che utilizzano i dati del contatore funzionino automaticamente con il prerendering.</span><span class="sxs-lookup"><span data-stu-id="1d54e-269">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="1d54e-270">Vedere la sezione Gestire il [prerendering](#handle-prerendering) per i dettagli.</span><span class="sxs-lookup"><span data-stu-id="1d54e-270">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="1d54e-271">In generale, è consigliabile il modello di componente padre del provider di stato:In general, *state provider parent component* pattern is recommended:</span><span class="sxs-lookup"><span data-stu-id="1d54e-271">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="1d54e-272">Per consumare lo stato in molti altri componenti.</span><span class="sxs-lookup"><span data-stu-id="1d54e-272">To consume state in many other components.</span></span>
* <span data-ttu-id="1d54e-273">Se è presente un solo oggetto di stato di primo livello da mantenere.</span><span class="sxs-lookup"><span data-stu-id="1d54e-273">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="1d54e-274">Per rendere persistenti molti oggetti di stato diversi e utilizzare sottoinsiemi diversi di oggetti in posizioni diverse, è preferibile evitare di gestire il caricamento e il salvataggio dello stato a livello globale.</span><span class="sxs-lookup"><span data-stu-id="1d54e-274">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
