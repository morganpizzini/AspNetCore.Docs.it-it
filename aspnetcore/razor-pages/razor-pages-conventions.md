---
title: Convenzioni di route e app per Razor Pages in ASP.NET Core
author: rick-anderson
description: Informazioni su come le convenzioni di route e del provider di modello di app consentono di controllare routing, individuazione ed elaborazione delle pagine.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: f45e327051aba54d1cab67148eb540fb1a5cc149
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667860"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Convenzioni di route e app per Razor Pages in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Informazioni su come usare le [convenzioni di route e del provider di modello di app](xref:mvc/controllers/application-model#conventions) della pagina per controllare routing, individuazione ed elaborazione nelle app Razor Pages.

Quando è necessario configurare la route di una pagina personalizzata per le singole pagine, configurare il routing alle pagine con la [convenzione AddPageRoute](#configure-a-page-route) descritta più avanti in questo argomento.

Per specificare una route di pagina, aggiungere segmenti di percorso o `@page` aggiungere parametri a una route, utilizzare la direttiva della pagina. Per ulteriori informazioni, consultate [Route personalizzate.](xref:razor-pages/index#custom-routes)

Sono presenti parole riservate che non possono essere utilizzate come segmenti di route o nomi di parametri. Per ulteriori informazioni, vedere Routing: nomi di [routing riservati](xref:fundamentals/routing#reserved-routing-names).

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))

| Scenario | L'esempio illustra come eseguire le seguenti operazioni: |
| -------- | --------------------------- |
| [Convenzioni modello](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Aggiungere un modello e un'intestazione di route alle pagine di un'app. |
| [Convenzioni per le azioni di route di pagina](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Aggiungere un modello di route alle pagine in una cartella e a una pagina singola. |
| [Convenzioni per le azioni del modello di pagina](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe di filtro, espressione lambda o factory di filtro)</li></ul> | Aggiungere un'intestazione alle pagine in una cartella, aggiungere un'intestazione a una pagina singola e configurare una [factory di filtro](xref:mvc/controllers/filters#ifilterfactory) per aggiungere un'intestazione alle pagine di un'app. |

Le convenzioni Razor Pages vengono <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> aggiunte <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> e configurate `Startup` usando il metodo di estensione per nella raccolta di servizi nella classe. Gli esempi di convenzione seguenti sono illustrati più avanti in questo argomento:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordine ciclo di lavorazione

Le route <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> specificano un per l'elaborazione (corrispondenza route).

| JSON            | Comportamento |
| :--------------: | -------- |
| -1               | Il ciclo di lavorazione viene elaborato prima dell'elaborazione di altri cicli di lavorazione. |
| 0                | L'ordine non è specificato (valore predefinito). Se non `Order` `Order = null`si assegna ( `Order` ) il percorso predefinito è 0 (zero) per l'elaborazione. |
| 1, 2, &hellip; n | Specifica l'ordine di elaborazione del ciclo di lavorazione. |

L'elaborazione del percorso viene stabilita per convenzione:Route processing is established by convention:

* Le route vengono elaborate in ordine sequenziale (-1, 0, 1, 2, &hellip; n).
* Quando i percorsi hanno lo stesso `Order`, al percorso più specifico viene confrontato per primo, seguito da percorsi meno specifici.
* Quando le route `Order` con lo stesso numero e lo stesso numero di parametri corrispondono <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>a un URL di richiesta, le route vengono elaborate nell'ordine in cui vengono aggiunte all'oggetto .

Se possibile, evitare di dipendere da un ordine di elaborazione del ciclo di lavorazione stabilito. In genere, il routing seleziona la route corretta con corrispondenza URL. Se è necessario `Order` impostare correttamente le proprietà della route per instradare correttamente le richieste, lo schema di routing dell'app è probabilmente fonte di confusione per i client e fragile da gestire. Cerca di semplificare lo schema di routing dell'app. L'app di esempio richiede un ordine di elaborazione della route esplicito per illustrare diversi scenari di routing usando una singola app. Tuttavia, è consigliabile tentare `Order` di evitare la procedura di impostazione del percorso nelle app di produzione.

Il routing di Razor Pages e il routing del controller MVC condividono un'implementazione. Le informazioni sull'ordine delle route negli argomenti MVC sono disponibili in [Routing alle azioni del controller: Ordinamento delle route degli attributi](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenzioni del modello

Aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> delegato per aggiungere [convenzioni di modello](xref:mvc/controllers/application-model#conventions) che si applicano alle pagine Razor.Add a delegate for to add model conventions that apply to Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di route a tutte le pagine

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> e aggiungere <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> un alla raccolta di istanze che vengono applicate durante la costruzione del modello di percorso di pagina.

L'app di esempio aggiunge un modello di route `{globalTemplate?}` a tutte le pagine nell'app:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `1`. Ciò garantisce il seguente comportamento di corrispondenza delle route nell'app di esempio:This ensures the following route matching behavior in the sample app:

* Un modello `TheContactPage/{text?}` di route per viene aggiunto più avanti nell'argomento. Il percorso della pagina di `null` `Order = 0`contatto ha un `{globalTemplate?}` ordine predefinito di ( ), in modo che corrisponda prima del modello di route.
* Un `{aboutTemplate?}` modello di route viene aggiunto più avanti nell'argomento. Al modello `{aboutTemplate?}` viene assegnato un `Order` di `2`. Quando viene richiesta la pagina di informazioni in `/About/RouteDataValue`, "RouteDataValue" viene caricato in `RouteData.Values["globalTemplate"]` (`Order = 1`) e non in `RouteData.Values["aboutTemplate"]` (`Order = 2`) a causa dell'impostazione della proprietà `Order`.
* Un `{otherPagesTemplate?}` modello di route viene aggiunto più avanti nell'argomento. Al modello `{otherPagesTemplate?}` viene assegnato un `Order` di `2`. Quando una pagina nella cartella *Pages/OtherPages* viene richiesta con `/OtherPages/Page1/RouteDataValue`un parametro di route `RouteData.Values["globalTemplate"]` `Order = 1`(ad `RouteData.Values["otherPagesTemplate"]` esempio, "RouteDataValue" viene caricato in ( ) e non (`Order = 2`) a causa dell'impostazione della `Order` proprietà.

Ove possibile, non impostare il `Order` `Order = 0`metodo , che comporta . Fare affidamento sul routing per selezionare la route corretta.

Le opzioni Razor Pages, ad esempio l'aggiunta <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>di `Startup.ConfigureServices`, vengono aggiunte quando MVC viene aggiunto all'insieme di servizi in . Per un esempio completo, vedere [l'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About/GlobalRouteValue` ed esaminare il risultato:

![La pagina di informazioni viene richiesta con un segmento di route di GlobalRouteValue. La pagina sottoposta a rendering indica che il valore di dati della route viene acquisito nel metodo OnGet della pagina.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di app a tutte le pagine

Consente <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> di creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> e aggiungere <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> un alla raccolta di istanze applicate durante la costruzione del modello di app di pagina.

Per dimostrare questa e altre convenzioni più avanti in questo argomento, l'app di esempio include una classe `AddHeaderAttribute`. Il costruttore della classe accetta una stringa `name` e una matrice di stringhe `values`. Questi valori vengono usati nel relativo metodo `OnResultExecuting` per impostare un'intestazione di risposta. La classe completa è illustrata nella sezione [Convenzioni per le azioni del modello di pagina](#page-model-action-conventions) più avanti in questo argomento.

L'app di esempio usa la classe `AddHeaderAttribute` per aggiungere un'intestazione, `GlobalHeader`, a tutte le pagine nell'app:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina di informazioni indicano che è stato aggiunto l'elemento GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di gestore a tutte le pagine

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> e aggiungere <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> un alla raccolta di istanze che vengono applicate durante la costruzione del modello del gestore di pagina.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenzioni per le azioni di route di pagina

Il provider del modello di <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> route predefinito che deriva da richiama le convenzioni progettate per fornire punti di estendibilità per la configurazione delle route di pagina.

### <a name="folder-route-model-convention"></a>Convenzione per il modello di route cartella

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> e aggiungere un che <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> richiama un'azione su per tutte le pagine nella cartella specificata.

L'app di esempio usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> per aggiungere un modello di route `{otherPagesTemplate?}` alle pagine nella cartella *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `2`. In questo modo si `{globalTemplate?}` garantisce che al `1`modello per (impostato in precedenza nell'argomento a ) venga assegnata la priorità per la prima posizione del valore dei dati della route quando viene fornito un singolo valore di route. Se una pagina nella cartella *Pages/OtherPages* viene richiesta con `/OtherPages/Page1/RouteDataValue`un valore del parametro `RouteData.Values["globalTemplate"]` di`Order = 1`route `RouteData.Values["otherPagesTemplate"]` (ad esempio, "RouteDataValue" viene caricato in ( ) e non (`Order = 2`) a causa dell'impostazione della `Order` proprietà.

Ove possibile, non impostare il `Order` `Order = 0`metodo , che comporta . Fare affidamento sul routing per selezionare la route corretta.

Richiedere la pagina Page1 dell'esempio in `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ed esaminare il risultato:

![La pagina Page1 nella cartella OtherPages viene richiesta con un segmento di route di GlobalRouteValue e OtherPagesRouteValue. La pagina sottoposta a rendering indica che i valori di dati della route vengono acquisiti nel metodo OnGet della pagina.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convenzione per il modello di route pagina

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> e aggiungere un che <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> richiama un'azione nella pagina con il nome specificato.

L'app di esempio usa `AddPageRouteModelConvention` per aggiungere un modello di route `{aboutTemplate?}` alla pagina di informazioni:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `2`. In questo modo si `{globalTemplate?}` garantisce che al `1`modello per (impostato in precedenza nell'argomento a ) venga assegnata la priorità per la prima posizione del valore dei dati della route quando viene fornito un singolo valore di route. Se la pagina Informazioni su viene `/About/RouteDataValue`richiesta con un valore del `RouteData.Values["globalTemplate"]` `Order = 1`parametro `RouteData.Values["aboutTemplate"]` di`Order = 2`route in `Order` , "RouteDataValue" viene caricato in ( ) e non ( ) a causa dell'impostazione della proprietà.

Ove possibile, non impostare il `Order` `Order = 0`metodo , che comporta . Fare affidamento sul routing per selezionare la route corretta.

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ed esaminare il risultato:

![La pagina di informazioni viene richiesta con segmenti di route per GlobalRouteValue e AboutRouteValue. La pagina sottoposta a rendering indica che i valori di dati della route vengono acquisiti nel metodo OnGet della pagina.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Utilizzare un trasformatore di parametri per personalizzare le route di paginaUse a parameter transformer to customize page routes

Le route delle pagine generate da ASP.NET Core possono essere personalizzate utilizzando un trasformatore di parametri. Un trasformatore di parametri implementa `IOutboundParameterTransformer` e trasforma il valore dei parametri. Ad esempio, un trasformatore di parametri `SlugifyParameterTransformer` personalizzato cambia il valore di route `SubscriptionManagement` in `subscription-management`.

La `PageRouteTransformerConvention` convenzione del modello di route di pagina applica un trasformatore di parametri ai segmenti di cartella e nome file delle route di pagina generate automaticamente in un'app. Ad esempio, il file Razor Pages in */Pages/SubscriptionManagement/ViewAll.cshtml* la route verrà riscritta da `/SubscriptionManagement/ViewAll` a `/subscription-management/view-all`.

`PageRouteTransformerConvention`trasforma solo i segmenti generati automaticamente di un percorso di pagina che provengono dalla cartella Razor Pages e dal nome del file. Non trasforma i segmenti di route `@page` aggiunti con la direttiva. La convenzione inoltre non trasforma <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>le route aggiunte da .

Il `PageRouteTransformerConvention` è registrato come `Startup.ConfigureServices`opzione in :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
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

## <a name="configure-a-page-route"></a>Configurare una route di pagina

Consente <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> di configurare una route a una pagina nel percorso di pagina specificato. I collegamenti generati alla pagina usano la route specificata. `AddPageRoute` usa `AddPageRouteModelConvention` per stabilire la route.

L'app di esempio crea una route a `/TheContactPage` per *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

La pagina di contatto può anche essere raggiunta in corrispondenza di `/Contact` tramite la route predefinita.

La route personalizzata dell'app di esempio alla pagina di contatto consente un segmento di route `text` facoltativo (`{text?}`). La pagina include anche tale segmento facoltativo nella relativa istruzione `@page` nel caso in cui il visitatore acceda alla pagina in corrispondenza della relativa route `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Si noti che l'URL generato per il collegamento **Contatto** nella pagina sottoposta a rendering riflette la route aggiornata:

![Collegamento alla pagina di contatto dell'app di esempio nella barra di spostamento](razor-pages-conventions/_static/contact-link.png)

![L'esame del collegamento alla pagina di contatto nell'HTML sottoposto a rendering indica che href è impostato su '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

Visitare la pagina di contatto nella route normale, `/Contact`, o nella route personalizzata `/TheContactPage`. Se si specifica un segmento di route `text` aggiuntivo, la pagina visualizza il segmento codificato in formato HTML specificato dall'utente:

![Esempio di browser Microsoft Edge in cui viene specificato un segmento di route facoltativo 'text' corrispondente a 'TextValue' nell'URL. La pagina sottoposta a rendering visualizza il valore del segmento 'text'.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Convenzioni per le azioni del modello di pagina

Il provider del modello <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> di pagina predefinito che implementa richiama le convenzioni progettate per fornire punti di estendibilità per la configurazione dei modelli di pagina. Queste convenzioni sono utili durante la compilazione e la modifica degli scenari di individuazione ed elaborazione delle pagine.

Per gli esempi in questa sezione, `AddHeaderAttribute` l'app <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>di esempio usa una classe, ovvero un oggetto , che applica un'intestazione di risposta:For the examples in this section, the sample app uses an class, which is a , that applies a response header:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Tramite le convenzioni, nell'esempio viene illustrato come applicare l'attributo a tutte le pagine in una cartella e a una singola pagina.

**Convenzione per il modello di app cartella**

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> che richiama un'azione sulle istanze per tutte le pagine nella cartella specificata.

Nell'esempio viene illustrato l'uso di `AddFolderApplicationModelConvention` aggiungendo un'intestazione, `OtherPagesHeader`, alle pagine all'interno della cartella *OtherPages* dell'app:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Richiedere la pagina Page1 dell'esempio in `localhost:5000/OtherPages/Page1` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina OtherPages/Page1 indicano che è stato aggiunto l'elemento OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convenzione per il modello di app cartella**

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> e aggiungere un che <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> richiama un'azione nella pagina con il nome specificato.

Nell'esempio viene illustrato l'uso di `AddPageApplicationModelConvention` aggiungendo un'intestazione, `AboutHeader`, alla pagina di informazioni:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina di informazioni indicano che è stato aggiunto l'elemento AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurare un filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>configura il filtro specificato da applicare. È possibile implementare una classe di filtro, ma l'app di esempio illustra come implementare un filtro in un'espressione lambda, che viene implementata in background come una factory che restituisce un filtro:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

Viene usato il modello di app della pagina per verificare il percorso relativo per i segmenti che portano alla pagina Page2 nella cartella *OtherPages*. Se la condizione viene superata, viene aggiunta un'intestazione. In caso contrario, viene applicato `EmptyFilter`.

`EmptyFilter` è un [filtro azione](xref:mvc/controllers/filters#action-filters). Poiché i filtri Azione vengono `EmptyFilter` ignorati da Pagine Razor, non `OtherPages/Page2`ha alcun effetto come previsto se il percorso non contiene .

Richiedere la pagina Page2 dell'esempio in `localhost:5000/OtherPages/Page2` ed esaminare le intestazioni per visualizzare il risultato:

![L'elemento OtherPagesPage2Header viene aggiunto alla risposta per Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurare una factory di filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>configura la factory specificata per applicare [filtri](xref:mvc/controllers/filters) a tutte le pagine Razor.

L'app di esempio illustra un esempio dell'uso di una [factory di filtro](xref:mvc/controllers/filters#ifilterfactory) aggiungendo un'intestazione, `FilterFactoryHeader`, con due valori per le pagine dell'app:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina informazioni indicano che sono state aggiunte due intestazioni FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtri MVC e il filtro di pagina (IPageFilter)

I [filtri azione](xref:mvc/controllers/filters#action-filters) MVC vengono ignorati da Razor Pages, poiché vengono usati i metodi del gestore. Sono disponibili altri tipi di filtri MVC: [autorizzazione](xref:mvc/controllers/filters#authorization-filters), [eccezione](xref:mvc/controllers/filters#exception-filters), [risorse](xref:mvc/controllers/filters#resource-filters) e [risultati](xref:mvc/controllers/filters#result-filters). Per altre informazioni, vedere l'argomento [Filtri](xref:mvc/controllers/filters).

Il filtro<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>Pagina ( ) è un filtro che si applica alle pagine Razor. Per altre informazioni, vedere [Modalità di filtro per pagine Razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Informazioni su come usare le [convenzioni di route e del provider di modello di app](xref:mvc/controllers/application-model#conventions) della pagina per controllare routing, individuazione ed elaborazione nelle app Razor Pages.

Quando è necessario configurare la route di una pagina personalizzata per le singole pagine, configurare il routing alle pagine con la [convenzione AddPageRoute](#configure-a-page-route) descritta più avanti in questo argomento.

Per specificare una route di pagina, aggiungere segmenti di percorso o `@page` aggiungere parametri a una route, utilizzare la direttiva della pagina. Per ulteriori informazioni, consultate [Route personalizzate.](xref:razor-pages/index#custom-routes)

Sono presenti parole riservate che non possono essere utilizzate come segmenti di route o nomi di parametri. Per ulteriori informazioni, vedere Routing: nomi di [routing riservati](xref:fundamentals/routing#reserved-routing-names).

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))

| Scenario | L'esempio illustra come eseguire le seguenti operazioni: |
| -------- | --------------------------- |
| [Convenzioni modello](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Aggiungere un modello e un'intestazione di route alle pagine di un'app. |
| [Convenzioni per le azioni di route di pagina](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Aggiungere un modello di route alle pagine in una cartella e a una pagina singola. |
| [Convenzioni per le azioni del modello di pagina](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe di filtro, espressione lambda o factory di filtro)</li></ul> | Aggiungere un'intestazione alle pagine in una cartella, aggiungere un'intestazione a una pagina singola e configurare una [factory di filtro](xref:mvc/controllers/filters#ifilterfactory) per aggiungere un'intestazione alle pagine di un'app. |

Le convenzioni Razor Pages vengono <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> aggiunte <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> e configurate `Startup` usando il metodo di estensione per nella raccolta di servizi nella classe. Gli esempi di convenzione seguenti sono illustrati più avanti in questo argomento:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordine ciclo di lavorazione

Le route <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> specificano un per l'elaborazione (corrispondenza route).

| JSON            | Comportamento |
| :--------------: | -------- |
| -1               | Il ciclo di lavorazione viene elaborato prima dell'elaborazione di altri cicli di lavorazione. |
| 0                | L'ordine non è specificato (valore predefinito). Se non `Order` `Order = null`si assegna ( `Order` ) il percorso predefinito è 0 (zero) per l'elaborazione. |
| 1, 2, &hellip; n | Specifica l'ordine di elaborazione del ciclo di lavorazione. |

L'elaborazione del percorso viene stabilita per convenzione:Route processing is established by convention:

* Le route vengono elaborate in ordine sequenziale (-1, 0, 1, 2, &hellip; n).
* Quando i percorsi hanno lo stesso `Order`, al percorso più specifico viene confrontato per primo, seguito da percorsi meno specifici.
* Quando le route `Order` con lo stesso numero e lo stesso numero di parametri corrispondono <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>a un URL di richiesta, le route vengono elaborate nell'ordine in cui vengono aggiunte all'oggetto .

Se possibile, evitare di dipendere da un ordine di elaborazione del ciclo di lavorazione stabilito. In genere, il routing seleziona la route corretta con corrispondenza URL. Se è necessario `Order` impostare correttamente le proprietà della route per instradare correttamente le richieste, lo schema di routing dell'app è probabilmente fonte di confusione per i client e fragile da gestire. Cerca di semplificare lo schema di routing dell'app. L'app di esempio richiede un ordine di elaborazione della route esplicito per illustrare diversi scenari di routing usando una singola app. Tuttavia, è consigliabile tentare `Order` di evitare la procedura di impostazione del percorso nelle app di produzione.

Il routing di Razor Pages e il routing del controller MVC condividono un'implementazione. Le informazioni sull'ordine delle route negli argomenti MVC sono disponibili in [Routing alle azioni del controller: Ordinamento delle route degli attributi](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenzioni del modello

Aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> delegato per aggiungere [convenzioni di modello](xref:mvc/controllers/application-model#conventions) che si applicano alle pagine Razor.Add a delegate for to add model conventions that apply to Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di route a tutte le pagine

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> e aggiungere <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> un alla raccolta di istanze che vengono applicate durante la costruzione del modello di percorso di pagina.

L'app di esempio aggiunge un modello di route `{globalTemplate?}` a tutte le pagine nell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `1`. Ciò garantisce il seguente comportamento di corrispondenza delle route nell'app di esempio:This ensures the following route matching behavior in the sample app:

* Un modello `TheContactPage/{text?}` di route per viene aggiunto più avanti nell'argomento. Il percorso della pagina di `null` `Order = 0`contatto ha un `{globalTemplate?}` ordine predefinito di ( ), in modo che corrisponda prima del modello di route.
* Un `{aboutTemplate?}` modello di route viene aggiunto più avanti nell'argomento. Al modello `{aboutTemplate?}` viene assegnato un `Order` di `2`. Quando viene richiesta la pagina di informazioni in `/About/RouteDataValue`, "RouteDataValue" viene caricato in `RouteData.Values["globalTemplate"]` (`Order = 1`) e non in `RouteData.Values["aboutTemplate"]` (`Order = 2`) a causa dell'impostazione della proprietà `Order`.
* Un `{otherPagesTemplate?}` modello di route viene aggiunto più avanti nell'argomento. Al modello `{otherPagesTemplate?}` viene assegnato un `Order` di `2`. Quando una pagina nella cartella *Pages/OtherPages* viene richiesta con `/OtherPages/Page1/RouteDataValue`un parametro di route `RouteData.Values["globalTemplate"]` `Order = 1`(ad `RouteData.Values["otherPagesTemplate"]` esempio, "RouteDataValue" viene caricato in ( ) e non (`Order = 2`) a causa dell'impostazione della `Order` proprietà.

Ove possibile, non impostare il `Order` `Order = 0`metodo , che comporta . Fare affidamento sul routing per selezionare la route corretta.

Le opzioni Razor Pages, ad esempio l'aggiunta <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>di `Startup.ConfigureServices`, vengono aggiunte quando MVC viene aggiunto all'insieme di servizi in . Per un esempio completo, vedere [l'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About/GlobalRouteValue` ed esaminare il risultato:

![La pagina di informazioni viene richiesta con un segmento di route di GlobalRouteValue. La pagina sottoposta a rendering indica che il valore di dati della route viene acquisito nel metodo OnGet della pagina.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di app a tutte le pagine

Consente <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> di creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> e aggiungere <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> un alla raccolta di istanze applicate durante la costruzione del modello di app di pagina.

Per dimostrare questa e altre convenzioni più avanti in questo argomento, l'app di esempio include una classe `AddHeaderAttribute`. Il costruttore della classe accetta una stringa `name` e una matrice di stringhe `values`. Questi valori vengono usati nel relativo metodo `OnResultExecuting` per impostare un'intestazione di risposta. La classe completa è illustrata nella sezione [Convenzioni per le azioni del modello di pagina](#page-model-action-conventions) più avanti in questo argomento.

L'app di esempio usa la classe `AddHeaderAttribute` per aggiungere un'intestazione, `GlobalHeader`, a tutte le pagine nell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina di informazioni indicano che è stato aggiunto l'elemento GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di gestore a tutte le pagine

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> e aggiungere <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> un alla raccolta di istanze che vengono applicate durante la costruzione del modello del gestore di pagina.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenzioni per le azioni di route di pagina

Il provider del modello di <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> route predefinito che deriva da richiama le convenzioni progettate per fornire punti di estendibilità per la configurazione delle route di pagina.

### <a name="folder-route-model-convention"></a>Convenzione per il modello di route cartella

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> e aggiungere un che <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> richiama un'azione su per tutte le pagine nella cartella specificata.

L'app di esempio usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> per aggiungere un modello di route `{otherPagesTemplate?}` alle pagine nella cartella *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `2`. In questo modo si `{globalTemplate?}` garantisce che al `1`modello per (impostato in precedenza nell'argomento a ) venga assegnata la priorità per la prima posizione del valore dei dati della route quando viene fornito un singolo valore di route. Se una pagina nella cartella *Pages/OtherPages* viene richiesta con `/OtherPages/Page1/RouteDataValue`un valore del parametro `RouteData.Values["globalTemplate"]` di`Order = 1`route `RouteData.Values["otherPagesTemplate"]` (ad esempio, "RouteDataValue" viene caricato in ( ) e non (`Order = 2`) a causa dell'impostazione della `Order` proprietà.

Ove possibile, non impostare il `Order` `Order = 0`metodo , che comporta . Fare affidamento sul routing per selezionare la route corretta.

Richiedere la pagina Page1 dell'esempio in `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ed esaminare il risultato:

![La pagina Page1 nella cartella OtherPages viene richiesta con un segmento di route di GlobalRouteValue e OtherPagesRouteValue. La pagina sottoposta a rendering indica che i valori di dati della route vengono acquisiti nel metodo OnGet della pagina.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convenzione per il modello di route pagina

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> e aggiungere un che <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> richiama un'azione nella pagina con il nome specificato.

L'app di esempio usa `AddPageRouteModelConvention` per aggiungere un modello di route `{aboutTemplate?}` alla pagina di informazioni:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `2`. In questo modo si `{globalTemplate?}` garantisce che al `1`modello per (impostato in precedenza nell'argomento a ) venga assegnata la priorità per la prima posizione del valore dei dati della route quando viene fornito un singolo valore di route. Se la pagina Informazioni su viene `/About/RouteDataValue`richiesta con un valore del `RouteData.Values["globalTemplate"]` `Order = 1`parametro `RouteData.Values["aboutTemplate"]` di`Order = 2`route in `Order` , "RouteDataValue" viene caricato in ( ) e non ( ) a causa dell'impostazione della proprietà.

Ove possibile, non impostare il `Order` `Order = 0`metodo , che comporta . Fare affidamento sul routing per selezionare la route corretta.

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ed esaminare il risultato:

![La pagina di informazioni viene richiesta con segmenti di route per GlobalRouteValue e AboutRouteValue. La pagina sottoposta a rendering indica che i valori di dati della route vengono acquisiti nel metodo OnGet della pagina.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Utilizzare un trasformatore di parametri per personalizzare le route di paginaUse a parameter transformer to customize page routes

Le route delle pagine generate da ASP.NET Core possono essere personalizzate utilizzando un trasformatore di parametri. Un trasformatore di parametri implementa `IOutboundParameterTransformer` e trasforma il valore dei parametri. Ad esempio, un trasformatore di parametri `SlugifyParameterTransformer` personalizzato cambia il valore di route `SubscriptionManagement` in `subscription-management`.

La `PageRouteTransformerConvention` convenzione del modello di route di pagina applica un trasformatore di parametri ai segmenti di cartella e nome file delle route di pagina generate automaticamente in un'app. Ad esempio, il file Razor Pages in */Pages/SubscriptionManagement/ViewAll.cshtml* la route verrà riscritta da `/SubscriptionManagement/ViewAll` a `/subscription-management/view-all`.

`PageRouteTransformerConvention`trasforma solo i segmenti generati automaticamente di un percorso di pagina che provengono dalla cartella Razor Pages e dal nome del file. Non trasforma i segmenti di route `@page` aggiunti con la direttiva. La convenzione inoltre non trasforma <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>le route aggiunte da .

Il `PageRouteTransformerConvention` è registrato come `Startup.ConfigureServices`opzione in :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
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

## <a name="configure-a-page-route"></a>Configurare una route di pagina

Consente <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> di configurare una route a una pagina nel percorso di pagina specificato. I collegamenti generati alla pagina usano la route specificata. `AddPageRoute` usa `AddPageRouteModelConvention` per stabilire la route.

L'app di esempio crea una route a `/TheContactPage` per *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

La pagina di contatto può anche essere raggiunta in corrispondenza di `/Contact` tramite la route predefinita.

La route personalizzata dell'app di esempio alla pagina di contatto consente un segmento di route `text` facoltativo (`{text?}`). La pagina include anche tale segmento facoltativo nella relativa istruzione `@page` nel caso in cui il visitatore acceda alla pagina in corrispondenza della relativa route `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Si noti che l'URL generato per il collegamento **Contatto** nella pagina sottoposta a rendering riflette la route aggiornata:

![Collegamento alla pagina di contatto dell'app di esempio nella barra di spostamento](razor-pages-conventions/_static/contact-link.png)

![L'esame del collegamento alla pagina di contatto nell'HTML sottoposto a rendering indica che href è impostato su '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

Visitare la pagina di contatto nella route normale, `/Contact`, o nella route personalizzata `/TheContactPage`. Se si specifica un segmento di route `text` aggiuntivo, la pagina visualizza il segmento codificato in formato HTML specificato dall'utente:

![Esempio di browser Microsoft Edge in cui viene specificato un segmento di route facoltativo 'text' corrispondente a 'TextValue' nell'URL. La pagina sottoposta a rendering visualizza il valore del segmento 'text'.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Convenzioni per le azioni del modello di pagina

Il provider del modello <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> di pagina predefinito che implementa richiama le convenzioni progettate per fornire punti di estendibilità per la configurazione dei modelli di pagina. Queste convenzioni sono utili durante la compilazione e la modifica degli scenari di individuazione ed elaborazione delle pagine.

Per gli esempi in questa sezione, `AddHeaderAttribute` l'app <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>di esempio usa una classe, ovvero un oggetto , che applica un'intestazione di risposta:For the examples in this section, the sample app uses an class, which is a , that applies a response header:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Tramite le convenzioni, nell'esempio viene illustrato come applicare l'attributo a tutte le pagine in una cartella e a una singola pagina.

**Convenzione per il modello di app cartella**

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> che richiama un'azione sulle istanze per tutte le pagine nella cartella specificata.

Nell'esempio viene illustrato l'uso di `AddFolderApplicationModelConvention` aggiungendo un'intestazione, `OtherPagesHeader`, alle pagine all'interno della cartella *OtherPages* dell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Richiedere la pagina Page1 dell'esempio in `localhost:5000/OtherPages/Page1` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina OtherPages/Page1 indicano che è stato aggiunto l'elemento OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convenzione per il modello di app cartella**

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> e aggiungere un che <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> richiama un'azione nella pagina con il nome specificato.

Nell'esempio viene illustrato l'uso di `AddPageApplicationModelConvention` aggiungendo un'intestazione, `AboutHeader`, alla pagina di informazioni:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina di informazioni indicano che è stato aggiunto l'elemento AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurare un filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>configura il filtro specificato da applicare. È possibile implementare una classe di filtro, ma l'app di esempio illustra come implementare un filtro in un'espressione lambda, che viene implementata in background come una factory che restituisce un filtro:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Viene usato il modello di app della pagina per verificare il percorso relativo per i segmenti che portano alla pagina Page2 nella cartella *OtherPages*. Se la condizione viene superata, viene aggiunta un'intestazione. In caso contrario, viene applicato `EmptyFilter`.

`EmptyFilter` è un [filtro azione](xref:mvc/controllers/filters#action-filters). Poiché i filtri Azione vengono `EmptyFilter` ignorati da Pagine Razor, non `OtherPages/Page2`ha alcun effetto come previsto se il percorso non contiene .

Richiedere la pagina Page2 dell'esempio in `localhost:5000/OtherPages/Page2` ed esaminare le intestazioni per visualizzare il risultato:

![L'elemento OtherPagesPage2Header viene aggiunto alla risposta per Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurare una factory di filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>configura la factory specificata per applicare [filtri](xref:mvc/controllers/filters) a tutte le pagine Razor.

L'app di esempio illustra un esempio dell'uso di una [factory di filtro](xref:mvc/controllers/filters#ifilterfactory) aggiungendo un'intestazione, `FilterFactoryHeader`, con due valori per le pagine dell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina informazioni indicano che sono state aggiunte due intestazioni FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtri MVC e il filtro di pagina (IPageFilter)

I [filtri azione](xref:mvc/controllers/filters#action-filters) MVC vengono ignorati da Razor Pages, poiché vengono usati i metodi del gestore. Sono disponibili altri tipi di filtri MVC: [autorizzazione](xref:mvc/controllers/filters#authorization-filters), [eccezione](xref:mvc/controllers/filters#exception-filters), [risorse](xref:mvc/controllers/filters#resource-filters) e [risultati](xref:mvc/controllers/filters#result-filters). Per altre informazioni, vedere l'argomento [Filtri](xref:mvc/controllers/filters).

Il filtro<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>Pagina ( ) è un filtro che si applica alle pagine Razor. Per altre informazioni, vedere [Modalità di filtro per pagine Razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Informazioni su come usare le [convenzioni di route e del provider di modello di app](xref:mvc/controllers/application-model#conventions) della pagina per controllare routing, individuazione ed elaborazione nelle app Razor Pages.

Quando è necessario configurare la route di una pagina personalizzata per le singole pagine, configurare il routing alle pagine con la [convenzione AddPageRoute](#configure-a-page-route) descritta più avanti in questo argomento.

Per specificare una route di pagina, aggiungere segmenti di percorso o `@page` aggiungere parametri a una route, utilizzare la direttiva della pagina. Per ulteriori informazioni, consultate [Route personalizzate.](xref:razor-pages/index#custom-routes)

Sono presenti parole riservate che non possono essere utilizzate come segmenti di route o nomi di parametri. Per ulteriori informazioni, vedere Routing: nomi di [routing riservati](xref:fundamentals/routing#reserved-routing-names).

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))

| Scenario | L'esempio illustra come eseguire le seguenti operazioni: |
| -------- | --------------------------- |
| [Convenzioni modello](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Aggiungere un modello e un'intestazione di route alle pagine di un'app. |
| [Convenzioni per le azioni di route di pagina](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Aggiungere un modello di route alle pagine in una cartella e a una pagina singola. |
| [Convenzioni per le azioni del modello di pagina](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe di filtro, espressione lambda o factory di filtro)</li></ul> | Aggiungere un'intestazione alle pagine in una cartella, aggiungere un'intestazione a una pagina singola e configurare una [factory di filtro](xref:mvc/controllers/filters#ifilterfactory) per aggiungere un'intestazione alle pagine di un'app. |

Le convenzioni Razor Pages vengono <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> aggiunte <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> e configurate `Startup` usando il metodo di estensione per nella raccolta di servizi nella classe. Gli esempi di convenzione seguenti sono illustrati più avanti in questo argomento:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordine ciclo di lavorazione

Le route <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> specificano un per l'elaborazione (corrispondenza route).

| JSON            | Comportamento |
| :--------------: | -------- |
| -1               | Il ciclo di lavorazione viene elaborato prima dell'elaborazione di altri cicli di lavorazione. |
| 0                | L'ordine non è specificato (valore predefinito). Se non `Order` `Order = null`si assegna ( `Order` ) il percorso predefinito è 0 (zero) per l'elaborazione. |
| 1, 2, &hellip; n | Specifica l'ordine di elaborazione del ciclo di lavorazione. |

L'elaborazione del percorso viene stabilita per convenzione:Route processing is established by convention:

* Le route vengono elaborate in ordine sequenziale (-1, 0, 1, 2, &hellip; n).
* Quando i percorsi hanno lo stesso `Order`, al percorso più specifico viene confrontato per primo, seguito da percorsi meno specifici.
* Quando le route `Order` con lo stesso numero e lo stesso numero di parametri corrispondono <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>a un URL di richiesta, le route vengono elaborate nell'ordine in cui vengono aggiunte all'oggetto .

Se possibile, evitare di dipendere da un ordine di elaborazione del ciclo di lavorazione stabilito. In genere, il routing seleziona la route corretta con corrispondenza URL. Se è necessario `Order` impostare correttamente le proprietà della route per instradare correttamente le richieste, lo schema di routing dell'app è probabilmente fonte di confusione per i client e fragile da gestire. Cerca di semplificare lo schema di routing dell'app. L'app di esempio richiede un ordine di elaborazione della route esplicito per illustrare diversi scenari di routing usando una singola app. Tuttavia, è consigliabile tentare `Order` di evitare la procedura di impostazione del percorso nelle app di produzione.

Il routing di Razor Pages e il routing del controller MVC condividono un'implementazione. Le informazioni sull'ordine delle route negli argomenti MVC sono disponibili in [Routing alle azioni del controller: Ordinamento delle route degli attributi](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenzioni del modello

Aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> delegato per aggiungere [convenzioni di modello](xref:mvc/controllers/application-model#conventions) che si applicano alle pagine Razor.Add a delegate for to add model conventions that apply to Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di route a tutte le pagine

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> e aggiungere <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> un alla raccolta di istanze che vengono applicate durante la costruzione del modello di percorso di pagina.

L'app di esempio aggiunge un modello di route `{globalTemplate?}` a tutte le pagine nell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `1`. Ciò garantisce il seguente comportamento di corrispondenza delle route nell'app di esempio:This ensures the following route matching behavior in the sample app:

* Un modello `TheContactPage/{text?}` di route per viene aggiunto più avanti nell'argomento. Il percorso della pagina di `null` `Order = 0`contatto ha un `{globalTemplate?}` ordine predefinito di ( ), in modo che corrisponda prima del modello di route.
* Un `{aboutTemplate?}` modello di route viene aggiunto più avanti nell'argomento. Al modello `{aboutTemplate?}` viene assegnato un `Order` di `2`. Quando viene richiesta la pagina di informazioni in `/About/RouteDataValue`, "RouteDataValue" viene caricato in `RouteData.Values["globalTemplate"]` (`Order = 1`) e non in `RouteData.Values["aboutTemplate"]` (`Order = 2`) a causa dell'impostazione della proprietà `Order`.
* Un `{otherPagesTemplate?}` modello di route viene aggiunto più avanti nell'argomento. Al modello `{otherPagesTemplate?}` viene assegnato un `Order` di `2`. Quando una pagina nella cartella *Pages/OtherPages* viene richiesta con `/OtherPages/Page1/RouteDataValue`un parametro di route `RouteData.Values["globalTemplate"]` `Order = 1`(ad `RouteData.Values["otherPagesTemplate"]` esempio, "RouteDataValue" viene caricato in ( ) e non (`Order = 2`) a causa dell'impostazione della `Order` proprietà.

Ove possibile, non impostare il `Order` `Order = 0`metodo , che comporta . Fare affidamento sul routing per selezionare la route corretta.

Le opzioni Razor Pages, ad esempio l'aggiunta <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>di `Startup.ConfigureServices`, vengono aggiunte quando MVC viene aggiunto all'insieme di servizi in . Per un esempio completo, vedere [l'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About/GlobalRouteValue` ed esaminare il risultato:

![La pagina di informazioni viene richiesta con un segmento di route di GlobalRouteValue. La pagina sottoposta a rendering indica che il valore di dati della route viene acquisito nel metodo OnGet della pagina.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di app a tutte le pagine

Consente <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> di creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> e aggiungere <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> un alla raccolta di istanze applicate durante la costruzione del modello di app di pagina.

Per dimostrare questa e altre convenzioni più avanti in questo argomento, l'app di esempio include una classe `AddHeaderAttribute`. Il costruttore della classe accetta una stringa `name` e una matrice di stringhe `values`. Questi valori vengono usati nel relativo metodo `OnResultExecuting` per impostare un'intestazione di risposta. La classe completa è illustrata nella sezione [Convenzioni per le azioni del modello di pagina](#page-model-action-conventions) più avanti in questo argomento.

L'app di esempio usa la classe `AddHeaderAttribute` per aggiungere un'intestazione, `GlobalHeader`, a tutte le pagine nell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina di informazioni indicano che è stato aggiunto l'elemento GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di gestore a tutte le pagine

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> e aggiungere <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> un alla raccolta di istanze che vengono applicate durante la costruzione del modello del gestore di pagina.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenzioni per le azioni di route di pagina

Il provider del modello di <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> route predefinito che deriva da richiama le convenzioni progettate per fornire punti di estendibilità per la configurazione delle route di pagina.

### <a name="folder-route-model-convention"></a>Convenzione per il modello di route cartella

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> e aggiungere un che <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> richiama un'azione su per tutte le pagine nella cartella specificata.

L'app di esempio usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> per aggiungere un modello di route `{otherPagesTemplate?}` alle pagine nella cartella *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `2`. In questo modo si `{globalTemplate?}` garantisce che al `1`modello per (impostato in precedenza nell'argomento a ) venga assegnata la priorità per la prima posizione del valore dei dati della route quando viene fornito un singolo valore di route. Se una pagina nella cartella *Pages/OtherPages* viene richiesta con `/OtherPages/Page1/RouteDataValue`un valore del parametro `RouteData.Values["globalTemplate"]` di`Order = 1`route `RouteData.Values["otherPagesTemplate"]` (ad esempio, "RouteDataValue" viene caricato in ( ) e non (`Order = 2`) a causa dell'impostazione della `Order` proprietà.

Ove possibile, non impostare il `Order` `Order = 0`metodo , che comporta . Fare affidamento sul routing per selezionare la route corretta.

Richiedere la pagina Page1 dell'esempio in `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ed esaminare il risultato:

![La pagina Page1 nella cartella OtherPages viene richiesta con un segmento di route di GlobalRouteValue e OtherPagesRouteValue. La pagina sottoposta a rendering indica che i valori di dati della route vengono acquisiti nel metodo OnGet della pagina.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convenzione per il modello di route pagina

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> e aggiungere un che <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> richiama un'azione nella pagina con il nome specificato.

L'app di esempio usa `AddPageRouteModelConvention` per aggiungere un modello di route `{aboutTemplate?}` alla pagina di informazioni:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `2`. In questo modo si `{globalTemplate?}` garantisce che al `1`modello per (impostato in precedenza nell'argomento a ) venga assegnata la priorità per la prima posizione del valore dei dati della route quando viene fornito un singolo valore di route. Se la pagina Informazioni su viene `/About/RouteDataValue`richiesta con un valore del `RouteData.Values["globalTemplate"]` `Order = 1`parametro `RouteData.Values["aboutTemplate"]` di`Order = 2`route in `Order` , "RouteDataValue" viene caricato in ( ) e non ( ) a causa dell'impostazione della proprietà.

Ove possibile, non impostare il `Order` `Order = 0`metodo , che comporta . Fare affidamento sul routing per selezionare la route corretta.

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ed esaminare il risultato:

![La pagina di informazioni viene richiesta con segmenti di route per GlobalRouteValue e AboutRouteValue. La pagina sottoposta a rendering indica che i valori di dati della route vengono acquisiti nel metodo OnGet della pagina.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Configurare una route di pagina

Consente <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> di configurare una route a una pagina nel percorso di pagina specificato. I collegamenti generati alla pagina usano la route specificata. `AddPageRoute` usa `AddPageRouteModelConvention` per stabilire la route.

L'app di esempio crea una route a `/TheContactPage` per *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

La pagina di contatto può anche essere raggiunta in corrispondenza di `/Contact` tramite la route predefinita.

La route personalizzata dell'app di esempio alla pagina di contatto consente un segmento di route `text` facoltativo (`{text?}`). La pagina include anche tale segmento facoltativo nella relativa istruzione `@page` nel caso in cui il visitatore acceda alla pagina in corrispondenza della relativa route `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Si noti che l'URL generato per il collegamento **Contatto** nella pagina sottoposta a rendering riflette la route aggiornata:

![Collegamento alla pagina di contatto dell'app di esempio nella barra di spostamento](razor-pages-conventions/_static/contact-link.png)

![L'esame del collegamento alla pagina di contatto nell'HTML sottoposto a rendering indica che href è impostato su '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

Visitare la pagina di contatto nella route normale, `/Contact`, o nella route personalizzata `/TheContactPage`. Se si specifica un segmento di route `text` aggiuntivo, la pagina visualizza il segmento codificato in formato HTML specificato dall'utente:

![Esempio di browser Microsoft Edge in cui viene specificato un segmento di route facoltativo 'text' corrispondente a 'TextValue' nell'URL. La pagina sottoposta a rendering visualizza il valore del segmento 'text'.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Convenzioni per le azioni del modello di pagina

Il provider del modello <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> di pagina predefinito che implementa richiama le convenzioni progettate per fornire punti di estendibilità per la configurazione dei modelli di pagina. Queste convenzioni sono utili durante la compilazione e la modifica degli scenari di individuazione ed elaborazione delle pagine.

Per gli esempi in questa sezione, `AddHeaderAttribute` l'app <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>di esempio usa una classe, ovvero un oggetto , che applica un'intestazione di risposta:For the examples in this section, the sample app uses an class, which is a , that applies a response header:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Tramite le convenzioni, nell'esempio viene illustrato come applicare l'attributo a tutte le pagine in una cartella e a una singola pagina.

**Convenzione per il modello di app cartella**

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> che richiama un'azione sulle istanze per tutte le pagine nella cartella specificata.

Nell'esempio viene illustrato l'uso di `AddFolderApplicationModelConvention` aggiungendo un'intestazione, `OtherPagesHeader`, alle pagine all'interno della cartella *OtherPages* dell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Richiedere la pagina Page1 dell'esempio in `localhost:5000/OtherPages/Page1` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina OtherPages/Page1 indicano che è stato aggiunto l'elemento OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convenzione per il modello di app cartella**

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> per creare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> e aggiungere un che <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> richiama un'azione nella pagina con il nome specificato.

Nell'esempio viene illustrato l'uso di `AddPageApplicationModelConvention` aggiungendo un'intestazione, `AboutHeader`, alla pagina di informazioni:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina di informazioni indicano che è stato aggiunto l'elemento AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurare un filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>configura il filtro specificato da applicare. È possibile implementare una classe di filtro, ma l'app di esempio illustra come implementare un filtro in un'espressione lambda, che viene implementata in background come una factory che restituisce un filtro:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Viene usato il modello di app della pagina per verificare il percorso relativo per i segmenti che portano alla pagina Page2 nella cartella *OtherPages*. Se la condizione viene superata, viene aggiunta un'intestazione. In caso contrario, viene applicato `EmptyFilter`.

`EmptyFilter` è un [filtro azione](xref:mvc/controllers/filters#action-filters). Poiché i filtri Azione vengono `EmptyFilter` ignorati da Pagine Razor, non `OtherPages/Page2`ha alcun effetto come previsto se il percorso non contiene .

Richiedere la pagina Page2 dell'esempio in `localhost:5000/OtherPages/Page2` ed esaminare le intestazioni per visualizzare il risultato:

![L'elemento OtherPagesPage2Header viene aggiunto alla risposta per Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurare una factory di filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>configura la factory specificata per applicare [filtri](xref:mvc/controllers/filters) a tutte le pagine Razor.

L'app di esempio illustra un esempio dell'uso di una [factory di filtro](xref:mvc/controllers/filters#ifilterfactory) aggiungendo un'intestazione, `FilterFactoryHeader`, con due valori per le pagine dell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina informazioni indicano che sono state aggiunte due intestazioni FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtri MVC e il filtro di pagina (IPageFilter)

I [filtri azione](xref:mvc/controllers/filters#action-filters) MVC vengono ignorati da Razor Pages, poiché vengono usati i metodi del gestore. Sono disponibili altri tipi di filtri MVC: [autorizzazione](xref:mvc/controllers/filters#authorization-filters), [eccezione](xref:mvc/controllers/filters#exception-filters), [risorse](xref:mvc/controllers/filters#resource-filters) e [risultati](xref:mvc/controllers/filters#result-filters). Per altre informazioni, vedere l'argomento [Filtri](xref:mvc/controllers/filters).

Il filtro<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>Pagina ( ) è un filtro che si applica alle pagine Razor. Per altre informazioni, vedere [Modalità di filtro per pagine Razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
