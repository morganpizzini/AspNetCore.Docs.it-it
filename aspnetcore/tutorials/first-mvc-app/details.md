---
title: Parte 10, esaminare i dettagli ed eliminare i metodi di un'app ASP.NET Core
author: rick-anderson
description: Parte 10 della serie di esercitazioni su ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 2bde0e649af4928a03027b646436cc7b215b707c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021653"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="6f83e-103">Parte 10, esaminare i dettagli ed eliminare i metodi di un'app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f83e-103">Part 10, examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="6f83e-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6f83e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6f83e-105">Aprire il controller di film ed esaminare il metodo `Details`:</span><span class="sxs-lookup"><span data-stu-id="6f83e-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="6f83e-106">Il motore di scaffolding MVC che ha creato questo metodo di azione aggiunge un commento che mostra una richiesta HTTP che richiama il metodo.</span><span class="sxs-lookup"><span data-stu-id="6f83e-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="6f83e-107">In questo caso si tratta di una richiesta GET con tre segmenti di URL, il controller `Movies`, il metodo `Details` e un valore `id`.</span><span class="sxs-lookup"><span data-stu-id="6f83e-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="6f83e-108">Tenere presente che questi segmenti vengono definiti nel file *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="6f83e-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="6f83e-109">Entity Framework semplifica la ricerca di dati usando il metodo `FirstOrDefaultAsync`.</span><span class="sxs-lookup"><span data-stu-id="6f83e-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="6f83e-110">Un'importante funzionalità di sicurezza integrata nel metodo è che il codice verifica che il metodo di ricerca abbia trovato un film prima di tentare di eseguire una qualsiasi operazione su di esso.</span><span class="sxs-lookup"><span data-stu-id="6f83e-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="6f83e-111">Ad esempio, un hacker potrebbe introdurre errori nel sito modificando l'URL creato dai collegamenti di `http://localhost:{PORT}/Movies/Details/1` in qualcosa di simile a `http://localhost:{PORT}/Movies/Details/12345` o usando altri valori che non rappresentano un film effettivo.</span><span class="sxs-lookup"><span data-stu-id="6f83e-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:{PORT}/Movies/Details/1` to something like  `http://localhost:{PORT}/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="6f83e-112">Se non è stato cercato un film con valore Null, l'app genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="6f83e-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="6f83e-113">Esaminare i metodi `Delete` e `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="6f83e-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="6f83e-114">Si noti che il metodo `HTTP GET Delete` non elimina il film specificato, ma restituisce una vista del film in cui è possibile inviare (HttpPost) la richiesta di eliminazione.</span><span class="sxs-lookup"><span data-stu-id="6f83e-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="6f83e-115">L'esecuzione di un'operazione di eliminazione in risposta a una richiesta GET (o l'esecuzione di un'operazione di modifica, di creazione o di qualsiasi altra azione che modifica i dati) introduce un problema di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="6f83e-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="6f83e-116">Il metodo `[HttpPost]` che elimina i dati è denominato `DeleteConfirmed` per fornire al metodo HTTP POST un nome o una firma univoca.</span><span class="sxs-lookup"><span data-stu-id="6f83e-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="6f83e-117">Le firme dei due metodi sono illustrate di seguito:</span><span class="sxs-lookup"><span data-stu-id="6f83e-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="6f83e-118">Il Common Language Runtime (CLR) richiede metodi in rapporto di overload per disporre di una firma di parametro univoca, ovvero lo stesso nome di metodo ma un elenco diverso di parametri.</span><span class="sxs-lookup"><span data-stu-id="6f83e-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="6f83e-119">Tuttavia, qui è necessario usare due `Delete` metodi: uno per GET e uno per POST, entrambi con la stessa firma di parametro.</span><span class="sxs-lookup"><span data-stu-id="6f83e-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="6f83e-120">Entrambi devono accettare un singolo intero come parametro.</span><span class="sxs-lookup"><span data-stu-id="6f83e-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="6f83e-121">Per risolvere questo problema esistono due approcci. Una possibilità è assegnare nomi diversi ai metodi.</span><span class="sxs-lookup"><span data-stu-id="6f83e-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="6f83e-122">Questa operazione è stata eseguita dal meccanismo di scaffolding nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="6f83e-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="6f83e-123">Tuttavia in questo modo si introduce un piccolo problema: ASP.NET esegue il mapping dei segmenti di un URL ai metodi di azione in base al nome e, se si rinomina un metodo, generalmente il routing non è in grado di trovare questo metodo.</span><span class="sxs-lookup"><span data-stu-id="6f83e-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="6f83e-124">La soluzione è mostrata nell'esempio e consiste nell'aggiungere l'attributo `ActionName("Delete")` al metodo `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="6f83e-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="6f83e-125">Questo attributo esegue il mapping per il sistema di routing in modo che un URL che includa /Delete/ per una richiesta POST troverà il metodo `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="6f83e-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="6f83e-126">Un'altra alternativa comune per i metodi con nomi e firme identici consiste nel modificare artificialmente la firma del metodo POST in modo da includere un parametro aggiuntivo (non usato).</span><span class="sxs-lookup"><span data-stu-id="6f83e-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="6f83e-127">Questa azione è stata eseguita in un post precedente quando è stato aggiunto il parametro `notUsed`.</span><span class="sxs-lookup"><span data-stu-id="6f83e-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="6f83e-128">È possibile eseguire la stessa operazione qui per il metodo `[HttpPost] Delete`:</span><span class="sxs-lookup"><span data-stu-id="6f83e-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="6f83e-129">Eseguire la pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="6f83e-129">Publish to Azure</span></span>

<span data-ttu-id="6f83e-130">Per informazioni sulla distribuzione in Azure, vedere [Tutorial: Build a .NET Core and SQL Database web app in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb) (Esercitazione: Creare un'app Web .NET Core e database SQL nel Servizio app di Azure).</span><span class="sxs-lookup"><span data-stu-id="6f83e-130">For information on deploying to Azure, see [Tutorial: Build a .NET Core and SQL Database web app in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="6f83e-131">Indietro</span><span class="sxs-lookup"><span data-stu-id="6f83e-131">Previous</span></span>](validation.md)
