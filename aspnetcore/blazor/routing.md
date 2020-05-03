---
title: Routing Blazor ASP.NET Core
author: guardrex
description: Informazioni su come instradare le richieste nelle app e sul componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 291ec33f951008df10f85336c7abd0b3d0a50bbc
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727710"
---
# <a name="aspnet-core-blazor-routing"></a>Routing di ASP.NET Core Blazer

Di [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Informazioni su come instradare le richieste e su `NavLink` come usare il componente per creare collegamenti di navigazione nelle app blazer.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integrazione di routing endpoint ASP.NET Core

Il server blazer è integrato nel [Routing ASP.NET Core endpoint](xref:fundamentals/routing). Un'app ASP.NET Core è configurata in modo da accettare le connessioni in `MapBlazorHub` ingresso `Startup.Configure`per i componenti interattivi con in:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

La configurazione più tipica consiste nel routing di tutte le richieste a una pagina Razor, che funge da host per la parte lato server dell'app del server Blaze. Per convenzione, la pagina *host* è in genere denominata *_Host. cshtml*. La route specificata nel file host viene chiamata route di *fallback* perché funziona con una priorità bassa nella corrispondenza della route. La route di fallback viene considerata quando altre route non corrispondono. Ciò consente all'app di usare altri controller e pagine senza interferire con l'app del server Blaze.

## <a name="route-templates"></a>Modelli di route

Il `Router` componente consente il routing a ogni componente con una route specificata. Il `Router` componente viene visualizzato nel file *app. Razor* :

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Quando viene compilato un file *Razor* con `@page` una direttiva, alla classe generata viene fornito un oggetto <xref:Microsoft.AspNetCore.Components.RouteAttribute> che specifica il modello di route.

In fase di esecuzione `RouteView` , il componente:

* Riceve `RouteData` da `Router` insieme a tutti i parametri desiderati.
* Esegue il rendering del componente specificato con il layout (o un layout predefinito facoltativo) utilizzando i parametri specificati.

Facoltativamente, è possibile specificare `DefaultLayout` un parametro con una classe layout da utilizzare per i componenti che non specificano un layout. I modelli di Blazer predefiniti specificano il `MainLayout` componente. *MainLayout. Razor* si trova nella cartella *condivisa* del progetto modello. Per ulteriori informazioni sui layout, vedere <xref:blazor/layouts>.

È possibile applicare più modelli di route a un componente. Il componente seguente risponde alle richieste per `/BlazorRoute` e: `/DifferentBlazorRoute`

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Per la corretta risoluzione degli URL, è necessario che l' `<base>` app includa un tag nel file *wwwroot/index.html* (webassembly Blazer) o nel file *pages/_Host. cshtml* (server Blazer) con il percorso `href` di base`<base href="/">`dell'app specificato nell'attributo (). Per altre informazioni, vedere <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Fornire contenuto personalizzato quando il contenuto non è stato trovato

Il `Router` componente consente all'app di specificare contenuto personalizzato se non viene trovato contenuto per la route richiesta.

Nel file *app. Razor* impostare il contenuto personalizzato nel parametro del `NotFound` modello del `Router` componente:

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

Il contenuto dei `<NotFound>` tag può includere elementi arbitrari, ad esempio altri componenti interattivi. Per applicare un layout predefinito al `NotFound` contenuto, vedere <xref:blazor/layouts>.

## <a name="route-to-components-from-multiple-assemblies"></a>Routing a componenti da più assembly

Usare il `AdditionalAssemblies` parametro per specificare gli assembly aggiuntivi da `Router` considerare per il componente durante la ricerca di componenti instradabili. Gli `AppAssembly`assembly specificati sono considerati oltre all'assembly specificato da. Nell'esempio seguente `Component1` è un componente instradabile definito in una libreria di classi a cui si fa riferimento. Nell'esempio `AdditionalAssemblies` seguente viene restituito il supporto del `Component1`routing per:

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
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

I parametri facoltativi non sono supportati. Nell' `@page` esempio precedente vengono applicate due direttive. Il primo consente la navigazione al componente senza un parametro. La seconda `@page` direttiva accetta il `{text}` parametro Route e assegna il valore alla `Text` proprietà.

## <a name="route-constraints"></a>Vincoli di route

Un vincolo di route impone la corrispondenza tra i tipi in un segmento di route e un componente.

Nell'esempio seguente, la route per il `Users` componente corrisponde solo a se:

* Un `Id` segmento di route è presente nell'URL della richiesta.
* Il `Id` segmento è un numero intero`int`().

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
> I vincoli di route che verificano l'URL e vengono convertiti in un tipo CLR, ad esempio `int` o `DateTime`, usano sempre le impostazioni cultura inglese non dipendenti da paese/area geografica, presupponendo che l'URL sia non localizzabile.

### <a name="routing-with-urls-that-contain-dots"></a>Routing con URL contenenti punti

Nelle app del server blazer, la route predefinita in *_Host. cshtml* `/` è`@page "/"`(). Un URL di richiesta che contiene un punto`.`() non corrisponde alla route predefinita perché l'URL viene visualizzato per richiedere un file. Un'app Blazer restituisce una risposta *404 non trovata* per un file statico che non esiste. Per usare le route che contengono un punto, configurare *_Host. cshtml* con il modello di route seguente:

```cshtml
@page "/{**path}"
```

Il `"/{**path}"` modello include:

* Double-asterisco *catch-all* Syntax (`**`) per acquisire il percorso tra più limiti di cartelle senza codificare le barre`/`().
* `path`nome del parametro di route.

> [!NOTE]
> La sintassi dei parametri *catch-all* (`*`/`**`) **non** è supportata nei componenti Razor (*Razor*).

Per altre informazioni, vedere <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Componente NavLink

Usare un `NavLink` componente al posto di elementi collegamento ipertestuale`<a>`HTML () durante la creazione di collegamenti di navigazione. Un `NavLink` componente si comporta come un `<a>` elemento, con la differenza che viene attivata `active` o disattivata una classe `href` CSS a seconda che corrisponda all'URL corrente. La `active` classe consente a un utente di comprendere quale pagina è la pagina attiva tra i collegamenti di navigazione visualizzati.

Il componente `NavMenu` seguente crea una barra di navigazione [bootstrap](https://getbootstrap.com/docs/) che illustra come usare `NavLink` i componenti di:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Sono disponibili due `NavLinkMatch` opzioni che è possibile assegnare all' `Match` attributo dell' `<NavLink>` elemento:

* `NavLinkMatch.All`&ndash; L' `NavLink` oggetto è attivo quando corrisponde all'intero URL corrente.
* `NavLinkMatch.Prefix`(*impostazione predefinita*) &ndash; L' `NavLink` oggetto è attivo quando corrisponde a qualsiasi prefisso dell'URL corrente.

Nell'esempio precedente la Home `NavLink` `href=""` corrisponde all'URL Home e riceve solo la `active` classe CSS nell'URL del percorso di base predefinito dell'app (ad esempio, `https://localhost:5001/`). Il secondo `NavLink` riceve la `active` classe quando l'utente visita un URL con un `MyComponent` prefisso, `https://localhost:5001/MyComponent` ad esempio e `https://localhost:5001/MyComponent/AnotherSegment`.

Gli `NavLink` attributi dei componenti aggiuntivi vengono passati al tag di ancoraggio sottoposto a rendering. Nell'esempio seguente il `NavLink` componente include l' `target` attributo:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Viene eseguito il rendering del markup HTML seguente:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Helper per lo stato di navigazione e URI

Usare <xref:Microsoft.AspNetCore.Components.NavigationManager> per lavorare con gli URI e la navigazione nel codice C#. `NavigationManager`fornisce l'evento e i metodi illustrati nella tabella seguente.

| Membro | Descrizione |
| ------ | ----------- |
| Uri | Ottiene l'URI assoluto corrente. |
| BaseUri | Ottiene l'URI di base (con una barra finale) che può essere anteposto ai percorsi URI relativi per produrre un URI assoluto. In genere `BaseUri` , corrisponde all' `href` attributo sull' `<base>` elemento del documento in *wwwroot/index.html* Blazor (webassembly) o *pages/_Host. cshtml* (Blazor Server). |
| NavigateTo | Passa all'URI specificato. Se `forceLoad` è `true`:<ul><li>Il routing lato client viene ignorato.</li><li>Il browser è forzato a caricare la nuova pagina dal server, indipendentemente dal fatto che l'URI venga normalmente gestito dal router lato client.</li></ul> |
| LocationChanged | Un evento che viene attivato quando il percorso di navigazione viene modificato. |
| ToAbsoluteUri | Converte un URI relativo in un URI assoluto. |
| <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Dato un URI di base (ad esempio, un URI restituito in `GetBaseUri`precedenza da), converte un URI assoluto in un URI relativo al prefisso URI di base. |

Quando si seleziona il pulsante, il componente seguente `Counter` passa al componente dell'app:

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

Il componente seguente gestisce un evento di modifica della posizione. Il `HandleLocationChanged` metodo è unhooked quando `Dispose` viene chiamato dal Framework. L'unhook del metodo consente Garbage Collection del componente.

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

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornisce le informazioni seguenti sull'evento:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; URL della nuova posizione.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Se `true`, Blazor intercetta la navigazione dal browser. Se `false`, [NavigationManager. NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) ha causato la navigazione.

Per ulteriori informazioni sull'eliminazione dei componenti, <xref:blazor/lifecycle#component-disposal-with-idisposable>vedere.
