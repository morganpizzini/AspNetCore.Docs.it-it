---
title: Interfaccia utente Razor riutilizzabile nelle librerie di classi con ASP.NET Core
author: Rick-Anderson
description: Viene illustrato come creare un'interfaccia Razor utente riutilizzabile usando visualizzazioni parziali in una libreria di classi in ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: 2c2a2c1e13b2d511ecf8c1c02c235192861fd486
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774275"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Creare un'interfaccia utente riutilizzabile usando il progetto libreria di classi Razor in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Le visualizzazioni Razor, le pagine, i controller, i modelli di pagina, i [componenti Razor](xref:blazor/class-libraries), i [componenti di visualizzazione](xref:mvc/views/view-components)e i modelli di dati possono essere incorporati in una libreria di classi Razor (RCL). La libreria di classi Razor può essere inclusa nel pacchetto e usata nuovamente. Le applicazioni possono includere la libreria di classi Razor ed eseguire l'override delle visualizzazioni e pagine in essa contenute. Quando viene trovata una visualizzazione, visualizzazione parziale o pagina Razor sia nell'app Web che nella libreria di classi Razor, il markup Razor (file con estensione *cshtml*) nell'app Web ha la precedenza.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Creare una libreria di classi contenente l'interfaccia utente Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* In Visual Studio selezionare **Crea nuovo nuovo progetto**.
* Selezionare **libreria** > di classi Razor **Avanti**.
* Assegnare un nome alla libreria (ad esempio, "RazorClassLib") > **creare**. Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.
* Se è necessario supportare le visualizzazioni, selezionare **pagine e visualizzazioni di supporto** . Per impostazione predefinita, sono supportati solo Razor Pages. Selezionare **Crea**.

Per impostazione predefinita, il modello RCL (Razor Class Library) per impostazione predefinita è lo sviluppo di componenti Razor. L'opzione **pagine e visualizzazioni di supporto** supporta pagine e viste.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Eseguire `dotnet new razorclasslib` dalla riga di comando. Ad esempio:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Per impostazione predefinita, il modello RCL (Razor Class Library) per impostazione predefinita è lo sviluppo di componenti Razor. Passare l' `--support-pages-and-views` opzione (`dotnet new razorclasslib --support-pages-and-views`) per fornire il supporto per le pagine e le visualizzazioni.

Per altre informazioni, vedere [dotnet new](/dotnet/core/tools/dotnet-new). Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.

---

Aggiungere i file Razor alla libreria di classi Razor.

