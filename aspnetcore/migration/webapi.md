---
title: Eseguire la migrazione da API Web ASP.NET a ASP.NET Core
author: ardalis
description: Informazioni su come eseguire la migrazione di un'implementazione di API Web dall'API Web ASP.NET 4. x a ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/webapi
ms.openlocfilehash: dda457daa0cb8a59ccd4c326a601e375fe4a81bb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766589"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Eseguire la migrazione da API Web ASP.NET a ASP.NET Core

Di [Scott Addie](https://twitter.com/scott_addie) e [Steve Smith](https://ardalis.com/)

Un'API Web ASP.NET 4. x è un servizio HTTP che raggiunge un'ampia gamma di client, inclusi browser e dispositivi mobili. ASP.NET Core unifica i modelli di app per le API Web e MVC di ASP.NET 4. x in un modello di programmazione più semplice noto come ASP.NET Core MVC. Questo articolo illustra i passaggi necessari per eseguire la migrazione dall'API Web ASP.NET 4. x a ASP.NET Core MVC.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Esaminare il progetto API Web ASP.NET 4. x

Come punto di partenza, in questo articolo viene usato il progetto *ProductsApp* creato in [Introduzione con API Web ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). In tale progetto, un semplice progetto API Web ASP.NET 4. x viene configurato come segue.

In *Global.asax.cs*viene effettuata una chiamata a `WebApiConfig.Register`:

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

La `WebApiConfig` classe si trova nella cartella *app_start* e dispone di un metodo `Register` statico:

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

Questa classe configura il [routing degli attributi](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), sebbene non sia effettivamente utilizzato nel progetto. Viene inoltre configurata la tabella di routing, che viene utilizzata da API Web ASP.NET. In questo caso, l'API Web ASP.NET 4. x prevede che gli URL corrispondano `/api/{controller}/{id}`al formato `{id}` , con l'opzione facoltativo.

Il progetto *ProductsApp* include un controller. Il controller eredita da `ApiController` e contiene due azioni:

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

Il `Product` modello utilizzato da `ProductsController` è una classe semplice:

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

Le sezioni seguenti illustrano la migrazione del progetto API Web a ASP.NET Core MVC.

## <a name="create-destination-project"></a>Crea progetto di destinazione

Completare i passaggi seguenti in Visual Studio:

* Passare a **file** > **nuovo** > **progetto** > **altri tipi** > di progetto**soluzioni di Visual Studio**. Selezionare **soluzione vuota**e denominare la soluzione *WebAPIMigration*. Fare clic sul pulsante **OK**.
* Aggiungere il progetto *ProductsApp* esistente alla soluzione.
* Aggiungere un nuovo progetto di **applicazione Web ASP.NET Core** alla soluzione. Selezionare **.NET Core** Target Framework dall'elenco a discesa e selezionare il modello di progetto **API** . Denominare il progetto *ProductsCore*e fare clic sul pulsante **OK** .

La soluzione ora contiene due progetti. Le sezioni seguenti illustrano come eseguire la migrazione del contenuto del progetto *ProductsApp* al progetto *ProductsCore* .

## <a name="migrate-configuration"></a>Eseguire la migrazione della configurazione

ASP.NET Core non utilizza la cartella *app_start* o il file *Global. asax* e il file *Web. config* viene aggiunto in fase di pubblicazione. *Startup.cs* è la sostituzione di *Global. asax* e si trova nella radice del progetto. La `Startup` classe gestisce tutte le attività di avvio dell'app. Per altre informazioni, vedere <xref:fundamentals/startup>.

In ASP.NET Core MVC, il routing degli attributi è incluso per <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> impostazione predefinita quando `Startup.Configure`viene chiamato in. La chiamata `UseMvc` seguente sostituisce il file di *app_start/Webapiconfig.cs* del progetto *ProductsApp* :

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>Eseguire la migrazione di modelli e controller

Copiare il controller del progetto *ProductApp* e il modello usato. A tale scopo, seguire questa procedura:

1. Copiare *Controllers/ProductsController. cs* dal progetto originale a quello nuovo.
1. Copiare l'intera cartella *Models* dal progetto originale a quella nuova.
1. Modificare gli spazi dei nomi dei file copiati in modo che corrispondano al nuovo nome del progetto (*ProductsCore*). Modificare anche `using ProductsApp.Models;` l'istruzione in *ProductsController.cs* .

A questo punto, la compilazione dell'app comporta una serie di errori di compilazione. Gli errori si verificano perché i componenti seguenti non esistono in ASP.NET Core:

* Classe `ApiController`
* Spazio dei nomi `System.Web.Http`
* Interfaccia `IHttpActionResult`

Correggere gli errori nel modo seguente:

1. Cambiare `ApiController` in <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Aggiungere `using Microsoft.AspNetCore.Mvc;` per risolvere il `ControllerBase` riferimento.
1. Eliminare `using System.Web.Http;`.
1. Modificare il `GetProduct` tipo restituito dell'azione da `IHttpActionResult` a `ActionResult<Product>`.

Semplificare l' `GetProduct` `return` istruzione dell'azione a quanto segue:

```csharp
return product;
```

## <a name="configure-routing"></a>Configurare il routing

Configurare il routing come segue:

1. Contrassegnare `ProductsController` la classe con gli attributi seguenti:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    L'attributo [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) precedente configura il modello di routing degli attributi del controller. L' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attributo rende il routing degli attributi un requisito per tutte le azioni nel controller.

    Il `[controller]` routing degli attributi supporta i token, ad `[action]`esempio e. In fase di esecuzione, ogni token viene sostituito rispettivamente dal nome del controller o dell'azione a cui è stato applicato l'attributo. I token riducono il numero di stringhe magiche nel progetto. I token assicurano inoltre che le route rimangano sincronizzate con i controller e le azioni corrispondenti quando vengono applicati i refactoring di ridenominazione automatica.
1. Impostare la modalità di compatibilità del progetto su ASP.NET Core 2,2:

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    La modifica precedente:

    * È necessario per usare l' `[ApiController]` attributo a livello di controller.
    * Consente di optare per i comportamenti potenzialmente innovatori introdotti in ASP.NET Core 2,2.
1. Abilitare le richieste HTTP Get alle `ProductController` azioni:
    * Applicare l' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attributo all' `GetAllProducts` azione.
    * Applicare l' `[HttpGet("{id}")]` attributo all' `GetProduct` azione.

Dopo le modifiche precedenti e la rimozione delle istruzioni inutilizzate `using` , il file *ProductsController.cs* ha un aspetto simile al seguente:

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

Eseguire il progetto migrato e passare a `/api/products`. Viene visualizzato un elenco completo di tre prodotti. Passare a `/api/products/1`. Il primo prodotto verrà visualizzato.

## <a name="compatibility-shim"></a>Shim di compatibilità

La libreria [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) fornisce uno shim di compatibilità per spostare i progetti API Web ASP.NET 4. x in ASP.NET Core. Lo shim di compatibilità estende ASP.NET Core per supportare una serie di convenzioni di ASP.NET 4. x Web API 2. L'esempio portato in precedenza in questo documento è abbastanza elementare che lo shim di compatibilità non fosse necessario. Per i progetti di grandi dimensioni, l'uso dello shim di compatibilità può essere utile per colmare temporaneamente il gap dell'API tra ASP.NET Core e ASP.NET 4. x Web API 2.

Lo shim di compatibilità dell'API Web è pensato per essere usato come misura temporanea per supportare la migrazione di progetti API Web ASP.NET 4. x di grandi dimensioni a ASP.NET Core. Nel corso del tempo, è necessario aggiornare i progetti in modo da usare ASP.NET Core modelli anziché basarsi sullo shim di compatibilità.

Le funzionalità di compatibilità `Microsoft.AspNetCore.Mvc.WebApiCompatShim` incluse in includono:

* Aggiunge un `ApiController` tipo in modo che i tipi di base dei controller non debbano essere aggiornati.
* Abilita l'associazione di modelli di tipo API Web. ASP.NET Core funzioni di associazione di modelli MVC in modo analogo a quello di ASP.NET 4. x MVC 5, per impostazione predefinita. Lo shim di compatibilità modifica l'associazione di modelli in modo da essere più simile alle convenzioni di associazione del modello ASP.NET 4. x Web API 2. Ad esempio, i tipi complessi vengono automaticamente associati dal corpo della richiesta.
* Estende l'associazione di modelli in modo che le azioni del controller `HttpRequestMessage`possano prendere parametri di tipo.
* Aggiunge formattatori di messaggi che consentono alle azioni di restituire `HttpResponseMessage`risultati di tipo.
* Aggiunge metodi di risposta aggiuntivi che possono essere usati dalle azioni API Web 2 per rispondere alle risposte:
  * `HttpResponseMessage`generatori
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Metodi di risultato dell'azione:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Aggiunge un'istanza di `IContentNegotiator` al contenitore di inserimento delle dipendenze dell'app e rende disponibili i tipi correlati alla negoziazione del contenuto da [Microsoft. AspNet. WebAPI. client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/). Esempi di tali tipi includono `DefaultContentNegotiator` e `MediaTypeFormatter`.

Per usare lo shim di compatibilità:

1. Installare il pacchetto NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .
1. Registrare i servizi dello shim di compatibilità con il contenitore DI dell'app chiamando `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.
1. Definire route specifiche dell'API Web usando `MapWebApiRoute` `IRouteBuilder` nella `IApplicationBuilder.UseMvc` chiamata dell'app.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
