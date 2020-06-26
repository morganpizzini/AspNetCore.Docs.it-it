---
title: Eseguire la migrazione da ASP.NET Core 1.x alla versione 2.0
author: scottaddie
description: In questo articolo vengono illustrati i prerequisiti e i passaggi più comuni per la migrazione di un progetto di ASP.NET Core 1.x su ASP.NET 2.0 Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/index
ms.openlocfilehash: 97fe2f36aed4a2ac60a7ffc30ede5e682a838e5e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408695"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a>Eseguire la migrazione da ASP.NET Core 1.x alla versione 2.0

Di [Scott Addie](https://github.com/scottaddie)

Questo articolo illustra come aggiornare un progetto ASP.NET Core 1.x esistente ad ASP.NET Core 2.0. La migrazione dell'applicazione ad ASP.NET 2.0 Core consente di sfruttare le [molte nuove caratteristiche e i miglioramenti delle prestazioni](xref:aspnetcore-2.0).

Le applicazioni esistenti di ASP.NET Core 1.x si basano su modelli di progetto specifico della versione. Quando il framework di ASP.NET Core si evolve, i modelli del progetto fanno la stessa cosa, così come il codice di avvio in essi contenuti. Oltre ad aggiornare il framework di ASP.NET Core, è necessario aggiornare il codice dell'applicazione.

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Prerequisiti

Vedere [Introduzione ad ASP.NET Core](xref:getting-started).

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>Aggiornare Moniker della versione di .NET Framework di destinazione (TFM, Target Framework Moniker)

I progetti destinati a .NET Core devono usare il [TFM](/dotnet/standard/frameworks) di una versione successiva o uguale a .NET Core 2.0. Cercare il nodo `<TargetFramework>` nel file *csproj*, e sostituire il testo interno con `netcoreapp2.0`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

I progetti destinati a .NET Framework devono usare il TFM di una versione successiva o uguale a .NET Framework 4.6.1. Cercare il nodo `<TargetFramework>` nel file *csproj*, e sostituire il testo interno con `net461`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> .NET core 2.0 offre una quantità di superficie maggiore rispetto a .NET Core 1.x. Se il destinatario è .NET Framework esclusivamente a causa della mancanza di API in .NET Core 1.x, avere .NET Core 2.0 come destinatario potrebbe funzionare.

Se il file di progetto contiene `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>Aggiornare la versione di .NET Core SDK in global.json

Se la soluzione si basa su un file [global.json](/dotnet/core/tools/global-json) per avere come destinatario una specifica versione di .NET Core SDK, aggiornare la relativa proprietà `version` per usare la versione 2.0 installata nel computer:

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>Aggiornare i riferimenti del pacchetto

Il file *csproj* in un progetto di 1.x elenca ogni pacchetto NuGet usato dal progetto.

In un progetto ASP.NET Core 2.0 destinato a .NET Core 2.0, un singolo riferimento del [metapacchetto](xref:fundamentals/metapackage) nel file *csproj* sostituisce la raccolta di pacchetti:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

Tutte le funzionalità di ASP.NET Core 2.0 e di Entity Framework Core 2.0 sono incluse nel metapacchetto.

I progetti di ASP.NET Core 2.0 con destinazione .NET Framework devono fare riferimento a singoli pacchetti NuGet. Aggiornamento dell'attributo `Version` di ogni `<PackageReference />` nodo a 2.0.0.

Ad esempio, ecco l'elenco dei `<PackageReference />` nodi usati in un progetto ASP.NET Core 2.0 tipico destinato a .NET Framework:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>Strumenti dell'interfaccia della riga di comando di .NET Core

Nel file *.csproj* aggiornare l'attributo `Version` di ogni nodo `<DotNetCliToolReference />` a 2.0.0.

Ad esempio, ecco l'elenco degli strumenti dell'interfaccia della riga di comando usati in un progetto ASP.NET Core 2.0 tipico destinato a .NET Core 2.0:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>Rinominare la proprietà di Fallback di destinazione del pacchetto

Il file *csproj* di un progetto 1.x ha usato un nodo `PackageTargetFallback` e una variabile:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

Rinominare il nodo e la variabile a `AssetTargetFallback`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>Aggiornare il metodo Main in Program.cs

Nei progetti di 1.x, il metodo `Main` di *Program.cs* era simile al seguente:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

Nei progetti 2.0, il metodo `Main` di *Program.cs* è stato semplificato:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

L'adozione di questo nuovo modello 2.0 è altamente consigliabile ed è necessaria affinché le funzionalità di prodotto come le [Migrazioni Entity Framework (EF) Core](xref:data/ef-mvc/migrations) funzionino. Ad esempio, l'esecuzione di `Update-Database` dalla finestra della Console di Gestione pacchetti o di `dotnet ef database update` dalla riga di comando (nei progetti convertiti in ASP.NET Core 2.0) genera l'errore seguente:

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a>Aggiungere provider di configurazione

Nei progetti di 1. x viene usato il costruttore `Startup` per aggiungere i provider di configurazione a un'app. La procedura implica la creazione di un'istanza di `ConfigurationBuilder`, il caricamento di provider applicabili (variabili di ambiente, impostazioni dell'app e così via) e l'inizializzazione di un membro di `IConfigurationRoot`.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

Nell'esempio precedente viene caricato il `Configuration` membro con le impostazioni di configurazione da *appsettings.js* e qualsiasi *appSettings. \<EnvironmentName\> . file JSON* che corrisponde alla `IHostingEnvironment.EnvironmentName` Proprietà. Questi file si trovano nello stesso percorso di *Startup.cs*.

Nei progetti 2.0 il codice di configurazione boilerplate relativo ai progetti 1. x viene eseguito in background. Le variabili di ambiente e le impostazioni dell'app vengono ad esempio caricate all'avvio. Il codice *Startup.cs* equivalente viene ridotto all'inizializzazione `IConfiguration` con l'istanza inserita:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

Per rimuovere i provider predefiniti aggiunti da `WebHostBuilder.CreateDefaultBuilder`, richiamare il metodo `Clear` nella proprietà `IConfigurationBuilder.Sources` all'interno di `ConfigureAppConfiguration`. Per aggiungere di nuovo i provider, usare il metodo `ConfigureAppConfiguration`in *Program.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

La configurazione usata dal metodo `CreateDefaultBuilder` nel frammento di codice precedente può essere visualizzata [qui](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).

Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index).

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>Spostare il codice di inizializzazione del database

Nei progetti 1.x che usano EF Core 1.x un comando come ad esempio `dotnet ef migrations add` esegue le operazioni seguenti:

1. Crea un'istanza dell'istanza `Startup`
1. Richiama il metodo `ConfigureServices` per registrare tutti i servizi con inserimento di dipendenze, inclusi i tipi `DbContext`
1. Esegue le attività necessarie

Nei progetti 2.0 che usano EF Core 2.0 viene richiamato `Program.BuildWebHost` per ottenere i servizi delle applicazioni. A differenza della versione 1.x, questo ha l'effetto collaterale di richiamare `Startup.Configure`. Se l'app 1.x ha richiamato il codice di inizializzazione del database nel metodo `Configure`, possono verificarsi problemi imprevisti. Ad esempio, se il database non esiste ancora, il codice di seeding viene eseguito prima dell'esecuzione del comando delle migrazioni di EF Core. Questo problema causa l'esito negativo del comando `dotnet ef migrations list` se il database non esiste ancora.

Si consideri il codice di inizializzazione del seed 1.x seguente nel metodo `Configure` di *Startup.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

Nei progetti 2.0 spostare la chiamata `SeedData.Initialize` al metodo `Main` di *Program.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

A partire dalla versione 2.0, non è una buona prassi eseguire alcuna operazione in `BuildWebHost` ad eccezione della compilazione e della configurazione dell'host Web. Tutto ciò che riguarda l'esecuzione dell'applicazione deve essere gestito all'esterno di `BuildWebHost` &mdash; in genere nel `Main` metodo di *Program.cs*.

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a>Verifica Razor impostazione di compilazione vista

Tempi di avvio dell'applicazione più rapidi e aggregazioni pubblicate più piccole sono di importanza fondamentale per l'utente. Per questi motivi, la [ Razor compilazione delle visualizzazioni](xref:mvc/views/view-compilation) è abilitata per impostazione predefinita in ASP.NET Core 2,0.

L'impostazione della proprietà `MvcRazorCompileOnPublish` su true non è più necessaria. A meno che non si stia disattivando la compilazione della vista, la proprietà può essere rimossa dal file *csproj*.

Quando la destinazione è .NET Framework, è comunque necessario fare riferimento in modo esplicito a [Microsoft. AspNetCore. Mvc. Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation)Pacchetto NuGet ViewCompilation nel file con *estensione csproj* :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>Basarsi sulle funzionalità "light-up" di Application Insights

Un semplice programma di installazione di strumentazione delle prestazioni dell'applicazione è importante. È ora possibile basarsi sulle nuove funzionalità "light-up" di [Application Insights](/azure/application-insights/app-insights-overview) disponibili negli strumenti di Visual Studio 2017.

Per impostazione predefinita, i progetti ASP.NET Core 1.1 creati in Visual Studio 2017 hanno aggiunto Application Insights. Se non si usa Application Insights SDK direttamente, di fuori di *Program.cs* e *Startup.cs*, seguire questa procedura:

1. Se la destinazione è .NET Core, rimuovere il nodo `<PackageReference />` seguente dal file *.csproj*:

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. Se la destinazione è .NET Core, rimuovere la chiamata del metodo di estensione `UseApplicationInsights` da *Program.cs*:

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. Rimuovere la chiamata di API sul lato client di Application Insights da *_Layout.cshtml*. Comprende le due righe di codice seguenti:

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

Se si usa Application Insights SDK direttamente, continuare a farlo. Il [metapacchetto](xref:fundamentals/metapackage) 2.0 comprende la versione più recente di Application Insights, pertanto viene visualizzato un errore di downgrade del pacchetto se si fa riferimento a una versione precedente.

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a>Adottare l'autenticazione/ Identity miglioramenti

ASP.NET Core 2,0 dispone di un nuovo modello di autenticazione e di una serie di modifiche significative da ASP.NET Core Identity . Se il progetto è stato creato con account utente singoli abilitati o se è stata aggiunta manualmente l'autenticazione o Identity , vedere [eseguire la migrazione dell'autenticazione e Identity al ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Modifiche importanti apportate in ASP.NET Core 2.0](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
