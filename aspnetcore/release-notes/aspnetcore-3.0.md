---
title: Novità di ASP.NET Core 3.0
author: rick-anderson
description: Scopri le nuove funzionalità di ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 4886673a9b16b8be8d9a0b0d5c7002a91760544e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976976"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="b8001-103">Novità di ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="b8001-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="b8001-104">In questo articolo vengono evidenziate le modifiche più significative in ASP.NET Core 3.0 con collegamenti alla documentazione pertinente.</span><span class="sxs-lookup"><span data-stu-id="b8001-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

Blazor<span data-ttu-id="b8001-105">è un nuovo framework in ASP.NET Core per la creazione di un'interfaccia utente Web interattiva sul lato client con .NET:Is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span><span class="sxs-lookup"><span data-stu-id="b8001-105"> is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="b8001-106">Creare interfacce utente interattive avanzate con C# invece di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b8001-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="b8001-107">Condividere la logica dell'app scritta in .NET sul lato client e sul lato server.</span><span class="sxs-lookup"><span data-stu-id="b8001-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="b8001-108">Eseguire il rendering dell'interfaccia utente come HTML e CSS per un ampio supporto dei browser, inclusi i browser per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="b8001-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

Blazor<span data-ttu-id="b8001-109">scenari supportati dal framework:</span><span class="sxs-lookup"><span data-stu-id="b8001-109"> framework supported scenarios:</span></span>

* <span data-ttu-id="b8001-110">Componenti riutilizzabili dell'interfaccia utente (componenti Razor)Reusable UI components (Razor components)</span><span class="sxs-lookup"><span data-stu-id="b8001-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="b8001-111">Routing lato client</span><span class="sxs-lookup"><span data-stu-id="b8001-111">Client-side routing</span></span>
* <span data-ttu-id="b8001-112">Layout dei componenti</span><span class="sxs-lookup"><span data-stu-id="b8001-112">Component layouts</span></span>
* <span data-ttu-id="b8001-113">Supporto per l'iniezione di dipendenza</span><span class="sxs-lookup"><span data-stu-id="b8001-113">Support for dependency injection</span></span>
* <span data-ttu-id="b8001-114">Moduli e convalida</span><span class="sxs-lookup"><span data-stu-id="b8001-114">Forms and validation</span></span>
* <span data-ttu-id="b8001-115">Compilare librerie di componenti con librerie di classi RazorBuild component libraries with Razor class libraries</span><span class="sxs-lookup"><span data-stu-id="b8001-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="b8001-116">Interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="b8001-116">JavaScript interop</span></span>

<span data-ttu-id="b8001-117">Per altre informazioni, vedere <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="b8001-117">For more information, see <xref:blazor/index>.</span></span>

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="b8001-118">Server</span><span class="sxs-lookup"><span data-stu-id="b8001-118"> Server</span></span>

Blazor<span data-ttu-id="b8001-119">disaccoppia la logica di rendering dei componenti dalla modalità di applicazione degli aggiornamenti dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="b8001-119"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="b8001-120">Server fornisce il supporto per l'hosting di componenti Razor nel server in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b8001-120"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="b8001-121">Gli aggiornamenti dell'interfaccia SignalR utente vengono gestiti tramite una connessione.</span><span class="sxs-lookup"><span data-stu-id="b8001-121">UI updates are handled over a SignalR connection.</span></span> Blazor<span data-ttu-id="b8001-122">Il server è supportato in ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="b8001-122"> Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="opno-locblazor-webassembly-preview"></a>Blazor<span data-ttu-id="b8001-123">WebAssembly (anteprima)</span><span class="sxs-lookup"><span data-stu-id="b8001-123"> WebAssembly (Preview)</span></span>

Blazor<span data-ttu-id="b8001-124">le app possono anche essere eseguite direttamente nel browser usando un runtime .NET basato su WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b8001-124"> apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> Blazor<span data-ttu-id="b8001-125">WebAssembly è in anteprima e *non* è supportato in ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="b8001-125"> WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> Blazor<span data-ttu-id="b8001-126">WebAssembly sarà supportato in una versione futura di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b8001-126"> WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="b8001-127">Componenti Razor</span><span class="sxs-lookup"><span data-stu-id="b8001-127">Razor components</span></span>

Blazor<span data-ttu-id="b8001-128">le app sono costruite dai componenti.</span><span class="sxs-lookup"><span data-stu-id="b8001-128"> apps are built from components.</span></span> <span data-ttu-id="b8001-129">I componenti sono blocchi autonomi dell'interfaccia utente, ad esempio una pagina, una finestra di dialogo o un form.</span><span class="sxs-lookup"><span data-stu-id="b8001-129">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="b8001-130">I componenti sono classi .NET normali che definiscono la logica di rendering dell'interfaccia utente e i gestori eventi sul lato client.</span><span class="sxs-lookup"><span data-stu-id="b8001-130">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="b8001-131">È possibile creare app Web interattive avanzate senza JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b8001-131">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="b8001-132">I Blazor componenti in vengono in genere creati utilizzando la sintassi Razor, una combinazione naturale di HTML e C.</span><span class="sxs-lookup"><span data-stu-id="b8001-132">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="b8001-133">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span><span class="sxs-lookup"><span data-stu-id="b8001-133">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="b8001-134">A differenza delle pagine e delle visualizzazioni, basate su un modello di richiesta-risposta, i componenti vengono usati in modo specifico per la gestione della composizione dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="b8001-134">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="b8001-135">gRPC</span><span class="sxs-lookup"><span data-stu-id="b8001-135">gRPC</span></span>

