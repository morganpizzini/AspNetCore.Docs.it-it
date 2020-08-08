---
title: Metapacchetto Microsoft.AspNetCore.All per ASP.NET Core 2.0
author: Rick-Anderson
description: Il metapacchetto Microsoft.AspNetCore.All non è consigliato per ASP.NET Core 2.1 e versioni successive.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/metapackage
ms.openlocfilehash: 58201aed604fa53c703176e11af5d749f2b3274e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017031"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a>Metapacchetto Microsoft.AspNetCore.All per ASP.NET Core 2.0

::: moniker range=">= aspnetcore-3.0"

Il `Microsoft.AspNetCore.All` metapacchetto non è incluso in ASP.NET Core 3,0 e versioni successive. Per altre informazioni, vedere [questo problema di GitHub](https://github.com/aspnet/Announcements/issues/314).

::: moniker-end

> [!NOTE]
> È consigliabile che le applicazioni destinate a ASP.NET Core 2.1 e versioni successive usino il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) invece di questo pacchetto. Vedere [Migrazione da Microsoft.AspNetCore.All a Microsoft.AspNetCore.App](#migrate) in questo articolo.

Questa funzionalità richiede ASP.NET Core 2.x con destinazione .NET Core 2.x.

[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) è un metapacchetto che fa riferimento a un framework condiviso. Un *framework condiviso* è un set di assembly (file *DLL*) che non sono presenti nelle cartelle dell'app. Il framework condiviso deve essere installato nel computer per eseguire l'app. Per altre informazioni, vedere [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) (Il framework condiviso).

Il framework condiviso cui fa riferimento `Microsoft.AspNetCore.All` include:

* Tutti i pacchetti supportati dal team ASP.NET Core.
* Tutti i pacchetti supportati da Entity Framework Core.
* Le dipendenze interne e di terze parti usate da ASP.NET Core e da Entity Framework Core.

Tutte le funzionalità di ASP.NET Core 2.x e Entity Framework Core 2.x sono incluse nel pacchetto `Microsoft.AspNetCore.All`. I modelli di progetto predefiniti destinati ad ASP.NET Core 2.0 usano questo pacchetto.

Il numero di versione del metapacchetto `Microsoft.AspNetCore.All` rappresenta le versioni minime di ASP.NET Core e di Entity Framework Core.

Il file con estensione *csproj* seguente fa riferimento al metapacchetto `Microsoft.AspNetCore.All` per ASP.NET Core:

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a>Controllo delle versioni implicito

In ASP.NET Core 2.1 o versioni successive è possibile specificare il riferimento al pacchetto `Microsoft.AspNetCore.All` senza la versione. Quando la versione non è specificata, il SDK specifica una versione implicita (`Microsoft.NET.Sdk.Web`). È consigliabile basarsi sulla versione implicita specificata dall'SDK e non impostando in modo esplicito il numero di versione sul riferimento al pacchetto. In caso di domande su questo approccio, lasciare un commento GitHub nella pagina della [discussione per la versione implicita Microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).

La versione implicita è impostata su `major.minor.0` per le app portabili. Il meccanismo di roll forward del framework condiviso esegue l'app sulla versione compatibile più recente tra i framework condivisi installati. Per garantire che venga usata la stessa versione in fase di sviluppo, test e produzione, verificare che in tutti gli ambienti sia installata la stessa versione del framework condiviso. Per le app autonome, il numero di versione implicita è impostato sul valore `major.minor.patch` del framework condiviso nel bundle nell'SDK installato.

Il fatto di specificare un numero di versione nel riferimento del pacchetto `Microsoft.AspNetCore.All`**non** garantisce che verrà scelta la versione corrispondente del framework condiviso. Si supponga ad esempio che venga specificata la versione "2.1.1" ma che sia installata la versione "2.1.3". In tal caso, l'app userà "2.1.3". Benché non sia consigliabile, è possibile disabilitare il roll forward (patch e/o versioni secondarie). Per altre informazioni su come eseguire il roll forward dell'host dotnet e configurarne il comportamento, vedere [dotnet host rollforward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md) (Roll forward dell'host dotnet).

Il SDK del progetto deve essere impostato su `Microsoft.NET.Sdk.Web` nel file di progetto perché venga usata la versione implicita di `Microsoft.AspNetCore.All`. Se è specificato il SDK `Microsoft.NET.Sdk` (`<Project Sdk="Microsoft.NET.Sdk">` nella parte superiore del file di progetto), viene generato l'avviso seguente:

*Avviso NU1604: la dipendenza del progetto Microsoft. AspNetCore. All non contiene un limite inferiore inclusivo. Includere un limite inferiore nella versione della dipendenza per garantire risultati di ripristino coerenti.*

Si tratta di un problema noto con .NET Core 2.1 SDK, che verrà risolto in .NET Core 2.2 SDK.

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a>Migrazione da Microsoft.AspNetCore.All a Microsoft.AspNetCore.App

I pacchetti seguenti sono inclusi in `Microsoft.AspNetCore.All` ma non il pacchetto `Microsoft.AspNetCore.App`.

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

Per passare da `Microsoft.AspNetCore.All` a `Microsoft.AspNetCore.App`, se l'app usa qualsiasi API dai pacchetti sopra o pacchetti inseriti da tali pacchetti, aggiungere i riferimenti a tali pacchetti nel progetto.

Tutte le dipendenze dei pacchetti precedenti che non sono in altro modo dipendenze di `Microsoft.AspNetCore.App` non sono incluse in modo implicito. Ad esempio:

* `StackExchange.Redis` come dipendenza di `Microsoft.Extensions.Caching.Redis`
* `Microsoft.ApplicationInsights` come dipendenza di `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`

## <a name="update-aspnet-core-21"></a>Aggiornare ASP.NET Core 2.1

È consigliabile eseguire la migrazione al metapacchetto `Microsoft.AspNetCore.App` per la versione 2.1 e versioni successive. Per continuare a usare il metapacchetto `Microsoft.AspNetCore.All` e assicurarsi che venga distribuita la versione della patch più recente:

* Nei computer di sviluppo e nei server di compilazione: installare il [.NET Core SDK](https://dotnet.microsoft.com/download) più recente.
* Nei server di distribuzione: installare il [runtime di .NET Core](https://dotnet.microsoft.com/download) più recente.
 L'app eseguirà il roll forward all'ultima versione installata al riavvio dell'applicazione.
