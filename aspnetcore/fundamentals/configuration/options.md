---
title: Modello di opzioni in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare il modello di opzioni per rappresentare gruppi di opzioni correlate nelle app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
uid: fundamentals/configuration/options
ms.openlocfilehash: 756d3d57122642ab10ab671c9accb75975c3799d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665459"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="713bd-103">Modello di opzioni in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="713bd-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="713bd-104">Il modello di opzioni usa le classi per rappresentare i gruppi di impostazioni correlate.</span><span class="sxs-lookup"><span data-stu-id="713bd-104">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="713bd-105">Quando le [impostazioni di configurazione](xref:fundamentals/configuration/index) vengono isolate in base allo scenario in classi separate, l'app aderisce a due importanti principi di progettazione del software:</span><span class="sxs-lookup"><span data-stu-id="713bd-105">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="713bd-106">[Principio di segregazione delle interfacce (Interface Segregation Principle, ISP) o incapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Gli scenari (classi) che dipendono dalle impostazioni di configurazione dipendono solo dalle impostazioni di configurazione che usano.</span><span class="sxs-lookup"><span data-stu-id="713bd-106">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="713bd-107">[Separazione dei problemi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Le impostazioni per le diverse parti dell'app non sono dipendenti o accoppiate l'una all'altra.</span><span class="sxs-lookup"><span data-stu-id="713bd-107">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="713bd-108">Le opzioni offrono anche un meccanismo per convalidare i dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-108">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="713bd-109">Per altre informazioni, vedere la sezione [Opzioni di convalida](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="713bd-109">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="713bd-110">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="713bd-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="713bd-111">Pacchetto</span><span class="sxs-lookup"><span data-stu-id="713bd-111">Package</span></span>

<span data-ttu-id="713bd-112">Il [pacchetto Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) fa riferimento in modo implicito nelle app di ASP.NET Core.The Microsoft.Extensions.Options.ConfigurationExtensions package is implicitly referenced in ASP.NET Core apps.</span><span class="sxs-lookup"><span data-stu-id="713bd-112">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="713bd-113">Interfacce per le opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-113">Options interfaces</span></span>