<span data-ttu-id="b8001-136">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="b8001-136">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="b8001-137">È un framework RPC (chiamata di procedura remota) popolare e ad alte prestazioni.</span><span class="sxs-lookup"><span data-stu-id="b8001-137">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="b8001-138">Offre un approccio orientato al contratto per lo sviluppo di API.</span><span class="sxs-lookup"><span data-stu-id="b8001-138">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="b8001-139">Utilizza tecnologie moderne come:</span><span class="sxs-lookup"><span data-stu-id="b8001-139">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="b8001-140">HTTP/2 per il trasporto.</span><span class="sxs-lookup"><span data-stu-id="b8001-140">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="b8001-141">Protocol Buffers come linguaggio di descrizione dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="b8001-141">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="b8001-142">Formato di serializzazione binario.</span><span class="sxs-lookup"><span data-stu-id="b8001-142">Binary serialization format.</span></span>
* <span data-ttu-id="b8001-143">Fornisce funzionalità quali:</span><span class="sxs-lookup"><span data-stu-id="b8001-143">Provides features such as:</span></span>

  * <span data-ttu-id="b8001-144">Authentication</span><span class="sxs-lookup"><span data-stu-id="b8001-144">Authentication</span></span>
  * <span data-ttu-id="b8001-145">Flusso bidirezionale e controllo di flusso.</span><span class="sxs-lookup"><span data-stu-id="b8001-145">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="b8001-146">Cancellazione e timeout.</span><span class="sxs-lookup"><span data-stu-id="b8001-146">Cancellation and timeouts.</span></span>

