---
title: Routing ad azioni del controller in ASP.NET Core
author: rick-anderson
description: Informazioni su come ASP.NET Core MVC usa middleware di routing per verificare la corrispondenza degli URL delle richieste in ingresso ed eseguirne il mapping alle azioni.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 9f7a26a482cb115697a0a3d7439c14a062677c92
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277132"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Routing ad azioni del controller in ASP.NET Core

Di [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), e [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET controller Core utilizzano il [middleware](xref:fundamentals/middleware/index) Routing per associare gli URL delle richieste in ingresso e mapparli alle [azioni](#action).  Modelli di percorso:

* Sono definiti nel codice o negli attributi di avvio.
* Descrivere il modo in cui i percorsi URL vengono associati alle [azioni](#action).
* Vengono utilizzati per generare URL per i collegamenti. I collegamenti generati vengono in genere restituiti nelle risposte.

Le azioni vengono [instradate convenzionalmente](#cr) o [con attributi](#ar). L'inserimento di una route nel controller o [nell'azione](#action) lo rende indirizzato con attributo. Per altre informazioni, vedere [Routing misto](#routing-mixed-ref-label).

Questo documento:

* Vengono illustrate le interazioni tra MVC e routing:
  * Modalità di utilizzo delle funzionalità di routing da parte delle app MVC tipiche.
  * Copre entrambi:
    * [Convenzionalmente il routing](#cr) viene in genere utilizzato con i controller e le viste.
    * *Routing degli attributi* utilizzato con le API REST. Se si è interessati principalmente al routing per le API REST, passare alla sezione Routing degli attributi [per le API REST.](#ar)
  * Per informazioni dettagliate, vedere [Routing](xref:fundamentals/routing) per i dettagli di routing avanzato.
* Fa riferimento al sistema di routing predefinito aggiunto in ASP.NET Core 3.0, denominato routing degli endpoint. È possibile utilizzare i controller con la versione precedente del routing per motivi di compatibilità. Per istruzioni, vedere la guida alla [migrazione 2.2-3.0.See the 2.2-3.0 migration guide](xref:migration/22-to-30) for instructions. Fare riferimento alla [versione 2.2 di questo documento](xref:mvc/controllers/routing?view=aspnetcore-2.2) per il materiale di riferimento sul sistema di routing legacy.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Impostare il percorso convenzionale

`Startup.Configure`in genere ha codice simile al seguente quando si utilizza [il routing convenzionale:](#crd)

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

All'interno <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>della <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> chiamata a , viene utilizzato per creare una singola route. Il percorso singolo `default` è denominato route. La maggior parte delle app con controller `default` e visualizzazioni usa un modello di percorso simile alla route. Le API REST devono usare il [routing degli attributi.](#ar)

Il modello `"{controller=Home}/{action=Index}/{id?}"`di percorso :

* Corrisponde a un percorso URL come`/Products/Details/5`
* Estrae `{ controller = Products, action = Details, id = 5 }` i valori della route suddelizzando in token il percorso. L'estrazione dei valori di route genera una `ProductsController` corrispondenza `Details` se l'app ha un controller denominato e un'azione:The extraction of route values results in a match if the app has a controller named and a action:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`model associa il `id = 5` valore di `id` per `5`impostare il parametro su . Per ulteriori dettagli, vedere [Associazione di](xref:mvc/models/model-binding) modelli.
* `{controller=Home}`definisce `Home` come `controller`predefinito .
* `{action=Index}`definisce `Index` come `action`predefinito .
*  Il `?` carattere `{id?}` `id` in definisce come facoltativo.
  * I parametri di route predefiniti e facoltativi non devono necessariamente essere presenti nel percorso URL per trovare una corrispondenza. Vedere [Riferimento per i modelli di route](xref:fundamentals/routing#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.
* Corrisponde al `/`percorso URL .
* Produce i `{ controller = Home, action = Index }`valori della route.

I valori `controller` `action` per e utilizzare i valori predefiniti. `id`non produce un valore poiché non esiste un segmento corrispondente nel percorso URL. `/`corrisponde solo se `HomeController` esiste `Index` un'azione:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Utilizzando la definizione del controller `HomeController.Index` precedente e il modello di route, l'azione viene eseguita per i percorsi URL seguenti:Using the preceding controller definition and route template, the action is run for the following URL paths:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

Il percorso `/` URL utilizza `Home` i `Index` controller e l'azione predefiniti del modello di route. Il percorso `/Home` URL utilizza `Index` l'azione predefinita del modello di route.

Il metodo pratico <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:

```csharp
endpoints.MapDefaultControllerRoute();
```

Sostituisce:

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> Il routing viene <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> configurato utilizzando il middleware e . Per utilizzare i controller:
>
> * Chiamare <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> `UseEndpoints` all'interno per mappare i controller [instradati dell'attributo.](#ar)
> * Chiamare <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>o , per mappare i controller [instradati convenzionalmente.](#cr)

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Routing convenzionale

Il routing convenzionale viene utilizzato con i controller e le viste. La route predefinita (`default`):

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

è un esempio di *routing convenzionale*. Si chiama *routing convenzionale* perché stabilisce una *convenzione* per i percorsi URL:It's called conventional routing because it establishes a convention for URL paths:

* Il primo segmento di percorso, `{controller=Home}`, esegue il mapping al nome del controller.
* Il secondo `{action=Index}`segmento, , esegue il mapping al nome [dell'azione.](#action)
* Il terzo `{id?}` segmento viene `id`utilizzato per un oggetto . L'in `?` `{id?}` lo rende facoltativo. `id`viene utilizzato per eseguire il mapping a un'entità modello.

Utilizzando `default` questa route, il percorso URL:

* `/Products/List`mappa all'azione. `ProductsController.List`
* `/Blog/Article/17`esegue `BlogController.Article` il mapping e `id` in genere il modello associa il parametro a 17.

Questo mapping:

* Si basa **solo**sui nomi del controller e [dell'azione](#action) .
* Non è basato su spazi dei nomi, percorsi di file di origine o parametri del metodo.

L'uso del routing convenzionale con la route predefinita consente di creare l'app senza dover creare un nuovo modello di URL per ogni azione. Per un'app con azioni di stile [CRUD,](https://wikipedia.org/wiki/Create,_read,_update_and_delete) la coerenza per gli URL tra i controller:

* Consente di semplificare il codice.
* Rende l'interfaccia utente più prevedibile.

> [!WARNING]
> Il `id` nel codice precedente è definito come facoltativo dal modello di ciclo di lavorazione. Le azioni possono essere eseguite senza l'ID facoltativo fornito come parte dell'URL. In genere, quando`id` viene omesso dall'URL:
>
> * `id`è impostato `0` su dall'associazione del modello.
> * Nel database corrispondente a `id == 0`.
>
> [Il routing degli attributi](#ar) fornisce un controllo granulare per rendere l'ID necessario per alcune azioni e non per altre. Per convenzione, la documentazione include parametri facoltativi, ad esempio `id` quando è probabile che vengano visualizzati nell'utilizzo corretto.

La maggior parte delle app dovrebbe scegliere uno schema di routing semplice e descrittivo in modo che gli URL siano leggibili e significativi. La route convenzionale predefinita `{controller=Home}/{action=Index}/{id?}`:

* Supporta uno schema di routing semplice e descrittivo.
* È un punto iniziale utile per le app basate su interfaccia utente.
* È l'unico modello di route necessario per molte app dell'interfaccia utente Web. Per le app con interfaccia utente Web più grandi, un'altra route che usa [Aree](#areas) se spesso tutto ciò che è necessario.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>e <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :

* Assegna automaticamente un valore **dell'ordine** ai relativi endpoint in base all'ordine in cui vengono richiamati.

Routing degli endpoint in ASP.NET Core 3.0 e versioni successive:

* Non ha un concetto di percorsi.
* Non fornisce garanzie di ordinazione per l'esecuzione dell'estendibilità, tutti gli endpoint vengono elaborati contemporaneamente.

Abilitare la [registrazione](xref:fundamentals/logging/index) per verificare in che modo le implementazioni del routing predefinite, ad esempio <xref:Microsoft.AspNetCore.Routing.Route>, corrispondono alle richieste.

[Il routing degli attributi](#ar) viene illustrato più avanti in questo documento.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Percorsi convenzionali multipli

È possibile aggiungere più `UseEndpoints` [percorsi convenzionali](#cr) all'interno aggiungendo più chiamate a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> e <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>. In questo modo si consente di definire più convenzioni o di aggiungere percorsi convenzionali dedicati a [un'azione](#action)specifica, ad esempio:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

Il `blog` percorso nel codice precedente è un **percorso convenzionale dedicato.** Si chiama un percorso convenzionale dedicato perché:

* Utilizza [il routing convenzionale](#cr).
* È dedicato a [un'azione](#action)specifica.

Perché `controller` `action` e non vengono visualizzati `"blog/{*article}"` nel modello di percorso come parametri:

* Possono avere solo i `{ controller = "Blog", action = "Article" }`valori predefiniti.
* Questo percorso viene sempre `BlogController.Article`mappato all'azione .

`/Blog`, `/Blog/Article`, `/Blog/{any-string}` e sono gli unici percorsi URL che corrispondono alla route del blog.

L'esempio precedente:

* `blog`percorso ha una priorità più `default` alta per le corrispondenze rispetto al percorso perché viene aggiunto per primo.
* È ed esempio di routing in stile [Slug](https://developer.mozilla.org/docs/Glossary/Slug) in cui è tipico avere un nome di articolo come parte dell'URL.

> [!WARNING]
> In ASP.NET Core 3.0 e versioni successive, il routing non:In ASP.NET Core 3.0 and later, routing doesn't:
> * Definire un concetto denominato *route*. `UseRouting`aggiunge la corrispondenza delle route alla pipeline middleware. Il `UseRouting` middleware esamina il set di endpoint definito nell'app e seleziona la corrispondenza migliore dell'endpoint in base alla richiesta.
> * Fornire garanzie sull'ordine di esecuzione <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> dell'estensibilità come o <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.
>
>Vedere [Routing](xref:fundamentals/routing) per il materiale di riferimento sul routing.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Ordine di instradamento convenzionale

Il routing convenzionale corrisponde solo a una combinazione di azione e controller definiti dall'app. Ciò ha lo scopo di semplificare i casi in cui i percorsi convenzionali si sovrappongono.
Aggiunta di <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>route <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> utilizzando , , e assegnare automaticamente un valore di ordine ai relativi endpoint in base all'ordine in cui vengono richiamati. Le corrispondenze di un percorso che appare in precedenza hanno una priorità più alta. Il routing convenzionale dipende dall'ordinamento. In generale, i percorsi con aree devono essere posizionati prima in quanto sono più specifici dei percorsi senza area. [Percorsi convenzionali dedicati](#dcr) con `{*article}` catch tutti i parametri di route come possono rendere un percorso troppo [avido](xref:fundamentals/routing#greedy), il che significa che corrisponde agli URL che si intendeva abbinare da altre route. Inserire i percorsi greedy in un secondo momento nella tabella dei percorsi per evitare corrispondenze avide.

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Risoluzione di azioni ambigue

Quando due endpoint corrispondono tramite routing, il routing deve eseguire una delle operazioni seguenti:

* Scegli il candidato migliore.
* Generazione di un'eccezione.

Ad esempio:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Il controller precedente definisce due azioni che corrispondono:The preceding controller defines two actions that match:

* Il percorso URL`/Products33/Edit/17`
* Dati `{ controller = Products33, action = Edit, id = 17 }`del percorso .

Questo è un modello tipico per i controller MVC:This is a typical pattern for MVC controllers:

* `Edit(int)`visualizza un modulo per modificare un prodotto.
* `Edit(int, Product)`elabora il modulo registrato.

Per risolvere il percorso corretto:

* `Edit(int, Product)`viene selezionato quando la `POST`richiesta è http .
* `Edit(int)`è selezionato quando il [verbo HTTP](#verb) è qualsiasi altra cosa. `Edit(int)`è generalmente `GET`chiamato via .

Il <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`percorso , , viene fornito al routing in modo che possa scegliere in base al metodo HTTP della richiesta. Il `HttpPostAttribute` `Edit(int, Product)` fa una `Edit(int)`corrispondenza migliore di .

È importante comprendere il ruolo degli `HttpPostAttribute`attributi come . Attributi simili sono definiti per altri [verbi HTTP.](#verb) Nel [routing convenzionale,](#cr)è comune che le azioni utilizzino lo stesso nome di azione quando fanno parte di un modulo di presentazione, inviano il flusso di lavoro del modulo. Ad esempio, vedere [Esaminare i due metodi di azione Modifica](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Se il routing non è in <xref:System.Reflection.AmbiguousMatchException> grado di scegliere un candidato migliore, viene generata un'eccezione, che elenca più endpoint corrispondenti.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Nomi di route convenzionali

Le `"blog"` stringhe `"default"` e negli esempi seguenti sono nomi di route convenzionali:The strings and in the following examples are conventional route names:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

I nomi delle route assegnano alla route un nome logico. La route denominata può essere utilizzata per la generazione di URL. L'utilizzo di una route denominata semplifica la creazione di URL quando l'ordine delle route potrebbe complicare la generazione di URL. I nomi delle route devono essere univoci a livello di applicazione.

Nomi dei percorsi:

* Non hanno alcun impatto sulla corrispondenza o sulla gestione delle richieste degli URL.
* Vengono utilizzati solo per la generazione di URL.

Il concetto di nome della route è rappresentato nel routing come [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata). I termini **nome route** e **nome endpoint:**

* Sono intercambiabili.
* Quale viene utilizzato nella documentazione e nel codice dipende dall'API descritta.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Routing degli attributi per le API RESTAttribute routing for REST APIs

Le API REST devono usare il routing degli attributi per modellare la funzionalità dell'app come un set di risorse in cui le operazioni sono rappresentate da [verbi HTTP.](#verb)

Il routing con attributi usa un set di attributi per eseguire il mapping delle azioni direttamente ai modelli di route. Il `StartUp.Configure` codice seguente è tipico per un'API REST e viene usato nell'esempio successivo:The following code is typical for a REST API and is used in the next sample:

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

Nel codice precedente, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> viene `UseEndpoints` chiamato all'interno per eseguire il mapping dei controller instradati dell'attributo.

Nell'esempio seguente:

* Viene utilizzato `Configure` il metodo precedente.
* `HomeController`corrisponde a un set di URL simile `{controller=Home}/{action=Index}/{id?}` a quello corrispondente alla route convenzionale predefinita.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

L'azione `HomeController.Index` viene eseguita per `/` `/Home`uno `/Home/Index`qualsiasi `/Home/Index/3`dei percorsi URL , , , o .

In questo esempio viene evidenziata una differenza di programmazione fondamentale tra il routing degli attributi e [il routing convenzionale.](#cr) Il routing degli attributi richiede più input per specificare una route. Il percorso predefinito convenzionale gestisce i percorsi in modo più conciso. Tuttavia, il routing degli attributi consente e richiede un controllo preciso dei modelli di route applicati a ogni [azione.](#action)

Con il routing degli attributi, il nome del controller e i nomi delle azioni non svolgono **alcun** ruolo in cui viene trovata una corrispondenza con l'azione. L'esempio seguente corrisponde agli stessi URL dell'esempio precedente:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Il codice seguente usa `action` `controller`la sostituzione di token per e :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Il codice `[Route("[controller]/[action]")]` seguente si applica al controller:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Nel codice precedente, `Index` i modelli di `/` metodo `~/` devono anteporre o ai modelli di ciclo di lavorazione. I modelli di route applicati a un'azione che iniziano con `/` o `~/` non vengono combinati con i modelli di route applicati al controller.

Vedere [Precedenza del modello](xref:fundamentals/routing#rtp) di percorso per informazioni sulla selezione del modello di percorso.

## <a name="reserved-routing-names"></a>Nomi riservati di routing

Le parole chiave seguenti sono nomi di parametri di route riservati quando si utilizzano controller o pagine Razor:The following keywords are reserved route parameter names when using Controllers or Razor Pages:

* `action`
* `area`
* `controller`
* `handler`
* `page`

L'utilizzo `page` come parametro route con routing degli attributi è un errore comune. In questo modo si ottiene un comportamento incoerente e confuso con la generazione di URL.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.

<a name="verb"></a>

## <a name="http-verb-templates"></a>Modelli di verbi HTTP

ASP.NET Core dispone dei seguenti modelli di verbi HTTP:

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Modelli di percorso

ASP.NET Core dispone dei modelli di ciclo di lavorazione seguenti:

* Tutti i modelli di [verbo HTTP](#verb) sono modelli di route.
* [[Percorso]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Routing degli attributi con attributi di verbo HttpAttribute routing with Http verb attributes

Si consideri il controller seguente:Consider the following controller:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

Nel codice precedente:

* Ogni azione `[HttpGet]` contiene l'attributo , che vincola la corrispondenza solo alle richieste HTTP GET.
* L'azione `GetProduct` `"{id}"` include il `id` modello, pertanto viene aggiunto al `"api/[controller]"` modello nel controller. Il modello `"api/[controller]/"{id}""`di metodi è . Pertanto questa azione corrisponde solo `/api/test2/xyz`alle`/api/test2/123``/api/test2/{any string}`richieste GET di per il formato , , , e così via.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* L'azione `GetIntProduct` `"int/{id:int}")` contiene il modello. La `:int` parte del modello vincola i valori della `id` route alle stringhe che possono essere convertite in un numero intero. Una richiesta `/api/test2/int/abc`GET a :
  * Non corrisponde a questa azione.
  * Restituisce un errore [404 Non trovato.](https://developer.mozilla.org/docs/Web/HTTP/Status/404)
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* `GetInt2Product` L'azione `{id}` contiene nel modello, ma `id` non vincola avalori che possono essere convertiti in un numero intero. Una richiesta `/api/test2/int2/abc`GET a :
  * Corrisponde a questo percorso.
  * L'associazione `abc` di modelli non viene convertita in un numero intero. Il `id` parametro del metodo è integer.
  * Restituisce una [richiesta 400 non](https://developer.mozilla.org/docs/Web/HTTP/Status/400) `abc` valida perché l'associazione di modelli non è riuscita a convertire in un numero intero.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

Il routing <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> degli attributi <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>può <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>utilizzare attributi quali , e . Tutti gli attributi [dei verbi HTTP](#verb) accettano un modello di route. L'esempio seguente mostra due azioni che corrispondono allo stesso modello di route:The following example shows two actions that match the same route template:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Utilizzando il `/products3`percorso URL :

* L'azione `MyProductsController.ListProducts` viene eseguita `GET`quando il [verbo HTTP](#verb) è .
* L'azione `MyProductsController.CreateProduct` viene eseguita `POST`quando il [verbo HTTP](#verb) è .

Quando si compila un'API REST, è raro `[Route(...)]` che è necessario utilizzare su un metodo di azione perché l'azione accetta tutti i metodi HTTP. È preferibile usare [l'attributo verbo HTTP](#verb) più specifico per essere precisi su ciò che l'API supporta. I client delle API REST devono sapere quali percorsi e verbi HTTP devono essere mappati a operazioni logiche specifiche.

Le API REST devono usare il routing degli attributi per modellare la funzionalità dell'app come un set di risorse in cui le operazioni sono rappresentate da verbi HTTP. Ciò significa che molte operazioni, ad esempio GET e POST sulla stessa risorsa logica, utilizzano lo stesso URL. Il routing degli attributi offre un livello di controllo necessario per progettare con attenzione il layout dell'endpoint pubblico di un'API.

Poiché una route con attributi si applica a un'azione specifica, è facile fare in modo che i parametri siano richiesti come parte della definizione del modello di route. Nell'esempio seguente, `id` è necessario come parte del percorso URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

L'azione: `Products2ApiController.GetProduct(int)`

* Viene eseguito con il percorso URL come`/products2/3`
* Non viene eseguito con `/products2`il percorso URL .

L'attributo [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) consente a un'azione di limitare i tipi di contenuto della richiesta supportati. Per ulteriori informazioni, consultate [Definire i tipi di contenuto delle richieste supportati con l'attributo Consumes](xref:web-api/index#consumes).

 Vedere [Routing](xref:fundamentals/routing) per una descrizione completa dei modelli di route e delle opzioni correlate.

Per ulteriori `[ApiController]`informazioni [sull'attributo ApiController](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Nome route

Nell’esempio di codice riportato di seguito viene definito un nome di route di `Products_List`:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

I nomi di route possono essere usati per generare un URL in base a un percorso specifico. Nomi dei percorsi:

* Non hanno alcun impatto sul comportamento di corrispondenza URL del routing.
* Vengono utilizzati solo per la generazione di URL.

I nomi delle route devono essere univoci a livello di applicazione.

Confrontare il codice precedente con la route `id` predefinita convenzionale, che definisce il parametro come facoltativo ( ).`{id?}` La possibilità di specificare con precisione `/products` le `/products/5` API presenta vantaggi, ad esempio consentire e inviare a diverse azioni.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Combinazione di route di attributiCombining attribute routes

Per rendere il routing con attributi meno ripetitivo, gli attributi di route del controller vengono combinati con gli attributi di route delle singole azioni. I modelli di route definiti per il controller vengono anteposti ai modelli di route delle azioni. Inserendo un attributo di route nel controller, **tutte** le azioni presenti nel controller useranno il routing con attributi.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

Nell'esempio precedente:

* Il percorso `/products` URL può corrispondere`ProductsApi.ListProducts`
* Il percorso `/products/5` URL `ProductsApi.GetProduct(int)`può corrispondere a .

Entrambe queste azioni `GET` corrispondono solo a `[HttpGet]` HTTP perché sono contrassegnate con l'attributo.

I modelli di route applicati a un'azione che iniziano con `/` o `~/` non vengono combinati con i modelli di route applicati al controller. L'esempio seguente corrisponde a un set di percorsi URL simili alla route predefinita.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

Nella tabella seguente `[Route]` vengono illustrati gli attributi nel codice precedente:

| Attributo               | Si combina con`[Route("Home")]` | Definisce il modello di percorso |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Sì | `"Home"` |
| `[Route("Index")]` | Sì | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Sì | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Ordine ciclo di lavorazione attributi

Il routing crea un albero e corrisponde a tutti gli endpoint contemporaneamente:Routing builds a tree and matches all endpoints simultaneously:

* Le voci del ciclo di lavorazione si comportano come se siano collocate in un ordinamento ideale.
* I percorsi più specifici hanno la possibilità di essere eseguiti prima delle route più generali.

Ad esempio, una `blog/search/{topic}` route di attributo come `blog/{*article}`è più specifica di una route di attributo come . La `blog/search/{topic}` route ha una priorità più alta, per impostazione predefinita, perché è più specifica. Utilizzando il [routing convenzionale](#cr), lo sviluppatore è responsabile dell'inserimento dei percorsi nell'ordine desiderato.

Le route degli attributi <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> possono configurare un ordine utilizzando la proprietà . Tutti gli [attributi](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) della `Order` route forniti dal framework includono . Le route vengono elaborate in base a un ordinamento crescente della proprietà `Order`. L'ordine predefinito è `0`. Impostazione di `Order = -1` un percorso utilizzando le condutture prima dei percorsi che non impostano un ordine. Impostazione di `Order = 1` un ciclo di lavorazione utilizzando le condutture dopo l'ordinamento del ciclo di lavorazione predefinito.

**Evitare** `Order`di dipendere da . Se lo spazio URL di un'app richiede valori di ordine espliciti per essere instradati correttamente, è probabile che si confonda anche i client. In generale, il routing degli attributi seleziona la route corretta con la corrispondenza dell'URL. Se l'ordine predefinito utilizzato per la generazione di URL non funziona, l'utilizzo di un nome di route come override è in genere più semplice rispetto all'applicazione della `Order` proprietà.

Si considerino i due controller `/home`seguenti che definiscono entrambi la corrispondenza del percorso:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

La `/home` richiesta con il codice precedente genera un'eccezione simile alla seguente:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

L'aggiunta `Order` a uno degli attributi della route risolve l'ambiguità:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Con il codice `/home` precedente, `HomeController.Index` esegue l'endpoint. Per accedere `MyDemoController.MyIndex`a `/home/MyIndex`, richiedere . **Nota**:

* Il codice precedente è un esempio o una progettazione di routing scadente. E 'stato utilizzato `Order` per illustrare la proprietà.
* La `Order` proprietà risolve solo l'ambiguità, che il modello non può essere abbinato. Sarebbe meglio rimuovere il `[Route("Home")]` modello.

Vedere Convenzioni di route e app di pagine Razor: ordine del percorso per informazioni sull'ordine del percorso con le pagine Razor.See Razor Pages route and [app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.

In alcuni casi, viene restituito un errore HTTP 500 con route ambigue. Utilizzare [la registrazione](xref:fundamentals/logging/index) per vedere `AmbiguousMatchException`quali endpoint hanno causato il metodo .

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Sostituzione di token nei modelli di percorso [controller], [azione], [area]

Per comodità, le route degli attributi supportano la sostituzione di token per i parametri di route riservati racchiudendo un token in uno degli elementi seguenti:For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:

* Parentesi graffe quadrate:`[]`
* Parentesi graffe:`{}`

I token `[action]` `[area]`, `[controller]` , e vengono sostituiti con i valori del nome dell'azione, del nome dell'area e del nome del controller dell'azione in cui è definita la route:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

Nel codice precedente:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Corrisponde`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Corrisponde`/Products0/Edit/{id}`

La sostituzione dei token avviene come ultimo passaggio della creazione delle route con attributi. L'esempio precedente si comporta come il codice seguente:The preceding example behaves the same as the following code:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Le route con attributi possono anche essere combinate con l'ereditarietà. Questo è potente combinato con la sostituzione di token. La sostituzione dei token si applica anche ai nomi di route definiti dalle route con attributi.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`genera un nome di route univoco per ogni azione:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

La sostituzione dei token si applica anche ai nomi di route definiti dalle route con attributi.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
 genera un nome di route univoco per ogni azione.

Per verificare la corrispondenza del delimitatore letterale della sostituzione di token `[` o `]`, eseguirne l'escape ripetendo il carattere (`[[` o `]]`).

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Usare un trasformatore di parametri per personalizzare la sostituzione dei token

La sostituzione dei token può essere personalizzata usando un trasformatore di parametri. Un trasformatore di parametri implementa <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> e trasforma il valore dei parametri. Ad esempio, `SlugifyParameterTransformer` un trasformatore `SubscriptionManagement` di `subscription-management`parametri personalizzati modifica il valore della route in:

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> è una convenzione del modello di applicazione che:

* Applica un trasformatore di parametri a tutte le route di attributi in un'applicazione.
* Personalizza i valori dei token delle route di attributi quando vengono sostituiti.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

Il metodo `ListAll` precedente `/subscription-management/list-all`corrisponde a .

La convenzione `RouteTokenTransformerConvention` è registrata come un'opzione in `ConfigureServices`.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Per la definizione di Slug, vedere Documenti [Web di MDN su Slug.](https://developer.mozilla.org/docs/Glossary/Slug)

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Più route di attributiMultiple attribute routes

Il routing con attributi supporta la definizione di più route che raggiungono la stessa azione. L'uso più comune è simulare il comportamento della route convenzionale predefinita come illustrato nell'esempio seguente:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

L'inserimento di più attributi di route nel controller significa che ognuno combina con ognuno degli attributi del percorso nei metodi di azione:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Tutti i vincoli `IActionConstraint`di route [dei verbi HTTP](#verb) implementano .

Quando più attributi <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> di route che implementano vengono inseriti in un'azione:When multiple route attributes that implement are placed on an action:

* Ogni vincolo di azione viene combinato con il modello di route applicato al controller.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

L'uso di più percorsi sulle azioni può sembrare utile e potente, è preferibile mantenere lo spazio URL dell'app di base e ben definito. Utilizzare più route sulle azioni **solo** se necessario, ad esempio per supportare i client esistenti.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Definizione di parametri facoltativi, valori predefiniti e vincoli della route con attributi

Le route con attributi supportano la stessa sintassi inline delle route convenzionali per specificare i parametri facoltativi, i valori predefiniti e i vincoli.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

Nel codice precedente, `[HttpPost("product/{id:int}")]` applica un vincolo di route. L'azione `ProductsController.ShowProduct` viene confrontata `/product/3`solo con percorsi URL come . La parte `{id:int}` del modello di route vincola il segmento ai soli numeri interi.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Vedere [Riferimento per i modelli di route](xref:fundamentals/routing#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Attributi di route personalizzati tramite IRouteTemplateProviderCustom route attributes using IRouteTemplateProvider

Tutti gli [attributi](#rt) <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>della route implementano . Il runtime di ASP.NET Core:

* Cerca gli attributi nelle classi controller e nei metodi di azione all'avvio dell'app.
* Utilizza gli attributi `IRouteTemplateProvider` che implementano per compilare il set iniziale di route.

Implementare `IRouteTemplateProvider` per definire gli attributi della route personalizzati. Ogni `IRouteTemplateProvider` consente di definire una singola route con un modello di route, un ordinamento e un nome personalizzati:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

Il metodo `Get` precedente `Order = 2, Template = api/MyTestApi`restituisce .

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Utilizzare il modello di applicazione per personalizzare le route degli attributiUse application model to customize attribute routes

Il modello applicativo:

* È un modello a oggetti creato all'avvio.
* Contiene tutti i metadati usati da ASP.NET Core per instradare ed eseguire le azioni in un'app.

Il modello di applicazione include tutti i dati raccolti dagli attributi del percorso. I dati degli attributi della `IRouteTemplateProvider` route vengono forniti dall'implementazione. Convenzioni:

* Può essere scritto per modificare il modello dell'applicazione per personalizzare il funzionamento del routing.
* Vengono letti all'avvio dell'app.

In questa sezione viene illustrato un esempio di base della personalizzazione del routing tramite il modello di applicazione. Il codice seguente rende i percorsi più o meno allineati con la struttura di cartelle del progetto.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Il codice seguente `namespace` impedisce che la convenzione venga applicata ai controller con l'attributo route:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Ad esempio, il seguente controller `NamespaceRoutingConvention`non utilizza:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

Il metodo `NamespaceRoutingConvention.Apply`:

* Non esegue alcuna operazione se il controller è attribuito instradato.
* Imposta il modello di `namespace`controller in `namespace` base alla base , con la base rimossa.

Il `NamespaceRoutingConvention` può essere `Startup.ConfigureServices`applicato in:

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Si consideri ad esempio il controller seguente:For example, consider the following controller:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

Nel codice precedente:

* La `namespace` base `My.Application`è .
* Il nome completo del controller `My.Application.Admin.Controllers.UsersController`precedente è .
* Il `NamespaceRoutingConvention` imposta il `Admin/Controllers/Users/[action]/{id?`modello di controller su .

Il `NamespaceRoutingConvention` può anche essere applicato come attributo su un controller:The can also be applied as an attribute on a controller:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Routing misto: routing con attributi e routing convenzionale

ASP.NET le app principali possono combinare l'uso del routing convenzionale e del routing degli attributi. In genere le route convenzionali vengono usate per i controller che gestiscono le pagine HTML per i browser e le route con attributi per i controller che gestiscono le API REST.

Le azioni vengono indirizzate in modo convenzionale o con attributi. Se una route viene inserita nel controller o nell'azione, viene indirizzata con attributi. Le azioni che definiscono le route con attributi non possono essere raggiunte usando le route convenzionali e viceversa. **Qualsiasi** attributo route nel controller esegue **tutte le** azioni nell'attributo del controller instradato.

Il routing degli attributi e il routing convenzionale utilizzano lo stesso motore di routing.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>Generazione URL e valori di ambiente

Le app possono usare le funzionalità di generazione degli URL di routing per generare collegamenti URL ad azioni. La generazione di URL elimina gli URL hardcoded, rendendo il codice più affidabile e gestibile. Questa sezione è incentrata sulle funzionalità di generazione degli URL fornite da MVC e illustra solo le nozioni di base sul funzionamento della generazione di URL. Vedere [Routing](xref:fundamentals/routing) per una descrizione dettagliata della generazione di URL.

L'interfaccia <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> è l'elemento sottostante dell'infrastruttura tra MVC e routing per la generazione di URL. Un'istanza `IUrlHelper` di è `Url` disponibile tramite la proprietà in controller, visualizzazioni e componenti di visualizzazione.

Nell'esempio seguente, `IUrlHelper` l'interfaccia `Controller.Url` viene utilizzata tramite la proprietà per generare un URL a un'altra azione.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Se l'app utilizza la route convenzionale `url` predefinita, il `/UrlGeneration/Destination`valore della variabile è la stringa del percorso URL . Questo percorso URL viene creato mediante routing combinando:

* I valori della route dalla richiesta corrente, denominati **valori di ambiente**.
* I valori `Url.Action` passati e inseriti in tali valori nel modello di route:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Il valore di ogni parametro di route del modello di route viene sostituito attraverso la corrispondenza dei nomi con i valori e i valori di ambiente. Un parametro di route che non ha un valore può:A route parameter that doesn't have a value can:

* Utilizzare un valore predefinito se ne è stato utilizzato uno.
* Essere saltato se è facoltativo. Ad esempio, `id` il dal `{controller}/{action}/{id?}`modello di percorso .

La generazione dell'URL non riesce se qualsiasi parametro di route obbligatorio non ha un valore corrispondente. Se la generazione di URL non riesce per una route, viene tentata la route successiva finché non vengono tentate tutte le route o viene trovata una corrispondenza.

Nell'esempio precedente `Url.Action` di presuppone il [routing convenzionale](#cr). La generazione di URL funziona in modo simile con il routing degli [attributi](#ar), anche se i concetti sono diversi. Con il routing convenzionale:

* I valori della route vengono utilizzati per espandere un modello.
* I valori `controller` del `action` percorso per e in genere vengono visualizzati in tale modello. Questo funziona perché gli URL corrispondenti al routing aderiscono a una convenzione.

Nell'esempio seguente viene utilizzato il routing degli attributi:The following example uses attribute routing:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

L'azione `Source` nel codice `custom/url/to/destination`precedente genera .

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>è stato aggiunto in ASP.NET Core `IUrlHelper`3.0 come alternativa a . `LinkGenerator`offre funzionalità simili ma più flessibili. Ogni metodo `IUrlHelper` su ha una `LinkGenerator` famiglia corrispondente di metodi su pure.

### <a name="generating-urls-by-action-name"></a>Generazione di URL in base al nome dell'azione

[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)e tutti gli overload correlati sono tutti progettati per generare l'endpoint di destinazione specificando un nome di controller e un nome di azione.

Quando `Url.Action`si utilizza , `controller` i `action` valori della route correnti per e vengono forniti dal runtime:

* Il valore `controller` `action` di e fanno parte sia dei [valori di ambiente](#ambient) che dei valori. Il `Url.Action` metodo utilizza sempre `action` i `controller` valori correnti di e genera un percorso URL che indirizza all'azione corrente.

Il routing tenta di utilizzare i valori nei valori di ambiente per inserire informazioni non fornite durante la generazione di un URL. Si consideri `{a}/{b}/{c}/{d}` un `{ a = Alice, b = Bob, c = Carol, d = David }`percorso come con i valori di ambiente :

* Il routing dispone di informazioni sufficienti per generare un URL senza valori aggiuntivi.
* Il routing dispone di informazioni sufficienti perché tutti i parametri di route hanno un valore.

Se il `{ d = Donovan }` valore viene aggiunto:

* Il `{ d = David }` valore viene ignorato.
* Il percorso URL `Alice/Bob/Carol/Donovan`generato è .

**Avviso:** i percorsi URL sono gerarchici. Nell'esempio precedente, se `{ c = Cheryl }` il valore viene aggiunto:

* Entrambi i `{ c = Carol, d = David }` valori vengono ignorati.
* Non esiste più un `d` valore per e la generazione dell'URL ha esito negativo.
* I valori `c` desiderati `d` di e devono essere specificati per generare un URL.  

Si potrebbe prevedere di colpire questo `{controller}/{action}/{id?}`problema con la route predefinita . Questo problema è raro `Url.Action` nella pratica `controller` perché `action` specifica sempre in modo esplicito un valore e .

Diversi overload di [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) accettano un oggetto valori di `controller` `action`route per fornire valori per i parametri di route diversi da e . L'oggetto valori di `id`route viene spesso utilizzato con . Ad esempio: `Url.Action("Buy", "Products", new { id = 17 })`. Oggetto dei valori del percorso:

* Per convenzione è in genere un oggetto di tipo anonimo.
* Può essere `IDictionary<>` un o un [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).

I valori di route aggiuntivi che non corrispondono a parametri di route vengono inseriti nella stringa di query.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Il codice precedente `/Products/Buy/17?color=red`genera .

Il codice seguente genera un URL assoluto:The following code generates an absolute URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Per creare un URL assoluto, utilizzare una delle seguenti opzioni:

* Overload che accetta `protocol`un oggetto . Ad esempio, il codice precedente.
* [LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), che genera URI assoluti per impostazione predefinita.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Genera URL per route

Il codice precedente ha dimostrato la generazione di un URL passando il nome del controller e dell'azione. `IUrlHelper`fornisce inoltre la famiglia di metodi [Url.RouteUrl.](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) Questi metodi sono simili a [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ma non `action` `controller` copiano i valori correnti di e nei valori della route. L'uso più `Url.RouteUrl`comune di :

* Specifica un nome di route per generare l'URL.
* In genere non specifica un nome di controller o azione.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Il seguente file Razor genera `Destination_Route`un collegamento HTML al :

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>Generare URL in HTML e Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>fornisce <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> i metodi [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) e [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) per generare `<form>` rispettivamente e `<a>` gli elementi. Questi metodi utilizzano il metodo [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) per generare un URL e accettano argomenti simili. Gli oggetti `Url.RouteUrl` complementi di `HtmlHelper` sono `Html.BeginRouteForm` e `Html.RouteLink` e hanno una funzionalità simile.

Gli helper tag generano gli URL attraverso l'helper tag `form` e l'helper tag `<a>`. Entrambi usano `IUrlHelper` per la propria implementazione. Per ulteriori informazioni, vedere [Helper tag nei moduli.](xref:mvc/views/working-with-forms)

All'interno delle visualizzazioni `IUrlHelper` è reso disponibile dalla proprietà `Url` per tutte le generazioni di URL ad hoc che non rientrano nelle situazioni descritte in precedenza.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Generazione di URL nei risultati dell'azione

Negli esempi precedenti `IUrlHelper` è stato illustrato l'utilizzo in un controller. L'utilizzo più comune in un controller consiste nel generare un URL come parte del risultato di un'azione.

Le classi di base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> e <xref:Microsoft.AspNetCore.Mvc.Controller> offrono metodi pratici per i risultati delle azioni che fanno riferimento a un'altra azione. Un utilizzo tipico è quello di reindirizzare dopo aver accettato l'input dell'utente:One typical usage is to redirect after accepting user input:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

L'azione restituisce <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> metodi <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> factory, ad esempio `IUrlHelper`e seguire un modello simile ai metodi in .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Caso speciale per le route convenzionali dedicate

[Il routing convenzionale](#cr) può utilizzare un tipo speciale di definizione di percorso denominato [percorso convenzionale dedicato.](#dcr) Nell'esempio seguente, la `blog` route denominata è una route convenzionale dedicata:In the following example, the route named is a dedicated conventional route:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Utilizzando le definizioni `Url.Action("Index", "Home")` di route `/` precedenti, `default` genera il percorso URL utilizzando la route, ma perché? Si potrebbe pensare che i valori di route `{ controller = Home, action = Index }` siano sufficienti per generare un URL usando `blog` e che il risultato sia `/blog?action=Index&controller=Home`.

[Le route convenzionali dedicate](#dcr) si basano su un comportamento speciale dei valori predefiniti che non dispongono di un parametro di route corrispondente che impedisce che la route sia troppo [avida](xref:fundamentals/routing#greedy) con la generazione dell'URL. In questo caso i valori predefiniti sono `{ controller = Blog, action = Article }` e né `controller` né `action` vengono visualizzati come parametri di route. Quando il routing esegue la generazione di URL, i valori specificati devono corrispondere ai valori predefiniti. La generazione `blog` dell'URL `{ controller = Home, action = Index }` utilizzando non `{ controller = Blog, action = Article }`riesce perché i valori non corrispondono a . Il routing quindi esegue il fallback per provare `default`, che ha esito positivo.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Aree

[Le aree](xref:mvc/controllers/areas) sono una funzionalità MVC utilizzata per organizzare le funzionalità correlate in un gruppo come elemento separato:Areas are an MVC feature used to organize related functionality into a group as a separate:

* Spazio dei nomi di routing per le azioni del controller.
* Struttura delle cartelle per le viste.

L'uso di aree consente a un'app di avere più controller con lo stesso nome, purché abbiano aree diverse. Usando le aree si crea una gerarchia per il routing aggiungendo un altro parametro di route, `area` a `controller` e `action`. In questa sezione viene illustrato come il routing interagisce con le aree. Per informazioni dettagliate sull'utilizzo delle aree con le viste, vedere [Aree.](xref:mvc/controllers/areas)

Nell'esempio seguente MVC viene configurato per `area` utilizzare la `area` `Blog`route convenzionale predefinita e una route per un oggetto denominato:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Nel codice precedente, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> viene chiamato `"blog_route"`per creare l'oggetto . Il secondo `"Blog"`parametro, , è il nome dell'area.

Quando si trova `/Manage/Users/AddUser`una `"blog_route"` corrispondenza con `{ area = Blog, controller = Users, action = AddUser }`un percorso URL come , la route genera i valori della route. Il `area` valore della route viene `area`prodotto da un valore predefinito per . Il ciclo `MapAreaControllerRoute` di lavorazione creato da equivale a quanto segue:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute` crea una route che usa sia un valore predefinito che un vincolo per `area` usando il nome di area specificato, in questo caso `Blog`. Il valore predefinito assicura che la route generi sempre `{ area = Blog, ... }`, il vincolo richiede il valore `{ area = Blog, ... }` per la generazione di URL.

Il routing convenzionale dipende dall'ordinamento. In generale, i percorsi con aree devono essere posizionati prima in quanto sono più specifici dei percorsi senza area.

Utilizzando l'esempio precedente, `{ area = Blog, controller = Users, action = AddUser }` i valori della route corrispondono all'azione seguente:Using the preceding example, the route values match the following action:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

L'attributo [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) è ciò che denota un controller come parte di un'area. Questo controller si `Blog` trova nella zona. I controller `[Area]` senza un attributo non sono membri `area` di alcuna area e **non** corrispondono quando il valore della route viene fornito dal routing. Nell'esempio seguente solo il primo controller indicato può corrispondere ai valori di route `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Lo spazio dei nomi di ogni controller viene illustrato qui per completezza. Se i controller precedenti utilizzano lo stesso spazio dei nomi, verrebbe generato un errore del compilatore. Gli spazi dei nomi delle classi non hanno effetto sul routing di MVC.

I primi due controller sono membri di aree e corrispondono solo quando viene specificato il relativo nome di area dal valore di route `area`. Il terzo controller non è un membro di un'area e può corrispondere solo quando non vengono specificati valori per `area` dal routing.

<a name="aa"></a>

In termini di corrispondenza con *nessun valore*, l'assenza del valore `area` è come se il valore per `area` fosse Null o la stringa vuota.

Quando si esegue un'azione all'interno `area` di un'area, il valore di route per è disponibile come [valore di ambiente](#ambient) per il routing da utilizzare per la generazione di URL. Ciò significa che per impostazione predefinita le aree funzionano *temporaneamente* per la generazione di URL, come illustrato nell'esempio seguente.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Il codice seguente genera `/Zebra/Users/AddUser`un URL per:

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Definizione dell'azione

I metodi pubblici in un controller, ad eccezione di quelli con l'attributo [NonAction,](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) sono azioni.

## <a name="sample-code"></a>Codice di esempio

 * Il [myDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) metodo è incluso nel [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) e viene utilizzato per visualizzare le informazioni di routing.
* [Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ( come[scaricare](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC usa [middleware](xref:fundamentals/middleware/index) di routing per verificare la corrispondenza degli URL delle richieste in ingresso ed eseguirne il mapping alle azioni. Le route sono definite nel codice di avvio o negli attributi. Le route descrivono il modo in cui i percorsi URL devono corrispondere alle azioni. Vengono usate anche per generare gli URL (per i collegamenti) inviati nelle risposte.

Le azioni vengono indirizzate in modo convenzionale o con attributi. Se una route viene inserita nel controller o nell'azione, viene indirizzata con attributi. Per altre informazioni, vedere [Routing misto](#routing-mixed-ref-label).

Questo documento illustra le interazioni tra MVC e il routing e il modo in cui le tipiche app MVC usano le funzionalità di routing. Vedere [Routing](xref:fundamentals/routing) per informazioni dettagliate sul routing avanzato.

## <a name="setting-up-routing-middleware"></a>Impostazione del middleware di routing

Nel metodo *Configure* può apparire codice simile al seguente:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Nella chiamata a `UseMvc`, si usa `MapRoute` per creare una singola route, a cui si farà riferimento come alla route predefinita (`default`). La maggior parte delle app MVC userà una route con un modello simile alla route `default`.

Il modello di route `"{controller=Home}/{action=Index}/{id?}"` può corrispondere a un percorso URL come `/Products/Details/5` ed estrae i valori di route `{ controller = Products, action = Details, id = 5 }` suddividendo il percorso in token. MVC tenterà di individuare un controller denominato `ProductsController` e di eseguire l'azione `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Si noti che in questo esempio l'associazione di modelli usa il valore di `id = 5` per impostare il parametro `id` su `5` quando si richiama l'azione. Per maggiori dettagli, vedere [Associazione di modelli](../models/model-binding.md).

Usando la route `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Il modello di route:

* `{controller=Home}` definisce `Home` come oggetto `controller` predefinito

* `{action=Index}` definisce `Index` come oggetto `action` predefinito

* `{id?}` definisce `id` come facoltativo

I parametri di route predefiniti e facoltativi non devono necessariamente essere presenti nel percorso URL per trovare una corrispondenza. Vedere [Riferimento per i modelli di route](xref:fundamentals/routing#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.

`"{controller=Home}/{action=Index}/{id?}"` può corrispondere al percorso URL `/` e restituirà i valori di route `{ controller = Home, action = Index }`. I valori per `controller` e `action` usano i valori predefiniti, `id` non genera un valore perché non esiste un segmento corrispondente nel percorso URL. MVC usa questi valori di route per selezionare l'azione `HomeController` e `Index`:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Usando questa definizione del controller e il modello di route, l'azione `HomeController.Index` viene eseguita per tutti i percorsi URL seguenti:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Il metodo pratico `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

Può essere usato per sostituire:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` e `UseMvcWithDefaultRoute` aggiungono un'istanza di `RouterMiddleware` alla pipeline middleware. MVC non interagisce direttamente con il middleware e usa il routing per gestire le richieste. MVC è connesso alle route attraverso un'istanza di `MvcRouteHandler`. Il codice all'interno di `UseMvc` è simile al seguente:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` non definisce direttamente le route, aggiunge un segnaposto alla raccolta di route per la route `attribute`. L'overload `UseMvc(Action<IRouteBuilder>)` consente di aggiungere le proprie route e supporta anche il routing con attributi.  `UseMvc`e tutte le relative varianti aggiungono un segnaposto per la route degli `UseMvc`attributi: il routing degli attributi è sempre disponibile indipendentemente dalla configurazione di . `UseMvcWithDefaultRoute` definisce una route predefinita e supporta il routing con attributi. La sezione [Routing con attributi](#attribute-routing-ref-label) include maggiori dettagli sul routing con attributi.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Routing convenzionale

La route predefinita (`default`):

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Il codice precedente è un esempio di routing convenzionale. Questo stile è chiamato routing convenzionale perché stabilisce una *convenzione* per i percorsi URL:This style is called conventional routing because it establishes a convention for URL paths:

* Il primo segmento di percorso esegue il mapping al nome del controller.
* Il secondo esegue il mapping al nome dell'azione.
* Il terzo segmento viene `id`utilizzato per un oggetto . `id`esegue il mapping a un'entità modello.

Usando la route `default`, il percorso URL `/Products/List` esegue il mapping all'azione `ProductsController.List` e `/Blog/Article/17` lo esegue a `BlogController.Article`. Questo mapping si basa **solo** sui nomi dell'azione e del controller e non su spazi dei nomi, percorsi dei file di origine o parametri del metodo.

> [!TIP]
> L'uso del routing convenzionale con la route predefinita consente di compilare rapidamente l'applicazione senza dover creare un nuovo modello di URL per ogni azione che si definisce. Per un'applicazione con azioni di stile CRUD, la coerenza degli URL tra i controller può semplificare il codice e rendere l'interfaccia utente più prevedibile.

> [!WARNING]
> L'oggetto `id` è definito come facoltativo nel modello di route, ovvero le azioni possono essere eseguite senza l'ID specificato come parte dell'URL. In genere, se si omette `id` dall'URL, il valore viene impostato su `0` dall'associazione del modello e di conseguenza non viene rilevata alcuna entità nel database corrispondente a `id == 0`. Il routing con attributi offre un controllo con granularità fine che consente di rendere l'ID obbligatorio per alcune azioni e non per altre. Per convenzione la documentazione include i parametri facoltativi come `id` quando è probabile che appaiano nell'uso corretto.

## <a name="multiple-routes"></a>Route multiple

È possibile aggiungere più route all'interno di `UseMvc` aggiungendo altre chiamate a `MapRoute`. Questa operazione consente di definire più convenzioni o di aggiungere route convenzionali dedicate a un'azione specifica, ad esempio:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

La route `blog` è una *route convenzionale dedicata*, vale a dire che usa il sistema di routing convenzionale, ma è dedicata a un'azione specifica. Poiché `controller` e `action` non appaiono nel modello di route come parametri, possono avere solo i valori predefiniti e quindi questa route eseguirà sempre il mapping all'azione `BlogController.Article`.

Le route sono ordinate nella raccolta di route e verranno elaborate nell'ordine in cui vengono aggiunte. Quindi in questo esempio la route `blog` viene tentata prima della route `default`.

> [!NOTE]
> *Le route convenzionali dedicate* spesso utilizzano parametri di route **catch-all** come `{*article}` per acquisire la parte rimanente del percorso URL. In questo modo una route può diventare troppo "greedy", ovvero corrispondere agli URL che dovrebbero corrispondere ad altre route. Posizionare le route "greedy" più avanti nella tabella delle route per risolvere il problema.

### <a name="fallback"></a>Fallback

Come parte dell'elaborazione della richiesta, MVC verifica che i valori di route possano essere usati per trovare un controller e un'azione nell'applicazione. Se i valori di route non corrispondano a un'azione, la route non viene considerata una corrispondenza e viene tentata la route successiva. Questa operazione si chiama *fallback* e viene eseguita allo scopo di semplificare i casi in cui le route convenzionali si sovrappongono.

### <a name="disambiguating-actions"></a>Rimozione dell'ambiguità delle azioni

Quando due azioni corrispondono in base al routing, MVC deve rimuovere l'ambiguità per scegliere il candidato migliore, altrimenti genera un'eccezione. Ad esempio:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Questo controller definisce due azioni corrispondenti al percorso URL `/Products/Edit/17` e indirizzano i dati `{ controller = Products, action = Edit, id = 17 }`. Si tratta di un modello tipico per i controller MVC in cui `Edit(int)` visualizza un modulo per modificare un prodotto e `Edit(int, Product)` elabora il modulo inviato. Per poter eseguire l'operazione, MVC deve scegliere `Edit(int, Product)` se la richiesta è un `POST` HTTP e `Edit(int)` quando il verbo HTTP è un altro elemento.

`HttpPostAttribute` (`[HttpPost]`) è un'implementazione di `IActionConstraint` che consente di selezionare l'azione solo quando il verbo HTTP è `POST`. La presenza di un oggetto `IActionConstraint` fa sì che `Edit(int, Product)` sia una corrispondenza migliore di `Edit(int)`, quindi `Edit(int, Product)` viene tentata per prima.

È necessario scrivere implementazioni personalizzate di `IActionConstraint` solo in scenari specializzati, ma è importante comprendere il ruolo degli attributi, ad esempio `HttpPostAttribute`. Attributi simili vengono definiti per altri verbi HTTP. Nel routing convenzionale è normale che le azioni usino lo stesso nome di azione quando fanno parte di un flusso di lavoro `show form -> submit form`. La comodità offerta da questo modello sarà più evidente dopo aver esaminato la sezione relativa a [IActionConstraint](#understanding-iactionconstraint).

Se vi sono più route corrispondenti e MVC non è in grado di trovare la route migliore, viene generata un'eccezione `AmbiguousActionException`.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Nomi di route

Le stringhe `"blog"` e `"default"` degli esempi seguenti sono nomi di route:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

I nomi di route consentono di assegnare a una route un nome logico, in modo che la route denominata possa essere usata per la generazione di URL. Questo semplifica notevolmente la creazione degli URL quando l'ordinamento delle route può rendere complessa la generazione di URL. I nomi delle route devono essere univoci a livello di applicazione.

I nomi di route non influiscono sulla corrispondenza degli URL o sulla gestione delle richieste, vengono usati solo per la generazione di URL. L'articolo sul [routing](xref:fundamentals/routing) contiene informazioni dettagliate sulla generazione di URL, inclusa la generazione negli helper specifici di MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Routing con attributi

Il routing con attributi usa un set di attributi per eseguire il mapping delle azioni direttamente ai modelli di route. Nell'esempio seguente viene usato l'oggetto `app.UseMvc();` nel metodo `Configure` e non viene passata alcuna route. `HomeController` corrisponde a un set di URL simile a quello a cui corrisponderebbe la route predefinita `{controller=Home}/{action=Index}/{id?}`:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

L'azione `HomeController.Index()` verrà eseguita per tutti i percorsi URL, `/`, `/Home` o `/Home/Index`.

> [!NOTE]
> In questo esempio viene evidenziata un'importante differenza a livello di programmazione tra il routing con attributi e il routing convenzionale. Il routing con attributi richiede più input per specificare una route, la route convenzionale predefinita gestisce le route in modo più conciso. Tuttavia, il routing con attributi consente, e richiede, un controllo preciso dei modelli route da applicare a ogni azione.

Con il routing con attributi i nomi del controller e delle azioni **non** hanno alcun ruolo nella selezione dell'azione. In questo esempio viene verificata la corrispondenza degli stessi URL dell'esempio precedente.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> I modelli di route precedenti non definiscono i parametri di route per `action`, `area` e `controller`. In realtà, questi parametri di route non sono consentiti nelle route con attributi. Poiché il modello di route è già associato a un'azione, non avrebbe senso analizzare il nome dell'azione dall'URL.

## <a name="attribute-routing-with-httpverb-attributes"></a>Routing con attributi Http[Verb]

Il routing con attributi può usare anche gli attributi `Http[Verb]`, ad esempio `HttpPostAttribute`. Tutti questi attributi possono accettare un modello di route. Questo esempio illustra due azioni che corrispondono allo stesso modello di route:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Per un percorso URL come `/products` l'azione `ProductsApi.ListProducts` verrà eseguita quando il verbo HTTP è `GET` e `ProductsApi.CreateProduct` verrà eseguita quando il verbo HTTP è `POST`. Nel routing con attributi l'URL viene prima confrontato con il set di modelli di route definiti dagli attributi della route. Quando un modello di route corrisponde, vengono applicati i vincoli `IActionConstraint` per determinare quali azioni possono essere eseguite.

> [!TIP]
> Quando si compila un'API REST, è raro `[Route(...)]` che si desidera utilizzare su un metodo di azione come l'azione accetterà tutti i metodi HTTP. Si consiglia di usare l'oggetto `Http*Verb*Attributes`, più specifico, per essere precisi circa gli elementi supportati dall'API. I client delle API REST devono sapere quali percorsi e verbi HTTP devono essere mappati a operazioni logiche specifiche.

Poiché una route con attributi si applica a un'azione specifica, è facile fare in modo che i parametri siano richiesti come parte della definizione del modello di route. In questo esempio `id` è richiesto come parte del percorso URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

L'azione `ProductsApi.GetProduct(int)` verrà eseguita per un percorso URL come `/products/3`, ma non per un percorso URL come `/products`. Vedere [Routing](xref:fundamentals/routing) per una descrizione completa dei modelli di route e delle opzioni correlate.

## <a name="route-name"></a>Nome di route

Nell’esempio di codice riportato di seguito viene definito un *nome di route* di `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

I nomi di route possono essere usati per generare un URL in base a un percorso specifico. I nomi di route non influiscono sul comportamento del routing riguardo alla corrispondenza degli URL e vengono usati solo per la generazione di URL. I nomi delle route devono essere univoci a livello di applicazione.

> [!NOTE]
> Confrontare questa opzione con la *route predefinita* convenzionale, che definisce il parametro `id` come facoltativo (`{id?}`). La possibilità di specificare in modo preciso le API presenta dei vantaggi, ad esempio consente di inviare `/products` e `/products/5` ad azioni  differenti.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Combinazione di route

Per rendere il routing con attributi meno ripetitivo, gli attributi di route del controller vengono combinati con gli attributi di route delle singole azioni. I modelli di route definiti per il controller vengono anteposti ai modelli di route delle azioni. Inserendo un attributo di route nel controller, **tutte** le azioni presenti nel controller useranno il routing con attributi.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

In questo esempio il percorso URL `/products` può corrispondere a `ProductsApi.ListProducts` e il percorso URL `/products/5` può corrispondere a `ProductsApi.GetProduct(int)`. Entrambe queste azioni `GET` corrispondono solo a `HttpGetAttribute`HTTP perché sono contrassegnate con il file .

I modelli di route applicati a un'azione che iniziano con `/` o `~/` non vengono combinati con i modelli di route applicati al controller. Questo esempio illustra la corrispondenza di un set di percorsi URL simile alla *route predefinita*.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Ordinamento delle route con attributi

A differenza delle route convenzionali, che vengono eseguite in un ordine definito, il routing degli attributi crea un albero e corrisponde a tutte le route contemporaneamente. Si comporta come se le voci delle route seguissero un ordine ideale in cui le route più specifiche vengono probabilmente eseguite prima delle route più generali.

Ad esempio, una route come `blog/search/{topic}` è più specifica di una route come `blog/{*article}`. In termini logici la route `blog/search/{topic}` viene eseguita per prima, per impostazione predefinita, perché questo è l'ordine più plausibile. Usando il routing convenzionale, lo sviluppatore ordina le route in base alle proprie esigenze.

Nelle route con attributi si può configurare un ordine usando la proprietà `Order` di tutti gli attributi di route specificati dal framework. Le route vengono elaborate in base a un ordinamento crescente della proprietà `Order`. L'ordine predefinito è `0`. L'impostazione di una route usando `Order = -1` viene eseguita prima delle route per cui non è impostato un ordine. L'impostazione di una route usando `Order = 1` viene eseguito dopo l'ordinamento predefinito delle route.

> [!TIP]
> Evitare la dipendenza da `Order`. Se lo spazio URL richiede i valori in un ordine esplicito per indirizzare correttamente i dati, si può probabilmente creare confusione anche per i client. In genere il routing con attributi seleziona la route corretta con la corrispondenza di URL. Se l'ordine predefinito usato per la generazione di URL non funziona, usare il nome della route come override è in genere più semplice che applicare la proprietà `Order`.

Il routing di Razor Pages e il routing del controller MVC condividono un'implementazione. Per informazioni sull'ordine di routing negli argomenti di Razor Pages, vedere [Convenzioni di route e app per Razor Pages in ASP.NET Core: Ordine della route](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Sostituzione dei token nei modelli di route ([controller], [action], [area])

Per comodità, le route degli attributi supportano la`[`sostituzione `]`di *token* racchiudendo un token tra parentesi quadrate ( , ). I token `[action]`, `[area]` e `[controller]` vengono sostituiti con i valori di nome dell'azione, nome dell'area e nome del controller dall'azione in cui è definita la route. Nell'esempio seguente le azioni corrispondono ai percorsi URL come descritto nei commenti:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

La sostituzione dei token avviene come ultimo passaggio della creazione delle route con attributi. L'esempio precedente si comporterà come il codice seguente:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Le route con attributi possono anche essere combinate con l'ereditarietà. Ciò risulta particolarmente efficace se combinato con la sostituzione dei token.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

La sostituzione dei token si applica anche ai nomi di route definiti dalle route con attributi. `[Route("[controller]/[action]", Name="[controller]_[action]")]` genera un nome di route univoco per ogni azione.

Per verificare la corrispondenza del delimitatore letterale della sostituzione di token `[` o `]`, eseguirne l'escape ripetendo il carattere (`[[` o `]]`).

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Usare un trasformatore di parametri per personalizzare la sostituzione dei token

La sostituzione dei token può essere personalizzata usando un trasformatore di parametri. Un trasformatore di parametri implementa `IOutboundParameterTransformer` e trasforma il valore dei parametri. Ad esempio, un trasformatore di parametri `SlugifyParameterTransformer` personalizzato cambia il valore di route `SubscriptionManagement` in `subscription-management`.

`RouteTokenTransformerConvention` è una convenzione del modello di applicazione che:

* Applica un trasformatore di parametri a tutte le route di attributi in un'applicazione.
* Personalizza i valori dei token delle route di attributi quando vengono sostituiti.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

La convenzione `RouteTokenTransformerConvention` è registrata come un'opzione in `ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Route multiple

Il routing con attributi supporta la definizione di più route che raggiungono la stessa azione. L'uso più comune è simulare il comportamento della *route convenzionale predefinita* come illustrato nell'esempio seguente:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Inserire più attributi di route nel controller significa che verranno tutti combinati con tutti gli attributi di route nei metodi dell'azione.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Quando più attributi di route (che implementano `IActionConstraint`) sono inseriti in un'azione, ogni vincolo dell'azione viene combinato con il modello di route dall'attributo che lo definisce.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Benché l'uso di più route per le azioni possa sembrare efficace, è preferibile mantenere lo spazio URL dell'applicazione semplice e ben definito. Usare più route per le azioni solo se necessario, ad esempio per supportare i client esistenti.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Definizione di parametri facoltativi, valori predefiniti e vincoli della route con attributi

Le route con attributi supportano la stessa sintassi inline delle route convenzionali per specificare i parametri facoltativi, i valori predefiniti e i vincoli.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Vedere [Riferimento per i modelli di route](xref:fundamentals/routing#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Attributi di route personalizzati che usano `IRouteTemplateProvider`

Tutti gli attributi di route disponibili nel framework (`[Route(...)]`, `[HttpGet(...)]` e così via) implementano l'interfaccia `IRouteTemplateProvider`. MVC cerca gli attributi per le classi del controller e i metodi delle azioni all'avvio dell'app e usa quelli che implementano `IRouteTemplateProvider` per compilare il set iniziale di route.

È possibile implementare `IRouteTemplateProvider` per definire i propri attributi di route. Ogni `IRouteTemplateProvider` consente di definire una singola route con un modello di route, un ordinamento e un nome personalizzati:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

L'attributo dell'esempio precedente imposta automaticamente `Template` su `"api/[controller]"` quando si applica `[MyApiController]`.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Uso del modello applicativo per personalizzare le route con attributi

Il *modello applicativo* è un modello a oggetti creato all'avvio con tutti i metadati usati da MVC per indirizzare ed eseguire le azioni. Il *modello applicativo* include tutti i dati raccolti da attributi di route (attraverso `IRouteTemplateProvider`). È possibile scrivere *convenzioni* per modificare il modello applicativo in fase di avvio per personalizzare il comportamento del routing. Questa sezione illustra un semplice esempio di personalizzazione del routing con il modello applicativo.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Routing misto: routing con attributi e routing convenzionale

Le applicazioni MVC sono in grado di usare una combinazione di routing convenzionale e routing con attributi. In genere le route convenzionali vengono usate per i controller che gestiscono le pagine HTML per i browser e le route con attributi per i controller che gestiscono le API REST.

Le azioni vengono indirizzate in modo convenzionale o con attributi. Se una route viene inserita nel controller o nell'azione, viene indirizzata con attributi. Le azioni che definiscono le route con attributi non possono essere raggiunte usando le route convenzionali e viceversa. **Qualsiasi** attributo di route del controller rende tutte le azioni presenti nel controller indirizzate con attributi.

> [!NOTE]
> Ciò che distingue i due tipi di sistema di routing è il processo applicato dopo che si è verificata la corrispondenza di un URL con un modello di route. Nel routing convenzionale i valori di route della corrispondenza vengono usati per scegliere l'azione e il controller da una tabella di ricerca di tutte le azioni indirizzate in modo convenzionale. Nel routing con attributi ogni modello è già associato a un'azione e non è necessaria alcuna ulteriore ricerca.

## <a name="complex-segments"></a>Segmenti complessi

I segmenti complessi (ad esempio, `[Route("/dog{token}cat")]`), vengono elaborati individuando corrispondenze per i valori letterali da destra a sinistra in modalità non-greedy. Vedere [il codice sorgente](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) per una descrizione. Per altre informazioni, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Generazione di URL

Le applicazioni MVC sono in grado di usare le funzionalità di generazione di URL del routing per generare i collegamenti URL alle azioni. La generazione di URL evita la codifica degli URL e consente di rendere il codice più affidabile e gestibile. Questa sezione illustra le funzionalità di generazione di URL disponibili in MVC e tratta solo le nozioni di base sul funzionamento della generazione di URL. Vedere [Routing](xref:fundamentals/routing) per una descrizione dettagliata della generazione di URL.

L'interfaccia `IUrlHelper` è l'elemento sottostante dell'infrastruttura tra MVC e il routing per la generazione di URL. È possibile trovare un'istanza di `IUrlHelper` disponibile usando la proprietà `Url` in controller, visualizzazioni e componenti di visualizzazione.

In questo esempio l'interfaccia `IUrlHelper` viene usata attraverso la proprietà `Controller.Url` per generare un URL a un'altra azione.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Se l'applicazione usa la route convenzionale predefinita, il valore della variabile `url` sarà la stringa del percorso URL `/UrlGeneration/Destination`. Il percorso URL viene creato dal routing combinando i valori di route della richiesta corrente (valori di ambiente) con i valori passati a `Url.Action` e sostituendo tali valori nel modello di route:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Il valore di ogni parametro di route del modello di route viene sostituito attraverso la corrispondenza dei nomi con i valori e i valori di ambiente. Per un parametro di route senza un valore si può usare un valore predefinito, se ne esiste uno, oppure ignorare il parametro se è facoltativo (come nel caso di `id` in questo esempio). La generazione di URL avrà esito negativo se un parametro di route obbligatorio non ha un valore corrispondente. Se la generazione di URL non riesce per una route, viene tentata la route successiva finché non vengono tentate tutte le route o viene trovata una corrispondenza.

Nell'esempio di `Url.Action` precedente si presuppone che il routing sia convenzionale, ma la generazione di URL funziona in modo analogo con il routing con attributi, sebbene i concetti siano differenti. Con il routing convenzionale i valori di route vengono usati per espandere un modello e i valori di route per `controller` e `action` in genere appaiono in quel modello. Questo procedimento funziona perché gli URL corrispondenti in base al routing rispettano una *convenzione*. Nel routing con attributi i valori di route per `controller` e `action` non possono essere visualizzati nel modello ma vengono usati per individuare il modello da usare.

Questo esempio usa il routing con attributi:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC compila una tabella di ricerca di tutte le azioni indirizzate con attributi e stabilisce la corrispondenza dei valori `controller` e `action` per selezionare il modello di route da usare per la generazione di URL. Nell'esempio precedente viene generato `custom/url/to/destination`.

### <a name="generating-urls-by-action-name"></a>Generazione di URL in base al nome dell'azione

`Url.Action` (`IUrlHelper` . `Action`) e tutti gli overload correlati sono basati sull'idea che si voglia definire l'oggetto a cui ci si sta collegando specificando un nome di controller e un nome di azione.

> [!NOTE]
> Quando si usa `Url.Action`, i valori di route correnti per `controller` e `action` vengono specificati automaticamente: i valori di `controller` e `action` fanno parte sia dei *valori di ambiente* **sia dei ** *valori*. Il metodo `Url.Action`, usa sempre i valori correnti di `action` e `controller` e genererà un percorso URL che indirizza all'azione corrente.

Il routing tenta di usare i valori nei valori di ambiente per ottenere le informazioni non specificate durante la generazione di un URL. Usando una route come `{a}/{b}/{c}/{d}` e i valori di ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, il routing ha informazioni sufficienti per generare un URL senza valori aggiuntivi, poiché tutti i parametri di route hanno un valore. Se è stato aggiunto il valore `{ d = Donovan }`, il valore `{ d = David }` viene ignorato e il percorso URL generato è `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> I percorsi URL sono gerarchici. Nell'esempio precedente, se è stato aggiunto il valore `{ c = Cheryl }`, entrambi i valori `{ c = Carol, d = David }` vengono ignorati. In questo caso non è più disponibile un valore per `d` e la generazione dell'URL avrà esito negativo. Sarà necessario specificare un valore per `c` e `d`.  In apparenza questo problema si può risolvere con la route predefinita (`{controller}/{action}/{id?}`), ma in realtà questo comportamento si verifica raramente perché `Url.Action` specifica sempre in modo esplicito un valore `controller` e `action`.

Anche gli overload di `Url.Action` di maggiore durata accettano anche un oggetto *valori di route* aggiuntivo per specificare i valori per i parametri di route diversi da `controller` e `action`. In genere viene usato con `id` come `Url.Action("Buy", "Products", new { id = 17 })`. Per convenzione l'oggetto *valori di route* è in genere un oggetto di tipo anonimo, ma può anche essere un `IDictionary<>` o un *normale oggetto .NET*. I valori di route aggiuntivi che non corrispondono a parametri di route vengono inseriti nella stringa di query.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Per creare un URL assoluto, usare un overload che accetta un `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Generazione di URL in base alla route

Il codice precedente illustra come generare un URL passando il nome del controller e dell'azione. `IUrlHelper` specifica anche la famiglia di metodi `Url.RouteUrl`. Questi metodi sono simili a `Url.Action`, ma non copiano i valori correnti di `action` e `controller` nei valori di route. L'uso più comune consiste nello specificare un nome di route per usare un percorso specifico per generare l'URL, in genere *senza* specificare un nome di controller o azione.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Generazione di URL in HTML

`IHtmlHelper` specifica i metodi `HtmlHelper``Html.BeginForm` e `Html.ActionLink` per generare rispettivamente gli elementi `<form>` e `<a>`. Questi metodi usano il metodo `Url.Action` per generare un URL e accettano argomenti simili. Gli oggetti `Url.RouteUrl` complementi di `HtmlHelper` sono `Html.BeginRouteForm` e `Html.RouteLink` e hanno una funzionalità simile.

Gli helper tag generano gli URL attraverso l'helper tag `form` e l'helper tag `<a>`. Entrambi usano `IUrlHelper` per la propria implementazione. Per altre informazioni, vedere [Uso dei moduli](../views/working-with-forms.md).

All'interno delle visualizzazioni `IUrlHelper` è reso disponibile dalla proprietà `Url` per tutte le generazioni di URL ad hoc che non rientrano nelle situazioni descritte in precedenza.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Generazione di URL nei risultati delle azioni

Negli esempi precedenti è stato usato `IUrlHelper` in un controller, ma l'uso più comune in un controller è generare un URL come parte del risultato di un'azione.

Le classi di base `ControllerBase` e `Controller` offrono metodi pratici per i risultati delle azioni che fanno riferimento a un'altra azione. Un uso tipico è eseguire il reindirizzamento dopo aver accettato l'input dell'utente.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

I metodi factory dei risultati delle azioni seguono un modello simile ai metodi per `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Caso speciale per le route convenzionali dedicate

Nel routing convenzionale è possibile usare un tipo speciale di definizione di route denominata *route convenzionale dedicata*. Nell'esempio seguente la route denominata `blog` è una route convenzionale dedicata.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Usando queste definizioni di route, `Url.Action("Index", "Home")` genera il percorso URL `/` con la route `default` per un motivo ben preciso. Si potrebbe pensare che i valori di route `{ controller = Home, action = Index }` siano sufficienti per generare un URL usando `blog` e che il risultato sia `/blog?action=Index&controller=Home`.

Le route convenzionali dedicate si basano su un comportamento speciale dei valori predefiniti per i quali non esiste un parametro di route corrispondente che impedisce alla route di essere troppo "greedy" con la generazione degli URL. In questo caso i valori predefiniti sono `{ controller = Blog, action = Article }` e né `controller` né `action` vengono visualizzati come parametri di route. Quando il routing esegue la generazione di URL, i valori specificati devono corrispondere ai valori predefiniti. La generazione di URL che usa `blog` avrà esito negativo perché i valori `{ controller = Home, action = Index }` non corrispondono a `{ controller = Blog, action = Article }`. Il routing quindi esegue il fallback per provare `default`, che ha esito positivo.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Aree

Le [aree](areas.md) sono una funzionalità di MVC che consente di organizzare le funzioni correlate in un gruppo come spazio dei nomi di routing separato (per le azioni del controller) e struttura di cartelle (per le visualizzazioni). L'uso delle aree consente a un'applicazione di usare più controller con lo stesso nome, purché abbiano *aree* diverse. Usando le aree si crea una gerarchia per il routing aggiungendo un altro parametro di route, `area` a `controller` e `action`. Questa sezione illustra il modo in cui il routing interagisce con le aree. Vedere [Aree](areas.md) per informazioni dettagliate sull'uso delle aree con le visualizzazioni.

Nell'esempio seguente MVC viene configurato in modo da usare la route convenzionale predefinita e una *route di area* per un'area denominata `Blog`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Nella corrispondenza di un percorso URL come `/Manage/Users/AddUser` la prima route produrrà i valori di route `{ area = Blog, controller = Users, action = AddUser }`. Il valore di route `area` è generato da un valore predefinito per `area`, infatti la route creata da `MapAreaRoute` equivale alla seguente:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` crea una route che usa sia un valore predefinito che un vincolo per `area` usando il nome di area specificato, in questo caso `Blog`. Il valore predefinito assicura che la route generi sempre `{ area = Blog, ... }`, il vincolo richiede il valore `{ area = Blog, ... }` per la generazione di URL.

> [!TIP]
> Il routing convenzionale dipende dall'ordinamento. In generale, le route con aree devono essere posizionate prima delle altre nella tabella di route poiché sono più specifiche rispetto alle route senza un'area.

Se si usa l'esempio precedente, i valori di route corrispondono all'azione seguente:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` è ciò che denota un controller come parte di un'area, questo controller in particolare si trova nell'area `Blog`. I controller senza un attributo `[Area]` non sono membri di alcuna area e **non** corrispondono quando il valore di route `area` viene specificato dal routing. Nell'esempio seguente solo il primo controller indicato può corrispondere ai valori di route `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Lo spazio dei nomi di ogni controller viene indicato qui per motivi di completezza, per evitare conflitti di denominazione nei controller e la generazione di errori del compilatore. Gli spazi dei nomi delle classi non hanno effetto sul routing di MVC.

I primi due controller sono membri di aree e corrispondono solo quando viene specificato il relativo nome di area dal valore di route `area`. Il terzo controller non è un membro di un'area e può corrispondere solo quando non vengono specificati valori per `area` dal routing.

> [!NOTE]
> In termini di corrispondenza con *nessun valore*, l'assenza del valore `area` è come se il valore per `area` fosse Null o la stringa vuota.

Quando si esegue un'azione all'interno di un'area, il valore di route per `area` sarà disponibile come *valore di ambiente* per il routing da usare per la generazione di URL. Ciò significa che per impostazione predefinita le aree funzionano *temporaneamente* per la generazione di URL, come illustrato nell'esempio seguente.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Informazioni su IActionConstraint

> [!NOTE]
> Questa sezione offre un'analisi approfondita degli elementi interni del framework e del modo in cui MVC sceglie un'azione da eseguire. Un'applicazione tipica non richiede un oggetto `IActionConstraint` personalizzato

Probabilmente l'utente ha già usato `IActionConstraint` anche se non ha familiarità con l'interfaccia. L'attributo `[HttpGet]` e gli attributi `[Http-VERB]` dello stesso tipo implementano `IActionConstraint` per limitare l'esecuzione di un metodo di azione.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Se si usa la route convenzionale predefinita, il percorso URL `/Products/Edit` produce i valori `{ controller = Products, action = Edit }`, che corrispondono a **entrambe** le azioni illustrate qui. Nella terminologia di `IActionConstraint` si dice che entrambe le azioni sono considerate candidati poiché corrispondono entrambe ai dati della route.

Quando viene eseguito `HttpGetAttribute` indica che *Edit()* è una corrispondenza per *GET* e non è una corrispondenza per qualsiasi altro verbo HTTP. L'azione `Edit(...)` non ha tutti i vincoli definiti e quindi corrisponde a qualsiasi verbo HTTP. Presupponendo l'uso di un oggetto `POST`, corrisponde solo `Edit(...)`. Per un oggetto `GET` possono invece corrispondere entrambe le azioni, tuttavia un'azione con `IActionConstraint` viene sempre considerata *migliore* rispetto a un'azione senza tale oggetto. Quindi, dal momento che `Edit()` ha `[HttpGet]` è considerata più specifica e verrà selezionata se entrambe le azioni possono corrispondere.

Concettualmente, `IActionConstraint` è una forma di *overload*, ma anziché sostituire i metodi con lo stesso nome, supporta l'overload tra le azioni che corrispondono allo stesso URL. Anche il routing con attributi usa `IActionConstraint` e può accadere che azioni di controller diversi vengano entrambe considerate candidati.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementazione di IActionConstraint

Il modo più semplice di implementare un oggetto `IActionConstraint` è creare una classe derivata da `System.Attribute` e inserirla nelle azioni e nel controller. MVC rileverà automaticamente tutti gli oggetti `IActionConstraint` applicati come attributi. È possibile usare il modello di applicazione per applicare i vincoli e questo è probabilmente l'approccio più flessibile, poiché consente di metaprogrammare le modalità di applicazione.

Nell'esempio seguente, un vincolo sceglie un'azione in base a un *codice paese* dai dati del ciclo di lavorazione. Vedere l'[esempio completo su GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

L'utente è responsabile dell'implementazione del metodo `Accept` e della scelta di un "ordine" per il vincolo da eseguire. In questo caso il metodo `Accept` restituisce `true` per indicare che l'azione è una corrispondenza quando il valore di route `country` corrisponde. Questo comportamento è diverso da quello di `RouteValueAttribute` poiché viene consentito il fallback a un'azione senza attributi. L'esempio indica che se si definisce un'azione `en-US`, per il codice paese, ad esempio `fr-FR`, viene eseguito il fallback a un controller più generico a cui non è applicato `[CountrySpecific(...)]`.

La proprietà `Order` decide a quale *fase* appartiene il vincolo. I vincoli relativi alle azioni vengono eseguiti in gruppi basati su `Order`. Ad esempio, tutti gli attributi del metodo HTTP specificati dal framework usano lo stesso valore `Order` in modo da essere eseguiti nella stessa fase. È possibile usare un numero illimitato di fasi per implementare i criteri necessari.

> [!TIP]
> Quando si sceglie un valore per `Order` considerare se il vincolo dovrà essere applicato o meno prima dei metodi HTTP. I numeri più bassi vengono eseguiti per primi.

::: moniker-end
