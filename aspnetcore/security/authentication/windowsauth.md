---
title: Configurare l'autenticazione di Windows in ASP.NET Core
author: scottaddie
description: Informazioni su come configurare l'autenticazione di Windows in ASP.NET Core per IIS e HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330684"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="e07e9-103">Configurare l'autenticazione di Windows in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e07e9-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="e07e9-104">Di [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="e07e9-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e07e9-105">L'autenticazione di Windows (nota anche come Negotiate, Kerberos o NTLM) può essere configurata per ASP.NET Core app ospitate con [IIS](xref:host-and-deploy/iis/index), [gheppio](xref:fundamentals/servers/kestrel)o [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="e07e9-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e07e9-106">L'autenticazione di Windows (nota anche come Negotiate, Kerberos o NTLM) può essere configurata per ASP.NET Core app ospitate con [IIS](xref:host-and-deploy/iis/index) o [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="e07e9-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="e07e9-107">L'autenticazione di Windows si basa sul sistema operativo per autenticare gli utenti delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e07e9-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="e07e9-108">È possibile usare l'autenticazione di Windows quando il server viene eseguito in una rete aziendale usando Active Directory identità di dominio o account di Windows per identificare gli utenti.</span><span class="sxs-lookup"><span data-stu-id="e07e9-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="e07e9-109">L'autenticazione di Windows è più adatta agli ambienti Intranet in cui gli utenti, le app client e i server Web appartengono allo stesso dominio Windows.</span><span class="sxs-lookup"><span data-stu-id="e07e9-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="e07e9-110">L'autenticazione di Windows non è supportata con HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e07e9-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="e07e9-111">Le richieste di autenticazione possono essere inviate in risposta HTTP/2, ma il client deve eseguire il downgrade a HTTP/1.1 prima dell'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e07e9-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="e07e9-112">Scenari di proxy e bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="e07e9-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="e07e9-113">L'autenticazione di Windows è uno scenario con stato utilizzato principalmente in una rete Intranet, in cui un proxy o un servizio di bilanciamento del carico non gestisce in genere il traffico tra client e server.</span><span class="sxs-lookup"><span data-stu-id="e07e9-113">Windows Authentication is a stateful scenario primarily used in an intranet, where a proxy or load balancer doesn't usually handle traffic between clients and servers.</span></span> <span data-ttu-id="e07e9-114">Se si usa un proxy o un servizio di bilanciamento del carico, l'autenticazione di Windows funziona solo se il proxy o il servizio di bilanciamento del carico:</span><span class="sxs-lookup"><span data-stu-id="e07e9-114">If a proxy or load balancer is used, Windows Authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="e07e9-115">Gestisce l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e07e9-115">Handles the authentication.</span></span>
* <span data-ttu-id="e07e9-116">Passa le informazioni di autenticazione utente all'app, ad esempio in un'intestazione di richiesta, che agisce sulle informazioni di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e07e9-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="e07e9-117">Un'alternativa all'autenticazione di Windows negli ambienti in cui vengono usati proxy e servizi di bilanciamento del carico è Active Directory servizi federati (ADFS) con OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="e07e9-117">An alternative to Windows Authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="e07e9-118">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="e07e9-118">IIS/IIS Express</span></span>

<span data-ttu-id="e07e9-119">Aggiungere i servizi di autenticazione richiamando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> spazio dei nomi) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e07e9-119">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="e07e9-120">Impostazioni di avvio (debugger)</span><span class="sxs-lookup"><span data-stu-id="e07e9-120">Launch settings (debugger)</span></span>

<span data-ttu-id="e07e9-121">La configurazione per le impostazioni di avvio influiscono solo sulle *Proprietà/launchSettings.jsnel* file per IIS Express e non configura IIS per l'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="e07e9-121">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="e07e9-122">La configurazione del server è illustrata nella sezione [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="e07e9-122">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="e07e9-123">Il modello di **applicazione Web** disponibile tramite Visual Studio o il interfaccia della riga di comando di .NET Core può essere configurato per supportare l'autenticazione di Windows, che aggiorna automaticamente le *Proprietà/launchSettings.jssul* file.</span><span class="sxs-lookup"><span data-stu-id="e07e9-123">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e07e9-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e07e9-124">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e07e9-125">**Nuovo progetto**</span><span class="sxs-lookup"><span data-stu-id="e07e9-125">**New project**</span></span>

1. <span data-ttu-id="e07e9-126">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="e07e9-126">Create a new project.</span></span>
1. <span data-ttu-id="e07e9-127">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="e07e9-128">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-128">Select **Next**.</span></span>
1. <span data-ttu-id="e07e9-129">Specificare un nome nel campo **nome progetto** .</span><span class="sxs-lookup"><span data-stu-id="e07e9-129">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="e07e9-130">Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto.</span><span class="sxs-lookup"><span data-stu-id="e07e9-130">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="e07e9-131">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-131">Select **Create**.</span></span>
1. <span data-ttu-id="e07e9-132">Selezionare **modifica** in **autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-132">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="e07e9-133">Nella finestra **Cambia autenticazione** selezionare autenticazione di **Windows**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-133">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="e07e9-134">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-134">Select **OK**.</span></span>
1. <span data-ttu-id="e07e9-135">Selezionare **applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-135">Select **Web Application**.</span></span>
1. <span data-ttu-id="e07e9-136">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-136">Select **Create**.</span></span>

<span data-ttu-id="e07e9-137">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="e07e9-137">Run the app.</span></span> <span data-ttu-id="e07e9-138">Il nome utente viene visualizzato nell'interfaccia utente dell'app di cui è stato eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="e07e9-138">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="e07e9-139">**Progetto esistente**</span><span class="sxs-lookup"><span data-stu-id="e07e9-139">**Existing project**</span></span>

<span data-ttu-id="e07e9-140">Le proprietà del progetto abilitano l'autenticazione di Windows e disabilitano l'autenticazione anonima:</span><span class="sxs-lookup"><span data-stu-id="e07e9-140">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="e07e9-141">Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-141">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="e07e9-142">Selezionare la scheda **Debug**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-142">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="e07e9-143">Deselezionare la casella di controllo **Abilita autenticazione anonima**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-143">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="e07e9-144">Selezionare la casella di controllo **Abilita autenticazione di Windows**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-144">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="e07e9-145">Salvare e chiudere la pagina delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="e07e9-145">Save and close the property page.</span></span>

<span data-ttu-id="e07e9-146">In alternativa, le proprietà possono essere configurate nel `iisSettings` nodo del *launchSettings.js* nel file:</span><span class="sxs-lookup"><span data-stu-id="e07e9-146">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[<span data-ttu-id="e07e9-147">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="e07e9-147">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e07e9-148">**Nuovo progetto**</span><span class="sxs-lookup"><span data-stu-id="e07e9-148">**New project**</span></span>

<span data-ttu-id="e07e9-149">Eseguire il comando [DotNet New](/dotnet/core/tools/dotnet-new) con l' `webapp` argomento (ASP.NET Core app Web) e `--auth Windows` Switch:</span><span class="sxs-lookup"><span data-stu-id="e07e9-149">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```dotnetcli
dotnet new webapp --auth Windows
```

<span data-ttu-id="e07e9-150">**Progetto esistente**</span><span class="sxs-lookup"><span data-stu-id="e07e9-150">**Existing project**</span></span>

<span data-ttu-id="e07e9-151">Aggiornare il `iisSettings` nodo del *launchSettings.jsnel* file:</span><span class="sxs-lookup"><span data-stu-id="e07e9-151">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="e07e9-152">Quando si modifica un progetto esistente, verificare che il file di progetto includa un riferimento al pacchetto per il [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) **o** il pacchetto NuGet [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="e07e9-152">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="e07e9-153">IIS</span><span class="sxs-lookup"><span data-stu-id="e07e9-153">IIS</span></span>

<span data-ttu-id="e07e9-154">IIS usa il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) per ospitare ASP.NET Core app.</span><span class="sxs-lookup"><span data-stu-id="e07e9-154">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="e07e9-155">L'autenticazione di Windows è configurata per IIS tramite il file di *web.config* .</span><span class="sxs-lookup"><span data-stu-id="e07e9-155">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="e07e9-156">Le sezioni seguenti mostrano come:</span><span class="sxs-lookup"><span data-stu-id="e07e9-156">The following sections show how to:</span></span>

* <span data-ttu-id="e07e9-157">Fornire un file di *web.config* locale che attiva l'autenticazione di Windows nel server quando viene distribuita l'app.</span><span class="sxs-lookup"><span data-stu-id="e07e9-157">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="e07e9-158">Utilizzare Gestione IIS per configurare il file di *web.config* di un'app ASP.NET Core che è già stata distribuita nel server.</span><span class="sxs-lookup"><span data-stu-id="e07e9-158">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="e07e9-159">Se non è già stato fatto, abilitare IIS per ospitare ASP.NET Core app.</span><span class="sxs-lookup"><span data-stu-id="e07e9-159">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="e07e9-160">Per altre informazioni, vedere <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="e07e9-160">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="e07e9-161">Abilitare il servizio ruolo IIS per l'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="e07e9-161">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="e07e9-162">Per ulteriori informazioni, vedere [abilitare l'autenticazione di Windows nei servizi ruolo IIS (vedere il passaggio 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="e07e9-162">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="e07e9-163">Il [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) è configurato per l'autenticazione automatica delle richieste per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="e07e9-163">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="e07e9-164">Per ulteriori informazioni, vedere [Host ASP.NET Core in Windows con IIS: opzioni IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="e07e9-164">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="e07e9-165">Il modulo ASP.NET Core è configurato per l'invio del token di autenticazione di Windows all'app per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="e07e9-165">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="e07e9-166">Per altre informazioni, vedere [riferimento alla configurazione del modulo ASP.NET Core: attributi dell'elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="e07e9-166">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="e07e9-167">Usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="e07e9-167">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="e07e9-168">**Prima di pubblicare e distribuire il progetto,** aggiungere il seguente file di *web.config* alla radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="e07e9-168">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="e07e9-169">Quando il progetto viene pubblicato dal .NET Core SDK (senza la `<IsTransformWebConfigDisabled>` proprietà impostata su `true` nel file di progetto), il file di *web.config* pubblicato include la `<location><system.webServer><security><authentication>` sezione.</span><span class="sxs-lookup"><span data-stu-id="e07e9-169">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="e07e9-170">Per ulteriori informazioni sulla `<IsTransformWebConfigDisabled>` proprietà, vedere <xref:host-and-deploy/iis/index#webconfig-file> .</span><span class="sxs-lookup"><span data-stu-id="e07e9-170">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="e07e9-171">**Dopo la pubblicazione e la distribuzione del progetto,** eseguire la configurazione lato server con gestione IIS:</span><span class="sxs-lookup"><span data-stu-id="e07e9-171">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="e07e9-172">In Gestione IIS selezionare il sito IIS nel nodo **siti** della barra laterale **connessioni** .</span><span class="sxs-lookup"><span data-stu-id="e07e9-172">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="e07e9-173">Fare doppio clic su **autenticazione** nell'area **IIS** .</span><span class="sxs-lookup"><span data-stu-id="e07e9-173">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="e07e9-174">Selezionare **autenticazione anonima**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-174">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="e07e9-175">Selezionare **Disabilita** nella barra laterale **azioni** .</span><span class="sxs-lookup"><span data-stu-id="e07e9-175">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="e07e9-176">Selezionare **Autenticazione di Windows**.</span><span class="sxs-lookup"><span data-stu-id="e07e9-176">Select **Windows Authentication**.</span></span> <span data-ttu-id="e07e9-177">Selezionare **Abilita** nella barra laterale **azioni** .</span><span class="sxs-lookup"><span data-stu-id="e07e9-177">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="e07e9-178">Quando vengono eseguite queste azioni, gestione IIS modifica il file di *web.config* dell'app.</span><span class="sxs-lookup"><span data-stu-id="e07e9-178">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="e07e9-179">`<system.webServer><security><authentication>`Viene aggiunto un nodo con le impostazioni aggiornate per `anonymousAuthentication` e `windowsAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="e07e9-179">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="e07e9-180">La `<system.webServer>` sezione aggiunta al file di *web.config* da Gestione IIS si trova al di fuori della `<location>` sezione dell'app aggiunta dall'.NET Core SDK quando viene pubblicata l'app.</span><span class="sxs-lookup"><span data-stu-id="e07e9-180">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="e07e9-181">Poiché la sezione viene aggiunta all'esterno del `<location>` nodo, le impostazioni vengono ereditate dalle app [secondarie](xref:host-and-deploy/iis/index#sub-applications) all'app corrente.</span><span class="sxs-lookup"><span data-stu-id="e07e9-181">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="e07e9-182">Per impedire l'ereditarietà, spostare la `<security>` sezione aggiunta all'interno della `<location><system.webServer>` sezione specificata dal .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="e07e9-182">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="e07e9-183">Quando si usa Gestione IIS per aggiungere la configurazione di IIS, questo influisca solo sul file di *web.config* dell'app nel server.</span><span class="sxs-lookup"><span data-stu-id="e07e9-183">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="e07e9-184">Una distribuzione successiva dell'app potrebbe sovrascrivere le impostazioni nel server se la copia del server *web.config* viene sostituita dal file *web.config* del progetto.</span><span class="sxs-lookup"><span data-stu-id="e07e9-184">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="e07e9-185">Per gestire le impostazioni, usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="e07e9-185">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="e07e9-186">Utilizzare Gestione IIS per reimpostare le impostazioni nel file di *web.config* dopo la sovrascrittura del file durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="e07e9-186">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="e07e9-187">Aggiungere un *file diweb.config* all'app localmente con le impostazioni.</span><span class="sxs-lookup"><span data-stu-id="e07e9-187">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="e07e9-188">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e07e9-188">Kestrel</span></span>

<span data-ttu-id="e07e9-189">Il pacchetto NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) può essere usato con [gheppio](xref:fundamentals/servers/kestrel) per supportare l'autenticazione di Windows tramite Negotiate e Kerberos in Windows, Linux e MacOS.</span><span class="sxs-lookup"><span data-stu-id="e07e9-189">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate and Kerberos on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="e07e9-190">Le credenziali possono essere rese permanente tra le richieste in una connessione.</span><span class="sxs-lookup"><span data-stu-id="e07e9-190">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="e07e9-191">*L'autenticazione Negotiate non deve essere usata con i proxy, a meno che il proxy non mantenga un'affinità di connessione 1:1 (una connessione permanente) con gheppio.*</span><span class="sxs-lookup"><span data-stu-id="e07e9-191">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="e07e9-192">Il gestore Negotiate rileva se il server sottostante supporta l'autenticazione di Windows in modalità nativa e se è abilitato.</span><span class="sxs-lookup"><span data-stu-id="e07e9-192">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="e07e9-193">Se il server supporta l'autenticazione di Windows ma è disabilitato, viene generato un errore in cui viene chiesto di abilitare l'implementazione del server.</span><span class="sxs-lookup"><span data-stu-id="e07e9-193">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="e07e9-194">Quando l'autenticazione di Windows è abilitata nel server, il gestore Negotiate li trasmette in modo trasparente.</span><span class="sxs-lookup"><span data-stu-id="e07e9-194">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

<span data-ttu-id="e07e9-195">Aggiungere i servizi di autenticazione richiamando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> e <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e07e9-195">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices`:</span></span>

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="e07e9-196">Aggiungere il middleware di autenticazione chiamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e07e9-196">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();
```

<span data-ttu-id="e07e9-197">Per ulteriori informazioni sul middleware, vedere <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="e07e9-197">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="e07e9-198">Sono consentite richieste anonime.</span><span class="sxs-lookup"><span data-stu-id="e07e9-198">Anonymous requests are allowed.</span></span> <span data-ttu-id="e07e9-199">Utilizzare [ASP.NET Core autorizzazione](xref:security/authorization/introduction) per la verifica delle richieste anonime di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e07e9-199">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="e07e9-200">Configurazione dell'ambiente Windows</span><span class="sxs-lookup"><span data-stu-id="e07e9-200">Windows environment configuration</span></span>

<span data-ttu-id="e07e9-201">Il componente [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) esegue l'autenticazione in modalità utente.</span><span class="sxs-lookup"><span data-stu-id="e07e9-201">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="e07e9-202">È necessario aggiungere i nomi dell'entità servizio (SPN) all'account utente che esegue il servizio, non all'account del computer.</span><span class="sxs-lookup"><span data-stu-id="e07e9-202">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="e07e9-203">Eseguire `setspn -S HTTP/myservername.mydomain.com myuser` in una shell di comandi amministrativi.</span><span class="sxs-lookup"><span data-stu-id="e07e9-203">Execute `setspn -S HTTP/myservername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="e07e9-204">Configurazione dell'ambiente Linux e macOS</span><span class="sxs-lookup"><span data-stu-id="e07e9-204">Linux and macOS environment configuration</span></span>

<span data-ttu-id="e07e9-205">Le istruzioni per l'aggiunta di un computer Linux o macOS a un dominio Windows sono disponibili nell'articolo [connettere Azure Data Studio all'SQL Server mediante autenticazione di Windows-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) .</span><span class="sxs-lookup"><span data-stu-id="e07e9-205">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="e07e9-206">Le istruzioni creano un account computer per il computer Linux nel dominio.</span><span class="sxs-lookup"><span data-stu-id="e07e9-206">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="e07e9-207">È necessario aggiungere nomi SPN all'account del computer.</span><span class="sxs-lookup"><span data-stu-id="e07e9-207">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="e07e9-208">Quando si seguono le istruzioni riportate nell'articolo [connettere Azure Data Studio all'SQL Server mediante autenticazione di Windows-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , sostituire `python-software-properties` con `python3-software-properties` se necessario.</span><span class="sxs-lookup"><span data-stu-id="e07e9-208">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="e07e9-209">Una volta che il computer Linux o macOS è stato aggiunto al dominio, sono necessari passaggi aggiuntivi per fornire un [file keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) con i nomi SPN:</span><span class="sxs-lookup"><span data-stu-id="e07e9-209">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="e07e9-210">Nel controller di dominio aggiungere nuovi SPN del servizio Web all'account del computer:</span><span class="sxs-lookup"><span data-stu-id="e07e9-210">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="e07e9-211">Usare [lo Ktpass](/windows-server/administration/windows-commands/ktpass) per generare un file keytab:</span><span class="sxs-lookup"><span data-stu-id="e07e9-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="e07e9-212">Alcuni campi devono essere specificati in lettere maiuscole come indicato.</span><span class="sxs-lookup"><span data-stu-id="e07e9-212">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="e07e9-213">Copiare il file keytab nel computer Linux o macOS.</span><span class="sxs-lookup"><span data-stu-id="e07e9-213">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="e07e9-214">Selezionare il file keytab tramite una variabile di ambiente:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="e07e9-214">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="e07e9-215">Richiama `klist` per visualizzare gli SPN attualmente disponibili per l'utilizzo.</span><span class="sxs-lookup"><span data-stu-id="e07e9-215">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="e07e9-216">Un file keytab contiene le credenziali di accesso al dominio e deve essere protetto di conseguenza.</span><span class="sxs-lookup"><span data-stu-id="e07e9-216">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="e07e9-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e07e9-217">HTTP.sys</span></span>

<span data-ttu-id="e07e9-218">[HTTP.sys](xref:fundamentals/servers/httpsys) supporta l'autenticazione di Windows in modalità kernel utilizzando l'autenticazione Negotiate, NTLM o di base.</span><span class="sxs-lookup"><span data-stu-id="e07e9-218">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="e07e9-219">Aggiungere i servizi di autenticazione richiamando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> spazio dei nomi) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e07e9-219">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="e07e9-220">Configurare l'host web dell'app per l'uso di HTTP.sys con l'autenticazione di Windows (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="e07e9-220">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="e07e9-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>si trova nello <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="e07e9-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e07e9-222">Per la delega all'autenticazione in modalità kernel, HTTP.sys usa il protocollo di autenticazione Kerberos.</span><span class="sxs-lookup"><span data-stu-id="e07e9-222">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="e07e9-223">L'autenticazione in modalità utente non è supportata con Kerberos e HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="e07e9-223">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="e07e9-224">È necessario usare l'account del computer per decrittografare il token/ticket Kerberos ottenuto da Active Directory e inoltrato dal client al server per autenticare l'utente.</span><span class="sxs-lookup"><span data-stu-id="e07e9-224">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="e07e9-225">Registrare il nome dell'entità servizio per l'host, non l'utente dell'app.</span><span class="sxs-lookup"><span data-stu-id="e07e9-225">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="e07e9-226">HTTP.sys non è supportato in nano Server versione 1709 o successiva.</span><span class="sxs-lookup"><span data-stu-id="e07e9-226">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="e07e9-227">Per usare l'autenticazione di Windows e HTTP.sys con nano server, usare un [contenitore Server Core (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="e07e9-227">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="e07e9-228">Per ulteriori informazioni su Server Core, vedere [che cos'è l'opzione di installazione dei componenti di base del server in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="e07e9-228">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="e07e9-229">Autorizzare gli utenti</span><span class="sxs-lookup"><span data-stu-id="e07e9-229">Authorize users</span></span>

<span data-ttu-id="e07e9-230">Lo stato di configurazione dell'accesso anonimo determina il modo in cui `[Authorize]` gli `[AllowAnonymous]` attributi e vengono usati nell'app.</span><span class="sxs-lookup"><span data-stu-id="e07e9-230">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="e07e9-231">Nelle due sezioni seguenti viene illustrato come gestire gli Stati di configurazione non consentiti e consentiti di accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="e07e9-231">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="e07e9-232">Non consentire l'accesso anonimo</span><span class="sxs-lookup"><span data-stu-id="e07e9-232">Disallow anonymous access</span></span>

<span data-ttu-id="e07e9-233">Quando l'autenticazione di Windows è abilitata e l'accesso anonimo è disabilitato, gli `[Authorize]` `[AllowAnonymous]` attributi e non hanno alcun effetto.</span><span class="sxs-lookup"><span data-stu-id="e07e9-233">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="e07e9-234">Se un sito IIS è configurato per non consentire l'accesso anonimo, la richiesta non raggiunge mai l'app.</span><span class="sxs-lookup"><span data-stu-id="e07e9-234">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="e07e9-235">Per questo motivo, l' `[AllowAnonymous]` attributo non è applicabile.</span><span class="sxs-lookup"><span data-stu-id="e07e9-235">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="e07e9-236">Consenti accesso anonimo</span><span class="sxs-lookup"><span data-stu-id="e07e9-236">Allow anonymous access</span></span>

<span data-ttu-id="e07e9-237">Quando è abilitata l'autenticazione di Windows e l'accesso anonimo, utilizzare gli `[Authorize]` `[AllowAnonymous]` attributi e.</span><span class="sxs-lookup"><span data-stu-id="e07e9-237">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="e07e9-238">L' `[Authorize]` attributo consente di proteggere gli endpoint dell'app che richiedono l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e07e9-238">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="e07e9-239">L' `[AllowAnonymous]` attributo sostituisce l' `[Authorize]` attributo nelle app che consentono l'accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="e07e9-239">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="e07e9-240">Per informazioni dettagliate sull'utilizzo degli attributi, vedere <xref:security/authorization/simple> .</span><span class="sxs-lookup"><span data-stu-id="e07e9-240">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="e07e9-241">Per impostazione predefinita, gli utenti che non dispongono dell'autorizzazione per accedere a una pagina vengono presentati con una risposta HTTP 403 vuota.</span><span class="sxs-lookup"><span data-stu-id="e07e9-241">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="e07e9-242">Il [middleware StatusCodePages](xref:fundamentals/error-handling#usestatuscodepages) può essere configurato in modo da offrire agli utenti una migliore esperienza di "accesso negato".</span><span class="sxs-lookup"><span data-stu-id="e07e9-242">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="e07e9-243">Rappresentazione</span><span class="sxs-lookup"><span data-stu-id="e07e9-243">Impersonation</span></span>

<span data-ttu-id="e07e9-244">ASP.NET Core non implementa la rappresentazione.</span><span class="sxs-lookup"><span data-stu-id="e07e9-244">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="e07e9-245">Le app vengono eseguite con l'identità dell'app per tutte le richieste, usando il pool di applicazioni o l'identità del processo.</span><span class="sxs-lookup"><span data-stu-id="e07e9-245">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="e07e9-246">Se l'app deve eseguire un'azione per conto di un utente, utilizzare [WindowsIdentity. RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in un [middleware inline terminale](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="e07e9-246">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="e07e9-247">Eseguire una singola azione in questo contesto e quindi chiudere il contesto.</span><span class="sxs-lookup"><span data-stu-id="e07e9-247">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="e07e9-248">`RunImpersonated`non supporta le operazioni asincrone e non deve essere usato per scenari complessi.</span><span class="sxs-lookup"><span data-stu-id="e07e9-248">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="e07e9-249">Ad esempio, il wrapping di intere richieste o catene di middleware non è supportato o consigliato.</span><span class="sxs-lookup"><span data-stu-id="e07e9-249">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e07e9-250">Mentre il pacchetto [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) Abilita l'autenticazione in Windows, Linux e MacOS, la rappresentazione è supportata solo in Windows.</span><span class="sxs-lookup"><span data-stu-id="e07e9-250">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="e07e9-251">Trasformazioni delle attestazioni</span><span class="sxs-lookup"><span data-stu-id="e07e9-251">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e07e9-252">Quando si ospita con IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> non viene chiamato internamente per inizializzare un utente.</span><span class="sxs-lookup"><span data-stu-id="e07e9-252">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="e07e9-253">Pertanto, un'implementazione di <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usate per trasformare le attestazioni dopo ogni autenticazione non viene attivata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="e07e9-253">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="e07e9-254">Per ulteriori informazioni e un esempio di codice che attiva le trasformazioni delle attestazioni, vedere <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="e07e9-254">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e07e9-255">Quando si esegue l'hosting con la modalità in-process di IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> non viene chiamato internamente per inizializzare un utente.</span><span class="sxs-lookup"><span data-stu-id="e07e9-255">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="e07e9-256">Pertanto, un'implementazione di <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usate per trasformare le attestazioni dopo ogni autenticazione non viene attivata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="e07e9-256">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="e07e9-257">Per ulteriori informazioni e un esempio di codice che attiva le trasformazioni delle attestazioni durante l'hosting in-process, vedere <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="e07e9-257">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e07e9-258">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e07e9-258">Additional resources</span></span>

* [<span data-ttu-id="e07e9-259">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="e07e9-259">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
