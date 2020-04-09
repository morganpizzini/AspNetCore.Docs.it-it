---
title: librerie di classi dei componenti ASP.NET Core Razor
author: guardrex
description: Scopri come i componenti Blazor possono essere inclusi nelle app da una libreria di componenti esterna.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218766"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>librerie di classi dei componenti ASP.NET Core Razor

Di [Simon Timms](https://github.com/stimms)

I componenti possono essere condivisi in una libreria di [classi Razor (RCL)](xref:razor-pages/ui-class) tra progetti. Una libreria di *classi di componenti Razor* può essere inclusa da:A Razor components library can be included from:

* Un altro progetto nella soluzione.
* Un pacchetto NuGet.
* Una libreria .NET di riferimento.

Così come i componenti sono tipi .NET regolari, i componenti forniti da un RCL sono normali assembly .NET.

## <a name="create-an-rcl"></a>Creare un RCLCreate an RCL

Seguire le indicazioni <xref:blazor/get-started> contenute nell'articolo per configurare l'ambiente per Blazor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Creare un nuovo progetto.
1. Selezionare **Libreria di classi Razor**. Selezionare **Avanti**.
1. Nella finestra di dialogo Crea una nuova libreria di classi **Razor** selezionare **Crea**.
1. Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito. Negli esempi di questo argomento `MyComponentLib1`viene utilizzato il nome del progetto . Selezionare **Create** (Crea).
1. Aggiungere la RCL a una soluzione:Add the RCL to a solution:
   1. Fare clic con il pulsante destro del mouse sulla soluzione. Selezionare **Aggiungi** > **progetto esistente**.
   1. Passare al file di progetto della RCL.
   1. Selezionare il file di progetto della RCL (*con estensione csproj*).
1. Aggiungere un riferimento RCL dall'app:Add a reference the RCL from the app:
   1. Fare clic con il pulsante destro del mouse sul progetto dell'app. Selezionare **Aggiungi** > **riferimento**.
   1. Selezionare il progetto RCL. Selezionare **OK**.

> [!NOTE]
> Se la casella di controllo **Pagine e viste** di supporto è selezionata durante la generazione dell'RCL dal modello, aggiungere anche un file *_Imports.razor* alla radice del progetto generato con il contenuto seguente per abilitare la creazione del componente Razor:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Aggiungere manualmente il file alla radice del progetto generato.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

1. Utilizzare il modello Libreria`razorclasslib`di classi **Razor** ( ) con il [comando dotnet new](/dotnet/core/tools/dotnet-new) in una shell dei comandi. Nell'esempio riportato di seguito `MyComponentLib1`viene creato un oggetto RCL denominato . La cartella `MyComponentLib1` che contiene viene creata automaticamente quando viene eseguito il comando:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Se `-s|--support-pages-and-views` l'opzione viene utilizzata durante la generazione dell'RCL dal modello, aggiungere anche un file *_Imports.razor* alla radice del progetto generato con il contenuto seguente per abilitare la creazione del componente Razor:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Aggiungere manualmente il file alla radice del progetto generato.

1. Per aggiungere la libreria a un progetto esistente, utilizzare il comando [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) in una shell dei comandi. Nell'esempio seguente, il valore RCL viene aggiunto all'app. Eseguire il comando seguente dalla cartella del progetto dell'app con il percorso della libreria:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Utilizzo di un componente di libreria

Per utilizzare i componenti definiti in una libreria in un altro progetto, utilizzare uno dei seguenti approcci:

* Utilizzare il nome completo del tipo con lo spazio dei nomi.
* Utilizzare la direttiva [ \@using](xref:mvc/views/razor#using) di Razor.Use Razor's using directive. I singoli componenti possono essere aggiunti per nome.

Negli esempi seguenti `MyComponentLib1` è una libreria `SalesReport` di componenti contenente un componente.

È `SalesReport` possibile fare riferimento al componente usando il nome completo del tipo con spazio dei nomi:The component can be referenced using its full type name with namespace:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

È inoltre possibile fare riferimento al componente se `@using` la libreria viene inserita nell'ambito con una direttiva:The component can also be referenced if the library is brought into scope with an directive:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Includere `@using MyComponentLib1` la direttiva nel file *_Import.razor* di primo livello per rendere i componenti della libreria disponibili per un intero progetto. Aggiungere la direttiva a un file *_Import.razor* a qualsiasi livello per applicare lo spazio dei nomi a una singola pagina o a un set di pagine all'interno di una cartella.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Creare una libreria di classi di componenti Razor con risorse staticheCreate a Razor components class library with static assets

Un RCL può includere asset statici. Le risorse statiche sono disponibili per qualsiasi app che utilizza la libreria. Per altre informazioni, vedere <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>Costruisci, impacchetta e spedisci a NuGet

Poiché le librerie di componenti sono librerie .NET standard, la confezione e la spedizione a NuGet non è diversa dalla creazione di pacchetti e la spedizione di qualsiasi libreria a NuGet.Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet. La creazione di pacchetti viene eseguita utilizzando il comando [dotnet pack](/dotnet/core/tools/dotnet-pack) in una shell dei comandi:

```dotnetcli
dotnet pack
```

Caricare il pacchetto su NuGet usando il comando [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) in una shell dei comandi.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:razor-pages/ui-class>
* [Aggiungere un file di configurazione del linker XML a una libreriaAdd an XML linker configuration file to a library](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