<span data-ttu-id="713bd-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> consente di recuperare le opzioni e gestire le notifiche di opzioni per le istanze `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="713bd-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="713bd-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supporta gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="713bd-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="713bd-116">Notifiche di modifica</span><span class="sxs-lookup"><span data-stu-id="713bd-116">Change notifications</span></span>
* [<span data-ttu-id="713bd-117">Opzioni denominate</span><span class="sxs-lookup"><span data-stu-id="713bd-117">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="713bd-118">Configurazione ricaricabile</span><span class="sxs-lookup"><span data-stu-id="713bd-118">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="713bd-119">Invalidamento selettivo di opzioni (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="713bd-119">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="713bd-120">Gli scenari di [post-configurazione](#options-post-configuration) consentono di impostare o modificare le opzioni dopo la configurazione di tutte le <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-120">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="713bd-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> è responsabile della creazione di nuove istanze di opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="713bd-122">Include un singolo metodo <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="713bd-122">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="713bd-123">L'implementazione predefinita accetta tutte le interfacce <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrate ed esegue tutte le configurazioni, seguite dalla post-configurazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-123">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="713bd-124">Fa distinzione tra <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chiama solo l'interfaccia appropriata.</span><span class="sxs-lookup"><span data-stu-id="713bd-124">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="713bd-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> viene usata da <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> per memorizzare nella cache le istanze di `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="713bd-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="713bd-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida le istanze delle opzioni nel monitoraggio in modo che il valore venga ricalcolato (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="713bd-126">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="713bd-127">I valori possono essere introdotti manualmente con <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="713bd-127">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="713bd-128">Il metodo <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> viene usato quando tutte le istanze denominate devono essere ricreate su richiesta.</span><span class="sxs-lookup"><span data-stu-id="713bd-128">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="713bd-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> è utile negli scenari in cui le opzioni devono essere ricalcolate a ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="713bd-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="713bd-130">Per altre informazioni, vedere la sezione [Ricaricare i dati di configurazione con IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="713bd-130">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="713bd-131"><xref:Microsoft.Extensions.Options.IOptions%601> può essere usata a supporto delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-131"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="713bd-132">Tuttavia <xref:Microsoft.Extensions.Options.IOptions%601> non supporta gli scenari precedenti di <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-132">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="713bd-133">È possibile continuare a usare <xref:Microsoft.Extensions.Options.IOptions%601> in framework e librerie esistenti che già usano l'interfaccia <xref:Microsoft.Extensions.Options.IOptions%601> e non richiedono gli scenari forniti da <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-133">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="713bd-134">Configurazione delle opzioni generali</span><span class="sxs-lookup"><span data-stu-id="713bd-134">General options configuration</span></span>

<span data-ttu-id="713bd-135">La configurazione delle opzioni generali è illustrata nell'Esempio 1 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-135">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="713bd-136">Una classe di opzioni deve essere non astratta con un costruttore pubblico senza parametri.</span><span class="sxs-lookup"><span data-stu-id="713bd-136">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="713bd-137">La classe seguente, `MyOptions`, ha due proprietà, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="713bd-137">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="713bd-138">Sebbene l'impostazione dei valori predefiniti sia facoltativa, il costruttore della classe nell'esempio seguente imposta il valore predefinito di `Option1`.</span><span class="sxs-lookup"><span data-stu-id="713bd-138">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="713bd-139">`Option2` ha un valore impostato tramite l'inizializzazione diretta della proprietà (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-139">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="713bd-140">La classe `MyOptions` viene aggiunta al contenitore di servizi con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associata alla configurazione:</span><span class="sxs-lookup"><span data-stu-id="713bd-140">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="713bd-141">Il modello di pagina seguente usa l'[inserimento delle dipendenze del costruttore](xref:mvc/controllers/dependency-injection) con <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> per accedere alle impostazioni (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-141">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="713bd-142">Il file *appsettings.json* dell'esempio specifica i valori di `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="713bd-142">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="713bd-143">Quando viene eseguita l'app, il metodo `OnGet` del modello di pagina restituisce una stringa che mostra i valori delle classi delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="713bd-143">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="713bd-144">Se si usa un <xref:System.Configuration.ConfigurationBuilder> personalizzato per caricare la configurazione delle opzioni da un file di impostazioni, verificare che il percorso base sia impostato correttamente:</span><span class="sxs-lookup"><span data-stu-id="713bd-144">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="713bd-145">L'impostazione esplicita del percorso base non è necessaria se si carica la configurazione delle opzioni dal file di impostazioni tramite <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="713bd-145">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="713bd-146">Configurare opzioni semplici con un delegato</span><span class="sxs-lookup"><span data-stu-id="713bd-146">Configure simple options with a delegate</span></span>

<span data-ttu-id="713bd-147">La configurazione di opzioni semplici con un delegato è illustrata nell'Esempio 2 dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-147">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="713bd-148">Usare un delegato per impostare i valori delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-148">Use a delegate to set options values.</span></span> <span data-ttu-id="713bd-149">L'app di esempio usa la classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-149">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="713bd-150">Nel codice seguente viene aggiunto un secondo servizio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="713bd-150">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="713bd-151">Viene usato un delegato per configurare l'associazione a `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="713bd-151">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="713bd-152">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="713bd-152">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="713bd-153">È possibile aggiungere più provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-153">You can add multiple configuration providers.</span></span> <span data-ttu-id="713bd-154">I provider di configurazione sono disponibili dai pacchetti NuGet e vengono applicati nell'ordine in cui sono registrati.</span><span class="sxs-lookup"><span data-stu-id="713bd-154">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="713bd-155">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="713bd-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="713bd-156">Ogni chiamata a <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> aggiunge un servizio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="713bd-156">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="713bd-157">Nell'esempio precedente i valori di `Option1` e `Option2` sono entrambi specificati in *appsettings.json*, mentre i valori di `Option1` e `Option2` sono sottoposti a override dal delegato configurato.</span><span class="sxs-lookup"><span data-stu-id="713bd-157">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="713bd-158">Quando sono abilitati più servizi di configurazione, l'origine di configurazione più recente specificata *ha priorità* e imposta il valore di configurazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-158">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="713bd-159">Quando viene eseguita l'app, il metodo `OnGet` del modello di pagina restituisce una stringa che mostra i valori delle classi delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="713bd-159">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="713bd-160">Configurazione delle opzioni secondarie</span><span class="sxs-lookup"><span data-stu-id="713bd-160">Suboptions configuration</span></span>

<span data-ttu-id="713bd-161">La configurazione delle opzioni secondarie è illustrata nell'Esempio 3 dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-161">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="713bd-162">Le app devono creare classi di opzioni che riguardano gruppi di scenari (classi) specifici nell'app.</span><span class="sxs-lookup"><span data-stu-id="713bd-162">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="713bd-163">Le parti dell'app che richiedono valori di configurazione devono avere accesso solo ai valori di configurazione necessari.</span><span class="sxs-lookup"><span data-stu-id="713bd-163">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="713bd-164">Durante l'associazione delle opzioni alla configurazione, ogni proprietà del tipo di opzioni viene associata a una chiave di configurazione con formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="713bd-164">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="713bd-165">Ad esempio, la proprietà `MyOptions.Option1` viene associata alla chiave `Option1`, che viene letta dalla proprietà `option1` in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="713bd-165">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="713bd-166">Nel codice seguente viene aggiunto un terzo servizio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="713bd-166">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="713bd-167">`MySubOptions` viene associato alla sezione `subsection` del file *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="713bd-167">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="713bd-168">Il `GetSection` metodo <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> richiede lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="713bd-168">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="713bd-169">Il file *appsettings.json* dell'esempio definisce un membro `subsection` con chiavi per `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="713bd-169">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="713bd-170">La classe `MySubOptions` definisce le proprietà `SubOption1` e `SubOption2` per contenere i valori delle opzioni (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-170">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="713bd-171">Il metodo `OnGet` del modello di pagina restituisce una stringa con i valori delle opzioni (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-171">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="713bd-172">Quando viene eseguita l'app, il metodo `OnGet` restituisce una stringa che mostra i valori delle classi delle opzioni secondarie:</span><span class="sxs-lookup"><span data-stu-id="713bd-172">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="713bd-173">Iniezione di opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-173">Options injection</span></span>

<span data-ttu-id="713bd-174">L'inserimento di opzioni viene dimostrato come esempio 4 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-174">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="713bd-175">Iniettare <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in:</span><span class="sxs-lookup"><span data-stu-id="713bd-175">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="713bd-176">Una pagina Razor o [`@inject`](xref:mvc/views/razor#inject) una visualizzazione MVC con il Razor direttiva.</span><span class="sxs-lookup"><span data-stu-id="713bd-176">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="713bd-177">Un modello di pagina o di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-177">A page or view model.</span></span>

<span data-ttu-id="713bd-178">L'esempio seguente dell'app <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> di esempio inserisce in un modello di pagina (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-178">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="713bd-179">L'app di esempio mostra come inserire `IOptionsMonitor<MyOptions>` con una direttiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="713bd-179">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="713bd-180">Quando l'app viene eseguita, i valori delle opzioni sono visibili nella pagina di cui è stato eseguito il rendering:</span><span class="sxs-lookup"><span data-stu-id="713bd-180">When the app is run, the options values are shown in the rendered page:</span></span>

![I valori delle opzioni Option1: value1_from_json e Option2: -1 vengono caricati dal modello e tramite inserimento nella visualizzazione.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="713bd-182">Ricaricare i dati di configurazione con IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="713bd-182">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="713bd-183">Il ricaricamento <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dei dati di configurazione con è illustrato nell'esempio 5 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-183">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="713bd-184">Utilizzando <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, le opzioni vengono calcolate una volta per ogni richiesta quando si accede e memorizzate nella cache per la durata della richiesta.</span><span class="sxs-lookup"><span data-stu-id="713bd-184">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="713bd-185">La differenza `IOptionsMonitor` `IOptionsSnapshot` tra ed è che:</span><span class="sxs-lookup"><span data-stu-id="713bd-185">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="713bd-186">`IOptionsMonitor`è un [servizio singleton](xref:fundamentals/dependency-injection#singleton) che recupera i valori delle opzioni correnti in qualsiasi momento, il che è particolarmente utile nelle dipendenze singleton.</span><span class="sxs-lookup"><span data-stu-id="713bd-186">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="713bd-187">`IOptionsSnapshot`è un [servizio con ambito](xref:fundamentals/dependency-injection#scoped) e fornisce uno `IOptionsSnapshot<T>` snapshot delle opzioni al momento della costruzione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="713bd-187">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="713bd-188">Gli snapshot delle opzioni sono progettati per l'utilizzo con dipendenze temporanee e con ambito.</span><span class="sxs-lookup"><span data-stu-id="713bd-188">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="713bd-189">L'esempio seguente illustra la creazione di un nuovo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dopo le modifiche ad *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="713bd-189">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="713bd-190">Più richieste al server restituiscono valori costanti forniti dal file *appsettings.json* fino a quando il file non viene modificato e la configurazione non viene ricaricata.</span><span class="sxs-lookup"><span data-stu-id="713bd-190">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="713bd-191">L'immagine seguente illustra i valori iniziali `option1` e `option2` caricati dal file *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="713bd-191">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="713bd-192">Modificare i valori nel file *appsettings.json* in `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="713bd-192">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="713bd-193">Salvare il file *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="713bd-193">Save the *appsettings.json* file.</span></span> <span data-ttu-id="713bd-194">Aggiornare il browser per visualizzare i valori delle opzioni aggiornati:</span><span class="sxs-lookup"><span data-stu-id="713bd-194">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="713bd-195">Supporto delle opzioni denominate con IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="713bd-195">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="713bd-196">Il supporto delle opzioni denominate con <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> è illustrato nell'Esempio 6 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-196">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="713bd-197">Il supporto delle opzioni denominate consente all'app di distinguere le configurazioni delle opzioni denominate.</span><span class="sxs-lookup"><span data-stu-id="713bd-197">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="713bd-198">Nell'app di esempio le opzioni denominate vengono dichiarate con [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), che chiama il [> TOptions ConfigureNamedOptions.\< Configurare il](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="713bd-198">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="713bd-199">Per le opzioni denominate viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="713bd-199">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="713bd-200">L'app di esempio accede alle opzioni denominate con <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-200">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="713bd-201">Eseguendo l'app di esempio, vengono restituite le opzioni denominate:</span><span class="sxs-lookup"><span data-stu-id="713bd-201">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="713bd-202">I valori `named_options_1` vengono specificati dalla configurazione, caricata dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="713bd-202">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="713bd-203">I valori `named_options_2` sono specificati da:</span><span class="sxs-lookup"><span data-stu-id="713bd-203">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="713bd-204">Delegato `named_options_2` in `ConfigureServices` per `Option1`.</span><span class="sxs-lookup"><span data-stu-id="713bd-204">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="713bd-205">Valore predefinito per `Option2` specificato dalla classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="713bd-205">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="713bd-206">Configurare tutte le opzioni con il metodo ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="713bd-206">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="713bd-207">Configurare tutte le istanze delle opzioni con il metodo <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="713bd-207">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="713bd-208">Il codice seguente configura `Option1` per tutte le istanze di configurazione con un valore comune.</span><span class="sxs-lookup"><span data-stu-id="713bd-208">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="713bd-209">Aggiungere manualmente il codice seguente al metodo `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="713bd-209">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="713bd-210">L'esecuzione dell'app di esempio dopo l'aggiunta del codice produce il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="713bd-210">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="713bd-211">Tutte le opzioni sono istanze denominate.</span><span class="sxs-lookup"><span data-stu-id="713bd-211">All options are named instances.</span></span> <span data-ttu-id="713bd-212">Le istanze di <xref:Microsoft.Extensions.Options.IConfigureOptions%601> esistenti sono trattate come se fossero indirizzate all'istanza di `Options.DefaultName`, ovvero `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="713bd-212">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="713bd-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementa anche <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="713bd-214">L'implementazione predefinita di <xref:Microsoft.Extensions.Options.IOptionsFactory%601> include la logica per usarle in modo appropriato.</span><span class="sxs-lookup"><span data-stu-id="713bd-214">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="713bd-215">L'opzione denominata `null` viene usata per avere come destinazione tutte le istanze denominate anziché un'istanza specifica denominata (questa convenzione è usata da <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>).</span><span class="sxs-lookup"><span data-stu-id="713bd-215">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="713bd-216">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="713bd-216">OptionsBuilder API</span></span>

<span data-ttu-id="713bd-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> viene usata per configurare le istanze di `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="713bd-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="713bd-218">`OptionsBuilder` semplifica la creazione di opzioni denominate perché è costituita da un singolo parametro nella chiamata iniziale ad `AddOptions<TOptions>(string optionsName)` invece di essere visualizzata in tutte le chiamate successive.</span><span class="sxs-lookup"><span data-stu-id="713bd-218">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="713bd-219">La convalida delle opzioni e gli overload `ConfigureOptions` che accettano le dipendenze dei servizi sono disponibili solo tramite `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="713bd-219">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="713bd-220">Usare i servizi di inserimento delle dipendenze per configurare le opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-220">Use DI services to configure options</span></span>

<span data-ttu-id="713bd-221">È possibile accedere ad altri servizi dall'inserimento delle dipendenze durante la configurazione delle opzioni in due modi:</span><span class="sxs-lookup"><span data-stu-id="713bd-221">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="713bd-222">Passare un delegato di configurazione a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) in [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="713bd-222">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="713bd-223">`OptionsBuilder<TOptions>`fornisce overload di [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) che consentono di utilizzare fino a cinque servizi per configurare le opzioni:</span><span class="sxs-lookup"><span data-stu-id="713bd-223">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="713bd-224">Creare un tipo personalizzato che implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> o <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrare il tipo come servizio.</span><span class="sxs-lookup"><span data-stu-id="713bd-224">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="713bd-225">È consigliabile passare un delegato di configurazione a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) perché la creazione di un servizio è più complessa.</span><span class="sxs-lookup"><span data-stu-id="713bd-225">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="713bd-226">La creazione di un tipo personalizzato equivale alle operazioni che il framework esegue automaticamente quando si usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="713bd-226">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="713bd-227">La chiamata a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra un'interfaccia <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> generica temporanea, che ha un costruttore che accetta i tipi di servizi generici specificati.</span><span class="sxs-lookup"><span data-stu-id="713bd-227">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="713bd-228">Convalida delle opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-228">Options validation</span></span>

<span data-ttu-id="713bd-229">La convalida le opzioni consente di convalidare le opzioni quando vengono configurate.</span><span class="sxs-lookup"><span data-stu-id="713bd-229">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="713bd-230">Chiamare `Validate` con un metodo di convalida che restituisce `true` se le opzioni sono valide e `false` se non sono valide:</span><span class="sxs-lookup"><span data-stu-id="713bd-230">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="713bd-231">L'esempio precedente imposta l'istanza di opzioni denominata su `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="713bd-231">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="713bd-232">L'istanza di opzioni predefinita è `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="713bd-232">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="713bd-233">La convalida viene eseguita quando viene creata l'istanza di opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-233">Validation runs when the options instance is created.</span></span> <span data-ttu-id="713bd-234">È garantito che un'istanza di opzioni superi la convalida al primo accesso.</span><span class="sxs-lookup"><span data-stu-id="713bd-234">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="713bd-235">La convalida delle opzioni non protegge dalle modifiche delle opzioni dopo la creazione dell'istanza di opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-235">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="713bd-236">Ad esempio, `IOptionsSnapshot` le opzioni vengono create e convalidate una volta per ogni richiesta quando si accede per la prima volta alle opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-236">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="713bd-237">Le `IOptionsSnapshot` opzioni non vengono nuovamente convalidate nei successivi tentativi di accesso *per la stessa richiesta.*</span><span class="sxs-lookup"><span data-stu-id="713bd-237">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="713bd-238">Il metodo `Validate` accetta `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="713bd-238">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="713bd-239">Per personalizzare completamente la convalida, implementare `IValidateOptions<TOptions>`, che consente:</span><span class="sxs-lookup"><span data-stu-id="713bd-239">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="713bd-240">La convalida di più tipi di opzioni: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="713bd-240">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="713bd-241">La convalida che dipende da un altro tipo di opzione: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="713bd-241">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="713bd-242">`IValidateOptions` convalida:</span><span class="sxs-lookup"><span data-stu-id="713bd-242">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="713bd-243">Un'istanza di opzioni denominata specifica.</span><span class="sxs-lookup"><span data-stu-id="713bd-243">A specific named options instance.</span></span>
* <span data-ttu-id="713bd-244">Tutte le opzioni quando `name` è `null`.</span><span class="sxs-lookup"><span data-stu-id="713bd-244">All options when `name` is `null`.</span></span>

<span data-ttu-id="713bd-245">Restituire `ValidateOptionsResult` dall'implementazione dell'interfaccia:</span><span class="sxs-lookup"><span data-stu-id="713bd-245">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="713bd-246">La convalida basata sull'annotazione dei dati è disponibile dal <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> pacchetto `OptionsBuilder<TOptions>` [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) chiamando il metodo su .</span><span class="sxs-lookup"><span data-stu-id="713bd-246">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="713bd-247">`Microsoft.Extensions.Options.DataAnnotations`viene fatto riferimento implicitamente nelle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="713bd-247">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="713bd-248">La convalida eager (fallimento e risposta immediata agli errori all'avvio) è pianificata per una versione futura.</span><span class="sxs-lookup"><span data-stu-id="713bd-248">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="713bd-249">Post-configurazione delle opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-249">Options post-configuration</span></span>

<span data-ttu-id="713bd-250">Impostare la post-configurazione con <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-250">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="713bd-251">La post-configurazione viene eseguita dopo il completamento della configurazione di tutte le <xref:Microsoft.Extensions.Options.IConfigureOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="713bd-251">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="713bd-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> è disponibile per la post-configurazione delle opzioni denominate:</span><span class="sxs-lookup"><span data-stu-id="713bd-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="713bd-253">Usare <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> per la post-configurazione di tutte le istanze di configurazione:</span><span class="sxs-lookup"><span data-stu-id="713bd-253">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="713bd-254">Accesso alle opzioni durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="713bd-254">Accessing options during startup</span></span>

<span data-ttu-id="713bd-255"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> possono essere usate in `Startup.Configure`, perché i servizi vengono compilati prima dell'esecuzione del metodo `Configure`.</span><span class="sxs-lookup"><span data-stu-id="713bd-255"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="713bd-256">Non usare <xref:Microsoft.Extensions.Options.IOptions%601> oppure <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="713bd-256">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="713bd-257">Le opzioni potrebbero avere uno stato incoerente a causa dell'ordinamento delle registrazioni dei servizi.</span><span class="sxs-lookup"><span data-stu-id="713bd-257">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="713bd-258">Il modello di opzioni usa le classi per rappresentare i gruppi di impostazioni correlate.</span><span class="sxs-lookup"><span data-stu-id="713bd-258">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="713bd-259">Quando le [impostazioni di configurazione](xref:fundamentals/configuration/index) vengono isolate in base allo scenario in classi separate, l'app aderisce a due importanti principi di progettazione del software:</span><span class="sxs-lookup"><span data-stu-id="713bd-259">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="713bd-260">[Principio di segregazione delle interfacce (Interface Segregation Principle, ISP) o incapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Gli scenari (classi) che dipendono dalle impostazioni di configurazione dipendono solo dalle impostazioni di configurazione che usano.</span><span class="sxs-lookup"><span data-stu-id="713bd-260">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="713bd-261">[Separazione dei problemi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Le impostazioni per le diverse parti dell'app non sono dipendenti o accoppiate l'una all'altra.</span><span class="sxs-lookup"><span data-stu-id="713bd-261">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="713bd-262">Le opzioni offrono anche un meccanismo per convalidare i dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-262">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="713bd-263">Per altre informazioni, vedere la sezione [Opzioni di convalida](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="713bd-263">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="713bd-264">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="713bd-264">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="713bd-265">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="713bd-265">Prerequisites</span></span>

<span data-ttu-id="713bd-266">Fare riferimento al [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) oppure aggiungere un riferimento al pacchetto [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="713bd-266">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="713bd-267">Interfacce per le opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-267">Options interfaces</span></span>

<span data-ttu-id="713bd-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> consente di recuperare le opzioni e gestire le notifiche di opzioni per le istanze `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="713bd-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="713bd-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supporta gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="713bd-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="713bd-270">Notifiche di modifica</span><span class="sxs-lookup"><span data-stu-id="713bd-270">Change notifications</span></span>
* [<span data-ttu-id="713bd-271">Opzioni denominate</span><span class="sxs-lookup"><span data-stu-id="713bd-271">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="713bd-272">Configurazione ricaricabile</span><span class="sxs-lookup"><span data-stu-id="713bd-272">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="713bd-273">Invalidamento selettivo di opzioni (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="713bd-273">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="713bd-274">Gli scenari di [post-configurazione](#options-post-configuration) consentono di impostare o modificare le opzioni dopo la configurazione di tutte le <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-274">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="713bd-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> è responsabile della creazione di nuove istanze di opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="713bd-276">Include un singolo metodo <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="713bd-276">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="713bd-277">L'implementazione predefinita accetta tutte le interfacce <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrate ed esegue tutte le configurazioni, seguite dalla post-configurazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-277">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="713bd-278">Fa distinzione tra <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chiama solo l'interfaccia appropriata.</span><span class="sxs-lookup"><span data-stu-id="713bd-278">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="713bd-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> viene usata da <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> per memorizzare nella cache le istanze di `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="713bd-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="713bd-280"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida le istanze delle opzioni nel monitoraggio in modo che il valore venga ricalcolato (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="713bd-280">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="713bd-281">I valori possono essere introdotti manualmente con <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="713bd-281">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="713bd-282">Il metodo <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> viene usato quando tutte le istanze denominate devono essere ricreate su richiesta.</span><span class="sxs-lookup"><span data-stu-id="713bd-282">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="713bd-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> è utile negli scenari in cui le opzioni devono essere ricalcolate a ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="713bd-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="713bd-284">Per altre informazioni, vedere la sezione [Ricaricare i dati di configurazione con IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="713bd-284">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="713bd-285"><xref:Microsoft.Extensions.Options.IOptions%601> può essere usata a supporto delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-285"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="713bd-286">Tuttavia <xref:Microsoft.Extensions.Options.IOptions%601> non supporta gli scenari precedenti di <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-286">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="713bd-287">È possibile continuare a usare <xref:Microsoft.Extensions.Options.IOptions%601> in framework e librerie esistenti che già usano l'interfaccia <xref:Microsoft.Extensions.Options.IOptions%601> e non richiedono gli scenari forniti da <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-287">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="713bd-288">Configurazione delle opzioni generali</span><span class="sxs-lookup"><span data-stu-id="713bd-288">General options configuration</span></span>

<span data-ttu-id="713bd-289">La configurazione delle opzioni generali è illustrata nell'Esempio 1 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-289">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="713bd-290">Una classe di opzioni deve essere non astratta con un costruttore pubblico senza parametri.</span><span class="sxs-lookup"><span data-stu-id="713bd-290">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="713bd-291">La classe seguente, `MyOptions`, ha due proprietà, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="713bd-291">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="713bd-292">Sebbene l'impostazione dei valori predefiniti sia facoltativa, il costruttore della classe nell'esempio seguente imposta il valore predefinito di `Option1`.</span><span class="sxs-lookup"><span data-stu-id="713bd-292">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="713bd-293">`Option2` ha un valore impostato tramite l'inizializzazione diretta della proprietà (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-293">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="713bd-294">La classe `MyOptions` viene aggiunta al contenitore di servizi con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associata alla configurazione:</span><span class="sxs-lookup"><span data-stu-id="713bd-294">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="713bd-295">Il modello di pagina seguente usa l'[inserimento delle dipendenze del costruttore](xref:mvc/controllers/dependency-injection) con <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> per accedere alle impostazioni (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-295">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="713bd-296">Il file *appsettings.json* dell'esempio specifica i valori di `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="713bd-296">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="713bd-297">Quando viene eseguita l'app, il metodo `OnGet` del modello di pagina restituisce una stringa che mostra i valori delle classi delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="713bd-297">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="713bd-298">Se si usa un <xref:System.Configuration.ConfigurationBuilder> personalizzato per caricare la configurazione delle opzioni da un file di impostazioni, verificare che il percorso base sia impostato correttamente:</span><span class="sxs-lookup"><span data-stu-id="713bd-298">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="713bd-299">L'impostazione esplicita del percorso base non è necessaria se si carica la configurazione delle opzioni dal file di impostazioni tramite <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="713bd-299">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="713bd-300">Configurare opzioni semplici con un delegato</span><span class="sxs-lookup"><span data-stu-id="713bd-300">Configure simple options with a delegate</span></span>

<span data-ttu-id="713bd-301">La configurazione di opzioni semplici con un delegato è illustrata nell'Esempio 2 dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-301">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="713bd-302">Usare un delegato per impostare i valori delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-302">Use a delegate to set options values.</span></span> <span data-ttu-id="713bd-303">L'app di esempio usa la classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-303">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="713bd-304">Nel codice seguente viene aggiunto un secondo servizio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="713bd-304">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="713bd-305">Viene usato un delegato per configurare l'associazione a `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="713bd-305">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="713bd-306">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="713bd-306">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="713bd-307">È possibile aggiungere più provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-307">You can add multiple configuration providers.</span></span> <span data-ttu-id="713bd-308">I provider di configurazione sono disponibili dai pacchetti NuGet e vengono applicati nell'ordine in cui sono registrati.</span><span class="sxs-lookup"><span data-stu-id="713bd-308">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="713bd-309">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="713bd-309">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="713bd-310">Ogni chiamata a <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> aggiunge un servizio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="713bd-310">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="713bd-311">Nell'esempio precedente i valori di `Option1` e `Option2` sono entrambi specificati in *appsettings.json*, mentre i valori di `Option1` e `Option2` sono sottoposti a override dal delegato configurato.</span><span class="sxs-lookup"><span data-stu-id="713bd-311">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="713bd-312">Quando sono abilitati più servizi di configurazione, l'origine di configurazione più recente specificata *ha priorità* e imposta il valore di configurazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-312">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="713bd-313">Quando viene eseguita l'app, il metodo `OnGet` del modello di pagina restituisce una stringa che mostra i valori delle classi delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="713bd-313">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="713bd-314">Configurazione delle opzioni secondarie</span><span class="sxs-lookup"><span data-stu-id="713bd-314">Suboptions configuration</span></span>

<span data-ttu-id="713bd-315">La configurazione delle opzioni secondarie è illustrata nell'Esempio 3 dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-315">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="713bd-316">Le app devono creare classi di opzioni che riguardano gruppi di scenari (classi) specifici nell'app.</span><span class="sxs-lookup"><span data-stu-id="713bd-316">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="713bd-317">Le parti dell'app che richiedono valori di configurazione devono avere accesso solo ai valori di configurazione necessari.</span><span class="sxs-lookup"><span data-stu-id="713bd-317">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="713bd-318">Durante l'associazione delle opzioni alla configurazione, ogni proprietà del tipo di opzioni viene associata a una chiave di configurazione con formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="713bd-318">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="713bd-319">Ad esempio, la proprietà `MyOptions.Option1` viene associata alla chiave `Option1`, che viene letta dalla proprietà `option1` in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="713bd-319">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="713bd-320">Nel codice seguente viene aggiunto un terzo servizio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="713bd-320">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="713bd-321">`MySubOptions` viene associato alla sezione `subsection` del file *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="713bd-321">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="713bd-322">Il `GetSection` metodo <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> richiede lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="713bd-322">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="713bd-323">Il file *appsettings.json* dell'esempio definisce un membro `subsection` con chiavi per `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="713bd-323">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="713bd-324">La classe `MySubOptions` definisce le proprietà `SubOption1` e `SubOption2` per contenere i valori delle opzioni (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-324">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="713bd-325">Il metodo `OnGet` del modello di pagina restituisce una stringa con i valori delle opzioni (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-325">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="713bd-326">Quando viene eseguita l'app, il metodo `OnGet` restituisce una stringa che mostra i valori delle classi delle opzioni secondarie:</span><span class="sxs-lookup"><span data-stu-id="713bd-326">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="713bd-327">Iniezione di opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-327">Options injection</span></span>

<span data-ttu-id="713bd-328">L'inserimento di opzioni viene dimostrato come esempio 4 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-328">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="713bd-329">Iniettare <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in:</span><span class="sxs-lookup"><span data-stu-id="713bd-329">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="713bd-330">Una pagina Razor o [`@inject`](xref:mvc/views/razor#inject) una visualizzazione MVC con il Razor direttiva.</span><span class="sxs-lookup"><span data-stu-id="713bd-330">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="713bd-331">Un modello di pagina o di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-331">A page or view model.</span></span>

<span data-ttu-id="713bd-332">L'esempio seguente dell'app <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> di esempio inserisce in un modello di pagina (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-332">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="713bd-333">L'app di esempio mostra come inserire `IOptionsMonitor<MyOptions>` con una direttiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="713bd-333">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="713bd-334">Quando l'app viene eseguita, i valori delle opzioni sono visibili nella pagina di cui è stato eseguito il rendering:</span><span class="sxs-lookup"><span data-stu-id="713bd-334">When the app is run, the options values are shown in the rendered page:</span></span>

![I valori delle opzioni Option1: value1_from_json e Option2: -1 vengono caricati dal modello e tramite inserimento nella visualizzazione.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="713bd-336">Ricaricare i dati di configurazione con IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="713bd-336">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="713bd-337">Il ricaricamento <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dei dati di configurazione con è illustrato nell'esempio 5 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-337">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="713bd-338">Utilizzando <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, le opzioni vengono calcolate una volta per ogni richiesta quando si accede e memorizzate nella cache per la durata della richiesta.</span><span class="sxs-lookup"><span data-stu-id="713bd-338">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="713bd-339">La differenza `IOptionsMonitor` `IOptionsSnapshot` tra ed è che:</span><span class="sxs-lookup"><span data-stu-id="713bd-339">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="713bd-340">`IOptionsMonitor`è un [servizio singleton](xref:fundamentals/dependency-injection#singleton) che recupera i valori delle opzioni correnti in qualsiasi momento, il che è particolarmente utile nelle dipendenze singleton.</span><span class="sxs-lookup"><span data-stu-id="713bd-340">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="713bd-341">`IOptionsSnapshot`è un [servizio con ambito](xref:fundamentals/dependency-injection#scoped) e fornisce uno `IOptionsSnapshot<T>` snapshot delle opzioni al momento della costruzione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="713bd-341">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="713bd-342">Gli snapshot delle opzioni sono progettati per l'utilizzo con dipendenze temporanee e con ambito.</span><span class="sxs-lookup"><span data-stu-id="713bd-342">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="713bd-343">L'esempio seguente illustra la creazione di un nuovo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dopo le modifiche ad *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="713bd-343">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="713bd-344">Più richieste al server restituiscono valori costanti forniti dal file *appsettings.json* fino a quando il file non viene modificato e la configurazione non viene ricaricata.</span><span class="sxs-lookup"><span data-stu-id="713bd-344">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="713bd-345">L'immagine seguente illustra i valori iniziali `option1` e `option2` caricati dal file *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="713bd-345">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="713bd-346">Modificare i valori nel file *appsettings.json* in `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="713bd-346">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="713bd-347">Salvare il file *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="713bd-347">Save the *appsettings.json* file.</span></span> <span data-ttu-id="713bd-348">Aggiornare il browser per visualizzare i valori delle opzioni aggiornati:</span><span class="sxs-lookup"><span data-stu-id="713bd-348">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="713bd-349">Supporto delle opzioni denominate con IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="713bd-349">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="713bd-350">Il supporto delle opzioni denominate con <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> è illustrato nell'Esempio 6 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-350">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="713bd-351">Il supporto delle opzioni denominate consente all'app di distinguere le configurazioni delle opzioni denominate.</span><span class="sxs-lookup"><span data-stu-id="713bd-351">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="713bd-352">Nell'app di esempio le opzioni denominate vengono dichiarate con [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), che chiama il [> TOptions ConfigureNamedOptions.\< Configurare il](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="713bd-352">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="713bd-353">Per le opzioni denominate viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="713bd-353">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="713bd-354">L'app di esempio accede alle opzioni denominate con <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-354">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="713bd-355">Eseguendo l'app di esempio, vengono restituite le opzioni denominate:</span><span class="sxs-lookup"><span data-stu-id="713bd-355">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="713bd-356">I valori `named_options_1` vengono specificati dalla configurazione, caricata dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="713bd-356">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="713bd-357">I valori `named_options_2` sono specificati da:</span><span class="sxs-lookup"><span data-stu-id="713bd-357">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="713bd-358">Delegato `named_options_2` in `ConfigureServices` per `Option1`.</span><span class="sxs-lookup"><span data-stu-id="713bd-358">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="713bd-359">Valore predefinito per `Option2` specificato dalla classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="713bd-359">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="713bd-360">Configurare tutte le opzioni con il metodo ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="713bd-360">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="713bd-361">Configurare tutte le istanze delle opzioni con il metodo <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="713bd-361">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="713bd-362">Il codice seguente configura `Option1` per tutte le istanze di configurazione con un valore comune.</span><span class="sxs-lookup"><span data-stu-id="713bd-362">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="713bd-363">Aggiungere manualmente il codice seguente al metodo `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="713bd-363">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="713bd-364">L'esecuzione dell'app di esempio dopo l'aggiunta del codice produce il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="713bd-364">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="713bd-365">Tutte le opzioni sono istanze denominate.</span><span class="sxs-lookup"><span data-stu-id="713bd-365">All options are named instances.</span></span> <span data-ttu-id="713bd-366">Le istanze di <xref:Microsoft.Extensions.Options.IConfigureOptions%601> esistenti sono trattate come se fossero indirizzate all'istanza di `Options.DefaultName`, ovvero `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="713bd-366">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="713bd-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementa anche <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="713bd-368">L'implementazione predefinita di <xref:Microsoft.Extensions.Options.IOptionsFactory%601> include la logica per usarle in modo appropriato.</span><span class="sxs-lookup"><span data-stu-id="713bd-368">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="713bd-369">L'opzione denominata `null` viene usata per avere come destinazione tutte le istanze denominate anziché un'istanza specifica denominata (questa convenzione è usata da <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>).</span><span class="sxs-lookup"><span data-stu-id="713bd-369">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="713bd-370">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="713bd-370">OptionsBuilder API</span></span>

<span data-ttu-id="713bd-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> viene usata per configurare le istanze di `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="713bd-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="713bd-372">`OptionsBuilder` semplifica la creazione di opzioni denominate perché è costituita da un singolo parametro nella chiamata iniziale ad `AddOptions<TOptions>(string optionsName)` invece di essere visualizzata in tutte le chiamate successive.</span><span class="sxs-lookup"><span data-stu-id="713bd-372">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="713bd-373">La convalida delle opzioni e gli overload `ConfigureOptions` che accettano le dipendenze dei servizi sono disponibili solo tramite `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="713bd-373">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="713bd-374">Usare i servizi di inserimento delle dipendenze per configurare le opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-374">Use DI services to configure options</span></span>

<span data-ttu-id="713bd-375">È possibile accedere ad altri servizi dall'inserimento delle dipendenze durante la configurazione delle opzioni in due modi:</span><span class="sxs-lookup"><span data-stu-id="713bd-375">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="713bd-376">Passare un delegato di configurazione a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) in [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="713bd-376">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="713bd-377">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornisce overload di [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) che consentono di usare fino a cinque servizi per configurare le opzioni:</span><span class="sxs-lookup"><span data-stu-id="713bd-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="713bd-378">Creare un tipo personalizzato che implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> o <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrare il tipo come servizio.</span><span class="sxs-lookup"><span data-stu-id="713bd-378">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="713bd-379">È consigliabile passare un delegato di configurazione a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) perché la creazione di un servizio è più complessa.</span><span class="sxs-lookup"><span data-stu-id="713bd-379">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="713bd-380">La creazione di un tipo personalizzato equivale alle operazioni che il framework esegue automaticamente quando si usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="713bd-380">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="713bd-381">La chiamata a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra un'interfaccia <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> generica temporanea, che ha un costruttore che accetta i tipi di servizi generici specificati.</span><span class="sxs-lookup"><span data-stu-id="713bd-381">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="713bd-382">Convalida delle opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-382">Options validation</span></span>

<span data-ttu-id="713bd-383">La convalida le opzioni consente di convalidare le opzioni quando vengono configurate.</span><span class="sxs-lookup"><span data-stu-id="713bd-383">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="713bd-384">Chiamare `Validate` con un metodo di convalida che restituisce `true` se le opzioni sono valide e `false` se non sono valide:</span><span class="sxs-lookup"><span data-stu-id="713bd-384">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="713bd-385">L'esempio precedente imposta l'istanza di opzioni denominata su `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="713bd-385">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="713bd-386">L'istanza di opzioni predefinita è `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="713bd-386">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="713bd-387">La convalida viene eseguita quando viene creata l'istanza di opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-387">Validation runs when the options instance is created.</span></span> <span data-ttu-id="713bd-388">È garantito che un'istanza di opzioni superi la convalida al primo accesso.</span><span class="sxs-lookup"><span data-stu-id="713bd-388">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="713bd-389">La convalida delle opzioni non protegge dalle modifiche delle opzioni dopo la creazione dell'istanza di opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-389">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="713bd-390">Ad esempio, `IOptionsSnapshot` le opzioni vengono create e convalidate una volta per ogni richiesta quando si accede per la prima volta alle opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-390">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="713bd-391">Le `IOptionsSnapshot` opzioni non vengono nuovamente convalidate nei successivi tentativi di accesso *per la stessa richiesta.*</span><span class="sxs-lookup"><span data-stu-id="713bd-391">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="713bd-392">Il metodo `Validate` accetta `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="713bd-392">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="713bd-393">Per personalizzare completamente la convalida, implementare `IValidateOptions<TOptions>`, che consente:</span><span class="sxs-lookup"><span data-stu-id="713bd-393">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="713bd-394">La convalida di più tipi di opzioni: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="713bd-394">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="713bd-395">La convalida che dipende da un altro tipo di opzione: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="713bd-395">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="713bd-396">`IValidateOptions` convalida:</span><span class="sxs-lookup"><span data-stu-id="713bd-396">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="713bd-397">Un'istanza di opzioni denominata specifica.</span><span class="sxs-lookup"><span data-stu-id="713bd-397">A specific named options instance.</span></span>
* <span data-ttu-id="713bd-398">Tutte le opzioni quando `name` è `null`.</span><span class="sxs-lookup"><span data-stu-id="713bd-398">All options when `name` is `null`.</span></span>

<span data-ttu-id="713bd-399">Restituire `ValidateOptionsResult` dall'implementazione dell'interfaccia:</span><span class="sxs-lookup"><span data-stu-id="713bd-399">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="713bd-400">La convalida basata sull'annotazione dei dati è disponibile dal <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> pacchetto `OptionsBuilder<TOptions>` [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) chiamando il metodo su .</span><span class="sxs-lookup"><span data-stu-id="713bd-400">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="713bd-401">`Microsoft.Extensions.Options.DataAnnotations`è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="713bd-401">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="713bd-402">La convalida eager (fallimento e risposta immediata agli errori all'avvio) è pianificata per una versione futura.</span><span class="sxs-lookup"><span data-stu-id="713bd-402">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="713bd-403">Post-configurazione delle opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-403">Options post-configuration</span></span>

<span data-ttu-id="713bd-404">Impostare la post-configurazione con <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-404">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="713bd-405">La post-configurazione viene eseguita dopo il completamento della configurazione di tutte le <xref:Microsoft.Extensions.Options.IConfigureOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="713bd-405">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="713bd-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> è disponibile per la post-configurazione delle opzioni denominate:</span><span class="sxs-lookup"><span data-stu-id="713bd-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="713bd-407">Usare <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> per la post-configurazione di tutte le istanze di configurazione:</span><span class="sxs-lookup"><span data-stu-id="713bd-407">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="713bd-408">Accesso alle opzioni durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="713bd-408">Accessing options during startup</span></span>

<span data-ttu-id="713bd-409"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> possono essere usate in `Startup.Configure`, perché i servizi vengono compilati prima dell'esecuzione del metodo `Configure`.</span><span class="sxs-lookup"><span data-stu-id="713bd-409"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="713bd-410">Non usare <xref:Microsoft.Extensions.Options.IOptions%601> oppure <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="713bd-410">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="713bd-411">Le opzioni potrebbero avere uno stato incoerente a causa dell'ordinamento delle registrazioni dei servizi.</span><span class="sxs-lookup"><span data-stu-id="713bd-411">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="713bd-412">Il modello di opzioni usa le classi per rappresentare i gruppi di impostazioni correlate.</span><span class="sxs-lookup"><span data-stu-id="713bd-412">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="713bd-413">Quando le [impostazioni di configurazione](xref:fundamentals/configuration/index) vengono isolate in base allo scenario in classi separate, l'app aderisce a due importanti principi di progettazione del software:</span><span class="sxs-lookup"><span data-stu-id="713bd-413">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="713bd-414">[Principio di segregazione delle interfacce (Interface Segregation Principle, ISP) o incapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Gli scenari (classi) che dipendono dalle impostazioni di configurazione dipendono solo dalle impostazioni di configurazione che usano.</span><span class="sxs-lookup"><span data-stu-id="713bd-414">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="713bd-415">[Separazione dei problemi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Le impostazioni per le diverse parti dell'app non sono dipendenti o accoppiate l'una all'altra.</span><span class="sxs-lookup"><span data-stu-id="713bd-415">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="713bd-416">Le opzioni offrono anche un meccanismo per convalidare i dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-416">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="713bd-417">Per altre informazioni, vedere la sezione [Opzioni di convalida](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="713bd-417">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="713bd-418">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="713bd-418">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="713bd-419">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="713bd-419">Prerequisites</span></span>

<span data-ttu-id="713bd-420">Fare riferimento al [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) oppure aggiungere un riferimento al pacchetto [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="713bd-420">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="713bd-421">Interfacce per le opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-421">Options interfaces</span></span>

<span data-ttu-id="713bd-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> consente di recuperare le opzioni e gestire le notifiche di opzioni per le istanze `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="713bd-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="713bd-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supporta gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="713bd-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="713bd-424">Notifiche di modifica</span><span class="sxs-lookup"><span data-stu-id="713bd-424">Change notifications</span></span>
* [<span data-ttu-id="713bd-425">Opzioni denominate</span><span class="sxs-lookup"><span data-stu-id="713bd-425">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="713bd-426">Configurazione ricaricabile</span><span class="sxs-lookup"><span data-stu-id="713bd-426">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="713bd-427">Invalidamento selettivo di opzioni (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="713bd-427">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="713bd-428">Gli scenari di [post-configurazione](#options-post-configuration) consentono di impostare o modificare le opzioni dopo la configurazione di tutte le <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-428">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="713bd-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> è responsabile della creazione di nuove istanze di opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="713bd-430">Include un singolo metodo <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="713bd-430">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="713bd-431">L'implementazione predefinita accetta tutte le interfacce <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrate ed esegue tutte le configurazioni, seguite dalla post-configurazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-431">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="713bd-432">Fa distinzione tra <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chiama solo l'interfaccia appropriata.</span><span class="sxs-lookup"><span data-stu-id="713bd-432">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="713bd-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> viene usata da <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> per memorizzare nella cache le istanze di `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="713bd-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="713bd-434"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida le istanze delle opzioni nel monitoraggio in modo che il valore venga ricalcolato (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="713bd-434">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="713bd-435">I valori possono essere introdotti manualmente con <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="713bd-435">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="713bd-436">Il metodo <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> viene usato quando tutte le istanze denominate devono essere ricreate su richiesta.</span><span class="sxs-lookup"><span data-stu-id="713bd-436">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="713bd-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> è utile negli scenari in cui le opzioni devono essere ricalcolate a ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="713bd-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="713bd-438">Per altre informazioni, vedere la sezione [Ricaricare i dati di configurazione con IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="713bd-438">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="713bd-439"><xref:Microsoft.Extensions.Options.IOptions%601> può essere usata a supporto delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-439"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="713bd-440">Tuttavia <xref:Microsoft.Extensions.Options.IOptions%601> non supporta gli scenari precedenti di <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-440">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="713bd-441">È possibile continuare a usare <xref:Microsoft.Extensions.Options.IOptions%601> in framework e librerie esistenti che già usano l'interfaccia <xref:Microsoft.Extensions.Options.IOptions%601> e non richiedono gli scenari forniti da <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-441">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="713bd-442">Configurazione delle opzioni generali</span><span class="sxs-lookup"><span data-stu-id="713bd-442">General options configuration</span></span>

<span data-ttu-id="713bd-443">La configurazione delle opzioni generali è illustrata nell'Esempio 1 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-443">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="713bd-444">Una classe di opzioni deve essere non astratta con un costruttore pubblico senza parametri.</span><span class="sxs-lookup"><span data-stu-id="713bd-444">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="713bd-445">La classe seguente, `MyOptions`, ha due proprietà, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="713bd-445">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="713bd-446">Sebbene l'impostazione dei valori predefiniti sia facoltativa, il costruttore della classe nell'esempio seguente imposta il valore predefinito di `Option1`.</span><span class="sxs-lookup"><span data-stu-id="713bd-446">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="713bd-447">`Option2` ha un valore impostato tramite l'inizializzazione diretta della proprietà (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-447">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="713bd-448">La classe `MyOptions` viene aggiunta al contenitore di servizi con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associata alla configurazione:</span><span class="sxs-lookup"><span data-stu-id="713bd-448">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="713bd-449">Il modello di pagina seguente usa l'[inserimento delle dipendenze del costruttore](xref:mvc/controllers/dependency-injection) con <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> per accedere alle impostazioni (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-449">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="713bd-450">Il file *appsettings.json* dell'esempio specifica i valori di `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="713bd-450">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="713bd-451">Quando viene eseguita l'app, il metodo `OnGet` del modello di pagina restituisce una stringa che mostra i valori delle classi delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="713bd-451">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="713bd-452">Se si usa un <xref:System.Configuration.ConfigurationBuilder> personalizzato per caricare la configurazione delle opzioni da un file di impostazioni, verificare che il percorso base sia impostato correttamente:</span><span class="sxs-lookup"><span data-stu-id="713bd-452">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="713bd-453">L'impostazione esplicita del percorso base non è necessaria se si carica la configurazione delle opzioni dal file di impostazioni tramite <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="713bd-453">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="713bd-454">Configurare opzioni semplici con un delegato</span><span class="sxs-lookup"><span data-stu-id="713bd-454">Configure simple options with a delegate</span></span>

<span data-ttu-id="713bd-455">La configurazione di opzioni semplici con un delegato è illustrata nell'Esempio 2 dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-455">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="713bd-456">Usare un delegato per impostare i valori delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="713bd-456">Use a delegate to set options values.</span></span> <span data-ttu-id="713bd-457">L'app di esempio usa la classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-457">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="713bd-458">Nel codice seguente viene aggiunto un secondo servizio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="713bd-458">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="713bd-459">Viene usato un delegato per configurare l'associazione a `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="713bd-459">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="713bd-460">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="713bd-460">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="713bd-461">È possibile aggiungere più provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-461">You can add multiple configuration providers.</span></span> <span data-ttu-id="713bd-462">I provider di configurazione sono disponibili dai pacchetti NuGet e vengono applicati nell'ordine in cui sono registrati.</span><span class="sxs-lookup"><span data-stu-id="713bd-462">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="713bd-463">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="713bd-463">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="713bd-464">Ogni chiamata a <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> aggiunge un servizio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="713bd-464">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="713bd-465">Nell'esempio precedente i valori di `Option1` e `Option2` sono entrambi specificati in *appsettings.json*, mentre i valori di `Option1` e `Option2` sono sottoposti a override dal delegato configurato.</span><span class="sxs-lookup"><span data-stu-id="713bd-465">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="713bd-466">Quando sono abilitati più servizi di configurazione, l'origine di configurazione più recente specificata *ha priorità* e imposta il valore di configurazione.</span><span class="sxs-lookup"><span data-stu-id="713bd-466">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="713bd-467">Quando viene eseguita l'app, il metodo `OnGet` del modello di pagina restituisce una stringa che mostra i valori delle classi delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="713bd-467">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="713bd-468">Configurazione delle opzioni secondarie</span><span class="sxs-lookup"><span data-stu-id="713bd-468">Suboptions configuration</span></span>

<span data-ttu-id="713bd-469">La configurazione delle opzioni secondarie è illustrata nell'Esempio 3 dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-469">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="713bd-470">Le app devono creare classi di opzioni che riguardano gruppi di scenari (classi) specifici nell'app.</span><span class="sxs-lookup"><span data-stu-id="713bd-470">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="713bd-471">Le parti dell'app che richiedono valori di configurazione devono avere accesso solo ai valori di configurazione necessari.</span><span class="sxs-lookup"><span data-stu-id="713bd-471">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="713bd-472">Durante l'associazione delle opzioni alla configurazione, ogni proprietà del tipo di opzioni viene associata a una chiave di configurazione con formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="713bd-472">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="713bd-473">Ad esempio, la proprietà `MyOptions.Option1` viene associata alla chiave `Option1`, che viene letta dalla proprietà `option1` in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="713bd-473">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="713bd-474">Nel codice seguente viene aggiunto un terzo servizio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="713bd-474">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="713bd-475">`MySubOptions` viene associato alla sezione `subsection` del file *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="713bd-475">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="713bd-476">Il `GetSection` metodo <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> richiede lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="713bd-476">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="713bd-477">Il file *appsettings.json* dell'esempio definisce un membro `subsection` con chiavi per `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="713bd-477">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="713bd-478">La classe `MySubOptions` definisce le proprietà `SubOption1` e `SubOption2` per contenere i valori delle opzioni (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-478">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="713bd-479">Il metodo `OnGet` del modello di pagina restituisce una stringa con i valori delle opzioni (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-479">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="713bd-480">Quando viene eseguita l'app, il metodo `OnGet` restituisce una stringa che mostra i valori delle classi delle opzioni secondarie:</span><span class="sxs-lookup"><span data-stu-id="713bd-480">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="713bd-481">Opzioni fornite da un modello di visualizzazione o con l'inserimento diretto della visualizzazione</span><span class="sxs-lookup"><span data-stu-id="713bd-481">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="713bd-482">Le opzioni fornite da un modello di visualizzazione o con l'inserimento diretto della visualizzazione sono illustrate nell'Esempio 4 dell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-482">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="713bd-483">Le opzioni possono essere rese disponibili in un modello di visualizzazione oppure inserendo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> direttamente in una visualizzazione (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-483">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="713bd-484">L'app di esempio mostra come inserire `IOptionsMonitor<MyOptions>` con una direttiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="713bd-484">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="713bd-485">Quando l'app viene eseguita, i valori delle opzioni sono visibili nella pagina di cui è stato eseguito il rendering:</span><span class="sxs-lookup"><span data-stu-id="713bd-485">When the app is run, the options values are shown in the rendered page:</span></span>

![I valori delle opzioni Option1: value1_from_json e Option2: -1 vengono caricati dal modello e tramite inserimento nella visualizzazione.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="713bd-487">Ricaricare i dati di configurazione con IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="713bd-487">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="713bd-488">Il ricaricamento <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dei dati di configurazione con è illustrato nell'esempio 5 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-488">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="713bd-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supporta il ricaricamento delle opzioni con un overhead di elaborazione minimo.</span><span class="sxs-lookup"><span data-stu-id="713bd-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="713bd-490">Le opzioni vengono calcolate una volta per richiesta quando viene eseguito l'accesso e la memorizzazione nella cache per la durata della richiesta.</span><span class="sxs-lookup"><span data-stu-id="713bd-490">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="713bd-491">L'esempio seguente illustra la creazione di un nuovo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dopo le modifiche ad *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="713bd-491">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="713bd-492">Più richieste al server restituiscono valori costanti forniti dal file *appsettings.json* fino a quando il file non viene modificato e la configurazione non viene ricaricata.</span><span class="sxs-lookup"><span data-stu-id="713bd-492">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="713bd-493">L'immagine seguente illustra i valori iniziali `option1` e `option2` caricati dal file *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="713bd-493">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="713bd-494">Modificare i valori nel file *appsettings.json* in `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="713bd-494">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="713bd-495">Salvare il file *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="713bd-495">Save the *appsettings.json* file.</span></span> <span data-ttu-id="713bd-496">Aggiornare il browser per visualizzare i valori delle opzioni aggiornati:</span><span class="sxs-lookup"><span data-stu-id="713bd-496">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="713bd-497">Supporto delle opzioni denominate con IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="713bd-497">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="713bd-498">Il supporto delle opzioni denominate con <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> è illustrato nell'Esempio 6 nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="713bd-498">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="713bd-499">Il supporto delle opzioni denominate consente all'app di distinguere le configurazioni delle opzioni denominate.</span><span class="sxs-lookup"><span data-stu-id="713bd-499">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="713bd-500">Nell'app di esempio le opzioni denominate vengono dichiarate con [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), che chiama il [> TOptions ConfigureNamedOptions.\< Configurare il](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="713bd-500">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="713bd-501">Per le opzioni denominate viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="713bd-501">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="713bd-502">L'app di esempio accede alle opzioni denominate con <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="713bd-502">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="713bd-503">Eseguendo l'app di esempio, vengono restituite le opzioni denominate:</span><span class="sxs-lookup"><span data-stu-id="713bd-503">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="713bd-504">I valori `named_options_1` vengono specificati dalla configurazione, caricata dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="713bd-504">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="713bd-505">I valori `named_options_2` sono specificati da:</span><span class="sxs-lookup"><span data-stu-id="713bd-505">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="713bd-506">Delegato `named_options_2` in `ConfigureServices` per `Option1`.</span><span class="sxs-lookup"><span data-stu-id="713bd-506">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="713bd-507">Valore predefinito per `Option2` specificato dalla classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="713bd-507">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="713bd-508">Configurare tutte le opzioni con il metodo ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="713bd-508">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="713bd-509">Configurare tutte le istanze delle opzioni con il metodo <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="713bd-509">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="713bd-510">Il codice seguente configura `Option1` per tutte le istanze di configurazione con un valore comune.</span><span class="sxs-lookup"><span data-stu-id="713bd-510">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="713bd-511">Aggiungere manualmente il codice seguente al metodo `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="713bd-511">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="713bd-512">L'esecuzione dell'app di esempio dopo l'aggiunta del codice produce il risultato seguente:</span><span class="sxs-lookup"><span data-stu-id="713bd-512">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="713bd-513">Tutte le opzioni sono istanze denominate.</span><span class="sxs-lookup"><span data-stu-id="713bd-513">All options are named instances.</span></span> <span data-ttu-id="713bd-514">Le istanze di <xref:Microsoft.Extensions.Options.IConfigureOptions%601> esistenti sono trattate come se fossero indirizzate all'istanza di `Options.DefaultName`, ovvero `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="713bd-514">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="713bd-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementa anche <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="713bd-516">L'implementazione predefinita di <xref:Microsoft.Extensions.Options.IOptionsFactory%601> include la logica per usarle in modo appropriato.</span><span class="sxs-lookup"><span data-stu-id="713bd-516">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="713bd-517">L'opzione denominata `null` viene usata per avere come destinazione tutte le istanze denominate anziché un'istanza specifica denominata (questa convenzione è usata da <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>).</span><span class="sxs-lookup"><span data-stu-id="713bd-517">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="713bd-518">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="713bd-518">OptionsBuilder API</span></span>

<span data-ttu-id="713bd-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> viene usata per configurare le istanze di `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="713bd-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="713bd-520">`OptionsBuilder` semplifica la creazione di opzioni denominate perché è costituita da un singolo parametro nella chiamata iniziale ad `AddOptions<TOptions>(string optionsName)` invece di essere visualizzata in tutte le chiamate successive.</span><span class="sxs-lookup"><span data-stu-id="713bd-520">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="713bd-521">La convalida delle opzioni e gli overload `ConfigureOptions` che accettano le dipendenze dei servizi sono disponibili solo tramite `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="713bd-521">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="713bd-522">Usare i servizi di inserimento delle dipendenze per configurare le opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-522">Use DI services to configure options</span></span>

<span data-ttu-id="713bd-523">È possibile accedere ad altri servizi dall'inserimento delle dipendenze durante la configurazione delle opzioni in due modi:</span><span class="sxs-lookup"><span data-stu-id="713bd-523">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="713bd-524">Passare un delegato di configurazione a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) in [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="713bd-524">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="713bd-525">[OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornisce overload di [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) che consentono di usare fino a cinque servizi per configurare le opzioni:</span><span class="sxs-lookup"><span data-stu-id="713bd-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="713bd-526">Creare un tipo personalizzato che implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> o <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrare il tipo come servizio.</span><span class="sxs-lookup"><span data-stu-id="713bd-526">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="713bd-527">È consigliabile passare un delegato di configurazione a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) perché la creazione di un servizio è più complessa.</span><span class="sxs-lookup"><span data-stu-id="713bd-527">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="713bd-528">La creazione di un tipo personalizzato equivale alle operazioni che il framework esegue automaticamente quando si usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="713bd-528">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="713bd-529">La chiamata a [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra un'interfaccia <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> generica temporanea, che ha un costruttore che accetta i tipi di servizi generici specificati.</span><span class="sxs-lookup"><span data-stu-id="713bd-529">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="713bd-530">Post-configurazione delle opzioni</span><span class="sxs-lookup"><span data-stu-id="713bd-530">Options post-configuration</span></span>

<span data-ttu-id="713bd-531">Impostare la post-configurazione con <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="713bd-531">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="713bd-532">La post-configurazione viene eseguita dopo il completamento della configurazione di tutte le <xref:Microsoft.Extensions.Options.IConfigureOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="713bd-532">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="713bd-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> è disponibile per la post-configurazione delle opzioni denominate:</span><span class="sxs-lookup"><span data-stu-id="713bd-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="713bd-534">Usare <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> per la post-configurazione di tutte le istanze di configurazione:</span><span class="sxs-lookup"><span data-stu-id="713bd-534">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="713bd-535">Accesso alle opzioni durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="713bd-535">Accessing options during startup</span></span>

<span data-ttu-id="713bd-536"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> possono essere usate in `Startup.Configure`, perché i servizi vengono compilati prima dell'esecuzione del metodo `Configure`.</span><span class="sxs-lookup"><span data-stu-id="713bd-536"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="713bd-537">Non usare <xref:Microsoft.Extensions.Options.IOptions%601> oppure <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="713bd-537">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="713bd-538">Le opzioni potrebbero avere uno stato incoerente a causa dell'ordinamento delle registrazioni dei servizi.</span><span class="sxs-lookup"><span data-stu-id="713bd-538">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="713bd-539">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="713bd-539">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
