---
title: Interfaccia utente Razor riutilizzabile nelle librerie di classi con ASP.NET Core
author: Rick-Anderson
description: Viene illustrato come creare un'interfaccia utente Razor riutilizzabile usando visualizzazioni parziali in una libreria di classi in ASP.NET Core.Explains how to create reusable Razor UI using partial views in a class library in ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667566"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Creare un'interfaccia utente riutilizzabile usando il progetto di libreria di classi Razor in ASP.NET CoreCreate reusable UI using the Razor class library project in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

In una libreria di classi Razor (RCL) è possibile compilare visualizzazioni Razor, controller, modelli di pagina, [componenti Razor](xref:blazor/class-libraries), [Visualizza componenti](xref:mvc/views/view-components)e modelli di dati. La libreria di classi Razor può essere inclusa nel pacchetto e usata nuovamente. Le applicazioni possono includere la libreria di classi Razor ed eseguire l'override delle visualizzazioni e pagine in essa contenute. Quando viene trovata una visualizzazione, visualizzazione parziale o pagina Razor sia nell'app Web che nella libreria di classi Razor, il markup Razor (file con estensione *cshtml*) nell'app Web ha la precedenza.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Creare una libreria di classi contenente l'interfaccia utente Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* In Visual Studio selezionare **Crea nuovo progetto**.
* Selezionare **Razor Class Library** > **Next**.
* Assegnare alla libreria il nome (ad esempio, "RazorClassLib"), > **Create**. Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.
* Selezionare **Pagine e visualizzazioni** di supporto se è necessario supportare le visualizzazioni. Per impostazione predefinita, sono supportate solo le pagine Razor.By default, only Razor Pages are supported. Selezionare **Create** (Crea).

Il modello Razor libreria di classi (RCL) per impostazione predefinita è Razor component development per impostazione predefinita. L'opzione **Pagine e visualizzazioni** di supporto supporta le pagine e le visualizzazioni.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Eseguire `dotnet new razorclasslib` dalla riga di comando. Ad esempio:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Il modello Razor libreria di classi (RCL) per impostazione predefinita è Razor component development per impostazione predefinita. Passare `--support-pages-and-views` l'opzione (`dotnet new razorclasslib --support-pages-and-views`) per fornire supporto per pagine e visualizzazioni.

Per altre informazioni, vedere [dotnet new](/dotnet/core/tools/dotnet-new). Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.

---

Aggiungere i file Razor alla libreria di classi Razor.

