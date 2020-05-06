---
title: Introduzione a ASP.NET CoreBlazor
author: guardrex
description: Esplora ASP.NET Core Blazor, un modo per creare un'interfaccia utente Web interattiva sul lato client con .NET in un'app ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 03/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/index
ms.openlocfilehash: ced3e2cc0428fccf6f0b2eba7a3f045e07002234
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771950"
---
# <a name="introduction-to-aspnet-core-blazor"></a>Introduzione a ASP.NET CoreBlazor

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

*Benvenuti in Blazor.*

Blazorè un Framework per la creazione di un'interfaccia utente Web interattiva sul lato client con .NET:

* Creare interfacce utente interattive avanzate con C# invece di JavaScript.
* Condividere la logica dell'app scritta in .NET sul lato client e sul lato server.
* Eseguire il rendering dell'interfaccia utente come HTML e CSS per un ampio supporto dei browser, inclusi i browser per dispositivi mobili.
* Integrarsi con piattaforme di hosting moderne, ad esempio [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).

L'uso di .NET per lo sviluppo Web lato client offre i vantaggi seguenti:

* scrivere codice in C# invece che in JavaScript.
* Permette di sfruttare l'ecosistema .NET esistente di librerie .NET.
* Permette di condividere la logica dell'app tra server e client.
* Permette di ottenere le prestazioni, l'affidabilità e la sicurezza di .NET.
* produttività con Visual Studio in Windows, Linux e macOS.
* basato su un set comune di linguaggi, framework e strumenti che sono stabili, ricchi di funzionalità e facili da usare.

## <a name="components"></a>Componenti

Blazorle app sono basate su *componenti*. Un componente di Blazor è un elemento dell'interfaccia utente, ad esempio una pagina, una finestra di dialogo o un form di immissione dati.

I componenti sono classi .NET compilati in assembly .NET che:

* Definiscono la logica di rendering dell'interfaccia utente flessibile.
* Gestiscono gli eventi utente.
* Possono essere annidati e riutilizzati.
* Può essere condiviso e distribuito come [ Razor librerie di classi](xref:razor-pages/ui-class) o [pacchetti NuGet](/nuget/what-is-nuget).

La classe Component viene in genere scritta sotto forma di pagina [Razor](xref:mvc/views/razor) di markup con estensione *Razor* . I componenti Blazor di sono definiti formalmente come * Razor componenti*di. Razorè una sintassi per combinare il markup HTML con il codice C# progettato per la produttività degli sviluppatori. Razorconsente di passare dal markup HTML al linguaggio C# e viceversa nello stesso file con supporto [IntelliSense](/visualstudio/ide/using-intellisense) . RazorAnche le pagine e MVC Razorusano. Diversamente dalle Razor pagine e da MVC, che sono basate su un modello di richiesta/risposta, i componenti vengono usati in modo specifico per la composizione e la logica dell'interfaccia utente lato client.

Il markup Razor seguente illustra un componente (*Dialog. Razor*), che può essere annidato all'interno di un altro componente:

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

Il contenuto del corpo della finestra di dialogo (`ChildContent`) e il titolo (`Title`) vengono forniti dal componente che usa questo componente nella propria interfaccia utente. `OnYes` è un metodo C# attivato dall'evento `onclick` del pulsante.

BlazorUsa tag HTML naturali per la composizione dell'interfaccia utente. Gli elementi HTML specificano i componenti e gli attributi di un tag passano valori alle proprietà del componente.

Nell'esempio seguente il componente `Index` usa il componente `Dialog`. `ChildContent` e `Title` vengono impostati dagli attributi e dal contenuto dell'elemento `<Dialog>`.

*Index.razor*:

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

Il rendering della finestra di dialogo viene eseguito quando viene effettuato l'accesso all'elemento padre (*Index.razor*) in un browser:

![Rendering del componente Dialog nel browser](index/_static/dialog.png)

Quando questo componente viene usato nell'app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) e [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) accelera lo sviluppo con il completamento di sintassi e parametri.

Il rendering dei componenti viene eseguito in una rappresentazione in memoria del modello DOM (Document Object Model) del browser denominata *albero di rendering*, usato per aggiornare l'interfaccia utente in modo flessibile ed efficiente.