<span data-ttu-id="b8001-147">La funzionalità gRPC in ASP.NET Core 3.0 include:</span><span class="sxs-lookup"><span data-stu-id="b8001-147">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="b8001-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; Un framework ASP.NET Core per l'hosting di servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="b8001-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="b8001-149">gRPC su ASP.NET Core si integra con le funzionalità standard ASP.NET Core come la registrazione, l'inserimento delle dipendenze (DI), l'autenticazione e l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="b8001-149">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="b8001-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; Un client gRPC per .NET Core `HttpClient`che si basa sul familiare file .</span><span class="sxs-lookup"><span data-stu-id="b8001-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="b8001-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC integrazione client con `HttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="b8001-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="b8001-152">Per altre informazioni, vedere <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="b8001-152">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="b8001-153">Vedere [ SignalR Aggiornare il codice](xref:migration/22-to-30#signalr) per le istruzioni di migrazione.</span><span class="sxs-lookup"><span data-stu-id="b8001-153">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> SignalR<span data-ttu-id="b8001-154">ora `System.Text.Json` utilizza per serializzare/deserializzare i messaggi JSON.</span><span class="sxs-lookup"><span data-stu-id="b8001-154"> now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="b8001-155">Vedere [Passare a Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) per `Newtonsoft.Json`istruzioni sul ripristino del serializzatore basato.</span><span class="sxs-lookup"><span data-stu-id="b8001-155">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="b8001-156">In JavaScript e .NET SignalRClients for , è stato aggiunto il supporto per la riconnessione automatica.</span><span class="sxs-lookup"><span data-stu-id="b8001-156">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="b8001-157">Per impostazione predefinita, il client tenta di riconnettersi immediatamente e riprovare dopo 2, 10 e 30 secondi, se necessario.</span><span class="sxs-lookup"><span data-stu-id="b8001-157">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="b8001-158">Se il client si riconnette correttamente, riceve un nuovo ID di connessione.</span><span class="sxs-lookup"><span data-stu-id="b8001-158">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="b8001-159">La riconnessione automatica è opt-in:</span><span class="sxs-lookup"><span data-stu-id="b8001-159">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="b8001-160">Gli intervalli di riconnessione possono essere specificati passando una matrice di durate basate su millisecondi:The reconnection intervals can be specified by passing an array of millisecond-based durations:</span><span class="sxs-lookup"><span data-stu-id="b8001-160">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="b8001-161">Un'implementazione personalizzata può essere passata per il controllo completo degli intervalli di riconnessione.</span><span class="sxs-lookup"><span data-stu-id="b8001-161">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="b8001-162">Se la riconnessione non riesce dopo l'ultimo intervallo di riconnessione:</span><span class="sxs-lookup"><span data-stu-id="b8001-162">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="b8001-163">Il client considera che la connessione è offline.</span><span class="sxs-lookup"><span data-stu-id="b8001-163">The client considers the connection is offline.</span></span>
* <span data-ttu-id="b8001-164">Il client smette di provare a riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="b8001-164">The client stops trying to reconnect.</span></span>

<span data-ttu-id="b8001-165">Durante i tentativi di riconnessione, aggiorna l'interfaccia utente dell'app per notificare all'utente che è in corso il tentativo di riconnessione.</span><span class="sxs-lookup"><span data-stu-id="b8001-165">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="b8001-166">Per fornire feedback dell'interfaccia utente SignalR quando la connessione viene interrotta, l'API client è stata espansa per includere i gestori eventi seguenti:To provide UI feedback when the connection is interrupted, the client API has been expanded to include the following event handlers:</span><span class="sxs-lookup"><span data-stu-id="b8001-166">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="b8001-167">`onreconnecting`: offre agli sviluppatori l'opportunità di disabilitare l'interfaccia utente o di indire agli utenti che l'app è offline.</span><span class="sxs-lookup"><span data-stu-id="b8001-167">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="b8001-168">`onreconnected`: offre agli sviluppatori l'opportunità di aggiornare l'interfaccia utente una volta ristabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="b8001-168">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="b8001-169">Il codice `onreconnecting` seguente usa per aggiornare l'interfaccia utente durante il tentativo di connessione:The following code uses to update the UI while trying to connect:</span><span class="sxs-lookup"><span data-stu-id="b8001-169">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="b8001-170">Il codice `onreconnected` seguente usa per aggiornare l'interfaccia utente alla connessione:The following code uses to update the UI on connection:</span><span class="sxs-lookup"><span data-stu-id="b8001-170">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR<span data-ttu-id="b8001-171">3.0 e versioni successive fornisce una risorsa personalizzata ai gestori di autorizzazione quando un metodo hub richiede l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="b8001-171"> 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="b8001-172">La risorsa è `HubInvocationContext`un'istanza di .</span><span class="sxs-lookup"><span data-stu-id="b8001-172">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="b8001-173">Il `HubInvocationContext` include il:</span><span class="sxs-lookup"><span data-stu-id="b8001-173">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="b8001-174">Nome del metodo dell'hub richiamato.</span><span class="sxs-lookup"><span data-stu-id="b8001-174">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="b8001-175">Argomenti per il metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b8001-175">Arguments to the hub method.</span></span>

<span data-ttu-id="b8001-176">Si consideri l'esempio seguente di un'app di chat room che consente l'accesso a più organizzazioni tramite Azure Active Directory.Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="b8001-176">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="b8001-177">Chiunque disponga di un account Microsoft può accedere alla chat, ma solo i membri dell'organizzazione proprietaria possono vietare gli utenti o visualizzare le cronologie delle chat degli utenti.</span><span class="sxs-lookup"><span data-stu-id="b8001-177">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="b8001-178">L'app potrebbe limitare determinate funzionalità da utenti specifici.</span><span class="sxs-lookup"><span data-stu-id="b8001-178">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="b8001-179">Nel codice precedente, `DomainRestrictedRequirement` funge da `IAuthorizationRequirement`oggetto personalizzato.</span><span class="sxs-lookup"><span data-stu-id="b8001-179">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="b8001-180">Poiché `HubInvocationContext` il parametro della risorsa viene passato, la logica interna può:Because the resource parameter is being passed in, the internal logic can:</span><span class="sxs-lookup"><span data-stu-id="b8001-180">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="b8001-181">Esaminare il contesto in cui viene chiamato l'hub.</span><span class="sxs-lookup"><span data-stu-id="b8001-181">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="b8001-182">Prendere decisioni su come consentire all'utente di eseguire singoli metodi Hub.</span><span class="sxs-lookup"><span data-stu-id="b8001-182">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="b8001-183">I singoli metodi Hub possono essere contrassegnati con il nome del criterio che controlla il codice in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b8001-183">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="b8001-184">Quando i client tentano di `DomainRestrictedRequirement` chiamare singoli metodi Hub, il gestore viene eseguito e controlla l'accesso ai metodi.</span><span class="sxs-lookup"><span data-stu-id="b8001-184">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="b8001-185">In base al `DomainRestrictedRequirement` modo in cui i controlli accedono:</span><span class="sxs-lookup"><span data-stu-id="b8001-185">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="b8001-186">Tutti gli utenti connessi `SendMessage` possono chiamare il metodo .</span><span class="sxs-lookup"><span data-stu-id="b8001-186">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="b8001-187">Solo gli utenti che `@jabbr.net` hanno effettuato l'accesso con un indirizzo e-mail possono visualizzare le cronologie degli utenti.</span><span class="sxs-lookup"><span data-stu-id="b8001-187">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="b8001-188">Solo `bob42@jabbr.net` gli utenti possono escludere dalla chat room.</span><span class="sxs-lookup"><span data-stu-id="b8001-188">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="b8001-189">La `DomainRestricted` creazione del criterio potrebbe comportare:</span><span class="sxs-lookup"><span data-stu-id="b8001-189">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="b8001-190">In *Startup.cs*, aggiungendo il nuovo criterio.</span><span class="sxs-lookup"><span data-stu-id="b8001-190">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="b8001-191">Specificare `DomainRestrictedRequirement` il requisito personalizzato come parametro.</span><span class="sxs-lookup"><span data-stu-id="b8001-191">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="b8001-192">Registrazione `DomainRestricted` con il middleware di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="b8001-192">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

SignalR<span data-ttu-id="b8001-193">gli hub utilizzano [il routing degli endpoint](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="b8001-193"> hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> SignalR<span data-ttu-id="b8001-194">connessione hub è stata precedentemente eseguita in modo esplicito:hub connection was previously done explicitly:</span><span class="sxs-lookup"><span data-stu-id="b8001-194"> hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="b8001-195">Nella versione precedente, gli sviluppatori dovevano collegare controller, pagine Razor e hub in una varietà di posizioni.</span><span class="sxs-lookup"><span data-stu-id="b8001-195">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="b8001-196">La connessione esplicita produce una serie di segmenti di routing quasi identici:Explicit connection results in a series of nearly-identical routing segments:</span><span class="sxs-lookup"><span data-stu-id="b8001-196">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

SignalR<span data-ttu-id="b8001-197">Gli hub 3.0 possono essere instradati tramite il routing endpoint.</span><span class="sxs-lookup"><span data-stu-id="b8001-197"> 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="b8001-198">Con il routing degli endpoint, `UseRouting`in genere tutto il routing può essere configurato in:</span><span class="sxs-lookup"><span data-stu-id="b8001-198">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="b8001-199">ASP.NET Core 3.0 SignalR aggiunto:</span><span class="sxs-lookup"><span data-stu-id="b8001-199">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="b8001-200">Streaming da client a server.</span><span class="sxs-lookup"><span data-stu-id="b8001-200">Client-to-server streaming.</span></span> <span data-ttu-id="b8001-201">Con lo streaming da client a server, i metodi `IAsyncEnumerable<T>` `ChannelReader<T>`lato server possono accettare istanze di un oggetto o .</span><span class="sxs-lookup"><span data-stu-id="b8001-201">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="b8001-202">Nell'esempio di codice `UploadStream` c'è il seguente esempio di codice, il metodo nell'hub riceverà un flusso di stringhe dal client:</span><span class="sxs-lookup"><span data-stu-id="b8001-202">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="b8001-203">Le app client .NET `IAsyncEnumerable<T>` `ChannelReader<T>` possono passare `stream` un'istanza o come argomento del `UploadStream` metodo Hub precedente.</span><span class="sxs-lookup"><span data-stu-id="b8001-203">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="b8001-204">Dopo `for` che il ciclo è stato completato e la funzione locale viene chiusa, viene inviato il completamento del flusso:After the loop has completed and the local function exits, the stream completion is sent:</span><span class="sxs-lookup"><span data-stu-id="b8001-204">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="b8001-205">Le app client SignalR `Subject` JavaScript usano (o un `stream` [oggetto RxJS](https://rxjs.dev/api/index/class/Subject)) per l'argomento del metodo `UploadStream` Hub precedente.</span><span class="sxs-lookup"><span data-stu-id="b8001-205">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="b8001-206">Il codice JavaScript `subject.next` potrebbe usare il metodo per gestire le stringhe quando vengono acquisite e pronte per essere inviate al server.</span><span class="sxs-lookup"><span data-stu-id="b8001-206">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="b8001-207">Usando codice come i due frammenti precedenti, è possibile creare esperienze di streaming in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="b8001-207">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="b8001-208">Nuova serializzazione JSON</span><span class="sxs-lookup"><span data-stu-id="b8001-208">New JSON serialization</span></span>

<span data-ttu-id="b8001-209">ASP.NET Core 3.0 <xref:System.Text.Json> ora usa per impostazione predefinita per la serializzazione JSON:The Core 3.0 now uses by default for JSON serialization:</span><span class="sxs-lookup"><span data-stu-id="b8001-209">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="b8001-210">Legge e scrive JSON in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="b8001-210">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="b8001-211">È ottimizzato per il testo UTF-8.</span><span class="sxs-lookup"><span data-stu-id="b8001-211">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="b8001-212">Prestazioni in `Newtonsoft.Json`genere superiori a quelle di .</span><span class="sxs-lookup"><span data-stu-id="b8001-212">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="b8001-213">Per aggiungere Json.NET a ASP.NET Core 3.0, vedere Aggiungere il supporto del [formato JSON basato su Newtonsoft.Json.](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)</span><span class="sxs-lookup"><span data-stu-id="b8001-213">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="b8001-214">Nuove direttive Razor</span><span class="sxs-lookup"><span data-stu-id="b8001-214">New Razor directives</span></span>

<span data-ttu-id="b8001-215">L'elenco seguente contiene le nuove direttive Razor:The following list contains new Razor directives:</span><span class="sxs-lookup"><span data-stu-id="b8001-215">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="b8001-216">[`@attribute`](xref:mvc/views/razor#attribute)&ndash; La `@attribute` direttiva applica l'attributo specificato alla classe della pagina o della visualizzazione generata.</span><span class="sxs-lookup"><span data-stu-id="b8001-216">[`@attribute`](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="b8001-217">Ad esempio: `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="b8001-217">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="b8001-218">[`@implements`](xref:mvc/views/razor#implements)&ndash; La `@implements` direttiva implementa un'interfaccia per la classe generata.</span><span class="sxs-lookup"><span data-stu-id="b8001-218">[`@implements`](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="b8001-219">Ad esempio: `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="b8001-219">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="b8001-220">IdentityServer4 supporta l'autenticazione e l'autorizzazione per le API Web e le SPA</span><span class="sxs-lookup"><span data-stu-id="b8001-220">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="b8001-221">ASP.NET Core 3.0 offre l'autenticazione nelle applicazioni a pagina singola (SPA) usando il supporto per l'autorizzazione API Web.</span><span class="sxs-lookup"><span data-stu-id="b8001-221">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="b8001-222">ASP.NET core Identity per l'autenticazione e l'archiviazione degli utenti viene combinato con [IdentityServer4](https://identityserver.io/) per l'implementazione di Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="b8001-222">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="b8001-223">IdentityServer4 è un framework OpenID Connect e OAuth 2.0 per ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="b8001-223">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="b8001-224">Abilita le seguenti funzionalità di sicurezza:</span><span class="sxs-lookup"><span data-stu-id="b8001-224">It enables the following security features:</span></span>

* <span data-ttu-id="b8001-225">Autenticazione come servizio (AaaS)</span><span class="sxs-lookup"><span data-stu-id="b8001-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="b8001-226">Single Sign-On/Off (SSO) su più tipi di applicazione</span><span class="sxs-lookup"><span data-stu-id="b8001-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="b8001-227">Controllo di accesso per le APIAccess control for APIs</span><span class="sxs-lookup"><span data-stu-id="b8001-227">Access control for APIs</span></span>
* <span data-ttu-id="b8001-228">Gateway federativo</span><span class="sxs-lookup"><span data-stu-id="b8001-228">Federation Gateway</span></span>

<span data-ttu-id="b8001-229">Per ulteriori informazioni, vedere la documentazione di [IdentityServer4](http://docs.identityserver.io/en/latest/index.html) o [Autenticazione e autorizzazione per LE SCA.](xref:security/authentication/identity/spa)</span><span class="sxs-lookup"><span data-stu-id="b8001-229">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="b8001-230">Autenticazione basata su certificati e Kerberos</span><span class="sxs-lookup"><span data-stu-id="b8001-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="b8001-231">L'autenticazione del certificato richiede:</span><span class="sxs-lookup"><span data-stu-id="b8001-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="b8001-232">Configurazione del server per l'accettazione dei certificati.</span><span class="sxs-lookup"><span data-stu-id="b8001-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="b8001-233">Aggiunta del middleware `Startup.Configure`di autenticazione in .</span><span class="sxs-lookup"><span data-stu-id="b8001-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="b8001-234">Aggiunta del servizio `Startup.ConfigureServices`di autenticazione del certificato in .</span><span class="sxs-lookup"><span data-stu-id="b8001-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="b8001-235">Le opzioni per l'autenticazione del certificato includono la possibilità di:Options for certificate authentication include the ability to:</span><span class="sxs-lookup"><span data-stu-id="b8001-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="b8001-236">Accettare certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="b8001-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="b8001-237">Verificare la revoca del certificato.</span><span class="sxs-lookup"><span data-stu-id="b8001-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="b8001-238">Verificare che il certificato offerto disponga dei flag di utilizzo corretti.</span><span class="sxs-lookup"><span data-stu-id="b8001-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="b8001-239">Dalle proprietà del certificato viene costruita un'entità utente predefinita.</span><span class="sxs-lookup"><span data-stu-id="b8001-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="b8001-240">L'entità utente contiene un evento che consente l'integrazione o la sostituzione dell'entità.</span><span class="sxs-lookup"><span data-stu-id="b8001-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="b8001-241">Per altre informazioni, vedere <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="b8001-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="b8001-242">[L'autenticazione](/windows-server/security/windows-authentication/windows-authentication-overview) di Windows è stata estesa su Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="b8001-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="b8001-243">Nelle versioni precedenti, l'autenticazione di Windows era limitata a [IIS](xref:host-and-deploy/iis/index) e [HttpSys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="b8001-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="b8001-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) è in grado di utilizzare Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)e [NTLM in Windows](/windows-server/security/kerberos/ntlm-overview), Linux e gli host di macOS per windows aggiunti a un dominio.</span><span class="sxs-lookup"><span data-stu-id="b8001-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="b8001-245">Il supporto Kestrel di questi schemi di autenticazione viene fornito dal pacchetto [Microsoft.AspNetCore.Authentication.Negotiate NuGet.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)</span><span class="sxs-lookup"><span data-stu-id="b8001-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="b8001-246">Come per gli altri servizi di autenticazione, configurare l'autenticazione a livello di app, quindi configurare il servizio:As with the other authentication services, configure authentication app wide, then configure the service:</span><span class="sxs-lookup"><span data-stu-id="b8001-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="b8001-247">Requisiti dell'host:</span><span class="sxs-lookup"><span data-stu-id="b8001-247">Host requirements:</span></span>

* <span data-ttu-id="b8001-248">Gli host Windows devono avere [i nomi dell'entità servizio](/windows/win32/ad/service-principal-names) (SPN) aggiunti all'account utente che ospita l'app.</span><span class="sxs-lookup"><span data-stu-id="b8001-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="b8001-249">I computer Linux e macOS devono essere aggiunti al dominio.</span><span class="sxs-lookup"><span data-stu-id="b8001-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="b8001-250">Per il processo Web è necessario creare nomi SPN.</span><span class="sxs-lookup"><span data-stu-id="b8001-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="b8001-251">[I file Keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) devono essere generati e configurati sul computer host.</span><span class="sxs-lookup"><span data-stu-id="b8001-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="b8001-252">Per altre informazioni, vedere <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="b8001-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="b8001-253">Modifiche al modello</span><span class="sxs-lookup"><span data-stu-id="b8001-253">Template changes</span></span>

<span data-ttu-id="b8001-254">I modelli di interfaccia utente Web (pagine Razor, MVC con controller e visualizzazioni) sono stati rimossi i seguenti:</span><span class="sxs-lookup"><span data-stu-id="b8001-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="b8001-255">L'interfaccia utente di consenso ai cookie non è più inclusa.</span><span class="sxs-lookup"><span data-stu-id="b8001-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="b8001-256">Per attivare la funzionalità di consenso ai cookie in <xref:security/gdpr>un'app generata dal modello ASP.NET Core 3.0, vedere .</span><span class="sxs-lookup"><span data-stu-id="b8001-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="b8001-257">Gli script e le risorse statiche correlate sono ora referenziati come file locali anziché utilizzare le reti CDN.</span><span class="sxs-lookup"><span data-stu-id="b8001-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="b8001-258">Per ulteriori informazioni, vedere Script e gli asset statici correlati a cui [viene ora fatto riferimento come file locali anziché utilizzare reti CDN in base all'ambiente corrente (aspnet/AspNetCore.Docs #14350).](https://github.com/dotnet/AspNetCore.Docs/issues/14350)</span><span class="sxs-lookup"><span data-stu-id="b8001-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="b8001-259">Modello angolare aggiornato per l'utilizzo di Angular 8.</span><span class="sxs-lookup"><span data-stu-id="b8001-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="b8001-260">Il modello Razor libreria di classi (RCL) per impostazione predefinita è Razor component development per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="b8001-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="b8001-261">Una nuova opzione di modello in Visual Studio fornisce il supporto dei modelli per pagine e visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="b8001-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="b8001-262">Quando si crea un RCL dal modello `--support-pages-and-views` in`dotnet new razorclasslib --support-pages-and-views`una shell dei comandi, passare l'opzione ( ).</span><span class="sxs-lookup"><span data-stu-id="b8001-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="b8001-263">Host generico</span><span class="sxs-lookup"><span data-stu-id="b8001-263">Generic Host</span></span>

<span data-ttu-id="b8001-264">I modelli di ASP.NET <xref:fundamentals/host/generic-host>Core 3.0 utilizzano .</span><span class="sxs-lookup"><span data-stu-id="b8001-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="b8001-265">Le versioni <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>precedenti utilizzate .</span><span class="sxs-lookup"><span data-stu-id="b8001-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="b8001-266">L'utilizzo di .NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core Generic Host ( ) fornisce una migliore integrazione delle app ASP.NET Core con altri scenari server non specifici del Web.</span><span class="sxs-lookup"><span data-stu-id="b8001-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="b8001-267">Per ulteriori informazioni, vedere [HostBuilder sostituisce WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="b8001-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="b8001-268">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="b8001-268">Host configuration</span></span>

<span data-ttu-id="b8001-269">Prima del rilascio di ASP.NET Core 3.0, le variabili di ambiente con `ASPNETCORE_` prefisso venivano caricate per la configurazione host dell'host Web.</span><span class="sxs-lookup"><span data-stu-id="b8001-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="b8001-270">Nella 3.0, `AddEnvironmentVariables` viene utilizzato per caricare `DOTNET_` le variabili `CreateDefaultBuilder`di ambiente precedute da prefisso per la configurazione dell'host con .</span><span class="sxs-lookup"><span data-stu-id="b8001-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="b8001-271">Modifiche all'inserimento del costruttore di avvioChanges to Startup constructor injection</span><span class="sxs-lookup"><span data-stu-id="b8001-271">Changes to Startup constructor injection</span></span>

<span data-ttu-id="b8001-272">L'host generico supporta solo `Startup` i seguenti tipi per l'inserimento del costruttore:</span><span class="sxs-lookup"><span data-stu-id="b8001-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="b8001-273">Tutti i servizi possono comunque essere `Startup.Configure` iniettati direttamente come argomenti al metodo.</span><span class="sxs-lookup"><span data-stu-id="b8001-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="b8001-274">Per ulteriori informazioni, vedere [L'host generico limita l'inserimento del costruttore di avvio (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="b8001-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="b8001-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="b8001-275">Kestrel</span></span>

* <span data-ttu-id="b8001-276">La configurazione di Kestrel è stata aggiornata per la migrazione all'host generico.</span><span class="sxs-lookup"><span data-stu-id="b8001-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="b8001-277">Nella versione 3.0 Kestrel è configurato nel `ConfigureWebHostDefaults`generatore di host Web fornito da .</span><span class="sxs-lookup"><span data-stu-id="b8001-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="b8001-278">Gli adattatori di connessione sono stati rimossi da Kestrel e sostituiti con il middleware di connessione, che è simile al middleware HTTP nella pipeline ASP.NET Core ma per le connessioni di livello inferiore.</span><span class="sxs-lookup"><span data-stu-id="b8001-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="b8001-279">Il livello di trasporto Kestrel è `Connections.Abstractions`stato esposto come interfaccia pubblica in .</span><span class="sxs-lookup"><span data-stu-id="b8001-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="b8001-280">L'ambiguità tra intestazioni e trailer è stata risolta spostando le intestazioni finali in una nuova raccolta.</span><span class="sxs-lookup"><span data-stu-id="b8001-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="b8001-281">Le API i/o sincrone, ad `HttpRequest.Body.Read`esempio , sono un'origine comune di incudine di thread che causa arresti anomali dell'app.</span><span class="sxs-lookup"><span data-stu-id="b8001-281">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="b8001-282">Nella 3.0, `AllowSynchronousIO` è disabilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="b8001-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="b8001-283">Per altre informazioni, vedere <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="b8001-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="b8001-284">HTTP/2 abilitato per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="b8001-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="b8001-285">HTTP/2 è abilitato per impostazione predefinita in Kestrel per gli endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b8001-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="b8001-286">Il supporto HTTP/2 per IIS o HTTP.sys è abilitato quando è supportato dal sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="b8001-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="b8001-287">EventCounters su richiesta</span><span class="sxs-lookup"><span data-stu-id="b8001-287">EventCounters on request</span></span>

<span data-ttu-id="b8001-288">L'hosting EventSource, `Microsoft.AspNetCore.Hosting`, genera <xref:System.Diagnostics.Tracing.EventCounter> i nuovi tipi seguenti correlati alle richieste in ingresso:</span><span class="sxs-lookup"><span data-stu-id="b8001-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="b8001-289">Routing degli endpoint</span><span class="sxs-lookup"><span data-stu-id="b8001-289">Endpoint routing</span></span>

<span data-ttu-id="b8001-290">Endpoint Routing, che consente ai framework (ad esempio MVC) di funzionare bene con middleware, è migliorato:Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span><span class="sxs-lookup"><span data-stu-id="b8001-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="b8001-291">L'ordine del middleware e degli endpoint è `Startup.Configure`configurabile nella pipeline di elaborazione delle richieste di .</span><span class="sxs-lookup"><span data-stu-id="b8001-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="b8001-292">Gli endpoint e il middleware si comcompongono bene con altre tecnologie basate su ASP.NET Core, ad esempio i controlli di integrità.</span><span class="sxs-lookup"><span data-stu-id="b8001-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="b8001-293">Gli endpoint possono implementare criteri, ad esempio CORS o autorizzazione, sia nel middleware che in MVC.</span><span class="sxs-lookup"><span data-stu-id="b8001-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="b8001-294">I filtri e gli attributi possono essere posizionati sui metodi nei controller.</span><span class="sxs-lookup"><span data-stu-id="b8001-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="b8001-295">Per altre informazioni, vedere <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="b8001-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="b8001-296">Controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="b8001-296">Health Checks</span></span>

<span data-ttu-id="b8001-297">I controlli di integrità utilizzano il routing degli endpoint con l'host generico.</span><span class="sxs-lookup"><span data-stu-id="b8001-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="b8001-298">In `Startup.Configure`, `MapHealthChecks` chiamare il generatore di endpoint con l'URL dell'endpoint o il percorso relativo:In , call on the endpoint builder with the endpoint URL or relative path:</span><span class="sxs-lookup"><span data-stu-id="b8001-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="b8001-299">Gli endpoint dei controlli di integrità possono:Health Checks endpoints can:</span><span class="sxs-lookup"><span data-stu-id="b8001-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="b8001-300">Specificare uno o più host/porte consentiti.</span><span class="sxs-lookup"><span data-stu-id="b8001-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="b8001-301">Richiedere l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="b8001-301">Require authorization.</span></span>
* <span data-ttu-id="b8001-302">Richiedere CORS.</span><span class="sxs-lookup"><span data-stu-id="b8001-302">Require CORS.</span></span>

<span data-ttu-id="b8001-303">Per altre informazioni, vedere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="b8001-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="b8001-304">Pipe su HttpContext</span><span class="sxs-lookup"><span data-stu-id="b8001-304">Pipes on HttpContext</span></span>

<span data-ttu-id="b8001-305">È ora possibile leggere il corpo della richiesta e <xref:System.IO.Pipelines> scrivere il corpo della risposta usando l'API.</span><span class="sxs-lookup"><span data-stu-id="b8001-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="b8001-306">Il valore di</span><span class="sxs-lookup"><span data-stu-id="b8001-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="b8001-307">`HttpRequest.BodyReader`property fornisce <xref:System.IO.Pipelines.PipeReader> un che può essere utilizzato per leggere il corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="b8001-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="b8001-308">Il valore di</span><span class="sxs-lookup"><span data-stu-id="b8001-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="b8001-309">`HttpResponse.BodyWriter`property fornisce <xref:System.IO.Pipelines.PipeWriter> un che può essere utilizzato per scrivere il corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="b8001-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="b8001-310">`HttpRequest.BodyReader`è un analogo `HttpRequest.Body` del flusso.</span><span class="sxs-lookup"><span data-stu-id="b8001-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="b8001-311">`HttpResponse.BodyWriter`è un analogo `HttpResponse.Body` del flusso.</span><span class="sxs-lookup"><span data-stu-id="b8001-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="b8001-312">Segnalazione errori migliorata in IISImproved error reporting in IIS</span><span class="sxs-lookup"><span data-stu-id="b8001-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="b8001-313">Gli errori di avvio quando si ospitano applicazioni ASP.NET Core in IIS ora producono dati di diagnostica più ricchi.</span><span class="sxs-lookup"><span data-stu-id="b8001-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="b8001-314">Questi errori vengono segnalati nel registro eventi di Windows con le tracce dello stack, ove applicabile.</span><span class="sxs-lookup"><span data-stu-id="b8001-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="b8001-315">Inoltre, tutti gli avvisi, gli errori e le eccezioni non gestite vengono registrati nel registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="b8001-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="b8001-316">Servizio di lavoro e SDK di lavoro</span><span class="sxs-lookup"><span data-stu-id="b8001-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="b8001-317">.NET Core 3.0 introduce il nuovo modello di app Servizio di lavoro.</span><span class="sxs-lookup"><span data-stu-id="b8001-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="b8001-318">Questo modello fornisce un punto di partenza per la scrittura di servizi a esecuzione prolungata in .NET Core.This template provides a starting point for writing long running services in .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b8001-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="b8001-319">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="b8001-319">For more information, see:</span></span>

* [<span data-ttu-id="b8001-320">.NET Core Workers come servizi Windows</span><span class="sxs-lookup"><span data-stu-id="b8001-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="b8001-321">Miglioramenti del middleware delle intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="b8001-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="b8001-322">Nelle versioni precedenti di <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> ASP.NET <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> Core, le chiamate e le quali erano problematiche quando venivano distribuite in un Linux di Azure o dietro qualsiasi proxy inverso diverso da IIS.</span><span class="sxs-lookup"><span data-stu-id="b8001-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="b8001-323">La correzione per le versioni precedenti è documentata in [Inoltrare lo schema per i proxy inversi Linux e non IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="b8001-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="b8001-324">Questo scenario è stato risolto in ASP.NET Core 3.0.This scenario is fixed in ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="b8001-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="b8001-325">L'host abilita il [middleware Intestazioni inoltrate](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) quando la `ASPNETCORE_FORWARDEDHEADERS_ENABLED` variabile di ambiente è impostata su . `true`</span><span class="sxs-lookup"><span data-stu-id="b8001-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="b8001-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`è impostato `true` su nelle immagini del contenitore.</span><span class="sxs-lookup"><span data-stu-id="b8001-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="b8001-327">Miglioramenti delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="b8001-327">Performance improvements</span></span>

<span data-ttu-id="b8001-328">ASP.NET Core 3.0 include molti miglioramenti che riducono l'utilizzo della memoria e migliorano la velocità effettiva:ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span><span class="sxs-lookup"><span data-stu-id="b8001-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="b8001-329">Riduzione dell'utilizzo della memoria quando si utilizza il contenitore di inserimento delle dipendenze incorporato per i servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="b8001-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="b8001-330">Riduzione delle allocazioni in tutto il framework, inclusi scenari middleware e routing.</span><span class="sxs-lookup"><span data-stu-id="b8001-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="b8001-331">Riduzione dell'utilizzo della memoria per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b8001-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="b8001-332">Miglioramenti alla riduzione della memoria e alla velocità effettiva per le connessioni HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b8001-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="b8001-333">Nuovo serializzatore JSON ottimizzato e completamente asincrono.</span><span class="sxs-lookup"><span data-stu-id="b8001-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="b8001-334">Miglioramenti all'utilizzo della memoria e alla velocità effettiva dell'analisi dei moduli.</span><span class="sxs-lookup"><span data-stu-id="b8001-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="b8001-335">ASP.NET Core 3.0 viene eseguito solo in .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="b8001-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="b8001-336">A partire da ASP.NET Core 3.0, .NET Framework non è più un framework di destinazione supportato.</span><span class="sxs-lookup"><span data-stu-id="b8001-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="b8001-337">I progetti destinati a .NET Framework possono continuare in modo completamente supportato utilizzando la versione LTS di [.NET Core 2.1.](https://dotnet.microsoft.com/download/dotnet-core/2.1)</span><span class="sxs-lookup"><span data-stu-id="b8001-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="b8001-338">La maggior parte dei pacchetti correlati ASP.NET Core 2.1.x sarà supportata a tempo indeterminato, oltre il periodo LTS di tre anni per .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="b8001-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="b8001-339">Per informazioni sulla migrazione, vedere [Eseguire il porting del codice da .NET Framework a .NET Core.](/dotnet/core/porting/)</span><span class="sxs-lookup"><span data-stu-id="b8001-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="b8001-340">Usare il framework condiviso ASP.NET CoreUse the ASP.NET Core shared framework</span><span class="sxs-lookup"><span data-stu-id="b8001-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="b8001-341">Il framework condiviso ASP.NET Core 3.0, contenuto nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), non richiede più un elemento esplicito `<PackageReference />` nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="b8001-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="b8001-342">Viene fatto automaticamente riferimento al `Microsoft.NET.Sdk.Web` framework condiviso quando si usa l'SDK nel file di progetto:The shared framework is automatically referenced when using the SDK in the project file:</span><span class="sxs-lookup"><span data-stu-id="b8001-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="b8001-343">Assembly rimossi dal framework condiviso ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b8001-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="b8001-344">Gli assembly più importanti rimossi dal framework condiviso ASP.NET Core 3.0 sono:The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span><span class="sxs-lookup"><span data-stu-id="b8001-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="b8001-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span><span class="sxs-lookup"><span data-stu-id="b8001-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="b8001-346">Per aggiungere Json.NET a ASP.NET Core 3.0, vedere Aggiungere il supporto del [formato JSON basato su Newtonsoft.Json.](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)</span><span class="sxs-lookup"><span data-stu-id="b8001-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="b8001-347">ASP.NET Core 3.0 `System.Text.Json` introduce per la lettura e la scrittura json.</span><span class="sxs-lookup"><span data-stu-id="b8001-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="b8001-348">Per altre informazioni, vedere [Nuova serializzazione JSON](#new-json-serialization) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="b8001-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="b8001-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b8001-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="b8001-350">Per un elenco completo degli assembly rimossi dal framework condiviso, vedere [Assembly rimossi da Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="b8001-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="b8001-351">Per ulteriori informazioni sulla motivazione di questa modifica, vedere [modifiche di rilievo a Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) e Una prima occhiata alle modifiche in ASP.NET Core [3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="b8001-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 