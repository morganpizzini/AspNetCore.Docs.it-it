---
title: routing Blazor di ASP.NET Core
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
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218895"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET routing Blazor core

Di [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Informazioni su come instradare `NavLink` le richieste e su come usare il componente per creare collegamenti di navigazione nelle app Blazor.

## <a name="aspnet-core-endpoint-routing-integration"></a>integrazione del routing degli endpoint ASP.NET Core

Blazor Server è integrato in [ASP.NET core Endpoint Routing](xref:fundamentals/routing). Un'app ASP.NET Core è configurata per `MapBlazorHub` `Startup.Configure`accettare connessioni in ingresso per componenti interattivi con:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

La configurazione più tipica consiste nell'instradare tutte le richieste a una pagina Razor, che funge da host per la parte lato server dell'app Blazor Server. Per convenzione, la pagina *host* è in genere denominata *_Host.cshtml*. La route specificata nel file host viene definita route di *fallback* perché opera con una priorità bassa nella corrispondenza della route. La route di fallback viene considerata quando altre route non corrispondono. Ciò consente all'app di utilizzare altri controller e pagine senza interferire con l'app Blazor Server.

## <a name="route-templates"></a>Modelli di percorso

Il `Router` componente consente l'instradamento a ciascun componente con un percorso specificato. Il `Router` componente viene visualizzato nel file *App.razor:*

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

Quando viene compilato un file *con estensione razor* con una `@page` direttiva, alla classe generata viene fornito un <xref:Microsoft.AspNetCore.Components.RouteAttribute> modello di route che specifica.

In fase `RouteView` di esecuzione, il componente:

* Riceve il `RouteData` dal `Router` lungo con tutti i parametri desiderati.
* Esegue il rendering del componente specificato con il relativo layout (o un layout predefinito facoltativo) utilizzando i parametri specificati.

Facoltativamente, è `DefaultLayout` possibile specificare un parametro con una classe di layout da utilizzare per i componenti che non specificano un layout. I modelli Blazor `MainLayout` di default specificano il componente. *MainLayout.razor* si trova nella cartella *Shared* del progetto modello. Per ulteriori informazioni sui <xref:blazor/layouts>layout, vedere .

A un componente è possibile applicare più modelli di percorso. Il componente seguente risponde `/BlazorRoute` alle `/DifferentBlazorRoute`richieste per e:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Affinché gli URL vengano risolti correttamente, l'app deve includere `<base>` un tag nel relativo file di *wwwroot/index.html* (Blazor WebAssembly) o nel file *Pages/_Host.cshtml* (Blazor Server) con il percorso di base dell'app specificato nell'attributo `href` ( ).`<base href="/">` Per altre informazioni, vedere <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Fornire contenuto personalizzato quando il contenuto non viene trovato

Il `Router` componente consente all'app di specificare contenuto personalizzato se il contenuto non viene trovato per la route richiesta.

Nel file *App.razor* impostare il `NotFound` contenuto personalizzato `Router` nel parametro template del componente:

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

Il contenuto `<NotFound>` dei tag può includere elementi arbitrari, ad esempio altri componenti interattivi. Per applicare un `NotFound` layout predefinito <xref:blazor/layouts>al contenuto, vedere .

## <a name="route-to-components-from-multiple-assemblies"></a>Indirizzamento ai componenti da più assiemi

Utilizzare `AdditionalAssemblies` il parametro per `Router` specificare assiemi aggiuntivi per il componente da considerare durante la ricerca di componenti instradabili. Gli assembly specificati vengono `AppAssembly`considerati in aggiunta all'assembly specificato. Nell'esempio seguente, `Component1` è un componente instradabile definito in una libreria di classi di riferimento. L'esempio seguente determina il supporto del routing per:The following `AdditionalAssemblies` example results in routing support for: `Component1`

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Parametri del percorso

Il router utilizza i parametri di route per popolare i parametri del componente corrispondente con lo stesso nome (senza distinzione tra maiuscole e minuscole):

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

I parametri facoltativi non sono supportati. Nell'esempio precedente vengono applicate due `@page` direttive. Il primo consente la navigazione al componente senza un parametro. La `@page` seconda direttiva accetta il `{text}` parametro `Text` route e assegna il valore alla proprietà.

## <a name="route-constraints"></a>Vincoli del percorso

Un vincolo di route applica la corrispondenza dei tipi a un segmento di route a un componente.

Nell'esempio seguente, il `Users` percorso verso il componente corrisponde solo se:

* Un `Id` segmento di route è presente nell'URL della richiesta.
* Il `Id` segmento è`int`un numero intero ( ).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Sono disponibili i vincoli di route illustrati nella tabella seguente. Per i vincoli di route che corrispondono alle impostazioni cultura invarianti, vedere l'avviso sotto la tabella per ulteriori informazioni.

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

### <a name="routing-with-urls-that-contain-dots"></a>Routing con URL che contengono punti

Nelle app del server Blazor, la route `/` `@page "/"`predefinita in *_Host.cshtml* è ( ). Un URL di richiesta`.`che contiene un punto ( ) non corrisponde alla route predefinita perché l'URL sembra richiedere un file. Un'app Blazor restituisce una risposta *404 - Non trovata* per un file statico che non esiste. Per utilizzare route che contengono un punto, configurare *_Host.cshtml* con il modello di route seguente:

```cshtml
@page "/{**path}"
```

Il `"/{**path}"` modello include:

* Sintassi *catch-all* a`**`doppio asterisco ( ) per acquisire`/`il percorso attraverso più limiti di cartella senza codificare le barre ( ).
* `path`nome del parametro del percorso.

> [!NOTE]
> La sintassi dei`*`/`**`parametri *Catch-all* ( ) **non** è supportata nei componenti Razor (*.razor*).

Per altre informazioni, vedere <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Componente NavLink

Utilizzare `NavLink` un componente al posto`<a>`degli elementi dei collegamenti ipertestuali HTML ( ) durante la creazione dei collegamenti di navigazione. Un `NavLink` componente si comporta `<a>` come un elemento, `active` ad eccezione `href` del fatto che attiva o disattiva una classe CSS in base al fatto che corrisponda all'URL corrente. La `active` classe consente a un utente di comprendere quale pagina è la pagina attiva tra i collegamenti di navigazione visualizzati.

Il `NavMenu` componente seguente crea una barra di `NavLink` navigazione [Bootstrap](https://getbootstrap.com/docs/) che dimostra come utilizzare i componenti:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Sono disponibili `NavLinkMatch` due opzioni che `Match` è possibile `<NavLink>` assegnare all'attributo dell'elemento:

* `NavLinkMatch.All`&ndash; L'oggetto `NavLink` è attivo quando corrisponde all'intero URL corrente.
* `NavLinkMatch.Prefix`(*predefinito*) &ndash; L'oggetto `NavLink` è attivo quando corrisponde a qualsiasi prefisso dell'URL corrente.

Nell'esempio precedente, Home `NavLink` `href=""` corrisponde all'URL principale `active` e riceve solo la classe CSS all'URL `https://localhost:5001/`del percorso di base predefinito dell'app, ad esempio ). Il `NavLink` secondo riceve `active` la classe quando l'utente visita qualsiasi URL con un `MyComponent` prefisso (ad esempio, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment`).

Gli `NavLink` attributi aggiuntivi del componente vengono passati al tag di ancoraggio sottoposto a rendering. Nell'esempio seguente, `NavLink` il `target` componente include l'attributo:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Viene eseguito il rendering del markup HTML seguente:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI e helper dello stato di navigazione

Usare <xref:Microsoft.AspNetCore.Components.NavigationManager> per usare gli URI e la navigazione nel codice C. `NavigationManager`fornisce l'evento e i metodi illustrati nella tabella seguente.

| Membro | Descrizione |
| ------ | ----------- |
| Uri | Ottiene l'URI assoluto corrente. |
| BaseUri | Ottiene l'URI di base (con una barra finale) che può essere anteposto ai percorsi URI relativi per produrre un URI assoluto. In `BaseUri` genere, `href` corrisponde all'attributo `<base>` nell'elementoBlazor del documento in *wwwroot/index.html* (Blazor WebAssembly) o *Pages/_Host.cshtml* ( Server). |
| NavigateTo | Passa all'URI specificato. Se `forceLoad` `true`è:<ul><li>Il routing lato client viene ignorato.</li><li>Il browser è costretto a caricare la nuova pagina dal server, indipendentemente dal fatto che l'URI venga normalmente gestito dal router sul lato client.</li></ul> |
| LocationChanged | Evento che viene generato quando la posizione di navigazione viene modificata. |
| AAbsoluteUri | Converte un URI relativo in un URI assoluto. |
| <span style="word-break:normal;word-wrap:normal">Percorso di base</span> | Dato un URI di base (ad esempio, un URI restituito in precedenza da `GetBaseUri`), converte un URI assoluto in un URI relativo al prefisso URI di base. |

Il componente seguente consente di `Counter` passare al componente dell'app quando viene selezionato il pulsante:

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

Il componente seguente gestisce un evento di modifica della posizione. Il `HandleLocationChanged` metodo viene unhooked quando `Dispose` viene chiamato dal framework. L'annullamento dell'hook del metodo consente la procedura di Garbage Collection del componente.

```razor
@implement IDisposable
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

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornisce le seguenti informazioni sull'evento:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; URL del nuovo percorso.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Se `true` Blazor , è stata intercettata la navigazione dal browser. Se `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) ha causato la navigazione.

Per ulteriori informazioni sullo <xref:blazor/lifecycle#component-disposal-with-idisposable>smaltimento dei componenti, vedere .
