---
title: RazorLibrerie di classi dei componenti ASP.NET Core
author: guardrex
description: Scopri in che modo i componenti possono essere inclusi nelle Blazor app da una libreria di componenti esterna.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: d933a677a063d50fbe708264106e3ce19400a270
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628573"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>RazorLibrerie di classi dei componenti ASP.NET Core

Di [Simon Timms](https://github.com/stimms)

I componenti possono essere condivisi in una [ Razor libreria di classi (RCL)](xref:razor-pages/ui-class) nei progetti. È possibile includere una * Razor libreria di classi Components* da:

* Un altro progetto nella soluzione.
* Un pacchetto NuGet.
* Libreria .NET A cui si fa riferimento.

Così come i componenti sono tipi .NET normali, i componenti forniti da un RCL sono assembly .NET normali.

## <a name="create-an-rcl"></a>Creare un RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Creare un nuovo progetto.
1. Selezionare ** Razor libreria di classi**. Selezionare **Avanti**.
1. Nella finestra di dialogo **Crea una nuova Razor libreria di classi** Selezionare **Crea**.
1. Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito. Gli esempi in questo argomento usano il nome del progetto `ComponentLibrary` . Selezionare **Create** (Crea).
1. Aggiungere RCL a una soluzione:
   1. Fare clic con il pulsante destro del mouse sulla soluzione. Selezionare **Aggiungi**  >  **progetto esistente**.
   1. Passare al file di progetto di RCL.
   1. Selezionare il file di progetto di RCL ( `.csproj` ).
1. Aggiungere un riferimento a RCL dall'app:
   1. Fare clic con il pulsante destro del mouse sul progetto app. Selezionare **Aggiungi**  >  **riferimento**.
   1. Selezionare il progetto RCL. Selezionare **OK**.

> [!NOTE]
> Se la casella di controllo **pagine e visualizzazioni di supporto** è selezionata durante la generazione del RCL dal modello, aggiungere anche un `_Imports.razor` file alla radice del progetto generato con il contenuto seguente per abilitare la Razor creazione dei componenti:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Aggiungere manualmente il file alla radice del progetto generato.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

1. Usare il modello ** Razor libreria di classi** ( `razorclasslib` ) con il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando in una shell dei comandi. Nell'esempio seguente viene creato un RCL denominato `ComponentLibrary` . La cartella che include `ComponentLibrary` viene creata automaticamente quando si esegue il comando:

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > Se l' `-s|--support-pages-and-views` opzione viene utilizzata durante la generazione del RCL dal modello, aggiungere anche un `_Imports.razor` file alla radice del progetto generato con il contenuto seguente per abilitare la Razor creazione dei componenti:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Aggiungere manualmente il file alla radice del progetto generato.

1. Per aggiungere la libreria a un progetto esistente, usare il [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) comando in una shell dei comandi. Nell'esempio seguente viene aggiunto RCL all'app. Eseguire il comando seguente dalla cartella del progetto dell'app con il percorso della libreria:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Utilizzare un componente di libreria

Per utilizzare i componenti definiti in una raccolta in un altro progetto, utilizzare uno degli approcci seguenti:

* Usare il nome completo del tipo con lo spazio dei nomi.
* Usare Razor la [`@using`](xref:mvc/views/razor#using) direttiva. I singoli componenti possono essere aggiunti in base al nome.

Negli esempi seguenti `ComponentLibrary` è una libreria di componenti contenente il `Component1` componente ( `Component1.razor` ). Il `Component1` componente è un componente di esempio aggiunto automaticamente dal modello di progetto RCL al momento della creazione della libreria.

Fare riferimento al `Component1` componente utilizzando il relativo spazio dei nomi:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

In alternativa, portare la libreria nell'ambito con una [`@using`](xref:mvc/views/razor#using) direttiva e usare il componente senza il relativo spazio dei nomi:

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

Facoltativamente, includere la `@using ComponentLibrary` direttiva nel file di primo livello `_Import.razor` per rendere disponibili i componenti della libreria a un intero progetto. Aggiungere la direttiva a un `_Import.razor` file a qualsiasi livello per applicare lo spazio dei nomi a un singolo componente o a un set di componenti all'interno di una cartella.

::: moniker range=">= aspnetcore-5.0"

Per specificare `Component1` la `my-component` classe CSS per il componente, collegarsi al foglio di stile della libreria usando il [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) del Framework in `Component1.razor` :

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

Per fornire il foglio di stile nell'app, è possibile collegarsi in alternativa al foglio di stile della libreria nel `wwwroot/index.html` file ( Blazor WebAssembly ) o nel file dell'app `Pages/_Host.cshtml` ( Blazor Server ):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

Quando il `Link` componente viene usato in un componente figlio, l'asset collegato è disponibile anche per qualsiasi altro componente figlio del componente padre, purché venga eseguito il rendering dell'elemento figlio con il `Link` componente. La distinzione tra l'uso del `Link` componente in un componente figlio e l'inserimento di un `<link>` tag HTML in `wwwroot/index.html` o `Pages/_Host.cshtml` è che il tag HTML sottoposto a rendering di un componente del Framework:

* Può essere modificato in base allo stato dell'applicazione. Un `<link>` tag HTML hardcoded non può essere modificato in base allo stato dell'applicazione.
* Viene rimosso dal codice HTML `<head>` quando il componente padre non viene più sottoposto a rendering.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Per specificare `Component1` la `my-component` classe CSS, collegarsi al foglio di stile della libreria nel `wwwroot/index.html` file ( Blazor WebAssembly ) o nel file dell'app `Pages/_Host.cshtml` ( Blazor Server ):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>Creare una Razor libreria di classi di componenti con asset statici

Un RCL può includere asset statici. Gli asset statici sono disponibili per qualsiasi app che usa la libreria. Per altre informazioni, vedere <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>Fornire componenti e risorse statiche a più app ospitate Blazor

Per altre informazioni, vedere <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

## <a name="build-pack-and-ship-to-nuget"></a>Compilare, comprimere e spedire a NuGet

Poiché le librerie dei componenti sono librerie .NET standard, la creazione di pacchetti e la spedizione a NuGet non è diversa dalla creazione di pacchetti e dalla distribuzione di qualsiasi libreria a NuGet. La creazione di pacchetti viene eseguita usando il [`dotnet pack`](/dotnet/core/tools/dotnet-pack) comando in una shell dei comandi:

```dotnetcli
dotnet pack
```

Caricare il pacchetto in NuGet usando il [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) comando in una shell dei comandi.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:razor-pages/ui-class>
* [Aggiungere un file di configurazione del linker XML a una raccolta](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