I modelli di ASP.NET Core presuppongono che il contenuto RCL si trovi nella cartella *aree* . Vedere [layout di pagine RCL](#rcl-pages-layout) per creare un RCL che espone il `~/Pages` contenuto in `~/Areas/Pages`anziché.

## <a name="reference-rcl-content"></a>Contenuto RCL di riferimento

Il riferimento alla libreria di classi Razor può essere eseguito da:

* Pacchetto NuGet. Vedere [Creazione di pacchetti NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Creare e pubblicare un pacchetto NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. Vedere [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Eseguire l'override di visualizzazioni, visualizzazioni parziali e pagine

Quando viene trovata una visualizzazione, visualizzazione parziale o pagina Razor sia nell'app Web che nella libreria di classi Razor, il markup Razor (file con estensione *cshtml*) nell'app Web ha la precedenza. Ad esempio, aggiungere *app Web 1/areas/la funzionalità/Pages/Pagina1. cshtml* a app Web 1 e Pagina1 in app Web 1 avrà la precedenza su PAGINA1 in RCL.

Nel download di esempio, rinominare *WebApp1/aree/MyFeature2* in *WebApp1/aree/MyFeature* per testare la precedenza.

Copiare la visualizzazione parziali *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* in *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aggiornare il markup per indicare la nuova posizione. Compilare ed eseguire l'app per verificare quale versione del parziale sia in uso.

### <a name="rcl-pages-layout"></a>Layout di pagine RCL

Per fare riferimento al contenuto RCL come se facesse parte della cartella *pagine* dell'app Web, creare il progetto RCL con la struttura di file seguente:

* *RazorUIClassLib/pagine*
* *RazorUIClassLib/pagine/condiviso*

Si supponga che *RazorUIClassLib/Pages/Shared* contenga due file parziali: *_Header. cshtml* e *_Footer. cshtml*. È `<partial>` possibile aggiungere i tag al file *_Layout. cshtml* :

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Creare un RCL con asset statici

Un RCL può richiedere risorse statiche complementari a cui è possibile fare riferimento tramite il RCL o l'app consumer di RCL. ASP.NET Core consente di creare RCL che includono risorse statiche disponibili per un'app di consumo.

Per includere asset complementari come parte di un RCL, creare una cartella *wwwroot* nella libreria di classi e includere tutti i file necessari in tale cartella.

Quando si esegue la compressione di un RCL, tutti gli asset complementari nella cartella *wwwroot* vengono inclusi automaticamente nel pacchetto.

### <a name="exclude-static-assets"></a>Escludi asset statici

Per escludere asset statici, aggiungere il percorso di esclusione desiderato `$(DefaultItemExcludes)` al gruppo di proprietà nel file di progetto. Separare le voci con un punto`;`e virgola ().

Nell'esempio seguente, il foglio di stile *lib. CSS* nella cartella *wwwroot* non è considerato un asset statico e non è incluso nel RCL pubblicato:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Integrazione typescript

Per includere i file TypeScript in un RCL:

1. Inserire i file TypeScript (*. TS*) all'esterno della cartella *wwwroot* Inserire, ad esempio, i file in una cartella *client* .

1. Configurare l'output di compilazione TypeScript per la cartella *wwwroot* . Impostare la `TypescriptOutDir` proprietà all'interno di `PropertyGroup` un oggetto nel file di progetto:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Includere la destinazione TypeScript come dipendenza della `ResolveCurrentProjectStaticWebAssets` destinazione aggiungendo la destinazione seguente all'interno di un oggetto `PropertyGroup` nel file di progetto:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Utilizzare il contenuto da un RCL a cui si fa riferimento

I file inclusi nella cartella *wwwroot* di RCL vengono esposti a RCL o all'app consumer sotto il prefisso `_content/{LIBRARY NAME}/`. Ad esempio, una libreria denominata *Razor. Class. lib* genera un percorso al contenuto statico in `_content/Razor.Class.Lib/`. Quando si produce un pacchetto NuGet e il nome dell'assembly non è uguale all'ID del pacchetto, usare l'ID `{LIBRARY NAME}`del pacchetto per.

L'app consumer fa riferimento a risorse statiche fornite dalla libreria `<script>`con `<style>`, `<img>`, e altri tag HTML. Per l'app consumer deve essere abilitato il [supporto file statico](xref:fundamentals/static-files) in `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Quando si esegue l'app consumer dall'output di`dotnet run`compilazione (), le risorse Web statiche sono abilitate per impostazione predefinita nell'ambiente di sviluppo. Per supportare asset in altri ambienti durante l'esecuzione dall'output di compilazione `UseStaticWebAssets` , chiamare sul generatore host in *Program.cs*:

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

Quando `UseStaticWebAssets` si esegue un'app dall'output pubblicato (`dotnet publish`), la chiamata non è obbligatoria.

### <a name="multi-project-development-flow"></a>Flusso di sviluppo per più progetti

Quando viene eseguita l'app consumer:

* Gli asset nel RCL rimanere nelle cartelle originali. Gli asset non vengono spostati nell'app consumer.
* Tutte le modifiche apportate all'interno della cartella *wwwroot* di RCL vengono riflesse nell'app consumer dopo che il RCL viene ricompilato e senza ricompilare l'app consumer.

Quando viene compilato il RCL, viene generato un manifesto che descrive i percorsi di asset Web statici. L'app consumer legge il manifesto in fase di esecuzione per usare gli asset dei progetti e dei pacchetti a cui si fa riferimento. Quando si aggiunge un nuovo asset a un RCL, è necessario ricompilare il RCL per aggiornare il manifesto prima che un'app che utilizza possa accedere al nuovo asset.

### <a name="publish"></a>Pubblicazione

Quando viene pubblicata l'app, gli asset complementari di tutti i progetti e i pacchetti a cui viene fatto *wwwroot* riferimento vengono copiati nella cartella wwwroot `_content/{LIBRARY NAME}/`dell'app pubblicata in.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Le visualizzazioni Razor, le pagine, i controller, i modelli di pagina, i [componenti Razor](xref:blazor/class-libraries), i [componenti di visualizzazione](xref:mvc/views/view-components)e i modelli di dati possono essere incorporati in una libreria di classi Razor (RCL). La libreria di classi Razor può essere inclusa nel pacchetto e usata nuovamente. Le applicazioni possono includere la libreria di classi Razor ed eseguire l'override delle visualizzazioni e pagine in essa contenute. Quando viene trovata una visualizzazione, visualizzazione parziale o pagina Razor sia nell'app Web che nella libreria di classi Razor, il markup Razor (file con estensione *cshtml*) nell'app Web ha la precedenza.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

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

I modelli di ASP.NET Core presuppongono che il contenuto RCL si trovi nella cartella *aree* . Vedere [layout di pagine RCL](#rcl-pages-layout) per creare un RCL che espone il `~/Pages` contenuto in `~/Areas/Pages`anziché.

## <a name="reference-rcl-content"></a>Contenuto RCL di riferimento

Il riferimento alla libreria di classi Razor può essere eseguito da:

* Pacchetto NuGet. Vedere [Creazione di pacchetti NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Creare e pubblicare un pacchetto NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. Vedere [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Procedura dettagliata: creare un progetto RCL e usarlo da un progetto Razor Pages

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

## <a name="create-an-rcl"></a>Creare un RCL

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

* Crea l' `RazorUIClassLib` oggetto RCL.
* Crea una pagina Razor _Message e la aggiunge alla libreria di classi Razor. Il parametro `-np` crea la pagina senza un `PageModel`.
* Crea un file [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) e lo aggiunge al RCL.

Il file *_ViewStart. cshtml* è necessario per usare il layout del progetto Razor Pages (che viene aggiunto nella sezione successiva).

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

  Per altre informazioni su *_ViewImports. cshtml*, vedere [importazione di direttive condivise](xref:mvc/views/layout#importing-shared-directives)

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

Creare un'app Web Razor Pages e un file di soluzione contenente l'app Razor Pages e il RCL:

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

Passare a `/MyFeature/Page1` per verificare che la Razor libreria di classi dell'interfaccia utente sia in uso.

## <a name="override-views-partial-views-and-pages"></a>Eseguire l'override di visualizzazioni, visualizzazioni parziali e pagine

Quando si trova una visualizzazione, una visualizzazione Razor parziale o una pagina nell'app Web e nel RCL, il markup Razor (file con*estensione cshtml* ) nell'app Web ha la precedenza. Ad esempio, aggiungere *app Web 1/areas/la funzionalità/Pages/Pagina1. cshtml* a app Web 1 e Pagina1 in app Web 1 avrà la precedenza su PAGINA1 in RCL.

Nel download di esempio, rinominare *WebApp1/aree/MyFeature2* in *WebApp1/aree/MyFeature* per testare la precedenza.

Copiare la visualizzazione parziali *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* in *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aggiornare il markup per indicare la nuova posizione. Compilare ed eseguire l'app per verificare quale versione del parziale sia in uso.

### <a name="rcl-pages-layout"></a>Layout di pagine RCL

Per fare riferimento al contenuto RCL come se facesse parte della cartella *pagine* dell'app Web, creare il progetto RCL con la struttura di file seguente:

* *RazorUIClassLib/pagine*
* *RazorUIClassLib/pagine/condiviso*

Si supponga che *RazorUIClassLib/Pages/Shared* contenga due file parziali: *_Header. cshtml* e *_Footer. cshtml*. È `<partial>` possibile aggiungere i tag al file *_Layout. cshtml* :

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
