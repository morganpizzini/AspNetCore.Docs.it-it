---
title: Microsoft.AspNetCore.App metapackage for ASP.NET Core
author: Rick-Anderson
description: Framework condiviso Microsoft.AspNetCore.App
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: b30c90116f5a53ba487f88544514f36e388233d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511379"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>Microsoft.AspNetCore.App per ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

 Il framework condiviso`Microsoft.AspNetCore.App`ASP.NET Core ( ) contiene assembly sviluppati e supportati da Microsoft. `Microsoft.AspNetCore.App`viene installato quando viene installato [.NET Core 3.0 o versione successiva dell'SDK.](https://dotnet.microsoft.com/download/dotnet-core/3.0) Il *framework condiviso* è l'insieme di assembly (file*DLL)* installati nel computer e inclusi un componente di runtime e un pacchetto di destinazione. Per altre informazioni, vedere [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) (Il framework condiviso).

* I progetti `Microsoft.NET.Sdk.Web` destinati all'SDK `Microsoft.AspNetCore.App` fanno riferimento implicitamente al framework.

Non sono necessari riferimenti aggiuntivi per questi progetti:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

Il framework condiviso ASP.NET Core:

* Non include dipendenze di terze parti.
* Include tutti i pacchetti supportati dal team di ASP.NET Core.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Questa funzionalità richiede ASP.NET Core 2.x con destinazione .NET Core 2.x.

Il [metapacchetto](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [Microsoft.AspNetCore.App](/dotnet/core/packages#metapackages) per ASP.NET Core:

* Non include le dipendenze di terze parti, ad eccezione di [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) e [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/). Queste dipendenze di terze parti sono necessarie per garantire il funzionamento delle principali funzionalità dei framework.
* Include tutti i pacchetti supportati dal team di ASP.NET Core ad eccezione di quelli che contengono dipendenze di terze parti (diverse da quelle indicate in precedenza).
* Include tutti i pacchetti supportati dal team di Entity Framework Core ad eccezione di quelli che contengono dipendenze di terze parti (diverse da quelle indicate in precedenza).

Tutte le funzionalità di ASP.NET Core 2.x e Entity Framework Core 2.x sono incluse nel pacchetto `Microsoft.AspNetCore.App`. I modelli di progetto predefiniti destinati a ASP.NET Core 2.x usano questo pacchetto. È consigliabile che le applicazioni destinate a ASP.NET `Microsoft.AspNetCore.App` Core 2.x ed Entity Framework Core 2.x usano il pacchetto.

Il numero di versione del metapacchetto `Microsoft.AspNetCore.App` rappresenta le versioni minime di ASP.NET Core e di Entity Framework Core.

L'uso del metapacchetto `Microsoft.AspNetCore.App` implica restrizioni di versione che proteggono l'app:

* Se viene incluso un pacchetto che presenta una dipendenza transitiva (non diretta) in un pacchetto in `Microsoft.AspNetCore.App` e i numeri di versione sono diversi, NuGet genererà un errore.
* Altri pacchetti aggiunti all'app non possono modificare la versione dei pacchetti inclusi in `Microsoft.AspNetCore.App`.
* La coerenza della versione garantisce un'esperienza affidabile. `Microsoft.AspNetCore.App` è stato progettato per impedire le combinazioni di versioni non testate di bit correlati usati contemporaneamente nella stessa applicazione.

Le applicazioni che usano il metapacchetto `Microsoft.AspNetCore.App` sfruttano automaticamente il framework condiviso di ASP.NET Core. Quando si usa il metapacchetto `Microsoft.AspNetCore.App`, con l'applicazione **** non&mdash; vengono distribuiti asset dai pacchetti NuGet di riferimento di ASP.NET Core. Questi asset sono infatti inclusi nel framework condiviso di .NET Core. Gli asset contenuti nel framework condiviso sono precompilati per migliorare i tempi di avvio dell'applicazione. Per altre informazioni, vedere [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) (Il framework condiviso).

Il file di `Microsoft.AspNetCore.App` progetto seguente fa riferimento al metapacchetto per ASP.NET Core e rappresenta un tipico modello di ASP.NET Core 2.2:The following project file references the metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

Il markup precedente rappresenta un tipico modello ASP.NET Core 2.x.The preceding markup represents a typical ASP.NET Core 2.x template. Non specifica un numero di versione per il pacchetto di riferimento `Microsoft.AspNetCore.App`. Quando la versione non è specificata, una versione [implicita](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) viene specificata dall'SDK, vale a dire `Microsoft.NET.Sdk.Web`. È consigliabile basarsi sulla versione implicita specificata dall'SDK e non impostando in modo esplicito il numero di versione sul riferimento al pacchetto. In caso di domande su questo approccio, è possibile lasciare un commento GitHub nella pagina della [discussione per la versione implicita Microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).

La versione implicita è impostata su `major.minor.0` per le app portabili. Il meccanismo di roll forward del framework condiviso eseguirà l'app sulla versione compatibile più recente tra i framework condivisi installati. Per garantire che venga usata la stessa versione in fase di sviluppo, test e produzione, verificare che in tutti gli ambienti sia installata la stessa versione del framework condiviso. Per le app autonome, il numero di versione implicita è impostato sul valore `major.minor.patch` del framework condiviso nel bundle nell'SDK installato.

Specificando un numero di versione nel riferimento `Microsoft.AspNetCore.App`**non** si garantisce che verrà scelta la versione del framework condiviso. Si supponga, ad esempio, che venga specificata la versione "2.2.1", ma che sia installato "2.2.3". In tal caso, l'app utilizzerà "2.2.3". Benché non sia consigliabile, è possibile disabilitare il roll forward (patch e/o versioni secondarie). Per altre informazioni su come eseguire il roll forward dell'host dotnet e configurarne il comportamento, vedere [dotnet host rollforward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md) (Roll forward dell'host dotnet).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`<Project Sdk` deve essere impostato su `Microsoft.NET.Sdk.Web` per usare la versione implicita `Microsoft.AspNetCore.App`. Quando si usa `<Project Sdk="Microsoft.NET.Sdk">` (senza il carattere finale `.Web`):

* Viene generato l'avviso seguente:

  *Avviso NU1604: dipendenza del progetto Microsoft.AspNetCore.App non contiene un limite inferiore inclusivo. Includere un limite inferiore nella versione di dipendenza per garantire risultati di ripristino coerenti.*

* Si tratta di un problema noto in .NET Core 2.1 SDK.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>Aggiornare ASP.NET Core

Il  [metapacchetto](/dotnet/core/packages#metapackages)`Microsoft.AspNetCore.App` non è un pacchetto tradizionale aggiornato da NuGet. Analogamente a `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` rappresenta un runtime condiviso, con una semantica di controllo delle versioni speciale gestita all'esterno di NuGet. Per altre informazioni, vedere [Pacchetti, metapacchetti e framework](/dotnet/core/packages).

Per aggiornare ASP.NET Core:

* Nei computer di sviluppo e nei server di compilazione: scaricare e installare [.NET Core SDK](https://dotnet.microsoft.com/download).
* Nei server di distribuzione: scaricare e installare il [runtime di .NET Core](https://dotnet.microsoft.com/download).

 Le applicazioni eseguiranno il roll forward all'ultima versione installata al riavvio dell'applicazione. Non è necessario aggiornare il numero di versione di `Microsoft.AspNetCore.App` nel file di progetto. Per altre informazioni, vedere [Roll forward delle app dipendenti dal framework](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Se l'applicazione usava in precedenza `Microsoft.AspNetCore.All`, vedere [Migrazione da Microsoft.AspNetCore.All a Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).

::: moniker-end
