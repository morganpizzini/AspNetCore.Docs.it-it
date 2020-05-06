---
title: Tipi restituiti dall'azione del controller nell'API Web ASP.NET Core
author: scottaddie
description: Informazioni sull'uso dei diversi tipi restituiti del metodo di azione del controller in un'API Web ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/action-return-types
ms.openlocfilehash: 4db553a61ca0eeabe35a08731295333f588ee0fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774942"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Tipi restituiti dall'azione del controller nell'API Web ASP.NET Core

Di [Scott Addie](https://github.com/scottaddie)

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

ASP.NET Core offre le opzioni seguenti per i tipi restituiti dell'azione del controller API Web:

::: moniker range=">= aspnetcore-2.1"

* [Tipo specifico](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [Tipo specifico](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

Questo documento spiega quando è più appropriato utilizzare ogni tipo restituito.

## <a name="specific-type"></a>Tipo specifico

L'azione più semplice restituisce un tipo di dati primitivo o complesso, ad esempio, `string` o un tipo di oggetto personalizzato. Si consideri l'azione seguente, che restituisce una raccolta di oggetti `Product` personalizzati:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

In assenza di condizioni note da cui proteggersi durante l'esecuzione dell'azione, la restituzione di un tipo specifico potrebbe essere sufficiente. L'azione precedente non accetta parametri, quindi non è necessario eseguire la convalida dei vincoli dei parametri.

Quando per un'azione devono essere prese in considerazione condizioni note, vengono introdotti più percorsi di ritorno. In tal caso, è comune combinare un <xref:Microsoft.AspNetCore.Mvc.ActionResult> tipo restituito con il tipo restituito primitivo o complesso. Ai fini di questo tipo di azione, è necessario usare [IActionResult](#iactionresult-type) oppure [ActionResult\<T >](#actionresultt-type).

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>Return IEnumerable\<t> o IAsyncEnumerable\<t>

In ASP.NET Core 2,2 e versioni precedenti, <xref:System.Collections.Generic.IEnumerable%601> la restituzione da un'azione comporta l'iterazione di una raccolta sincrona da parte del serializzatore. Il risultato è il blocco delle chiamate e un potenziale per l'inedia del pool di thread. Per illustrare, si supponga che Entity Framework (EF) Core venga usato per le esigenze di accesso ai dati dell'API Web. Il tipo restituito dell'azione seguente viene enumerato in modo sincrono durante la serializzazione:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Per evitare l'enumerazione sincrona e le attese di blocco sul database in ASP.NET Core 2,2 e versioni `ToListAsync`precedenti, richiamare:

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

In ASP.NET Core 3,0 e versioni successive, `IAsyncEnumerable<T>` tornando da un'azione:

* Non produce più un'iterazione sincrona.
* Diventa efficiente come la restituzione <xref:System.Collections.Generic.IEnumerable%601>.

ASP.NET Core 3,0 e versioni successive memorizza nel buffer il risultato dell'azione seguente prima di fornirlo al serializzatore:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Provare a dichiarare il tipo restituito della firma dell'azione `IAsyncEnumerable<T>` come per garantire l'iterazione asincrona. In definitiva, la modalità di iterazione è basata sul tipo concreto sottostante restituito. MVC esegue automaticamente il buffering di qualsiasi tipo `IAsyncEnumerable<T>`concreto che implementa.

Si consideri l'azione seguente, che restituisce i record di prodotto `IEnumerable<Product>`con prezzo di vendita come:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

L' `IAsyncEnumerable<Product>` equivalente dell'azione precedente è:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

Entrambe le azioni precedenti sono non bloccanti a partire da ASP.NET Core 3,0.

## <a name="iactionresult-type"></a>Tipo IActionResult

Il <xref:Microsoft.AspNetCore.Mvc.IActionResult> tipo restituito è appropriato quando più `ActionResult` tipi restituiti sono possibili in un'azione. I tipi `ActionResult` rappresentano vari codici di stato HTTP. Qualsiasi classe non astratta che deriva da `ActionResult` viene qualificata come tipo restituito valido. Alcuni tipi restituiti comuni in questa categoria sono <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) e <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200). In alternativa, è possibile utilizzare i <xref:Microsoft.AspNetCore.Mvc.ControllerBase> metodi pratici della classe per restituire `ActionResult` i tipi da un'azione. Ad esempio, `return BadRequest();` è una forma abbreviata di `return new BadRequestResult();`.

Poiché in questo tipo di azione sono presenti più tipi e percorsi restituiti, è necessario utilizzare l' [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attributo in modo liberato. Questo attributo produce dettagli di risposta più descrittivi per le pagine della Guida dell'API Web generate da strumenti come [spavalderia](xref:tutorials/web-api-help-pages-using-swagger). `[ProducesResponseType]` indica i tipi noti e i codici di stato HTTP che l'azione deve restituire.

### <a name="synchronous-action"></a>Azione sincrona

Si consideri la seguente azione sincrona che prevede due possibili tipi restituiti:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

Nell'azione precedente:

* Viene restituito un codice di stato 404 quando il prodotto rappresentato `id` da non esiste nell'archivio dati sottostante. Il <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> metodo convenience viene richiamato come sintassi `return new NotFoundResult();`abbreviata per.
* Quando il prodotto esiste, viene restituito un `Product` codice di stato 200 con l'oggetto. Il <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> metodo convenience viene richiamato come sintassi `return new OkObjectResult(product);`abbreviata per.

### <a name="asynchronous-action"></a>Azione asincrona

Si consideri la seguente azione asincrona che prevede due possibili tipi restituiti:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

Nell'azione precedente:

* Quando la descrizione del prodotto contiene "XYZ widget", viene restituito un codice di stato 400. Il <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> metodo convenience viene richiamato come sintassi `return new BadRequestResult();`abbreviata per.
* Un codice di stato 201 viene generato dal <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> metodo di praticità quando viene creato un prodotto. Un'alternativa alla chiamata `CreatedAtAction` di `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`è. In questo percorso di codice, `Product` l'oggetto viene fornito nel corpo della risposta. Viene `Location` fornita un'intestazione di risposta contenente l'URL del prodotto appena creato.

Ad esempio, il modello seguente indica che le richieste devono includere le proprietà `Name` e `Description`. La mancata `Name` fornitura `Description` e nella richiesta causa la mancata convalida del modello.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Se viene [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) applicato l'attributo in ASP.NET Core 2,1 o versioni successive, gli errori di convalida del modello generano un codice di stato 400. Per altre informazioni, vedere [Risposte HTTP 400 automatiche](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>Tipo ActionResult\<T>

In ASP.NET Core 2,1 è stato introdotto il tipo restituito [\<ActionResult T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) per le azioni del controller API Web. Consente di restituire un tipo che deriva da <xref:Microsoft.AspNetCore.Mvc.ActionResult> o restituisce un [tipo specifico](#specific-type). `ActionResult<T>` offre i vantaggi seguenti rispetto al [tipo IActionResult](#iactionresult-type):

* La [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) `Type` proprietà dell'attributo può essere esclusa. Ad esempio, `[ProducesResponseType(200, Type = typeof(Product))]` viene semplificato in `[ProducesResponseType(200)]`. Il tipo restituito previsto dell'azione viene invece dedotto da `T` in `ActionResult<T>`.
* [Operatori di cast impliciti](/dotnet/csharp/language-reference/keywords/implicit) supportano la conversione di `T` e `ActionResult` in `ActionResult<T>`. `T`converte in <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, il che `return new ObjectResult(T);` significa che è `return T;`semplificato in.

C# non supporta operatori di cast impliciti sulle interfacce. Di conseguenza, per la conversione dell'interfaccia in un tipo concreto è necessario usare `ActionResult<T>`. Ad esempio, usare `IEnumerable` nell'esempio seguente non funziona:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

Una delle opzioni disponibili per correggere il codice precedente consiste nel restituire `_repository.GetProducts().ToList();`.

La maggior parte delle azioni presenta un tipo restituito specifico. Durante l'esecuzione di un'azione possono verificarsi condizioni impreviste, nel qual caso il tipo specifico non viene restituito. Ad esempio, il parametro di input di un'azione potrebbe non superare la convalida del modello. In tal caso, è consueto che venga restituito il tipo `ActionResult` appropriato anziché il tipo specifico.

### <a name="synchronous-action"></a>Azione sincrona

Si consideri un'azione sincrona che prevede due possibili tipi restituiti:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

Nell'azione precedente:

* Quando il prodotto non esiste nel database, viene restituito un codice di stato 404.
* Quando il prodotto esiste, viene restituito un codice `Product` di stato 200 con l'oggetto corrispondente. Prima di ASP.NET Core 2,1, `return product;` la riga doveva essere `return Ok(product);`.

### <a name="asynchronous-action"></a>Azione asincrona

Si consideri un'azione asincrona che prevede due possibili tipi restituiti:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

Nell'azione precedente:

* Il runtime di ASP.NET Core restituisce<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>un codice di stato 400 () quando:
  * L' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attributo è stato applicato e la convalida del modello ha esito negativo.
  * La descrizione del prodotto contiene "XYZ Widget".
* Un codice di stato 201 viene generato dal <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> metodo durante la creazione di un prodotto. In questo percorso di codice, `Product` l'oggetto viene fornito nel corpo della risposta. Viene `Location` fornita un'intestazione di risposta contenente l'URL del prodotto appena creato.

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
