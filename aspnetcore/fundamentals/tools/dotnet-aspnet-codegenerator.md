---
title: Comando dotnet aspnet-codegenerator
author: rick-anderson
description: Il comando dotnet aspnet-codegenerator esegue lo scaffolding dei progetti ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: af151b65d3fefb5863529de1cd97c6315f11ca82
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444130"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator` - Esegue il motore di scaffolding di ASP.NET Core. `dotnet aspnet-codegenerator` è necessario solo per eseguire lo scaffolding dalla riga di comando. Non è necessario per usare lo scaffolding con Visual Studio.

Questo articolo si applica a [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) e versioni successive.

## <a name="installing-aspnet-codegenerator"></a>Installazione di aspnet-codegenerator

`dotnet-aspnet-codegenerator` è uno [strumento global](/dotnet/core/tools/global-tools) che deve essere installato. Il comando seguente installa l'ultima versione stabile dello strumento `dotnet-aspnet-codegenerator`:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Il comando seguente aggiorna `dotnet-aspnet-codegenerator` alla versione stabile più recente disponibile dai .NET Core SDK installati:

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Riepilogo

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Descrizione

Il comando globale `dotnet aspnet-codegenerator` esegue il generatore di codice e il motore di scaffolding di ASP.NET Core.

## <a name="arguments"></a>Argomenti

`generator`

Il generatore di codice da eseguire. Sono disponibili i generatori seguenti:

| Generatore  | Operazione                                                            |
| ---------- | -------------------------------------------------------------------- |
| area       | [Esegue lo scaffolding di un'area](xref:mvc/controllers/areas)                      |
| controller | [Esegue lo scaffolding di un controller](xref:tutorials/first-mvc-app/adding-model)  |
| identity   | [Esegue lo scaffoldingIdentity](xref:security/authentication/scaffold-identity) |
| razorpage  | [Pagine di ponteggi Razor](xref:tutorials/razor-pages/model)            |
| vista       | [Esegue lo scaffolding di una visualizzazione](xref:mvc/views/overview)                          |

## <a name="options"></a>Opzioni

`-n|--nuget-package-dir`

Specifica la directory del pacchetto NuGet.

`-c|--configuration {Debug|Release}`

Definisce la configurazione di compilazione. Il valore predefinito è `Debug`.

`-tfm|--target-framework`

[Framework](/dotnet/standard/frameworks) di destinazione da usare. Ad esempio: `net46`.

`-b|--build-base-path`

Percorso di base di compilazione.

`-h|--help`

Stampa una breve guida per il comando.

`--no-build`

Non compila il progetto prima dell'esecuzione. Imposta anche in modo implicito il flag `--no-restore`.

`-p|--project <PATH>`

Specifica il percorso del file di progetto da eseguire: nome della cartella o percorso completo. Se non specificato, per impostazione predefinita il percorso corrisponde alla directory corrente.

## <a name="generator-options"></a>Opzioni del generatore

Nelle sezioni seguenti vengono descritte in dettaglio le opzioni disponibili per i generatori supportati:

* Area
* Controller
* Identity  
* Razorpage
* Visualizza

<a name="area"></a>

### <a name="area-options"></a>Opzioni per area

Questo strumento è progettato per i progetti Web ASP.NET Core con controller e visualizzazioni. Non è destinato alle Razor app di pagine.

Sintassi: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Il comando precedente genera le cartelle seguenti:

* *Aree*
  * *AreaNameToGenerate*
    * *Controllers*
    * *Dati*
    * *Modelli*
    * *Visualizzazioni*

<a name="ctl"></a>

### <a name="controller-options"></a>Opzioni per controller

Nella tabella seguente sono elencate le opzioni per `aspnet-codegenerator` `controller` e `razorpage` :

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

La tabella seguente contiene un elenco di opzioni specifiche per `aspnet-codegenerator controller`:

| Opzione                         | Descrizione                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| --controllerName o -name      | Nome del controller.                                                                                   |
| --useAsyncActions o -async    | Genera le azioni del controller asincrone.                                                                        |
| --noViews or -nv               | **Non** genera alcuna visualizzazione.                                                                                    |
| --restWithNoViews o -api      | Genera un controller con l'API in stile REST. Si presuppone `noViews` e qualsiasi opzione correlata alla visualizzazione viene ignorata. |
| --readWriteActions o -actions | Genera un controller con azioni di lettura/scrittura senza un modello.                                              |

Usare l'opzione `-h` per ottenere informazioni della Guida per il comando `aspnet-codegenerator controller`:

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Vedere [Eseguire lo scaffolding del modello di filmato](xref:tutorials/first-mvc-app/adding-model) per un esempio di `dotnet aspnet-codegenerator controller`.

### <a name="no-locrazorpage"></a>Razorpage

<a name="rp"></a>

RazorLe pagine possono essere personalizzate individualmente specificando il nome della nuova pagina e il modello da usare. I modelli supportati sono:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Ad esempio, il comando seguente usa il modello Edit per generare *MyEdit.cshtml* e *MyEdit.cshtml.cs*:

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

In genere, il modello e il nome di file generato non vengono specificati e vengono creati i modelli seguenti:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Nella tabella seguente sono elencate le opzioni per `aspnet-codegenerator` `razorpage` e `controller` :

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

La tabella seguente contiene un elenco di opzioni specifiche per `aspnet-codegenerator razorpage`:

| Opzione                        | Descrizione                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| --namespaceName o -namespace | Nome dello spazio dei nomi da usare per il PageModel generato                          |
| --partialView o -partial     | Genera una visualizzazione parziale. Le opzioni di layout -l e -udl vengono ignorate se viene specificata questa opzione. |
| --noPageModel o -npm         | Opzione per non generare una classe PageModel per un modello vuoto                           |

Usare l'opzione `-h` per ottenere informazioni della Guida per il comando `aspnet-codegenerator razorpage`:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Vedere [Eseguire lo scaffolding del modello di filmato](xref:tutorials/razor-pages/model) per un esempio di `dotnet aspnet-codegenerator razorpage`.

### Identity

Vedere [impalcatura Identity ](xref:security/authentication/scaffold-identity)