## <a name="blazor-webassembly"></a>BlazorWebassembly

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorWebassembly è un Framework di app a singola pagina per la creazione di app Web interattive sul lato client con .NET. BlazorWebassembly usa gli standard Web aperti senza plug-in o transpilazione di codice e funziona in tutti i Web browser moderni, inclusi i browser per dispositivi mobili.

L'esecuzione di codice .NET all'interno di Web browser è resa possibile da [WebAssembly](https://webassembly.org) (tecnologia nota anche con l'abbreviazione *wasm*). WebAssembly è un formato bytecode compatto ottimizzato per il download veloce e la velocità massima di esecuzione. WebAssembly è un standard Web aperto ed è supportato nei Web browser senza plug-in.

Il codice WebAssembly può accedere a tutte le funzionalità del browser tramite l'*interoperabilità JavaScript* (*JavaScript interop*). Il codice .NET eseguito tramite WebAssembly nel browser viene eseguito nella sandbox JavaScript del browser con le misure di sicurezza offerte dalla sandbox per la protezione da azioni dannose nel computer client.

![BlazorWebassembly esegue il codice .NET nel browser con webassembly.](index/_static/blazor-webassembly.png)

Quando un' Blazor app webassembly viene compilata ed eseguita in un browser:

* I file e i Razor file di codice C# vengono compilati in assembly .NET.
* Gli assembly e il runtime .NET vengono scaricati nel browser.
* BlazorWebassembly avvia il Runtime .NET e configura il runtime per caricare gli assembly per l'app. Il Blazor runtime di webassembly usa l'interoperabilità JavaScript per gestire la manipolazione Dom e le chiamate API del browser.

La dimensione dell'app pubblicata, ovvero la *dimensione del payload*, è un fattore cruciale per le prestazioni ai fini dell'usabilità dell'app. Un'app di grandi dimensioni impiega relativamente molto tempo a essere scaricata in un browser, influendo negativamente sull'esperienza utente. BlazorWebassembly ottimizza le dimensioni del payload per ridurre i tempi di download:

* Il codice non usato viene rimosso dall'app quando questa viene pubblicata dal [linker del linguaggio intermedio](xref:host-and-deploy/blazor/configure-linker).
* Le risposte HTTP vengono compresse.
* Il runtime e gli assembly .NET vengono memorizzati nella cache nel browser.

## <a name="blazor-server"></a>BlazorServer

Blazorsepara la logica di rendering dei componenti dal modo in cui vengono applicati gli aggiornamenti dell'interfaccia utente. BlazorIl server fornisce il supporto Razor per l'hosting di componenti nel server in un'app ASP.NET Core. Gli aggiornamenti dell'interfaccia utente vengono [SignalR](xref:signalr/introduction) gestiti tramite una connessione.

Il runtime gestisce l'invio di eventi dell'interfaccia utente dal browser al server e quindi applica gli aggiornamenti dell'interfaccia utente inviati dal server di nuovo al browser dopo l'esecuzione dei componenti.

La connessione utilizzata dal Blazor server per comunicare con il browser viene utilizzata anche per gestire le chiamate di interoperabilità JavaScript.

![BlazorIl server esegue il codice .NET sul server e interagisce con il Document Object Model sul client su una SignalR connessione](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Interoperabilità JavaScript

Per le app che richiedono librerie JavaScript di terze parti e l'accesso alle API del browser, i componenti supportano l'interoperabilità con JavaScript. I componenti sono in grado di usare qualsiasi libreria o API supportata da JavaScript. Il codice C# può effettuare chiamate nel codice JavaScript e vice versa. Per altre informazioni, vedere gli articoli seguenti:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a>Condivisione del codice e .NET Standard

Blazorimplementa [.NET Standard 2,0](/dotnet/standard/net-standard). .NET Standard è una specifica formale delle API .NET comuni tra le implementazioni di .NET. Le librerie di Blazorclassi .NET standard possono essere condivise tra diverse piattaforme .NET, ad esempio .NET Framework, .NET Core, Novell, mono e Unity.

Le API non valide all'interno di un Web browser (ad esempio per l'accesso al file system, l'apertura di un socket e la gestione dei thread) generano un'eccezione <xref:System.PlatformNotSupportedException>.

## <a name="additional-resources"></a>Risorse aggiuntive

* [WebAssembly](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [Guida a C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Collegamenti Blazor straordinari](https://github.com/AdrienTorris/awesome-blazor) della community