I modelli ASP.NET Core presuppongono che il contenuto RCL si trova nella cartella *Aree.* Vedere [Layout delle pagine RCL](#rcl-pages-layout) per creare `~/Pages` un `~/Areas/Pages`RCL che espone il contenuto anziché .

## <a name="reference-rcl-content"></a>Contenuto RCL di riferimento

Il riferimento alla libreria di classi Razor può essere eseguito da:

* Pacchetto NuGet. Vedere [Creazione di pacchetti NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Creare e pubblicare un pacchetto NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. Vedere [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Eseguire l'override di visualizzazioni, visualizzazioni parziali e pagine

Quando viene trovata una visualizzazione, visualizzazione parziale o pagina Razor sia nell'app Web che nella libreria di classi Razor, il markup Razor (file con estensione *cshtml*) nell'app Web ha la precedenza. Ad esempio, aggiungere *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* a WebApp1 e Page1 in WebApp1 avrà la precedenza su Page1 nell'RCL.

Nel download di esempio, rinominare *WebApp1/aree/MyFeature2* in *WebApp1/aree/MyFeature* per testare la precedenza.

Copiare la visualizzazione parziali *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* in *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aggiornare il markup per indicare la nuova posizione. Compilare ed eseguire l'app per verificare quale versione del parziale sia in uso.

### <a name="rcl-pages-layout"></a>Layout Pagine RCL

Per fare riferimento al contenuto RCL come se fa parte della cartella *Pages* dell'app Web, creare il progetto RCL con la struttura di file seguente:

* *RazorUIClassLib/Pagine*
* *RazorUIClassLib/Pagine/Condiviso*

Si supponga che *RazorUIClassLib/Pages/Shared* contenga due file parziali: *_Header.cshtml* e *_Footer.cshtml*. I `<partial>` tag possono essere aggiunti al file *_Layout.cshtml:*

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Creare un RCL con asset staticiCreate an RCL with static assets

Una libreria RCL può richiedere risorse statiche complementari a cui è possibile fare riferimento con la RCL o l'app consumer di RCL. ASP.NET Core consente di creare RCL che includono risorse statiche disponibili per un'app consumer.

Per includere risorse complementari come parte di una RCL, create una cartella *wwwroot* nella libreria di classi e includete tutti i file necessari in tale cartella.

Quando si crea l'imballaggio di una RCL, tutte le risorse complementari nella cartella *wwwroot* vengono automaticamente incluse nel pacchetto.

### <a name="exclude-static-assets"></a>Escludere le risorse statiche

Per escludere le risorse statiche, `$(DefaultItemExcludes)` aggiungete il percorso di esclusione desiderato al gruppo di proprietà nel file di progetto. Separare le voci con`;`un punto e virgola ( ).

Nell'esempio seguente, il foglio di stile *lib.css* nella cartella *wwwroot* non è considerato una risorsa statica e non è incluso nella RCL pubblicata:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Integrazione typescript

Per includere i file TypeScript in un RCL:

1. Inserire i file TypeScript (*.ts*) all'esterno della cartella *wwwroot.* Ad esempio, inserire i file in una cartella *Client.*

1. Configurare l'output di compilazione TypeScript per la cartella *wwwroot.* Impostare `TypescriptOutDir` la proprietà `PropertyGroup` all'interno di un nel file di progetto:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Includere la destinazione TypeScript come `ResolveCurrentProjectStaticWebAssets` dipendenza della destinazione aggiungendo la `PropertyGroup` destinazione seguente all'interno di un nel file di progetto:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Utilizzo di contenuto da una RCL di riferimentoConsume content from a referenced RCL

I file inclusi nella cartella *wwwroot* della RCL sono esposti alla RCL o all'app consumer sotto il prefisso `_content/{LIBRARY NAME}/`. Ad esempio, una libreria denominata *Razor.Class.Lib* determina `_content/Razor.Class.Lib/`un percorso al contenuto statico in corrispondenza di . Quando si produce un pacchetto NuGet e il nome dell'assembly non corrisponde `{LIBRARY NAME}`all'ID del pacchetto, utilizzare l'ID del pacchetto per .

L'app consumer fa riferimento a `<script>` `<style>`risorse `<img>`statiche fornite dalla libreria con tag , , e altri tag HTML. L'app consumer deve avere `Startup.Configure`il supporto dei file [statici](xref:fundamentals/static-files) abilitato in:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Quando si esegue l'app`dotnet run`consumer dall'output di compilazione ( ), gli asset Web statici sono abilitati per impostazione predefinita nell'ambiente di sviluppo. Per supportare gli asset in altri `UseStaticWebAssets` ambienti durante l'esecuzione dall'output di compilazione, chiamare il generatore host in *Program.cs:*

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

La `UseStaticWebAssets` chiamata non è necessaria quando si`dotnet publish`esegue un'app dall'output pubblicato ( ).

### <a name="multi-project-development-flow"></a>Flusso di sviluppo multiprogetto

Quando viene eseguita l'app consumer:

* Le risorse nella RCL rimangono nelle cartelle originali. Le risorse non vengono spostate nell'app consumer.
* Qualsiasi modifica all'interno della cartella *wwwroot* della RCL si riflette nell'app consumer dopo la ricompilazione della RCL e senza ricompilare l'app consumer.

Quando viene compilato il controllo RCL, viene prodotto un manifesto che descrive i percorsi delle risorse Web statiche. L'app consumer legge il manifesto in fase di esecuzione per usare gli asset dai progetti e dai pacchetti a cui si fa riferimento. Quando un nuovo asset viene aggiunto a una RCL, la libreria RCL deve essere ricompilata per aggiornare il manifesto prima che un'app consumer possa accedere al nuovo asset.

### <a name="publish"></a>Pubblica

Quando l'app viene pubblicata, le risorse complementari di tutti i progetti e `_content/{LIBRARY NAME}/`i pacchetti di riferimento vengono copiate nella cartella *wwwroot* dell'app pubblicata in .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In una libreria di classi Razor (RCL) è possibile compilare visualizzazioni Razor, controller, modelli di pagina, [componenti Razor](xref:blazor/class-libraries), [Visualizza componenti](xref:mvc/views/view-components)e modelli di dati. La libreria di classi Razor può essere inclusa nel pacchetto e usata nuovamente. Le applicazioni possono includere la libreria di classi Razor ed eseguire l'override delle visualizzazioni e pagine in essa contenute. Quando viene trovata una visualizzazione, visualizzazione parziale o pagina Razor sia nell'app Web che nella libreria di classi Razor, il markup Razor (file con estensione *cshtml*) nell'app Web ha la precedenza.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Creare una libreria di classi contenente l'interfaccia utente Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.
* Selezionare **Applicazione Web ASP.NET Core**.
* Assegnare un nome di libreria (ad esempio, "RazorClassLib") > **OK**. Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.
* Verificare che sia selezionato **ASP.NET Core 2.1** o versioni successive.
* Selezionare **Razor Class Library (Libreria di classi Razor)** > **OK**.

Un RCL ha il seguente file di progetto:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Eseguire `dotnet new razorclasslib` dalla riga di comando. Ad esempio:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Per altre informazioni, vedere [dotnet new](/dotnet/core/tools/dotnet-new). Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.

---

Aggiungere i file Razor alla libreria di classi Razor.

I modelli ASP.NET Core presuppongono che il contenuto RCL si trova nella cartella *Aree.* Vedere [Layout delle pagine RCL](#rcl-pages-layout) per creare `~/Pages` un `~/Areas/Pages`RCL che espone il contenuto anziché .

## <a name="reference-rcl-content"></a>Contenuto RCL di riferimento

Il riferimento alla libreria di classi Razor può essere eseguito da:

* Pacchetto NuGet. Vedere [Creazione di pacchetti NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Creare e pubblicare un pacchetto NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. Vedere [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Procedura dettagliata: Creare un progetto RCL e usare da un progetto Razor PagesWalkthrough: Create an RCL project and use from a Razor Pages project

È possibile scaricare il [progetto completo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testarlo anziché crearlo. Il download di esempio contiene codice aggiuntivo e collegamenti che rendono più semplice testare il progetto. È possibile lasciare commenti e suggerimenti in [questa discussione su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098) con i commenti sui download di esempio rispetto alle istruzioni dettagliate.

### <a name="test-the-download-app"></a>Testare l'app di download

Se non è stata scaricata l'app completa e si vuole invece creare il progetto della procedura dettagliata, passare alla [prossima sezione](#create-an-rcl).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aprire il file con estensione *sln* in Visual Studio. Eseguire l'app.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Al prompt dei comandi nella directory *cli*, compilare la libreria di classi Razor e l'app Web.

```dotnetcli
dotnet build
```

Passare alla directory *WebApp1* ed eseguire l'app:

```dotnetcli
dotnet run
```

---

Seguire le istruzioni indicate in [Testare WebApp1](#test-webapp1)

## <a name="create-an-rcl"></a>Creare un RCLCreate an RCL

In questa sezione viene creato un RCL. I file Razor vengono aggiunti alla libreria di classi Razor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Creare il progetto di libreria di classi Razor:

* Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.
* Selezionare **Applicazione Web ASP.NET Core**.
* Denominare l'app **RazorUIClassLib** > **OK**.
* Verificare che sia selezionato **ASP.NET Core 2.1** o versioni successive.
* Selezionare **Razor Class Library (Libreria di classi Razor)** > **OK**.
* Aggiungere un file di visualizzazione parziale Razor denominato *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Eseguire il comando seguente dalla riga di comando:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

I comandi precedenti:

* Crea `RazorUIClassLib` l'RCL.
* Crea una pagina Razor _Message e la aggiunge alla libreria di classi Razor. Il parametro `-np` crea la pagina senza un `PageModel`.
* Crea un file [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) e lo aggiunge all'RCL.

Il file *_ViewStart.cshtml* è necessario per utilizzare il layout del progetto Razor Pages (che viene aggiunto nella sezione successiva).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Aggiungere file e cartelle Razor al progetto

* Sostituire il markup *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* con il codice seguente:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Sostituire il markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* con il codice seguente:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` è necessario per usare la visualizzazione parziale (`<partial name="_Message" />`). Anziché includere la direttiva `@addTagHelper`, è possibile aggiungere un file *_ViewImports.cshtml*. Ad esempio:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Per ulteriori informazioni su _ViewImports.cshtml , vedere [Importazione di direttive condiviseFor more](xref:mvc/views/layout#importing-shared-directives) information on *_ViewImports.cshtml*, see Importing Shared Directives

* Compilare la libreria di classi per verificare che non siano presenti errori del compilatore:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

L'output di compilazione contiene *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* contiene il contenuto Razor compilato.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Usare la libreria dell'interfaccia utente Razor da un progetto Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Creare l'app Web di Razor Pages:

* In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla soluzione > **Aggiungi** >  **Nuovo progetto**.
* Selezionare **Applicazione Web ASP.NET Core**.
* Denominare l'app **WebApp1**.
* Verificare che sia selezionato **ASP.NET Core 2.1** o versioni successive.
* Selezionare **Applicazione Web** > **OK**.

* In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **WebApp1** e selezionare **Imposta come progetto di avvio**.
* In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **WebApp1** e selezionare **Dipendenze di compilazione** > **Dipendenze progetto**.
* Selezionare **RazorUIClassLib** come una dipendenza di **WebApp1**.
* In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **WebApp1** e scegliere **Aggiungi** > **Riferimento**.
* Nella finestra di dialogo **Gestione riferimenti** selezionare **RazorUIClassLib** > **OK**.

Eseguire l'app.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Creare un'app Web Razor Pages e un file di soluzione contenente l'app Razor Pages e la rcL:

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Compilare ed eseguire l'app Web:

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>Testare WebApp1

Passare `/MyFeature/Page1` a per verificare che la libreria di classi dell'interfaccia utente Razor sia in uso.

## <a name="override-views-partial-views-and-pages"></a>Eseguire l'override di visualizzazioni, visualizzazioni parziali e pagine

Quando viene trovata una visualizzazione, visualizzazione parziale o pagina Razor sia nell'app Web che nella libreria di classi Razor, il markup Razor (file con estensione *cshtml*) nell'app Web ha la precedenza. Ad esempio, aggiungere *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* a WebApp1 e Page1 in WebApp1 avrà la precedenza su Page1 nell'RCL.

Nel download di esempio, rinominare *WebApp1/aree/MyFeature2* in *WebApp1/aree/MyFeature* per testare la precedenza.

Copiare la visualizzazione parziali *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* in *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aggiornare il markup per indicare la nuova posizione. Compilare ed eseguire l'app per verificare quale versione del parziale sia in uso.

### <a name="rcl-pages-layout"></a>Layout Pagine RCL

Per fare riferimento al contenuto RCL come se fa parte della cartella *Pages* dell'app Web, creare il progetto RCL con la struttura di file seguente:

* *RazorUIClassLib/Pagine*
* *RazorUIClassLib/Pagine/Condiviso*

Si supponga che *RazorUIClassLib/Pages/Shared* contenga due file parziali: *_Header.cshtml* e *_Footer.cshtml*. I `<partial>` tag possono essere aggiunti al file *_Layout.cshtml:*

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/class-libraries>
