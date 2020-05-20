---
title: BlazorLayout ASP.NET Core
author: guardrex
description: Informazioni su come creare componenti di layout riutilizzabili per le Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 09cca9c4af23c35fdbc2ee92169913c960b0a68d
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424328"
---
# <a name="aspnet-core-blazor-layouts"></a>BlazorLayout ASP.NET Core

Di [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)

Alcuni elementi dell'app, ad esempio i menu, i messaggi di copyright e i logo aziendali, fanno in genere parte del layout generale dell'app e vengono usati da ogni componente nell'app. La copia del codice di questi elementi in tutti i componenti di un'app non è un approccio efficace. Ogni volta che uno degli elementi richiede un aggiornamento, è necessario aggiornare ogni componente. Tale duplicazione è difficile da gestire e può causare contenuti incoerenti nel tempo. I *layout* risolvono questo problema.

Tecnicamente, un layout è semplicemente un altro componente. Un layout è definito in un Razor modello o nel codice C# e può usare [Data Binding](xref:blazor/data-binding), l' [inserimento delle dipendenze](xref:blazor/dependency-injection)e altri scenari di componenti.

Per trasformare un *componente* in un *layout*, il componente:

* Eredita da `LayoutComponentBase` , che definisce una `Body` proprietà per il contenuto di cui è stato eseguito il rendering all'interno del layout.
* Usa la Razor sintassi `@Body` per specificare il percorso nel markup di layout in cui viene eseguito il rendering del contenuto.

Nell'esempio di codice seguente viene illustrato il Razor modello di un componente di layout, *MainLayout. Razor*. Il layout eredita `LayoutComponentBase` e imposta l'oggetto `@Body` tra la barra di spostamento e il piè di pagina:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

In un'app basata su uno dei Blazor modelli di app, il `MainLayout` componente (*MainLayout. Razor*) si trova nella cartella *condivisa* dell'app.

## <a name="default-layout"></a>Layout predefinito

Specificare il layout predefinito dell'app nel `Router` componente nel file *app. Razor* dell'app. Il `Router` componente seguente, fornito dai Blazor modelli predefiniti, imposta il layout predefinito sul `MainLayout` componente:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Per fornire un layout predefinito per il `NotFound` contenuto, specificare un oggetto `LayoutView` per il `NotFound` contenuto:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Per ulteriori informazioni sul `Router` componente, vedere <xref:blazor/routing> .

La specifica del layout come layout predefinito nel router è una procedura utile perché può essere sottoposta a override in base al singolo componente o alla cartella. Preferisci usare il router per impostare il layout predefinito dell'app perché è la tecnica più generale.

## <a name="specify-a-layout-in-a-component"></a>Specificare un layout in un componente

Utilizzare la Razor direttiva `@layout` per applicare un layout a un componente. Il compilatore esegue la conversione `@layout` in un oggetto `LayoutAttribute` , che viene applicato alla classe Component.

Il contenuto del componente seguente `MasterList` viene inserito nella `MasterLayout` posizione di `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

La specifica del layout direttamente in un componente sostituisce un set di *layout predefinito* nel router o una `@layout` direttiva importata da *_Imports. Razor*.

## <a name="centralized-layout-selection"></a>Selezione layout centralizzata

Ogni cartella di un'app può contenere facoltativamente un file modello denominato *_Imports. Razor*. Il compilatore include le direttive specificate nel file Imports in tutti i Razor modelli nella stessa cartella e in modo ricorsivo in tutte le relative sottocartelle. Pertanto, un file *_Imports. Razor* contenente `@layout MyCoolLayout` garantisce che tutti i componenti di una cartella usino `MyCoolLayout` . Non è necessario aggiungere ripetutamente `@layout MyCoolLayout` a tutti i file con *estensione Razor* nella cartella e nelle sottocartelle. `@using`le direttive vengono applicate anche ai componenti nello stesso modo.

Il seguente file di *_Imports. Razor* Imports:

* `MyCoolLayout`.
* Tutti i Razor componenti nella stessa cartella e in tutte le sottocartelle.
* Spazio dei nomi `BlazorApp1.Data`.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Il file *_Imports. Razor* è simile al [file _ViewImports. cshtml per le Razor visualizzazioni e le pagine,](xref:mvc/views/layout#importing-shared-directives) ma viene applicato in modo specifico ai Razor file del componente.

Specifica di un layout in *_Imports. Razor* sostituisce un layout specificato come *layout predefinito*del router.

## <a name="nested-layouts"></a>Layout annidati

Le app possono essere costituite da layout annidati. Un componente può fare riferimento a un layout che a sua volta fa riferimento a un altro layout. Ad esempio, i layout di annidamento vengono usati per creare una struttura di menu a più livelli.

Nell'esempio seguente viene illustrato come utilizzare layout annidati. Il file *EpisodesComponent. Razor* è il componente da visualizzare. Il componente fa riferimento a `MasterListLayout` :

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Il file *MasterListLayout. Razor* fornisce `MasterListLayout` . Il layout fa riferimento a un altro layout, `MasterLayout` , in cui viene eseguito il rendering. `EpisodesComponent`viene sottoposto a rendering quando `@Body` viene visualizzato:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Infine, `MasterLayout` in *MasterLayout. Razor* contiene gli elementi di layout di primo livello, ad esempio l'intestazione, il menu principale e il piè di pagina. `MasterListLayout`con `EpisodesComponent` viene eseguito il rendering di quando viene `@Body` visualizzato:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Condividere un Razor layout di pagine con componenti integrati

Quando i componenti instradabili sono integrati in un' Razor app pagine, il layout condiviso dell'app può essere usato con i componenti. Per altre informazioni, vedere <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/views/layout>
