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
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="6403b-103">Metapacchetto Microsoft.AspNetCore.All per ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="6403b-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6403b-104">Il `Microsoft.AspNetCore.All` metapacchetto non è incluso in ASP.NET Core 3,0 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="6403b-104">The `Microsoft.AspNetCore.All` metapackage isn't included in ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="6403b-105">Per altre informazioni, vedere [questo problema di GitHub](https://github.com/aspnet/Announcements/issues/314).</span><span class="sxs-lookup"><span data-stu-id="6403b-105">For more information, see [this GitHub issue](https://github.com/aspnet/Announcements/issues/314).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="6403b-106">È consigliabile che le applicazioni destinate a ASP.NET Core 2.1 e versioni successive usino il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) invece di questo pacchetto.</span><span class="sxs-lookup"><span data-stu-id="6403b-106">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="6403b-107">Vedere [Migrazione da Microsoft.AspNetCore.All a Microsoft.AspNetCore.App](#migrate) in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="6403b-107">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="6403b-108">Questa funzionalità richiede ASP.NET Core 2.x con destinazione .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="6403b-108">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="6403b-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) è un metapacchetto che fa riferimento a un framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="6403b-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="6403b-110">Un *framework condiviso* è un set di assembly (file *DLL*) che non sono presenti nelle cartelle dell'app.</span><span class="sxs-lookup"><span data-stu-id="6403b-110">A *shared framework* is a set of assemblies (*.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="6403b-111">Il framework condiviso deve essere installato nel computer per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="6403b-111">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="6403b-112">Per altre informazioni, vedere [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) (Il framework condiviso).</span><span class="sxs-lookup"><span data-stu-id="6403b-112">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="6403b-113">Il framework condiviso cui fa riferimento `Microsoft.AspNetCore.All` include:</span><span class="sxs-lookup"><span data-stu-id="6403b-113">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="6403b-114">Tutti i pacchetti supportati dal team ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6403b-114">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="6403b-115">Tutti i pacchetti supportati da Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6403b-115">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="6403b-116">Le dipendenze interne e di terze parti usate da ASP.NET Core e da Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6403b-116">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="6403b-117">Tutte le funzionalità di ASP.NET Core 2.x e Entity Framework Core 2.x sono incluse nel pacchetto `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="6403b-117">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="6403b-118">I modelli di progetto predefiniti destinati ad ASP.NET Core 2.0 usano questo pacchetto.</span><span class="sxs-lookup"><span data-stu-id="6403b-118">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="6403b-119">Il numero di versione del metapacchetto `Microsoft.AspNetCore.All` rappresenta le versioni minime di ASP.NET Core e di Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6403b-119">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="6403b-120">Il file con estensione *csproj* seguente fa riferimento al metapacchetto `Microsoft.AspNetCore.All` per ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6403b-120">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="6403b-121">Controllo delle versioni implicito</span><span class="sxs-lookup"><span data-stu-id="6403b-121">Implicit versioning</span></span>

<span data-ttu-id="6403b-122">In ASP.NET Core 2.1 o versioni successive è possibile specificare il riferimento al pacchetto `Microsoft.AspNetCore.All` senza la versione.</span><span class="sxs-lookup"><span data-stu-id="6403b-122">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="6403b-123">Quando la versione non è specificata, il SDK specifica una versione implicita (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="6403b-123">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="6403b-124">È consigliabile basarsi sulla versione implicita specificata dall'SDK e non impostando in modo esplicito il numero di versione sul riferimento al pacchetto.</span><span class="sxs-lookup"><span data-stu-id="6403b-124">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="6403b-125">In caso di domande su questo approccio, lasciare un commento GitHub nella pagina della [discussione per la versione implicita Microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="6403b-125">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="6403b-126">La versione implicita è impostata su `major.minor.0` per le app portabili.</span><span class="sxs-lookup"><span data-stu-id="6403b-126">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="6403b-127">Il meccanismo di roll forward del framework condiviso esegue l'app sulla versione compatibile più recente tra i framework condivisi installati.</span><span class="sxs-lookup"><span data-stu-id="6403b-127">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="6403b-128">Per garantire che venga usata la stessa versione in fase di sviluppo, test e produzione, verificare che in tutti gli ambienti sia installata la stessa versione del framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="6403b-128">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="6403b-129">Per le app autonome, il numero di versione implicita è impostato sul valore `major.minor.patch` del framework condiviso nel bundle nell'SDK installato.</span><span class="sxs-lookup"><span data-stu-id="6403b-129">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="6403b-130">Il fatto di specificare un numero di versione nel riferimento del pacchetto `Microsoft.AspNetCore.All`**non** garantisce che verrà scelta la versione corrispondente del framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="6403b-130">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="6403b-131">Si supponga ad esempio che venga specificata la versione "2.1.1" ma che sia installata la versione "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="6403b-131">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="6403b-132">In tal caso, l'app userà "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="6403b-132">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="6403b-133">Benché non sia consigliabile, è possibile disabilitare il roll forward (patch e/o versioni secondarie).</span><span class="sxs-lookup"><span data-stu-id="6403b-133">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="6403b-134">Per altre informazioni su come eseguire il roll forward dell'host dotnet e configurarne il comportamento, vedere [dotnet host rollforward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md) (Roll forward dell'host dotnet).</span><span class="sxs-lookup"><span data-stu-id="6403b-134">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="6403b-135">Il SDK del progetto deve essere impostato su `Microsoft.NET.Sdk.Web` nel file di progetto perché venga usata la versione implicita di `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="6403b-135">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="6403b-136">Se è specificato il SDK `Microsoft.NET.Sdk` (`<Project Sdk="Microsoft.NET.Sdk">` nella parte superiore del file di progetto), viene generato l'avviso seguente:</span><span class="sxs-lookup"><span data-stu-id="6403b-136">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="6403b-137">*Avviso NU1604: la dipendenza del progetto Microsoft. AspNetCore. All non contiene un limite inferiore inclusivo. Includere un limite inferiore nella versione della dipendenza per garantire risultati di ripristino coerenti.*</span><span class="sxs-lookup"><span data-stu-id="6403b-137">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="6403b-138">Si tratta di un problema noto con .NET Core 2.1 SDK, che verrà risolto in .NET Core 2.2 SDK.</span><span class="sxs-lookup"><span data-stu-id="6403b-138">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="6403b-139">Migrazione da Microsoft.AspNetCore.All a Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="6403b-139">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="6403b-140">I pacchetti seguenti sono inclusi in `Microsoft.AspNetCore.All` ma non il pacchetto `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="6403b-140">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

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

<span data-ttu-id="6403b-141">Per passare da `Microsoft.AspNetCore.All` a `Microsoft.AspNetCore.App`, se l'app usa qualsiasi API dai pacchetti sopra o pacchetti inseriti da tali pacchetti, aggiungere i riferimenti a tali pacchetti nel progetto.</span><span class="sxs-lookup"><span data-stu-id="6403b-141">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="6403b-142">Tutte le dipendenze dei pacchetti precedenti che non sono in altro modo dipendenze di `Microsoft.AspNetCore.App` non sono incluse in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="6403b-142">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="6403b-143">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="6403b-143">For example:</span></span>

* <span data-ttu-id="6403b-144">`StackExchange.Redis` come dipendenza di `Microsoft.Extensions.Caching.Redis`</span><span class="sxs-lookup"><span data-stu-id="6403b-144">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="6403b-145">`Microsoft.ApplicationInsights` come dipendenza di `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span><span class="sxs-lookup"><span data-stu-id="6403b-145">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="6403b-146">Aggiornare ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="6403b-146">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="6403b-147">È consigliabile eseguire la migrazione al metapacchetto `Microsoft.AspNetCore.App` per la versione 2.1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="6403b-147">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="6403b-148">Per continuare a usare il metapacchetto `Microsoft.AspNetCore.All` e assicurarsi che venga distribuita la versione della patch più recente:</span><span class="sxs-lookup"><span data-stu-id="6403b-148">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="6403b-149">Nei computer di sviluppo e nei server di compilazione: installare il [.NET Core SDK](https://dotnet.microsoft.com/download) più recente.</span><span class="sxs-lookup"><span data-stu-id="6403b-149">On development machines and build servers: Install the latest [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="6403b-150">Nei server di distribuzione: installare il [runtime di .NET Core](https://dotnet.microsoft.com/download) più recente.</span><span class="sxs-lookup"><span data-stu-id="6403b-150">On deployment servers: Install the latest [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>
 <span data-ttu-id="6403b-151">L'app eseguirà il roll forward all'ultima versione installata al riavvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6403b-151">Your app will roll forward to the latest installed version on an application restart.</span></span>
