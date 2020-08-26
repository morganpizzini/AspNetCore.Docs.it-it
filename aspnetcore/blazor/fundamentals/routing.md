---
title: BlazorRouting ASP.NET Core
author: guardrex
description: Informazioni su come instradare le richieste nelle app e sul componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: eb9e3cbddd2eaca8fef9a6782c28bbce4c029f58
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865324"
---
# <a name="aspnet-core-no-locblazor-routing"></a>BlazorRouting ASP.NET Core

Di [Luke Latham](https://github.com/guardrex)

Informazioni su come instradare le richieste e su come usare il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente per creare collegamenti di navigazione nelle Blazor app.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integrazione di routing endpoint ASP.NET Core

Blazor Server è integrato in [ASP.net core routing dell'endpoint](xref:fundamentals/routing). Un'app ASP.NET Core è configurata in modo da accettare le connessioni in ingresso per i componenti interattivi con <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure` :

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

La configurazione più tipica consiste nel routing di tutte le richieste a una Razor pagina, che funge da host per la parte lato server dell' Blazor Server app. Per convenzione, la pagina *host* è in genere denominata `_Host.cshtml` . La route specificata nel file host viene chiamata route di *fallback* perché funziona con una priorità bassa nella corrispondenza della route. La route di fallback viene considerata quando altre route non corrispondono. Ciò consente all'app di usare altri controller e pagine senza interferire con l' Blazor Server app.

Per informazioni sulla configurazione <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> di per l'hosting di server URL non radice, vedere <xref:blazor/host-and-deploy/index#app-base-path> .

## <a name="route-templates"></a>Modelli di route

Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente consente il routing a ogni componente con una route specificata. Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente viene visualizzato nel `App.razor` file:

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Quando `.razor` viene compilato un file con una `@page` direttiva, alla classe generata viene fornito un oggetto che <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifica il modello di route.

In fase di esecuzione, il <xref:Microsoft.AspNetCore.Components.RouteView> componente:

* Riceve <xref:Microsoft.AspNetCore.Components.RouteData> da <xref:Microsoft.AspNetCore.Components.Routing.Router> insieme a tutti i parametri desiderati.
* Esegue il rendering del componente specificato con il layout (o un layout predefinito facoltativo) utilizzando i parametri specificati.

Facoltativamente, è possibile specificare un <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametro con una classe layout da utilizzare per i componenti che non specificano un layout. I Blazor modelli predefiniti specificano il `MainLayout` componente. `MainLayout.razor` si trova nella cartella del progetto del modello `Shared` . Per ulteriori informazioni sui layout, vedere <xref:blazor/layouts> .

È possibile applicare più modelli di route a un componente. Il componente seguente risponde alle richieste per `/BlazorRoute` e `/DifferentBlazorRoute` :

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Per la corretta risoluzione degli URL, l'app deve includere un `<base>` tag nel `wwwroot/index.html` file ( Blazor WebAssembly ) o nel `Pages/_Host.cshtml` file ( Blazor Server ) con il percorso di base dell'applicazione specificato nell' `href` attributo ( `<base href="/">` ). Per altre informazioni, vedere <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Fornire contenuto personalizzato quando il contenuto non è stato trovato

Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente consente all'app di specificare contenuto personalizzato se non viene trovato contenuto per la route richiesta.

Nel `App.razor` file, impostare il contenuto personalizzato nel <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametro di modello del <xref:Microsoft.AspNetCore.Components.Routing.Router> componente:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

Il contenuto dei `<NotFound>` tag può includere elementi arbitrari, ad esempio altri componenti interattivi. Per applicare un layout predefinito al <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenuto, vedere <xref:blazor/layouts> .

## <a name="route-to-components-from-multiple-assemblies"></a>Routing a componenti da più assembly

Usare il <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametro per specificare gli assembly aggiuntivi da <xref:Microsoft.AspNetCore.Components.Routing.Router> considerare per il componente durante la ricerca di componenti instradabili. Gli assembly specificati sono considerati oltre all' `AppAssembly` assembly specificato da. Nell'esempio seguente `Component1` è un componente instradabile definito in una libreria di classi a cui si fa riferimento. Nell' <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> esempio seguente viene restituito il supporto del routing per `Component1` :

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Parametri di route

Il router usa parametri di route per popolare i parametri del componente corrispondenti con lo stesso nome (senza distinzione tra maiuscole e minuscole):

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

I parametri facoltativi non sono supportati. `@page`Nell'esempio precedente vengono applicate due direttive. Il primo consente la navigazione al componente senza un parametro. La seconda `@page` direttiva accetta il `{text}` parametro Route e assegna il valore alla `Text` Proprietà.

## <a name="route-constraints"></a>Vincoli di route

Un vincolo di route impone la corrispondenza tra i tipi in un segmento di route e un componente.

Nell'esempio seguente, la route per il `Users` componente corrisponde solo a se:

* Un `Id` segmento di route è presente nell'URL della richiesta.
* Il `Id` segmento è un numero intero ( `int` ).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Sono disponibili i vincoli di route indicati nella tabella seguente. Per ulteriori informazioni, vedere l'avviso sotto la tabella per i vincoli di route corrispondenti alla lingua inglese.

| Vincolo | Esempio           | Esempi di corrispondenza                                                                  | Invariante<br>culture<br>corrispondenti |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Sì                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Sì                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Sì                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Sì                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Sì                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Sì                              |

> [!WARNING]
> I vincoli di route che verificano l'URL e vengono convertiti in un tipo CLR, ad esempio `int` o <xref:System.DateTime>, usano sempre le impostazioni cultura inglese non dipendenti da paese/area geografica, presupponendo che l'URL sia non localizzabile.

### <a name="routing-with-urls-that-contain-dots"></a>Routing con URL contenenti punti

Nelle Blazor Server app la route predefinita in `_Host.cshtml` è `/` ( `@page "/"` ). Un URL di richiesta che contiene un punto ( `.` ) non corrisponde alla route predefinita perché l'URL viene visualizzato per richiedere un file. Un' Blazor app restituisce una risposta *404 non trovata* per un file statico che non esiste. Per usare le route che contengono un punto, configurare `_Host.cshtml` con il modello di route seguente:

```cshtml
@page "/{**path}"
```

Il `"/{**path}"` modello include:

* Double-asterisco *catch-all* Syntax ( `**` ) per acquisire il percorso tra più limiti di cartella senza decodificare le barre ( `/` ).
* `path` nome del parametro di route.

Per altre informazioni, vedere <xref:fundamentals/routing>.

## <a name="catch-all-route-parameters"></a>Parametri di route catch-all

::: moniker range=">= aspnetcore-5.0"

*Questa sezione si applica a .NET 5 Release Candidate 1 (RC1) o versione successiva, che verrà rilasciata a metà settembre.*

I parametri di route catch-all, che acquisiscono percorsi tra più limiti di cartella, sono supportati nei componenti di. Il parametro della route catch-all deve essere:

* Denominato in modo che corrisponda al nome del segmento di route. La denominazione non distingue tra maiuscole e minuscole.
* Tipo `string`. Il Framework non fornisce il cast automatico.
* Alla fine dell'URL.

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

Per l'URL `/page/this/is/a/test` con un modello di route di `/page/{*pageRoute}` , il valore di `PageRoute` è impostato su `this/is/a/test` .

Le barre e i segmenti del percorso acquisito vengono decodificati. Per un modello di route di `/page/{*pageRoute}` , l'URL `/page/this/is/a%2Ftest%2A` restituisce `this/is/a/test*` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

I parametri di route catch-all saranno supportati in .NET 5 Release Candidate 1 (RC1) o versioni successive, che verranno rilasciate a metà settembre. *

::: moniker-end

## <a name="navlink-component"></a>Componente NavLink

Usare un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente al posto di elementi collegamento ipertestuale HTML ( `<a>` ) durante la creazione di collegamenti di navigazione. Un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente si comporta come un `<a>` elemento, con la differenza che viene attivata o disattivata una `active` classe CSS a seconda che `href` corrisponda all'URL corrente. La `active` classe consente a un utente di comprendere quale pagina è la pagina attiva tra i collegamenti di navigazione visualizzati. Facoltativamente, assegnare un nome di classe CSS a <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> per applicare una classe CSS personalizzata al collegamento di cui è stato eseguito il rendering quando la route corrente corrisponde a `href` .

Il `NavMenu` componente seguente crea una [`Bootstrap`](https://getbootstrap.com/docs/) barra di navigazione che illustra come utilizzare i <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componenti di:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Sono disponibili due <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> opzioni che è possibile assegnare all' `Match` attributo dell' `<NavLink>` elemento:

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> È attivo quando corrisponde all'intero URL corrente.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*impostazione predefinita*): l'oggetto <xref:Microsoft.AspNetCore.Components.Routing.NavLink> è attivo quando corrisponde a qualsiasi prefisso dell'URL corrente.

Nell'esempio precedente la Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` corrisponde all'URL Home e riceve solo la `active` classe CSS nell'URL del percorso di base predefinito dell'app (ad esempio, `https://localhost:5001/` ). Il secondo <xref:Microsoft.AspNetCore.Components.Routing.NavLink> riceve la `active` classe quando l'utente visita un URL con un `MyComponent` prefisso, ad esempio `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment` .

<xref:Microsoft.AspNetCore.Components.Routing.NavLink>Gli attributi dei componenti aggiuntivi vengono passati al tag di ancoraggio sottoposto a rendering. Nell'esempio seguente il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente include l' `target` attributo:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Viene eseguito il rendering del markup HTML seguente:

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> A causa del modo in cui Blazor viene eseguito il rendering del contenuto figlio, `NavLink` i componenti di rendering all'interno di un `for` ciclo richiedono una variabile di indice locale se la variabile del ciclo di incremento viene utilizzata nel `NavLink` contenuto del componente (figlio):
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> L'uso di una variabile di indice in questo scenario è un requisito per **qualsiasi** componente figlio che usa una variabile di ciclo nel [contenuto figlio](xref:blazor/components/index#child-content), non solo nel `NavLink` componente.
>
> In alternativa, usare un `foreach` ciclo con <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a>Helper per lo stato di navigazione e URI

Usare <xref:Microsoft.AspNetCore.Components.NavigationManager> per lavorare con gli URI e la navigazione nel codice C#. <xref:Microsoft.AspNetCore.Components.NavigationManager> fornisce l'evento e i metodi illustrati nella tabella seguente.

| Membro | Descrizione |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Ottiene l'URI assoluto corrente. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Ottiene l'URI di base (con una barra finale) che può essere anteposto ai percorsi URI relativi per produrre un URI assoluto. In genere, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corrisponde all' `href` attributo sull'elemento del documento `<base>` in `wwwroot/index.html` ( Blazor WebAssembly ) o `Pages/_Host.cshtml` ( Blazor Server ). |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Passa all'URI specificato. Se `forceLoad` è `true` :<ul><li>Il routing lato client viene ignorato.</li><li>Il browser è forzato a caricare la nuova pagina dal server, indipendentemente dal fatto che l'URI venga normalmente gestito dal router lato client.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Un evento che viene attivato quando il percorso di navigazione viene modificato. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converte un URI relativo in un URI assoluto. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Dato un URI di base (ad esempio, un URI restituito in precedenza da <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), converte un URI assoluto in un URI relativo al prefisso URI di base. |

`Counter`Quando si seleziona il pulsante, il componente seguente passa al componente dell'app:

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

Il componente seguente gestisce un evento di modifica della posizione sottoscrivendo <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> . Il `HandleLocationChanged` metodo è unhooked quando `Dispose` viene chiamato dal Framework. L'unhook del metodo consente Garbage Collection del componente.

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> fornisce le informazioni seguenti sull'evento:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: URL della nuova posizione.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Se `true` , Blazor intercetta la navigazione dal browser. Se `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> ha causato la navigazione.

Per ulteriori informazioni sull'eliminazione dei componenti, vedere <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .

## <a name="query-string-and-parse-parameters"></a>Stringa di query e parametri di analisi

La stringa di query di una richiesta può essere ottenuta <xref:Microsoft.AspNetCore.Components.NavigationManager> dalla <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> proprietà di:

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

Per analizzare i parametri della stringa di query:

* Aggiungere un riferimento al pacchetto per [Microsoft. AspNetCore. webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).
* Ottenere il valore dopo aver analizzato la stringa di query con <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

Il segnaposto `{KEY}` nell'esempio precedente è la chiave del parametro della stringa di query. Ad esempio, la coppia chiave-valore dell'URL `?ship=Tardis` utilizza una chiave di `ship` .
