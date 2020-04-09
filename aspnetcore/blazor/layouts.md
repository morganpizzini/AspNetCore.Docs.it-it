---
title: layout Blazor di ASP.NET Core
author: guardrex
description: Scopri come creare componenti di Blazor layout riutilizzabili per le app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660412"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a>layout Blazor di ASP.NET Core

Di [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)

Alcuni elementi dell'app, ad esempio menu, messaggi di copyright e loghi aziendali, fanno in genere parte del layout generale dell'app e vengono usati da ogni componente dell'app. La copia del codice di questi elementi in tutti i&mdash;componenti di un'app non è un approccio efficiente ogni volta che uno degli elementi richiede un aggiornamento, ogni componente deve essere aggiornato. Tale duplicazione è difficile da mantenere e può portare a contenuti incoerenti nel tempo. *I layout* risolvono questo problema.

Tecnicamente, un layout è solo un altro componente. Un layout è definito in un modello Razor o nel codice C , e può utilizzare [l'associazione dati,](xref:blazor/data-binding) [l'inserimento](xref:blazor/dependency-injection)delle dipendenze e altri scenari di componenti.

Per trasformare un *componente* in un *layout,* il componente:

* Eredita da `LayoutComponentBase`, che `Body` definisce una proprietà per il contenuto di cui è stato eseguito il rendering all'interno del layout.
* Utilizza la sintassi `@Body` Razor per specificare la posizione nel markup del layout in cui viene eseguito il rendering del contenuto.

Nell'esempio di codice riportato di seguito viene illustrato il modello Razor di un componente di layout, *MainLayout.razor*. Il layout `LayoutComponentBase` eredita e `@Body` imposta l'oggetto tra la barra di navigazione e il piè di pagina:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

In un'app basata Blazor su uno `MainLayout` dei modelli di app, il componente (*MainLayout.razor*) si trova nella cartella *Shared* dell'app.

## <a name="default-layout"></a>Layout predefinito

Specificare il layout `Router` predefinito dell'app nel componente nel file *App.razor* dell'app. Il `Router` componente seguente, fornito dai Blazor modelli di default, `MainLayout` imposta il layout di default sul componente:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Per fornire un `NotFound` layout predefinito `LayoutView` per `NotFound` il contenuto, specificare un per il contenuto:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Per ulteriori informazioni `Router` sul <xref:blazor/routing>componente, vedere .

Specificare il layout come layout predefinito nel router è una procedura utile perché può essere sostituito in base al componente o alla cartella. Preferisci usare il router per impostare il layout predefinito dell'app perché è la tecnica più generale.

## <a name="specify-a-layout-in-a-component"></a>Specificare un layout in un componente

Utilizzare il Razor direttiva `@layout` per applicare un layout a un componente. Il compilatore `@layout` converte in un `LayoutAttribute`oggetto , che viene applicato alla classe del componente.

Il contenuto del `MasterList` seguente componente `MasterLayout` viene inserito `@Body`nella posizione di:

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Se si specifica il layout direttamente in un componente, `@layout` viene eseguito l'override di un set di layout *predefinito* nel router o di una direttiva importata da *_Imports.razor*.

## <a name="centralized-layout-selection"></a>Selezione centralizzata del layout

Ogni cartella di un'app può contenere facoltativamente un file modello denominato *_Imports.razor*. Il compilatore include le direttive specificate nel file imports in tutti i modelli Razor nella stessa cartella e in modo ricorsivo in tutte le relative sottocartelle. Pertanto, un file *_Imports.razor* contenente `@layout MyCoolLayout` garantisce che `MyCoolLayout`tutti i componenti in una cartella utilizzino . Non è necessario aggiungere `@layout MyCoolLayout` ripetutamente a tutti i file *.razor* all'interno della cartella e delle sottocartelle. `@using`direttive vengono applicate anche ai componenti nello stesso modo.

Le seguenti importazioni di file *_Imports.razor:*

* `MyCoolLayout`.
* Tutti i componenti Razor nella stessa cartella ed eventuali sottocartelle.
* Spazio dei nomi `BlazorApp1.Data`.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Il file *_Imports.razor* è simile al [file _ViewImports.cshtml per le visualizzazioni e le pagine Razor,](xref:mvc/views/layout#importing-shared-directives) ma applicato in modo specifico ai file dei componenti Razor.

Se si specifica un layout in *_Imports.razor,* viene eseguito l'override di un layout specificato come *layout predefinito*del router.

## <a name="nested-layouts"></a>Layout nidificati

Le app possono essere costituite da layout annidati. Un componente può fare riferimento a un layout che a sua volta fa riferimento a un altro layout. Ad esempio, i layout di nidificazione vengono utilizzati per creare una struttura di menu a più livelli.

Nell'esempio seguente viene illustrato come utilizzare layout annidati. Il file *EpisodesComponent.razor* è il componente da visualizzare. Il componente `MasterListLayout`fa riferimento a :

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Il file *MasterListLayout.razor* fornisce il `MasterListLayout`file . Il layout fa `MasterLayout`riferimento a un altro layout, , dove viene eseguito il rendering. `EpisodesComponent`viene eseguito il rendering dove `@Body` appare:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Infine, `MasterLayout` in *MasterLayout.razor* contiene gli elementi di layout di primo livello, ad esempio l'intestazione, il menu principale e il piè di pagina. `MasterListLayout`con `EpisodesComponent` il rendering `@Body` viene eseguito il punto in cui viene visualizzato:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Condividere un layout Pagine Razor con componenti integrati

Quando i componenti instradabili sono integrati in un'app Razor Pages, il layout condiviso dell'app può essere usato con i componenti. Per altre informazioni, vedere <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/views/layout>
